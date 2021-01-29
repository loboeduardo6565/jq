
##### El archivo input.json contiene configuraciones para una orquestador de contenedores (Marathon).

###### Dentro del mismo se encuentra la definición de N aplicaciones con información respecto al runtime (con que imagen de Docker corre, cuanta RAM/CPU tiene reservada, que puertos reserva/expone, cuantas instancias ocupa, etc.)

##### Se pide:
##### Obtener todas las aplicaciones que hayan cambiado su configuración en las ultimas 24 horas.

##### La salida del programa tiene que tener el nombre de cada aplicación y su fecha de modificación.
```
	.apps[] | .id, .versionInfo.lastConfigChangeAt
	.apps[] | {"Nombre de la Aplicación": .id, "Fecha de Modificación": .versionInfo.lastConfigChangeAt}
```
######	Si dos filtros están separados por una coma, entonces la misma entrada se alimentará en ambos y los flujos de valor de salida de los dos filtros se concatenarán en orden: primero, todas las salidas producidas por la expresión de la izquierda y luego todas las salidas producido por la derecha. Por ejemplo, filter .foo, .bar, produce tanto los campos "foo" como los campos "bar" como salidas independientes.

##### BONUS: Debe verse primero la última que se modifico.

```
	[.apps[] | {id,versionInfo}] | sort_by(.lastConfigChangeAt) | .[] | .id,.versionInfo.lastConfigChangeAt
	[.apps[] | {id,versionInfo}] | sort_by(.lastConfigChangeAt) | .[] | {"Nombre de la Aplicación": .id, "Fecha de Modificación": .versionInfo.lastConfigChangeAt}
```
###### BONUS: Que hayan cambiado su configuración en las ultimas X horas.

###### BONUS: Encontrar aquellas aplicaciones que contengan los mismos containerPort definidos y armar un reporte.

```	
	.apps[]  | {"Nombre de la Aplicación":"\(.id). Puerto del contenedor: \(.container.portMappings[].containerPort)"}
``` 