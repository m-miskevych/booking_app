# Booking App for a Personal Trainer

## What Problem I Am Solving

A personal trainer and their clients need a simple way to arrange training sessions without repeated messages about availability, confirmations, and cancellations.

## Who My Users Are

- trainers who manage their clients, availability, bookings, and calendar,
- invited clients who book sessions with their assigned trainer and view their bookings.

## How I Want to Solve the Problem

Create a mobile-first web app with PWA support. Trainers set their availability, and clients book sessions that are confirmed immediately.

## MVP - Minimum Viable Product

- Independent trainer registration and invitation-only client accounts, with each client linked to one trainer.
- Client records with a name and unique email, editable by the trainer; invitations valid for 3 days and resendable after expiry.
- Client deactivation and reactivation, preserving history; future bookings must first be cancelled individually.
- Strong passwords for both roles and password recovery by email.
- Availability ranges for individual dates, with multiple ranges per day and boundaries on the hour or half hour.
- Availability editing and removal, requiring affected bookings to be cancelled individually first.
- Immediately confirmed 60-minute sessions starting on the hour or half hour, with no overlapping bookings for a trainer.
- Clients book within availability at least 6 hours in advance; trainers can book for active clients with completed accounts outside availability and with less notice.
- Trainer calendar, client booking overview, and separate cancelled booking history.
- Cancellation before the session starts. Client cancellations under 12 hours receive a “Late Cancellation” marker after a warning; trainer cancellations do not. Trainers can remove the marker.
- A warning before clients book sessions starting in less than 12 hours.
- Booking changes require cancellation and a new booking. Past bookings cannot be created or cancelled; finished sessions remain “Confirmed”.
- Separate access to each trainer's data and each client's own bookings.
- A Slovak interface, Europe/Bratislava time zone, and online operation.

Detailed rules are defined in SPEC.MD.

## What Will Not Be in the MVP and Why

- Payments, billing, fee amounts, or payment tracking: only the late cancellation marker is recorded.
- Chat: communication stays outside the app.
- Training plans, statistics, or analytics dashboards: the focus is booking.
- Public client registration or trainer search: access is by invitation only.
- Booking approval, pair/group bookings, or a second-attendee note: bookings are immediate and assigned to one client.
- Booking notifications and reminders: invitation and password reset emails, plus booking and cancellation warnings, remain included.
- Recurring availability, direct booking edits, offline changes, multilingual support, or separate native mobile apps: excluded to keep the first version focused.

## What Technologies I Need

- Ruby on Rails for the backend and web interface.
- PostgreSQL for users, availability, and bookings.
- Authentication, email delivery, and hosting with HTTPS.
- Responsive layouts and PWA support for supported browsers and devices.

## INITIATIVE

**Make planning and booking personal training sessions easier.**

Help trainers and their clients manage sessions in one mobile-first web app, reducing the time spent arranging them through messages.

## EPICS AND USER STORIES

### EPIC 1: Accounts, Invitations, and Login

Support independent trainer registration and invitation-only client access.

- **US1.1:** As a trainer, I want to register so that I can manage clients and sessions.
- **US1.2:** As a trainer, I want to create client records and send or resend invitations so that clients can join.
- **US1.3:** As an invited client, I want my name and email prefilled and to set a strong password so that I can activate my account.
- **US1.4:** As a client, I want to be linked to the inviting trainer so that I can book their sessions.
- **US1.5:** As a trainer or client, I want to log in, log out, and reset my password so that I can manage access to my account.
- **US1.6:** As a trainer, I want to edit client details and deactivate or reactivate clients so that I can maintain my client list.

### EPIC 2: Training Availability

Allow trainers to manage availability for individual dates and clients to view bookable times.

- **US2.1:** As a trainer, I want to set one or more availability ranges per date so that clients can choose suitable times.
- **US2.2:** As a client, I want to view available session times so that I can choose when to train.
- **US2.3:** As a trainer, I want to edit or remove availability after cancelling affected bookings so that I can respond to schedule changes.

### EPIC 3: Booking Training Sessions

Create immediately confirmed bookings without trainer approval.

- **US3.1:** As a client, I want to book an available 60-minute session at least 6 hours ahead so that my training is confirmed immediately.
- **US3.2:** As a client, I want to choose a start on the hour or half hour so that I have flexible timing.
- **US3.3:** As a trainer or client, I want confirmed bookings to block overlapping sessions so that the trainer cannot be double-booked.
- **US3.4:** As a trainer, I want to book for an active client with a completed account, including outside availability or with less than 6 hours' notice, so that I can record sessions arranged directly.
- **US3.5:** As a client, I want a warning when booking less than 12 hours ahead so that I understand the late cancellation rule.

### EPIC 4: Training Schedule and Booking Overview

Provide a clear overview while keeping each user's data separate.

- **US4.1:** As a client, I want to view my bookings, including those created by my trainer, so that I know when I have training.
- **US4.2:** As a trainer, I want a calendar of availability and confirmed bookings so that I can plan my day.
- **US4.3:** As a trainer or client, I want access limited to my permitted data so that other users' records stay private.
- **US4.4:** As a trainer or client, I want a separate cancelled booking history with cancellation details and late cancellation markers so that I can review past cancellations.

### EPIC 5: Booking Cancellation

Allow cancellation before the session starts and record late client cancellations.

- **US5.1:** As a client, I want to cancel before the session starts, with a warning and late cancellation marker when less than 12 hours remain, so that I can release a session I cannot attend.
- **US5.2:** As a trainer, I want to cancel before the session starts without a late cancellation marker so that I can respond to changes.
- **US5.3:** As a client, I want released times to become bookable when availability and the 6-hour notice rule allow so that I can use newly available times.
- **US5.4:** As a trainer, I want to remove a late cancellation marker without restoring the booking so that I can waive it when appropriate.
