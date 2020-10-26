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
| language | \[a-z\]2 | no | 2-letter language code, e.g. en |
| gender | string(1) | no | either f, m or empty |
| firstName | string(50) | yes ||
| lastName | string(50) | yes ||
| street | string(50) | no ||
| streetNumber | string(10) | no ||
| postalCode | string(30) | no ||
| city | string(50) | no ||
| message | string(500) | yes | Plaint text message |
| responseEMail | string(100) | yes* | May be empty if a phone number is provided |
| responsePhone | string(20) | yes* | May be empty if an e-mail-address is provided |
| offerType | buy \| rent | no ||
| requestID | UUID/GUID | yes | This ID can be used for tracking and recognizing duplicate requests |
| requestDate | datetime | yes | Date and time the request was made |
