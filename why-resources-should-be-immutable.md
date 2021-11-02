# Why Resources SHOULD be Immutable

## Astract

The idea of this article is to show with a very simple but concrete example why _resources_ SHOULD be immutable.

## Introduction

Let's take as an example of a very simple restaurant web application, which is managing 2 _resources_: `/meals` and `/orders`.

## Immutable Scenario

We will start with the following  6 _API endpoints_.

```text
POST /meals
GET  /meals
GET  /meals/{mealId}
POST /orders
GET  /orders
GET  /orders/{orderId}
```

At the beginning, there are no `/meals` on the menu, and no `/orders` have been received.

```text
GET /meals
204 No Content
```

```text
GET /orders
Authorization: Bearer <Administrator>
204 No Content
```

Lets' the administrator add two `/meals` on the menu.

```text
POST /meals
Authorization: Bearer <Administrator>
{
  "name": "Hamburger",
  "price": 9.99
}
201 Created
{
  "id": "079244a5-3fee-4b09-a774-93b9c606389e",
  "name": "Hamburger",
  "price": 9.99
}
```

```text
POST /meals
Authorization: Bearer <Administrator>
{
  "name": "Pizza",
  "price": 7.99
}
201 Created
{
  "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
  "name": "Pizza",
  "price": 7.99
}
```

```text
GET /meals
200 OK
[
  {
    "id": "079244a5-3fee-4b09-a774-93b9c606389e",
    "name": "Hamburger",
    "price": 9.99
  },
  {
    "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
    "name": "Pizza",
    "price": 7.99
  }
]
```

```text
GET /meals/079244a5-3fee-4b09-a774-93b9c606389e
200 OK
{
  "id": "079244a5-3fee-4b09-a774-93b9c606389e",
  "name": "Hamburger",
  "price": 9.99
}
```

```text
GET /meals/e6bccb34-3bb5-43da-8192-eccbc2680e42
200 OK
{
  "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
  "name": "Pizza",
  "price": 7.99
}
```

> At this stage, there is not much difference between the structure of the responses of `GET /meals` and `GET /meals/{mealId}`. However, as we will add more detailed information of the `/meals`, not all these information will be availble in the response of `GET /meals`.

Now, users of the restaurant web application can create `/orders` for `/meals`.

```text
POST /orders
{
  "email": "user1@mail.com",
  "lines": [
    {
      "mealId": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
      "quantity": 1
    }
  ]
}
201 Created
{
  "id": "a05116c0-90c1-40dd-b879-4755b90d469d",
  "email": "user1@mail.com",
  "lines": [
    {
      "mealId": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
      "quantity": 1
    }
  ],
  "createdAt":"2021-11-02T11:32:00Z"
}
```

```text
POST /orders
{
  "email": "user2@mail.com",
  "lines": [
    {
      "mealId": "079244a5-3fee-4b09-a774-93b9c606389e",
      "quantity": 5
    },
    {
      "mealId": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
      "quantity": 3
    }
  ]
}
201 Created
{
  "id": "aee637ea-6284-4e12-a145-9bc75c9bf4dd",
  "email": "user2@mail.com",
  "lines": [
    {
      "mealId": "079244a5-3fee-4b09-a774-93b9c606389e",
      "quantity": 5
    },
    {
      "mealId": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
      "quantity": 3
    }
  ],
  "createdAt":"2021-11-02T11:45:00Z"
}
```

```text
GET /orders
Authorization: Bearer <Administrator>
200 OK
[
  {
    "id": "a05116c0-90c1-40dd-b879-4755b90d469d",
    "email": "user1@mail.com",
    "createdAt":"2021-11-02T11:32:00Z"
  },
  {
    "id": "aee637ea-6284-4e12-a145-9bc75c9bf4dd",
    "email": "user2@mail.com",
    "createdAt":"2021-11-02T11:45:00Z"
  }
]
```

```text
GET /orders/a05116c0-90c1-40dd-b879-4755b90d469d
Authorization: Bearer <Administrator>
200 OK
{
  "id": "a05116c0-90c1-40dd-b879-4755b90d469d",
  "email": "user1@mail.com",
  "lines": [
    {
      "mealId": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
      "quantity": 1
    }
  ],
  "createdAt":"2021-11-02T11:32:00Z"
}
```

```text
GET /orders/aee637ea-6284-4e12-a145-9bc75c9bf4dd
Authorization: Bearer <Administrator>
200 OK
{
  "id": "aee637ea-6284-4e12-a145-9bc75c9bf4dd",
  "email": "user2@mail.com",
  "lines": [
    {
      "mealId": "079244a5-3fee-4b09-a774-93b9c606389e",
      "quantity": 5
    },
    {
      "mealId": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
      "quantity": 3
    }
  ],
  "createdAt":"2021-11-02T11:45:00Z"
}
```

If the `/meals` stays **immutable**, the administrator can easily retrieve its turnover from its `/orders`.

## Mutable Scenario

```text
POST  /meals
GET   /meals
GET   /meals/{mealId}
PATCH /meals/{mealId}
POST  /orders
GET   /orders
GET   /orders/{orderId}
```

Let's update the `price` of the `/meals`.

```text
PATCH /meals/079244a5-3fee-4b09-a774-93b9c606389e
Authorization: Bearer <Administrator>
{
  "price": 10.99
}
200 OK
{
  "id": "079244a5-3fee-4b09-a774-93b9c606389e",
  "name": "Hamburger",
  "price": 10.99
}
```

```text
PATCH /meals/e6bccb34-3bb5-43da-8192-eccbc2680e42
Authorization: Bearer <Administrator>
{
  "price": 8.99
}
201 Created
{
  "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
  "name": "Pizza",
  "price": 8.99
}
```

```text
GET /meals
200 OK
[
  {
    "id": "079244a5-3fee-4b09-a774-93b9c606389e",
    "name": "Hamburger",
    "price": 10.99
  },
  {
    "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
    "name": "Pizza",
    "price": 8.99
  }
]
```

```text
GET /meals/079244a5-3fee-4b09-a774-93b9c606389e
200 OK
{
  "id": "079244a5-3fee-4b09-a774-93b9c606389e",
  "name": "Hamburger",
  "price": 10.99
}
```

```text
GET /meals/e6bccb34-3bb5-43da-8192-eccbc2680e42
200 OK
{
  "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
  "name": "Pizza",
  "price": 8.99
}
```

Let new users of the restaurant web application create `/orders` for these updated `/meals`.

```text
POST /orders
{
  "email": "user3@mail.com",
  "lines": [
    {
      "mealId": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
      "quantity": 2
    }
  ]
}
201 Created
{
  "id": "10964312-e48e-41eb-a8cf-3d80b1bdd1e1",
  "email": "user3@mail.com",
  "lines": [
    {
      "mealId": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
      "quantity": 2
    }
  ],
  "createdAt":"2021-11-03T11:32:00Z"
}
```

```text
POST /orders
{
  "email": "user4@mail.com",
  "lines": [
    {
      "mealId": "079244a5-3fee-4b09-a774-93b9c606389e",
      "quantity": 7
    },
    {
      "mealId": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
      "quantity": 4
    }
  ]
}
201 Created
{
  "id": "a526daaf-6dcb-4627-9f24-ebc175645fee",
  "email": "user4@mail.com",
  "lines": [
    {
      "mealId": "079244a5-3fee-4b09-a774-93b9c606389e",
      "quantity": 7
    },
    {
      "mealId": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
      "quantity": 4
    }
  ],
  "createdAt":"2021-11-03T11:45:00Z"
}
```

```text
GET /orders
Authorization: Bearer <Administrator>
200 OK
[
  {
    "id": "a05116c0-90c1-40dd-b879-4755b90d469d",
    "email": "user1@mail.com",
    "createdAt":"2021-11-02T11:32:00Z"
  },
  {
    "id": "aee637ea-6284-4e12-a145-9bc75c9bf4dd",
    "email": "user2@mail.com",
    "createdAt":"2021-11-02T11:45:00Z"
  },
  {
    "id": "10964312-e48e-41eb-a8cf-3d80b1bdd1e1",
    "email": "user3@mail.com",
    "createdAt":"2021-11-03T11:32:00Z"
  },
  {
    "id": "a526daaf-6dcb-4627-9f24-ebc175645fee",
    "email": "user4@mail.com",
    "createdAt":"2021-11-03T11:45:00Z"
  }
]
```

```text
GET /orders/a05116c0-90c1-40dd-b879-4755b90d469d
Authorization: Bearer <Administrator>
200 OK
{
  "id": "a05116c0-90c1-40dd-b879-4755b90d469d",
  "email": "user1@mail.com",
  "lines": [
    {
      "mealId": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
      "quantity": 1
    }
  ],
  "createdAt":"2021-11-02T11:32:00Z"
}
```

```text
GET /orders/aee637ea-6284-4e12-a145-9bc75c9bf4dd
Authorization: Bearer <Administrator>
200 OK
{
  "id": "aee637ea-6284-4e12-a145-9bc75c9bf4dd",
  "email": "user2@mail.com",
  "lines": [
    {
      "mealId": "079244a5-3fee-4b09-a774-93b9c606389e",
      "quantity": 5
    },
    {
      "mealId": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
      "quantity": 3
    }
  ],
  "createdAt":"2021-11-02T11:45:00Z"
}
```

```text
GET /orders/10964312-e48e-41eb-a8cf-3d80b1bdd1e1
Authorization: Bearer <Administrator>
200 OK
{
  "id": "10964312-e48e-41eb-a8cf-3d80b1bdd1e1",
  "email": "user3@mail.com",
  "lines": [
    {
      "mealId": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
      "quantity": 2
    }
  ],
  "createdAt":"2021-11-03T11:32:00Z"
}
```

```text
GET /orders/a526daaf-6dcb-4627-9f24-ebc175645fee
Authorization: Bearer <Administrator>
200 OK
{
  "id": "a526daaf-6dcb-4627-9f24-ebc175645fee",
  "email": "user4@mail.com",
  "lines": [
    {
      "mealId": "079244a5-3fee-4b09-a774-93b9c606389e",
      "quantity": 7
    },
    {
      "mealId": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
      "quantity": 4
    }
  ],
  "createdAt":"2021-11-03T11:45:00Z"
}
```

Since the `/meals` have **mutated**, it is not possible to retrieve the turnover from the `/orders`.

## Mutable Scenario with Instant Copy of All Attribute

We could fix the issue by copying **all the properties** of `/meals` when created an `/orders`.

Let's restart from the beginning.

```text
GET /meals
204 No Content
```

```text
GET /orders
Authorization: Bearer <Administrator>
204 No Content
```

Lets' the administrator add two `/meals` on the menu.

```text
POST /meals
Authorization: Bearer <Administrator>
{
  "name": "Hamburger",
  "price": 9.99
}
201 Created
{
  "id": "079244a5-3fee-4b09-a774-93b9c606389e",
  "name": "Hamburger",
  "price": 9.99
}
```

```text
POST /meals
Authorization: Bearer <Administrator>
{
  "name": "Pizza",
  "price": 7.99
}
201 Created
{
  "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
  "name": "Pizza",
  "price": 7.99
}
```

```text
GET /meals
200 OK
[
  {
    "id": "079244a5-3fee-4b09-a774-93b9c606389e",
    "name": "Hamburger",
    "price": 9.99
  },
  {
    "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
    "name": "Pizza",
    "price": 7.99
  }
]
```

```text
GET /meals/079244a5-3fee-4b09-a774-93b9c606389e
200 OK
{
  "id": "079244a5-3fee-4b09-a774-93b9c606389e",
  "name": "Hamburger",
  "price": 9.99
}
```

```text
GET /meals/e6bccb34-3bb5-43da-8192-eccbc2680e42
200 OK
{
  "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
  "name": "Pizza",
  "price": 7.99
}
```

Now, users of the restaurant web application can create `/orders` for `/meals`, but this time, we will copy **all the properties** of `/meals` when creating an `/orders`.

```text
POST /orders
{
  "email": "user1@mail.com",
  "lines": [
    {
      "meal": {
        "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
        "name": "Pizza",
        "price": 7.99
      },
      "quantity": 1
    }
  ]
}
201 Created
{
  "id": "a05116c0-90c1-40dd-b879-4755b90d469d",
  "email": "user1@mail.com",
  "lines": [
    {
      "meal": {
        "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
        "name": "Pizza",
        "price": 7.99
      },
      "quantity": 1
    }
  ],
  "createdAt":"2021-11-02T11:32:00Z"
}
```

```text
POST /orders
{
  "email": "user2@mail.com",
  "lines": [
    {
      "meal": {
        "id": "079244a5-3fee-4b09-a774-93b9c606389e",
        "name": "Hamburger",
        "price": 9.99
      },
      "quantity": 5
    },
    {
      "meal": {
        "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
        "name": "Pizza",
        "price": 7.99
      },
      "quantity": 3
    }
  ]
}
201 Created
{
  "id": "aee637ea-6284-4e12-a145-9bc75c9bf4dd",
  "email": "user2@mail.com",
  "lines": [
    {
      "meal": {
        "id": "079244a5-3fee-4b09-a774-93b9c606389e",
        "name": "Hamburger",
        "price": 9.99
      },
      "quantity": 5
    },
    {
      "meal": {
        "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
        "name": "Pizza",
        "price": 7.99
      },
      "quantity": 3
    }
  ],
  "createdAt":"2021-11-02T11:45:00Z"
}
```

```text
GET /orders
Authorization: Bearer <Administrator>
200 OK
[
  {
    "id": "a05116c0-90c1-40dd-b879-4755b90d469d",
    "email": "user1@mail.com",
    "createdAt":"2021-11-02T11:32:00Z"
  },
  {
    "id": "aee637ea-6284-4e12-a145-9bc75c9bf4dd",
    "email": "user2@mail.com",
    "createdAt":"2021-11-02T11:45:00Z"
  }
]
```

```text
GET /orders/a05116c0-90c1-40dd-b879-4755b90d469d
Authorization: Bearer <Administrator>
200 OK
{
  "id": "a05116c0-90c1-40dd-b879-4755b90d469d",
  "email": "user1@mail.com",
  "lines": [
    {
      "meal": {
        "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
        "name": "Pizza",
        "price": 7.99
      },
      "quantity": 1
    }
  ],
  "createdAt":"2021-11-02T11:32:00Z"
}
```

```text
GET /orders/aee637ea-6284-4e12-a145-9bc75c9bf4dd
Authorization: Bearer <Administrator>
200 OK
{
  "id": "aee637ea-6284-4e12-a145-9bc75c9bf4dd",
  "email": "user2@mail.com",
  "lines": [
    {
      "meal": {
        "id": "079244a5-3fee-4b09-a774-93b9c606389e",
        "name": "Hamburger",
        "price": 9.99
      },
      "quantity": 5
    },
    {
      "meal": {
        "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
        "name": "Pizza",
        "price": 7.99
      },
      "quantity": 3
    }
  ],
  "createdAt":"2021-11-02T11:45:00Z"
}
```

Let's update the `price` of the `/meals`.

```text
PATCH /meals/079244a5-3fee-4b09-a774-93b9c606389e
Authorization: Bearer <Administrator>
{
  "price": 10.99
}
200 OK
{
  "id": "079244a5-3fee-4b09-a774-93b9c606389e",
  "name": "Hamburger",
  "price": 10.99
}
```

```text
PATCH /meals/e6bccb34-3bb5-43da-8192-eccbc2680e42
Authorization: Bearer <Administrator>
{
  "price": 8.99
}
201 Created
{
  "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
  "name": "Pizza",
  "price": 8.99
}
```

```text
GET /meals
200 OK
[
  {
    "id": "079244a5-3fee-4b09-a774-93b9c606389e",
    "name": "Hamburger",
    "price": 10.99
  },
  {
    "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
    "name": "Pizza",
    "price": 8.99
  }
]
```

```text
GET /meals/079244a5-3fee-4b09-a774-93b9c606389e
200 OK
{
  "id": "079244a5-3fee-4b09-a774-93b9c606389e",
  "name": "Hamburger",
  "price": 10.99
}
```

```text
GET /meals/e6bccb34-3bb5-43da-8192-eccbc2680e42
200 OK
{
  "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
  "name": "Pizza",
  "price": 8.99
}
```

Let new users of the restaurant web application create `/orders` for these updated `/meals`.


```text
POST /orders
{
  "email": "user3@mail.com",
  "lines": [
    {
      "meal": {
        "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
        "name": "Pizza",
        "price": 8.99
      },
      "quantity": 2
    }
  ]
}
201 Created
{
  "id": "10964312-e48e-41eb-a8cf-3d80b1bdd1e1",
  "email": "user3@mail.com",
  "lines": [
    {
      "meal": {
        "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
        "name": "Pizza",
        "price": 8.99
      },
      "quantity": 2
    }
  ],
  "createdAt":"2021-11-03T11:32:00Z"
}
```

```text
POST /orders
{
  "email": "user4@mail.com",
  "lines": [
    {
      "meal": {
        "id": "079244a5-3fee-4b09-a774-93b9c606389e",
        "name": "Hamburger",
        "price": 10.99
      },
      "quantity": 7
    },
    {
      "meal": {
        "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
        "name": "Pizza",
        "price": 8.99
      },
      "quantity": 4
    }
  ]
}
201 Created
{
  "id": "a526daaf-6dcb-4627-9f24-ebc175645fee",
  "email": "user4@mail.com",
  "lines": [
    {
      "meal": {
        "id": "079244a5-3fee-4b09-a774-93b9c606389e",
        "name": "Hamburger",
        "price": 10.99
      },
      "quantity": 7
    },
    {
      "meal": {
        "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
        "name": "Pizza",
        "price": 8.99
      },
      "quantity": 4
    }
  ],
  "createdAt":"2021-11-03T11:45:00Z"
}
```

```text
GET /orders
Authorization: Bearer <Administrator>
200 OK
[
  {
    "id": "a05116c0-90c1-40dd-b879-4755b90d469d",
    "email": "user1@mail.com",
    "createdAt":"2021-11-02T11:32:00Z"
  },
  {
    "id": "aee637ea-6284-4e12-a145-9bc75c9bf4dd",
    "email": "user2@mail.com",
    "createdAt":"2021-11-02T11:45:00Z"
  },
  {
    "id": "10964312-e48e-41eb-a8cf-3d80b1bdd1e1",
    "email": "user3@mail.com",
    "createdAt":"2021-11-03T11:32:00Z"
  },
  {
    "id": "a526daaf-6dcb-4627-9f24-ebc175645fee",
    "email": "user4@mail.com",
    "createdAt":"2021-11-03T11:45:00Z"
  }
]
```

```text
GET /orders/a05116c0-90c1-40dd-b879-4755b90d469d
Authorization: Bearer <Administrator>
200 OK
{
  "id": "a05116c0-90c1-40dd-b879-4755b90d469d",
  "email": "user1@mail.com",
  "lines": [
    {
      "meal": {
        "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
        "name": "Pizza",
        "price": 7.99
      },
      "quantity": 1
    }
  ],
  "createdAt":"2021-11-02T11:32:00Z"
}
```

```text
GET /orders/aee637ea-6284-4e12-a145-9bc75c9bf4dd
Authorization: Bearer <Administrator>
200 OK
{
  "id": "aee637ea-6284-4e12-a145-9bc75c9bf4dd",
  "email": "user2@mail.com",
  "lines": [
    {
      "meal": {
        "id": "079244a5-3fee-4b09-a774-93b9c606389e",
        "name": "Hamburger",
        "price": 9.99
      },
      "quantity": 5
    },
    {
      "meal": {
        "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
        "name": "Pizza",
        "price": 7.99
      },
      "quantity": 3
    }
  ],
  "createdAt":"2021-11-02T11:45:00Z"
}
```

```text
GET /orders/10964312-e48e-41eb-a8cf-3d80b1bdd1e1
Authorization: Bearer <Administrator>
200 OK
{
  "id": "10964312-e48e-41eb-a8cf-3d80b1bdd1e1",
  "email": "user3@mail.com",
  "lines": [
    {
      "meal": {
        "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
        "name": "Pizza",
        "price": 8.99
      },
      "quantity": 2
    }
  ],
  "createdAt":"2021-11-03T11:32:00Z"
}
```

```text
GET /orders/a526daaf-6dcb-4627-9f24-ebc175645fee
Authorization: Bearer <Administrator>
200 OK
{
  "id": "a526daaf-6dcb-4627-9f24-ebc175645fee",
  "email": "user4@mail.com",
  "lines": [
    {
      "meal": {
        "id": "079244a5-3fee-4b09-a774-93b9c606389e",
        "name": "Hamburger",
        "price": 10.99
      },
      "quantity": 7
    },
    {
      "meal": {
        "id": "e6bccb34-3bb5-43da-8192-eccbc2680e42",
        "name": "Pizza",
        "price": 8.99
      },
      "quantity": 4
    }
  ],
  "createdAt":"2021-11-03T11:45:00Z"
}
```

Now, the administrator can again easily retrieve its turnover from its `/orders`, but you MUST copy **all the properties** of `/meals` when creating an `/orders`!