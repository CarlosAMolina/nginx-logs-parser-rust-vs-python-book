## Espacio necesario

Para comparar el espacio que cada programa necesita, se crearán imágenes Docker ya que permiten especificar todos los requisitos y conocer el espacio final.

Las imágenes Docker tendrán el mínimo espacio posible.

### Rust

Gracias a la opción `multi-stage` para crear imágenes Docker, primero se consturirá una imagen encargada de compilar el binario y luego llevaremos este a otra imagen que pueda ejecutarlo; de modo que, el software necesario para la compilación no es necesario en la imagen final.

Como imagen para compilar el binario, se utiliza la imagen oficial de Rust, en su versión slim y como imagen final que ejecutará el binario, la imagen se basará en alpine.

Estas son las imágenes creadas:

```bash
REPOSITORY        TAG           IMAGE ID       CREATED         SIZE
rust-nginx-logs   latest        9333f909cfe3   8 minutes ago   9.86MB
<none>            <none>        bbc7a0e4a8b9   8 minutes ago   2.1GB
rust              1.62.1-slim   803aa6cea85b   5 days ago      714MB
alpine            3.16          d7d3d98c851f   6 days ago      5.53MB
```

Es decir, para compilar y ejecutar el programa, son necesarios **2,83 GB**.

Tras eliminar las imágenes no necesarias para lanzar el programa, quedan las siguientes:

```bash
REPOSITORY        TAG       IMAGE ID       CREATED          SIZE
rust-nginx-logs   latest    9333f909cfe3   12 minutes ago   9.86MB
alpine            3.16      d7d3d98c851f   6 days ago       5.53MB
```

Por lo que, para ejecutar el programa son necesarios un total de **15,39 MB**.

Los resultados utilizando Docker son:

Descripción                                                |   Tamaño
-----------------------------------------------------------|-----------
Rust. Imágenes Docker para construir y ejecutar el binario | 2,83 GB
Rust. Imágenes Docker para ejecutar el binario             | 15,39 MB
Rust. Binario                                              | 4.1 MB

### Conclusiones

Como hemos visto, aunque con Rust necesitemos 15,39 MB, es importante recordar que a la hora de crear el binario fue necesario disponer de 2,83 GB.
