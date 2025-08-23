# Procesos para la operación nacional


**Listado de Procesos**
## [Gestión](request/) 
**Descripción:**  
El proceso de Gestión está diseñado para captar a un posible simpatizante mediante la solicitud o levantamiento de una necesidad en su entorno, como baches, luminarias, alcantarillas, entre otros. La gestión se levanta directamente desde la aplicación móvil, su seguimiento se realiza en la aplicación web y, al registrarse, se vincula al ciudadano como una actividad dentro de su historial. Estas gestiones pueden ser personales o grupales.


## [Validación de datos](validations_catchment/) 
**Descripción:**  
El proceso de datos está diseñado para validar la información de ciudadanos con status captados "catched" y statusValidated 1 o 2. Se realiza una verificación inicial de datos como teléfono y origen, y se registra cada intento de validación en el historial de actividades del ciudadano, sea exitoso o no. Dependiendo del resultado, el ciudadano puede ser marcado como validado "validated", "inactive", su estatus de validación statusValidated puede ser 1 por validar, 2 pendiente, 0 imposible de validar y 3 validado. Este proceso asegura que la información utilizada en actividades posteriores sea confiable y consistente.


