# Sesión III - Redireccionamiento, filtros y pipes

Herramientas computacionales para bioinformática: UNIX, expresiones regulares y shell script

Edita esta plantilla en formato markdown [Guía aquí](https://guides.github.com/features/mastering-markdown/) como se pide en el guión. 
Cuando hayas acabado, haz un commit de tus cambios y súbelos al repositorio antes de la fecha de entrega señalada. 

======================================


## Ejercicio 1
`sort -R` (random) desordena las líneas de un fichero. Prueba a desordenar el fichero `gene-2.bed` y crea un nuevo fichero llamado `gene-2-desordenado.bed`.

Trata ahora de ordenar este fichero de acuerdo a los siguientes criterios: 
1. Sin cortar los elementos
2. En orden descendente
3. Usando a la vez la tercera y la segunda columna (en este orden de prioridad). Consulta el manual para ver la opción -k. 

### Respuesta ejercicio 1
 Primero creamos un nuevo fichero llamado `gene-2-desordenado.bed` que contenía los datos desordenados del fichero `gene-2.bed`. Para esto usamos el comando:
 
 `sort -R gene-2.bed > gene-2-desordenado.bed`
 
![gen desordenado](https://user-images.githubusercontent.com/92113066/139421879-437f94a7-6f5f-46c6-a967-fc79c33cfdc5.png)

 La opción `-k`, seguida del número correspondiente al campo que queremos ordenar, permite empezar a ordenar por la columna que deseemos. Para ordenar nuestro archivo fijándonos en la tercera y segunda columna, en orden descendiente y sin cortar los elementos, la orden a ejecutar sería: `sort -nr -k3,3 -k2,2 gene-2-desordenado.bed`. Con esto, le estamos pidiendo que nos ordene "empezando y terminando" por la 3ª columna, y posteriormente, "empezando y terminando" por la segunda, razón por la que indicamos dos veces el mismo campo separado por una coma.

![sort 3](https://user-images.githubusercontent.com/92113066/140031772-58c93deb-ca7f-4efd-a53e-99fb10c3f8bd.png)


## Ejercicio 2

Cuáles son y cuántos tipos distintos de "features" hay en `Drosophila_melanogaster.BDGP6.28.102.gtf` y en `Homo_sapiens.GRCh38.102.gtf.gz`? Nota: para trabajar con ficheros .gunzip sin descomprimir puedes usar `zcat`.

### Respuesta ejercicio 2

Primeramente, ejecutaremos `cat` sobre el archivo que nos interesa ver por pantalla para poder trabajar con él. Eliminaremos los metadatos mediante el comando `grep -v "^#"` y seleccionaremos la columna que nos interesa, la que contiene "features", mediante `cut -f3`. Ordenamos los datos con `sort` para ejecutar posteriormente `uniq -c`y conocer así los diferentes tipos de features existentes. Nuestro pipeline quedaría así: 

`cat Drosophila_melanogaster.BDGP6.28.102.gtf | grep -v "^#" | cut -f3 | sort | uniq -c`

Posteriormente, podemos ejecutar `wc -l` para contar el número de features.

![features Drosophila](https://user-images.githubusercontent.com/92091175/139582871-313cbd9b-f1f7-4e34-b38c-da09a706362b.png)

Para trabajar con el archivo `Homo_sapiens.GRCh38.102.gtf.gz` podemos utilizar el mismo pipeline, modificando `cat` por `zcat`:

`zcat Homo_sapiens.GRCh38.102.gtf.gz | grep -v "^#" | cut -f3 | sort | uniq -c`


![features homo sapiens](https://user-images.githubusercontent.com/92091175/139583070-67c8e6fb-38fe-4338-962b-46d1b8a98473.png)



## Ejercicio 3

Recuerdas `covid-samples.fasta`? Localízalo en tu HOME, y extrae, usando un pipeline, los nombres de las secuencias contenidas en este fichero. Luego saca la primera palabra de cada una, ordénalas y guárdalas en un fichero `covid-seq-names.txt`.

### Respuesta ejercicio 3

Para extraer los nombres de las secuencias empleamos el pipeline `cat covid-samples.fasta | grep "^>"`.

![grep1](https://user-images.githubusercontent.com/92113066/139668793-6043ea9f-53d6-4a92-b550-e3dec9bfdca8.png)

Obtenemos así las filas que empiecen por '>', esto es, los nombres y datos de las secuencias. Seleccionamos después el número de caracteres que ocupa la referencia de las secuencias mediante `cut -c 1-11` y ordenamos los resultados volcándolos a un archivo de texto mediante la orden `sort > [ARCHIVO.TXT]`.
En una sola línea, nuestro pipeline sería:

`grep "^>" covid-samples.fasta | cut -c 1-11 | sort > ../negido/3-redireccionamiento-filtros-y-pipes-ana-y-noelia/covid-seq-names.txt`

![covid seq names](https://user-images.githubusercontent.com/92091175/139587940-c5b29e5b-e4bb-4d33-8a4f-781dc532b29c.png)



## Ejercicio 4

Encuentra, usando una sola línea, el número de usuarias diferentes que tienen al menos una carpeta a su nombre en el '/home' de CPG3.

### Respuesta ejercicio 4

Para obtener la respuesta utilizando una sola línea hay que hacer uso de pipelines. Nos situamos en '/home' y ejecutamos `ls -l` y `tail -n+2` para saltarnos las dos primeras filas. Seleccionamos el rango de caracteres que ocupa la columna que nos interesa, en este caso es la tercera y comprende desde el caracter 14 hasta el 34*¹. Posteriormente, añadimos `sort` y `uniq -c` para que ordene y cuente si hubiera algún nombre repetido.

![numero de usuarios](https://user-images.githubusercontent.com/92091175/140292222-e0f5aa9c-78a2-4fd9-9c06-c8d2b65f2f72.png)

Podemos añadir  después `wc -l` para saber el número de directorios existentes.

`ls -l | tail -n+2 | cut -c 14-34 | sort | uniq -c | wc -l`

Nos aparecen 34 usuarios diferentes.

*¹ Otra modo de seleccionar la tercera columna sería utilizar awk, de manera que el pipeline tendría esta estructura: 
`ls -l | tail -n+2 | awk '{print $3}' | sort | uniq -c`. Se obtiene el mismo resultado.


