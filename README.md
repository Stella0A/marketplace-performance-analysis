# Marketplace Service Performance Analysis

<div align="center">

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![Power Query](https://img.shields.io/badge/Powerquery-4479A1?style=for-the-badge&logo=powerquery&logoColor=white)
![DAX](https://img.shields.io/badge/Dax-3776AB?style=for-the-badge&logo=dax&logoColor=white)
![Excel](https://img.shields.io/badge/Excel-217346?style=for-the-badge&logo=microsoftexcel&logoColor=white)

*An end-to-end operational analysis investigating a significant decline in marketplace performance - identifying root causes, quantifying impact by seller cohort and product category, and delivering actionable recommendations.*

</div>

---

##  Table of Contents

- [The Business Problem](#-the-business-problem)
- [Data](#-data)
- [Key Findings](#-key-findings)
- [Recommendations](#-recommendations)
- [Tools Used](#-tools-used)

---

## The Business Problem

A growing online marketplace experienced a significant decline in performance over a 3-month period. The Head of Seller Operations requested an investigation into the root causes and recommendations to reverse the decline.

| Metric | Before | After | Change |
|:---|:---:|:---:|:---:|
| Buyer CSAT | 4.5 | 4.2 | ▼ 0.3 points |
| Late delivery rate | 7% | 12% |  ▲ 71% increase |
| Refund rate | 3% | 6% |  ▲ 100% increase |

**Four questions to answer:**
1. What is driving the increase in late deliveries?
2. What is driving the increase in refunds?
3. What factors are most strongly linked to low CSAT?
4. What recommendations would resolve the issues?

---

##  Data

Three datasets covering Oct–Nov 2025:

| Dataset | Rows | Key Columns |
|:---|:---:|:---|
| `orders.csv` | 500 | order_date, seller_id, category, order_value, delivery_estimate, delivery_actual, refunded, buyer_csat, fulfilment_type |
| `sellers.csv` | 40 | country, account_tier, active_listings, avg_dispatch_time_hours, join_date |
| `support_tickets.csv` | 150 | category, resolution_time_hours, related_order_id |

### Data Model
sellers ──(seller_id)──▶ orders ──(order_id)──▶ support_tickets
(1)                    (Many)     (1)              (Many)

> Orders joined to sellers on `seller_id` to bring in country, tier and dispatch time.
> Orders joined to support tickets on `order_id` to link complaints back to seller performance.

---

##  Key Findings

### Finding 1 - Late Deliveries Are a Seller-Origin Problem

| Country | Late Rate | Avg Dispatch | Avg Fulfilment | Days Over Promise |
|:---|:---:|:---:|:---:|:---:|
| 🇨🇳 CN | **64.7%** | 42-46 hrs | 4.18 days | +1.18 days |
| 🇩🇪 DE | **58.7%** | 38-44 hrs | 4.13 days | +1.13 days |
| 🇬🇧 UK | **8.2%** | 16-17 hrs | 3.16 days | +0.16 days |

>  Every seller promises 3-day delivery. UK sellers deliver in 3.16 days. CN and DE sellers consistently take over 4 days - the parcel is already late before it leaves the warehouse. Late orders average **40.3 hrs dispatch** vs **22.0 hrs** for on-time orders.

**Key observations:**
- Late deliveries are caused by the dispatch time. CN and DE sellers take 39-47 hours before shipping an order while UK sellers average 16-19 hours.
- The issue of late deliveries and long dispatch hours occurs across every product category for CN/DE especially with DE Fashion (87.5% late) and CN Electronics (75% late)
- 10 out of 40 sellers** (25%) exceed 50% late rate — all CN or DE
- S005, S012, S0028, S011, S029 all exceed 70% late rates and are among the top 5 sellers dispatching late taking 48-57 hours to dispatch
- Fulfilment type has no impact (Marketplace 29.1% vs Seller 26.9%) — ruling out platform logistics

---

### Finding 2 - Refunds Are Directly Caused by Late Deliveries

| Delivery Outcome | Refund Rate | Orders |
|:---|:---:|:---:|
|  On-time | **0%** | 360 |
|  Late | **30.7%** | 140 |

>  Not a single on-time order was refunded. Every refund came from a late order.

**Refund rate by country:**

| Country | Refund Rate | Share of All Refunds |
|:---|:---:|:---:|
| 🇨🇳 CN | 20.2% | 56% |
| 🇩🇪 DE | 17.5% | 26% |
| 🇬🇧 UK | 2.5% | 19% |

**Worst category × country combinations:**

| Country | Category | Refund Rate | Note |
|:---|:---|:---:|:---|
| 🇩🇪 DE | Fashion | **50.0%** | 1 in 2 orders refunded |
| 🇨🇳 CN | Electronics | **33.3%** | Quality issue suspected |
| 🇨🇳 CN | Toys | **27.6%** | High alongside late rate |
| 🇬🇧 UK | Fashion | 5.3% | Same category — 10x lower |

>  CN buyers are skipping delivery complaints and going straight to refund tickets — suggesting lost confidence in the seller.

**Key observations:**
- Late delivery is a significant contributor to refunds. 30.7% of all late orders end in a refund and not a single on-time order was refunded. 
- The same seller cohort is driving refunds. CN sellers refund 20.2% of orders, DE sellers 17.5%, UK sellers 2.5%.
- From the category level, Fashion (10.8%) and Toys (9.6%) carry the highest refund rates with DE Fashion taking a 50% refund rate and CN Electronics sits at 33.3%.
- S012 (CN), S0021 (CN), S025 (DE), S029 (CN), and S011 (CN) account for most of the refunds requested by customers.

---

### Finding 3 - Late Deliveries & Refunds request are affecting CSAT score

**CSAT by delivery outcome:**

| Outcome | Avg CSAT | Orders |
|:---|:---:|:---:|
|  On-time | **5.0** | 360 |
|  Late | **2.81** | 97 |
|  Refunded | **1.60** | 43 |

**CSAT by country:**

| Country | Avg CSAT | Late Rate | Refund Rate |
|:---|:---:|:---:|:---:|
| 🇬🇧 UK | **4.81** | 8.2% | 2.5% |
| 🇩🇪 DE | 3.70 | 58.7% | 17.5% |
| 🇨🇳 CN | 3.61 | 64.7% | 20.2% |

**Pro tier vs Standard — the broken badge:**

| Country | Tier | Late Rate | CSAT | Dispatch |
|:---|:---|:---:|:---:|:---:|
| 🇬🇧 UK | Pro | 3.7% | 4.89 | 15.1 hrs |
| 🇬🇧 UK | Standard | 10.5% | 4.78 | 18.1 hrs |
| 🇨🇳 CN | Pro | **68.9%** | 3.56 | 41.2 hrs |
| 🇨🇳 CN | Standard | 62.2% | 3.65 | 48.6 hrs |
| 🇩🇪 DE | Pro | 52.9% | 3.65 | 37.3 hrs |
| 🇩🇪 DE | Standard | 60.9% | 3.72 | 44.5 hrs |

>  CN Pro sellers perform **worse** than CN Standard sellers on late rate and CSAT. The Pro badge is currently misleading buyers.

**Key observations:**
- Late delivery is the strongest factor affecting CSAT score and causing it to decline. On-time orders score a perfect 5.0 CSAT. Late orders score 2.81. This shows customers reviews/rating are largely driven by the kind of service they receive.
- Refunds are also contributing to CSAT decline. When a buyer receives late orders, they request for refunds which decreases CSAT scores further. Refunded orders score 1.60 CSAT on average
- CN and DE sellers have the Lowest CSAT scores with S019, S025, S029 appearing among the top.
- UK Pro sellers outperform Standard sellers, but CN Pro sellers are actually performing worse than CN Standard sellers on late rate (68.9% vs 62.2%). 

---

##  Recommendations - Next Steps

### 

| # | Seller | Buyer |
|:---:|:---|:---:|
| 1 | Implement stricter late dispatch metrics  - we should consider enforcing a 24-hour dispatch time cap for sellers and penalties for consistent failure like reduced visibility or listing suspension. | Encourage exchange over refund** — keep revenue on platform | Immediate | Reduces refund volume |
| 2 | Review the Pro tier criteria and introduce stricter requirements for sellers to meet to  earn the badge. This could also include stricter vendor screening. | Consider introducing a compensation for late deliveries e.g. discount on next orders |
| 3 | Introduce tiered delivery options that meets the customers expectations and allow sellers to set realistic delivery windows within the marketplace windows. e.g. standard 3 to 5 days or premium next day.  | Alert buyers automatically when an order is running late  before they raise a ticket. This would help reduce complaint volume and show platform accountability. |
| 4 | Consistently encourage prepackaging items among sellers to speed up dispatch time and introduce category-level quality standards for Fashion and Toys |

---

## Tools Used

Tool 
- Power BI Desktop
- Power BI MCP
- Excel
- PowerPoint 

---

**Stella Akindolie** · Data & Operations Analyst




