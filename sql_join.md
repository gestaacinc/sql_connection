Here's the SQL script converted to markdown:

```markdown
# Day 7: Presentation SQL Queries

Run these queries against the `day7_data.db` database. Highlight a specific query and run it to see the results.

---

## 1. INNER JOIN (Slide 6 & 7)

Shows only customers who have placed an order.

```sql
SELECT 
    c.first_name, 
    o.total_amount 
FROM customers c 
INNER JOIN orders o 
    ON c.customer_id = o.customer_id;
```

---

## 2. LEFT JOIN (Slide 8)

Shows all customers, including Daniel, Angela, and Roberto, who will have `NULL` for their order amounts because they haven't bought anything.

```sql
SELECT 
    c.first_name, 
    o.total_amount 
FROM customers c 
LEFT JOIN orders o 
    ON c.customer_id = o.customer_id;
```

---

## 3. Three-Table JOIN (Slide 10)

Connects Customers, Orders, and Products to show **WHO** bought **WHAT** for **HOW MUCH**.

```sql
SELECT 
    c.first_name, 
    p.product_name, 
    o.quantity, 
    o.total_amount 
FROM orders o 
INNER JOIN customers c 
    ON o.customer_id = c.customer_id 
INNER JOIN products p 
    ON o.product_id = p.product_id;
```

---

## 4. Adding Filters to JOINs (Slide 11)

Takes the 3-table join and adds `WHERE` and `ORDER BY` clauses to filter for Electronics and sort by highest amount.

```sql
SELECT 
    c.first_name, 
    p.product_name, 
    o.quantity, 
    o.total_amount 
FROM orders o 
INNER JOIN customers c 
    ON o.customer_id = c.customer_id 
INNER JOIN products p 
    ON o.product_id = p.product_id
WHERE p.category = 'Electronics' 
ORDER BY o.total_amount DESC;
```

---

## 5. The 4-Table JOIN Challenge Solution (Slide 17 & 18)

Finds all NCR Electronics orders with customer name, product name, amount, and shipping status. Uses `LEFT JOIN` and `COALESCE` to handle missing shipping info.

```sql
SELECT 
    c.first_name, 
    p.product_name, 
    o.total_amount, 
    COALESCE(s.delivery_status, 'Not Shipped Yet') AS shipping_status
FROM orders o
INNER JOIN customers c 
    ON o.customer_id = c.customer_id
INNER JOIN products p 
    ON o.product_id = p.product_id
LEFT JOIN shipping s 
    ON o.order_id = s.order_id
WHERE c.region = 'NCR' 
  AND p.category = 'Electronics'
ORDER BY o.total_amount DESC;
```
```
