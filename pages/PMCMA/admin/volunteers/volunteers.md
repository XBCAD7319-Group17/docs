---
layout: default
title: "Volunteer"
parent: "Admin"
nav_order: 2
---

# Volunteer Statistics

> **Requires the role `Admin` to access the endpoint**

### **Method Type:** `GET`

---

This endpoint fetches statistics about volunteer sign-ups and returns the data to be used in the view.

---

### Volunteer Controller
```csharp
var stats = GetVolunteerSignups();
return View(stats);
```
### Fetching Volunteer Signups

---

The `GetVolunteerSignups` method retrieves volunteer statistics for the current month, last month, and two months ago.

---

```csharp
private VolunteerStatsViewModel GetVolunteerSignups()
{
    var today = DateTime.Today;
    var currentMonth = today.Month;
    var lastMonth = today.AddMonths(-1).Month;
    var twoMonthsAgo = today.AddMonths(-2).Month;
    var year = today.Year;

    var volunteers = _context.Volunteers.ToList();

    var currentMonthData = GetMonthlyData(volunteers, currentMonth, year);
    var lastMonthData = GetMonthlyData(volunteers, lastMonth, year);
    var twoMonthsAgoData = GetMonthlyData(volunteers, twoMonthsAgo, year);

    return new VolunteerStatsViewModel
    {
        CurrentMonthData = currentMonthData,
        LastMonthData = lastMonthData,
        TwoMonthsAgoData = twoMonthsAgoData
    };
}
```

### Monthly Data Calculation

---

The `GetMonthlyData` method counts volunteer sign-ups per week for a specific month.

---

```csharp
private List<int> GetMonthlyData(List<VolunteerViewModel> volunteers, int month, int year)
{
    return Enumerable.Range(1, 4).Select(week =>
        volunteers.Count(v => v.DateSignUp.Year == year &&
                              v.DateSignUp.Month == month &&
                              GetWeekOfMonth(v.DateSignUp) == week)
    ).ToList();
}
```
### Getting Week of Month

---

The `GetWeekOfMonth` method calculates which week of the month a specific date falls into.

---

```csharp
private int GetWeekOfMonth(DateOnly date)
{
    return (date.Day - 1) / 7 + 1;
}
```
