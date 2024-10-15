---
layout: default
title: "Donations"
parent: "Admin"
nav_order: 4
---

# Volunteer Statistics

> **Requires the role `Admin` to access the endpoint**

### **Method Type:** `GET`

---

This endpoint fetches donations made by users and returns the data to be used in the view. It provides crucial insights into the donation activities, including total amounts and comparisons between different time frames.

---

### Donations Controller

---

The following code snippet outlines the logic used to gather and process donation data before sending it to the view.

---

```csharp
var settledDonations = donations.Where(d => d.SettlemetStatus == "settled").ToList();
var totalSettledAmount = settledDonations.Sum(d => d.PaidAmount);
var allDonationsOrderedByDate = donations.OrderByDescending(d => d.UtcDateTime).ToList();

// Calculate the portfolio increase percentage
DateTime currentMonth = DateTime.UtcNow;
DateTime previousMonth = currentMonth.AddMonths(-1);

var currentMonthDonations = settledDonations
    .Where(d => d.UtcDateTime.Month == currentMonth.Month && d.UtcDateTime.Year == currentMonth.Year)
    .Sum(d => d.PaidAmount);

var previousMonthDonations = settledDonations
    .Where(d => d.UtcDateTime.Month == previousMonth.Month && d.UtcDateTime.Year == previousMonth.Year)
    .Sum(d => d.PaidAmount);

double portfolioIncreasePercentage = 0;
if (previousMonthDonations != 0)
{
    portfolioIncreasePercentage = ((currentMonthDonations - previousMonthDonations) / previousMonthDonations) * 100;
}

var model = new DonationsViewModel
{
    TotalSettledAmount = totalSettledAmount,
    PortfolioIncrease = Math.Round(portfolioIncreasePercentage, 2),
    AllDonations = allDonationsOrderedByDate,
    SettledDonations = settledDonations
};

return View(model);
```

## DonationsViewModel

```csharp
public class DonationsViewModel
{
    public double TotalSettledAmount { get; set; }

    public double PortfolioIncrease {  get; set; }
    public List<Donations> AllDonations { get; set; }
    public List<Donations> SettledDonations { get; set; }
}
```

### Variables Explained

---

1. **TotalSettledAmount**: 
   - This variable holds the sum of all settled donations. It is calculated by filtering the donations list to include only those with a `SettlemetStatus` of "settled" and then summing their `PaidAmount`.

2. **PortfolioIncrease**:
   - This variable represents the percentage increase in donations from the previous month to the current month. It is calculated as follows:
     - First, the total amount of donations for the current month is determined.
     - Then, the total for the previous month is calculated.
     - Finally, the increase percentage is computed using the formula:
       \[
       \text{Portfolio Increase Percentage} = \left(\frac{\text{Current Month Donations} - \text{Previous Month Donations}}{\text{Previous Month Donations}}\right) \times 100
       \]
     - If there were no donations in the previous month, the percentage is set to zero to avoid division by zero.

3. **AllDonations**:
   - This variable contains a list of all donations, ordered by their date in descending order. It provides a complete view of all donation activities, regardless of their status.

4. **SettledDonations**:
   - This variable includes only the donations that have been settled. It is useful for focused analysis on donations that have completed the payment process.

---

### View Representation

---

The view displays two lists:
- The left side shows **All Donations**, providing a comprehensive overview of every donation made.
- The right side lists **Settled Donations**, highlighting only those contributions that have been finalized and processed successfully.

---
