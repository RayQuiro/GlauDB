# glaudb-rqio

GlauDB SDK for [RayQuiro](https://rayquiro.raytolfas.com) scripting language.

> Requires `glaudb` binary to be in PATH (or same folder as your script).
> Start the server first: `glaudb serve`

## Setup

```rq
import "glaudb";

async fn main() {
    glaudb.connect("127.0.0.1:8081", "your_password_or_token");

    var user = glaudb.insert("users", "{\"name\":\"Alex\"}");
    print("ID: " + user?.["id"]);
}

main();
```

## Collections

```rq
glaudb.create_collection("users", "[{\"name\":\"email\",\"type\":\"text\",\"required\":true,\"unique\":true}]");
glaudb.delete_collection("users");
```

## Insert / Get / Update / Delete

```rq
var user = glaudb.insert("users", "{\"email\":\"a@b.com\",\"name\":\"Alex\"}");
var found = glaudb.get("users", user.id, false);
var updated = glaudb.update("users", user.id, "{\"name\":\"Max\"}");
glaudb.patch("users", user.id, "{\"status\":\"active\"}");
glaudb.delete("users", user.id);
```

## Query

```rq
var all = glaudb.all("users");
var filtered = glaudb.query("users", "[{\"field\":\"status\",\"op\":\"eq\",\"value\":\"active\"}]", "[]", 50, false);
var page2 = glaudb.paginate("users", "[]", "[]", 2, 20);
```

### Query Operators

| op | Meaning |
|----|---------|
| `eq` | Equal |
| `neq` | Not equal |
| `gt` / `gte` | Greater than / or equal |
| `lt` / `lte` | Less than / or equal |
| `contains` | String contains / array contains |
| `in` | Value is in array |
| `nin` | Value is NOT in array |
| `startswith` | String starts with |
| `endswith` | String ends with |
| `exists` | Field exists (true/false) |

## Find One / Exists

```rq
var user = glaudb.find_one("users", "[{\"field\":\"email\",\"op\":\"eq\",\"value\":\"a@b.com\"}]", null);
var user = glaudb.find_by("users", "email", "\"a@b.com\"");
var ok = glaudb.exists("users", "[{\"field\":\"role\",\"op\":\"eq\",\"value\":\"admin\"}]");
```

## Upsert

Insert or update based on a field match:

```rq
var result = glaudb.upsert("users", "email", "{\"email\":\"a@b.com\",\"name\":\"Alex\"}");
```

Returns `{ record: {...}, inserted: true/false }`.

## Bulk Operations

```rq
var docs = glaudb.bulk_insert("logs", "[{\"msg\":\"hello\"},{\"msg\":\"world\"}]");
var count = glaudb.bulk_delete("logs", "[\"id1\",\"id2\"]");
```

## Full-text Search

```rq
var results = glaudb.search("articles", "rust async", "[\"title\",\"body\"]", 10);
```

## Aggregation

```rq
var agg = glaudb.aggregate("orders", "{\"group_by\":\"status\",\"operations\":[{\"op\":\"count\",\"alias\":\"total\"}]}");
```

## Count

```rq
var n = glaudb.count("users", "{}");
var active = glaudb.count("users", "{\"filters\":[{\"field\":\"status\",\"op\":\"eq\",\"value\":\"active\"}]}");
```

## Shortcuts

```rq
glaudb.query_in("users", "role", "[\"admin\",\"mod\"]", 100);
glaudb.query_starts_with("users", "email", "alex", 10);
glaudb.query_where("orders", "total", "gt", "100", 50);
```

## Backup

```rq
glaudb.backup("backup.db");
```
