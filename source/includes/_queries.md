# Queries

As mentioned earlier, the source of truth for what's available can be browsed from directly inside your GQL Client. Duplicating and maintaining the spec would defeat the advantages of GQL. With that in mind, this section will be very high level to help shape your understanding of what's available, **not** the source of truth.

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

This query is split into two individual queries;

1. Get high-level cachable dates to get an idea of, roughly speaking, when a given activity is available.
2. Given a specific set of tickets (1x Adult, 2x Child) for an activity on a specific date, what times are available.

(coming soon)
