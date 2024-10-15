---
layout: default
title: "News and Announcement"
nav_order: 3
parent: "Admin"
has_children: true
---

# News and Announcements

> **Requires the role `Admin` to access the endpoint**

### **Method Type:** `GET`

---

This endpoint fetches the News and Announcement View and displays the 2 options that an admin cam make `Annonccement` and `Email`.

---

```csharp
public IActionResult NewsAndAnnouncements()
{
    return View();
}
```