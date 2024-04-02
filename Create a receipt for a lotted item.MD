# Call an API to create a receipt for a lotted item

## Pre-conditions:

You will do these pre-conditions in the i21 app.
1. Create an inventory item and set the Lot Tracking to any of the following list below. Note that an item is lotted when the "Lot Tracking" field is not set to `No`.
    - `Yes - Serial Number`
    - `Yes - Manual`
    - `Yes - Manual/Serial Number`

    ![alt text](image-1.png)

2. Configure the company locations for that newly-created lotted item.
![alt text](image-2.png)

## Create `receipt` POST endpoint

### Prepare the IDs

1. Get the `entityId` from the `vendor` endpoint.
    ```http
    GET {{url}}/vendor?filter={'groupOp': 'And', 'rules': [{'op': 'eq', 'field': 'id', 'data': 'ABC'}]}
    Content-Type: application/json
    Authorization: Bearer {{access_token}}

    {
    "data": [
        {
        "accountType": "Company",
        "active": true,
        "contact": "4343",
        "contactInfo": {
            "address1": "",
            "address2": "",
            "city": "Louisville",
            "country": "United States",
            "email": "test@irely.com",
            "fax": "",
            "firstName": "4343",
            "lastName": "4343",
            "mobile": "",
            "phone": "(260) 461-3457 x00000",
            "postalCode": "46774",
            "stateProv": "Kentucky",
            "website": ""
        },
        "dateCreated": null,
        "dateLastUpdated": "2022-12-12T17:46:39.477",
        "dateModified": null,
        "description": " Swift & Company",
        "entity": {
            "entityId": 2278,
            "entityName": " Swift & Company",
            "entityNo": "ABC"
        },
        "entityId": 2278,
        "id": "ABC",
        "isSupplier": true,
        "isTransportTerminal": true,
        "locations": [
        ],
        "originationDate": "2021-03-28T18:09:25.797",
        "shipFromLocationId": 1169,
        "shipFromLocationName": "Main",
        "shipVia": "Common Carrier Trucking Inc",
        "shipViaId": 106,
        "storageLocations": [],
        "useShipperWeight": false,
        "vendorType": "Vendor"
        }
    ],
    "count": 1,
    "currentPage": 1,
    "pageSize": 50,
    "totalPages": 1,
    "totalValueInPage": null,
    "totalValue": null,
    "totalValueFieldName": null,
    "disablePaging": null
    }
    ```
2. Get the company location ID from the `companylocation` endpoint. You can add filtering so it only returns the location information you need. In this example, we will choose "0000 - Home Office". 

    You will need the value of the `locationId` property when creating a receipt. In this example, the `locationId` is `1`.

    ```http
    GET {{url}}/companylocation?filter={'groupOp': 'And', 'rules': [{'op': 'eq', 'field': 'locationName', 'data': '0000 - Home Office'}]}
    Content-Type: application/json
    Authorization: Bearer {{access_token}}

    {
    "data": [
        {
        "locationId": 1,
        "locationName": "0000 - Home Office",
        "locationNumber": "009",
        "locationType": "Office",
        "active": false,
        "address": "344 New Albany R",
        "city": "Moorestown",
        "country": "United States",
        "dateCreated": "2019-05-06T15:18:47.15",
        "dateLastUpdated": "2023-08-29T02:28:53.837",
        "email": "",
        "fax": "",
        "latitude": 39.965957,
        "longitude": -74.971669,
        "phone": "1.888.561.5017",
        "stateProvince": "NJ",
        "storageLocations": [
            {
            "storageLocationId": 153,
            "name": "Gas Tank",
            "description": "Link to TM Site",
            "locationId": 1,
            "address": "",
            "city": "",
            "classification": "Inventory",
            "country": null,
            "countryId": null,
            "latitude": 0.000000,
            "longitude": 0.000000,
            "state": "",
            "vendor": null,
            "vendorId": null,
            "zipCode": ""
            },
            {
            "storageLocationId": 198,
            "name": "G2",
            "description": "iSite1",
            "locationId": 1,
            "address": "",
            "city": "",
            "classification": "Inventory",
            "country": null,
            "countryId": null,
            "latitude": 0.000000,
            "longitude": 0.000000,
            "state": "",
            "vendor": null,
            "vendorId": null,
            "zipCode": ""
            }
        ],
        "website": "",
        "zipPostalCode": "08057"
        }
    ],
    "count": 1,
    "currentPage": 1,
    "pageSize": 50,
    "totalPages": 1,
    "totalValueInPage": null,
    "totalValue": null,
    "totalValueFieldName": null,
    "disablePaging": null
    }
    ```

### Get the `storageLocationId`.
The JSON response of the `companylocation` endpoint includes an array of **storageLocations**. We can use that list to get the ID of the storage location. In this example, we will select `G2` as the storage location. Based on the JSON response above, the ID of the storage location is `198`.