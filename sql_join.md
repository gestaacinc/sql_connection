Here's the SQL queries reformatted as a clean Markdown document with proper headings, code blocks, and business scenarios organized clearly:

---

# Day 7: Presentation SQL Queries

> **Database:** `day7_data.db`  
> **Instructions:** Highlight a specific query and run it to see the results.

---

## 1. INNER JOIN *(Slide 6 & 7)*

Shows only customers who have placed an order.

**🎯 Business Scenario:**  
Marketing wants to send a "Thank You" email to all active customers who have successfully made at least one purchase.

```sql
SELECT 
    c.first_name, 
    o.total_amount 
FROM customers c 
INNER JOIN orders o 
    ON c.customer_id = o.customer_id;
```

---

## 2. LEFT JOIN *(Slide 8)*

Shows all customers, including Daniel, Angela, and Roberto, who will have NULL for their order amounts because they haven't bought anything.

**🎯 Business Scenario:**  
The Sales team needs a list of ALL registered customers to identify "cold leads" (those who signed up but have NULL orders) for a re-engagement campaign.

```sql
SELECT 
    c.first_name, 
    o.total_amount 
FROM customers c 
LEFT JOIN orders o 
    ON c.customer_id = o.customer_id;
```

---

## 3. Three-Table JOIN *(Slide 10)*

Connects Customers, Orders, and Products to show **WHO** bought **WHAT** for **HOW MUCH**.

**🎯 Business Scenarios:**

| Scenario | Team | Description |
|----------|------|-------------|
| **A** | Finance | Needs a detailed transaction log showing exactly which customer purchased which product to audit total revenue per transaction. |
| **B** | Account Management | Needs to know exactly which customers ordered which products. This 3-table join directly answers that question by linking customer names to their respective purchased items. |
| **C** | Marketing | Wants to identify cross-selling opportunities by matching customers with their purchased product categories, helping design targeted campaigns (e.g., sending keyboard promotions only to customers who bought Laptops). |

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

## 4. Adding Filters to JOINs *(Slide 11)*

Takes the 3-table join and adds `WHERE` and `ORDER BY` clauses to filter for Electronics and sort by highest amount.

**🎯 Business Scenario:**  
The Category Manager for "Electronics" wants a prioritized list of their top buyers to offer them VIP early access to upcoming tech releases.

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

## 5. The 4-Table JOIN Challenge Solution *(Slide 17 & 18)*

Finds all **NCR Electronics** orders with customer name, product name, amount, and shipping status. Uses `LEFT JOIN` and `COALESCE` to handle missing shipping info.

**🎯 Business Scenario:**  
Logistics and Customer Service need a consolidated dashboard for all high-value "Electronics" orders in the "NCR" region to proactively track delivery statuses and easily answer customer "Where is my order?" inquiries.

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

---

> **💡 Key Takeaways:**
> - `INNER JOIN` returns only matching rows from both tables
> - `LEFT JOIN` returns all rows from the left table, with `NULL` for non-matches
> - `COALESCE()` replaces `NULL` values with a default string
> - Business context transforms technical queries into actionable insights
