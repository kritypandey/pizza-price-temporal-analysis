# 🍕 Historical Pricing Revenue Analysis

## 📌 Project Overview

This project analyzes transaction data from a pizza restaurant to calculate **total revenue using historical product prices**.

Since product prices change over time, the correct price for each order must be determined based on the **price that was active at the time of the transaction**.

To solve this problem, we combine multiple datasets and perform a **time-aware join** to retrieve the appropriate price for each transaction.

The analysis demonstrates practical data analysis techniques such as:

- Working with multiple datasets
- Data cleaning and preparation
- Time-based joins
- Revenue calculations
- Data validation

---

# 📂 Datasets

The project uses **three datasets**.

## 1️⃣ Transactions Table

Contains customer order records.

| Column | Description |
|------|-------------|
| order_id | Unique order identifier |
| order_date | Date when the order was placed |
| pizza_id | Product identifier |
| quantity | Number of pizzas ordered |

Each row represents **one transaction**.

---

## 2️⃣ Products Table

Contains product-level information including the **current price**.

| Column | Description |
|------|-------------|
| pizza_id | Product identifier |
| pizza_name | Name of the pizza |
| current_price | Current product price |

Note:  
This table only stores the **latest price**, not historical pricing.

---

## 3️⃣ Price History Table

Tracks price changes over time.

| Column | Description |
|------|-------------|
| pizza_id | Product identifier |
| effective_date | Date when the new price became effective |
| price | Price of the product |

This table allows us to determine the **correct price for a product at a specific point in time**.

---

# ⚙️ Tools & Technologies

- Python
- Pandas
- NumPy
- Google Colab

---

# 🔍 Problem Statement

Given a transaction date and a product, determine the **correct historical price** from the price history table.

**Rule**

If the price change date is **equal to the order date**, that price should be used.

This requires retrieving the **most recent price where:**

effective_date ≤ order_date


---

# 🧹 Data Preparation

Before performing the analysis, the following steps were applied:

- Converted date columns to datetime format
- Verified dataset structure
- Ensured required columns were present
- Sorted datasets to support time-based joins

Example:

```python
transaction['order_date'] = pd.to_datetime(transaction['order_date'])
price_history['effective_date'] = pd.to_datetime(price_history['effective_date'])

🔗 Historical Price Lookup

To match each transaction with the correct historical price, we use Pandas merge_asof().

Both datasets must be sorted before applying the merge.

transaction = transaction.sort_values('order_date')
price_history = price_history.sort_values('effective_date')

Then perform the time-based merge:

result = pd.merge_asof(
    transaction,
    price_history,
    left_on='order_date',
    right_on='effective_date',
    by='pizza_id',
    direction='backward'
)

Why direction="backward"?

It finds the most recent price change before or equal to the transaction date.

This ensures each transaction uses the correct historical price.

💰 Revenue Calculation

Revenue for each transaction is calculated as:


Revenue = Price × Quantity

result['revenue'] = result['price'] * result['quantity']

📉 Rounding Down the Total Revenue

The final requirement is to round down the total revenue to the nearest integer.

import numpy as np

total_revenue = np.floor(result['revenue'].sum())

print(int(total_revenue))

Using floor() ensures the value is always rounded down, as required.

✅ Final Output

The result represents the total revenue generated from all transactions using the correct historical prices.

This approach guarantees that each order reflects the actual price active at the time of purchase.
