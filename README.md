
# Improving Search Speed in SQLite Using Indexing in Python

## Introduction
SQLite is a lightweight database used in many applications. However, when dealing with tables containing millions of records, searches can become extremely slow. In this repository, we will demonstrate how to improve search speed using **Indexing** in SQLite with **Python**.

## The Problem
When searching a table with over a million records without indexing, the search can take **1700 seconds (approximately 28 minutes)**. We will show how to reduce this time to **a few seconds** using indexes.

## Setting Up the Environment
### Install Dependencies:
```bash
pip install sqlite3 pandas time
```

## Python Code to Test Search Without Indexing
```python
import sqlite3
import time

# Create a connection to the database
conn = sqlite3.connect("test.db")
cursor = conn.cursor()

# Create a table without an index
cursor.execute("DROP TABLE IF EXISTS users")
cursor.execute("""
    CREATE TABLE users (
        id INTEGER PRIMARY KEY,
        name TEXT,
        age INTEGER,
        email TEXT
    )
""")

# Insert one million random records
import random, string
for _ in range(1_000_000):
    name = ''.join(random.choices(string.ascii_letters, k=10))
    age = random.randint(18, 80)
    email = name + "@example.com"
    cursor.execute("INSERT INTO users (name, age, email) VALUES (?, ?, ?)", (name, age, email))

conn.commit()

# Measure search time without an index
start_time = time.time()
cursor.execute("SELECT * FROM users WHERE email = 'randomuser@example.com'")
end_time = time.time()
print(f"Search time without indexing: {end_time - start_time:.2f} seconds")
```

## Improving Performance with Indexing
```python
# Create an index on the email column
cursor.execute("CREATE INDEX idx_email ON users(email)")
conn.commit()

# Measure search time after indexing
start_time = time.time()
cursor.execute("SELECT * FROM users WHERE email = 'randomuser@example.com'")
end_time = time.time()
print(f"Search time with indexing: {end_time - start_time:.2f} seconds")

# Close the connection
conn.close()
```

## Results
| Case | Search Time |
|--------|----------|
| Without Indexing | ~1700 seconds |
| With Indexing | A few seconds |

## Factors Affecting Search Speed
While indexing significantly improves search performance, other factors also play a role:
- **Storage Speed**: SSDs are much faster than HDDs, reducing disk read times.
- **Memory Availability**: More RAM allows SQLite to cache data and reduce disk access.
- **CPU Performance**: Faster processors can execute queries more efficiently.
- **Database Size**: Larger databases may still experience slow queries if not optimized properly.
- **Query Optimization**: Writing efficient SQL queries can further enhance performance.
- **Index Maintenance**: Too many indexes can slow down insert/update operations; only index frequently searched columns.

## Conclusion
This test demonstrates the significant performance improvement when using indexes in SQLite. When dealing with large tables, indexing is a crucial step to enhance search performance and reduce response time. Additionally, considering hardware and query optimization can further improve database efficiency.
