# Biila Go – API 1.0 description

**Production API:** https://go.biila.io/api/v1
**Testing API:** https://test.omakuski.fi/api/v1

To use the API, a valid *access token* is needed. It can be retrieved from authentication endpoint using *provided client_id* and *client secret*. Token endpoint can be found from address: `[domain]/token`.


# Endpoints
| Method | Url               | Description                                                | Details                                                                                                                                                                      |
| ------ | ----------------- | ---------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `GET`    | *v1/orders*       | List all/filtered orders.                                  | [GET v1/orders](#get-v1orders-–-list-orders)          |
| `GET`    | *v1/orders/[id]*  | Show details of a order.                                   | [GET v1/orders/[id]](#get-v1ordersid-–-show-order-details)   |
| `POST`   | *v1/orders/offer* | Get a starting price for a drive for given route and time. | [POST v1/orders/offer](#post-v1ordersoffer-–-get-a-starting-price) |
| `POST`   | *v1/orders*       | Store a new order.                                         | [POST v1/orders](#post-v1orders-–-create-order)       |



# Reading this document

By default, all parameters in requests are required. Optional parameters are wrapped in square brackets `[like_this]`. Often these parameters are still required, it some other condition is met. This is always specified in description field.



# **GET v1/orders** – List orders
## Response
| Field                | Type     | Description                                                                                                                                                                                      |
| -------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **`id`**             | int      | ID of the order.                                                                                                                                                                                 |
| **`customer_name`**  | string   | Customer name (first name – last name).                                                                                                                                                          |
| **`customer_phone`** | string   | Customer phone number.                                                                                                                                                                           |
| **`status`**         | string   | Order status (see [#Statuses](#statuses)) |
| **`car_reg`**        | string   | Car register licence.                                                                                                                                                                            |
| **`car_brand`**      | string   | Car brand.                                                                                                                                                                                       |
| **`car_model`**      | string   | Car model.                                                                                                                                                                                       |
| **`created_at`**     | datetime | When the order was created.                                                                                                                                                                      |


**Example response**
```json
[
  {
    "id": 1337,
    "customer_name": "Matti Meikäläinen",
    "customer_phone": "0401234567",
    "status": "DONE",
    "car_reg": "ABC-123",
    "car_brand": "Toyota",
    "car_model": "Corolla",
    "created_at": "2016-08-25T18:57:01.000000+03:00"
  },
  {
    "id": 1338,
    "customer_name": "Heikki Heikäläinen",
    "customer_phone": "0412345678",
    "status": "CONFIRMED",
    "car_reg": "LOL-112",
    "car_brand": "Opel",
    "car_model": "Astra",
    "created_at": "2019-03-03T12:24:41.000000+03:00"
  }
]
```




# **GET v1/orders/\<id\>** – Show order details


## Response
| Field                                   | Type             | Description                                                                                                                                                                                      |
| --------------------------------------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **`id`**                                | int              | ID of the order.                                                                                                                                                                                 |
| **`customer_name`**                     | string           | Customer name (first name – last name).                                                                                                                                                          |
| **`customer_phone`**                    | string           | Customer phone number.                                                                                                                                                                           |
| **`customer_email`**                    | null/string      | Customer email.                                                                                                                                                                                  |
| **`status`**                            | string           | Order status (see [#Statuses](#statuses)) |
| **`car_reg`**                           | string           | Car register licence.                                                                                                                                                                            |
| **`car_brand`**                         | string           | Car brand.                                                                                                                                                                                       |
| **`car_model`**                         | string           | Car model.                                                                                                                                                                                       |
| **`created_at`**                        | datetime         | When the order was created.                                                                                                                                                                      |
| **`drives`**                            | array            | Array containing all the drives in that order.                                                                                                                                                   |
| `drives.*.id`                     | int              | Drive ID.                                                                                                                                                                                        |
| `drives.*.title`                  | string           | Drive title. In format: “*City from – City to*”                                                                                                                                                  |
| `drives.*.duration`               | string           | Drive duration in human readable format (e.g. “*2h 13min*”).                                                                                                                                     |
| `drives.*.from`                   | *address* object | Starting address (see [#Address object](#address-object)).                                                                                                                                                                                |
| `drives.*.to`                     | *address* object | Destination address (see [#Address object](#address-object)).                                                                                                                                                                             |
| `drives.*.departs_at`             | datetime         | When the drive should depart.                                                                                                                                                                    |
| `drives.*.arrives_at`             | datetime         | When the drive should arrive (calculated from departs_at).                                                                                                                                       |
| `drives.*.arrive_flex`            | null/datetime    | If a flexible arrival time is in use, this tells the latest allowed arrival time. If this is `null`, flexible arrival time is not in use.                                                        |
| `drives.*.departed_at`            | null/datetime    | When the drive actually departed. If null, drive has not yet departed.                                                                                                                           |
| `drives.*.arrived_at`             | null/datetime    | When the drive actually arrived. If null, drive has not yet arrived.                                                                                                                             |
| `drives.*.photos`                 | array            | Contains array of url’s of the photos.                                                                                                                                                           |
| `drives.*.photos_after`           | array            | Contains array of url’s of the photos.                                                                                                                                                           |
| `drives.*.info`                   | string           | Additional information about the order.                                                                                                                                                          |
| `drives.*.driver`                 | null/object      | Contains driver details if driver is decided.                                                                                                                                                    |
| `drives.*.driver.name`            | string           | Driver name (first name, last name)                                                                                                                                                              |
| `drives.*.driver.phone`           | string           | Driver phone number.                                                                                                                                                                             |
| `drives.*.driver.email`           | string           | Driver email address.                                                                                                                                                                            |
| `drives.*.auction`                | null/object      | Contains auction details if one is launched.                                                                                                                                                     |
| `drives.*.auction.starts_at`      | datetime         | When will the auction start (started).                                                                                                                                                           |
| `drives.*.auction.ends_at`        | datetime         | When will the auction end (ended).                                                                                                                                                               |
| `drives.*.auction.winner_decided` | boolean          | Tells if the auction winner is decided.                                                                                                                                                          |
| `drives.*.auction.price`          | float            | If auction has ended, this tells the final price.                                                                                                                                                |


**Example response**

```json
{
  "id": 12345,
  "customer_name": "Asta Asiakas / Saka",
  "customer_phone": "0401234567",
  "customer_email": null,
  "status": "CONFIRMED",
  "car_reg": "ABC-123",
  "car_brand": "Toyota",
  "car_model": "Corolla",
  "drives": [
    {
      "id": 54321,
      "title": "Oulu – Jyväskylä",
      "duration": "4h 6min",
      "from_address": "Takojantie 1",
      "from_city": "Oulu",
      "from_zip": "90420",
      "to_address": "Seppäläntie 14",
      "to_city": "Jyväskylä",
      "to_zip": "40320",
      "departs_at": "2019-06-19T15:00:00+03:00",
      "arrives_at": "2019-06-19T19:06:44+03:00",
      "arrive_flex": "2019-06-20T18:00:00+03:00",
      "departed_at": null,
      "arrived_at": null,
      "photos": [
        "https://biila-go.s3.eu-north-1.amazonaws.com/drive-photos/1234/f0o.jpeg",
        "https://biila-go.s3.eu-north-1.amazonaws.com/drive-photos/1234/b4r.jpeg"
      ],
      "photos_after": [],
      "driver": {
        "name": "Kusti Polkija",
        "phone": "0401234567",
        "email": "kusti.polkija@example.org"
      },
      "auction": {
        "starts_at": "2019-06-19T10:26:57+03:00",
        "ends_at": "2019-06-19T10:48:42+03:00",
        "winner_decided": true,
        "price": 59
      },
      "info": "Mikäli toimitus tapahtuu aukioloaikojen ulkopuolella, sovi tästä yhteyshenkilön kanssa."
    }
  ],
  "created_at": "2019-06-18T17:37:35.000000+03:00"
}
```



# **POST v1/orders/offer** – Get a starting price


## Request
| Field                  | Type             | Description                                                                                                                                                          |
| ---------------------- | ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`from`**             | *address* object | Starting address (see [#Address object](#address-object)).                                                                                                                                                    |
| **`to`**               | *address* object | Destination address (see [#Address object](#address-object)).                                                                                                                                                 |
| **`[departs_at]`** | datetime         | Departure time (if not specified, current time is used).                                                                                                             |
| **`return`**           | boolean          | If true, return trip is wanted.                                                                                                                                      |
| **`[return_at]`**  | datetime         | When the return should depart.<br>***Required***, if `return == true`.                                                                                               |
| **`[return_to]`**  | *address* object | If you want the vehicle to be returned to different address than starting address, use this. If this is not defined, the starting address is used as return address (see [#Address object](#address-object)). |


**Example request**

```json
{
  "from": {
    "address": "Takojantie 1",
    "zip": "90420",
    "city": "Oulu",
  },
  "to": {
    "address": "Zatelliitintie 14",
    "zip": "90440",
    "city": "Kempele",
  },
  "departs_at": "2019-08-23T12:00:00.000000+03:00",
  "return": true,
  "return_at": "2019-08-24T12:00:00.000000+03:00"
  "return_to": {
    "address": "Isokatu 51",
    "zip": "90100",
    "city": "Oulu"
  }
}
```


----------


## Response

If request is successful, a **`"201 OK"`** response is returned along with following data:

| Field                        | Type  | Description                                                                                                                    |
| ---------------------------- | ----- | ------------------------------------------------------------------------------------------------------------------------------ |
| **`total_price`**            | float | Estimated starting price of the whole order in euros.                                                                          |
| **`drives`**                 | array | Contains drives of the order. First drive in the array is the first drive. The second one is return trip if that is requested. |
| `drives.*.start_price` | float | Starting price of the drive in euros.                                                                                          |
| `drives.*.duration`    | int   | Drive duration in seconds.                                                                                                     |


**Example response**

```json
{
  "drives": [
    {
      "start_price": 12,
      "duration": 585
    },
    {
      "start_price": 8,
      "duration": 585
    },
  ],
  "total_price": 20
}
   ```




# **POST v1/orders** – Create order


## Request
| Field                            | Type             | Description                                                                                                                                                                           |
| -------------------------------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`store_id`**                   | int              | ID of the store this order belongs to.                                                                                                                                                |
| **`only_pro`**                   | boolean          | Set true, if the order is only available for pro drivers.                                                                                                                             |
| **`customer`**                   | object           |                                                                                                                                                                                       |
| `customer.name`            | string           | Customer’s whole name (first name – last name).                                                                                                                                       |
| `customer.phone`           | string           | Customer’s phone number.                                                                                                                                                              |
| `[customer.email]`       | string           | Customer’s email *Required* if `payment_type == ONLINE`.                                                                                                                              |
| `customer.send_sms`        | boolean          | Should we send a message to the customer about the order.                                                                                                                             |
| `customer.payment_type`    | string           | Customer’s payment type: `BILL` / `ONLINE`.                                                                                                                                           |
| **`vehicle`**                    | object           |                                                                                                                                                                                       |
| `vehicle.brand`            | string           | Vehicle’s brand (e.g. “*Audi*”)                                                                                                                                                       |
| `vehicle.model`            | string           | Vehicles model (e.g. “*A6*”)                                                                                                                                                          |
| `vehicle.reg`              | string           | Register license.                                                                                                                                                                     |
| `[vehicle.year_model]`   | int              | Vehicle’s year model.<br>***Required*** if `only_pro == false`.                                                                                                                       |
| `[vehicle.transmission]` | string           | Vehicle transmission: `MANUAL` / `AUTOMATIC`.<br>***Required*** if `only_pro == false`.                                                                                               |
| `[vehicle.seats]`        | int              | Number of seats in the vehicle.<br>***Required*** if `only_pro == false`.                                                                                                             |
| **`drive`**                      | object           |                                                                                                                                                                                       |
| `drive.departs_at`         | datetime         | When should the drive depart.                                                                                                                                                         |
| `drive.arrive_flex`        | boolean          | If true, a flexible arrival time is allowed.                                                                                                                                          |
| `[drive.arrive_flex_at]` | datetime         | The last allowed arrival time.<br>***Required***, if `arrive_flex == true`.                                                                                                           |
| `drive.from`               | *address* object | Starting address (see [#Address object](#address-object)).                                                                                                                                                                     |
| `drive.to`                 | *address* object | Destination address (see [#Address object](#address-object)).                                                                                                                                                                  |
| `drive.return`             | boolean          | If true, return trip is wanted.                                                                                                                                                       |
| `[drive.return_at]`      | datetime         | When the return is wanted (if *flexible arrival time* is in use, this specifies the earliest allowed time when the vehicle can be picked up).<br>***Required***, if `return == true`. |
| `[drive.return_to]`      | *address* object | If you want the vehicle to be returned to different address than starting address, use this. If this is not defined, the starting address is used as return address (see [#Address object](#address-object)).                  |


**Example request**

```json
{
  "store_id": 159,
  "only_pro": true,
  "customer": {
    "name": "Matti Mallikas",
    "phone": "0451234567",
    "email": "matti@example.org",
    "send_sms": true,
    "payment_type": "BILL"
  },
  "vehicle": {
    "brand": "Opel",
    "model": "Vectra",
    "reg": "ABC-123",
    "year_model": "2007",
    "transmission": "MANUAL",
    "seats": 5
  },
  "drive": {
    "departs_at": "2019-09-05T10:00:00.000000+00:00",
    "arrive_flex": false,
    "arrive_flex_at": null,
    "from": {
      "address": "Alasintie 8",
      "zip": "90100",
      "city": "Oulu"
    },
    "to": {
      "address": "Zatelliitintie 14",
      "zip": "90440",
      "city": "Kempele"
    },
    "return": true,
    "return_at": "2019-09-07T10:00:00.000000+00:00",
    "return_to": {
      "address": "Isokatu 51",
      "zip": "90100",
      "city": "Oulu"
    }
  }
}
```


----------


## Response

If creation is successful, a **`"201 Created"`** response is returned along with following data:

| Field      | Type | Description                    |
| ---------- | ---- | ------------------------------ |
| `order_id` | int  | ID of the newly created order. |

**Example response**

```json
{
  "order_id": 12345
}
```


# Errors
| Error message           | Code | Description                                                                                                                                                                   |
| ----------------------- | ---- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| *Unprocessable Entity*  | 422  | Invalid input data. Make sure that all input fields follow given validation rules and all required fields are given.                                                          |
| *Forbidden*             | 403  |                                                                                                                                                                               |
| *Not Found*             | 404  | Requested item is not found from database or the client does not have access to it (client can only access own stores’ items). For example, SAKA can only view SAKA’s orders. |
| *Unauthorized*          | 401  | In most cases, access token is expired or it has been revoked for some reason. Try refreshing it through authentication endpoint.                                             |
| *Internal Server Error* | 500  | This is a sign of an error in our end and should never happen. If it happens though, please contact us.                                                                       |

If any of these errors appear even though they shouldn’t, please contact Ninjami: https://ninjami.com/contact.


# Other details


## Datetime

Every datetime field must be in **ISO-8601** format with timezone, exactly in the form given below:

    Y-m-d\TH:i:s.uP

For example:

    2019-08-23T10:45:50.000000+03:00

This works both ways; every datetime field coming out from the api is in this exact format.


## Address object

All street addresses in the API are transmitted as *address* objects, which contains the street address, zip/postal code and city.

| Field         | Type           | Description                                                    |
| ------------- | -------------- | -------------------------------------------------------------- |
| **`address`** | string         | Street address with house number, e.g. “*Aleksanterinkatu 2A*” |
| **`zip`**     | numeric string | Zip/postal code.                                               |
| **`city`**    | string         | City.                                                          |



## Statuses

Orders have different statuses.

| Key         | Translation (fi) | Description                                                               |
| ----------- | ---------------- | ------------------------------------------------------------------------- |
| `CONFIRMED` | *Vahvistettu*    | The order has been added to the system.                                   |
| `DRIVING`   | *Ajossa*         | One of the drives of this order is in drive.                              |
| `ACTION`    | *Toimenpiteissä* | The vehicle is in action (e.g. in a service).                             |
| `READY`     | *Noudettavissa*  | Action has been completed and the vehicle is ready to be picked up again. |
| `DONE`      | *Valmis*         | Order is done; all drives and actions have been completed.                |
| `CANCELED`  | *Peruttu*        | Order has been canceled.                                                  |


