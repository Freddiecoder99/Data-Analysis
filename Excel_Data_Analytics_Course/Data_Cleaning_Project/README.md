# Data Cleaning Project: Flipkart Products

**20,000 messy scraped rows in. One clean, tested, documented table out.**

Most portfolio projects show a dashboard. This one shows what happens *before* the dashboard: finding what's wrong with the data, deciding how to fix it, and proving the fix worked. Built in Excel with Power Query and formulas.

| | Before | After |
|---|---|---|
| Rows | 20,000 | 19,643 |
| Timestamps stored as text | 20,000 | 0 |
| Brands blank or "NA" | 5,864 | 0 |
| Duplicate rows | 357 | 0 |
| Descriptions with line breaks | 3,982 | 0 |
| Top-level categories | 265 | 33 |

---

## The starting point

The data is a sample of 20,000 products scraped from Flipkart, an Indian e-commerce site ([Kaggle dataset](https://www.kaggle.com/datasets/PromptCloudHQ/flipkart-products)). It has 15 columns: names, prices, brands, ratings, descriptions and more.

Scraped data is a good teacher because nobody has tidied it for you.

<img width="1600" height="826" alt="WhatsApp Image 2026-11-03 at 4 07 49 AM" src="https://github.com/user-attachments/assets/0b92c705-178b-46da-812a-6d434ad454e6" />


I kept this original sheet untouched for the whole project, so I could always compare against it. I turned it into an Excel Table so formulas could refer to columns by name.

<img width="1600" height="842" alt="image" src="https://github.com/user-attachments/assets/baefdc21-7859-426d-b94e-094fc4e9cca2" />

---

## Step 1: Audit before fixing anything

Before changing a single cell, I wrote formulas to measure every problem. That gave each fix a "before" number.

![The Audit sheet]<img width="954" height="512" alt="image" src="https://github.com/user-attachments/assets/eab331e6-0323-4b4b-8d4a-b8631c87762c" />
)

What I found:
- Missing and disguised-missing values (blank brands, "NA", "No rating available")
- Dates and ratings stored as text
- Messy text: double spaces, line breaks, garbled characters
- Inconsistent brand names, and colour words like "Black" used as a brand
- Hundreds of duplicate rows and a category column crammed into one cell

---

## Step 2: Clean it in Power Query

All the cleaning lives in one Power Query. Each decision is its own named step, so the whole process reads top to bottom and can be re-run with one click.

![Power Query Applied Steps]<img width="1600" height="845" alt="image" src="https://github.com/user-attachments/assets/36555734-5fb7-4d6c-a137-a741b645cc55" />
)

| Stage | What I did |
|---|---|
| **Structure** | Renamed columns, removed a duplicate column, split the category tree into three columns |
| **Data types** | Converted text dates and ratings to real dates and numbers |
| **Text** | Removed line breaks, fixed garbled characters, trimmed extra spaces |
| **Missing values** | Labelled missing brands `Unknown`, left prices and ratings blank instead of guessing |
| **Standardization** | Unified brand spelling, moved colour words out of the brand column, fixed categories |
| **Duplicates** | Removed 357 repeated rows |
| **Outliers** | Reviewed the highest prices, kept the real luxury items and flagged them |

---

## Step 3: Log every change

Every fix is recorded in a Change Log: the problem, what I did, how many rows it touched, and which tool I used.

![The Change Log](<img width="960" height="509" alt="image" src="https://github.com/user-attachments/assets/05b970ed-d975-4d9f-acec-95e1342c939f" />
)

---

## Step 4: Load it and test it

The final query loads into Excel as a clean table of 19,643 rows and 20 columns.

![The clean table](<img width="1600" height="851" alt="image" src="https://github.com/user-attachments/assets/733340dd-50fc-4a27-b78e-0619dfdd2c4d" />
)

Then I built a Validation sheet of 16 formula checks that test the finished data: no duplicates, no zero prices, no blank brands, no double spaces, and more. Each check shows PASS or FAIL.

<!-- When you have the screenshot, delete this line and the next one's comment markers:
![The Validation sheet](images/08-validation-sheet.png)
-->

---

## The results

![Before and after](i<img width="960" height="511" alt="image" src="https://github.com/user-attachments/assets/60d3688c-8578-4319-abb1-dc0fbaf8f0aa" />
)

---

## Judgment calls

Cleaning is mostly decisions, so here are the three that matter most:

- **I never invented data.** Missing prices and ratings stayed blank. A guessed price would quietly corrupt every average.
- **I kept the extreme prices.** The highest (571,230) is a luxury watch, not a typo. I checked each one and flagged them instead of deleting.
- **I labelled missing brands `Unknown`** instead of leaving them blank, so those rows stay visible in any breakdown.

## Known limitations

- 28 text cells still contain garbled characters that couldn't be repaired safely.
- About 37% of products have an `Unknown` brand, because the source simply didn't have it.
- Duplicate listings were treated as one product. The raw data is still in the workbook if you want the variants back.

---

## What's in this repo

- `excel/flipkart_cleaning_project.xlsx`: raw data, audit, change log, clean table and validation
- `power-query/clean_products.pq`: the full cleaning recipe
- `data/flipkart_clean.csv`: the cleaned table
- `images/`: screenshots used in this README

**To reproduce:** open the workbook, go to **Data → Queries & Connections**, right-click `Clean_Products` and choose **Edit** to see every step.

---

## About me

I'm Fredrick Bundi, building a career in data analysis with Excel, SQL and PowerBI. This is one of several projects in my portfolio.

[LinkedIn](https://www.linkedin.com/in/fredrickbundi/)
