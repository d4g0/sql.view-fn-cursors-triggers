# Cursors


## Declaration
```sql
DECLARE
    -- unbound
    curs1 refcursor;
    -- bound
    curs2 CURSOR FOR SELECT * FROM tenk1;
    curs3 CURSOR (key integer) FOR SELECT * FROM tenk1 WHERE unique1 = key;
```


## Open

### Unbounds
#### For Query
```sql
OPEN unbound_cursorvar [ [ NO ] SCROLL ] FOR query;
```

#### For Execute
```sql
OPEN unbound_cursorvar [ [ NO ] SCROLL ] FOR EXECUTE query_string [ USING expression [, ... ] ]
```


### Bounds
```sql
OPEN bound_cursorvar [ ( [ argument_name := ] argument_value [, ...] ) ];
```

### Examples
```sql
OPEN curs2;
OPEN curs3(42);
OPEN curs3(key := 42);
```

## Fetch
```sql
FETCH [ direction { FROM | IN } ] cursor INTO target;
```

Where **direction** migth be any of:
-   NEXT
-   PRIOR
-   FIRST
-   LAST
-   ABSOLUTE count
-   RELATIVE count
-   FORWARD
-   BACKWARD

### Examples
```sql
FETCH curs1 INTO rowvar;
FETCH curs2 INTO foo, bar, baz;
FETCH LAST FROM curs3 INTO x, y;
FETCH RELATIVE -2 FROM curs4 INTO x;
```



## Move
```sql
MOVE [ direction { FROM | IN } ] cursor;
```

As with `SELECT INTO`, the special variable `FOUND` can be checked to see whether there was a next row to move to.

### Examples
```sql
MOVE curs1;
MOVE LAST FROM curs3;
MOVE RELATIVE -2 FROM curs4;
MOVE FORWARD 2 FROM curs4;
```

## UPDATE/DELETE WHERE CURRENT OF
```sql
UPDATE table SET ... WHERE CURRENT OF cursor;
DELETE FROM table WHERE CURRENT OF cursor;
```

### Examples
```sql
UPDATE foo SET dataval = myval WHERE CURRENT OF curs1;
```



## CLOSE

```sql
CLOSE cursor;
```

### Examples
```sql
CLOSE curs1;
```