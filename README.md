# Yahoo-Finance-ETL-Looker-Dashboard

Automated ETL pipeline for financial asset tracking using Python and Yahoo Finance API. This project extracts daily stock data, loads it into an SQL server, Excel/CSV, or Google Sheets, and powers a dynamic Looker Studio dashboard. Ideal for showcasing fintech data engineering skills, automation, and cloud-based visualization.

## 💹 Yahoo Finance ETL

Yahoo Finance ETL lets you convert live market data into structured formats like MySQL tables, Excel files, and CSVs. It supports currencies, commodities, and crypto assets with built-in metrics like high/low, open/close, returns, and moving averages.

Want to explore asset trends right away? Connect your dashboard to the MySQL database or Google Sheets.

[Full documentation available here.](https://github.com/ibtisamk/yahoo-finance-etl-dashboard)

---

## ⚡ Quickstart

### Install dependencies

```bash
pip3 install yfinance
pip3 install pandas
pip3 install mysql-connector-python
pip3 install openpyxl         # For Excel (.xlsx) support
pip3 install gspread           # For Google Sheets integration
pip3 install oauth2client      # For Google Sheets authentication
```

### Run the ETL script

```bash
python3 etl/fetch_data.py
```

### Schedule daily ingestion with cron

```bash
crontab -e
```

Add this line to run every day at 8 AM:

```bash
0 8 * * * /usr/bin/python3 /path/to/fetch_data.py >> etl/etl.log 2>&1
```

---

## 📦 Supported Assets

- Currencies: GBP/USD, EUR/USD, JPY/USD, CNY/USD, INR/USD  
- Commodities: Gold Futures, Crude Oil  
- Crypto: Bitcoin, Ethereum

---

## 📊 Metrics Extracted

- Daily High, Open, Low, Close  
- Daily return  
- 7-day moving average  
- 30-day moving average

---

## 🛠️ MySQL Integration

The ETL script inserts data into a MySQL table (`market_prices`) with upsert logic to avoid duplicates.

**Schema includes:**

- `Date`, `ticker`, `asset_name`, `asset_type`  
- `Open`, `High`, `Low`, `Close`, `Volume`  
- `return`, `7d_MA`, `30d_MA`  
- `load_timestamp`

## ☁️ Google Cloud SQL Setup (Optional)

To use this ETL pipeline with a Google Cloud SQL (MySQL) instance:

### 1. Create a Cloud SQL Instance

- Go to [Google Cloud Console](https://console.cloud.google.com/sql)
- Click **"Create Instance"**
- Choose **MySQL**
- Set:
  - Instance ID (e.g., `market-data-instance`)
  - Root password
  - Region and zone
- Click **Create**

### 2. Create a Database

- After the instance is ready, go to the instance dashboard
- Click **"Databases"** → **"Create database"**
- Name it (e.g., `market_data`)

### 3. Create a User (Optional)

- Go to **"Users"** tab
- Click **"Add user account"**
- Set username and password

### 4. Enable Public IP Access (for local testing)

- Go to **"Connections"** tab
- Add your IP address under **"Authorized networks"**

### 5. Create the Table

Once your database is ready, connect using a MySQL client (e.g., Cloud Shell, MySQL Workbench, or `mysql` CLI) and run the following SQL to create the required table:

```sql
CREATE TABLE market_prices (
    Date DATE NOT NULL,
    ticker VARCHAR(20) NOT NULL,
    asset_name VARCHAR(50),
    asset_type VARCHAR(20),
    Open DOUBLE,
    High DOUBLE,
    Low DOUBLE,
    Close DOUBLE,
    Volume DOUBLE,
    return DOUBLE,
    7d_MA DOUBLE,
    30d_MA DOUBLE,
    load_timestamp DATETIME,
    PRIMARY KEY (Date, ticker)
);
```

This schema matches the ETL output and uses a composite primary key to prevent duplicate entries.

### 6. Connect from Python

Update your `mysql.connector.connect()` block in the script:

```python
conn = mysql.connector.connect(
    host='YOUR_INSTANCE_IP',
    database='market_data',
    user='your_user',
    password='your_password'
)
```

---

## 🧾 Excel Integration

The ETL script can export data to an Excel file (`market_data.xlsx`) using `pandas` and `openpyxl`. This format is ideal for analysts, researchers, and business users who prefer working in Excel or need to share data offline.

**Schema includes:**

- `Date`, `ticker`, `asset_name`, `asset_type`  
- `Open`, `High`, `Low`, `Close`, `Volume`  
- `return`, `7d_MA`, `30d_MA`  
- `load_timestamp`

**Use Cases:**

- 📊 Manual analysis with pivot tables and charts  
- 📤 Easy sharing via email or cloud drives  
- 📁 Import into BI tools like Power BI or Tableau  
- 📎 Archival snapshots for compliance or audit trails

**Export Example:**

```python
df_all.to_excel("market_data.xlsx", index=False, engine="openpyxl")
```
