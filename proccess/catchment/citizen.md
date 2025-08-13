# Captación de Ciudadanos


Endpoint: `api/citizens`

## Descripción

El proceso de captación ciudadanos es el resultado de la captura y guardado de datos mediante la AppWeb o la AppAvanza. Este proceso inicia mediante un acercamiento iniciar por parte de un brigadista o asistencia (registro) de un ciudadano a algún programa o actividad.

### Requisitos obligatorios
Los datos básicos para realizar un proceso de captación son los siguientes:
- **Nombre(s)** (name)
- **Primer Apellido** (firstSurname)
- **Segundo Apellido** (secondSurname)
- **Fecha de Nacimiento** (bithday)
- Dirección (address)
  - **Municipio** (municipality)
  - Sección Electoral (section)
- Captación (catchment)
  - Origen (origin)
  - Actividad (activity)

De la lista previa, cinco datos en negrita permiten formar una clave única e interna que permite la contención de registros duplicados, lo que permite tener un catálogo de Ciudadanos lo más exento de duplicidades.

## Estructura de request/response

```
{
  "id": "",
  "address": {
    "street": "C 105",
    "extNumber": "NUM 7",
    "intNumber": "",
    "suburb": "SANTA LUCIA",
    "municipality": "CAMPECHE",
    "locality": "SAN FRANCISCO DE CAMPECHE",
    "zipCode": "24020",
    "federalDistrict": 1,
    "localDistrict": 3,
    "section": 54,
    "block": 27,
    "promotionBlock": 0
  },
  "voteAddress": {
    "street": "CARRETERA FEDERAL IXTLAHUALA",
    "extNumber": "S/N",
    "intNumber": "",
    "suburb": "XBACAB",
    "municipality": "CHAMPOTON",
    "locality": "CHAMPOTON",
    "zipCode": "24400",
    "localDistrict": 15,
    "federalDistrict": 2,
    "section": 347,
    "block": 0,
    "promotionBlock": 0
  },
  "resideInAddress": 0,
  "catchment": {
    "appName": "app-avanza",
    "platform": "android",
    "idPopulation": 55768,
    "origin": "MOVIMIENTO CIUDADANO",
    "program": "AFILIACIONES 2025",
    "activity": "AFILIACION",
    "username": "victoralpuche",
    "catchedAt": "2025-08-08T15:14:17.652+00:00",
    "latitude": 18.6517127,
    "longitude": -91.7729817,
    "catchmentSessionId": "JMcnsmnqlDxt8iBj9co0",
    "promotionSessionId": ""
  },
  "birthPlace": "",
  "birthKey": "",
  "whatsapp": "5625006097",
  "whatsappStatus": 1,
  "phone": "",
  "email": "example@mail.com",
  "voteKey": "BRRNMR00020204M100",
  "hasVoteExperience": 0,
  "regexCode": "",
  "status": "catched",
  "idStatus": 1,
  "hasIncompleteData": 0,
  "apiVersion": 1,
  "classification": "",
  "nickname": "",
  "occupation": "",
  "tshirtSize": "",
  "studyLevel": "LICENCIATURA",
  "studyLevelCareer": "DERECHO",
  "sector": "",
  "childrenNumber": 0,
  "name": "MARISA ROSBEL",
  "firstSurname": "BARRERA",
  "secondSurname": "RINCON",
  "birthday": "02/02/2000",
  "genre": "FEMENINO",
  "childsNumber": 0,
  "photo": {
    "address": "",
    "profile": "",
    "card": ""
  }
}
```

## Modelo

![Modelado](modelado.v001.jpg "UML Diagram")

# DEVELOPMENT ESTIMATES

## Entregable:
### Backend tiempo (3 días)
  - Endpoints
    - **CREATE**
    - **UPDATE**
    - **DELETE**
    - **SINGLE**
    - **LIST**
    - Implícito
      - **Seguridad**
      - **Validaciones**

### Frontend (5 días)
  - Componente de Catálogo
    - Estructura archivos .ts
  - Servicio de consulta al API
  - Validaciones en formulario
  - Datos de otros catálogos necesarios para ingresar el nuevo registro
    - Datos ingresados
  - Precondiciones
    - Catálogo de datos geopolíticos (Proporcionar documentación)
    - Catálogo de usuario (Proporcionat estatus)

## Prerequisitos antes de iniciar la asignación (2 días)
- Definir modelado de datos de la entidad Ciudadano (Citizen); indicar si se reutilizará el modelado realizado en Campeche. 3 Hrs
- Aprobación del modelado. Máximo 24hrs tras notificación