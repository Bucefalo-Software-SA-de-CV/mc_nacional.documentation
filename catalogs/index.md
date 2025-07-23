# Catálogos de Apoyo para procesos

En este apartado se presentan los catálogos que sirven para poblar los registros que se muestran en los dos procesos iniciales Captación y Gestión. A continuación se muestra la lista y el objetivo de cada uno.

## Listado de Catálogos

- Actividades

Endpoint: `api/activities`
Descripción:
El recurso Actividades representa el conjunto de acciones planificadas para la captación de simpatizantes. Cada actividad es organizada y coordinada por los responsables de promoción, y puede estar clasificada por tipo, origen, estatus y ámbito territorial (distritos). Estas actividades sirven como puntos de contacto para establecer relación con potenciales simpatizantes dentro del proceso de promoción.

Ejemplo de estructura de respuesta:

```json
{
    "id": "string",
    "name": "string",
    "classification": "string",
    "origin": "string",
    "status": "string",
    "observations": "string",
    "districts": [],
    "createdOn": "date",
    "updatedOn": "date"
}
```

- Colonias
Endpoint: `api/suburbs`
Descripción:
El recurso Colonias contiene la lista de localidades para la clasificación geográfica de un simpatizante.

Ejemplo de estructura de respuesta:

```json
{
    "id": "string",
    "idLocality": "number",
    "idMunicipality": "number",
    "locality": "string",
    "municipality": "string",
    "name": "string",
    "originalId": "string",
    "status": "string",
    "suburbType": "Cstring",
    "updatedOn": "string"
}
```

- Programas
Endpoint: `api/social-programs`
Descripción:
Los programas sociales son eventos que se realizan para los simpatizantes y que permiten recolectar información de los simpatizantes.

Ejemplo de estructura de respuesta:

```json
{
    "id": "string",
    "idOrigin": "string",
    "createdOn": "date",
    "name": "string",
    "originName": "string",
    "status": "string",
    "titular": "string",
    "updatedOn": "date"
}
```

- Orígenes

```json
{
    "id": "string",
    "createdOn": "date",
    "name": "string",
    "description": "string",
    "status": "string",
    "updatedOn": "date"
}
```

- Ocupaciones

```json
{
    "id": "string",
    "createdOn": "date",
    "name": "string",
    "description": "string",
    "status": "string",
    "updatedOn": "date"
}
```

- Sectores

```json
{
    "createdOn": "date",
    "id": "string",
    "name": "string",
    "status": "string",
    "updatedOn": "date"
}
```

- Tipos de Trámite
Endpoint: `api/formalities`

```json
{
    "createdDate": "date",
    "createdMonth": "number",
    "createdOn": "date",
    "createdYear": "number",
    "id": "string",
    "name": "string",
    "status": "string",
    "updatedOn": "date"
}
```

- Dependencias

Endpoint: `api/organizations`

```json
{
    "createdOn": "date",
    "description": "description",
    "id": "string",
    "level": "number",
    "municipality": "string",
    "name": "string",
    "status": "string",
    "updatedOn": "date"
}
```

- Funcionarios

Endpoint: `api/representatives`

```json
{
    "createdOn": "date",
    "email": "string",
    "id": "string",
    "idOrg": "string",
    "level": "number",
    "municipality": "string",
    "name": "string",
    "org_id": "string",
    "org_name": "string",
    "phone": "string",
    "position": "string",
    "status": "string",
    "updatedOn": "date"
}
```
