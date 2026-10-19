# Booking App for a Personal Trainer

## What Problem I Am Solving

A personal trainer and their clients need a simple way to plan, book, and manage training sessions without spending too much time sending messages.

## Who My Users Are

The main users of the app are:

- a personal trainer who creates available time slots, manages bookings, and views their schedule,
- the trainer's clients who want to easily choose an available time slot, book a training session, and view their training sessions.

## How I Want to Solve the Problem

I want to create a mobile booking app for iPhone and Android where a trainer sets their availability and clients can book available training time slots.

## MVP - Minimum Viable Product

In the first version of the app, I want to include these features:

- trainers can sign up and log in on their own; clients can sign up only through an email invitation from a trainer, are automatically linked to that trainer, and then log in to their own account,
- support for multiple trainers; each trainer manages only their own invited clients, available time slots, and bookings,
- trainers can create available time slots,
- clients can view available time slots,
- clients can book a training session,
- clients can view their booked training sessions,
- trainers can view their bookings and schedule,
- clients can cancel a booking at least 12 hours before the training session starts; trainers can cancel a booking at any time,
- a training session lasts 60 minutes,
- the trainer must approve each booking; until it is approved, its status is pending and the time slot is blocked from other bookings,
- clients set their own password after accepting the email invitation,
- each time slot can have one booking; for a session for two people, one client makes the booking and adds a note saying that two people will attend.

## What Will Not Be in the MVP and Why

The MVP will not include:

- online payments, because the app will be used to book training sessions and payments will be handled outside the app; I do not plan to add them in future versions either,
- a chat between the trainer and the client, because they will communicate outside the app; I do not plan to add it in future versions either,
- training statistics for clients, because the main goal of the MVP is to handle bookings,
- a personal dashboard for clients and trainers, because the main goal is to handle bookings,
- public sign-up for clients or the option for clients to search for and choose a trainer, because clients get access only through an email invitation from a specific trainer and are automatically linked to that trainer.

## What Technologies I Need

To build the app, I will need:

- a mobile framework for both iOS and Android, such as Flutter,
- a backend for login, bookings, and working with data,
- a database to store users, time slots, and bookings,
- authentication for secure login,
- cloud hosting or a backend service, such as Firebase or Supabase,
- a design tool, such as Figma, to design the app screens.

## INITIATIVE

**Make planning and booking personal training sessions easier.**

Help personal trainers and their clients manage training sessions in one mobile app for iPhone and Android, reducing the time spent arranging sessions through messages.

## EPICS AND USER STORIES

### EPIC 1: Accounts, Invitations, and Login

Allow trainers to register independently and clients to join only through an invitation from their trainer.

- **US1.1:** As a trainer, I want to create an account so that I can manage my clients and training sessions.
- **US1.2:** As a trainer, I want to invite a client by email so that they can book my available training sessions.
- **US1.3:** As an invited client, I want to create my account and set my own password through the invitation so that I can access the app.
- **US1.4:** As an invited client, I want my account to be automatically linked to the trainer who invited me so that I can book sessions with that trainer.
- **US1.5:** As a trainer or client, I want to log in to my account so that I can access my bookings and schedule.

### EPIC 2: Available Training Time Slots

Allow trainers to offer available time slots and their clients to view them.

- **US2.1:** As a trainer, I want to create available 60-minute time slots so that my clients can book training sessions.
- **US2.2:** As a client, I want to view my trainer's available time slots so that I can choose a suitable training time.

### EPIC 3: Booking and Approval

Allow clients to request training sessions and trainers to approve or reject them.

- **US3.1:** As a client, I want to book an available time slot so that I can request a training session with my trainer.
- **US3.2:** As a client, I want to add a note that two people will attend so that my trainer knows the session is for a pair.
- **US3.3:** As a client, I want my requested time slot to be held while the booking is pending so that another client cannot book the same slot.
- **US3.4:** As a trainer, I want to approve or reject a pending booking so that I can confirm whether the session will take place.
- **US3.5:** As a client, I want to see the status of my booking so that I know whether it is pending, approved, or rejected.
- **US3.6:** As a trainer, I want a rejected time slot to become available again so that another client can book it.

### EPIC 4: Training Schedule and Booking Overview

Give trainers and clients a clear overview of their training sessions.

- **US4.1:** As a client, I want to view my booked training sessions so that I know when I have training.
- **US4.2:** As a trainer, I want to view my schedule and my clients' bookings so that I can plan my working day.
- **US4.3:** As a trainer, I want access to only my own clients, time slots, and bookings so that I can manage my work separately from other trainers.

### EPIC 5: Booking Cancellation

Allow trainers and clients to cancel bookings according to the cancellation rules.

- **US5.1:** As a client, I want to cancel a booking at least 12 hours before the session starts so that I can release the time slot if I cannot attend.
- **US5.2:** As a trainer, I want to cancel a booking at any time so that I can respond to changes in my availability.
- **US5.3:** As a trainer, I want a cancelled time slot to become available again so that a client can book it.
