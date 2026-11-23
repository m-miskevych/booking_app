## Problem

A personal trainer and their clients arrange training sessions through messages. This approach requires repeated communication about available time slots, session confirmations, and cancellations.

## Users and Roles

### Trainer

- Registers independently, creates client records with a name and email address, and sends email invitations.
- Can edit their clients' names and email addresses, including after account creation.
- Can deactivate and reactivate clients. Future bookings must first be cancelled individually before deactivation.
- Sets, edits, and removes their availability. Bookings affected by a reduction in availability must first be cancelled individually.
- Creates immediately confirmed 60-minute bookings for clients who have accepted their invitation and created an account, including outside configured availability and less than 6 hours before a session starts. Sessions start on the hour or half hour and must not overlap other confirmed bookings. Past bookings cannot be created.
- Views their availability and confirmed bookings in a calendar, including booking details.
- Views cancelled bookings and "Late Cancellation" markers in a separate history, where they can remove the markers.
- Can cancel a confirmed booking before the session starts without a late cancellation marker being recorded. Past bookings cannot be cancelled.
- Has access only to their own clients, availability, and bookings, including late cancellation markers.

### Client

- Registers only through an invitation, with their name and email prefilled, sets their own password, and is linked to the trainer who invited them.
- Views their trainer's available times and books 60-minute sessions starting on the hour or half hour, within the trainer's availability and at least 6 hours in advance. Bookings are confirmed immediately without trainer approval.
- Views their own bookings and their status, including bookings created for them by the trainer and any late cancellation markers.
- Can cancel their own confirmed booking before the session starts. Cancellations less than 12 hours before the start require confirmation of a warning and are marked as "Late Cancellation"; cancellations at least 12 hours in advance are not.
- Cannot access other clients' bookings.
- Cannot log in or create bookings while their account is deactivated.

## Features

### Trainer

**1. Account Management**

- **1.1 The trainer can register independently.**
  - **Acceptance criteria:**
    - Registration does not require an invitation.
    - Registration requires a name, email address, and password. If any are missing, no account is created and the missing fields are identified.
    - The email address must have a valid format. Otherwise, no account is created and an error message is displayed.
    - If the email address already exists for another account or client record in the app, no account is created and an error message is displayed.
    - The password must meet the requirements in section 17. Otherwise, no account is created and the unmet requirements are displayed.
    - Submitting valid registration details creates an account with the "Trainer" role.

- **1.2 The trainer can log in to their account.**
  - **Acceptance criteria:**
    - Entering the correct email address and password logs the trainer in and displays their calendar.
    - Entering incorrect credentials leaves the trainer logged out and displays an error message.

- **1.3 The trainer can log out of their account.**
  - **Acceptance criteria:**
    - Logging out displays the login screen.
    - Accessing protected data and actions after logging out requires logging in again.

**2. Creating Clients and Sending Invitations**

- **2.1 The trainer can create a client record by entering the client's name and email address.**
  - **Acceptance criteria:**
    - Entering a name and a valid, unused email address creates a client record linked to the logged-in trainer.
    - If the name or email address is missing, no record is created and the missing fields are identified.
    - If the email address has an invalid format, no record is created and an error message is displayed.
    - If the email address already exists for another client or account in the app, no record is created and a message states that the email address already exists.

- **2.2 The trainer can send an existing client an email invitation to create an account.**
  - **Acceptance criteria:**
    - Invitations can be sent only while the client is active.
    - The invitation is sent to the email address in the client's record.
    - The email contains a link to create an account.
    - The invitation can be accepted for 3 days after it is sent. Once this period expires, it can no longer be used to create an account.
    - Opening an invalid or expired invitation displays a clear error message.
    - Sending an invitation does not activate the client's account.

- **2.3 The trainer can resend an invitation that has expired without being accepted.**
  - **Acceptance criteria:**
    - When an unaccepted invitation expires, the client's record shows that it was not accepted and provides an option to resend it if the client is active. Resending is not allowed while the client is deactivated.
    - The new invitation is sent to the client's current email address and is valid for 3 days after the new sending time.
    - The original expired invitation remains invalid.
    - Resending an invitation does not create another client record.

- **2.4 The trainer can edit their client's name and email address.**
  - **Acceptance criteria:**
    - The details can be edited both before and after the client creates an account.
    - An empty name or email address, or an email address with an invalid format, is not saved and an appropriate error is displayed.
    - If the new email address is used by another client or account, the change is not saved and a message states that the email address already exists. Keeping the client's own current email address is not treated as a duplicate.
    - After changing the email address of a client with an account, the new email is used to log in and the original email can no longer be used. The password remains unchanged.
    - Editing the details preserves the client's trainer assignment, bookings, and history.
    - If the client has not yet accepted their invitation, changing the email invalidates the original invitation and allows the trainer to send a new invitation to the updated address, valid for 3 days.

**3. Setting, Editing, and Removing Availability**

- **3.1 The trainer can set one or more availability ranges for a specific date.**
  - **Acceptance criteria:**
    - Entering a valid date, start time, and end time saves the availability and displays it in the trainer's calendar.
    - New availability cannot start in the past. Such a range is not saved and an error message is displayed.
    - Multiple ranges can be saved for the same date, for example 08:00–12:00 and 14:00–19:00.
    - The start and end must be on the hour or half hour. Other times are not saved and an error message is displayed.
    - The end must be later than the start. Otherwise, the range is not saved and an error message is displayed.
    - If the date, start time, or end time is missing, the availability is not saved and the missing fields are identified.
    - Availability ranges for the same trainer must not overlap. An overlapping range is not saved and an error message is displayed.
    - Adjacent ranges are allowed, for example 08:00–12:00 and 12:00–15:00.
    - The availability applies only to the selected date and does not recur automatically.
    - Times offered to clients follow sections 12 and 13.

- **3.2 The trainer can edit an existing availability range.**
  - **Acceptance criteria:**
    - The trainer can change the start or end of a future range. For a range already in progress, only its future portion can be changed; the original start must be preserved.
    - The edited range must meet the same time ordering, hour or half-hour boundary, and non-overlap rules as a new range; adjacent ranges remain allowed. Preserving the original start of an in-progress range is permitted even though it is in the past.
    - Changes to elapsed availability are rejected with an error message. Past availability remains visible in the calendar.
    - If a confirmed booking overlaps the portion of availability being removed, the change is not saved and the affected bookings are displayed.
    - A change affecting a session already in progress is not allowed. Future affected bookings must first be cancelled individually before the change can be saved.
    - Editing availability does not itself cancel or reschedule any booking.
    - Saving the change updates the trainer's calendar and the times available for clients to book.

- **3.3 The trainer can remove part of an availability range or the entire range.**
  - **Acceptance criteria:**
    - Availability is removed only if the removal does not affect elapsed time or overlap a confirmed booking.
    - Elapsed availability cannot be removed and remains visible in the calendar. For a range already in progress, only its future portion can be removed; the original start must be preserved.
    - Removing an internal portion can create two separate ranges. For example, removing 12:00–13:00 from 08:00–19:00 leaves 08:00–12:00 and 13:00–19:00.
    - The boundaries of the portion being removed must be on the hour or half hour.
    - If a confirmed booking overlaps the time being removed, the removal is prevented and the affected bookings are displayed.
    - Removal affecting a session already in progress is not allowed. Future affected bookings must first be cancelled individually before retrying the removal.
    - Removing availability does not itself cancel or reschedule any booking.
    - Clients can no longer book the removed time.

**4. Training Calendar**

- **4.1 The trainer can view their availability and confirmed bookings in a calendar.**
  - **Acceptance criteria:**
    - The calendar displays the trainer's saved availability ranges at the corresponding dates and times.
    - Confirmed bookings appear at the corresponding dates and times, including bookings created by the trainer outside configured availability.
    - Each booking displays the client's name and the session's start and end times.
    - Time occupied by a booking is distinguished from available time.
    - Cancelled bookings are not displayed in the calendar.
    - The trainer can navigate to another date and view both future and past sessions.
    - The calendar displays only the logged-in trainer's availability and bookings.
    - Dates and times are displayed in the `Europe/Bratislava` time zone.

- **4.2 The trainer can open a booking's details from the calendar.**
  - **Acceptance criteria:**
    - Selecting a booking displays its details.
    - The details include the client's name, date, session start and end times, and the status "Confirmed".
    - Before the session starts, the details provide an option to cancel the booking according to the rules in section 6.
    - Cancellation is unavailable when prohibited by section 18.2.
    - Returning from the booking details restores the calendar to the previously viewed date.

**5. Creating a Booking for a Client**

- **5.1 The trainer can create a confirmed training booking for their client.**
  - **Acceptance criteria:**
    - The trainer can select only their own active client who has accepted the invitation and created an account.
    - Creating a booking requires a client, date, and start time. If any are missing, no booking is created and the missing fields are identified.
    - The booking must satisfy sections 12, 13, and 18.2. Invalid dates or times are rejected with an appropriate error message.
    - If the selected time conflicts with a confirmed booking, including one created simultaneously, no booking is created and a message states that the time is no longer available.
    - Upon successful creation, the booking has the status "Confirmed" and appears in the trainer's calendar and the client's bookings.

**6. Cancelling Bookings**

- **6.1 The trainer can cancel their client's confirmed booking before the session starts.**
  - **Acceptance criteria:**
    - The trainer can cancel only a booking assigned to them, regardless of whether it was created by the trainer or the client.
    - Cancellation is allowed with any remaining notice, subject to section 18.2. A rejected cancellation leaves the booking unchanged.
    - Cancelling changes the status from "Confirmed" to "Cancelled" and removes the booking from the trainer's calendar.
    - Time is released according to section 14, and late cancellation marking follows section 15.1.
    - The booking remains accessible in both users' cancelled booking history, recording that the trainer cancelled it.
    - An already cancelled booking cannot be cancelled again.

**6A. Client Deactivation and Reactivation**

- **6A.1 The trainer can deactivate their client.**
  - **Acceptance criteria:**
    - The trainer can deactivate only a client assigned to them.
    - If the client has future confirmed bookings, deactivation is prevented and the affected bookings are displayed.
    - The trainer must first cancel these bookings individually and then retry deactivation.
    - Deactivation does not itself cancel any booking.
    - After deactivation, the client is marked as "Inactive".
    - A deactivated client cannot log in or create bookings. If already logged in, their further access to protected data and actions is blocked.
    - The trainer cannot create a new booking for a deactivated client.
    - The client record, client details, booking history, and late cancellation markers are preserved and remain accessible to the trainer.

- **6A.2 The trainer can reactivate their deactivated client.**
  - **Acceptance criteria:**
    - The trainer can reactivate only their own deactivated client.
    - After reactivation, the client is marked as "Active".
    - A client with an existing account can log in again using their current email address and existing password.
    - The client can book sessions again, and the trainer can create bookings for them, according to the usual booking rules.
    - The client's details, booking history, and late cancellation markers are preserved.
    - Previously cancelled bookings are not automatically restored.
    - Reactivation does not replace invitation acceptance: a client without an account must still create one through a valid invitation.
    - After reactivation, a client can use their original invitation if it is still valid. If it has expired, the trainer can send a new invitation. Reactivation does not extend invitation validity.

### Client

**7. Account Creation and Management**

- **7.1 An invited client can create an account through a valid email invitation.**
  - **Acceptance criteria:**
    - Creating a client account requires a valid invitation. Public client registration is not available.
    - A deactivated client cannot create an account, even through a valid invitation. The app displays a message that access has been deactivated and the client should contact their trainer.
    - Opening a valid invitation prefills the client's name and email using the details entered by the trainer.
    - The client sets their own password that meets the requirements in section 17.
    - If the password is missing or does not meet the requirements, no account is created and the unmet requirements are displayed.
    - Upon successful creation, the account is linked to the existing client record and the trainer who sent the invitation. No duplicate client record is created.
    - The invitation is marked as accepted and cannot be reused to create an account.
    - An invalid, expired, or already used invitation does not allow account creation and displays an appropriate error message.
    - The client is linked to only one trainer and cannot select a different trainer during account creation.

- **7.2 An active client with an existing account can log in.**
  - **Acceptance criteria:**
    - Entering the correct current email address and password logs the client in and displays the training booking screen.
    - Entering incorrect credentials leaves the client logged out and displays an error message.
    - A client who has not yet created an account through an invitation cannot log in.
    - A deactivated client cannot log in, even with correct credentials.
    - After logging in, the client has access only to their trainer's availability and their own bookings, including their own cancelled booking history.

- **7.3 The client can log out of their account.**
  - **Acceptance criteria:**
    - Logging out displays the login screen.
    - Accessing protected data and actions after logging out requires logging in again, including when opening a direct link.

**8. Viewing Available Times**

- **8.1 The client can view available times for booking a training session with their trainer on a selected date.**
  - **Acceptance criteria:**
    - The client can select a date and view available times only for their own trainer.
    - Only times eligible for client booking under sections 12 and 13 are offered; cancelled bookings are handled according to section 14.
    - Each available time displays the session's start and end times.
    - If no times are available for the selected date, the message "Na tento deň nie sú dostupné žiadne termíny" is displayed.
    - Dates and times follow the Time Zone constraints.
    - The client cannot see other clients' names or booking details, as required by section 16.

**9. Booking a Training Session**

- **9.1 The client can book an available training session with their trainer.**
  - **Acceptance criteria:**
    - Only a logged-in, active client with an existing account can create a booking, and only for themselves with their assigned trainer.
    - The client selects a date and an available session start time.
    - Eligibility under sections 12, 13, and 18.2 is checked at final confirmation, including after any warning. If eligibility is no longer satisfied, no booking is created and an appropriate error message is displayed.
    - If cancelling at the current time would qualify as late under section 15.1, the app warns before creation that a subsequent cancellation will be marked as "Late Cancellation" and is subject to a cancellation fee. Otherwise, this warning is not displayed.
    - After seeing the warning, the client can confirm the booking or return without creating it.
    - The warning condition is checked again at final confirmation. If it now applies and the client has not confirmed the warning, the app displays it and requires confirmation before creating the booking.
    - Without confirmation of a required warning, no booking is created.
    - If another booking takes the selected time, including during simultaneous attempts, the unsuccessful attempt displays a message that the time is no longer available.
    - Upon successful creation, the booking immediately has the status "Confirmed", without trainer approval, and appears in the client's bookings and the trainer's calendar.
    - Creating a booking does not itself add a "Late Cancellation" marker; marking follows section 15.1.

**10. Booking Overview**

- **10.1 The client can view their own confirmed bookings.**
  - **Acceptance criteria:**
    - The overview displays only the logged-in client's bookings.
    - Bookings created by both the client and the trainer are displayed.
    - Each booking displays the date, session start and end times, and the status "Confirmed".
    - The client can view both future and past sessions, with past session status governed by section 18.2.
    - Bookings are sorted chronologically: future sessions from the soonest and past sessions from the most recent.
    - Cancelled bookings are excluded from this overview and remain accessible in a separate cancelled booking history.
    - If the client has no bookings in the selected future or past session group, an appropriate message is displayed.
    - Dates and times are displayed in the `Europe/Bratislava` time zone.

- **10.2 The client can open the details of their own confirmed booking.**
  - **Acceptance criteria:**
    - Selecting a booking displays its details, including the date, session start and end times, and the status "Confirmed".
    - The client cannot view another client's booking details, including by opening a direct link.
    - Before the session starts, an option to cancel the booking is available according to the rules in section 11.
    - Cancellation is unavailable when prohibited by section 18.2.
    - Booking changes follow section 18.1; direct editing is not available.
    - The client can return from the details to the booking overview.

**11. Cancelling Bookings**

- **11.1 The client can cancel their own confirmed booking before the session starts.**
  - **Acceptance criteria:**
    - A logged-in, active client can cancel only their own booking, regardless of whether it was created by the client or the trainer.
    - Cancellation must satisfy section 18.2. A rejected cancellation leaves the booking unchanged.
    - If cancellation qualifies as late under section 15.1, the app warns that the booking will be marked as "Late Cancellation" and is subject to a cancellation fee.
    - After seeing the warning, the client can confirm cancellation or keep the booking. Without confirmation, the booking remains unchanged and continues to block the time.
    - The late cancellation condition is evaluated at final confirmation. If it now applies and the client has not confirmed the warning, the app first displays it and requires confirmation.
    - Upon successful cancellation, the status changes from "Confirmed" to "Cancelled". Late cancellation marking follows section 15.1.
    - The booking disappears from the trainer's calendar and the client's confirmed booking overview. Time is released according to section 14.
    - The booking remains accessible in both users' cancelled booking history, recording that the client cancelled it and including any late cancellation marker.
    - An already cancelled booking cannot be cancelled again.

### Shared Rules

**12. Session Duration and Availability**

- **12.1 The app enforces session duration and availability rules when accepting bookings.**
  - **Acceptance criteria:**
    - Every session lasts 60 minutes and starts on the hour or half hour. The end time is calculated from the selected start; other start times are rejected.
    - A client can book only a session fully covered by the trainer's availability. Adjacent ranges are continuous: 08:00–12:00 and 12:00–15:00 allow a session from 11:30 to 12:30.
    - Clients must book at least 6 hours in advance. Exactly 6 hours is allowed; less notice is rejected.
    - Trainers can book with less notice, outside availability, or on a date with no availability, subject to section 18.2.
    - Creating a booking does not change configured availability or make surrounding times available to clients.

**13. Blocking Time and Preventing Overlaps**

- **13.1 The app prevents confirmed bookings for the same trainer from overlapping.**
  - **Acceptance criteria:**
    - A confirmed booking immediately blocks the entire session time.
    - An overlapping booking is rejected regardless of who creates it.
    - Only one of simultaneous overlapping booking attempts succeeds.
    - Adjacent sessions, such as 10:00–11:00 and 11:00–12:00, are allowed.

**14. Releasing Time**

- **14.1 The app releases the occupied time when a booking is cancelled.**
  - **Acceptance criteria:**
    - A cancelled booking does not block time, even when marked as "Late Cancellation".
    - Clients can book the released time only if sections 12 and 13 are satisfied. Cancelling an out-of-availability booking does not make that time available to clients.
    - Cancellation does not change the trainer's availability and preserves the booking record in history.

**15. Recording Late Cancellations**

- **15.1 The app marks late cancellations made by clients.**
  - **Acceptance criteria:**
    - A marker is added when a client cancels a confirmed booking less than 12 hours before the session starts, subject to section 18.2, including bookings created by the trainer.
    - No marker is added when at least 12 hours remain, including exactly 12 hours, or when the trainer cancels.
    - The marker belongs to a specific booking and client.
    - The app does not record an amount, charge fees, or process payments.

- **15.2 The trainer can remove a "Late Cancellation" marker.**
  - **Acceptance criteria:**
    - Only the booking's trainer can remove the marker; the client cannot.
    - The change is reflected in both the trainer's and the client's views.
    - The booking remains cancelled and retained in history.

**16. Data Access**

- **16.1 The app restricts access according to the user's role and assignment.**
  - **Acceptance criteria:**
    - Trainers can access only their own clients, availability, bookings, and markers.
    - Clients can access only their trainer's availability and their own bookings, history, and markers.
    - Direct links to another user's restricted data and attempts to modify that data do not grant access.
    - Each client belongs to exactly one trainer.

**17. Password Requirements**

- **17.1 The app requires strong passwords for both trainers and clients.**
  - **Acceptance criteria:**
    - A password must contain at least 12 characters, one uppercase letter, one digit, and one special character.
    - A non-compliant password is rejected and the unmet requirements are displayed.
    - The rule applies to account creation and password recovery.

**18. Booking Changes and Past Sessions**

- **18.1 Changing a booking's time or assigned client requires cancellation and a new booking.**
  - **Acceptance criteria:**
    - Direct changes to the time or assigned client are not allowed.
    - Cancellation follows sections 6 or 11 according to who cancels, including marking under section 15.1.
    - The new booking must satisfy section 5 or 9 according to who creates it.

- **18.2 The app preserves past sessions without allowing retrospective booking or cancellation.**
  - **Acceptance criteria:**
    - A booking can be created or cancelled only while the session start is strictly in the future. Once that time is reached, creation and cancellation are rejected.
    - A session that has not been cancelled retains the status "Confirmed" after it ends and remains available to view.

**19. Password Recovery**

- **19.1 Trainers and clients can request a password reset link.**
  - **Acceptance criteria:**
    - The "Forgot Password" option allows the user to enter an email address.
    - For an existing account, a reset link is sent to its current login email address.
    - Password reset emails are supported despite the exclusion of booking notifications.

- **19.2 The user can set a new password through a valid reset link.**
  - **Acceptance criteria:**
    - The link is valid for 15 minutes from its creation.
    - The new password must meet the requirements in section 17.
    - The link expires after 15 minutes or is invalidated when a new password is successfully saved, whichever happens first.
    - Opening the link or unsuccessfully submitting a new password does not consume it; the original expiry period continues to run.
    - After successful recovery, the new password works, the old password no longer works, and the reset link cannot be reused.
    - An invalid, used, or expired link does not change the password. The app displays a message and an option to request a new link.
    - Password recovery does not reactivate a deactivated client.

**20. Cancelled Booking History**

- **20.1 The trainer can view their clients' cancelled booking history.**
  - **Acceptance criteria:**
    - The overview includes the client's name, original session date, start and end times, who cancelled the booking, and any late cancellation marker.
    - The record remains accessible even if the released time is booked again.
    - Cancelled bookings do not occupy time in the calendar.

- **20.2 The client can view their own cancelled booking history.**
  - **Acceptance criteria:**
    - The client sees only their own cancelled bookings, with the date, time, who cancelled them, and any late cancellation marker.
    - A marker removed by the trainer is also removed from this overview.
    - If the history is empty, a message states that the client has no cancelled bookings.

## Constraints

**1. Technologies**

- The app will be built using Ruby on Rails and a PostgreSQL database.

**2. Platform and PWA**

- The app will be accessible through a web browser.
- On supported devices and browsers, users will be able to add the app to their home screen and use it as a PWA.

**3. Supported Screens and Interaction**

- The interface will be designed primarily for mobile phones and touch interaction.
- The layout will adapt to different screen sizes, and the app will also be usable on tablets and computers.

**4. Internet Connection**

- Logging in, viewing current data, booking sessions, cancelling bookings, and managing clients and availability will require an internet connection.
- Offline booking and offline changes will not be supported.

**5. Interface Language**

- The user interface, including notifications, error messages, and invitation emails, will be in Slovak.

**6. Time Zone**

- Training session dates and times will be displayed and entered in the `Europe/Bratislava` time zone.
- The app will account for transitions between daylight saving time and standard time.
- Booking and cancellation time limits will be evaluated using the actual time remaining before the session starts.

## Out of Scope

- **Payments and Billing:** The app will not process payments, issue invoices, or record fee amounts or fee payments. For late cancellations, it will store only the "Late Cancellation" marker.
- **Chat:** Communication between trainers and clients will take place outside the app.
- **Public Client Registration and Trainer Search:** Clients will gain access only through an invitation from a specific trainer. Public client registration and searching for trainers will not be supported.
- **Booking Approval:** Bookings will be confirmed immediately without trainer approval.
- **Training Plans and Statistics:** The app will not record exercises, performance, body measurements, or client progress.
- **Separate Analytics Dashboards:** Revenue, attendance, and performance analytics will not be included. The calendar and booking overview remain part of the app.
- **Pair and Group Bookings:** Each booking will be assigned to one client. A note indicating that a second person will attend will not be supported.
- **Separate Native iOS and Android Apps:** The first version will be a web app with PWA support.
- **Offline Changes:** Creating or cancelling bookings and editing data without an internet connection will not be supported.
- **Multilingual Interface:** The first version will be available only in Slovak.
- **Booking Notifications and Reminders:** The MVP will not provide booking notifications or training reminders, whether in-app, by email, or through push notifications. Invitation and password reset emails, as well as warnings displayed during booking and cancellation, remain in scope.
- **Recurring Availability:** Availability can be configured for individual dates only.
- **Editing Existing Bookings:** Changing the time or assigned client requires cancelling the booking and creating a new one.

## Open Questions

There are currently no unresolved questions recorded in this specification.
