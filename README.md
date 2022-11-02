# WALKTHROUGH-RAMP API DOCUMENTATION

## Endpoints
* POST /api/add-booking
* GET /api/order-status/<order_id>

---
## Accept Order
*POST **/api/add-booking***

#### Parameters
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
* **lat** - optional float
  * Latitude
* **lng** - optional float
  * Longitude
* **sq_ft** - required int
  * House's square feet
* **addons** - required dictionary
  * addons.**zillow_3d** - required bool
  * addons.**aerial_addon** - required bool
  * addons.**custom_domain** - required bool
  * addons.**floor_plans** - required bool
  * addons.**twilight** - required bool
* **realtor_name** - required string
* **realtor_email** - required string
* **preferred_scheduled_time_1** - required timestamp
  * Preferred listing schedule date. A UTC timestamp
* **preferred_scheduled_time_2** - required timestamp
  * Preferred listing schedule date. A UTC timestamp
* **comments** - optional string
  * Comments
* **vacant** - optional bool, default = false
  * Is vacant?
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

#### Response
```json
{
  "status": "success",
  "data": {order_object} # See reference below
}
```

#### Sample Request
```bash
curl --location --request POST 'https://getawalkthrough.com/api/add-booking' \
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
  "sq_ft": 900,
  "product_type": "photography_package",
  "addons": {
    "zillow_3d": true,
    "aerial_addon": true,
    "custom_domain": false,
    "floor_plans": false,
    "twilight": false
  },
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

## Order Status
*GET **/api/order-status/<order_id>***

#### Parameters
* **order_id** - required string(ObjectId)
  * House id in the walkthrough system. A 12-byte Field

#### Sample Request
```bash
curl --location --request GET 'https://getawalkthrough.com/api/order-status/634c6b5dbbcada0fb65187d1' \
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

## The Order Object
* **order_id** - string
  * House's id in the walkthrough system.
* **order_status** - string
  * Listing's status. Possible values are:
  * **unscheduled**
    * Needs To Be Scheduled
    * Need To Know How To Get Access
    * Scheduled Awaiting Payment
  * **proofing**
    * Processed
    * Photos / videos undergoing proofing and QA
  * **completed**
    * Completed
  * **scheduled**
    * Waiting For Processing
    * Processing
    * Scanned
    * Arrived
    * Ready For Photography
* **appointment** - timestamp
  * Scheduled date. A UTC timestamp
* **matterport_link** - string
  * The matterport link
* **dropbox_link** - string
  * The dropbox link

#### Sample Response
```json
{
    "order_id": "62827271d23e037077c74591",
    "order_status": "scheduled",
    "appointment": 1666066918,
    "matterport_link": "",
    "dropbox_link": "https://www.dropbox.com/sh/bhhwl2opxfmna1d/AAADyVTtCFBGDc_4lhREQgvca?dl=0",
}
```
---
## Error Codes
* **invalid_method** - 400
  * This action can only be taken in valid format.
* **unauthorized** - 401
  * Unauthorized 
* **outside_service_area** - 400
  * We don't currently serve that zip code. However, we sent a message to our operations team to see if we can make an exception. You can also call support at 303-900-0469.
* **duplicate_entry** - 400
  * It looks like we're already working on that address with you. Go to your dashboard to view the current status or call support at 303-900-0469.
* **not_found** - 404
  * "Error 404 not found"
* **sqft_below_zero** - 400
  * "We need you to put in a square footage other than 0. This is how we calculate how long your photography shoot will take, and we don't want to have to leave before we are done because of an incorrect square footage.
