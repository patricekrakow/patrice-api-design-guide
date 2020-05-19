## How to represent a many-to-many relationship with REST API endpoints

### One Implicit Single Relation

Let's say that we have the two concept `thingy` and `gizmo` that have one implicit single **many-to-many** relationship.

We can start representing the **_reading side_** of such a situation with the following API endpoints:

* `get /thingies` lists all `thingies`
* `get /thingies/{thingyId}` gets the details of a specific `thingy`
* `get /thingies/{thingyId}/gizmos` lists the `gizmos` that have an implicit single relation with a specific `thingy`
* `get /gizmos` lists all `gizmos`
* `get /gizmos/{gizmoId}` gets the details of a specific `gizmo`
* `get /gizmos/{gizmoId}/thingies` lists the `thingies` that have an implicit single relation with a specific `gizmo`

You may ask why I did not put the following API endpoints:

* `get /thingies/{thingyId}/gizmos/{gizmoId}`
* `get /gizmos/{gizmoId}/thingies/{thingyId}`

I do think we SHOULD avoid them. The `gizmoId` MUST (uniquely) identified a specific `gizmo` across all past, present and future `thingies`, so it is not necessary (i.e. stupid) to specify a particular `thingy` to retrieve a specific `gizmo`. The symmetric argument is of course also valid: the `thingyId` MUST (uniquely) identified a specific `thingy` across all past, present and future `gizmos`, so it is not necessary (i.e. stupid) to specify a particular `gizmo` to retrieve a specific `thingy`.

On the **_creation side_**, we have:

* `post /thingies` creates a `thingy`
* `post /thingies/{thingyId}/gizmos` creates a `gizmo` that has an implicit unique relation with a specific `thingy`
* `post /gizmos` creates a `gizmo`
* `post /gizmos/{gizmoId}/thingies` creates a `thingy` that has an implicit unique relation with a specific `gizmo`
* `post /thingies/{thingyId}/gizmos/{gizmoId}` creates an implicit unique relation between a specific `thingy` and a specific `gizmo`
* `post /gizmos/{gizmoId}/thingies/{thingyId}` creates an implicit unique relation between a specific `gizmo` and a specific `thingy`

Let's continue with the **_update side_** with the following API endpoints:

* `put /thingies/{thingyId}` updates a specific `thingy` using a full representation
* `patch /thingies/{thingyId}` updates a specific `thingy` using a partial representation
* `put /gizmos/{gizmoId}` updates a specific `gizmo` using a full representation
* `patch /gizmos/{gizmoId}` updates a specific `gizmo` using a partial representation

In this case of an implicit unique relation, we will first suppose that the relation itself does not change, that it does not have any attributes. The relation between a specific `thingy` and a specific `gizmo` is there or not, but it cannot be updated.

Finally, the **_delete side_** is quite straightforward:

* `delete /thingies/{thingyId}` deletes a specific `thingy`
* `delete /gizmos/{gizmoId}` deletes a specific `gizmo`
* `delete /thingies/{thingyId}/gizmos/{gizmoId}` delete the implicit unique relation between a specific `thingy` and a specific `gizmo`
* `delete /gizmos/{gizmoId}/thingies/{thingyId}` delete the implicit unique relation between a specific `gizmo` and a specific `thingy`

#### Summary of One Implicit Single Relation

* `post /thingies` creates a `thingy`
* `post /thingies/{thingyId}/gizmos` creates a `gizmo` that has an implicit unique relation with a specific `thingy`
* `post /gizmos` creates a `gizmo`
* `post /gizmos/{gizmoId}/thingies` creates a `thingy` that has an implicit unique relation with a specific `gizmo`
* `post /thingies/{thingyId}/gizmos/{gizmoId}` creates an implicit unique relation between a specific `thingy` and a specific `gizmo`
* `post /gizmos/{gizmoId}/thingies/{thingyId}` creates an implicit unique relation between a specific `gizmo` and a specific `thingy`
* `get /thingies` lists all `thingies`
* `get /thingies/{thingyId}` gets the details of a specific `thingy`
* `get /thingies/{thingyId}/gizmos` lists the `gizmos` that have an implicit single relation with a specific `thingy`
* `get /gizmos` lists all `gizmos`
* `get /gizmos/{gizmoId}` gets the details of a specific `gizmo`
* `get /gizmos/{gizmoId}/thingies` lists the `thingies` that have an implicit single relation with a specific `gizmo`
* `put /thingies/{thingyId}` updates a specific `thingy` using a full representation
* `patch /thingies/{thingyId}` updates a specific `thingy` using a partial representation
* `put /gizmos/{gizmoId}` updates a specific `gizmo` using a full representation
* `patch /gizmos/{gizmoId}` updates a specific `gizmo` using a partial representation
* `delete /thingies/{thingyId}` deletes a specific `thingy`
* `delete /gizmos/{gizmoId}` deletes a specific `gizmo`
* `delete /thingies/{thingyId}/gizmos/{gizmoId}` delete the implicit unique relation between a specific `thingy` and a specific `gizmo`
* `delete /gizmos/{gizmoId}/thingies/{thingyId}` delete the implicit unique relation between a specific `gizmo` and a specific `thingy`

### One Explicit Single Relation

Now, let's consider that we still have a single relation, but that this relation can change over time, can have some attributes which can change values over time. We will therefore represent it explicitly with the **singular** noun `relation`.

Therefore, the two API endpoints:

* `post /thingies/{thingyId}/gizmos` creates a `gizmo` that has an implicit unique relation with a specific `thingy`
* `post /gizmos/{gizmoId}/thingies` creates a `thingy` that has an implicit unique relation with a specific `gizmo`

becomes a bit ambiguous since they are creating, for the first one, **both** a `gizmo` and its single relation with a specific `thingy` with some (implicit) default values, and for the second one, **both** a `thingy` and its single relation with a specific `gizmo` with some (implicit) default values.

It is therefore preferable to only use one of the following two API endpoints to explicitly create a unique relation between an existing `thingy` and an existing `gizmo`:

* `post /thingies/{thingyId}/gizmos/{gizmoId}/relation` creates a unique `relation` between a specific `thingy` and a specific `gizmo`
* `post /gizmos/{gizmoId}/thingies/{thingyId}/relation` creates a unique `relation` between a specific `gizmo` and a specific `thingy`

And, the **_reading side_** must now be completed with the two following API endpoints:

* `get /thingies/{thingyId}/gizmos/{gizmoId}/relation` gets the details of the single `relation` between a specific `thingy` and a specific `gizmo`
* `get /gizmos/{gizmoId}/thingies/{thingyId}/relation` get the details of the single `relation` between a specific `gizmo` and a specific `thingy`

While the **_update side_** must be completed accordingly:

* `put /thingies/{thingyId}/gizmos/{gizmoId}/relation` updates the single `relation` between a specific `thingy` and a specific `gizmo`, using a full representation
* `put /gizmos/{gizmoId}/thingies/{thingyId}/relation` updates the single `relation` between a specific `gizmo` and a specific `thingy`, using a full representation
* `patch /thingies/{thingyId}/gizmos/{gizmoId}/relation` updates the single `relation` between a specific `thingy` and a specific `gizmo`, using a partial representation
* `patch /gizmos/{gizmoId}/thingies/{thingyId}/relation` updates the single `relation` between a specific `gizmo` and a specific `thingy`, using a partial representation

Finally, the two API endpoints:

* `delete /thingies/{thingyId}/gizmos/{gizmoId}` delete the implicit single relation between a specific `thingy` and a specific `gizmo`
* `delete /gizmos/{gizmoId}/thingies/{thingyId}` delete the implicit single relation between a specific `gizmo` and a specific `thingy`

while they could still be used, are better replaced by:

* `delete /thingies/{thingyId}/gizmos/{gizmoId}/relation` delete the single `relation` between a specific `thingy` and a specific `gizmo`
* `delete /gizmos/{gizmoId}/thingies/{thingyId}/relation` delete the single `relation` between a specific `gizmo` and a specific `thingy`

#### Summary of One Explicit Single Relation

* `post /thingies` creates a `thingy`
* `post /gizmos` creates a `gizmo`
* `post /thingies/{thingyId}/gizmos/{gizmoId}/relation` creates a unique `relation` between a specific `thingy` and a specific `gizmo`
* `post /gizmos/{gizmoId}/thingies/{thingyId}/relation` creates a unique `relation` between a specific `gizmo` and a specific `thingy`
* `get /thingies` lists all `thingies`
* `get /thingies/{thingyId}` gets the details of a specific `thingy`
* `get /thingies/{thingyId}/gizmos` lists the `gizmos` that have an implicit single relation with a specific `thingy`
* `get /gizmos` lists all `gizmos`
* `get /gizmos/{gizmoId}` gets the details of a specific `gizmo`
* `get /gizmos/{gizmoId}/thingies` lists the `thingies` that have an implicit single relation with a specific `gizmo`
* `get /thingies/{thingyId}/gizmos/{gizmoId}/relation` gets the details of the single `relation` between a specific `thingy` and a specific `gizmo`
* `get /gizmos/{gizmoId}/thingies/{thingyId}/relation` get the details of the single `relation` between a specific `gizmo` and a specific `thingy`
* `put /thingies/{thingyId}` updates a specific `thingy` using a full representation
* `patch /thingies/{thingyId}` updates a specific `thingy` using a partial representation
* `put /gizmos/{gizmoId}` updates a specific `gizmo` using a full representation
* `patch /gizmos/{gizmoId}` updates a specific `gizmo` using a partial representation
* `put /thingies/{thingyId}/gizmos/{gizmoId}/relation` updates the single `relation` between a specific `thingy` and a specific `gizmo`, using a full representation
* `put /gizmos/{gizmoId}/thingies/{thingyId}/relation` updates the single `relation` between a specific `gizmo` and a specific `thingy`, using a full representation
* `patch /thingies/{thingyId}/gizmos/{gizmoId}/relation` updates the single `relation` between a specific `thingy` and a specific `gizmo`, using a partial representation
* `patch /gizmos/{gizmoId}/thingies/{thingyId}/relation` updates the single `relation` between a specific `gizmo` and a specific `thingy`, using a partial representation
* `delete /thingies/{thingyId}` deletes a specific `thingy`
* `delete /gizmos/{gizmoId}` deletes a specific `gizmo`
* `delete /thingies/{thingyId}/gizmos/{gizmoId}/relation` delete the single `relation` between a specific `thingy` and a specific `gizmo`
* `delete /gizmos/{gizmoId}/thingies/{thingyId}/relation` delete the single `relation` between a specific `gizmo` and a specific `thingy`

### Multiple Relations

* `post /thingies/{thingyId}/gizmos/{gizmoId}/relations` creates a `relation` between a specific `thingy` and a specific `gizmo`
* `post /gizmos/{gizmoId}/thingies/{thingyId}/relations` creates a `relation` between a specific `gizmo` and a specific `thingy`

* `get /thingies/{thingyId}/gizmos/{gizmoId}/relations` lists the `relations` between a specific `thingy` and a specific `gizmo`
* `get /gizmos/{gizmoId}/thingies/{thingyId}/relations` lists the `relations` between a specific `gizmo` and a specific `thingy`
* `get /relations/{relationId}` gets the details of a specific `relation`
* `get /relations` lists all `relations`

* `put /relations/{relationId}` updates a specific `relation` using a full representation
* `patch /relations/{relationId}` ~ updates a specific `relation` using a partial representation

Finally, different types of relation might have quite different representations: if you want to represent a contractual relation with a bank, you will use different fields. This is why I would recommend to use a specific noun representing the relation, such as `contract`:


* `post /thingies/{thingyId}/gizmos/{gizmoId}/contracts`


* `put /contracts/{contractId}` updates a specific `contract` between a `thingy` and a `gizmo` using a complete/full representation
* `patch /contracts/{contractId}` updates a specific `contract` between a `thingy` and a `gizmo` using a partial representation

* `get /contracts` lists all `contracts`
* `get /contracts/{contractId}` gets the details of a specific `contract`

But, we could also add the following API endpoints:

* `get /thingies/{thingyId}/contracts` lists the `contracts` that include a specific `thingy`
* `get /gizmos/{gizmoId}/contracts` lists the `contracts` that include a specific `gizmo`
* `get /thingies/{thingyId}/gizmos/{gizmoId}/contracts` lists the `contracts` that include a specific `thingy` and a specific `gizmo`
* `get /gizmos/{gizmoId}/thingies/{thingyId}/contracts` lists the `contracts` that include a specific `gizmo` and a specific `thingy`

As well as the following API endpoints:

* `get /contracts/{contractId}/thingies` lists the `thingies` that are included in a specific `contract`
* `get /contracts/{contractId}/gizmos` lists the `gizmos` that are included in a specific `contract`

Finally, the *delete side* is quite straightforward:

* `delete /thingies/{thingyId}` deletes a specific `thingy`
* `delete /gizmos/{gizmoId}` deletes a specific `gizmo`
* `delete /contracts/{contractId}` deletes a specific `contract`

Of course, things are not that simple, because you will have to decide what you do with the **orphan** `contracts` when you delete a `thingy` or a `gizmo`:
* It can be forbidden to delete a `thingy` or a `gizmo` before deleting all related `contracts`
* Your implementation can *cascade* the delete to all `contracts`
* You may just be happy with orphan `contracts`

### Summary of multiple named relations

* `post /thingies` creates a `thingy`
* `post /gizmos` creates a `gizmo`
* `post /thingies/{thingyId}/gizmos/{gizmoId}/contracts` creates a `contract` between a specific `thingy` and a specific `gizmo`
* `post /gizmos/{gizmoId}/thingies/{thingyId}/contracts` creates a `contract` between a specific `gizmo` and a specific `thingy`
* `get /thingies` lists all `thingies`
* `get /thingies/{thingyId}` gets the details of a specific `thingy`
* `get /gizmos` lists all `gizmos`
* `get /gizmos/{gizmoId}` gets the details of a specific `gizmo`
* `get /contracts` lists all `contracts`
* `get /contracts/{contractId}` gets the details of a specific `contract`
* `get /thingies/{thingyId}/contracts` lists the `contracts` that include a specific `thingy`
* `get /gizmos/{gizmoId}/contracts` lists the `contracts` that include a specific `gizmo`
* `get /contracts/{contractId}/thingies` lists the `thingies` that are included in a specific `contract`
* `get /contracts/{contractId}/gizmos` lists the `gizmos` that are included in a specific `contract`
* `get /thingies/{thingyId}/gizmos/{gizmoId}/contracts` lists the `contracts` that include a specific `thingy` and a specific `gizmo`
* `get /gizmos/{gizmoId}/thingies/{thingyId}/contracts` lists the `contracts` that include a specific `gizmo` and a specific `thingy`
* `put /thingies/{thingyId}` updates a specific `thingy` using a full representation
* `patch /thingies/{thingyId}` updates a specific `thingy` using a partial representation
* `put /gizmos/{gizmoId}` updates a specific `gizmo` using a full representation
* `patch /gizmos/{gizmoId}` updates a specific `gizmo` using a partial representation
* `put /contracts/{contractId}` updates a specific `contract` between a `thingy` and a `gizmo` using a complete/full representation
* `patch /contracts/{contractId}` updates a specific `contract` between a `thingy` and a `gizmo` using a partial representation
* `delete /thingies/{thingyId}` deletes a specific `thingy`
* `delete /gizmos/{gizmoId}` deletes a specific `gizmo`
* `delete /contracts/{contractId}` deletes a specific `contract`