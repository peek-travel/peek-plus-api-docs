---
title: Peek Plus API

language_tabs:
* graphql

toc_footers:
* <a href='https://www.peek.com/developers'>Sign Up for a Developer Key</a>

includes:
* errors

search: true
---

# Introduction

Welcome to the PeekPlus GraphQL API!

You can use our API to access thousands upon thousands of things to do, curated by us.

With the PeekPlus API you can

* Search For Activities
* Check Real Time Availability
* Create Bookings

```
💥💥  One Endpoint  💥💥
https://api.peek.com/gql
```

# Why GQL?

We chose Graphql over some older standards because it is the **best tool** for us to expose our data to you in an *elegant*, *self-documenting*, *flexible* and *performant* way.

If you are new to Graphql, we are confident you will never
think about APIs the same way again.

If you are already familiar with Graphql, we can feel your excitement; we're right there with you.

## Smaller, Customizable Payloads

In a traditional "here is the endpoint to return activities" scenario, the original creators would include everything they felt "reasonable" - maybe something like:

* name
* description
* image

Then someone comes along and makes a really compelling case for exposing the location. So, for them, we add a few new fields for lat and lng.

Then someone comes along later and asks for review meta data because they want to expose it. Reasonable. So again we go and add a few new fields for the count and average rating.

Then someone comes along and says they want a field that is actually pretty heavy. They are aware that it is heavier but are comfortable only asking for a few activities. So we add it.

Now the original users who just want the name and description are telling us the api is too slow; uh oh they were querying for a ton of activities and that heavy field is now a problem. So we hack together some query params where you can white-list fields:

`http://example.com/activities?fields=name,description...`


Great, but when you ask for activities, we actually want to also be able to get the tickets.. so now you are in this world of

`http://example.com/activities?fields=name,description,tickets.name...`

And everything starts to feel really dirty.

With GraphQL, we simply expose our object graph:

* Activity
  * Name
  * Description
  * Tickets
* Ticket
  * Name

And you, the owner of your own experience, ask for what you need and we return it to you. Simple as that. Furthermore, if you find your self wanting 4 or 5 different things, you can ask for them all at once saving many round trips to the server.

Wins. All. Around.

## Strongly Typed

With a traditional "REST API", you find yourself stringing together complex query params to filter content:

`http://example.com/activities?sort=price&filter[state]=NY...`

Inevitably the question comes in; "what are possible values for SORT", or "what should I use for the STATE?". In those traditional APIs, you'd be digging for the email that linked to the docs or more likely guessing and checking. It is then up the api server to gracefully handle all possible mistakes to get you a reasonable error message if you deviate from what's expected. This is a lot of work for both parties, and is error prone.

With Graphql, every query is **Strongly Typed** - you get immediate feedback of what the values can be, and if you deviate from it, the request doesn't even make it to our application logic; you are immediately given a good error explaining your mistake.

## Self Documenting
Piggy backing on the strongly type point from above, Graphql was written, from the ground up, to be easy to "introspect". There are a host of great free IDEs out there; we are big fans of [https://insomnia.rest/graphql/](https://insomnia.rest/graphql/) - simply by downloading that client and pointing to our sandbox, you have every query, field and mutation documented. We find crafting your queries there and then moving them into your app is a solid way to work.


## Further Reading

* [https://graphql.org/learn/queries/](https://graphql.org/learn/queries/)


# Authentication

## JWT

When you are granted access to the API, you will be given an Api Key ("your-api-key") and a Secret ("your-secret"). Every request you make will require you to create a JWT, that includes your API Key in the `sub` field and a unique request ID in the `reqid` field. Which is the signed w/ your Secret:

`{"sub": "your-api-key", "reqid": "unique-request-id"}`

If you're new to JWTs, [https://jwt.io/](https://jwt.io/) is a GREAT place to start:

![image](https://user-images.githubusercontent.com/1004167/73561655-38b77700-440e-11ea-8ea7-f00068dc1750.png)

```elixir
defmodule Token do
  use Joken.Config

  @doc """
  Given a unique request ID, return a signed token.
  """
  def for_request_id(request_id) do
    {:ok, claims} =
      Token.generate_claims(%{
        "sub" => "your-api-key",
        "reqid" => request_id
      })

    {:ok, token, _claims} = encode_and_sign(claims, signer())

    token
  end

  defp signer, do: Joken.Signer.create("HS256", "your-secret")
end

Token.for_request_id("unique-request-id")
```

The reason we chose JWT is because there exists a client for (nearly?) every language imaginable. No need to write your own logic, just find the library (all listed on the above site) for your language and it should be straightforward from there.

We are an Elixir Shop (don't even get me started), so our Library of choice is called `Joken`; on the right is how you might choose to implement the above w/ Joken.

## Add Token as Header

The Token you generated above has to be included in the header for every request:

`Authorization: Bearer TOKEN`

<aside class="info">Replace TOKEN with your own token</aside>
<aside class="warning">Please keep your API credentials secret. Do not put it in client-side code.</aside>


# Idempotency

Since every request that you make must contain a unique request id inside the signed token, if you make a second request w/ the same request id we will assume this you replaying a request and will return the original response.

<aside class="warning">
If you erroneously use the same request id for a request with a different request body, this will return an error.
</aside>

# Versioning

Another advantage of GQL is that the need for versioning largely does away; the schema evolves overtime w/out the need for strict versions.

### Backwards Compatible Changes

99% of GQL changes are backwards compatible; they involve deprecating a field which just results in a warning if used but doesn't stop working. Or it's a new field altogether and since you aren't asking for it, it doesn't affect you.

### Breaking Changes

We try to refrain from doing backwards incompatible changes, but it might happen that we need to remove, rename or restructure fields or parameters. We will only do this after notifying our users beforehand and keeping the impact as small as possible. We might introduce a new api version (/v3) to incorporate these changes.

# Queries

As mentioned earlier, the source of truth for what's available can be browsed from directly inside your GQL Client. Duplicating and maintaining the spec would defeat the advantages of GQL. With that in mind, this section will be very high level to help shape your understanding of what's available, **not** the source of truth.

## Activities

```graphql
query Activities($filter: ActivitiesFilter!, $pagination: Pagination!, $sort: ActivitySort) {
  activities(filter: $filter, pagination: $pagination, sort: $sort) {
    entries {
      name
    }
  }
}
```

>  Example Response:

```json

{
  "activities": [{
    "entries": [
      { "name": "Activity 1" },
      { "name": "Activity 2" },
    ]
  }]
}
```

The `Activities` query is the most powerful query in the API in that it can be used for two major use-cases:

1. Provide activities based on filters/sorting (location / price / keywords / etc). This could power real-time experiences where you need very specific activities for a given use-case.
2. You want to "ingest" our entire inventory through crawling every page of the results, pulling every field that is of importance to you.

For the purposes of this example, let's say you just need each Activity's `name`. Your query for both use-cases would be identical.

In the two examples above, the main difference is what you pass in for variables. For a very specific query you'd pass in filters and sort values based on your desires. For crawling the whole inventory, you'd also likely not include any filters and you'd ask for `totalPages` so you can crawl through all of them, etc.
