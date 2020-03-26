# Queries

As mentioned earlier, the source of truth for what's available can be browsed from directly inside your GraphQL Client. Duplicating and maintaining the spec would defeat the advantages of GraphQL. With that in mind, this section will be very high level to help shape your understanding of what's available, **not** the source of truth.

## Activities

The `Activities` query is the most powerful query in the API in that it can be used for two major use-cases:

1. Provide activities based on filters/sorting (location / price / keywords / etc). This could power real-time experiences where you need very specific activities for a given use-case.
2. You want to "ingest" our entire inventory through crawling every page of the results, pulling every field that is of importance to you.

```graphql
query Activities($filter: ActivitiesFilter!, $pagination: Pagination!, $sort: ActivitySort) {
  activities(filter: $filter, pagination: $pagination, sort: $sort) {
    entries {
      name
    }
  }
}
```

For the purposes of this example, let's say you just need each Activity's `name`. Your query for both use-cases would be identical.

In the two examples above, the main difference is what you pass in for variables. For a very specific query you'd pass in filters and sort values based on your desires. For crawling the whole inventory, you'd also likely not include any filters and you'd ask for `totalPages` so you can crawl through all of them, etc.

>  Example Response:

```javascript
{
  "activities": [{
    "entries": [
      { "name": "Activity 1" },
      { "name": "Activity 2" },
    ]
  }]
}
```

## Availability

Determining whether something is available can be thought of as a two part process.

1. Get high-level cachable dates + times to get an idea of, roughly speaking, when a given activity is available.
2. Given a specific set of tickets (1x Adult, 2x Child) for a specific time, is it available and what will the price be.

The first is what we're going to talk about here. The second is in the next section as we call that a BookingQuote Mutation.

With GraphQL, pulling availability is simply a "field" you can request for an activity:

```graphql
query ActivityById($id: ID!, $startDate: Date!, $endDate: Date!, $quantity: Int) {
  activity(id: $id) {
    name
    dates(startDate:$startDate, endDate: $endDate, quantity: $quantity) {
      date
      times {
        id
        localDateTimeStart
        localDateTimeEnd
        status
      }
    }
  }
}
```

Notice with GraphQL, "fields" can accept arguments (in this case the desired availability).

The power of the above is that you can do the exact same thing with a search result and in a single query to the server, answer the question "What are the top 10 things to do close by and what dates/times are available for them".

<aside class="warning">
With great power... asking for a very large number of activities, each with a very large availability window will be slower and create a massive payload.

We recommend running large availability requests one activity at a time and use the feed only for very short windows; perhaps only returning the next week of days w/ no times, for example.
</aside>

