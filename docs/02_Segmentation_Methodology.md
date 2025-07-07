# 2. The Segmentation Methodology

This document details the analytical approach used to segment the customer base for Aurelia Ventures. The methodology is rooted in the Pareto principle, providing a simple yet powerful way to categorize customers by their contribution to total sales.

## The Three-Tier Segmentation Model

The methodology divides all customers into three distinct segments based on their cumulative percentage contribution to total sales. This allows the business to move from a one-size-fits-all approach to a focused, tiered strategy.

### Segment Definitions

1.  **Top Clients:**
    *   **Definition:** The elite group of customers who collectively account for the **first 70%** of all sales revenue.
    *   **Strategic Implication:** These are the company's most critical assets. The strategy should be focused on **retention and nurturing** through dedicated account management and premium service.

2.  **Mid Range Clients:**
    *   **Definition:** The next tier of customers, accounting for the **next 20% of sales** (i.e., from 70% to 90% of the cumulative total).
    *   **Strategic Implication:** This segment represents the greatest **growth potential**. The strategy should focus on targeted up-selling and cross-selling initiatives to graduate these customers into the "Top Client" tier.

3.  **Less Profitable Clients:**
    *   **Definition:** The "long tail" of the customer base, comprising a large number of customers who collectively account for the **final 10%** of sales.
    *   **Strategic Implication:** The strategy for this segment is **efficiency**. Service should be delivered through low-cost, automated channels to ensure profitability is maintained on small transactions.

## The DAX Implementation: A 3-Step Process

The segmentation is not a single measure, but a series of three calculated columns added sequentially to the `Customers` table. Each step builds upon the last to create the final, usable segment group.

### Step 1: Calculate Cumulated Sales

*   **Column Name:** `Cumulated Sales` (Calculated Column)
*   **Purpose:** This column first ranks all customers by their total sales. It then calculates a running total (cumulative sum) of sales down the ranked list. This is the foundation for determining each customer's overall contribution.
*   **Note:** This formula assumes a base `[Customer Sales]` column already exists in the `Customers` table, containing the total sales for each customer.
*   **DAX Code:**
    ```dax
    Cumulated Sales =
    CALCULATE(
        [Total Sales],
        FILTER(
            ALL(Customers),
            Customers[Customer Sales] >= EARLIER(Customers[Customer Sales])
        )
    )
    ```

### Step 2: Calculate Cumulated Percentage

*   **Column Name:** `Cumulated Percentage` (Calculated Column)
*   **Purpose:** Using the result from Step 1, this column converts the raw `Cumulated Sales` value into a percentage of the grand total sales. A customer with a value of `0.7` here means they fall within the top 70% of all sales.
*   **DAX Code:**
    ```dax
    Cumulated Percentage =
    DIVIDE(
        Customers[Cumulated Sales],
        SUM(Customers[Customer Sales]),
        0
    )
    ```

### Step 3: Assign the Segment Group

*   **Column Name:** `Customer Sales Group` (Calculated Column)
*   **Purpose:** This is the final step. A `SWITCH` statement reads the `Cumulated Percentage` for each customer and assigns them to a readable segment name. This is the column that is used directly in the report slicer, making the entire analysis interactive.
*   **DAX Code:**
    ```dax
    Customer Sales Group =
    SWITCH(
        TRUE(),
        Customers[Cumulated Percentage] <= 0.7, "Top Client",
        Customers[Cumulated Percentage] <= 0.9, "Mid Range Client",
        "Less Profitable Client"
    )
    ```
These calculated columns, working together, form the analytical engine of the report, allowing every visual on the page to be filtered by these powerful strategic groupings.