# Data Cleaning Project: Flipkart Products

**20,000 messy scraped rows in. One clean, tested, documented table out.**

Most portfolio projects show a dashboard. This one shows what happens *before* the dashboard: finding what's wrong with the data, deciding how to fix each problem, and proving the fix worked. Everything was done in Excel using Tables, Power Query and formulas.

| What I checked | Before | After |
|---|---|---|
| Rows | 20,000 | 19,643 |
| Timestamps stored as text | 20,000 | 0 |
| Ratings stored as text | 18,151 | 0 |
| Brands blank or "NA" | 5,864 | 0 |
| Colour or fit words used as a brand | 1,657 | 0 |
| Duplicate rows | 357 | 0 |
| Descriptions with line breaks | 3,982 | 0 |
| Distinct top-level categories | 265 | 33 |

---

## The starting point

The data is a sample of 20,000 products scraped from Flipkart, an Indian e-commerce site, published on Kaggle by PromptCloud ([dataset link](https://www.kaggle.com/datasets/PromptCloudHQ/flipkart-products)). It has 15 columns covering product names, categories, prices, brands, ratings, descriptions, specifications and a crawl timestamp.

Scraped data is a good teacher because nobody has tidied it for you.

[The raw file as downloaded]<img width="1600" height="826" alt="image" src="https://github.com/user-attachments/assets/6b328fe7-2437-4e04-9100-c95aaaad277f" />


The ground rules I set before touching anything:

- **The raw data stays untouched.** All cleaning happens in a Power Query that reads from it, so I can always compare against the original.
- **Measure first, fix second.** Every problem gets a number before it gets a fix.
- **Log everything.** Each change is written down.
- **Never invent data.** If a value can't be known, it stays blank or gets an honest label.

I converted the raw range into an Excel Table so formulas could refer to columns by name.

[Raw data as an Excel Table]<img width="1600" height="842" alt="image" src="https://github.com/user-attachments/assets/7fa1f83c-ccfc-422d-8ba1-2e22bd2f772f" />


---

## Step 1: Audit before fixing anything

I wrote formulas to count every problem I could find, and this sheet drove the rest of the project.

[The Audit sheet]<img width="954" height="512" alt="image" src="https://github.com/user-attachments/assets/f9a68a9d-ceb6-455d-86e9-104af3c81190" />


| Area | What I found |
|---|---|
| **Missing values** | 5,861 blank brands (29% of rows), 78 rows with no prices, 14 blank specifications |
| **Disguised blanks** | "No rating available" in 18,151 rating cells, "NA" as a brand, and a placeholder in 53 specification cells that no blank-count would catch |
| **Wrong data types** | All 20,000 timestamps were text, and 2 brand cells were TRUE/FALSE instead of text |
| **Redundant column** | `overall_rating` was identical to `product_rating` in every row |
| **Messy text** | Double spaces, line breaks and tabs inside descriptions, and garbled characters |
| **Inconsistent brands** | Casing variants like `TheLostPuppy` and `Thelostpuppy`, plus colour words like "Black" and "Regular" stored as brands |
| **Category column** | Up to 8 levels crammed into one cell, some truncated with `...`, some holding just a product name |
| **Duplicates** | The ID column was clean, but 357 rows repeated the same name, brand, prices, description and specs |

Things that were already fine: no discounted price above its retail price, every rating between 1 and 5, and no zero or negative prices. Knowing what is fine matters as much as knowing what is broken.

---

## Step 2: Clean it in Power Query

All the cleaning lives in a single Power Query. Each decision is its own named step, so the whole process reads from top to bottom and re-runs with one click.

[Power Query Applied Steps]<img width="1600" height="845" alt="image" src="https://github.com/user-attachments/assets/b571959c-8da0-4287-b92c-dd00a9c0ad1d" />


### Structure
I renamed five columns to a consistent style and removed `overall_rating`, but only after proving it was an exact copy (a formula comparing the two columns returned 0 differences). I also split the category tree into three columns (`category_1`, `category_2`, `category_3`) and kept the original as evidence.

### Data types
Timestamps became real dates, ratings became numbers, prices became whole numbers, and the Yes/No column became True/False. The two brand cells that Excel had misread as TRUE/FALSE were converted back to the text "TRUE", because the product is literally called "TRUE Bong".

> **A mistake worth sharing:** my first attempt converted the timestamps straight to dates. Power Query read the `+0000` time-zone marker and shifted every time to my own time zone, so `22:59` on 25 March quietly became `1:59 AM` on 26 March. No error message, just wrong dates. I caught it by comparing the first row against the raw sheet. The fix was to strip the marker as text first, then convert. Now I always spot-check a row after a type change.

### Text cleaning
- Replaced 3,982 line breaks and 3,981 tabs inside descriptions with spaces. I avoided the "Clean" button because it glues words together.
- Repaired garbled characters caused by an encoding problem (for example `DÃ©cor` became `Décor`, and `360Â°` became `360°`).
- Trimmed extra spaces and collapsed double spaces in 1,329 product names and 5,216 descriptions.

### Missing values
Missing data has different causes, so I decided column by column:

| Column | Decision | Why |
|---|---|---|
| Brand | Label as `Unknown` | Blank rows would vanish from every brand breakdown |
| Prices | Keep blank, add a `price_missing` flag | A made-up price would corrupt every average |
| Ratings | Keep blank | Blank means "nobody rated it", and an average would invent opinions |
| Specifications | Turn the placeholder into a true blank | An honest blank is better than a fake value |

### Standardization
Brand spellings were unified (the most common spelling wins). 1,657 colour and fit words stored as brands, like "Regular" and "Pink", were moved to `Unknown` because they're product attributes in the wrong column. Categories were fixed too: 328 rows where the "category" was really a product name became `Uncategorized`, and truncated `...` values were cleared.

### Duplicates
I removed 357 rows that were identical in name, brand, prices, description and specifications, keeping the first of each group. The ones I inspected looked like size or colour variants of one listing, so I treated each group as one product.

### Outliers
I reviewed the highest prices instead of deleting them. All 11 prices above 100,000 were real luxury goods: a Breitling watch at 571,230, leather sofas, a Cartier watch and studio headphones. I kept them and added a `high_price` flag, plus a `discount_pct` column (0% to 96.5%).

---

## Step 3: Log every change

Every fix is recorded with the problem, what I did, how many rows it affected and which tool I used. It's the paper trail for the whole project.

[The Change Log]<img width="960" height="509" alt="image" src="https://github.com/user-attachments/assets/d0d1e4cd-5179-4ef2-a035-d7d67ba2ec5f" />


---

## Step 4: Load it and test it

The final query loads into Excel as a clean table of 19,643 rows and 20 columns.

[The clean table]<img width="1600" height="851" alt="image" src="https://github.com/user-attachments/assets/19bde8be-61ee-44b8-9974-2138911df23e" />


Then I built a Validation sheet of 16 formula checks that test the finished data. Each one has an expected result and shows PASS or FAIL. They cover duplicates, price rules (above zero, discounted never above retail), rating range, blank brands, double spaces, line breaks, truncated categories and real dates.

## The results

[Before and after]<img width="960" height="511" alt="image" src="https://github.com/user-attachments/assets/4c05b79d-b655-414b-812c-02d1009b9180" />


---

## Judgment calls

Cleaning is mostly decisions, and these are the ones someone could reasonably disagree with:

- **I never invented data.** Prices and ratings stayed blank. Prices are facts and ratings are opinions, and neither can be guessed.
- **I kept the extreme prices.** Large is not the same as wrong. I checked each one and flagged it instead of deleting.
- **I labelled missing brands `Unknown`.** It keeps about 37% of rows visible in brand breakdowns, but it also means `Unknown` is the biggest "brand", so it should be treated separately in any analysis.
- **I didn't guess brands from product names.** About 1,143 blank brands could be recovered that way, with 99.5% accuracy on rows where the brand is known. But words like "Ocean" and "United" make it risky, so I left them out.
- **I collapsed duplicate listings into one product.** If you care about individual size or colour variants, the raw data is still in the workbook.

## Known limitations

- 28 text cells still contain garbled characters from double-encoded text that couldn't be repaired safely.
- `category_3` is best-effort. In some trees the third level is a product name, so `category_1` is the reliable level.
- `product_specifications` and `image_urls` are still raw text. Parsing them into proper columns would be a good next step.

---

## What's in this repo

- `excel/flipkart_cleaning_project.xlsx`: raw data, audit, change log, clean table and validation
- `power-query/clean_products.pq`: the full cleaning recipe
- `data/flipkart_clean.csv`: the cleaned table
- `images/`: screenshots used in this README

**To reproduce:** open the workbook, go to **Data → Queries & Connections**, right-click `Clean_Products` and choose **Edit** to see every step. **Data → Refresh All** reruns the whole process.

## Skills shown

Data profiling and auditing · Power Query and M code · Excel Tables and structured references · Data type conversion · Text cleaning and encoding repair · Missing-value strategy · Standardization · Duplicate detection · Outlier review · Validation rules · Documentation

---

## About me

I'm Fredrick Bundi, building a career in data analysis with Excel, SQL and PowerBI. This is one of several projects in my portfolio.

[LinkedIn](https://www.linkedin.com/in/fredrickbundi)
