# Triggers

## Create Triggers

## Trigger Fn
### Example
```sql
CREATE OR REPLACE FUNCTION actualizar_nombre1 () RETURNS TRIGGER AS $$
BEGIN
    
    IF NEW.nombre != OLD. nombre THEN
        IF NEW.edad > 18 THEN
            RETURN NEW;
        ELSE
            RAISE NOTICE 'No se puede realizar el cambio';
            RETURN NULL;
        END IF;
    END IF;
    
    RETURN NEW;
END;
$$
LANGUAGE 'plpgsql';
```

## Setup
### Example
Each row
```sql
CREATE TRIGGER actualizar_nombre1
BEFORE UPDATE ON persona
FOR EACH ROW EXECUTE PROCEDURE actualizar_nombre1 ();
```

```sql
CREATE TRIGGER check_update
    BEFORE UPDATE ON accounts
    FOR EACH ROW
    EXECUTE FUNCTION check_account_update();
```

With condition
```sql
CREATE TRIGGER actualizar_nombre2
BEFORE UPDATE OF nombre ON persona
FOR EACH ROW WHEN (NEW.nombre != OLD.nombre) EXECUTE PROCEDURE
actualizar_nombre2 ();
```

Specific column
```sql
CREATE OR REPLACE TRIGGER check_update
    BEFORE UPDATE OF balance ON accounts
    FOR EACH ROW
    EXECUTE FUNCTION check_account_update();
```