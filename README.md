<div align="center">
  <table width="100%" style="border-collapse: collapse; table-layout: fixed;">
    <tr>
      <td width="230" align="center" style="background-color: #121212; padding: 40px; border: 1px solid #333;">
        <img src="https://github.com/user-attachments/assets/4c3fe4d4-51d8-494e-a579-7381653f1b2d" alt="Logo de Kachin" width="160" />
      </td>
      <td style="background-color: #121212; padding: 20px; border: 1px solid #333; width: calc(100% - 230px);">
        <h2>Documentación Técnica de Integración con Subagentes</h2>
        <p><i>versión 1.0.1</i></p>
      </td>
    </tr>
  </table>
</div>


---

# Tabla de Contenidos

- [Introducción](#introducción)
- [Proceso de envíos con Subagentes](#proceso-de-envios-con-subagentes)
- [Proceso de pagos con subagentes](#proceso-de-pagos-con-subagentes)
- [Diagrama de Comunicación de Componentes](#diagrama-de-comunicacion-de-componentes)
- [Autenticación en los Endpoints](#autenticación-en-los-endpoints)
  - [Encabezados Requeridos](#encabezados-requeridos)
- [Ejemplo de Respuesta de Error](#ejemplo-de-respuesta-de-error)
  - [Códigos de Error](#códigos-de-error)


---

## Seleccione una versión:

<div align="center">
  <table>
    <tr>
      <td align="center" width="400">
        <a href="../../wiki/Versión-1.0" style="text-decoration: none;">
          <img src="https://img.shields.io/badge/Versión_1.0-Estable-0366D6?style=for-the-badge&logo=github&logoColor=white" alt="Versión 1.0 (Estable)" width="300"/>
        </a>
        <p>Versión actual en producción</p>
      </td>
      <td align="center" width="400">
        <a href="../../wiki/Versión-3.0" style="text-decoration: none;">
          <img src="https://img.shields.io/badge/Versión_3.0-Beta-28A745?style=for-the-badge&logo=github&logoColor=white" alt="Versión 3.0 (Beta)" width="300"/>
        </a>
        <p>Próxima versión con nuevas funcionalidades</p>
      </td>
    </tr>
  </table>
</div>

## Endpoints Disponibles

En nuestra wiki encontrará documentación detallada sobre los siguientes endpoints:

### Versión 1.0
- [Consulta de Transacción de Envío](../../wiki/Versión-1.0#endpoint-consulta-de-transacción-de-envio-disponible)
- [Proceso de Transacción de Envío](../../wiki/Versión-1.0#endpoint-proceso-de-transacción-disponible)
- [Consulta de Transacción de Pago](../../wiki/Versión-1.0#endpoint-consulta-de-transacción-de-pago-disponible)
- [Proceso de Transacción de Pago](../../wiki/Versión-1.0#endpoint-proceso-de-transacción-de-pago-disponible)

### Versión 3.0

- [Consulta de Transacción de Envío](../../wiki/Version-3.0#endpoint-consulta-de-transacción-de-envio-disponible)
- [Proceso de Transacción de Envío](../../wiki/Version-3.0#endpoint-proceso-de-transacción-disponible)
- [Consulta de Reimpresión de Comprobante de Envío](../../wiki/Version-3.0#endpoint-proceso-de-transacción-de-pago-disponible) `🟢 NEW`
- [Consulta de Transacción de Pago](../../wiki/Version-3.0#endpoint-consulta-de-transacción-de-pago-disponible)
- [Proceso de Transacción de Pago](../../wiki/Version-3.0#endpoint-proceso-de-transacción-de-pago-disponible)
- [Consulta de Reimpresión de Comprobante de Pago](../../wiki/Version-3.0#endpoint-proceso-de-transacción-de-pago-disponible) `🟢 NEW`

---

## Introducción
Este documento detalla el proceso de integración con el servicio de envío de transferencias de giros a través de las API's de Kachin. Se proporcionan ejemplos de solicitud con `cURL`, una descripción de los encabezados requeridos, la estructura de las respuestas esperadas y posibles errores.

## Proceso de envios con Subagentes
![image](https://github.com/user-attachments/assets/c3e678c9-d00c-4f95-9b87-d7a146e29c11)


---
## Proceso de pagos con subagentes
![image](https://github.com/user-attachments/assets/950d6e66-e91f-4daa-a119-4a5b45f43f91)

---

## Diagrama de Comunicacion de componentes

![image](https://github.com/user-attachments/assets/e6852864-b8b2-4c92-888e-a03e32c0e742)


---

## Autenticación en los endpoints

Este endpoint requiere autenticación mediante un token Bearer en el encabezado `Authorization`. Además, se deben proporcionar varios encabezados para la identificación del agente y del operador.

### Solicitud del Token
```sh
curl --location 'https://login.microsoftonline.com/19984b4a-950f-45c8-a475-065352ef0766/oauth2/v2.0/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'grant_type=client_credentials' \
--data-urlencode 'client_id={App-Id}' \
--data-urlencode 'client_secret={App-Secret}' \
--data-urlencode 'scope=https://{URL_BASE}/money-transfer/.default'
```

### Encabezados Requeridos
Los siguientes encabezados son necesarios en cada petición que se envie a los endpoints. 

| Encabezado       | Tipo   | Requerido | Descripción |
|------------------|--------|-----------|-------------|
| Accept           | string | Sí        | Indica que la respuesta debe ser en formato JSON. Valor: `application/json`. |
| Agent-Id         | string | Sí        | Identificador único del agente que realiza la solicitud. |
| Agency-Id        | string | Sí        | Identificador único de la agencia asociada al agente. |
| Operator-Id      | string | Sí        | Identificador único del operador que está procesando la solicitud. |
| Terminal-Id      | string | Sí        | Identificador del terminal desde donde se realiza la transacción. |
| API-Key          | string | Sí        | Clave de acceso a la API. |
| Authorization    | string | Sí        | Token de autenticación Bearer generado mediante un proceso de login. |

---

## Ejemplo de respuesta de API V3

```json
{
  "data": {
    "fieldName": "receipt",
    "fieldMessage": "Comprobante generado exitosamente",
    "fieldValue": "12345",
    "fieldType": "string"
  },
  "code": "200",
  "message": "Operación exitosa",
  "errorCode": null,
  "errorDetails": null
}
```

```json
{
  "data": null,
  "code": "400",
  "message": "No current staged money transfer transaction found with temporal code: 206324.",
  "errorCode": "100-10-404",
  "errorDetails": {
    "fields": [
      {
        "fieldName": "technicalMessage",
        "fieldMessage": "The temporal code '206324' was not found in the StagedTransactions collection.",
        "fieldValue": "Not Found",
        "fieldType": "string"
      },
      {
        "fieldName": "referenceId",
        "fieldMessage": "Unique reference identifier for tracking the error.",
        "fieldValue": "txn-206324",
        "fieldType": "string"
      },
      {
        "fieldName": "affectedModule",
        "fieldMessage": "Module where the error occurred.",
        "fieldValue": "MoneyTransferStaging",
        "fieldType": "string"
      },
      {
        "fieldName": "errorOrigin",
        "fieldMessage": "Source of the error.",
        "fieldValue": "CosmosDB query",
        "fieldType": "string"
      }
    ]
  }
}
```

### Estructura de respuesta de API V3
| Campo          | Tipo     | Descripción                                                  |
|----------------|----------|--------------------------------------------------------------|
| `data`         | object   | Contiene los datos específicos de la respuesta sea exitosa.  |
| `code`         | string   | Código de estado de la respuesta (`200` para éxito).         |
| `message`      | string   | Mensaje general de la respuesta que esta dando el API.       |
| `errorCode`    | string   | Código de error (nulo en una respuesta exitosa).             |
| `errorDetails` | object   | Detalles adicionales del error (nulo en éxito).              |

## Ejemplo de Respuesta de Error V1
```json
{
  "code": "100-10-404",
  "message": "No current staged money transfer transaction found with temporal code: 206324.",
  "details": "206324"
}
```

### Códigos de Error
| Código        | Descripción |
|---------------|-------------|
| 100-10-404   | No se encontró una transacción con el código temporal proporcionado. |
| 100-10-401   | Token de autenticación inválido o expirado. |
| 100-10-400   | Algún campo requerido en la petición está ausente o es incorrecto. |

---
