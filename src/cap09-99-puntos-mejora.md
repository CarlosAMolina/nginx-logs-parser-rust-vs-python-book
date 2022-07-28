## Puntos de mejora en Rust

Rust ofrece mayor eficiencia que Python, pese a ello, hay algunos aspectos en los que Python es una mejor opción.

### Uso de expresiones regulares

Como se comentó con anterioridad; con Python se consiguen menores tiempos de ejecución al trabajar con expresiones regulares.

### Cross compiling

TODO, indicar desventajas
TODO ¿go permite trabajar en todos los linux o dentro de linux también hay que especificar el target?

En otros lenguajes como Go, es más sencillo:

- <https://golangcookbook.com/chapters/running/cross-compiling/>
- <https://rust-lang.github.io/rustup/cross-compilation.html>

Al construir el binario en Rust (comando `cargo bild --target`) se debe indicar la arquitectura destino.

Pueden verse las opciones con el comando `rustc --print target-list`.
