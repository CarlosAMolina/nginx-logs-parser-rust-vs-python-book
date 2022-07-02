# Programa desarrollado

## Introducción 

Se ha creado un programa que transforma archivos de logs de un servidor Nginx en un único archivo `csv` con el fin de facilitar su análisis gracias a las funciones de separar su contenido en columnas, filtrar, etc.

El código del programa está disponible en el siguiente [enlace](https://github.com/CarlosAMolina/nginx-logs).

## Partes

Las acciones realizadas por este programa son:

- Recibir los comandos a ejecutar que el usuario indica por terminal.
- Ordenar los archivos de logs a analizar desde el más antiguo hasta el más actual para que en el archivo `csv` resultante los logs aparezcan en orden cronológico.
- Separar con coma las partes de cada log y exportar este resultado en un archivo final.

