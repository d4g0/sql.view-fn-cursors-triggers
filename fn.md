# Fn syntax
```sql
create [or replace] function function_name(param_list)
   returns return_type 
   language plpgsql
  as
$$
declare 
-- variable declaration
begin
 -- logic
end;
$$
```



## Examples

### is Room Available
```sql
create or replace function is_room_available_in( 
	room_id_filter integer,
    delta_search_days integer,
	query_range tsrange,
	out is_available boolean
) as $$

DECLARE
temp_lock RECORD;
current_utc_date date = current_date at time zone 'UTC';
latest_date_to_search date = current_utc_date - ( delta_search_days + 1);

BEGIN
is_available = true;

for temp_lock in 
	select 
            * 
        from 
            room_lock_period rlp 
        where rlp.room_id = room_id_filter and rlp.start_date > latest_date_to_search
loop
	if query_range && temp_lock.during then
		is_available = false;
		EXIT;
	end if;
end loop;

END;
$$
LANGUAGE plpgsql;

```


### get room data
```sql

create or replace function get_room_data(
	room_id_filter integer
) returns room_data as 
$$

DECLARE
temp_room_data room_data%rowtype;

BEGIN

select * into temp_room_data from (
	select
	rm.id,
	rm.hotel_id,
	rm.room_name,
	rm.night_price,
	rm.capacity,
	rm.number_of_beds,
	rm.created_at,
	rm.room_type as room_type_id,
-- room type
	( 
		select rt.room_type from room_types rt
		where rt.id = rm.room_type
	) as room_type_key,
-- 	room pictures
	ARRAY(
		select 
			rp.id || '/' || rp.filename
		from room_pictures rp
		where rp.room_id = rm.id
	) as room_pictures,
-- 	room amenities
	ARRAY(
		select 
		 ra.id || '/' || ra.amenity
		from room_amenity ra join rooms_amenities rams
		on ra.id = rams.amenity_id
		where rams.room_id = rm.id
	) as room_amenities
	from room rm 
	where rm.id = room_id_filter
) room_data;

return temp_room_data;

END;
$$
LANGUAGE plpgsql;

```

### Get Rooms Available in
```sql
create or replace function get_rooms_available_in(
	hotel_id_filter integer,
	delta_search_days integer,
	query_range tsrange
) returns setof room_data as
$$

DECLARE
-- temp record
temp_room_data room_data%rowtype;
temp_room room%rowtype;


BEGIN


for temp_room in 
		select * from room 
		where room.hotel_id = hotel_id_filter
		order by room.capacity
LOOP
IF ( select is_available
		from is_room_available_in(
			temp_room.id,
			delta_search_days,
			query_range
		)
	) = true  then
		RETURN QUERY select * from get_room_data(temp_room.id);
END IF;
	
END LOOP;
RETURN;

END;
$$
LANGUAGE plpgsql;
```