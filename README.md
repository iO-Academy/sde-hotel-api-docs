# Hotel booking API

## API documentation

### Return all rooms - optionally filtered

* **URL**

  /api/rooms

* **Method:**

  `GET`

* **URL Params**

  **Required:**

  There are no required URL params

  **Optional:**

    - `guests=int` - Filter by number of guests the room can accommodate
    - `type=int` - Filter by room type
    - `start=yyyy-mm-dd` - Filter by rooms with availability from a given date
    - `end=yyyy-mm-dd` - Filter by rooms with availabiltiy to a given date
    - Note: start and end filters MUST be used in tandem

  **Example:**

  `/api/rooms?start=2025-01-01&end=2025-12-25&guests=3&type=2`

* **Success Response:**

    * **Code:** 200 <br />
      **Content:** <br />

```json
{
  "message": "Rooms successfully retrieved",
  "data": [
    {
      "id": 1,
      "name": "Penthouse",
      "image": "https://picsum.photos/400/400",
      "min_capacity": 2,
      "max_capacity": 3,
      "type": {
        "id": 1,
        "name": "Sea view"
      }
    }
  ]
}
```

* **Error Response:**

    * **Code:** 422 UNPROCESSABLE CONTENT <br />
      **Content:**
        ```json
        {
          "message": "The guests field must be at least 0. (and 2 more errors)",
          "errors": {
            "guests": [
              "The guests field must be at least 0."
            ],
            "type": [
              "The selected type is invalid."
            ],
            "end": [
              "The end field is required when start is present."
            ]
          }
        }
        ```

### Return a specific room

* **URL**

  /api/rooms/{id}

* **Method:**

  `GET`


  **Example:**

  `/api/rooms/1`

* **Success Response:**

    * **Code:** 200 <br />
      **Content:** <br />

```json
{
  "message": "Room successfully retrieved",
  "data": {
    "id": 1,
    "name": "Penthouse",
    "rate": 500,
    "image": "https://picsum.photos/400/400",
    "min_capacity": 2,
    "max_capacity": 3,
    "description": " Integer maximus lacinia magna. Suspendisse nisi velit, tempus convallis pellentesque nec, malesuada sit amet metus. Nunc ornare laoreet bibendum. Nunc eget enim eget libero volutpat maximus. Nam et gravida ex. Donec condimentum ligula nec nunc bibendum consequat. In finibus consectetur nisl vitae congue. Donec condimentum metus velit, vel egestas lacus tempor id. Etiam vulputate tellus vitae elit porta ultrices. Vivamus sit amet orci massa. Aliquam tortor neque, imperdiet ut auctor sed, condimentum quis risus. ",
    "type": {
      "id": 1,
      "name": "Sea view"
    }
  }
}
```

* **Error Response:**

    * **Code:** 404 NOT FOUND <br />
      **Content:** `{"message": "Room with id 100 not found"}`

### Return all room types

* **URL**

  /api/types

* **Method:**

  `GET`


  **Example:**

  `/api/types`

* **Success Response:**

  * **Code:** 200 <br />
    **Content:** <br />

```json
{
  "message": "Types successfully retrieved",
  "data": [
    {
      "id": 1,
      "name": "Sea view"
    }
  ]
}
```

### Return all future bookings - optionally filtered

* **URL**

  /api/bookings

* **Method:**

  `GET`

* **URL Params**

  **Required:**

  There are no required URL params

  **Optional:**

  - `room_id=int` - Filter bookings that belong to a specific room

  **Example:**

  `/api/bookings?room_id=1`

* **Success Response:**

  * **Code:** 200 <br />
    **Content:** <br />

```json
{
  "message": "Bookings successfully retrieved",
  "data": [
    {
      "id": 14,
      "customer": "Ted",
      "start": "2024-03-15",
      "end": "2024-03-19",
      "created_at": "2024-03-15T14:02:29.000000Z",
      "room": {
        "id": 1,
        "name": "Penthouse"
      }
    }
  ]
}
```

* **Error Response:**

  * **Code:** 422 UNPROCESSABLE CONTENT <br />
    **Content:**
      ```json
      {
          "message": "The selected room id is invalid.",
          "errors": {
              "room_id": [
                  "The selected room id is invalid."
              ]
          }
      }
      ```

### Add a new booking

* **URL**

  /api/bookings

* **Method:**

  `POST`

* **Body Data**

  Must be sent as JSON with the correct headers

  **Required:**

    ```json
    {
      "room_id": integer,
      "customer": "string",
      "guests": integer,
      "start": "yyyy-mm-dd",
      "end": "yyyy-mm-dd"
    }
    ```

  **Optional:**

    There is no optional body data

  **Example:**

  `/api/bookings`

  * **Success Response:**

      * **Code:** 201 CREATED <br />
        **Content:** <br />

    ```json
    {"message": "Booking successfully created"}
    ```

* **Error Response:**

    * **Code:** 400 BAD REQUEST <br />
      **Content:** `{"message": "Start date must be before the end date"}`
  
    * **Code:** 400 BAD REQUEST <br />
      **Content:** `{"message": Room unavailable for the chosen dates"}`

    * **Code:** 400 BAD REQUEST <br />
      **Content:** `{"message": "The penthouse room can only accommodate between 3 and 5 guests"}`
  
    * **Code:** 422 UNPROCESSABLE CONTENT <br />
      **Content:**
    ```json
    {
      "message": "The room id field is required. (and 4 more errors)",
      "errors": {
        "room_id": [
          "The room id field is required."
        ],
        "customer": [
          "The customer field is required."
        ],
        "guests": [
          "The guests field is required."
        ],
        "start": [
          "The start field is required."
        ],
        "end": [
          "The end field is required."
        ]
      }
    }
    ```


### Cancel booking

* **URL**

  /api/bookings/{id}

* **Method:**

  `DELETE`

  **Example:**

  `/api/bookings/1`

* **Success Response:**

  * **Code:** 200 OK <br />
    **Content:** <br />

  ```json
  {"message": "Booking 1 cancelled"}
  ```

* **Error Response:**

  * **Code:** 404 NOT FOUND <br />
    **Content:** `{"message": "Unable to cancel booking, booking 1 not found"}`