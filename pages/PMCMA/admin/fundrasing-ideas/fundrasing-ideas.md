---
layout: default
title: "Fundraising Ideas"
parent: "Admin"
nav_order: 1
---

# Fundraising Ideas

> **Requires the role `Admin` to access the endpoint**

### Method Type: `GET`

---

This endpoint fetches all Fundraising Ideas that have been submitted and stores them in a `List<FundraiserIdeasViewModel>`, using the variable `fundraiserIdeas`. The data is then returned to be used in the view.

---

## FundraiserIdeasViewModel

```csharp
public class FundraiserIdeasViewModel
{
    public int Id { get; set; }
    public required string Name { get; set; }
    public required string Email { get; set; }
    public required string PhoneNumber { get; set; }
    public required string Message { get; set; }
}
```

## Query to Fetch Fundraising Ideas:
```csharp
var fundraiserIdeas = _context.FundraiserIdeas
    .Select(f => new FundraiserIdeasViewModel
    {
        Id = f.Id,
        Name = f.Name,
        Email = f.Email,
        PhoneNumber = f.PhoneNumber,
        Message = f.Message
    })
    .ToList();
return View(fundraiserIdeas)
```

## Display in the View

---

The view will display each idea in its own card, alternating between green and yellow inverted comma images. Users can navigate between ideas using the left and right navigation arrows.

---

```html
<div class="card-container">
    <div class="nav-arrow prev-arrow" onclick="prevSlide()">&#9664;</div>
    <div class="card-wrapper">
        @foreach (var idea in Model.Select((idea, index) => new { idea, index }))
        {
            <div class="card">
                <div class="card-body">
                    <img src="~/@(idea.index % 2 == 0 ? 'inverted-green.png' : 'inverted-yellow.png')" />
                    <p class="card-text" style="margin-top:30px; font-size:22px">@idea.idea.Message</p>
                    <h5 class="card-title">@idea.idea.Name</h5>
                    <h6 class="card-subtitle mb-2 text-muted">@idea.idea.Email</h6>
                    <p class="card-text">Phone: @idea.idea.PhoneNumber</p>
                </div>
            </div>
        }
    </div>
    <div class="nav-arrow next-arrow" onclick="nextSlide()">&#9654;</div>
</div>
```