---
layout: default
title: "Email"
parent: "News and Announcement"
---

# Email Notifications

> **Requires the role `Admin` to access the endpoint**

**Method Type:** `GET` and `POST`

---

This section describes how to send email notifications to a list of recipients.

---

### GET Method
---

The `NewsEmail` action renders the email notification form for the admin. Initialises a EmailAddress list to avoid a null reference

---

```csharp
var model = new EmailViewModel
{
    EmailAddresses = new List<EmailAddress>()
};

return View(model);
```

---

### Email Notification Form

---

The following HTML form allows the admin to compose and send an email notification.

---

```html
<form asp-action="NewsEmail" method="post" class="email-form">
    <div class="form-group">
        <label asp-for="Subject">Subject Line:</label>
        <input asp-for="Subject" class="form-control" />
        <span asp-validation-for="Subject" class="text-danger"></span>
    </div>
    <div class="form-group">
        <label asp-for="Body">Body:</label>
        <textarea asp-for="Body" class="form-control"></textarea>
        <span asp-validation-for="Body" class="text-danger"></span>
    </div>

    <h3>Add Email Addresses</h3>
    <div class="form-group">
        <label>Persons Email Address:</label>
        <input id="EmailAddress" name="EmailAddress" class="form-control" />
        <span class="text-danger"></span>
    </div>
    <div class="form-group">
        <label>Persons Name:</label>
        <input id="UserName" name="UserName" class="form-control" />
        <span class="text-danger"></span>
    </div>
    <button type="button" id="add-email-button" class="btn btn-primary">Add</button>

    <div id="email-list-container">
        <h3>Email Addresses To Send Email</h3>
        <ul id="email-list">
            @if (Model.EmailAddresses.Any())
            {
                foreach (var entry in Model.EmailAddresses)
                {
                    <li>
                        @entry.Name - @entry.Email
                        <button type="button" class="btn btn-danger btn-sm remove-email-button" data-email="@entry.Email" data-username="@entry.Name">Remove</button>
                    </li>
                }
            }
            else
            {
                <li>No emails added.</li>
            }
        </ul>
    </div>

    <input type="hidden" id="EmailAddressesJson" name="EmailAddressesJson" />

    <button type="submit" class="btn btn-primary">Send Email</button>
</form>
```

---
### POST Method
---

The POST method of the NewsEmail action handles the form submission. This method processes the submitted email details and sends the email notifications to the specified recipients.

---

```csharp
[HttpPost]
public async Task<ActionResult> NewsEmail(EmailViewModel model)
{
    if (ModelState.IsValid)
    {
        var emailAddressesJson = Request.Form["EmailAddressesJson"];
        var emailAddresses = JsonConvert.DeserializeObject<List<EmailAddress>>(emailAddressesJson);

        if (emailAddresses != null && emailAddresses.Count > 0)
        {
            emailAddresses.RemoveAt(0);
        }

        var client = new SendGridClient(_emailSettings.ApiKey);
        var fromEmail = new EmailAddress(_emailSettings.SenderEmail, _emailSettings.SenderName);
        var subject = model.Subject;
        var plainTextContent = model.Body;
        var htmlContent = $"<strong>{model.Body}</strong>";

        var msg = MailHelper.CreateSingleEmailToMultipleRecipients(fromEmail, emailAddresses, subject, plainTextContent, htmlContent);
        var response = await client.SendEmailAsync(msg).ConfigureAwait(false);

        return RedirectToAction("Index", "Home");
    }

    return View(model);
}
```

---

### Step-by-Step Explanation:

---

#### Model Validation:

---

The method first checks if the model state is valid. This ensures that all required fields in the EmailViewModel (such as Subject and Body) are filled out correctly before proceeding with sending the email.

---

```csharp
if (ModelState.IsValid)
```

---

#### Deserialize Email Addresses:

---

The method retrieves the JSON string of email addresses from the form. This string is then deserialized into a list of EmailAddress objects, allowing for easy manipulation and access to the email data.

---

```csharp
var emailAddressesJson = Request.Form["EmailAddressesJson"];
var emailAddresses = JsonConvert.DeserializeObject<List<EmailAddress>>(emailAddressesJson);
```

---

#### Email Address Management:

The first entry in the list of email addresses is removed. As it is a placeholder.

```csharp
if (emailAddresses != null && emailAddresses.Count > 0)
{
    emailAddresses.RemoveAt(0);
}
```

---

#### Prepare Email Parameters:

---

The method prepares the parameters required for sending the email, including the sender's email address, subject, and body content. It formats the HTML content for better presentation in the email.

---

```csharp
var client = new SendGridClient(_emailSettings.ApiKey);
var fromEmail = new EmailAddress(_emailSettings.SenderEmail, _emailSettings.SenderName);
var subject = model.Subject;
var plainTextContent = model.Body;
var htmlContent = $"<strong>{model.Body}</strong>";
```

---

#### Create and Send Email:

---

A message is created using MailHelper, which facilitates sending the email to multiple recipients. The method then uses the SendGrid client to send the email asynchronously.

---

```csharp
var msg = MailHelper.CreateSingleEmailToMultipleRecipients(fromEmail, emailAddresses, subject, plainTextContent, htmlContent);
var response = await client.SendEmailAsync(msg).ConfigureAwait(false);
```

---

#### Redirect After Sending:
---

If the email is sent successfully, the method redirects the user to the "Home" page. If there was an issue (e.g., invalid form data), it redisplays the form with the entered data to allow the user to correct any mistakes.

---