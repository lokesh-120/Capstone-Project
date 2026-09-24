# Capstone-Project
# Books Data Pipeline

## Project Description

This project scrapes book information from **Books to Scrape**, cleans and converts the data using Python and Pandas, stores the cleaned data in a normalized SQLite database, and runs SQL queries on the database.

## Requirements

Install the required Python libraries:

```bash
pip install requests beautifulsoup4 pandas
```

SQLite is included with Python, so no separate installation is required.

## How to Run

1. Clone or download the project.
2. Open the project folder.
3. Run the Python pipeline:

```bash
python data_pipeline.py
```

The pipeline will:

* Scrape book information from the website.
* Clean and convert the data.
* Create the SQLite database `books.db`.
* Insert the cleaned data into the database.
* Execute SQL queries.
* Save query results in `query_outputs.txt`.

## Data Collected

The following fields were collected:

* `title` — Book title
* `price` — Original price text in GBP
* `star_rating` — Original rating text such as One, Two, Three, Four, Five
* `availability` — Availability text
* `category` — Book category

## Data Cleaning and Parsing Decisions

### Price

The original `price` column was kept unchanged.

A new `price_gbp` column was created by removing the `£` symbol and converting the value to a float.

```python
df["price_gbp"] = df["price"].str.replace("£", "", regex=False).astype(float)
```

### Rating

The text ratings were converted into integers:

* One → 1
* Two → 2
* Three → 3
* Four → 4
* Five → 5

Missing numeric rating values were handled using the median.

```python
df["rating"] = df["rating"].fillna(df["rating"].median())
```

### Stock Availability

The `availability` text was converted into a Boolean `in_stock` column.

* `In stock` → `True`
* Not in stock → `False`

```python
df["in_stock"] = df["availability"].str.contains(
    "In stock", case=False, na=False
)
```

### Currency Conversion

A fixed conversion rate was used:

**1 GBP = 105.50 INR**

The INR price was calculated as:

```python
df["price_inr"] = (df["price_gbp"] * 105.50).round(2)
```

No live currency API was used because the assignment specifies a fixed baseline rate.

### Numeric Missing Values

For numeric fields such as `price_gbp` and `rating`, invalid values were converted to missing values where necessary and filled using the median.

Rows would be dropped when a value could not be meaningfully parsed and median imputation was not appropriate, such as an invalid Boolean field.

## Database Design

The SQLite database uses two normalized tables:

### categories

* `category_id` — Primary Key
* `category_name` — Unique category name

### books

* `book_id` — Primary Key
* `title`
* `price_gbp`
* `price_inr`
* `rating`
* `in_stock`
* `category_id` — Foreign Key referencing `categories.category_id`

The relationship is:

```text
categories
    |
    | category_id
    ↓
books.category_id
```

## SQL Queries

The project executes SQL queries demonstrating:

* SELECT and WHERE
* ORDER BY
* LIMIT
* DISTINCT
* BETWEEN
* JOIN

The query strings and their outputs are saved in:

```text
query_outputs.txt
```

## Output Files

After running the pipeline, the main outputs are:

```text
books.db
query_outputs.txt
```

`books.db` contains the normalized SQLite database.
`query_outputs.txt` contains the SQL queries and their results.
.............................................................................................................................
module 2 readme
.............................................................................................................................
1. Survival by Sex — Bar Chart
sns.barplot(data=df, x="sex", y="survived")
plt.title("Survival Rate by Sex")
plt.show()

Interpretation:
The chart compares survival rates between male and female passengers. Female passengers had a higher survival rate than male passengers. This shows that sex was strongly associated with survival in the Titanic dataset.

2. Survival by Passenger Class — Bar Chart
sns.barplot(data=df, x="pclass", y="survived")
plt.title("Survival Rate by Passenger Class")
plt.show()

Interpretation:
The chart shows survival rates across the three passenger classes. First-class passengers had a higher survival rate, while third-class passengers had a lower survival rate. This suggests that passenger class was associated with survival.

3. Sex + Passenger Class — Grouped Bar Chart
sns.barplot(data=df, x="pclass", y="survived", hue="sex")
plt.title("Survival Rate by Sex and Passenger Class")
plt.show()

Interpretation:
This chart combines sex and passenger class to give a more detailed view of survival. Female passengers generally had higher survival rates than male passengers within the passenger classes. The combination of sex and class provides more information than looking at either variable alone.

4. Age Distribution by Survival — Boxplot
sns.boxplot(data=df, x="survived", y="age")
plt.title("Age Distribution by Survival")
plt.show()

Interpretation:
The boxplot compares the age distributions of passengers who survived and those who did not. It shows the median age and spread of ages for both groups. Age therefore provides another dimension for understanding differences between survivors and non-survivors.
