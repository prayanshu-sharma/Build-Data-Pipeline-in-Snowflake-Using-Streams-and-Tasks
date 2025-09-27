# Build-Data-Pipeline-in-Snowflake-Using-Streams-and-Tasks
Real-Time ETL Pipeline inside Snowflake

//build data pipeline in snowflake using streams and tasks

--Create database 
create or replace database orders;

--source table
create or replace table raw_order
(
order_id int,
customer string,
amount number,
created_at timestamp
);

-- target table
create or replace table processed_order
(
order_id int,
customer string,
amount number,
created_at timestamp,
processed_at timestamp
);

-- create stream

create or replace stream order_stream on table raw_order;

show streams;

--create task

create or replace task process_new_orders
   warehouse =compute_wh,
   schedule='1 minute'
when SYSTEM$STREAM_HAS_DATA('order_stream')
AS
insert INTO processed_order
select 
order_id int,
customer string,
amount number,
created_at timestamp,
current_timestamp as processed_at 
from order_stream;

show tasks;

--activate task
ALTER TASK process_new_orders RESUME;

--insert data into raw_order table
INSERT INTO raw_order(order_id,customer,amount,created_at) VALUES
(1,'Alice',150,CURRENT_TIMESTAMP),
(2,'Prayanshu',99,CURRENT_TIMESTAMP);

--check if there is data in stream
select SYSTEM$STREAM_HAS_DATA('order_stream') as has_data;

--show data in stream
select * from order_stream;


--trigger task manually 
execute task process_new_orders;

INSERT INTO raw_order(order_id,customer,amount,created_at) VALUES
(3,'Bob',180,CURRENT_TIMESTAMP),
(4,'Pooja',199,CURRENT_TIMESTAMP);

--Target Table 

select * from processed_order;

<img width="1900" height="723" alt="image" src="https://github.com/user-attachments/assets/8905599e-941b-4c8f-bd03-e570acd577ba" />




