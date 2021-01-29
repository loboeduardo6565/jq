
##### El archivo input.json contiene configuraciones para una orquestador de contenedores (Marathon).

###### Dentro del mismo se encuentra la definición de N aplicaciones con información respecto al runtime (con que imagen de Docker corre, cuanta RAM/CPU tiene reservada, que puertos reserva/expone, cuantas instancias ocupa, etc.)

##### Se pide:
##### Obtener todas las aplicaciones que hayan cambiado su configuración en las ultimas 24 horas.

###### El siguiente filtro convierte la fecha actual en un arreglo para poder comparar cada posición con las fechas dentro del documento, en este sentido solo hubo que restar un 1 a la posición [2] correspondiente al día para observar los cambios en las ultimas 24 horas también mantiene el orden por fechas en caso de que existan varios hallazgos.

```
[.apps[] | {id,versionInfo}] | sort_by(.lastConfigChangeAt) | (now | gmtime | strftime("%Y-%m-%dT%H:%M:%S.%Z") | strptime("%FT%T.%Z") [0:6] ) as $f | map(select((.versionInfo.lastConfigChangeAt | strptime("%FT%T.%Z")[0] == $f[0]) and (.versionInfo.lastConfigChangeAt | strptime("%FT%T.%Z")[1] == $f[1]) and (.versionInfo.lastConfigChangeAt | strptime("%FT%T.%Z")[2] == $f[2] -1) ))
```
![query_out](https://github.com/loboeduardo6565/jq/blob/main/1-jq%20play.png)

##### La salida del programa tiene que tener el nombre de cada aplicación y su fecha de modificación.
```
	.apps[] | .id, .versionInfo.lastConfigChangeAt
	.apps[] | {"Nombre de la Aplicación": .id, "Fecha de Modificación": .versionInfo.lastConfigChangeAt}
```
![query_out](https://github.com/loboeduardo6565/jq/blob/main/3-jq%20play.png)

######	Si dos filtros están separados por una coma, entonces la misma entrada se alimentará en ambos y los flujos de valor de salida de los dos filtros se concatenarán en orden: primero, todas las salidas producidas por la expresión de la izquierda y luego todas las salidas producido por la derecha. Por ejemplo, filter .foo, .bar, produce tanto los campos "foo" como los campos "bar" como salidas independientes.
###### https://stedolan.github.io/jq/manual/

##### BONUS: Debe verse primero la última que se modifico.

###### Muestra solo el listado ordenado por la última modificación
```
	[.apps[] | {id,versionInfo}] | sort_by(.lastConfigChangeAt) | .[] | .id,.versionInfo.lastConfigChangeAt
```
###### Muestra el mismo listado ordenado manteniendo el formato json
```
	[.apps[] | {id,versionInfo}] | sort_by(.lastConfigChangeAt) | .[] | {"Nombre de la Aplicación": .id, "Fecha de Modificación": .versionInfo.lastConfigChangeAt}
```

![query_out_order](https://github.com/loboeduardo6565/jq/blob/main/6-jq%20play.png)

###### BONUS: Que hayan cambiado su configuración en las ultimas X horas.

###### Muestra las modificaciones del mismo día horas previas. 

```
[.apps[] | {id,versionInfo}] | sort_by(.lastConfigChangeAt) | (now | gmtime | strftime("%Y-%m-%dT%H:%M:%S.%Z") | strptime("%FT%T.%Z") [0:6] ) as $f | map(select((.versionInfo.lastConfigChangeAt | strptime("%FT%T.%Z")[0] == $f[0]) and (.versionInfo.lastConfigChangeAt | strptime("%FT%T.%Z")[1] == $f[1]) and (.versionInfo.lastConfigChangeAt | strptime("%FT%T.%Z")[2] == $f[2]) and (.versionInfo.lastConfigChangeAt | strptime("%FT%T.%Z")[3] <= $f[3]) ))
```

###### BONUS: Encontrar aquellas aplicaciones que contengan los mismos containerPort definidos y armar un reporte.

```	
	.apps[]  | {"Nombre de la Aplicación":"\(.id). Puerto del contenedor: \(.container.portMappings[].containerPort)"}
``` 

![query_report_PortId](https://github.com/loboeduardo6565/jq/blob/main/6.1-jq%20play.png)