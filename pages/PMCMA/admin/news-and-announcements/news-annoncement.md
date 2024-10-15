---
layout: default
title: "Announcement"
parent: "News and Announcement"
---

# Announcements

> **Requires the role `Admin` to access the endpoint**

**Method Type:** `GET` and `POST`

---

This section describes how to create announcements in the application.

---

### GET Method
---

The `Announcement` action renders the announcement form for the admin.

---

```csharp
return View();
```

### Announcement Form

---

The following HTML form allows the admin to create an announcement. Has a drop-down that is populated by the organisations. Title, Description and sign-off for the announcement.

---

```html
<form asp-action="Announcement" method="post" class="announcement-form">
    <div class="form-group">
        <label asp-for="Organistaion">Organization</label>
        <select asp-for="Organistaion" class="form-control" required>
            <option value="">Select Organization</option>
            <option value="Sizanani">Sizanani</option>
            <option value="Phakamisa">Phakamisa</option>
            <option value="Greenfields">Greenfields</option>
        </select>
        <span asp-validation-for="Organistaion" class="text-danger"></span>
    </div>
    <div class="form-group">
        <label asp-for="Title">Title</label>
        <input asp-for="Title" class="form-control" required />
        <span asp-validation-for="Title" class="text-danger"></span>
    </div>
    <div class="form-group">
        <label asp-for="Description">Description</label>
        <textarea asp-for="Description" class="form-control"></textarea>
        <span asp-validation-for="Description" class="text-danger"></span>
    </div>
    <div class="form-group">
        <label asp-for="Body">Body</label>
        <textarea asp-for="Body" class="form-control" required></textarea>
        <span asp-validation-for="Body" class="text-danger"></span>
    </div>
    <div class="form-group">
        <label asp-for="EndLine">Sign Off</label>
        <input asp-for="EndLine" class="form-control" />
        <span asp-validation-for="EndLine" class="text-danger"></span>
    </div>
    <button type="submit" class="btn btn-primary">Create Announcement</button>
</form>
```

---

### POST Method

---

The Announcement action handles form submission. It creates a new announcement and saves it to the database. The created announcement is there viewable on the `About` page.

---

```csharp
if (ModelState.IsValid)
{
    var newAnnouncement = new AnnouncementsViewModel
    {
        Organistaion = announcements.Organistaion,
        Title = announcements.Title,
        Description = announcements.Description,
        Body = announcements.Body,
        EndLine = announcements.EndLine,
        Created = DateOnly.FromDateTime(DateTime.Now)
    };

    _context.Announcements.Add(newAnnouncement);
    _context.SaveChanges();

    ViewBag.Message = "Announcement Created";
    return RedirectToAction("Index", "About");
}
return View(announcements);
}
```

