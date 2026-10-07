# d365-fo-table-generator
Skill para generar tablas en Dynamics 365 Finance & Operations. Permite crear archivos XML de tablas D365 F&O especificando nombre, campos, tipos de datos y relaciones.

## Soporte de tipos

El generador debe permitir más que tipos nativos. Debe admitir:

- Tipos nativos: string, int, int64, real, date, datetime, enum, boolean, container
- Tipos basados en EDT: `type: "EDT"` con `edtName`: por ejemplo `AccountNum`, `AmountCur`, `CustAccount`, `InvoiceId`, `TransDate`, `Name`, `VATNum`
- Tipos basados en enums: `type: "Enum"` con `enumName`: por ejemplo `NoYes`, `DocumentStatus`, `CustBlocked`, `SalesStatus`

## Formato recomendado de entrada

```json
{
  "tableName": "MyInvoiceTable",
  "fields": [
    {
      "name": "InvoiceId",
      "type": "EDT",
      "edtName": "InvoiceId",
      "required": true,
      "isPrimaryKey": true,
      "label": "Invoice ID"
    },
    {
      "name": "CustAccount",
      "type": "EDT",
      "edtName": "CustAccount",
      "required": true,
      "label": "Customer account"
    },
    {
      "name": "AmountCur",
      "type": "EDT",
      "edtName": "AmountCur",
      "required": false,
      "label": "Amount"
    },
    {
      "name": "Approved",
      "type": "Enum",
      "enumName": "NoYes",
      "required": false,
      "label": "Approved"
    },
    {
      "name": "Description",
      "type": "string",
      "length": 255,
      "required": false,
      "label": "Description"
    }
  ],
  "relations": [
    {
      "name": "CustTable_FK",
      "relatedTable": "CustTable",
      "localField": "CustAccount",
      "relatedTableField": "AccountNum",
      "deleteAction": "Restricted"
    }
  ]
}
```

## Reglas del skill

- Debe poder generar XML nativo de tabla para Dynamics 365 F&O.
- Debe aceptar campos con tipos nativos, EDT y enums.
- Debe aceptar relaciones opcionales.
- Debe permitir definir índices opcionales.
- Debe generar una salida lista para pegar en un proyecto de D365FO.
- Debe evitar asumir más de lo que el usuario especifica.

## Salida esperada

La salida debe ser un bloque XML tipo `AxTable` con nodos como:

```xml
<AxTable>
    <Name>MyInvoiceTable</Name>
    <Fields>
        <AxTableField>
            <Name>InvoiceId</Name>
            <ExtendedDataType>InvoiceId</ExtendedDataType>
        </AxTableField>
    </Fields>
    <Relations>
        <AxTableRelation>
            <Name>CustTable_FK</Name>
            <RelatedTable>CustTable</RelatedTable>
            <RelatedField>AccountNum</RelatedField>
            <Field>CustAccount</Field>
        </AxTableRelation>
    </Relations>
</AxTable>
```

## Consideración importante

Si el usuario solo indica: nombre de tabla, campos, tipos de datos y relaciones opcionales, el skill debe generar un XML válido para D365FO sin requerir definición compleja del resto del artefacto.
