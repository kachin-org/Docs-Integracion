# Documentación Técnica de Integración (v1.0.1)
[[TOC]]

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

## Ejemplo de Respuesta de Error
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

## Endpoint: Consulta de Transacción de envio disponible

**URL:**
```
https://{URLBASE}/transaction-engine/send/v1/moneytransfer/{CTT}
```

**Método:** `GET`

**Formato de respuesta:** `application/json`


### Ejemplo de Solicitud

```sh
curl --location 'https://{URLBASE}/transaction-engine/send/v1/moneytransfer/206328' \
--header 'Accept: application/json' \
--header 'Agent-Id: 0000B0' \
--header 'Agency-Id: 10001' \
--header 'Operator-id: 784DA' \
--header 'Terminal-Id: 7C777' \
--header 'API-Key: {KEY} ' \
--header 'Authorization: Bearer {TOKEN}'
```

### Respuesta Exitosa

#### Ejemplo de Respuesta
```json
{
  "sender": {
    "firstName": "JOE",
    "lastName": "DOE",
    "identityDocument": {
      "identityDocumentTypeCode": "P",
      "identityDocumentTypeName": "Pasaporte",
      "number": "AB78954213"
    }
  },
  "beneficiary": {
    "firstName": "Marie",
    "lastName": "Jane",
    "identityDocument": {
      "identityDocumentTypeCode": "P",
      "identityDocumentTypeName": null,
      "number": ""
    }
  },
  "transaction": {
    "senderTotalAmount": 83.45,
    "senderChargeAmount": 3.45,
    "fee": 3.0,
    "taxes": [
      {
        "taxDescription": "ISD",
        "taxRate": 0.05,
        "taxBase": 0,
        "taxAmount": 0.00
      }
    ],
    "date": "2025-02-13T00:35:01.6300624+00:00",
    "transactionalAuthorizationCode": "341C1AB1A317E9B8",
    "remittanceCompanyName": "",
    "uniqueMoneyTransferCode": "",
    "source": {
      "countryFromCode": "EC",
      "countryFromName": "Ecuador",
      "senderCurrencyCode": "USD",
      "senderCurrencyName": "Dólar americano",
      "senderAmount": 80.0
    },
    "destination": {
      "countryToCode": "CO",
      "countryToName": "Colombia",
      "beneficiaryCurrencyCode": "COP",
      "beneficiaryCurrencyName": "Peso colombiano",
      "beneficiaryAmount": 328865.60
    }
  }
}
```

### Descripción de Campos

#### **1. Datos del Remitente (`sender`)**
| Campo  | Tipo   | Descripción |
|--------|--------|-------------|
| **firstName** | string | Nombre del remitente. |
| **lastName** | string | Apellido del remitente. |
| **identityDocument** | objeto | Información del documento de identidad del remitente. |

#### **1.1. Documento de Identidad (`identityDocument`)**
| Campo  | Tipo   | Descripción |
|--------|--------|-------------|
| **identityDocumentTypeCode** | string | Código del tipo de documento (Ejemplo: `P` para pasaporte). |
| **identityDocumentTypeName** | string | Nombre del tipo de documento (Ejemplo: `Pasaporte`). Puede ser `null` si no está definido. |
| **number** | string | Número del documento de identidad. |

---

#### **2. Datos del Beneficiario (`beneficiary`)**
| Campo  | Tipo   | Descripción |
|--------|--------|-------------|
| **firstName** | string | Nombre del beneficiario. |
| **lastName** | string | Apellido del beneficiario. |
| **identityDocument** | objeto | Información del documento de identidad del beneficiario. |

#### **2.1. Documento de Identidad (`identityDocument`)**
| Campo  | Tipo   | Descripción |
|--------|--------|-------------|
| **identityDocumentTypeCode** | string | Código del tipo de documento (Ejemplo: `P` para pasaporte). |
| **identityDocumentTypeName** | string | Nombre del tipo de documento. Puede ser `null` si no está definido. |
| **number** | string | Número del documento de identidad. Puede ser vacío si no se proporciona. |

---

#### **3. Información de la Transacción (`transaction`)**
| Campo  | Tipo   | Descripción |
|--------|--------|-------------|
| **senderTotalAmount** | float | Monto total enviado por el remitente, incluyendo tarifas e impuestos. |
| **senderChargeAmount** | float | Cargos adicionales aplicados al remitente. |
| **fee** | float | Comisión de la transacción. |
| **taxes** | array | Lista de impuestos aplicados a la transacción. |
| **date** | string | Fecha y hora de la transacción en formato ISO 8601. |
| **transactionalAuthorizationCode** | string | Código único de autorización de la transacción. |
| **remittanceCompanyName** | string | Nombre de la empresa remesadora que procesa la transacción. Puede ser vacío si no se especifica. |
| **uniqueMoneyTransferCode** | string | Código único de la transferencia de dinero. Puede ser vacío si no se genera. |

### **3.1. Detalles de los Impuestos (`taxes`)**
| Campo  | Tipo   | Descripción |
|--------|--------|-------------|
| **taxDescription** | string | Descripción del impuesto aplicado (Ejemplo: `ISD`). |
| **taxRate** | float | Tasa del impuesto aplicada en la transacción. |
| **taxBase** | float | Base sobre la que se calcula el impuesto. |
| **taxAmount** | float | Monto del impuesto aplicado. |

---

#### **4. Origen de la Transferencia (`source`)**
| Campo  | Tipo   | Descripción |
|--------|--------|-------------|
| **countryFromCode** | string | Código del país de origen de la transacción (Ejemplo: `EC` para Ecuador). |
| **countryFromName** | string | Nombre del país de origen de la transacción. |
| **senderCurrencyCode** | string | Código de la moneda utilizada en el país de origen (Ejemplo: `USD`). |
| **senderCurrencyName** | string | Nombre de la moneda utilizada en el país de origen (Ejemplo: `Dólar americano`). |
| **senderAmount** | float | Monto enviado desde el país de origen antes de aplicar tarifas o impuestos. |

---

#### **5. Destino de la Transferencia (`destination`)**
| Campo  | Tipo   | Descripción |
|--------|--------|-------------|
| **countryToCode** | string | Código del país de destino de la transacción (Ejemplo: `CO` para Colombia). |
| **countryToName** | string | Nombre del país de destino de la transacción. |
| **beneficiaryCurrencyCode** | string | Código de la moneda en la que el beneficiario recibirá el dinero (Ejemplo: `COP`). |
| **beneficiaryCurrencyName** | string | Nombre de la moneda en la que el beneficiario recibirá el dinero (Ejemplo: `Peso colombiano`). |
| **beneficiaryAmount** | float | Monto total recibido por el beneficiario en la moneda de destino. |

---

## Endpoint: Proceso de Transacción disponible

**URL:**
```
https://{URLBASE}/transaction-engine/send/v1/moneytransfer
```

**Método:** `POST`

**Formato de respuesta:** `application/json`

### Cuerpo de la Solicitud (`POST`)

```json
{
  "2sCode": "597524"
}
```

### Ejemplo de Solicitud con `cURL`

```sh
curl --location 'https://{URLBASE}/transaction-engine/send/v1/moneytransfer' \
--header 'Accept: application/json' \
--header 'Agent-Id: 0000B0' \
--header 'Agency-Id: 10001' \
--header 'Operator-id: 784DA' \
--header 'Terminal-Id: 7C777' \
--header 'API-Key: {KEY} ' \
--header 'Authorization: Bearer {TOKEN}' \
--data '{
  "2sCode": "597524"
}'
```

### Respuesta Exitosa

#### Ejemplo de Respuesta
```json
{
  "brand": "Kachin",
  "agency": "11001-EA762-F10BE",
  "sender": {
    "firstName": "JOE",
    "lastName": "DOE",
    "identityDocument": {
      "identityDocumentTypeCode": "P",
      "identityDocumentTypeName": "Pasaporte",
      "number": "AB78954213"
    }
  },
 "beneficiary": {
    "firstName": "Marie",
    "lastName": "Jane",
    "identityDocument": {
      "identityDocumentTypeCode": "P",
      "identityDocumentTypeName": null,
      "number": ""
    }
  },
  "transaction": {
    "fee": 1.8,
    "invoice": {
      "subTotal": 0.0,
      "fee": 1.8,
      "taxes": [
        {
          "taxDescription": "ISD",
          "taxRate": 0.05,
          "taxBase": 0.0,
          "taxAmount": 0
        }
      ]
    },
    "date": "2025-02-13T15:27:28.5131331+00:00",
    "transactionalAuthorizationCode": "398FADD42ECFE201",
    "remittanceCompanyName": "Giros Kachin",
    "uniqueMoneyTransferCode": "4450446129",
    "source": {
      "countryFromCode": "EC",
      "countryFromName": "Ecuador",
      "senderCurrencyCode": "USD",
      "senderCurrencyName": "Dólar americano",
      "senderAmount": 5.0
    },
    "destination": {
      "countryToCode": "EC",
      "countryToName": "Ecuador",
      "beneficiaryCurrencyCode": "USD",
      "beneficiaryCurrencyName": "Dólar americano",
      "beneficiaryAmount": 5.0
    },
    "senderTotalAmount": 7.07
  }
}
```

### Explicación de los Campos de la Respuesta

#### Datos Generales
| Campo  | Tipo | Descripción |
|--------|------|-------------|
| brand  | string | Nombre de la marca o entidad que procesa la transacción. |
| agency | string | Código único de la agencia asociada a la transacción. |

#### Datos del Remitente (`sender`)
| Campo  | Tipo | Descripción |
|--------|------|-------------|
| firstName  | string | Nombre del remitente. |
| lastName  | string | Apellido del remitente. |
| identityDocument | objeto | Información del documento de identidad del remitente. |

#### Datos de la Transacción (`transaction`)
| Campo  | Tipo | Descripción |
|--------|------|-------------|
| fee  | float | Comisión de la transacción. |
| invoice  | objeto | Detalle de la factura de la transacción. |
| transactionalAuthorizationCode | string | Código único de autorización de la transacción. |
| remittanceCompanyName | string | Nombre de la empresa remesadora. |
| uniqueMoneyTransferCode | string | Código único de la transferencia de dinero. |

#### Origen de la Transferencia (`source`)
| Campo  | Tipo   | Descripción |
|--------|--------|-------------|
| **countryFromCode** | string | Código del país de origen de la transacción (Ejemplo: `EC` para Ecuador). |
| **countryFromName** | string | Nombre del país de origen de la transacción. |
| **senderCurrencyCode** | string | Código de la moneda utilizada en el país de origen (Ejemplo: `USD`). |
| **senderCurrencyName** | string | Nombre de la moneda utilizada en el país de origen (Ejemplo: `Dólar americano`). |
| **senderAmount** | float  | Monto enviado desde el país de origen antes de aplicar tarifas o impuestos. |

#### Destino de la Transferencia (`destination`)
| Campo  | Tipo   | Descripción |
|--------|--------|-------------|
| **countryToCode** | string | Código del país de destino de la transacción (Ejemplo: `EC` para Ecuador). |
| **countryToName** | string | Nombre del país de destino de la transacción. |
| **beneficiaryCurrencyCode** | string | Código de la moneda en la que el beneficiario recibirá el dinero (Ejemplo: `USD`). |
| **beneficiaryCurrencyName** | string | Nombre de la moneda en la que el beneficiario recibirá el dinero (Ejemplo: `Dólar americano`). |
| **beneficiaryAmount** | float  | Monto total recibido por el beneficiario en la moneda de destino. |

## Endpoint: Consulta de Transacción de pago disponible

**URL:**
```
https://{URLBASE}/transaction-engine/pay/v1/moneytransfer/{CTT}
```

**Método:** `GET`

**Formato de respuesta:** `application/json`


### Ejemplo de Solicitud

```sh
curl --location 'https://{URLBASE}/transaction-engine/pay/v1/moneytransfer/206328' \
--header 'Accept: application/json' \
--header 'Agent-Id: 0000B0' \
--header 'Agency-Id: 10001' \
--header 'Operator-id: 784DA' \
--header 'Terminal-Id: 7C777' \
--header 'API-Key: {KEY} ' \
--header 'Authorization: Bearer {TOKEN}'
```

#### Ejemplo de Respuesta
```json
{
  "sender": {
    "firstName": "JOE",
    "lastName": "DOE"
  },
  "beneficiary": {
    "firstName": "Mary",
    "lastName": "Jane",
    "identityDocument": {
      "identityDocumentTypeCode": "CI",
      "identityDocumentTypeName": "Cédula de identidad",
      "number": "0999999999"
    }
  },
  "transaction": {
    "beneficiaryTotalAmount": 5,
    "date": "2025-02-13T14:50:46.917+00:00",
    "transactionalAuthorizationCode": "",
    "remittanceCompanyName": "Giros Kachin",
    "uniqueMoneyTransferCode": "",
    "source": {
      "countryFromCode": "EC",
      "countryFromName": "Ecuador",
      "senderCurrencyCode": "USD",
      "senderCurrencyName": "Dólar americano",
      "senderAmount": 5
    },
    "destination": {
      "countryToCode": "EC",
      "countryToName": "Ecuador",
      "beneficiaryCurrencyCode": "USD",
      "beneficiaryCurrencyName": "Dólar americano",
      "beneficiaryAmount": 5
    },
    "senderTotalAmount": 5
  }
}
```
### Descripción de Campos
---

#### **1. Datos del Remitente (`sender`)**
| Campo  | Tipo   | Descripción |
|--------|--------|-------------|
| **firstName** | string | Nombre del remitente. |
| **lastName** | string | Apellido del remitente. |

---

#### **2. Datos del Beneficiario (`beneficiary`)**
| Campo  | Tipo   | Descripción |
|--------|--------|-------------|
| **firstName** | string | Nombre del beneficiario. |
| **lastName** | string | Apellido del beneficiario. |
| **identityDocument** | objeto | Información del documento de identidad del beneficiario. |

##### **2.1. Documento de Identidad (`identityDocument`)**
| Campo  | Tipo   | Descripción |
|--------|--------|-------------|
| **identityDocumentTypeCode** | string | Código del tipo de documento (Ejemplo: `CI` para cédula de identidad). |
| **identityDocumentTypeName** | string | Nombre del tipo de documento (Ejemplo: `Cédula de identidad`). |
| **number** | string | Número del documento de identidad. |

---

#### **3. Información de la Transacción (`transaction`)**
| Campo  | Tipo   | Descripción |
|--------|--------|-------------|
| **beneficiaryTotalAmount** | float | Monto total recibido por el beneficiario. |
| **date** | string | Fecha y hora de la transacción en formato ISO 8601. |
| **transactionalAuthorizationCode** | string | Código único de autorización de la transacción. |
| **remittanceCompanyName** | string | Nombre de la empresa remesadora que procesa la transacción. |
| **uniqueMoneyTransferCode** | string | Código único de la transferencia de dinero. |
| **senderTotalAmount** | float | Monto total que paga el remitente, incluyendo impuestos y tarifas. |

---

#### **4. Origen de la Transferencia (`source`)**
| Campo  | Tipo   | Descripción |
|--------|--------|-------------|
| **countryFromCode** | string | Código del país de origen de la transacción (Ejemplo: `EC` para Ecuador). |
| **countryFromName** | string | Nombre del país de origen de la transacción. |
| **senderCurrencyCode** | string | Código de la moneda utilizada en el país de origen (Ejemplo: `USD`). |
| **senderCurrencyName** | string | Nombre de la moneda utilizada en el país de origen (Ejemplo: `Dólar americano`). |
| **senderAmount** | float | Monto enviado desde el país de origen antes de aplicar tarifas o impuestos. |

---

#### **5. Destino de la Transferencia (`destination`)**
| Campo  | Tipo   | Descripción |
|--------|--------|-------------|
| **countryToCode** | string | Código del país de destino de la transacción (Ejemplo: `EC` para Ecuador). |
| **countryToName** | string | Nombre del país de destino de la transacción. |
| **beneficiaryCurrencyCode** | string | Código de la moneda en la que el beneficiario recibirá el dinero (Ejemplo: `USD`). |
| **beneficiaryCurrencyName** | string | Nombre de la moneda en la que el beneficiario recibirá el dinero (Ejemplo: `Dólar americano`). |
| **beneficiaryAmount** | float | Monto total recibido por el beneficiario en la moneda de destino. |

---

## Endpoint: Proceso de Transacción de pago disponible

**URL:**
```
https://{URLBASE}/transaction-engine/pay/v1/moneytransfer
```

**Método:** `POST`

**Formato de respuesta:** `application/json`

### Cuerpo de la Solicitud (`POST`)

```json
{
  "2sCode": "597524"
}
```

### Ejemplo de Solicitud con `cURL`

```sh
curl --location 'https://{URLBASE}/transaction-engine/send/v1/moneytransfer' \
--header 'Accept: application/json' \
--header 'Agent-Id: 0000B0' \
--header 'Agency-Id: 10001' \
--header 'Operator-id: 784DA' \
--header 'Terminal-Id: 7C777' \
--header 'API-Key: {KEY} ' \
--header 'Authorization: Bearer {TOKEN}' \
--data '{
  "2sCode": "597524"
}'
```

### Respuesta Exitosa

#### Ejemplo de Respuesta
```json
{
  "brand": "Kachin",
  "agency": "11001-EA762-F10BE",
  {
  "sender": {
    "firstName": "JOE",
    "lastName": "DOE"
  },
  "beneficiary": {
    "firstName": "Mary",
    "lastName": "Jane",
    "identityDocument": {
      "identityDocumentTypeCode": "CI",
      "identityDocumentTypeName": "Cédula de identidad",
      "number": "0999999999"
    }
  },
  "transaction": {
    "beneficiaryTotalAmount": 5,
    "date": "2025-02-14T14:20:31.8314223+00:00",
    "transactionalAuthorizationCode": "D1ACBE1E684DFB86",
    "remittanceCompanyName": "Giros Kachin",
    "uniqueMoneyTransferCode": "1190460437",
    "source": {
      "countryFromCode": "EC",
      "countryFromName": "Ecuador",
      "senderCurrencyCode": "USD",
      "senderCurrencyName": "Dólar americano",
      "senderAmount": 5
    },
    "destination": {
      "countryToCode": "EC",
      "countryToName": "Ecuador",
      "beneficiaryCurrencyCode": "USD",
      "beneficiaryCurrencyName": "Dólar americano",
      "beneficiaryAmount": 5
    },
    "senderTotalAmount": 0
  }
}
```

### Explicación de los Campos de la Respuesta

---

#### **1. Datos Generales**
| Campo  | Tipo   | Descripción |
|--------|--------|-------------|
| **brand** | string | Nombre de la marca o entidad que procesa la transacción. |
| **agency** | string | Código único de la agencia asociada a la transacción. |

---

#### **2. Datos del Remitente (`sender`)**
| Campo  | Tipo   | Descripción |
|--------|--------|-------------|
| **firstName** | string | Nombre del remitente. |
| **lastName** | string | Apellido del remitente. |

---

#### **3. Datos del Beneficiario (`beneficiary`)**
| Campo  | Tipo   | Descripción |
|--------|--------|-------------|
| **firstName** | string | Nombre del beneficiario. |
| **lastName** | string | Apellido del beneficiario. |
| **identityDocument** | objeto | Información del documento de identidad del beneficiario. |

##### **3.1. Documento de Identidad (`identityDocument`)**
| Campo  | Tipo   | Descripción |
|--------|--------|-------------|
| **identityDocumentTypeCode** | string | Código del tipo de documento (Ejemplo: `CI` para cédula de identidad). |
| **identityDocumentTypeName** | string | Nombre del tipo de documento (Ejemplo: `Cédula de identidad`). |
| **number** | string | Número del documento de identidad. |

---

#### **4. Información de la Transacción (`transaction`)**
| Campo  | Tipo   | Descripción |
|--------|--------|-------------|
| **beneficiaryTotalAmount** | float | Monto total recibido por el beneficiario. |
| **date** | string | Fecha y hora de la transacción en formato ISO 8601. |
| **transactionalAuthorizationCode** | string | Código único de autorización de la transacción. |
| **remittanceCompanyName** | string | Nombre de la empresa remesadora que procesa la transacción. |
| **uniqueMoneyTransferCode** | string | Código único de la transferencia de dinero. |
| **senderTotalAmount** | float | Monto total que paga el remitente, incluyendo impuestos y tarifas. |

---

#### **5. Origen de la Transferencia (`source`)**
| Campo  | Tipo   | Descripción |
|--------|--------|-------------|
| **countryFromCode** | string | Código del país de origen de la transacción (Ejemplo: `EC` para Ecuador). |
| **countryFromName** | string | Nombre del país de origen de la transacción. |
| **senderCurrencyCode** | string | Código de la moneda utilizada en el país de origen (Ejemplo: `USD`). |
| **senderCurrencyName** | string | Nombre de la moneda utilizada en el país de origen (Ejemplo: `Dólar americano`). |
| **senderAmount** | float/null | Monto enviado desde el país de origen antes de aplicar tarifas o impuestos. Puede ser `null` si no se especifica. |

---

#### **6. Destino de la Transferencia (`destination`)**
| Campo  | Tipo   | Descripción |
|--------|--------|-------------|
| **countryToCode** | string | Código del país de destino de la transacción (Ejemplo: `EC` para Ecuador). |
| **countryToName** | string | Nombre del país de destino de la transacción. |
| **beneficiaryCurrencyCode** | string | Código de la moneda en la que el beneficiario recibirá el dinero (Ejemplo: `USD`). |
| **beneficiaryCurrencyName** | string | Nombre de la moneda en la que el beneficiario recibirá el dinero (Ejemplo: `Dólar americano`). |
| **beneficiaryAmount** | float | Monto total recibido por el beneficiario en la moneda de destino. |
