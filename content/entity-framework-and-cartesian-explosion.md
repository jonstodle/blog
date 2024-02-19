+++
title = "Entity Framework and Cartesian Explosion"
date = 2024-02-19
[taxonomies]
tags = ["code"]
+++

Entity Framework, EF, is incredibly popular in the .NET community, for better or worse. There are many opinions on why you could use it and many why you shoulnd't use it. I'm not going to go into any reasoning around that now, though. What I want to highlight is something called _cartesian explosion_.

When making queries with EF, you can include related data through _navigation properties_ and _collections_. Both of these concepts rely on SQL foregin keys, but abstract the relationship in a more C#, object-oriented way. If you were fetching data about a TV episode for instance, you could include the director of the episode in the result like this:

```c#
var episode = db.Episodes
    .Include(e => e.Director)
    .Single(e => e.Id == 1)
```

No problem there. EF generates a `JOIN` to join the `epiosdes` and the `directors` tables and fetches the relevant data from both.

You could also include the actors in the episode in the result by doing this:

```c#
var episode = db.Episodes
    .Include(e => e.Director)
    .Include(e => e.Actors)
    .Single(e => e.Id == 1)
```

<details>
<summary>Expand to see the generated SQL query</summary>

```sql
SELECT "e"."Id", "e"."AirDate", "e"."DirectorId", "e"."Title", "d"."Id", "d"."Name", "a"."Id", "a"."Character", "a"."EpisodeId", "a"."Name"
FROM "Episodes" AS "e"
INNER JOIN "Directors" AS "d" ON "e"."DirectorId" = "d"."Id"
LEFT JOIN "Actors" AS "a" ON "e"."Id" = "a"."EpisodeId"
ORDER BY "e"."Id", "d"."Id"
```
</details>

`Actors` is a collection of actors. How does EF fetch multiple actors in a query for a single episode? It creates a query that returns one row for each actor with the episode and director data included in each row. It then merges all the duplicates into a single object locally on the client. This isn't too much of a problem as long as the data stored in `episodes` and `directors` is too big, but the real problem occurs when including multiple collections:

```c#
var episodes = db.Episodes
    .Include(e => e.Director)
    .Include(e => e.Actors)
    .Include(e => e.Crew)
    .Single(e => e.Id == 1);
```

<details>
<summary>Expand to see the generated SQL query</summary>

```sql
SELECT "e"."Id", "e"."AirDate", "e"."DirectorId", "e"."Title", "d"."Id", "d"."Name", "a"."Id", "a"."Character", "a"."EpisodeId", "a"."Name", "c"."Id", "c"."EpisodeId", "c"."Name", "c"."Role"
FROM "Episodes" AS "e"
INNER JOIN "Directors" AS "d" ON "e"."DirectorId" = "d"."Id"
LEFT JOIN "Actors" AS "a" ON "e"."Id" = "a"."EpisodeId"
LEFT JOIN "Crew" AS "c" ON "e"."Id" = "c"."EpisodeId"
ORDER BY "e"."Id", "d"."Id", "a"."Id"
```
</details>

EF will now `JOIN` both `actors` _and_ `crew` into into the result set. This means that it includes data from `episodes`, and `directors` for each row from `actors`, and then it includes all of those rows for each row from `crew`!

**If `actors` returns 10 rows, and `crew` returns 10 rows, the query will return 100 rows!**.

Again, this might not be too much of a problem if the amounts of data that's actually returned is small, but it's something to keep in mind every time you include multiple collections in a query.  There are multiple ways to avoid this, including just making multiple queries instead of one big one. EF even have a [built-in method](https://learn.microsoft.com/en-us/ef/core/querying/single-split-queries#split-queries) for doing just that.

I recommend reading up on [cartesian explosion and Entity Framework](https://learn.microsoft.com/en-us/ef/core/querying/single-split-queries#cartesian-explosion) in the Entity Framework documentation.

Happy coding!
