## f1
```sql
CREATE
OR REPLACE FUNCTION ejemplo3_cursor() RETURNS SETOF persona AS $ $ DECLARE cur_p CURSOR FOR (
    SELECT
        *
    FROM
        persona
    ORDER BY
        idpersona
);

pers persona;

contador INTEGER := 0;

BEGIN OPEN cur_p;

MOVE ABSOLUTE 10
FROM
    cur_p;

LOOP MOVE
FROM
    cur_p;

EXIT
WHEN NOT FOUND
OR contador = 3;

contador := contador + 1;

DELETE FROM
    persona
WHERE
    CURRENT OF cur_p;

/* El empleo de CURRENT OF enlaza la fila actual del cursor con la fila
 de la tabla*/
END LOOP;

CLOSE cur_p;

OPEN cur_p;

LOOP FETCH cur_p INTO pers;

EXIT
WHEN NOT FOUND;

RETURN NEXT pers;

END LOOP;

CLOSE cur_p;

END;

$ $ LANGUAGE 'plpgsql';

--
```


## f2
```sql
create function c_1()
	returns void
	language plpgsql
as $$
declare per persona;
cur_per cursor 
begin
	
	
end $$;
```