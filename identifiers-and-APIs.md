# Identifiers and (REST) APIs

## Summary

_**Definition:**_ An _identifier_ is a string that can distinguish a particular object form any other objects within a given set of objects.

### Option 1 (only server-side identifier)

```text
POST /thingies            | INSERT | non idempotent
PUT  /thingies/{thingyId} | UPDATE | idempotent
GET  /thingies            | LIST   | idempotent
GET  /thingies/{thingyId} | READ   | idempotent
```

### Option 2 (only client-side identifier)

```text
POST /thingies/{thingyId} | INSERT | idempotent
PUT  /thingies/{thingyId} | UPDATE | idempotent
GET  /thingies            | LIST   | idempotent
GET  /thingies/{thingyId} | READ   | idempotent
```

### Option 3 (only client-side identifier)

```text
PUT  /thingies/{thingyId} | UPSERT | idempotent
GET  /thingies            | LIST   | idempotent
GET  /thingies/{thingyId} | READ   | idempotent
```

### Option 4 (server-side or client-side identifier)

```text
POST /thingies            | INSERT | non idempotent
POST /thingies/{thingyId} | INSERT | idempotent
PUT  /thingies/{thingyId} | UPDATE | idempotent
GET  /thingies            | LIST   | idempotent
GET  /thingies/{thingyId} | READ   | idempotent
```

### Option 5 (server-side or client-side identifier)

```text
POST /thingies            | INSERT | non idempotent
PUT  /thingies/{thingyId} | UPSERT | idempotent
GET  /thingies            | LIST   | idempotent
GET  /thingies/{thingyId} | READ   | idempotent
```

## OPTION 1 (only server-side identifier)

### Overview of Option 1

```text
POST /thingies            | INSERT | non idempotent
PUT  /thingies/{thingyId} | UPDATE | idempotent
GET  /thingies            | LIST   | idempotent
GET  /thingies/{thingyId} | READ   | idempotent
```

### Details of Option 1

#### Option 1 | 1st insert without `thingyId`

```text
> POST /thingies
> {
>   "name": "bar"
>   "description": "happy fermi"
> }
< 201 Created
< Location: /thingies/0a16429a-177c-414c-b3eb-302b6e0e226d
< 
< {
<   "id": "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

<details>
<summary>list & read(s)</summary>

```text
> GET /thingies
< 200K
< 
< [
<   "0a16429a-177c-414c-b3eb-302b6e0e226d"
< ]
```

```text
> GET /thingies/0a16429a-177c-414c-b3eb-302b6e0e226d
< 200K
< 
< {
<   "id": "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

</details><br/>

#### Option 1 | 2nd (non idempotent) insert without `thingyId`

```text
> POST /thingies
> {
>   "name": "bar"
>   "description": "happy fermi"
> }
< 201 Created
< Location: /thingies/715fa730-e65c-420b-bd1f-5e76fce0d308
< 
< {
<   "id": "715fa730-e65c-420b-bd1f-5e76fce0d308",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

<details>
<summary>list & read(s)</summary>

```text
> GET /thingies
< 200K
< 
< [
<   "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "715fa730-e65c-420b-bd1f-5e76fce0d308"
< ]
```

```text
> GET /thingies/0a16429a-177c-414c-b3eb-302b6e0e226d
< 200K
< 
< {
<   "id": "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

```text
> GET /thingies/715fa730-e65c-420b-bd1f-5e76fce0d308
< 200K
< 
< {
<   "id": "715fa730-e65c-420b-bd1f-5e76fce0d308",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

</details><br/>

#### Option 1 | 1st update

```text
> PUT thingies/0a16429a-177c-414c-b3eb-302b6e0e226d
> {
>   "name": "foo"
>   "description": "elated khorana"
> }
< 200 OK
< {
<   "id": "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "name": "foo",
<   "description": "elated khorana"
< }
```

<details>
<summary>list & read(s)</summary>

```text
> GET /thingies
< 200K
< 
< [
<   "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "715fa730-e65c-420b-bd1f-5e76fce0d308"
< ]
```

```text
> GET /thingies/0a16429a-177c-414c-b3eb-302b6e0e226d
< 200K
< 
< {
<   "id": "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "name": "foo",
<   "description": "elated khorana"
< }
```

```text
> GET /thingies/715fa730-e65c-420b-bd1f-5e76fce0d308
< 200K
< 
< {
<   "id": "715fa730-e65c-420b-bd1f-5e76fce0d308",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

</details><br/>

#### Option 1 | 2nd (idempotent) update

```text
> PUT thingies/0a16429a-177c-414c-b3eb-302b6e0e226d
> {
>   "name": "foo"
>   "description": "elated khorana"
> }
< 200 OK
< {
<   "id": "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "name": "foo",
<   "description": "elated khorana"
< }
```

<details>
<summary>list & read(s)</summary>

```text
> GET /thingies
< 200K
< 
< [
<   "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "715fa730-e65c-420b-bd1f-5e76fce0d308"
< ]
```

```text
> GET /thingies/0a16429a-177c-414c-b3eb-302b6e0e226d
< 200K
< 
< {
<   "id": "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "name": "foo",
<   "description": "elated khorana"
< }
```

```text
> GET /thingies/715fa730-e65c-420b-bd1f-5e76fce0d308
< 200K
< 
< {
<   "id": "715fa730-e65c-420b-bd1f-5e76fce0d308",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

</details><br/>

## OPTION 2 (only client-side identifier)

### Overview of Option 2

```text
POST /thingies/{thingyId} | INSERT | idempotent
PUT  /thingies/{thingyId} | UPDATE | idempotent
GET  /thingies            | LIST   | idempotent
GET  /thingies/{thingyId} | READ   | idempotent
```

### Details of Option 2

#### Option 2 | 1st insert with `thingyId`

```text
> POST /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
> {
>   "name": "bar"
>   "description": "happy fermi"
> }
< 201 Created
< 
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

<details>
<summary>list & read(s)</summary>

```text
> GET /thingies
< 200K
< 
< [
<   "60ec9e6f-5980-4143-8482-2ac7ed326a19"
< ]
```

```text
> GET /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
< 200K
< 
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

</details><br/>

#### Option 2 | 2nd (idempotent) insert

```text
> POST /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
> {
>   "name": "bar"
>   "description": "happy fermi"
> }
< 200 OK
< 
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

<details>
<summary>list & read(s)</summary>

```text
> GET /thingies
< 200K
< 
< [
<   "60ec9e6f-5980-4143-8482-2ac7ed326a19"
< ]
```

```text
> GET /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
< 200K
< 
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

</details><br/>

_**IMPORTANT:**_ If the second (idempotent) insert does not contain the exact same payload, the API endpoint MUST return an error!

```text
> POST /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
> {
>   "name": "foo"
>   "description": "happy fermi"
> }
< 400 Bad Request
```

#### Option 2 | 1st update

```text
> PUT thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
> {
>   "name": "foo"
>   "description": "elated khorana"
> }
< 200 OK
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "foo",
<   "description": "elated khorana"
< }
```

<details>
<summary>list & read(s)</summary>

```text
> GET /thingies
< 200K
< 
< [
<   "60ec9e6f-5980-4143-8482-2ac7ed326a19"
< ]
```

```text
> GET /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
< 200K
< 
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "foo",
<   "description": "elated khorana"
< }
```

</details><br/>

#### Option 2 | 2nd (idempotent) update

```text
> PUT thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
> {
>   "name": "foo"
>   "description": "elated khorana"
> }
< 200 OK
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "foo",
<   "description": "elated khorana"
< }
```

<details>
<summary>list & read(s)</summary>

```text
> GET /thingies
< 200K
< 
< [
<   "60ec9e6f-5980-4143-8482-2ac7ed326a19"
< ]
```

```text
> GET /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
< 200K
< 
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "foo",
<   "description": "elated khorana"
< }
```

</details><br/>

## OPTION 3 (only client-side identifier)

### Overview of Option 3

```text
PUT  /thingies/{thingyId} | UPSERT | idempotent
GET  /thingies            | LIST   | idempotent
GET  /thingies/{thingyId} | READ   | idempotent
```

### Details of Option 3

#### Option 3 | 1st upsert

```text
> PUT /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
> {
>   "name": "bar"
>   "description": "happy fermi"
> }
< 201 Created
< 
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

<details>
<summary>list & read(s)</summary>

```text
> GET /thingies
< 200K
< 
< [
<   "60ec9e6f-5980-4143-8482-2ac7ed326a19"
< ]
```

```text
> GET /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
< 200K
< 
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

</details><br/>

#### Option 3 | 2nd upsert with the same payload

```text
> PUT /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
> {
>   "name": "bar"
>   "description": "happy fermi"
> }
< 200 OK
< 
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

<details>
<summary>list & read(s)</summary>

```text
> GET /thingies
< 200K
< 
< [
<   "60ec9e6f-5980-4143-8482-2ac7ed326a19"
< ]
```

```text
> GET /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
< 200K
< 
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

</details><br/>

#### Option 3 | 3rd upsert with a different payload

```text
> PUT /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
> {
>   "name": "foo"
>   "description": "elated khorana"
> }
< 200 OK
< 
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "foo"
<   "description": "elated khorana"
< }
```

<details>
<summary>list & read(s)</summary>

```text
> GET /thingies
< 200K
< 
< [
<   "60ec9e6f-5980-4143-8482-2ac7ed326a19"
< ]
```

```text
> GET /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
< 200K
< 
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "foo"
<   "description": "elated khorana"
< }
```

</details><br/>

## OPTION 4 (server-side or client-side identifier)

### Overview of Option 4

```text
POST /thingies            | INSERT | non idempotent
POST /thingies/{thingyId} | INSERT | idempotent
PUT  /thingies/{thingyId} | UPDATE | idempotent
GET  /thingies            | LIST   | idempotent
GET  /thingies/{thingyId} | READ   | idempotent
```

### Details of Option 4

#### Option 4 | 1st insert without `thingyId`

```text
> POST /thingies
> {
>   "name": "bar"
>   "description": "happy fermi"
> }
< 201 Created
< Location: /thingies/0a16429a-177c-414c-b3eb-302b6e0e226d
< 
< {
<   "id": "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

<details>
<summary>list & read(s)</summary>

```text
> GET /thingies
< 200K
< 
< [
<   "0a16429a-177c-414c-b3eb-302b6e0e226d"
< ]
```

```text
> GET /thingies/0a16429a-177c-414c-b3eb-302b6e0e226d
< 200K
< 
< {
<   "id": "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

</details><br/>

#### Option 4 | 2nd (non idempotent) insert without `thingyId`

```text
> POST /thingies
> {
>   "name": "bar"
>   "description": "happy fermi"
> }
< 201 Created
< Location: /thingies/715fa730-e65c-420b-bd1f-5e76fce0d308
< 
< {
<   "id": "715fa730-e65c-420b-bd1f-5e76fce0d308",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

<details>
<summary>list & read(s)</summary>

```text
> GET /thingies
< 200K
< 
< [
<   "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "715fa730-e65c-420b-bd1f-5e76fce0d308"
< ]
```

```text
> GET /thingies/0a16429a-177c-414c-b3eb-302b6e0e226d
< 200K
< 
< {
<   "id": "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

```text
> GET /thingies/715fa730-e65c-420b-bd1f-5e76fce0d308
< 200K
< 
< {
<   "id": "715fa730-e65c-420b-bd1f-5e76fce0d308",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

</details><br/>

#### Option 4 | 1st insert with `thingyId`

```text
> POST /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
> {
>   "name": "bar"
>   "description": "happy fermi"
> }
< 201 Created
< 
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

<details>
<summary>list & read(s)</summary>

```text
> GET /thingies
< 200K
< 
< [
    "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "715fa730-e65c-420b-bd1f-5e76fce0d308",
<   "60ec9e6f-5980-4143-8482-2ac7ed326a19"
< ]
```

```text
> GET /thingies/0a16429a-177c-414c-b3eb-302b6e0e226d
< 200K
< 
< {
<   "id": "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "name": "bar",
<   "description": "happy fermi"
< }

```text
> GET /thingies/715fa730-e65c-420b-bd1f-5e76fce0d308
< 200K
< 
< {
<   "id": "715fa730-e65c-420b-bd1f-5e76fce0d308",
<   "name": "bar",
<   "description": "happy fermi"
< }

```text
> GET /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
< 200K
< 
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

</details><br/>

#### Option 4 | 2nd (idempotent) insert

```text
> POST /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
> {
>   "name": "bar"
>   "description": "happy fermi"
> }
< 200 OK
< 
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

<details>
<summary>list & read(s)</summary>

```text
> GET /thingies
< 200K
< 
< [
<   "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "715fa730-e65c-420b-bd1f-5e76fce0d308",
<   "60ec9e6f-5980-4143-8482-2ac7ed326a19"
< ]
```

```text
> GET /thingies/0a16429a-177c-414c-b3eb-302b6e0e226d
< 200K
< 
< {
<   "id": "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "name": "bar",
<   "description": "happy fermi"
< }

```text
> GET /thingies/715fa730-e65c-420b-bd1f-5e76fce0d308
< 200K
< 
< {
<   "id": "715fa730-e65c-420b-bd1f-5e76fce0d308",
<   "name": "bar",
<   "description": "happy fermi"
< }

```text
> GET /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
< 200K
< 
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

</details><br/>

_**IMPORTANT:**_ If the second (idempotent) insert does not contain the exact same payload, the API endpoint MUST return an error!

```text
> POST /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
> {
>   "name": "foo"
>   "description": "happy fermi"
> }
< 400 Bad Request
```

#### Option 4 | 1st update

```text
> PUT thingies/0a16429a-177c-414c-b3eb-302b6e0e226d
> {
>   "name": "foo"
>   "description": "elated khorana"
> }
< 200 OK
< {
<   "id": "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "name": "foo",
<   "description": "elated khorana"
< }
```

<details>
<summary>list & read(s)</summary>

```text
> GET /thingies
< 200K
< 
< [
<   "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "715fa730-e65c-420b-bd1f-5e76fce0d308",
<   "60ec9e6f-5980-4143-8482-2ac7ed326a19"
< ]
```

```text
> GET /thingies/0a16429a-177c-414c-b3eb-302b6e0e226d
< 200K
< 
< {
<   "id": "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "name": "foo",
<   "description": "elated khorana"
< }
```

```text
> GET /thingies/715fa730-e65c-420b-bd1f-5e76fce0d308
< 200K
< 
< {
<   "id": "715fa730-e65c-420b-bd1f-5e76fce0d308",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

```text
> GET /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
< 200K
< 
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

</details><br/>

#### Option 4 | 2nd (idempotent) update

```text
> PUT thingies/0a16429a-177c-414c-b3eb-302b6e0e226d
> {
>   "name": "foo"
>   "description": "elated khorana"
> }
< 200 OK
< {
<   "id": "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "name": "foo",
<   "description": "elated khorana"
< }
```

<details>
<summary>list & read(s)</summary>

```text
> GET /thingies
< 200K
< 
< [
<   "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "715fa730-e65c-420b-bd1f-5e76fce0d308",
<   "60ec9e6f-5980-4143-8482-2ac7ed326a19"
< ]
```

```text
> GET /thingies/0a16429a-177c-414c-b3eb-302b6e0e226d
< 200K
< 
< {
<   "id": "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "name": "foo",
<   "description": "elated khorana"
< }
```

```text
> GET /thingies/715fa730-e65c-420b-bd1f-5e76fce0d308
< 200K
< 
< {
<   "id": "715fa730-e65c-420b-bd1f-5e76fce0d308",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

```text
> GET /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
< 200K
< 
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

</details><br/>

## OPTION 5 (server-side or client-side identifier)

### Overview of Option 5

```text
POST /thingies            | INSERT | non idempotent
PUT  /thingies/{thingyId} | UPSERT | idempotent
GET  /thingies            | LIST   | idempotent
GET  /thingies/{thingyId} | READ   | idempotent
```

### Details of Option 5

#### Option 5 | 1st insert without `thingyId`

```text
> POST /thingies
> {
>   "name": "bar"
>   "description": "happy fermi"
> }
< 201 Created
< Location: /thingies/0a16429a-177c-414c-b3eb-302b6e0e226d
< 
< {
<   "id": "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

<details>
<summary>list & read(s)</summary>

```text
> GET /thingies
< 200K
< 
< [
<   "0a16429a-177c-414c-b3eb-302b6e0e226d"
< ]
```

```text
> GET /thingies/0a16429a-177c-414c-b3eb-302b6e0e226d
< 200K
< 
< {
<   "id": "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

</details><br/>

#### Option 5 | 2nd (non idempotent) insert without `thingyId`

```text
> POST /thingies
> {
>   "name": "bar"
>   "description": "happy fermi"
> }
< 201 Created
< Location: /thingies/715fa730-e65c-420b-bd1f-5e76fce0d308
< 
< {
<   "id": "715fa730-e65c-420b-bd1f-5e76fce0d308",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

<details>
<summary>list & read(s)</summary>

```text
> GET /thingies
< 200K
< 
< [
<   "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "715fa730-e65c-420b-bd1f-5e76fce0d308"
< ]
```

```text
> GET /thingies/0a16429a-177c-414c-b3eb-302b6e0e226d
< 200K
< 
< {
<   "id": "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

```text
> GET /thingies/715fa730-e65c-420b-bd1f-5e76fce0d308
< 200K
< 
< {
<   "id": "715fa730-e65c-420b-bd1f-5e76fce0d308",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

</details><br/>

#### Option 5 | 1st upsert

```text
> PUT /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
> {
>   "name": "bar"
>   "description": "happy fermi"
> }
< 201 Created
< 
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

<details>
<summary>list & read(s)</summary>

```text
> GET /thingies
< 200K
< 
< [
<   "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "715fa730-e65c-420b-bd1f-5e76fce0d308",
<   "60ec9e6f-5980-4143-8482-2ac7ed326a19"
< ]
```

```text
> GET /thingies/0a16429a-177c-414c-b3eb-302b6e0e226d
< 200K
< 
< {
<   "id": "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

```text
> GET /thingies/715fa730-e65c-420b-bd1f-5e76fce0d308
< 200K
< 
< {
<   "id": "715fa730-e65c-420b-bd1f-5e76fce0d308",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

```text
> GET /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
< 200K
< 
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

</details><br/>

#### Option 5 | 2nd upsert with the same payload

```text
> PUT /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
> {
>   "name": "bar"
>   "description": "happy fermi"
> }
< 200 OK
< 
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

<details>
<summary>list & read(s)</summary>

```text
> GET /thingies
< 200K
< 
< [
<   "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "715fa730-e65c-420b-bd1f-5e76fce0d308",
<   "60ec9e6f-5980-4143-8482-2ac7ed326a19"
< ]
```

```text
> GET /thingies/0a16429a-177c-414c-b3eb-302b6e0e226d
< 200K
< 
< {
<   "id": "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

```text
> GET /thingies/715fa730-e65c-420b-bd1f-5e76fce0d308
< 200K
< 
< {
<   "id": "715fa730-e65c-420b-bd1f-5e76fce0d308",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

```text
> GET /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
< 200K
< 
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

</details><br/>

#### OPTION 5 | 3rd upsert with a different payload

```text
> PUT /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
> {
>   "name": "foo"
>   "description": "elated khorana"
> }
< 200 OK
< 
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "foo"
<   "description": "elated khorana"
< }
```

<details>
<summary>list & read(s)</summary>

```text
> GET /thingies
< 200K
< 
< [
<   "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "715fa730-e65c-420b-bd1f-5e76fce0d308",
<   "60ec9e6f-5980-4143-8482-2ac7ed326a19"
< ]
```

```text
> GET /thingies/0a16429a-177c-414c-b3eb-302b6e0e226d
< 200K
< 
< {
<   "id": "0a16429a-177c-414c-b3eb-302b6e0e226d",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

```text
> GET /thingies/715fa730-e65c-420b-bd1f-5e76fce0d308
< 200K
< 
< {
<   "id": "715fa730-e65c-420b-bd1f-5e76fce0d308",
<   "name": "bar",
<   "description": "happy fermi"
< }
```

```text
> GET /thingies/60ec9e6f-5980-4143-8482-2ac7ed326a19
< 200K
< 
< {
<   "id": "60ec9e6f-5980-4143-8482-2ac7ed326a19",
<   "name": "foo"
<   "description": "elated khorana"
< }
```

</details><br/>
