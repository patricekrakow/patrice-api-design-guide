# Patrice API Design Guide

## 1. Abstract

This document reflects a personal view of how the ideal web API design should look like.

## 2. What is an API?

_API_ is the acronym for Application Programming Interface.

"Application Programming Interface" is a compound made of three nouns: "application", "programming" and "interface". The head noun is "interface". Therefore, an "API" is an "interface", which is modified/specialized by the two other nouns: "application" and "programming". So, we suggest that the compound "API" should be understood as follow:

_**Definition:**_ An _API (endpoint)_ is an interface that allows applications to programmatically access to a certain capability/functionality.

> You can notice that we understand the (modifier) noun "application" as the entity which access to the capability/functionality, and **not** as the entity which implements the capability/functionality.
We think that it is a common mistake to understand that the (modifier) noun "application" within the compound API as the entity providing the capability/functionality.
We prefer to see the compound API as solely an "interface", the head noun, and the modifier noun "application" solely representing the entity consuming, and not providing, the capability/functionality.
We like to refer to the [Amazon Mechanical Turk](https://www.mturk.com/) service as a good illustration of this point of view. Indeed, the Amazon Mechanical Turk service exposes an API with which computer applications can access to human beings, clearly positioning the "application" on the consuming side, while human beings are on the providing side.

Following the [OpenAPI Specification (AOS)](https://github.com/OAI/OpenAPI-Specification), the actual interface called by an application is not the API, but the API endpoint that we will define as follow:

_**Definition:**_ An _API endpoint_ is an interface that allows applications to programmatically access to a certain capability/functionality, and uniquely identified with the 3-tuples:
* HTTP method
* host
* URL path template
