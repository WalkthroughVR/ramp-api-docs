# WALKTHROUGH-RAMP API DOCUMENTATION

## Endpoints
* V2
  * POST /api/v2/add-booking
  * GET /api/v2/order-status/<order_id>
* [Legacy](legacy.md)
  * POST /api/add-booking
  * GET /api/order-status/<order_id>

---
## Accept Order (V2)
*POST **/api/v2/add-booking***

#### Parameters
* **addons** - optional list of addons
  * addons[].**name** - required string
    * Addon's name
    * Valid values:
      * zillow_3d_tour
      * virtual_twilight
      * drone_video
      * walk_through_video
      * custom_domain
  * addons[].**quantity** - optional int
  * addons[].**sqft** - optional int
* **address** - required dictionary
  * address.**line_1** - required string
    * Address line 1 (e.g., street, PO Box, or company name).
  * address.**line_2** - optional string
    * Address line 2 (e.g., apartment, suite, unit, or building).
  * address.**city** - required string
    * City (ex. Denver)
  * address.**state** - required string
    * [State Abbrevation](https://www.bls.gov/respondents/mwr/electronic-data-interchange/appendix-d-usps-state-abbreviations-and-fips-codes.htm), Postal (ex. CO, AL, AK, AZ)
  * address.**zip** - required int
    * ZIP
* **comments** - optional string
  * Comments
* **contact** - required dictionary
  * contact.**name** - required string
    * Contact's name
  * contact.**who** - required enum
    * Possible values are
    * realtor
    * owner
    * tenants
    * none
  * contact.**email** - required string
    * Contact's email
  * contact.**phone** - optional string
    * Contact's phone
  * contact.**comments** - optional string
    * Contact's comments
* **lat** - optional float
  * Latitude
* **lng** - optional float
  * Longitude
* **preferred_scheduled_time_1** - required timestamp
  * Preferred listing schedule date. A UTC timestamp
* **preferred_scheduled_time_2** - required timestamp
  * Preferred listing schedule date. A UTC timestamp
* **main_products** - required list of main products
  * main_products[].**name** - required string
    * Product's name
    * Valid values:
      * hdr_photography
      * 3d_tour
      * floor_plan
      * twilight_photography
      * drone_photos
  * main_products[].**quantity** - optional int
    * Number of photos or the sqft
      * For hdr_photography, valid values are:
        * 25, 30, 35, 40, 45, 50
      * For 3d_tour, valid values are:
        * 0 - 10k sqft
      * For floor_plan, valid values are:
        * 0 - 8k sqft
      * For twilight_photography no need to include quantity
      * For drone_photos no need to include quantity
* **realtor_email** - required string
* **realtor_name** - required string
* **vacant** - optional bool, default = false
  * Is vacant?

#### Response
```json
{
  "status": "success",
  "data": {order_object} # See reference below
}
```

#### Sample Request
```bash
curl --location --request POST 'https://getawalkthrough.com/api/v2/add-booking' \
--header 'Content-Type: application/json' \
--header 'api-key: 74de3164-094d-4456-bda8-33a99f896930' \
--data-raw '{
  "address": {
    "line_1": "2995 Walnut St",
    "line_2": "",
    "city": "Denver",
    "state": "CO",
    "zip": 80205
  },
  "lat": 39.7630124,
  "lng": -104.9817728,
  "main_products": [
    {"name": "hdr_photography", "quantity": 45},
    {"name": "3d_tour", "quantity": 7500},
    {"name": "floor_plan", "quantity": 6000},
    {"name": "twilight_photography"},
    {"name": "drone_photos"}
  ],
  "addons": [
    {"name": "zillow_3d_tour", "quantity": 1000},
    {"name": "virtual_twilight", "quantity": 3},
    {"name": "drone_video"},
    {"name": "walk_through_video"},
    {"name": "custom_domain"}
  ],
  "realtor_name": "Robert Montemayor",
  "realtor_email": "roberto@getawalkthrough.com",
  "preferred_scheduled_time_1": 1666236260,
  "preferred_scheduled_time_2": 1666236260,
  "comments": "",
  "vacant": false,
  "contact": {
    "name": "Robert Montemayor",
    "who": "realtor",
    "email": "roberto123@gmail.com",
    "phone": "09288657242",
  }
}'

```

---
## Order Status (V2)
*GET **/api/v2/order-status/<order_id>***

#### Parameters
* **order_id** - required string(ObjectId)
  * House id in the walkthrough system. A 12-byte Field

#### Sample Request
```bash
curl --location --request GET 'https://getawalkthrough.com/api/v2/order-status/634c6b5dbbcada0fb65187d1' \
--header 'api-key: 74de3164-094d-4456-bda8-33a99f896930'
```

#### Response
```json
{
  "status": "success",
  "data": {order_object} # See reference below
}
```

---
## Success Response
* **status** - string
  * success
* **data** - dictionary 
  * The house object. See reference above
---
## Error Response
* **status** - string
  * error
* **code** - string
  * The error's code if response's status is error. See Error codes below
* **message** - string
  * The error message
---
## Error Codes
* **invalid_method** - 400
  * This action can only be taken in valid format.
* **unauthorized** - 401
  * Unauthorized 
* **missing_parameter** - 400
  * <parameter_name> is required but missing
* **invalid_value** - 400
  * Invalid parameter value
* **missing_quantity** - 400
  * [].quantity for <main_product> is not found
* **missing_name** - 400
  * [].name is not found
* **unhandled_error** - 400
  * Low level error message
