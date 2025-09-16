---
title: postgres hacks
description: Useful tips and queries working with postgres
---

## Decoding hex byte array to string #postgres #sql

Asuming there is a table which contains `id` and a `content` column, where the content is stored as byte array BUT in a text column.
| id :number: | content :text: |
|----------|----------|
| 1  | \x48616c6c6f2057656c74  |
| 2  | \x48616c6c6f206c696562652057656c74  |


```sql
select
	id,
	content,
	convert_from(content::bytea, 'UTF8') AS decoded_content
from table_name
where key = 1
```

> Result:
> | id | content | decoded_content |
> |----------|----------|----------|
> | 1  | \x48616c6c6f2057656c74  | Hallo Welt |


## Adding different schemas to search_path in #postgres

View current search path entries
```sql
SHOW search_path
```

Set search path
```sql
ALTER DATABASE <my-databse> SET search_path TO <my-schema-1>,<my-schema-2>,<my-schema-n>,temp,public
```
or
```sql
SET search_path TO <my-schema-1>,<my-schema-2>,<my-schema-n>,temp,public
```