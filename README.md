# The Packages SQL Program

This SQL program is designed to answer six specific questions regarding three distinct scenarios: The Lost Letter, The Devious Delivery, and The Forgotten Gift. Each section in the program corresponds to one of these scenarios and includes SQL queries to retrieve relevant data from the database.

## Purpose
The purpose of this program is to analyze a database containing information about addresses, packages, scans, and drivers in order to provide answers to the following questions:

1. At what type of address did the Lost Letter end up?
2. At what address did the Lost Letter end up?
3. At what type of address did the Devious Delivery end up?
4. What were the contents of the Devious Delivery?
5. What are the contents of the Forgotten Gift?
6. Who has the Forgotten Gift?
kk
## SQL Queries for Each Scenario

### The Lost Letter
The Lost Letter scenario aims to identify the address where a particular package, referred to as the Lost Letter, ended up.

```sql
SELECT * FROM addresses
WHERE address LIKE "2 Fin%";

SELECT * FROM packages
WHERE from_address_id = (
    SELECT id FROM addresses
    WHERE address = "900 Somerville Avenue"
) AND to_address_id = (
    SELECT id FROM addresses
    WHERE address LIKE "2 Fin%"
);

SELECT * FROM scans
WHERE package_id = (
    SELECT id FROM packages
    WHERE from_address_id = (
        SELECT id FROM addresses
        WHERE address = "900 Somerville Avenue"
    ) AND to_address_id = (
        SELECT id FROM addresses
        WHERE address LIKE "2 Fin%"
    )
);
```

### The Devious Delivery
The Devious Delivery scenario aims to identify the type of address where a suspicious delivery ended up and retrieve information about its contents.

```sql
SELECT * FROM packages
WHERE from_address_id IS NULL;

SELECT * FROM scans
WHERE package_id = (
    SELECT id FROM packages
    WHERE from_address_id IS NULL
);

SELECT * FROM addresses
WHERE id = (
    SELECT address_id FROM scans
    WHERE action LIKE "drop" AND package_id = (
        SELECT id FROM packages
        WHERE from_address_id IS NULL
    )
);
```

### The Forgotten Gift
The Forgotten Gift scenario aims to retrieve information about the contents of a specific package and identify the recipient.

```sql
SELECT * FROM addresses WHERE address = "109 Tileston Street";

SELECT * FROM packages
WHERE from_address_id = (
    SELECT id FROM addresses
    WHERE address = "109 Tileston Street"
);

SELECT * FROM scans
WHERE package_id = (
    SELECT id FROM packages
    WHERE from_address_id = (
        SELECT id FROM addresses
        WHERE address = "109 Tileston Street"
    )
)
ORDER BY timestamp DESC LIMIT 1;

SELECT name FROM drivers
WHERE id = (
    SELECT driver_id FROM scans
    WHERE package_id = (
        SELECT id FROM packages
        WHERE from_address_id = (
            SELECT id FROM addresses
            WHERE address = "109 Tileston Street"
        )
    )
    ORDER BY timestamp DESC LIMIT 1
);
```

## How to Use
To execute these queries, access your SQL database and run each query in the provided order under their respective scenario headings. Adjust the database names or table structures as necessary to match your database setup.

Remember to replace specific addresses or values with your actual data to obtain accurate results.

## Note
This SQL program assumes a specific database schema and might require adjustments to function correctly with your database.


