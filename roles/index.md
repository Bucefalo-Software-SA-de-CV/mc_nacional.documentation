# Roles y Permisos

## Coordinador Estatal (01)

Todos los registros del Estado. Tendrá acceso a los módulos de Módulo 1, Módulo 2, ..., Módulo N

## Coordinador Distrito Federal (02)

Todos los registros del Distrito Federal Asignado

## Coordinador Municipal (03)

Todos los registros del Municipio asignado. Este NO tiene asignado nada, los acumulados de Simpatizantes, se calcula por las secciones asignadas a ese municipio.

-----------------------------------------------------

## Coordinador Distrito Local (04)

Todos los registros del Distrito Local asignado. Un distrito local agrupa zonas, que NO se pueden compartir con otros distritos, esto es, cada distrito tiene sus propias Zonas.

## Responsable de Zona (05)

Todos los registros de las secciones que esa Zona tiene asignadas. Zona es un grupo de secciones, no tienen que pertenecer a la zona, es decir a las secciones que forman parte de la zona. El óptimo de operación es 4000 simpatizantes como máximo por zona, es decir, que en cada zona a 10 responsables de sección. La zona esta restringida a un Municipio Un responsable de Zona, NO puede tener dos o más zonas asignadas. Se deberá de agregar un catálogo de Zonas (Zona 001, Zona 002, Zona 003, etc.)

## Responsable Sección (06)

Todos los registros de la Sección asignada (no es obligatorio que viva en la sección, 
un responsable de sección debe ver cuando mucho 400 simpatizantes. Va a tener más o menos 20 responsables de manzana, por lo tanto, se suman los Simpatizantes que tienen asignadas las manzanas de cada responsable de manzana. Un responsable de sección una o más secciones, en caso de que se divida tendremos sección 99A y sección 99B. Se puede tener dos responsables de sección, pero cada sección tiene manzanas distintas)

## Responsable Manzana (07)

Todos los registros de la Manzana asignada (debe vivir en la sección y puede ser responsable de varias manzanas de la misma sección. Se espera que un responsable de manzana tenga 20 simpatizantes)

## Roles funcionales (99)

Sin permisos por default