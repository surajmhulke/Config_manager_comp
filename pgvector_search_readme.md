Here is a **simple end-to-end project example** using `pgvector` to **search two vectors** in **PostgreSQL 17** on a Tiger Analytics environment. It includes installation, database creation, table setup, vector insertion, and a similarity search using `cosine_distance`.

---

### ✅ 1. **Install PostgreSQL 17 with pgvector**

Use this updated version of commands to install PostgreSQL 17:

```bash
sudo apt update
sudo apt install -y wget gnupg
wget -qO - https://apt.postgresql.org/pub/repos/apt/$(lsb_release -cs)-pgdg.asc | sudo tee /etc/apt/trusted.gpg.d/postgresql.asc
echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" | sudo tee /etc/apt/sources.list.d/pgdg.list
sudo apt update
sudo apt install -y postgresql-17
```

---

### ✅ 2. **Install pgvector extension**

After installing PostgreSQL 17, log into `psql` and enable `pgvector`:

```bash
sudo -u postgres psql
```

```sql
CREATE EXTENSION IF NOT EXISTS vector;
```

---

### ✅ 3. **Create a Sample Table**

You can do this either in `psql` or using Python via SQL execution.

```sql
CREATE TABLE vector_test (
    id SERIAL PRIMARY KEY,
    name TEXT,
    embedding VECTOR(3)
);
```

---

### ✅ 4. **Insert Sample Vectors**

```sql
INSERT INTO vector_test (name, embedding)
VALUES 
    ('Apple', '[1.0, 0.0, 0.0]'),
    ('Orange', '[0.0, 1.0, 0.0]'),
    ('Banana', '[0.0, 0.0, 1.0]');
```

---

### ✅ 5. **Query for Similar Vectors Using Cosine Distance**

Let’s say you have a query vector `[1.0, 0.1, 0.0]`. To get the most similar item:

```sql
SELECT name, embedding, embedding <#> '[1.0, 0.1, 0.0]' AS cosine_distance
FROM vector_test
ORDER BY cosine_distance ASC
LIMIT 1;
```

The `<#>` operator is for **cosine distance** in `pgvector`.

---

### ✅ 6. **Python Version Using psycopg2**

If using Python (e.g., `create_db.ipynb`), the code would look like:

```python
import psycopg2
from psycopg2.extras import register_vector
import numpy as np

conn = psycopg2.connect("dbname=yourdb user=youruser password=yourpass host=localhost")
cur = conn.cursor()

# Enable pgvector
cur.execute("CREATE EXTENSION IF NOT EXISTS vector;")

# Create table
cur.execute("""
    CREATE TABLE IF NOT EXISTS vector_test (
        id SERIAL PRIMARY KEY,
        name TEXT,
        embedding VECTOR(3)
    );
""")

# Insert data
cur.execute("INSERT INTO vector_test (name, embedding) VALUES (%s, %s)", ("Apple", [1.0, 0.0, 0.0]))
conn.commit()

# Search
query_vector = [1.0, 0.1, 0.0]
cur.execute("""
    SELECT name, embedding, embedding <#> %s AS cosine_distance
    FROM vector_test
    ORDER BY cosine_distance ASC
    LIMIT 1
""", (query_vector,))
result = cur.fetchone()
print(result)

cur.close()
conn.close()
```

---

### 📁 Setup Notes

* Copy `create_db.ipynb` into your `notebook/` folder and run it.
* Make sure to replace `tablename` and `vector(256)` with your actual vector size (e.g., 3 for demo).
* Your `eval(emb)` from your original snippet should be avoided if using PostgreSQL; use SQL directly for similarity search.

---

Would you like a `.ipynb` version of this sample project?
PostgreSQL Installation 

Install and verify the PostgreSQL database setup 

https://docs.google.com/document/d/1db9Fw1f0bnGl0VlY9grdPFnYZvovx54BGQOv7xpTIxs/edit?tab=t.0#heading=h.6952ym2yetex 

Open this doc in tiger env. 

Change is the version 17 should be used in above file due to pgvector issue 
sudo apt update 
 sudo apt install -y wget gnupg 
 wget -qO - https://apt.postgresql.org/pub/repos/apt/$(lsb_release -cs)-pgdg.asc | sudo tee /etc/apt/trusted.gpg.d/postgresql.asc 
 echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" | sudo tee /etc/apt/sources.list.d/pgdg.list 
 sudo apt update 
 sudo apt install -y postgresql-17 

PFA create_db.ipynb from Setup Files Ipro to notebook folder and run it. 

vector_ext_query = """CREATE EXTENSION IF NOT EXISTS vector;"""
db_factory.execute_query(
            connection=db_conn, query=vector_ext_query, return_value=False, content=None
        )
table= """CREATE TABLE tablename
embeddings vector(256),
df["similarity"] = df["embeddings"].apply(
                lambda emb: cosine_similarity(
                    np.array(query_embedding).reshape(1, -1),
                    np.array(eval(emb)).reshape(1, -1),
                )[0][0]
            )
