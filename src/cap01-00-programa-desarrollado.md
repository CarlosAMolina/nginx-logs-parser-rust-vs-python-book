# Programa desarrollado

## Introducción 

Se ha creado un programa que transforma archivos de logs de un servidor Nginx en un único archivo `csv` con el fin de facilitar su análisis gracias a las opciones de separar su contenido en columnas, filtrar, etc. que ofece un lector de archivos `csv`.

El código del programa está disponible en el siguiente [enlace](https://github.com/CarlosAMolina/nginx-logs).

## Partes

Las acciones realizadas por este programa son:

- Recibir los comandos a ejecutar que el usuario indica por terminal.
- Ordenar los archivos de logs a analizar desde el más antiguo hasta el más actual para que en el archivo `csv` resultante los logs aparezcan en orden cronológico.
- Separar con coma las partes de cada log y exportar este resultado en un archivo final.

## Consideraciones para mejorar la velocidad de ejecución

Veamos algunos detalles a tener en cuenta a la hora de desarrollar el programa.

### Lectura y escritura de archivos

Con Python, al leer y escribir en archivos se utiliza la orden `with open`. En cambio, de las diferentes opciones que presenta Rust, es recomendable utilizar `BufReader` y `BufWriter` en lugar de `Read` y `Write` cuando hagamos pequeñas y repetidas llamadas al mismo archivo ya que, el uso de un buffer aumenta la velocidad del programa; en el capítulo de recursos utilizados puede verse el enlace a la documentación donde se explica esto.

### Uso de expresiones regulares

De los diferentes métodos para realizar búsquedas en strings, el uso de expresiones regulares ofrece en Python unos tiempos de ejecución muy bajos, mientras que en Rust consumen más tiempo que otras opciones.

Para ejemplificar esto, se comparará el tiempo requerido según la implementación del método que toma las partes de un log a guardar en columnas del `csv`.

La opción más eficiente consiste en analizar cada carácter del string en busca de aquellos que indican el final de cada parte:

```rust
pub fn get_log(text: &str) -> Option<Log> {
    let mut log_parts_index = vec![0];
    let characters_to_match = vec![
        b' ', b' ', b'[', b']', b'"', b'"',
        b' ', b' ', b'"', b'"', b'"',
    ];
    let bytes = text.as_bytes();
    let mut match_index = 0;
    for (i, &item) in bytes.iter().enumerate() {
        if item == characters_to_match[match_index] {
            log_parts_index.push(i);
            if match_index < characters_to_match.len() - 1 {
                match_index += 1;
            }
        }
    }
    if log_parts_index.len() == 13 {
        Some(Log {
            remote_addr: &text[
                log_parts_index[0]..log_parts_index[1]],
            remote_user: &text[
                log_parts_index[2] + 1..log_parts_index[3] - 1],
            time_local: &text[
                log_parts_index[3] + 1..log_parts_index[4]],
            request: &text[
                log_parts_index[5] + 1..log_parts_index[6]],
            status: &text[
                log_parts_index[7] + 1..log_parts_index[8]],
            body_bytes_sent: &text[
                log_parts_index[8] + 1..log_parts_index[9] - 1],
            http_referer: &text[
                log_parts_index[9] + 1..log_parts_index[10]],
            http_user_agent: &text[
                log_parts_index[11] + 1..log_parts_index[12]],
        })
    } else {
        None
    }
}
```

Para conocer el tiempo necesario con expresiones regulares, hay que añadir estas dependencias al archivo `Cargo toml`:

```bash
[dependencies]
lazy_static = "1.4.0"
regex = "1.5.6"
```

También, hay que sustituir la función encargada de parserar el log por lo siguiente:

```rust
fn get_result_with_regex_captures(text: &str) -> Option<Log> {
    lazy_static! {
        static ref RE: Regex = Regex::new(
            r#"(?x)
          ^
          ((\d{1,3}[\.]){3}\d{1,3}) # IPv4
          \s-\s
          (.+)                      # Remote user
          \s\[
          (.+)                      # Time local
          \]\s"
          (.*)                      # Request
          "\s
          (\d{1,3})                 # Status
          \s
          (\d+)                     # Body bytes sent
          \s"
          (.+)                      # HTTP referer
          "\s"
          (.+)                      # HTTP user agent
          "
          $
        "#,
        )
        .unwrap();
    }
    RE.captures(text).and_then(|cap| {
        let groups = (
            cap.get(1),
            cap.get(3),
            cap.get(4),
            cap.get(5),
            cap.get(6),
            cap.get(7),
            cap.get(8),
            cap.get(9),
        );
        match groups {
            (
                Some(remote_addr),
                Some(remote_user),
                Some(time_local),
                Some(request),
                Some(status),
                Some(body_bytes_sent),
                Some(http_referer),
                Some(http_user_agent),
            ) => Some(Log {
                remote_addr: remote_addr.as_str(),
                remote_user: remote_user.as_str(),
                time_local: time_local.as_str(),
                request: request.as_str(),
                status: status.as_str(),
                body_bytes_sent: body_bytes_sent.as_str(),
                http_referer: http_referer.as_str(),
                http_user_agent: http_user_agent.as_str(),
            }),
            _ => None,
        }
    })
}
```

Ejecutando el programa con `cargo run`, tenemos unos tiempos de ejecución de TODO.

De las opciones que presenta Rust en el uso de expresiones regulares, `find` es más rápida que `captures`, pese a ello, sigue siendo más lento que el modo de parsear los logs utilizado en este proyecto.

### Compilación del programa

Python no requiere compilación, en cuanto a Rust, hay que tener en cuenta que hay dos opciones al crear el ejecutable, con `cargo build` o `cargo build --release`; la última opción aplica unas optimizaciones que aumenta la velocidad del programa; pero al requerir mayor tiempo de compilación, es conveniente usar `cargo build` durante el desarrollo ya que compilaremos más veces el programa.

De lanzar el mismo programa con `cargo build` y `cargo build --release` obtenemos unos tiempos de ejecución de TODO y TODO respectivamente.
