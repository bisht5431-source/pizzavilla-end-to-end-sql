# pizzavilla-end-to-end-sql
Hands-on SQL project analyzing pizza sales, revenue and orders using advanced PostgreSQL queries on a real-world restaurant database.
# 🍕 PizzaVilla — SQL Business Intelligence Case Study

![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-FF6B35?style=for-the-badge&logo=database&logoColor=white)
![GitHub](https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)
![Queries](https://img.shields.io/badge/Queries-13-orange?style=for-the-badge)

> 🍕 *End-to-end PostgreSQL case study solving 13 business queries on a pizza restaurant database using JOINs, Subqueries and Window Functions.*

---

## 👨‍💻 About Me

Hi! I am **Manish Bisht** — an aspiring **Data Analyst / MIS Executive** who believes that
the best way to learn SQL is not just reading theory but **building real projects from scratch.**

This project — **PizzaVilla** — is my hands-on PostgreSQL practice project where I:
- 🗄️ Designed a **complete restaurant database** from scratch
- 🔗 Built **relationships between 4 tables**
- 📊 Solved **13 real business queries** step by step
- 🧠 Applied **advanced SQL concepts** like Window Functions and Subqueries

---

## 🍕 What is PizzaVilla?

**PizzaVilla** is a fictional pizza restaurant chain.
This project simulates a **real business database** that tracks:

```
🛒 Customer Orders       — What was ordered and when
🍕 Pizza Menu Items      — Types, sizes and prices
📦 Order Details         — Quantity and breakdown per order
💰 Revenue & Sales       — How much the business earned
```

> **Think of it like being the Data Analyst of a real pizza company
> — your job is to answer the business questions the owner is asking.**

---

## 🗄️ Database Schema

### 📋 Tables Used

| Table Name | Description | Key Column |
|---|---|---|
| `orders` | Stores each order placed | `order_id`, `order_date`, `order_time` |
| `order_details` | Items inside each order | `order_details_id`, `order_id`, `pizza_id`, `quantity` |
| `pizzas` | Pizza sizes and prices | `pizza_id`, `pizza_type_id`, `size`, `price` |
| `pizza_types` | Pizza names and categories | `pizza_type_id`, `name`, `category` |

---

### 🔗 Table Relationships (ERD)

```
pizza_types
    │
    │ pizza_type_id (ONE TO MANY)
    ▼
  pizzas
    │
    │ pizza_id (ONE TO MANY)
    ▼
order_details ◄──── orders
                    order_id (ONE TO MANY)
```

> 📌 **Star Schema Style** — order_details is the central FACT table
> connected to all DIMENSION tables

---

## ❓ Business Questions Solved

### 🟢 Basic Level — Foundation Queries

---

#### ✅ Q1 — Total Number of Orders Placed
> *"How many total orders has PizzaVilla received?"*

```sql
SELECT 
    COUNT(order_id) AS Total_Orders
FROM orders;
```
💡 **Insight:** Gives the total business volume —
a fundamental KPI for any restaurant operation.

---

#### ✅ Q2 — Total Revenue Generated From Pizza Sales
> *"How much total money has PizzaVilla earned?"*

```sql
SELECT 
    SUM(d.quantity * p.price) AS Total_Sales
FROM order_details d
JOIN pizzas p ON d.pizza_id = p.pizza_id;
```
💡 **Insight:** Calculates total revenue by multiplying
quantity × price across all orders. Core financial metric.

---

#### ✅ Q3 — Highest Priced Pizza on the Menu
> *"Which is the most expensive pizza we sell?"*

```sql
SELECT 
    pt.name,
    p.price
FROM pizza_types pt
JOIN pizzas p ON p.pizza_type_id = pt.pizza_type_id
ORDER BY p.price DESC
LIMIT 1;
```
💡 **Insight:** Identifies the premium product.
Useful for menu pricing strategy and upselling.

---

#### ✅ Q4 — Most Common Pizza Size Ordered
> *"Which pizza size do customers prefer the most?"*

```sql
SELECT 
    p.size,
    COUNT(d.order_details_id) AS Total_Orders
FROM pizzas p
JOIN order_details d ON p.pizza_id = d.pizza_id
GROUP BY p.size
ORDER BY Total_Orders DESC
LIMIT 1;
```
💡 **Insight:** Reveals customer size preference (S/M/L/XL).
Helps with inventory and dough preparation planning.

---

#### ✅ Q5 — Top 5 Most Ordered Pizza Types
> *"Which 5 pizzas are our customers ordering the most?"*

```sql
SELECT 
    pt.name,
    SUM(d.quantity) AS Total_Pizzas_Ordered
FROM pizza_types pt
JOIN pizzas p ON p.pizza_type_id = pt.pizza_type_id
JOIN order_details d ON p.pizza_id = d.pizza_id
GROUP BY pt.name
ORDER BY Total_Pizzas_Ordered DESC
LIMIT 5;
```
💡 **Insight:** Top 5 best sellers.
Perfect for promotions, combo deals and marketing campaigns.

---

### 🟡 Intermediate Level — Business Insights

---

#### ✅ Q6 — Total Quantity by Pizza Category
> *"Which pizza category sells the most overall?"*

```sql
SELECT 
    pt.category,
    SUM(d.quantity) AS Total_Quantity
FROM pizza_types pt
JOIN pizzas p ON p.pizza_type_id = pt.pizza_type_id
JOIN order_details d ON d.pizza_id = p.pizza_id
GROUP BY pt.category
ORDER BY Total_Quantity DESC;
```
💡 **Insight:** Reveals which category
(Classic / Veggie / Supreme / Chicken) dominates in volume.

---

#### ✅ Q7 — Order Distribution by Hour of the Day
> *"At what time of day do most customers place orders?"*

```sql
SELECT 
    EXTRACT(HOUR FROM order_time) AS Hour_of_Day,
    COUNT(order_id) AS Order_Count
FROM orders
GROUP BY Hour_of_Day
ORDER BY Order_Count DESC;
```
💡 **Insight:** Identifies peak hours like lunch (12PM–2PM)
and dinner rush (6PM–8PM). Critical for staff scheduling.

---

#### ✅ Q8 — Category-Wise Pizza Distribution
> *"How many pizza varieties exist in each category?"*

```sql
SELECT 
    category,
    COUNT(name) AS Total_Pizzas
FROM pizza_types
GROUP BY category
ORDER BY Total_Pizzas DESC;
```
💡 **Insight:** Shows menu diversity per category.
Useful for menu balancing and gap analysis.

---

#### ✅ Q9 — Average Pizzas Ordered Per Day
> *"On average how many pizzas does PizzaVilla sell daily?"*

```sql
SELECT 
    ROUND(AVG(Total_Quantity), 0) AS Avg_Pizzas_Per_Day
FROM (
    SELECT 
        o.order_date,
        SUM(d.quantity) AS Total_Quantity
    FROM orders o
    JOIN order_details d ON o.order_id = d.order_id
    GROUP BY o.order_date
) AS Daily_Orders;
```
💡 **Insight:** Uses a **Subquery** to first calculate daily totals
then averages them. Helps plan daily ingredient procurement.

---

#### ✅ Q10 — Top 3 Pizzas by Revenue
> *"Which 3 pizzas generate the most money for the business?"*

```sql
SELECT 
    pt.name,
    SUM(d.quantity * p.price) AS Revenue
FROM pizza_types pt
JOIN pizzas p ON p.pizza_type_id = pt.pizza_type_id
JOIN order_details d ON d.pizza_id = p.pizza_id
GROUP BY pt.name
ORDER BY Revenue DESC
LIMIT 3;
```
💡 **Insight:** Revenue leaders are not always the most ordered
but the most profitable. Key for business focus decisions.

---

### 🔴 Advanced Level — Expert SQL Techniques

---

#### ✅ Q11 — Percentage Revenue Contribution by Category
> *"What percentage of total revenue does each category contribute?"*

```sql
SELECT
    pt.category,
    ROUND(
        SUM(d.quantity * p.price) /
        (SELECT SUM(d2.quantity * p2.price)
         FROM order_details d2
         JOIN pizzas p2 ON p2.pizza_id = d2.pizza_id
        ) * 100, 2
    ) AS Revenue_Percentage
FROM pizza_types pt
JOIN pizzas p ON p.pizza_type_id = pt.pizza_type_id
JOIN order_details d ON d.pizza_id = p.pizza_id
GROUP BY pt.category
ORDER BY Revenue_Percentage DESC;
```
💡 **Insight:** Uses a **Subquery inside SELECT** to calculate
the total revenue as denominator. Shows exact % share of each
category — essential for menu investment decisions.

---

#### ✅ Q12 — Cumulative Revenue Generated Over Time
> *"How has PizzaVilla's revenue grown day by day?"*

```sql
SELECT 
    order_date,
    SUM(Total_Revenue) OVER (ORDER BY order_date) AS Cumulative_Revenue
FROM (
    SELECT 
        o.order_date,
        SUM(d.quantity * p.price) AS Total_Revenue
    FROM order_details d
    JOIN pizzas p ON d.pizza_id = p.pizza_id
    JOIN orders o ON o.order_id = d.order_id
    GROUP BY o.order_date
) AS Daily_Sales;
```
💡 **Insight:** Uses **Window Function — SUM() OVER()**
to create a running total showing how revenue grows over time.
Powerful for trend analysis and business growth tracking.

---

#### ✅ Q13 — Top 3 Pizzas by Revenue For Each Category
> *"Who are the top 3 revenue earners within EACH pizza category?"*

```sql
SELECT name, Revenue, Category
FROM (
    SELECT
        pt.category,
        pt.name,
        SUM(d.quantity * p.price) AS Revenue,
        RANK() OVER (
            PARTITION BY pt.category
            ORDER BY SUM(d.quantity * p.price) DESC
        ) AS Rnk
    FROM pizza_types pt
    JOIN pizzas p ON p.pizza_type_id = pt.pizza_type_id
    JOIN order_details d ON d.pizza_id = p.pizza_id
    GROUP BY pt.category, pt.name
) AS Ranked
WHERE Rnk <= 3;
```
💡 **Insight:** Uses **RANK() OVER (PARTITION BY)** —
the most powerful Window Function that ranks pizzas
separately within each category. Real analyst-level query.

---

## 🧠 SQL Concepts Used in This Project

| Concept | Used In |
|---|---|
| `SELECT` `WHERE` `ORDER BY` | Q1, Q2, Q3, Q4 |
| `COUNT()` `SUM()` `AVG()` `ROUND()` | Q1, Q2, Q4, Q9 |
| `INNER JOIN` (2 Tables) | Q2, Q3, Q4, Q8 |
| `INNER JOIN` (3 Tables) | Q5, Q6, Q10, Q11 |
| `GROUP BY` | Q4, Q5, Q6, Q7, Q8 |
| `LIMIT` | Q3, Q4, Q5, Q10 |
| `EXTRACT(HOUR FROM time)` | Q7 |
| `Subquery in FROM` | Q9, Q12, Q13 |
| `Subquery in SELECT` | Q11 |
| `SUM() OVER()` Window Function | Q12 |
| `RANK() OVER (PARTITION BY)` | Q13 |

---

## 💼 Key Business Insights Delivered

```
📌 Identified peak order hours   → Better staff scheduling
📌 Found top revenue pizzas      → Focus menu marketing
📌 Tracked cumulative revenue    → Monitor business growth
📌 Ranked best sellers by category → Smarter menu decisions
📌 Calculated category revenue % → Investment prioritization
📌 Found most popular size       → Optimize inventory planning
```

---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|---|---|
| **PostgreSQL** | Database & Query Execution |
| **pgAdmin 4** | GUI for PostgreSQL |
| **SQL** | Query Language |
| **GitHub** | Project Hosting & Version Control |

---

## 📁 Project Structure

```
📁 pizzavilla-sql-case-study/
│
├── 📄 README.md               ← You are here
├── 📄 create_tables.sql       ← Table creation scripts
├── 📄 insert_data.sql         ← Sample data insertion
├── 📄 basic_queries.sql       ← Q1 to Q5
├── 📄 intermediate_queries.sql← Q6 to Q10
└── 📄 advanced_queries.sql    ← Q11 to Q13
```

---

## 🚀 How to Run This Project

```bash
# Step 1 — Clone this repository
git clone https://github.com/yourusername/pizzavilla-sql-case-study.git

# Step 2 — Open pgAdmin 4 or any PostgreSQL client

# Step 3 — Create the database
CREATE DATABASE pizzavilla_db;

# Step 4 — Run table creation script
-- Run create_tables.sql

# Step 5 — Insert sample data
-- Run insert_data.sql

# Step 6 — Run queries one by one
-- Run basic_queries.sql
-- Run intermediate_queries.sql
-- Run advanced_queries.sql
```

---

## 🏁 What I Learned From This Project

```
✅ How to design a relational database from scratch
✅ Importance of Primary Key & Foreign Key relationships
✅ How JOIN connects real business data across tables
✅ Writing GROUP BY for sales reports and summaries
✅ Using Subqueries to answer complex business questions
✅ Applying Window Functions for running totals & rankings
✅ How real companies use SQL to track performance daily
✅ Thinking like a Data Analyst — not just writing queries
```

---

## 🗣️ Project Summary — In My Own Words

> *"PizzaVilla is my end-to-end PostgreSQL case study project
> where I built a complete pizza restaurant database from scratch.
> I designed 4 relational tables, connected them using Primary
> and Foreign Keys, and solved 13 real business questions —
> starting from basic SELECT queries all the way to advanced
> Window Functions like RANK() OVER PARTITION BY and cumulative
> SUM() OVER(). This project gave me the confidence to work
> on real-world data problems and think like a true Data Analyst."*

---

## 📬 Connect With Me

[![LinkedIn](www.linkedin.com/in/dataanalyst-manish)
[![GitHub](https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white)](https://github.com/yourusername)

---

<p align="center">
⭐ If you found this project helpful — please give it a Star!
</p>

<p align="center">
🍕 Made with ❤️ and lots of SQL queries by [Your Name]
</p>
