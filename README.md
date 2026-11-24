# Customer Journey, Retention and Marketing Funnel Analysis (Olist)

A practice analytics project built on two public datasets from Olist, a Brazilian e-commerce company. It is modelled on a typical marketing analytics brief: understand customer behaviour, find where the funnel leaks, compare channels, and say what the data can and cannot prove.

> **Note:** This is a self-directed portfolio project on public data. It is not client work, and the findings describe Olist's 2016-2018 data, not any current business.

**Tools:** MySQL 8 (data prep, analysis, views) · Power BI (3-page dashboard) · Excel (scenario model)

---

## Business questions

1. How many customers buy again, and how long do they take to return?
2. Are repeat customers worth more than one-time customers?
3. Where do orders drop out between purchase and delivery, and does late delivery hurt retention?
4. Which marketing channels turn leads into closed deals, and how long does that take?
5. What would improve the funnel, and what can't this data tell us?

## Data

| Dataset | Used for | Tables used |
|---|---|---|
| [Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) | Customer, order and delivery analysis | customers, orders, order_items, order_payments |
| [Marketing Funnel by Olist](https://www.kaggle.com/datasets/olistbr/marketing-funnel-olist) | Lead-to-deal channel analysis | marketing qualified leads, closed deals |

The raw files are not stored in this repository. Download them from Kaggle and check each page for its licence terms. The 1M-row geolocation file was not needed and was skipped.

**Important:** in the Marketing Funnel dataset, "leads" are businesses wanting to sell on the Olist marketplace, not shoppers.

## Approach

1. **Prepare data (SQL):** converted text dates to dates, summed payments per order (an order can have several payment rows), and built one joined analysis table.
2. **Identify customers correctly:** `customer_id` changes with every order, so all repeat-customer logic uses `customer_unique_id`. Cancelled orders are excluded from customer analysis.
3. **Analyse (SQL):** repeat rate, time to second order (using `ROW_NUMBER()` to find each customer's first and second purchase), order funnel, late deliveries, lead conversion and time to close by channel.
4. **Publish through views:** four SQL views feed Power BI, so the logic lives in SQL and the dashboard only displays it.
5. **Visualise (Power BI):** three pages: Customer Retention, Orders and Delivery, Channels.

## Key findings

### Retention
- **Only 3.1% of customers bought again** (2,997 of 96,096). About 97% bought once.
- **Median time to a second order is 28 days** (the average is higher at about 80 because a smaller group returns much later), so the first month after purchase is the window that matters most.
- **Repeat customers spend about twice as much per customer** (R$315 vs R$162). They are 3.1% of customers but about 5.9% of revenue. The higher value comes from placing more orders, not from larger orders.

### Orders and delivery
- 97.0% of orders are delivered (96,476 of 99,441). The largest drop is between approval and shipping (1,623 orders, about 1.6%), which points to seller-side fulfilment delays.
- **8.1% of delivered orders arrive late** (7,827 of 96,476). The highest late rates are in Alagoas (23.9%) and Maranhão (19.7%), though states with few orders can swing widely.
- **Customers whose first order arrived late repeated less often:** 2.54% vs 3.13% (about 19% lower). A two-proportion z-test gives z of about 2.8.
- The effect is small. If late first orders repeated at the on-time rate, that would add roughly 45 repeat customers, so lateness is not the main reason retention is low.

### Marketing funnel
- 8,000 leads produced 842 closed deals, an overall conversion of **10.5%**. The average time to close is about 47 days.
- **Social is the weakest large channel:** 5.6% conversion (75 deals from 1,350 leads), about half the overall rate, and the slowest to close at 61 days.
- **Paid search (12.3%) and organic search (11.8%) perform about the same.** The gap is within noise.
- **Direct traffic and referral close fastest** (31 and 33 days).
- **Tracking gap:** leads with an unknown or blank source are 14.5% of all leads and 23% of closed deals. Attribution cannot be trusted for these.

## Scenario (Excel model)

`excel/scenario_model.xlsx` models leads needed per channel for a target number of deals. One illustrative scenario: if social converted at the overall 10.5% instead of 5.6%, the same 1,350 leads would produce about 67 more deals (+8%).

This is a "what if" built on assumptions, not a forecast. It assumes conversion stays constant as volume changes, and it contains no cost data.

## Recommendations (hypotheses to test, not proven causes)

1. **Retention:** test a follow-up campaign within the first four weeks after a first purchase.
2. **Social leads:** review how social leads are sourced and qualified, since volume is high but conversion is low.
3. **Tracking:** make lead source a required field with a single value for "unknown", and tag campaigns consistently (UTM parameters), so the 23% of unattributed deals can be traced.
4. **Delivery:** investigate seller shipping delays and the states with the highest late rates.

## Limitations

- **Single source per lead (first-touch only).** Assisted and last-touch attribution, and multi-channel journeys, cannot be analysed with this data.
- **No spend data**, so no cost per acquisition or return on ad spend.
- **Small channels** (fewer than 30 closed deals: email, display, referral, other, other_publicities) are flagged and should not be ranked against larger ones.
- **Average days to close covers closed deals only.** Leads that never closed are not in it.
- **Correlation, not causation.** For example, late delivery is associated with fewer repeat purchases, but late orders may also be concentrated among certain sellers or regions.
- **Historical data:** 2016-2018 Brazil. Trend charts exclude sparse months at the edges of the data.

## Repository structure

```
├── README.md
├── sql/
│   ├── 01_data_prep.sql
│   ├── 02_customer_analysis.sql
│   ├── 03_order_funnel_and_delivery.sql
│   ├── 04_marketing_funnel.sql
│   └── 05_views_for_powerbi.sql
├── powerbi/
│   └── olist_dashboard.pbix
├── excel/
│   └── scenario_model.xlsx
└── images/
    ├── retention.png
    ├── orders_delivery.png
    └── channels.png
```

## Dashboard preview

![Customer Retention](images/retention.png)
![Orders and Delivery](images/orders_delivery.png)
![Channels](images/channels.png)

## How to reproduce

1. Download both datasets from Kaggle and create a MySQL 8 database.
2. Import the tables listed above (use `LOAD DATA LOCAL INFILE` or pandas `to_sql` with chunks for faster loading).
3. Run the files in `sql/` in numerical order. The last file creates the four views used by Power BI.
4. Open `olist_dashboard.pbix` and point the data source to your MySQL database.

## About

Built by **Aqiba**, a self-taught data analyst (Python, SQL, Power BI, Excel).
LinkedIn: [linkedin.com/in/aqiba-farman](https://www.linkedin.com/in/aqiba-farman)
