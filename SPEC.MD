## Problem

A personal trainer and their clients arrange training sessions through messages. This approach requires repeated communication about available time slots, session confirmations, and cancellations.

## Users and Roles

### Trainer

- Registers independently, creates client records with a name and email address, and sends email invitations.
- Can edit their clients' names and email addresses, including after account creation.
- Can deactivate and reactivate clients. Future bookings must first be cancelled individually before deactivation.
- Sets, edits, and removes their availability. Bookings affected by a reduction in availability must first be cancelled individually.
- Creates immediately confirmed 60-minute bookings for clients who have accepted their invitation and created an account, including outside configured availability and less than 6 hours before a session starts. Sessions start on the hour or half hour and must not overlap other confirmed bookings. Past bookings cannot be created.
- Views their availability and their clients' bookings in a calendar, including booking details and late cancellation markers.
- Views cancelled bookings in a separate history and can remove late cancellation markers.
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

- **1.1** The trainer can register independently.
- **1.2** The trainer can log in and log out.

**2. Creating Clients and Sending Invitations**

- **2.1** The trainer can create a client record by entering the client's name and email address.
- **2.2** The trainer can send the client an email invitation to create an account.
- **2.3** An invitation is valid for 3 days after it is sent. An expired invitation cannot be used to create an account.
- **2.4** If the invitation expires without being accepted, the client's record shows that it was not accepted, and the trainer can resend it. The resent invitation is valid for a new 3-day period.
- **2.5** If the email address already exists in the app, client creation is prevented and an error message explains that the email address already exists.
- **2.6** The trainer can edit the client's name and email address, including after the client has created an account.
- **2.7** After account creation, changing the client's email also changes their login email. Their password, bookings, and booking history remain unchanged.
- **2.8** If the new email address is already used by another account or client record in the app, the change is prevented and an error message is displayed.
- **2.9** Before the invitation is accepted, changing the client's email invalidates the previous invitation. The trainer can then send a new invitation to the updated address, valid for 3 days.

**3. Setting, Editing, and Removing Availability**

- **3.1** The trainer can set the start and end of their availability for a specific date, for example from 08:00 to 19:00.
- **3.2** The trainer can edit an availability range, remove part of it, or remove the entire range.
- **3.3** If a confirmed booking overlaps the portion of availability being removed, the app prevents the change and displays the affected bookings.
- **3.4** The trainer must first cancel the affected bookings individually. They can then edit or remove the availability.
- **3.5** Editing or removing availability does not automatically cancel or reschedule bookings.
- **3.6** The trainer can set multiple availability ranges for the same date, for example 08:00–12:00 and 14:00–19:00.
- **3.7** Availability is set for individual dates only; recurring availability is not supported in the MVP.
- **3.8** Availability ranges start and end on the hour or half hour.

**4. Training Calendar**

- **4.1** The trainer can view their availability and their clients' confirmed bookings in a calendar. Cancelled bookings are excluded from the calendar and remain accessible in a separate history.
- **4.2** For each booking, the trainer can view the client's name, date, time, and booking status.
- **4.3** For a cancelled booking, the trainer can see whether it is marked as "Late Cancellation" and which client it belongs to.

**5. Creating a Booking for a Client**

- **5.1** The trainer can create a booking for one of their clients by specifying the training date and time, but only after the client has accepted their invitation and created an account, and only while the client is active.
- **5.2** The trainer can create a booking less than 6 hours before the session starts.
- **5.3** The trainer can create a booking outside their configured availability. Such a booking does not make surrounding times available for clients to book.
- **5.4** The session lasts 60 minutes and can start on the hour or half hour.
- **5.5** The booking must not overlap another confirmed booking for the trainer.
- **5.6** The booking is confirmed immediately upon creation and appears in the trainer's calendar and the client's bookings.
- **5.7** The trainer cannot create a booking retrospectively for a session that has already started.

**6. Cancelling Bookings**

- **6.1** The trainer can cancel a confirmed booking before the session starts, regardless of how much time remains. Its status changes to "Cancelled". The trainer cannot cancel a booking retrospectively once the session has started.
- **6.2** A booking cancelled by the trainer is not marked as "Late Cancellation".

**6A. Client Deactivation and Reactivation**

- **6A.1** The trainer can deactivate a client instead of deleting their record.
- **6A.2** A deactivated client cannot log in or create bookings. The trainer cannot create new bookings for that client either.
- **6A.3** If the client has future confirmed bookings, the app prevents deactivation and displays the affected bookings. The trainer must first cancel them individually; deactivation does not automatically cancel bookings.
- **6A.4** Deactivation preserves the client's booking history and late cancellation markers.
- **6A.5** The trainer can reactivate the client, restoring their access and eligibility for new bookings. Previously cancelled bookings are not restored.

### Client

**7. Account Creation and Management**

- **7.1** The client can create an account only through an email invitation from a trainer.
- **7.2** When creating an account through the invitation, the client's name and email are prefilled using the details entered by the trainer.
- **7.3** The client sets their own password when creating the account.
- **7.4** The client is automatically linked to the trainer who invited them.
- **7.5** The client can log in and log out.

**8. Viewing Available Times**

- **8.1** The client can view available times for booking with their trainer.
- **8.2** A time is available for booking only if the entire 60-minute session fits within the trainer's availability, does not overlap another confirmed booking, and starts at least 6 hours from the time of booking.

**9. Booking a Training Session**

- **9.1** The client can book an available 60-minute training session.
- **9.2** The session can start on the hour or half hour, for example at 08:00 or 08:30.
- **9.3** The client can create a booking at least 6 hours before the session starts. Booking is still allowed when exactly 6 hours remain.
- **9.4** A successfully created booking is immediately confirmed and has the status "Confirmed". Trainer approval is not required.
- **9.5** When a client attempts to book a session starting in less than 12 hours, the app warns them before creation that cancelling the booking will be marked as "Late Cancellation" and is subject to a cancellation fee. The client can confirm the booking or return without creating it. This warning is not shown when at least 12 hours remain.

**10. Booking Overview**

- **10.1** The client can view their own bookings, including bookings created for them by the trainer.
- **10.2** For each booking, the client can view the date, time, and status: "Confirmed" or "Cancelled".
- **10.3** For a cancelled booking, the client can see whether it is marked as "Late Cancellation".

**11. Cancelling Bookings**

- **11.1** The client can cancel their confirmed booking before the session starts, including a booking created by the trainer.
- **11.2** If at least 12 hours remain before the session starts, the client can cancel without the booking being marked as "Late Cancellation".
- **11.3** If less than 12 hours remain before the session starts, the app warns the client that the cancellation will be marked as "Late Cancellation" and is subject to a cancellation fee.
- **11.4** After seeing the warning, the client can confirm the cancellation or keep the booking.
- **11.5** Upon confirmation, the booking status changes to "Cancelled". If less than 12 hours remain before the session starts, it is also marked as "Late Cancellation".

### Shared Rules

**12. Session Duration and Availability**

- **12.1** Every training session lasts 60 minutes and starts on the hour or half hour.
- **12.2** For a booking created by a client, the entire session must fit within the trainer's configured availability.
- **12.3** Bookings created by the trainer are exempt from the configured availability requirement and the minimum 6-hour booking notice.

**13. Blocking Time and Preventing Overlaps**

- **13.1** A confirmed booking immediately blocks the time from the start to the end of the session.
- **13.2** Confirmed bookings for the same trainer must not overlap, regardless of whether they were created by the trainer or a client.
- **13.3** If bookings for overlapping times for the same trainer are created simultaneously, the app allows only one of them.

**14. Releasing Time**

- **14.1** A cancelled booking does not block the trainer's time, even if it is marked as "Late Cancellation".
- **14.2** The time becomes available for clients to book only if it is within the trainer's availability, does not overlap another confirmed booking, and the session starts at least 6 hours from the time of booking.
- **14.3** Cancelling a booking created outside the trainer's availability does not automatically make that time available to clients.
- **14.4** The cancelled booking record and any "Late Cancellation" marker are retained after the time is released.

**15. Recording Late Cancellations**

- **15.1** A booking is marked as "Late Cancellation" only when the client cancels a confirmed booking less than 12 hours before the session starts.
- **15.2** This rule also applies to bookings created for the client by the trainer.
- **15.3** The marker is associated with the specific cancelled booking and client.
- **15.4** The app records only the late cancellation marker. It does not record a monetary amount, charge or collect fees, or process payments.
- **15.5** The trainer can remove a "Late Cancellation" marker from a cancelled booking. The booking remains cancelled and retained in history; removing the marker does not restore it.
- **15.6** Removing a marker is reflected in both the trainer's and the client's views. The client cannot remove the marker themselves.

**16. Data Access**

- **16.1** The trainer has access only to their own clients, availability, and bookings, including late cancellation markers.
- **16.2** The client has access only to their trainer's availability and their own bookings, including their own late cancellation markers.
- **16.3** The client cannot access other clients' bookings.
- **16.4** Each client is linked to exactly one trainer and cannot belong to multiple trainers.

**17. Password Requirements**

- **17.1** Both trainers and clients must use a password containing at least 12 characters, at least one uppercase letter, one digit, and one special character, such as `!`, `?`, or `#`.
- **17.2** The app rejects passwords that do not meet these requirements and displays which requirements have not been met.

**18. Booking Changes and Past Sessions**

- **18.1** The time or assigned client of an existing booking cannot be edited. To make a change, the booking must be cancelled and a new one created.
- **18.2** The existing cancellation rules apply when a booking is cancelled in order to create a replacement, including the late cancellation rule when the client performs the cancellation.
- **18.3** After a session ends, a booking that has not been cancelled retains the status "Confirmed". No "Completed" status is introduced.

**19. Password Recovery**

- **19.1** Trainers and clients can select "Forgot Password" and enter their email address to request a password reset link by email.
- **19.2** The reset link allows the user to set a new password that must meet the password requirements in feature 17.
- **19.3** Password reset emails are part of account management and remain in scope despite the exclusion of booking notifications from the MVP.

**20. Cancelled Booking History**

- **20.1** Cancelled bookings are removed from the calendar and do not occupy available time. Their records remain accessible in a separate cancelled booking history.
- **20.2** The trainer can view cancelled bookings for their clients, including the client's name, original session date and time, who cancelled the booking, and any "Late Cancellation" marker.
- **20.3** The client can view only their own cancelled bookings, including the same booking details and any "Late Cancellation" marker.
- **20.4** Cancelled booking records remain accessible even if another client books the released time.

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
