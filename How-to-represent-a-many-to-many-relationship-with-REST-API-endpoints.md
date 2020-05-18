## How to represent a many-to-many relationship with REST API endpoints

Let's say that we have `thingies` and `gizmos` that have a **many-to-many** relationship.
We can start representing the *reading side* of such a situation with the following API endpoints:

* `get /thingies` ~ Get the list of all `thingies`
* `get /thingies/{thingyId}` ~ Get detailed information about a specific `thingy`
* `get /thingies/{thingyId}/gizmos` ~ Get the list of `gizmos` *that are linked to* a specific `thingy`
* `get /gizmos` ~ Get the list of all `gizmos`
* `get /gizmos/{gizmoId}` ~ Get detailed information about a specific `gizmo`
* `get /gizmos/{gizmoId}/thingies` ~ Get the list of `thingies` *that are linked to* a specific `gizmo`

The text "that are linked to" is the most generic expression I could think about. In real cases, you SHOULD replace it with a domain specific expression such as "of", "for", "owned by", "that belongs to", "that are used by", ...

You may ask why I did not put the following API endpoints:

* `get /thingies/{thingyId}/gizmos/{gizmoId}`
* `get /gizmos/{gizmoId}/thingies/{thingyId}`

I do think we SHOULD avoid them. The `gizmoId` MUST (uniquely) identified a specific `gizmo` across all past, present and future `thingies`, so it not necessary (i.e. stupid) to specify a particular `thingy` to retrieve a specific `gizmo`. The symmetric argument is of course also valid: the `thingyId` MUST (uniquely) identified a specific `thingy` across all past, present and future `gizmos`, so it not necessary (i.e. stupid) to specify a particular `gizmo` to retrieve a specific `thingy`.

On the *creation side*, we could have

* `post /thingies` ~ Create a `thingy`
* `post /thingies/{thingyId}/gizmos` ~ Create a `gizmo` *that is linked to* a specific `thingy`

if a `gizmo` can also exist linked to **one** specific `thingy`. In this case, `thingies` and `gizmos` do not have a **many-to-many** relationship, but just a **one-to-many** relationship. We could also have a **many-to-one** relationship between `thingies` and `gizmos` that leads to the following API endpoints:

* `post /gizmos` ~ Create a `gizmo`
* `post /gizmos/{gizmoId}/thingies` ~ Create a `thingy` *that is linked to* a specific `gizmo`

If `thingies` and `gizmos` do have a genuine **many-to-many** relationship, we could use the following API endpoints:

* `post /thingies` ~ Create a `thingy`
* `post /gizmos` ~ Create a `gizmo`
* `post /thingies/{thingyId}/gizmos/{gizmoId}` ~ Create a relation (link) between a specific `thingy` and a specific `gizmo`
* `post /gizmos/{gizmoId}/thingies/{thingyId}` ~ Create a relation (link) between a specific `gzimo` and a specific `thingy`

Let's continue with the *update side*. We, of course, MUST have the following API endpoints:

* `put /thingies/{thingyId}` ~ Update a specific `thingy` using a complete/full representation
* `patch /thingies/{thingyId}` ~ Update a specific `thingy` using a partial representation
* `put /gizmos/{gizmoId}` ~ Update a specific `thingy` using a complete/full representation
* `patch /gizmos/{gizmoId}` ~ Update a specific `thingy` using a partial representation

Moreover, in case of a genuine many-to-many relationship, we SHOULD also have:

* `put /thingies/{thingyId}/gizmos/{gizmoId}` ~ Update the relation (link) between a specific `thingy` and a specific `gizmo` using a complete/full representation
* `patch /thingies/{thingyId}/gizmos/{gizmoId}` ~ Update the relation (link) between a specific `thingy` and a specific `gizmo` using a partial representation
* `put /gizmos/{gizmoId}/thingies/{thingyId}` ~ Update the relation (link) between a specific `gzimo` and a specific `thingy` using a complete/full representation
* `patch /gizmos/{gizmoId}/thingies/{thingyId}` ~ Update the relation (link) between a specific `gzimo` and a specific `thingy` using a partial representation

which MIGHT work well if you have one (and only one) possible relation between `thingies` and `gizmos`, but what if we want to manage different (type of) relations between `thingies` and `gizmos`.

Imagine that the two API endpoints creating a relation return a `relation` like the following:

* `post /thingies/{thingyId}/gizmos/{gizmoId}` returning `/relations/{relationId}`
* `post /gizmos/{gizmoId}/thingies/{thingyId}` returning `/relations/{relationId}`

we can then have

* `put /relations/{relationId}` ~ Update a relation (link) between a specific `thingy` and a specific `gizmo` using a complete/full representation
* `patch /relations/{relationId}` ~ Update a relation (link) between a specific `thingy` and a specific `gizmo` using a partial representation

But, it is not nice/common to have a *somehting*, a `relation` in this case, created via `post` on a URL containing some words, and updated via `put` and `patch` on a URL containing other words! So, what about having only one API enpoint to creation a `relation`:

* `post /relations` returing `/relations/{relationId}`

or

* `post /thingies/{thingyId}/gizmos/{gizmoId}/relations` returning `/relations/{relationId}`
* `post /gizmos/{gizmoId}/thingies/{thingyId}/relations` returning `/relations/{relationId}`

Finally, different types of relation might have quite different represenations: if you want to represent a contractual relation with a bank, you will use different fields. This is why I would recommend to use a specific noun representing the relation, such as `contract`:

* `post /contracts` ~ Create a `contract` between a `thingy` and a `gizmo`

or

* `post /thingies/{thingyId}/gizmos/{gizmoId}/contracts`

The *update side* of these two posibilities are both completed by:

* `put /contracts/{contractId}` ~ Update a specific contract between a `thingy` and a `gizmo` using a complete/full representation
* `patch /contracts/{contractId}` ~ Update a specific contract between a `thingy` and a `gizmo` using a partial representation

We should now add the *reading side* of this relation of a specific type, the `contract`. We have the obvious ones:

* `get /contracts` ~ Get the list of all `contracts`
* `get /contracts/{contractId}` ~ Get detailed information about a specific `contract`

But, we could also add the following API endpoints:

* `get /thingies/{thingyId}/contracts` ~ Get the list of `contracts` that include a specific `thingy`
* `get /gizmos/{gizmoId}/contracts` ~ Get the list of `contracts` that include a specific `gizmo`
* `get /thingies/{thingyId}/gizmos/{gizmoId}/contracts` ~ Get the list of `contracts` that include a specific `thingy` and a specific `gizmo`
* `get /gizmos/{gizmoId}/thingies/{thingyId}/contracts` ~ Get the list of `contracts` that include a specific `gizmo` and a specific `thingy`

As well as the following API endpoints:

* `get /contracts/{contractId}/thingies` ~ Get the list of `thingies` that are included in a specific `contract`
* `get /contracts/{contractId}/gizmos` ~ Get the list of `gizmos` that are included in a specific `contract`

Finally, the *delete side* is quite straightforward:

* `delete /thingies/{thingyId}` ~ Delete a specific `thingy`
* `delete /gizmos/{gizmoId}` ~ Delete a specific `gizmo`
* `delete /contracts/{contractId}` ~ Delete a specific `contract`

Of course, things are not that simple, because you will have to decide what you do with the **orphan** `contracts` when you delete a `thingy` or a `gizmo`:
* It can be forbidden to delete a `thingy` or a `gizmo` before deleting all related `contracts`
* Your implementation can *cascade* the delete to all `contracts`
* You may just be happy with orphan `contracts`

Let's now summarize all our findings, first when there is a **unique** implicit relation between the `thingies` and the `gizmos`, and then when we name explicitly the relation, as `contract` in our exmaple, in order to welcome other (types of) relation.

### One Implicit Unique Relation

* `post /thingies` ~ Create a `thingy`
* `post /gizmos` ~ Create a `gizmo`
* `post /thingies/{thingyId}/gizmos/{gizmoId}` ~ Create the relation (link) between a specific `thingy` and a specific `gizmo`
* `post /gizmos/{gizmoId}/thingies/{thingyId}` ~ Create the relation (link) between a specific `gzimo` and a specific `thingy`
* `get /thingies` ~ Get the list of all `thingies`
* `get /thingies/{thingyId}` ~ Get detailed information about a specific `thingy`
* `get /thingies/{thingyId}/gizmos` ~ Get the list of `gizmos` *that are linked to* a specific `thingy`
* `get /gizmos` ~ Get the list of all `gizmos`
* `get /gizmos/{gizmoId}` ~ Get detailed information about a specific `gizmo`
* `get /gizmos/{gizmoId}/thingies` ~ Get the list of `thingies` *that are linked to* a specific `gizmo`
* `get /thingies/{thingyId}/gizmos/{gizmoId}` ~ Get detailed information about the relation (link) between a specific `thingy` and a specific `gizmo`
* `get /gizmos/{gizmoId}/thingies/{thingyId}` ~ Get detailed information about the relation (link) between a specific `gizmo` and a specific `thingy`
* `put /thingies/{thingyId}` ~ Update a specific `thingy` using a complete/full representation
* `patch /thingies/{thingyId}` ~ Update a specific `thingy` using a partial representation
* `put /gizmos/{gizmoId}` ~ Update a specific `thingy` using a complete/full representation
* `patch /gizmos/{gizmoId}` ~ Update a specific `thingy` using a partial representation
* `put /thingies/{thingyId}/gizmos/{gizmoId}` ~ Update the relation (link) between a specific `thingy` and a specific `gizmo` using a complete/full representation
* `patch /thingies/{thingyId}/gizmos/{gizmoId}` ~ Update the relation (link) between a specific `thingy` and a specific `gizmo` using a partial representation
* `put /gizmos/{gizmoId}/thingies/{thingyId}` ~ Update the relation (link) between a specific `gzimo` and a specific `thingy` using a complete/full representation
* `patch /gizmos/{gizmoId}/thingies/{thingyId}` ~ Update the relation (link) between a specific `gzimo` and a specific `thingy` using a partial representation
* `delete /thingies/{thingyId}/gizmos/{gizmoId}` ~ Delete the relation (link) between a specific `thingy` and a specific `gizmo`
* `delete /gizmos/{gizmoId}/thingies/{thingyId}` ~ Delete the relation (link) between a specific `gzimo` and a specific `thingy`
* `delete /thingies/{thingyId}` ~ Delete a specific `thingy`
* `delete /gizmos/{gizmoId}` ~ Delete a specific `gizmo`

### One Explicit Relation Amongst Other Possible Relations

* `post /thingies` ~ Create a `thingy`
* `post /gizmos` ~ Create a `gizmo`
* `post /contracts` or `post /thingies/{thingyId}/gizmos/{gizmoId}/contracts` ~ Create a `contract` between a `thingy` and a `gizmo`
* `get /thingies` ~ Get the list of all `thingies`
* `get /thingies/{thingyId}` ~ Get detailed information about a specific `thingy`
* `get /thingies/{thingyId}/gizmos` ~ Get the list of `gizmos` *that are linked to* a specific `thingy`
* `get /gizmos` ~ Get the list of all `gizmos`
* `get /gizmos/{gizmoId}` ~ Get detailed information about a specific `gizmo`
* `get /gizmos/{gizmoId}/thingies` ~ Get the list of `thingies` *that are linked to* a specific `gizmo`
* `get /contracts` ~ Get the list of all `contracts`
* `get /contracts/{contractId}` ~ Get detailed information about a specific `contract`
* `get /thingies/{thingyId}/contracts` ~ Get the list of `contracts` that include a specific `thingy`
* `get /gizmos/{gizmoId}/contracts` ~ Get the list of `contracts` that include a specific `gizmo`
* `get /thingies/{thingyId}/gizmos/{gizmoId}/contracts` ~ Get the list of `contracts` that include a specific `thingy` and a specific `gizmo`
* `get /gizmos/{gizmoId}/thingies/{thingyId}/contracts` ~ Get the list of `contracts` that include a specific `gizmo` and a specific `thingy`
* `get /contracts/{contractId}/thingies` ~ Get the list of `thingies` that are included in a specific `contract`
* `get /contracts/{contractId}/gizmos` ~ Get the list of `gizmos` that are included in a specific `contract`
* `put /thingies/{thingyId}` ~ Update a specific `thingy` using a complete/full representation
* `patch /thingies/{thingyId}` ~ Update a specific `thingy` using a partial representation
* `put /gizmos/{gizmoId}` ~ Update a specific `thingy` using a complete/full representation
* `patch /gizmos/{gizmoId}` ~ Update a specific `thingy` using a partial representation
* `put /contracts/{contractId}` ~ Update a specific `contract` using a complete/full representation
* `patch /contracts/{contractId}` ~ Update a specific `contract` using a partial representation
* `delete /thingies/{thingyId}` ~ Delete a specific `thingy`
* `delete /gizmos/{gizmoId}` ~ Delete a specific `gizmo`
* `delete /contracts/{contractId}` ~ Delete a specific `contract`
