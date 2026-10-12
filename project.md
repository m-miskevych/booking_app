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
