## Espacio necesario

Para comparar el espacio que cada programa necesita, se crearán imágenes Docker ya que permiten especificar todos los requisitos y conocer el espacio final.

Las imágenes Docker tendrán el mínimo espacio posible.

### Rust

Gracias a la opción `multi-stage` para crear imágenes Docker, primero se consturirá una imagen encargada de compilar el binario y luego llevaremos este a otra imagen que pueda ejecutarlo; de modo que, el software requerido para la compilación no es necesario en la imagen final.

Como imagen para compilar el binario, se utiliza la imagen oficial de Rust, en su versión slim y como imagen final que ejecutará el binario, la imagen se basará en alpine.

Estas son las imágenes creadas por el archivo Dockerfile:

```bash
REPOSITORY        TAG           IMAGE ID       SIZE
rust-nginx-logs   latest        9333f909cfe3   9.86MB
<none>            <none>        bbc7a0e4a8b9   2.1GB
rust              1.62.1-slim   803aa6cea85b   714MB
alpine            3.16          d7d3d98c851f   5.53MB
```

Es decir, para compilar y ejecutar el programa, se requieren **2,83 GB**.

Tras eliminar las imágenes no necesarias para lanzar el programa, quedan las siguientes:

```bash
REPOSITORY        TAG       IMAGE ID       SIZE
rust-nginx-logs   latest    9333f909cfe3   9.86MB
alpine            3.16      d7d3d98c851f   5.53MB
```

Por lo que, para ejecutar el programa son necesarios un total de **15,39 MB**.

Los resultados utilizando Docker son:

Descripción                                            | Tamaño
-------------------------------------------------------|---------
Imágenes Docker para construir el binario y ejecutarlo | 2,83 GB
Imágenes Docker para ejecutar el binario               | 15,39 MB

Dejando a un lado las imágenes Docker, el binario resultante tiene un tamaño de 4.1 MB, por lo que de disponer de un sistema compatible, solo haría falta este espacio para trabajar con el programa.

### Python

La imagen utilizada es la oficial de Python en Docker, su variante slim.

Al contrario que en Rust, no es necesario compilar el programa; solo hace falta la imagen Docker de Python para ejecutarlo.

Tras construir las imágenes definidas en el archivo Dockerfile, queda:

```bash
REPOSITORY          TAG                 IMAGE ID       SIZE
python-nginx-logs   latest              281c8db30c3f   47.4MB
python              3.9.13-alpine3.16   0c3301b7a3b8   47.4MB
```

En este caso, no es posible borrar ninguna imagen por lo que, el espacio requerido por python es de **94.8MB**:

Descripción                               | Tamaño
------------------------------------------|--------
Imágenes Docker para ejecutar el programa | 94.8 MB


### Conclusiones

Como hemos visto, para ejecutar el programa con Rust necesitamos 15,39 MB y 94.8 MB en Python.

Es importante destacar dos puntos:

- Al compilar el binario de Rust fue necesario disponer de 2,83 GB libres aunque luego se puedan recuperar eliminando imágenes no necesarias para lanzar el programa.
- Trabajar únicamente con el binario de Rust, sin Docker, requeriría de 4.1 MB.

Tabla comparativa: 

Descripción                                            | Rust     | Python
-------------------------------------------------------|----------|--------
Imágenes Docker para construir el binario y ejecutarlo | 2,83 GB  | -
Imágenes Docker para ejecutar el binario/programa      | 15,39 MB | 94.8 MB
Binario                                                | 4.1 MB   | -

