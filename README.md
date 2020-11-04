# swissrets-feedback

## Abstract
The way inquiries to an advert are handled differs between different real estate portals.
This makes it difficult for realtors/advertisers to process inquiries in an automated manner.
This specification sketches a simple API faciliating the exchange of inquiries between a portal and the advertiser.

The standard has to define how to send the data (*protocol*), where to send the data (*addressability*), 
what data to send and what format to use (*data*),
and how to protect the api from unauthorized access (*protection*).

## Specification

### Protocol
Inquiries are sent via the HTTP-Protocol using the POST-Method.

### Addressability
The API consists fo a single URL accepting HTTP-Post-Requests. There are no further requirements concerning the structure
or naming of the URL path. This provides for maximum flexibility. Hence the URL does not contain any information about the property the inquiry is made for.
All information needed to determine the property is in the data submitted in the request's body.

At the moment there is not formal way to communicate the URL. Ideally there will be an extension of the SwissRETS format providing a mechanism to communicating
the URL in the property's data.

### Data
Data is exchanged using the JSON format.

#### Request
The request's properties can be partioned in three parts:

| Field | Type | Is required | Remarks|
| --- | --- | --- | --- |
| propertyId | string | yes | The properties' reference-ID |
| inquiryLanguage | \[a-z\]2 | no | 2-letter language code, e.g. en |
| inquiryGender | string(1) | no | either f, m or empty |
| inquiryFirstName | string(50) | yes ||
| inquiryLastName | string(50) | yes ||
| inquiryStreet | string(50) | no ||
| inquiryStreetNumber | string(10) | no ||
| inquiryPostalCode | string(30) | no ||
| inquiryCity | string(50) | no ||
| inquiryMessage | string(500) | yes | Plaint text message |
| inquiryEMail | string(100) | yes* | May be empty if a phone number is provided |
| inquiryPhone | string(20) | yes* | May be empty if an e-mail-address is provided |
| offerType | buy \| rent | no ||
| requestID | UUID/GUID | yes | This ID can be used for tracking and recognizing duplicate requests |
| requestDate | timestamp | yes | Date and time the request was made |

#### Response
When the request is successfully submitted the service responds with a status 201 (Created) and a JSON-Object with the following properties is returned:

| Field | Type | Remarks |
| --- | --- | --- |
| confirmationId | UUID/GUID | Serves as a recipt that the request has been accepted |
| requestId | string | The request-ID the response is for |
| timeReceived | timestamp | |

In the case of failure the service responds with a status 400. The reason for the failure is stated in the response body.

### Protection
The API is protected using an API-Key in the form of a UUID/GUID. The involved parties have to exchange the API before using the API.
The API-Key is submitted in the request header field with name X-API-Key.

### Example
#### Request
Assume the service is located at https://swissrets.ch/api/feedback. Then the service could be called as follows:

```
curl --location \ 
--request POST 'https://swissrets.ch/api/feedback' \ 
--header 'Accept: application/json' \ 
--header 'X-API-Key: 153b4171-5d17-4aeb-8d4b-3d27e436bc7c' \ 
--header 'Content-Type: application/json' \ 
--data-raw '{ 
  "requestId": "fe09f2d8198f4497b9b8cad397ee2dba",
  "requestDate": "2020-06-22T11:40:07.9507505+02:00", 
  "propertyId": "3394663", 
  "inquiryLanguage": "de", 
  "inquiryGender": "m", 
  "inquiryFirstName": "John", 
  "inquiryLastName": "Doe", 
  "inquiryStreet": "Sunset Boulevard", 
  "inquiryStreetNumber": "17", 
  "inquiryPostalCode": "1234", 
  "inquiryCity": "Nowhere", 
  "inquiryMessage": "This is a test message. Please do not respond.", 
  "inquiryEMail": "john.doe@swissrets.ch", 
  "inquiryPhone": "+41 41 123 45 67", 
  "offerType": "rent" 
}'
```

#### Response
201 Created

```
{
  "confirmationId": "1f2a9f2a6e8e4ed2b8b2bc93100efda7",
  "requestId": "fe09f2d8198f4497b9b8cad397ee2dba",
  "timeReceived": "2020-06-22T11:07:49.7600294+02:00" 
} 
```
