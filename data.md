Here's the content formatted in Markdown:

---

# SQL Data Cleaning: The Mentor's Guide (Find, Fix, Verify)

Welcome back! As a data analyst, the **Find, Fix, Verify** method is your best friend. We never just blindly change data. We find the exact problem, we carefully apply a fix, and then we check our work to prove the problem is gone.

We will use the `payroll` table from your `Day06_demo.db` file. Let's tackle the most common data problems using this three-step workflow!

---

## Problem 1: Inconsistent Casing (The 'MAKATI' vs 'Makati' Issue)

**The Reason:** SQL is literal. If it sees `"SALES"` and `"Sales"`, it thinks those are two completely different departments. If you try to sum up the payroll for the Sales department, you will get the wrong total because SQL will split them into two groups. We use `UPPER` or `LOWER` to force everything to match.

### 🕵️ FIND
Use `DISTINCT` to look at the unique values. This proves to us that the mess exists.

```sql
-- Why: To see all the messy, inconsistent casings in the department column.
SELECT DISTINCT department FROM payroll;
```

**Result:** You'll likely see `'Sales'`, `'SALES'`, `'hr'`, `'Finance'`, etc.

### 🛠️ FIX
Use `UPDATE` and `UPPER` to force every single department name into ALL CAPS.

```sql
-- Why: To standardize the column so every entry matches perfectly.
UPDATE payroll 
SET department = UPPER(department);
```

### ✅ VERIFY
Run your FIND query again. It should come back clean!

```sql
-- Why: To confirm that only clean, uppercase departments remain.
SELECT DISTINCT department FROM payroll;
```

---

## Problem 2: Sneaky Spaces (The ' IT' vs 'IT' Issue)

**The Reason:** Sometimes humans accidentally hit the spacebar before or after typing a word. To a human, `" IT"` and `"IT"` look the same. To SQL, that invisible space makes them completely different. We use `TRIM` to act like scissors and snip off those extra leading and trailing spaces.

### 🕵️ FIND
Because spaces are invisible, we can use a trick to find them. We put brackets around the word so the spaces become obvious!

```sql
-- Why: Adding brackets around the name reveals if there is a space hiding inside.
SELECT '[' || employee_name || ']' AS NameCheck 
FROM payroll;
```

**Result:** If someone has a space, it will look like `[ Joy Bautista]` instead of `[Joy Bautista]`.

### 🛠️ FIX
Use `UPDATE` with `TRIM` to snip those spaces away.

```sql
-- Why: To remove invisible spaces so names can be properly searched or alphabetized.
UPDATE payroll 
SET employee_name = TRIM(employee_name);
```

### ✅ VERIFY
Run your bracket check again!

```sql
-- Why: To prove the invisible spaces have been eradicated.
SELECT '[' || employee_name || ']' AS NameCheck 
FROM payroll;
```

---

## Problem 3: Standardizing Categories

**The Reason:** Sometimes `UPPER` and `LOWER` aren't enough. What if users typed `"Processed"`, `"processed_"`, or `"PROCESSED"`? They are a mess. We use a `CASE` statement (like an IF statement) to act as a traffic cop and manually map messy variations to one clean, standard word.

### 🕵️ FIND
Use `DISTINCT` to see all the weird variations of the status.

```sql
-- Why: To identify exactly which incorrect statuses we need to write rules for.
SELECT DISTINCT status FROM payroll;
```

### 🛠️ FIX
Use `CASE` to define the rules for fixing the data.

```sql
-- Why: To clean up multiple specific messy variations in a single sweep.
UPDATE payroll 
SET status = CASE
    WHEN LOWER(status) LIKE 'processed%' THEN 'Processed'
    WHEN LOWER(status) LIKE 'pending%' THEN 'Pending'
    WHEN LOWER(status) LIKE 'hold%' THEN 'Hold'
    ELSE status
END;
```

### ✅ VERIFY

```sql
-- Why: To ensure our CASE statement caught all the weird variations.
SELECT DISTINCT status FROM payroll;
```

---

## Problem 4: Dealing with Missing Data

**The Reason:** Empty data (`NULL`) can break charts or formulas. Sometimes we need to find who is missing data to follow up with them, or we need to replace the `NULL` with a placeholder like `"No Email"` so our reports look clean.

### 🕵️ FIND
Remember, you cannot use `= NULL`. You must use `IS NULL`.

```sql
-- Why: To isolate the exact rows that are missing critical information.
SELECT * FROM payroll 
WHERE email IS NULL;
```

### 🛠️ FIX (Temporary Fix for a Report)
Instead of permanently altering the data, we often use `COALESCE` or `IFNULL` to temporarily replace the blank just for the visual report we are sending to our boss.

```sql
-- Why: To ensure our final report doesn't have ugly blank holes in it.
SELECT 
    employee_name, 
    COALESCE(email, 'No Email Provided') as email_address
FROM payroll;
```

### ✅ VERIFY
Just look at the results of your query above! You will see `"No Email Provided"` instead of a blank space.

---

## Problem 5: Taking Out the Trash (Test Data)

**The Reason:** Sometimes developers create "Test" entries when building the system. If we leave them in, our total payroll numbers will be inflated, and our analysis will be wrong! We use `DELETE` to shred them.

### 🕵️ FIND
First, locate the junk data using a `SELECT` query. **Never skip this step!**

```sql
-- Why: To guarantee we are ONLY targeting the test row and not real employees.
SELECT * FROM payroll 
WHERE employee_name LIKE '%test%';
```

### 🛠️ FIX
Swap the `SELECT *` for `DELETE`.

```sql
-- Why: To permanently remove the bad data from the database.
DELETE FROM payroll 
WHERE employee_name = 'test entry';
```

### ✅ VERIFY
Run your FIND query again. It should come back empty!

```sql
-- Why: To confirm the shredder did its job and the row is gone.
SELECT * FROM payroll 
WHERE employee_name LIKE '%test%';
```

---

## Problem 6: Duplicate Records (The Clone Attack)

**The Reason:** Duplicate entries are dangerous. If an employee is accidentally entered into the payroll system twice, your total salary reports will be completely wrong! We use `GROUP BY` and `HAVING` to group matching records together and count if there is more than 1 of them.

### 🕵️ FIND
To answer your question about whether there is a duplicate in the database, we need to run this scanner!

```sql
-- Why: To group employees by name and count how many times they appear. Anything greater than 1 is a duplicate!
SELECT 
    employee_name, 
    COUNT(*) as NumberOfTimes
FROM payroll
GROUP BY employee_name
HAVING COUNT(*) > 1;
```

### 🛠️ FIX
If you found a duplicate, we need to delete the extra one. In SQLite, every row has a hidden, unique ID called `rowid`. We can tell SQL to keep the first entry (the lowest `rowid`) for each employee, and delete any others.

```sql
-- Why: This keeps the first entry (MIN rowid) and deletes any extra clones.
DELETE FROM payroll
WHERE rowid NOT IN (
    SELECT MIN(rowid)
    FROM payroll
    GROUP BY employee_name
);
```

### ✅ VERIFY
Run the FIND query one more time to make sure no one has a count greater than 1!

```sql
-- Why: To prove the clones have been removed.
SELECT 
    employee_name, 
    COUNT(*) as NumberOfTimes
FROM payroll
GROUP BY employee_name
HAVING COUNT(*) > 1;
```

---

## 🏆 Final Mentor Advice

By treating every single data problem as a **Find → Fix → Verify** cycle, you guarantee accuracy. You leave a trail of exactly what you did, and you can confidently tell your stakeholders that the data is 100% reliable for analysis!
