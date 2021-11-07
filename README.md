# DBT Snowflake Style Guide  


##  Jinja style guide

- When using Jinja delimiters, use spaces on the inside of your delimiter, like ` {{ this }} ` instead of ` {{this}} `

- Use newlines to visually indicate logical blocks of Jinja

##  Naming and field conventions

- Schema, table and column names should be in **UPPERCASE**.

- Schema, table and column names should be in **SNAKE_CASE**.

- Use declared datatypes in source tables as early as possible, after this we don’t need to declare datatypes in following models/tables that reference the earlier model. Use this method to declare datatypes: **FIELD_NAME::DATATYPE()**

- Use the correct datatype for each field i.e. BOOLEAN datatype for true false, DATES, NUMBERS (note: make sure to use correct decimal places i.e. 2 for values 6 for currency rates).

- Each model should have a primary key.

- The primary key of a model should be named **<OBJECT>_ID**, e.g. ACCOUNT_ID– this makes it easier to know what IDis being referenced in downstream joined models.

- For base/staging models, fields should be ordered in categories, where identifiers are first and timestamps are at the end.

- For final models we can use a similar ordering however PowerBI does alphabetical.

- Timestamp columns should be named **<EVENT>_AT**, e.g. PICKUPDATE_AT, and should be the localtime of that datapoint if there is no specific timezone. If a specific timezone is being used, this should be indicated with a suffix, e.g PICKUPDATE_AT_NZT for example when the database default timezone for fields like cr_booking.added is GMT we should specify by using BOOKING_ADDED_AT_UTC

- Locations: 

Branches: fields should begin with **BRANCH_**

- Financial values should be named **<VALUE>_<VALUETYPE>_<CURRENCY 3-digit>** for example TTV_GROSS_NZD

TTV_INSURANCE_NZD

INSURANCE_COMMISSION_NZD

UNDERWRITER_COST_NZD

- Booleans should be prefixed with **IS_** or **HAS_**. and BOOLEAN should be 1 or 0. 1 means true, 0 means false.

- Price/revenue fields should be in decimal currency (e.g. 19.99 for $19.99; many app databases store prices as integers in cents). If non-decimal currency is used, indicate this with suffix, e.g. PRICE_IN_CENTS.

- Avoid reserved words as column names i.e. FROM SELECT etc.

- Consistency is key! Use the same field names across models where possible, e.g. a key to the CUSTOMERStable should be named CUSTOMER_IDrather than USER_ID.

##  SQL style guide

- Use quotes "" around field names in final output, and even in subqueries (without this can cause issues in snowflake)

- Use similar patterns for all SQL statements using WITH ctes

`
WITH BOOKINGS AS (
    SELECT BOOKING_ID FROM CR_BOOKING
)
SELECT 
    BOOKING_ID AS "BOOKING_ID"
FROM BOOKINGS
`
    
- Use trailing commas:

`
SELECT
    T.BOOKING_ID AS "BOOKING_ID",
    T.CUSTOMER_ID AS "CUSTOMER_ID",
    T.VALUE AS "VALUE"
FROM TABLE AS T
`

- Schema, table and column names should be in **UPPERCASE** in queries.

- Indents should be four spaces (except for predicates, which should line up with the **WHERE** keyword)

- Lines of SQL should be no longer than 80 characters

- Field names and function names should all be UPPERCASE

- The **AS** keyword should be used when aliasing a field or table

- Fields should be stated before aggregates / window functions

- Aggregations should be executed as early as possible before joining to another table.

- Ordering and grouping by a number (eg. group by 1, 2) is preferred over listing the column names. Note that if you are grouping by more than a few columns, it may be worth revisiting your model design.

- Specify join keys - do not use USING. Certain warehouses have inconsistencies in USING results (specifically Snowflake).

- Prefer **UNION ALL** to UNION *

- Avoid table aliases in join conditions (especially initialisms) – it's harder to understand what the table called "c" is compared to "customers".

- If joining two or more tables, always prefix your column names with the table alias. If only selecting from one table, prefixes are not needed.

- Be explicit about your join (i.e. write **INNER JOIN** instead of **JOIN** ). **LEFT JOIN**(s) are normally the most useful, **RIGHT JOIN**(s) often indicate that you should change which table you select FROM and which one you JOIN to.

- DO NOT OPTIMIZE FOR A SMALLER NUMBER OF LINES OF CODE. NEWLINES ARE CHEAP, BRAIN TIME IS EXPENSIVE
