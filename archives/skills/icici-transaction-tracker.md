# Skill Archive: icici-transaction-tracker

**Added by:** User
**Last updated:** Oct 18, 2025
**Archived:** 2026-03-11

## Description

Extract ICICI Bank credit card transactions from Gmail, organize them in Excel with merchant mapping, expense categories, currency conversion, and visual analysis with pie charts

---

## SKILL.md Content

# ICICI Transaction Tracker

## Overview

This skill extracts ICICI Bank credit card transaction emails from Gmail, processes the data with merchant identification, expense categorization, multi-currency support with INR conversion, and visual analytics including pie charts.

Claude should use this skill whenever a user requests to:

- Track ICICI credit card transactions
- Organize ICICI Bank transaction emails
- Create a spending summary from ICICI emails
- Analyze credit card expenses from ICICI Bank

## Core Features

- Smart Transaction Filtering - Excludes declined transactions and EMI conversion notifications
- Multi-Currency Support - Handles foreign currency transactions with INR conversion
- Merchant Name Mapping - Converts cryptic bank tags to friendly business names
- Expense Category Assignment - Categorizes spending for analysis
- Visual Analytics - Pie charts showing spending distribution by category
- Dynamic Formulas - Auto-updating summaries and breakdowns
- Merchant Legend - Reference sheet for all merchant mappings
- Professional Formatting - Color-coded, well-organized spreadsheet

## Email Search Strategy

### Gmail Search Patterns

Use these search queries to find ICICI transaction emails:

```
from:alerts@icicbank.com subject:"transaction alert"
```

Or more specific:

```
from:alerts@icicbank.com "transaction of INR" OR "transaction of USD" OR "transaction of EUR"
```

### Transaction Filtering

**IMPORTANT: Exclude these transaction types:**

**Declined Transactions**
- Look for phrases like "declined", "not successful", "failed"
- These should be filtered out during processing

**EMI Conversion Notifications**
- Emails about converting transactions to EMI
- Usually contain "EMI conversion" or "Convert to EMI"
- These are notifications, not actual transactions

Only include successful transaction emails in the final spreadsheet.

## Information Extraction

### Key Fields to Extract

From each transaction email, extract:

| Field | Description |
|-------|-------------|
| Date | Transaction date (DD-MM-YYYY format) |
| Time | Transaction time (HH:MM:SS format) |
| Amount | Original transaction amount |
| Currency | Currency code (INR, USD, EUR, etc.) |
| Amount (INR) | INR equivalent (if foreign currency) |
| Bank Tag | Original merchant identifier from email |
| Merchant Name | Friendly business name (ask user) |
| Expense Category | Spending category (ask user) |
| Card Last 4 | Last 4 digits of card used |
| Type | Transaction type (Purchase, Online, etc.) |
| Balance | Available balance after transaction |
| Email Date | Date email was received |

### Merchant Identification and Mapping

Process:

1. Extract all unique "bank tags" (merchant identifiers) from emails
2. Ask the user to identify what business each tag represents
3. Create a mapping dictionary
4. Use mapped names in the spreadsheet

Example Bank Tags:

- `RRS REST` → User might identify as "Terra Bites Cafe"
- `RCB FINA` → User might identify as "Ritz Carlton Restaurant"
- `STAR SERVICE STATION` → User might identify as "Gundlupet Petrol Pump"
- `myG THON` → User might identify as "My Gym Thane"

Implementation:

```python
MERCHANT_LEGEND = {
    "RRS REST": {"name": "Terra Bites Cafe", "category": "Cafe outing"},
    "RCB FINA": {"name": "Ritz Carlton Restaurant", "category": "Dinner outing"},
    "STAR SERVICE STATION": {"name": "Gundlupet Petrol Pump", "category": "Fuel"},
}

def get_merchant_name(bank_tag):
    merchant_info = MERCHANT_LEGEND.get(bank_tag)
    return merchant_info["name"] if merchant_info else bank_tag

def get_expense_category(bank_tag):
    merchant_info = MERCHANT_LEGEND.get(bank_tag)
    return merchant_info["category"] if merchant_info else "Uncategorized"
```

### Expense Category Mapping

Process:

1. After identifying merchant names, ask user to assign categories
2. Common categories include:
   - Fuel
   - Food & Dining / Restaurant
   - Groceries / Quick Commerce
   - Travel / Hotel
   - Gifts
   - Shopping / Home Purchase
   - Entertainment
   - Healthcare
   - Utilities
   - Cafe outing
   - Dinner outing
   - Misc / Miscellaneous
3. Store categories in the merchant legend
4. Use categories for spending analysis

User Interaction Example:

```
"Now, what expense category should each merchant belong to?

1. Terra Bites Cafe → ?
2. Ritz Carlton Restaurant → ?
3. Gundlupet Petrol Pump → ?

Common categories: Fuel, Food & Dining, Travel, Groceries, Shopping, Entertainment, etc."
```

### Parsing Guidelines

**Date and Time:**
- Extract from transaction details
- Format: DD-MM-YYYY for dates, HH:MM:SS for times
- Handle both 12-hour and 24-hour formats

**Amounts:**
- Remove currency symbols and commas
- Store as numeric values
- Keep 2 decimal places

**Card Information:**
- Extract last 4 digits
- Format: "XXXX" (just the 4 digits)

**Foreign Currency Handling:**

When a transaction is in foreign currency:

1. Extract Original Amount and Currency
   - Look for patterns like "USD 45.00" or "EUR 100.50"
   - Store original amount in "Amount" column
   - Store currency code in "Currency" column
2. Extract INR Equivalent
   - ICICI emails usually show: "INR equivalent: 3,776.00"
   - Store this value in "Amount (INR)" column
3. Formatting
   - Highlight the "Amount (INR)" cell in yellow (#FFFFCC)
   - This makes foreign currency transactions easily visible

Example Foreign Currency Email:

```
Transaction of USD 45.00 at RCB FINA
Card ending 1008
INR equivalent: 3,776.00
Available balance: INR 4,12,345.67
```

Extraction:
- Amount: 45.00
- Currency: USD
- Amount (INR): 3,776.00 (highlighted in yellow)
- Bank Tag: RCB FINA

## Spreadsheet Creation

### Spreadsheet Structure

Three sheets required:

1. Transactions - Main data sheet
2. Merchant Legend - Reference for mappings
3. Summary - Analytics and totals

### Transactions Sheet (12 columns)

| Column | Description | Format | Example |
|--------|-------------|--------|---------|
| Date | Transaction date | DD-MM-YYYY | 05-10-2025 |
| Time | Transaction time | HH:MM:SS | 14:23:45 |
| Amount | Original amount | Number | 45.00 |
| Currency | Currency code | Text | USD |
| Amount (INR) | INR equivalent | Number | 3,776.00 |
| Bank Tag | Original identifier | Text (gray) | RCB FINA |
| Merchant Name | Friendly name | Text (bold) | Ritz Carlton Restaurant |
| Expense Category | Spending category | Text (blue italic) | Dinner outing |
| Card Last 4 | Card ending digits | Text | 1008 |
| Type | Transaction type | Text | Purchase |
| Balance | Available balance | Number | 4,12,345.67 |
| Email Date | Email received | DD-MM-YYYY | 05-10-2025 |

Important Notes:
- "Bank Tag" column uses gray text (#808080) to de-emphasize
- "Merchant Name" column uses bold text for prominence
- "Expense Category" column uses blue italic text (#0066CC)
- Foreign currency transactions have yellow background in "Amount (INR)" column

### Merchant Legend Sheet (3 columns)

| Column | Description | Format |
|--------|-------------|--------|
| Bank Tag | Original identifier | Text (gray) |
| Merchant Name | Friendly name | Text (bold) |
| Expense Category | Spending category | Text (blue italic) |

Purpose:
- Serves as reference guide for user
- Documents all merchant mappings
- Shows category assignments
- Alphabetically sorted by merchant name

Example:

```
Bank Tag              | Merchant Name                | Expense Category
RCB FINA             | Ritz Carlton Restaurant      | Dinner outing
RRS REST             | Terra Bites Cafe             | Cafe outing
STAR SERVICE STATION | Gundlupet Petrol Pump       | Fuel
```

### Formatting Best Practices

**Headers:**
- Background: Blue (#4472C4)
- Text: White, Bold
- Font size: 11pt

**Bank Tag Column:**
- Text color: Gray (#808080)
- Font: Normal
- Purpose: De-emphasize technical identifier

**Merchant Name Column:**
- Text: Bold black
- Font: Normal size
- Purpose: Make friendly name prominent

**Expense Category Column:**
- Text color: Blue (#0066CC)
- Font: Italic
- Purpose: Distinguish category from other text

**Foreign Currency Highlighting:**
- "Amount (INR)" cell background: Yellow (#FFFFCC)
- Apply only when Currency ≠ "INR"
- Makes foreign transactions stand out

**Alternating Row Colors:**
- Even rows: Light gray (#F2F2F2)
- Odd rows: White
- Improves readability

**Column Widths:**
- Date: 12 characters
- Time: 10 characters
- Amount: 12 characters
- Currency: 10 characters
- Amount (INR): 14 characters
- Bank Tag: 25 characters
- Merchant Name: 30 characters
- Expense Category: 20 characters
- Card: 12 characters
- Type: 15 characters
- Balance: 18 characters
- Email Date: 12 characters

### Summary Sheet

Create a summary sheet with:

**Left Side (Columns A-B):**

Overall Statistics:
- Total Transactions: `=COUNTA(Transactions!A2:A[last_row])`
- Total Amount Spent: `=SUM(Transactions!E2:E[last_row])`

Card-wise Breakdown:
```excel
Card ending 1008: =SUMIF(Transactions!I2:I[last_row],"1008",Transactions!E2:E[last_row])
Card ending 1047: =SUMIF(Transactions!I2:I[last_row],"1047",Transactions!E2:E[last_row])
```

Category-wise Breakdown (SORTED BY AMOUNT - HIGHEST TO LOWEST):
```excel
Travel / Hotel: =SUMIF(Transactions!H2:H[last_row],"Travel / Hotel",Transactions!E2:E[last_row])
Fuel: =SUMIF(Transactions!H2:H[last_row],"Fuel",Transactions!E2:E[last_row])
Dinner outing: =SUMIF(Transactions!H2:H[last_row],"Dinner outing",Transactions!E2:E[last_row])
... (continue for all categories, sorted by amount)
```

**IMPORTANT:** Sort the category breakdown by spending amount (highest to lowest), not alphabetically. This shows the biggest spending categories first.

**Right Side (Columns D-I):**

Pie Chart Visualization:
- Chart title: "Spending by Category"
- Data source: Category names and amounts from left side
- Chart type: Pie chart
- Data labels: Show category name and percentage
- Position: Anchored at cell D2

**Formula Implementation:**

```python
# Category breakdown with SUMIF formulas
categories = ["Travel / Hotel", "Fuel", "Dinner outing", "Home Purchase", ...]
for category in categories:
    formula = f'=SUMIF(Transactions!H:H,"{category}",Transactions!E:E)'

# Sort categories by amount (highest first)
# Then write to summary sheet in sorted order
```

**Pie Chart Creation:**

```python
from openpyxl.chart import PieChart, Reference

# Create pie chart
pie = PieChart()
pie.title = "Spending by Category"

# Reference category names and amounts
labels = Reference(ws_summary, min_col=1, min_row=5, max_row=5+len(categories))
data = Reference(ws_summary, min_col=2, min_row=4, max_row=5+len(categories))

pie.add_data(data, titles_from_data=False)
pie.set_categories(labels)

# Show category names and percentages
pie.dataLabels = DataLabelList()
pie.dataLabels.showCatName = True
pie.dataLabels.showPercent = True

# Add to sheet
ws_summary.add_chart(pie, "D2")
```

## Workflow

### Complete Process Flow

1. Search Gmail for ICICI transaction emails
2. Read and parse each email
3. Filter transactions - Exclude declined and EMI conversion emails
4. Identify unique merchants - Extract all unique bank tags
5. Ask user for merchant names - "What business is RRS REST?"
6. Ask user for expense categories - "What category for Terra Bites Cafe?"
7. Create merchant mapping with names and categories
8. Validate data - Ensure all required fields present
9. Read the xlsx skill - Load spreadsheet creation guidance
10. Create spreadsheet with three sheets
11. Populate Transactions sheet with formatted data
12. Apply formatting - Colors, fonts, column widths
13. Create Merchant Legend sheet with all mappings
14. Add Summary sheet with formulas and sorted category breakdown
15. Create pie chart in Summary sheet
16. Save to /mnt/user-data/outputs/
17. Provide download link to user

## Error Handling

### Common Issues and Solutions

**Issue: No emails found**
Solution: Verify search query, check date range, confirm email account

**Issue: Declined transaction not filtered**
Solution: Look for keywords: "declined", "failed", "not successful"

**Issue: EMI notification included**
Solution: Check for "EMI conversion" or similar phrases

**Issue: Merchant name unclear**
Solution: Ask user for clarification, show email snippet

**Issue: Foreign currency without INR equivalent**
Solution: Mark as "Conversion not available" in Amount (INR) column

**Issue: Missing balance information**
Solution: Leave balance field empty, note in summary

**Issue: Duplicate transactions**
Solution: Check if genuinely duplicate or separate charges

**Issue: Category unclear**
Solution: Suggest common category or let user provide custom one

## User Communication

### Success Message Template

After creating the spreadsheet, provide:

```
I've created your ICICI transaction tracker for October 2025!

📊 Summary:
- [X] transactions processed
- ₹[Y] total spent
- [Z] declined/EMI emails excluded
- [N] foreign currency transactions converted
- [M] unique merchants identified
- [C] expense categories assigned

The spreadsheet includes:
✅ Transaction details with dates, amounts, and merchant names
✅ Merchant Legend sheet with all [M] merchant mappings
✅ Summary sheet with spending breakdown by category (sorted by amount)
✅ Pie chart visualization showing category distribution
✅ Dynamic formulas for automatic calculations
✅ Foreign currency conversions highlighted in yellow

[View your transaction tracker](computer:///mnt/user-data/outputs/icici_transactions_[month][year].xlsx)
```

Customization:
- Replace [X], [Y], [Z], [N], [M], [C] with actual numbers
- Add specific insights if notable patterns found
- Keep message concise but informative

## Advanced Features

### Analytics & Insights

After creating the spreadsheet, Claude can optionally:

**Identify Spending Patterns**
- Highest expense category
- Most frequent merchant
- Average transaction amount

**Pie Chart Visualization**
- Shows spending distribution by category
- Displays percentages and amounts
- Makes patterns immediately visible
- Sorted by category size (largest to smallest)

**Time-based Analysis**
- Spending by date/day of week (if requested)
- Weekend vs weekday spending

**Card Usage**
- Which card used most frequently
- Spending per card

**Filters & Views**
- Suggest enabling Excel filters
- Create named ranges for common views

## Best Practices

- Always filter out declined and EMI conversion emails
- Ask for merchant identification before processing - Don't guess
- Handle foreign currencies carefully - Highlight conversions
- Use dynamic formulas - Don't hard-code totals
- Sort categories by spending amount - Highest first in summary
- Create pie chart for visual impact - Shows patterns clearly
- Keep merchant legend updated - Add new merchants as found
- Validate data before creating spreadsheet - Check for anomalies
- Use professional formatting - Colors enhance readability
- Provide clear success message - User knows what they got

## Notes & Tips

### For Claude
- Read the xlsx skill before creating spreadsheets for formatting guidance
- Ask clarifying questions if bank tags are unclear
- Be patient with merchant identification - users need time to recall
- Suggest categories but let users customize
- Highlight the category sorting - highest spending first
- Emphasize the pie chart - visual insights are valuable
- Use formulas over hard-coded values - enables future updates
- Test formulas before finalizing spreadsheet
- Save to outputs directory - /mnt/user-data/outputs/
- Provide computer:// link - User can download directly

### For Users
- First use takes longer - Merchant identification is one-time setup
- Reuse mappings - Save merchant legend for future runs
- Update categories - Adjust as spending habits change
- Check declined filters - Ensure no valid transactions missed
- Review pie chart - Quickly spot spending patterns
- Use for budgeting - Category breakdown shows where money goes
- Export if needed - Excel file works in Google Sheets too

## Limitations

- Manual merchant identification - Cannot auto-identify on first run
- Email access required - Needs Gmail connection
- ICICI-specific - Email format may vary by bank
- One-time currency conversion - Does not update exchange rates
- Static snapshot - Not live/real-time tracking

## Future Enhancements

Possible additions (not yet implemented):
- Month-over-month comparison
- Budget tracking and alerts
- Automatic recurring processing
- Multiple currency support in one transaction
- Receipt attachment handling

## Example Usage

### User Request

"Track my ICICI credit card transactions for October 2025"

### Claude's Process

1. Search Gmail: `from:alerts@icicbank.com "transaction of INR" after:2025/10/01 before:2025/10/31`
2. Read 18 transaction emails
3. Filter out 2 (1 declined, 1 EMI notification)
4. Extract 16 successful transactions
5. Identify 11 unique merchants
6. Ask user to identify each merchant
7. Ask user to assign categories
8. Create MERCHANT_LEGEND dictionary
9. Read xlsx skill for formatting guidance
10. Create spreadsheet with 3 sheets
11. Apply professional formatting
12. Add dynamic formulas to summary
13. Sort categories by spending amount
14. Create pie chart visualization
15. Save to outputs
16. Provide download link with summary

### Result

Professional Excel file with:
- 16 transactions across 12 columns
- 11 merchants with friendly names and categories
- 8 expense categories (sorted by amount)
- Currency conversions highlighted
- Summary with card and category breakdowns
- Pie chart showing spending distribution
- Merchant Legend reference sheet
- Dynamic formulas throughout

## Technical Requirements

- Gmail access via read_gmail tools
- Python with openpyxl library
- Excel/Spreadsheet creation capability
- XLSX skill for formatting guidance

## When NOT to Use This Skill

- For other banks (different email formats)
- For debit card transactions (if format differs)
- Without Gmail access
- For live/real-time tracking (this creates snapshots)
- When user wants fully automatic processing (requires merchant ID input)
