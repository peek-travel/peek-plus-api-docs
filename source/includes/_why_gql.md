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

![image](https://user-images.githubusercontent.com/1004167/73576938-893fcc00-4430-11ea-9045-66020005fae8.png)

## Self Documenting
Piggy backing on the strongly type point from above, Graphql was written, from the ground up, to be easy to "introspect". There are a host of great free IDEs out there; we are big fans of [https://insomnia.rest/graphql/](https://insomnia.rest/graphql/) - simply by downloading that client and pointing to our sandbox, you have every query, field and mutation documented. We find crafting your queries there and then moving them into your app is a solid way to work.

![image](https://user-images.githubusercontent.com/1004167/73577149-010df680-4431-11ea-8cb9-ccfd998d6ca7.png)



## Further Reading

* [https://graphql.org/learn/queries/](https://graphql.org/learn/queries/)
