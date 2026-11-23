# Booking App Technical Design

## 1. Document Purpose

This document describes **how the app will be implemented**. Product requirements and acceptance criteria are defined in `SPEC.MD`; the overall intent is described in `project.md`. If there is a conflict, `SPEC.MD` takes precedence.

The document defines the architecture, technologies, user flows, screens, data model, routes, and verification approach. Unresolved choices are collected in section 15, "Open Technical Decisions", and are not binding implementation rules.

The technical design respects the specification's rules and MVP scope.

## 2. Application Overview

- A web app with a Ruby on Rails backend and a PostgreSQL database.
- An interface designed primarily for mobile phones, also usable on tablets and computers.
- PWA support on compatible devices and browsers.
- User interface and emails in Slovak.
- Session dates and times entered and displayed in the `Europe/Bratislava` time zone.
- An internet connection is required for current data and making changes.
- Two roles: trainer and client. Each client belongs to one trainer.

**Architecture:** a single Rails project providing server-rendered screens, authentication, authorization, and booking logic. The interface uses HAML templates, Turbo, and Stimulus. The MVP will not have a separate frontend project or a public JSON API.

```text
Mobile or desktop browser
                 ↓ HTTPS
Rails: screens, login, authorization, and rules
                 ↓
            PostgreSQL

Rails → email service → invitations and password recovery
```

## 3. Technologies

| Area | Technology | Purpose |
|---|---|---|
| Backend | Ruby on Rails | Server-rendered interface and application logic |
| Database | PostgreSQL | Data storage and database constraints |
| Mobile access | Responsive web and PWA | Browser use and adding the app to the home screen |
| Screen interactions | Stimulus | Small user interface interactions |
| Testing | RSpec, `rspec-rails`, and Capybara | Automated tests in `spec/` |
| Language and framework | Ruby 3.4.x, Rails 8.1.x | Language and framework versions |
| Database version | PostgreSQL 17.x | Database system version |
| Templates | HAML, `haml-rails` | Server-rendered HTML in `.html.haml` files |
| Navigation and partial updates | Turbo through Hotwire | Navigation and updates to parts of screens |
| Styling | Tailwind CSS, `tailwindcss-rails` | Responsive layouts and HAML template styling |
| Login and password recovery | `devise` | Email and password login, logout, password hashing, and password recovery |
| Invitations | `devise_invitable` | Invitation mechanism |
| Authorization | `pundit` | Checks based on role and ownership |
| Emails | Action Mailer and SMTP | Email composition and delivery |
| Local development emails | `letter_opener` | Viewing emails in the browser without actually sending them |
| Background jobs | Active Job and Solid Queue | Sending emails in the background |
| Web server | Puma | Running Rails |
| JavaScript loading | `importmap-rails` | Loading Turbo, Stimulus, and other JavaScript modules |
| Static assets | Propshaft | Managing and publishing CSS, JavaScript, and images |
| Test data | `factory_bot_rails` | Creating users, availability, and bookings in tests |
| Ruby code style | RuboCop with Rails rules | Automated checks for consistent code |
| HAML and RSpec checks | `rubocop-haml`, `rubocop-rspec` | Checking templates and tests |
| Code security checks | Brakeman | Finding potential security issues in the Rails app |
| Dependency checks | `bundler-audit` | Checking gems for known vulnerabilities |
| Local configuration | `dotenv-rails` | Loading local environment variables |
| Development startup | Foreman through `bin/dev` | Starting the server, CSS watcher, and worker together |
| Automated checks | GitHub Actions | Running tests and checks after changes are submitted |

Exact compatible dependency versions will be locked in `Gemfile.lock`, and the Ruby version in `.ruby-version`.

### 3.1 Stimulus and Turbo

Stimulus will handle small interactions, such as:

- opening and closing confirmation dialogs,
- showing and hiding passwords,
- displaying password requirements as the user types,
- displaying the session end time when the start time changes.

Turbo will handle navigation, form submissions, and updates to parts of screens after a server response.

Interface checks are only supplementary. Rails always verifies authorization, time limits, overlaps, and the resulting state on the server.

### 3.2 JavaScript Loading and CSS Build

Turbo and Stimulus will be loaded through Importmap. JavaScript dependencies will be stored locally in the project, with configuration in `config/importmap.rb`.

Tailwind CSS will be built using `tailwindcss-rails`. This setup does not require Node.js or Yarn for normal application development. Propshaft manages static assets.

## 4. Design Principles

- Design the interface primarily for mobile phones, then adapt it to tablets and computers.
- Check authorization on every protected request.
- Implement shared rules in one place and use them for both roles.
- Enforce uniqueness and non-overlap through database constraints as well.
- Preserve history: cancel bookings by changing their status and deactivate clients.
- Keep controllers small; move more complex operations into services and check authorization through Pundit policies.
- Separate client activity from account completion through an invitation.
- Do not add payments, chat, booking approval, or notifications outside the specification's scope.

## 5. Authentication, Invitations, and Authorization

### 5.1 Required Behavior

Behavior is based on sections 1, 2, 6A, 7, 16, 17, and 19 of `SPEC.MD`:

- The trainer registers independently with a name, email, and password.
- The trainer creates a client record and sends an invitation separately.
- The client sets a password through a valid invitation; public client registration is not available.
- An invitation is valid for 3 days. The trainer can resend an expired, unaccepted invitation.
- Deactivation blocks login, access through an existing login session, invitation acceptance, and booking creation.
- Invitations cannot be sent to a deactivated client. Reactivation does not extend invitation validity.
- Changing a client's email changes the login email; before invitation acceptance, it invalidates the original invitation.
- Passwords for both roles must meet the requirements in section 17 of the specification.
- A password reset link is valid for 15 minutes and is consumed only after a new password is successfully saved.
- Password recovery does not reactivate a deactivated client.

### 5.2 Technical Implementation

- Devise handles email and password login, logout, secure password hashing, and password recovery with a 15-minute reset link validity period. The app adds strong password and client activity checks.
- Login uses Rails sessions through secure cookies.
- Devise Invitable handles invitations, and Pundit handles authorization checks.
- Public registration assigns the trainer role on the server. The role and trainer assignment must not be accepted from form input without verification.
- Pundit policies and scoped database queries ensure access only to permitted data.
- Every protected request checks the client's current activity status.
- Password recovery must not bypass invitation acceptance for an incomplete client account.
- Tokens are not stored in plaintext or written to logs.
- Changing a client's email invalidates unused password reset links sent to the previous address.

### 5.3 Protecting Login and Email Operations

The app limits repeated requests for login, password reset links, and sending or resending invitations.

Limits are evaluated by operation, IP address, and, where appropriate, account or target email. Invitation rate limits do not replace invitation validity checks.

When a limit is exceeded, the server returns `429 Too Many Requests` and displays a Slovak message explaining when the user can retry.

The Rails `rate_limit` mechanism will be used. Production counters must be shared across all web processes; separate memory within each process is insufficient. Specific limits and shared storage are listed under open technical decisions.

Password recovery requests display the same generic confirmation whether or not the email exists. An email is sent only for an eligible existing account. This rule does not change email uniqueness checks during registration or client management.

## 6. Main User Flows

This section describes the app's main user flows. `SPEC.MD` defines the rules for each operation; section 7 defines the screens and their content.

### 6.1 Trainer Registration

1. On the login screen, the trainer selects "Registrovať sa ako tréner".
2. They enter their name, email, and password. Password strength requirements are visible next to the password field.
3. They press "Vytvoriť účet".
4. The server validates the details. On failure, the form remains open and displays specific errors.
5. Successful registration creates the account, automatically logs the trainer in, and opens their calendar.

### 6.2 Inviting a Client

1. The trainer opens "Klienti" → "Pridať klienta".
2. They enter the client's name and email and press "Vytvoriť klienta".
3. The server validates the details. On failure, the form remains open with an explanation.
4. After creation, the client detail opens with the "Poslať pozvánku" option.
5. After sending, the status "Pozvánka odoslaná" and its expiry date are displayed.
6. The client opens the valid link, sees their prefilled name and email, and sets a password.
7. After successful account completion, the client is automatically logged in and the available-times calendar opens. The existing client record is completed without duplication.
8. The trainer can see that the client's account has been created. An expired, unaccepted invitation provides the "Poslať pozvánku znova" option.

Invitation validity and client activity checks from section 5 and `SPEC.MD` apply throughout this flow.

### 6.3 Client Booking

1. The client opens the available-times calendar and selects a date.
2. Available times are displayed with the session start and end, for example 10:30–11:30.
3. Selecting a time displays a summary with the trainer, date, and time.
4. If the session starts in less than 12 hours, a warning about the late cancellation rule is displayed.
5. The client presses "Potvrdiť rezerváciu". The server checks availability, time limits, and any required warning confirmation again, according to section 9.1 of `SPEC.MD`.
6. On success, the booking details open with the message "Rezervácia bola potvrdená".
7. If someone else has taken the time in the meantime, no booking is created. The client sees an explanation and updated available times for the selected date.

Selecting a time does not itself block it for others.

### 6.4 Trainer Booking

1. Select the trainer's own active client with a completed account.
2. Enter the date and start time.
3. Verify that the start is in the future and there are no conflicts.
4. Create the booking; configured availability and the 6-hour notice requirement do not apply to the trainer.

### 6.5 Booking Cancellation

1. Open the booking details and select cancellation.
2. For the client, display and confirm a late cancellation warning where required.
3. Check current conditions on the server.
4. Change the booking status to cancelled, release the time, and preserve history.

### 6.6 Availability Changes and Deactivation

If future bookings are affected, the action is rejected and the affected bookings are listed. The trainer cancels them individually and retries the original action. Sessions already in progress cannot be cancelled.

## 7. Navigation and Screens

### 7.1 Shared Guidelines

- The interface is designed primarily for mobile phones and adapts to tablets and computers.
- Forms and details may be separate screens or dialogs. This section defines their content and available actions.
- Logout is available in the user menu.
- Every screen defines its empty state, error messages, and behavior when the internet connection is lost.

### 7.2 Trainer Screens

**Registration**

- Name, email, and password with password strength requirements.
- Successful registration automatically logs the trainer in and opens the calendar.

**Login**

- Email, password, and the "Zabudnuté heslo" link.
- Login opens the calendar.

**Calendar**

- Switching between day and week views; each column represents one day.
- Navigation between dates.
- Availability and confirmed bookings, including the client's name and session time.
- Options to add, edit, and remove availability.
- Options to create a booking for a client and open an existing booking's details.
- Cancelled bookings are not displayed in the calendar.

**Trainer Calendar Layout:**

- Screen title and "Deň / Týždeň" switch at the top.
- Below them, the date, navigation arrows, and the "Dnes" button.
- A time axis with hours on the left; each column represents one day.
- Availability displayed as shaded ranges.
- Bookings displayed as blocks with the client's name and session time.
- The "Pridať" button offers "Dostupnosť" and "Rezervácia".
- Mobile defaults to day view, while desktop defaults to week view. Switching is available on both.

**Client List**

- A list of the trainer's own clients and the "Pridať klienta" button.
- Selecting a client opens their details.

**Client Details**

- Name, email, client activity, and invitation or account completion status.
- Editing the name and email.
- Sending or resending an invitation according to the rules.
- Client deactivation and reactivation.
- Bookings divided into:
  - **Naplánované:** confirmed sessions, starting with the soonest.
  - **Minulé:** confirmed sessions, starting with the most recent.
  - **Zrušené:** cancellation history, including the original session time, who cancelled, and any "Neskoré zrušenie" marker.
- Options to open booking details and remove a late cancellation marker from a cancelled booking.

**Forms and Details**

- Adding and editing a client.
- Adding, editing, and removing availability.
- Creating a booking for a client.
- Booking details with cancellation available according to the rules.

### 7.3 Client Screens

**Account Creation Through an Invitation**

- Prefilled name and email.
- Password setup with password strength requirements.
- Successful account completion automatically logs the client in and opens the available-times calendar.

**Login**

- Email, password, and the "Zabudnuté heslo" link.
- Login opens the available-times calendar.

**Calendar**

- Date selection and available times for booking.
- Each available time includes the session start and end.
- The client's own confirmed sessions, distinguished from available times.
- Selecting an available time opens booking confirmation.
- Selecting the client's own session opens its details.
- Other clients' names and booking details are not displayed.

**Client Profile**

- The client's own name and email.
- Bookings divided into **Naplánované**, **Minulé**, and **Zrušené**, as in the trainer's client detail screen.
- An option to open the details of the client's own booking.
- Details are read-only; the trainer edits the client's name and email.

**Booking Confirmation and Details**

- Before creation, the trainer, date, and time are displayed, with a late cancellation warning where required.
- Existing booking details display the date, time, status, and permitted cancellation option.
- Late cancellation requires confirmation of the warning before it is performed.

### 7.4 Password Recovery

Both roles have access to:

- A screen for entering an email and requesting a reset link.
- A screen for setting a new password through a valid link.
- A message for an invalid, used, or expired link.

### 7.5 Main Navigation

| Trainer | Client |
|---|---|
| Kalendár | Kalendár |
| Klienti | Môj profil |

Booking history is part of client details or the client's own profile, so it does not have a separate main navigation item.

## 8. Data Model

The data model consists of three main tables: `users`, `availability_ranges`, and `bookings`, with the fields and relationships below.

### 8.1 Users: `users`

A single `User` model is used for both roles. A client record is created before invitation acceptance, and the same record is subsequently completed by setting a password.

| Field | Meaning |
|---|---|
| `id` | Primary key |
| `name`, `email` | Required name and globally unique email |
| `role` | `trainer` or `client` |
| `trainer_id` | The client's trainer reference; empty for trainers |
| `active` | Activity independent of invitation acceptance |
| `encrypted_password` | Password hash managed by Devise |
| Invitation fields | Token and creation, sending, and acceptance times |
| Password recovery fields | Token and request time managed by Devise |
| `created_at`, `updated_at` | Creation and update times |

Emails are normalized by trimming surrounding whitespace and standardizing letter case. A database index also enforces uniqueness.

An invited client must not receive a usable substitute password or be allowed to log in before account completion.

### 8.2 Availability: `availability_ranges`

| Field | Meaning |
|---|---|
| `id` | Primary key |
| `trainer_id` | Availability owner |
| `starts_at`, `ends_at` | Start and end |
| `created_at`, `updated_at` | Creation and update times |

Half-hour slots are not stored in a separate table. Available start times are calculated from the ranges.

### 8.3 Bookings: `bookings`

| Field | Meaning |
|---|---|
| `id` | Primary key |
| `trainer_id`, `client_id` | Trainer and client |
| `created_by_id` | User who created the booking |
| `starts_at`, `ends_at` | Start and end |
| `status` | `confirmed` or `cancelled` |
| `cancelled_at`, `cancelled_by_id` | When and by whom the booking was cancelled |
| `late_cancellation` | Late cancellation marker |
| `created_at`, `updated_at` | Creation and update times |

Cancelled booking history is a filtered view of the same records, not another copy of bookings.

A booking is not linked to a specific availability range and has no `availability_range_id`. Trainers can book outside availability, and a client's session may be covered by multiple adjacent ranges. Availability coverage is checked at creation according to the user's role.

### 8.4 Database Constraints

- Foreign keys and required fields.
- A unique normalized email.
- Allowed roles and statuses.
- Availability ends after it starts; a booking lasts 60 minutes.
- No overlapping confirmed bookings for the same trainer.
- No overlapping availability ranges for the same trainer.

Time intervals are treated as `[start, end)`, allowing adjacent sessions and availability ranges. The choice of the specific database mechanism is listed in section 15.

## 9. Rules, Calculations, and Concurrent Requests

### 9.1 Calculating Available Times

A service loads the ranges for a given day, combines adjacent ranges for calculation purposes, and generates hour-long sessions starting on the hour or half hour. It excludes insufficient notice, uncovered time, and conflicts according to sections 12–14 of the specification.

The client receives only available start and end times, not other clients' data.

### 9.2 Server Logic

- The server determines the session end time, resulting status, and cancellation marker.
- Rules are checked at final confirmation, including after a warning is displayed.
- The operation's actor is derived from the login session, not from unrestricted form input.
- Cancellation changes the status; it does not physically delete the booking.
- Shared rule implementations respect the differences between trainers and clients.

### 9.3 Concurrency Protection

A combination of a transaction, an exclusive lock on the trainer record, and database constraints will be used.

Booking creation and cancellation, availability changes, and client deactivation use the same approach. After acquiring the lock, data is reloaded and the current time, availability, and activity are verified.

This also handles booking attempts concurrent with availability removal or client deactivation. Database conflicts are translated into clear Slovak messages.

### 9.4 Time Zone

Instants are stored in UTC and converted to `Europe/Bratislava` on input and output. Half-hour boundaries are checked in local time; 6 and 12 hours are evaluated as actual elapsed time differences.

A nonexistent or ambiguous local time during daylight saving time transitions is rejected with an explanation instead of being silently shifted.

## 10. API and Routes

Rails returns HTML or Turbo responses. GET loads screens and data without changing them, POST creates records or initiates operations, PATCH updates data or status, and DELETE removes the relevant resource. No separate public JSON API is created for the MVP.

Every protected request checks login, role, client activity, and authorization for the specific record according to section 5. Actor identity and ownership are determined on the server.

### 10.1 Registration, Login, and Password Recovery

| Method and Path | Purpose | Access |
|---|---|---|
| `GET /trainers/new` | Trainer registration form | Public |
| `POST /trainers` | Trainer creation and automatic login | Public |
| `GET /users/sign_in` | Login screen | Public |
| `POST /users/sign_in` | Login | Public |
| `DELETE /users/sign_out` | Logout | Logged-in user |
| `GET /users/password/new` | Password recovery request form | Public |
| `POST /users/password` | Sending a password reset link | Public |
| `GET /users/password/edit` | New password form | Valid password reset token |
| `PUT /users/password` | Saving a new password | Valid password reset token |
| `GET /invitation/accept` | Invitation acceptance form | Valid invitation token and active client |
| `PUT /invitation` | Account completion and automatic login | Valid invitation token and active client |

GET requests with an invalid token display an appropriate message without changing data. Public client registration and unrestricted account editing through the default Devise registration routes are not exposed.

### 10.2 Trainer Screens and Actions

| Method and Path | Purpose | Access |
|---|---|---|
| `GET /trainer/calendar` | Day or week calendar | Trainer |
| `GET /trainer/clients` | List of the trainer's own clients | Trainer |
| `GET /trainer/clients/new` | Add client form | Trainer |
| `POST /trainer/clients` | Client creation | Trainer |
| `GET /trainer/clients/:id` | Client details and bookings, including cancelled booking history | Their trainer |
| `GET /trainer/clients/:id/edit` | Edit client form | Their trainer |
| `PATCH /trainer/clients/:id` | Saving the name and email | Their trainer |
| `POST /trainer/clients/:id/invitation` | Sending or resending an invitation | Their trainer, active client |
| `PATCH /trainer/clients/:id/activation` | Deactivation or reactivation | Their trainer |
| `GET /trainer/availability_ranges/new` | Add availability form | Trainer |
| `POST /trainer/availability_ranges` | Availability creation | Trainer |
| `GET /trainer/availability_ranges/:id/edit` | Editing availability and selecting a portion to remove | Owner |
| `PATCH /trainer/availability_ranges/:id` | Editing availability boundaries | Owner |
| `DELETE /trainer/availability_ranges/:id` | Removing an entire range | Owner |
| `POST /trainer/availability_ranges/:id/removals` | Removing part of a range | Owner |
| `GET /trainer/bookings/new` | Booking for a client form | Trainer |
| `POST /trainer/bookings` | Creating a booking for a client | Trainer |
| `GET /trainer/bookings/:id` | Details of the trainer's own confirmed or cancelled booking | The booking's trainer |
| `DELETE /trainer/bookings/:id/late_cancellation` | Removing a late cancellation marker | The booking's trainer |

The calendar uses `date` and `view=day|week` parameters. Client details use `tab=planned|past|cancelled`; planned sessions are sorted from the soonest and past sessions from the most recent. Section 7.2 defines cancelled history content. A separate trainer cancellation history screen or route is not needed.

### 10.3 Client Screens and Actions

All routes below require an active, logged-in client with a completed account.

| Method and Path | Purpose |
|---|---|
| `GET /client/calendar` | Calendar of available times and the client's own confirmed sessions |
| `GET /client/bookings/new` | Selected session summary and any required warning before booking |
| `POST /client/bookings` | Creating the client's own booking |
| `GET /client/bookings/:id` | Details of the client's own confirmed or cancelled booking |
| `GET /client/profile` | Name, email, and the client's own bookings, including cancelled history |

The calendar uses the `date` parameter. The profile uses `tab=planned|past|cancelled`. Available times are part of the calendar and can be updated through a Turbo Frame on the same route; a separate `/client/available_slots` route is not needed.

The profile is read-only and therefore has no PATCH route. The booking overview and cancelled booking history are part of the profile, without separate `/client/bookings` or `/client/cancellations` pages.

### 10.4 Booking Cancellation and Navigation

| Method and Path | Purpose | Access |
|---|---|---|
| `PATCH /bookings/:id/cancellation` | Cancelling a booking according to role and time rules | Its trainer or the active client who owns it |

Confirmations and warnings are displayed in the relevant details or form. A GET request for a summary or details does not create or cancel a booking or block a time.

No route allows direct editing of a booking's time or client. Returning from details preserves the authorized calendar or profile context and the viewed date or tab. Return URLs are restricted to permitted internal application paths.

After login, trainers are redirected to `/trainer/calendar` and clients to `/client/calendar`. Successful client account creation through an invitation opens the same client calendar.

### 10.5 Responses and Errors

- **303:** redirect after a successful change through an HTML form.
- **422:** invalid data or a disallowed business operation; the form displays an explanation.
- **409:** a time conflict, for example during concurrent booking attempts.
- **403:** unauthorized role.
- **404:** a nonexistent record or a record outside the authorized scope.
- **429:** exceeding the request limits in section 5.3, with a Slovak message.
- Unauthenticated users are redirected to login when accessing protected screens.

Turbo responses update the relevant part of the interface; they do not change validation rules, authorization, or operation outcomes.

## 11. Project Structure

| Directory | Responsibility |
|---|---|
| `app/models/` | Users, availability, bookings, database relationships, and basic validations |
| `app/controllers/` | Request handling, with separate trainer and client areas |
| `app/policies/` | Pundit authorization checks |
| `app/services/` | Booking, cancellation, availability, and client management operations |
| `app/views/` | HAML screens and shared elements |
| `app/javascript/controllers/` | Stimulus interactions |
| `app/mailers/` | Invitation and password recovery emails |
| `app/jobs/` | Background jobs |
| `config/` | Routes, Slovak text, and configuration |
| `db/` | Migrations and database schema |
| `spec/` | RSpec tests |

```text
.github/
  workflows/
    ci.yml
bin/
  setup
  dev
app/
  models/
    user.rb
    availability_range.rb
    booking.rb
  controllers/
    trainer/
    client/
    users/
    invitations_controller.rb
    booking_cancellations_controller.rb
  policies/
    user_policy.rb
    availability_range_policy.rb
    booking_policy.rb
  services/
    bookings/
    availability/
    clients/
    invitations/
  views/
    trainer/
    client/
    users/
    shared/
  javascript/controllers/
  mailers/
  jobs/
config/
  routes.rb
  importmap.rb
  locales/sk.yml
  initializers/devise.rb
db/
  migrate/
  seeds.rb
  structure.sql
spec/
  factories/
  models/
  services/
  policies/
  requests/
  mailers/
  jobs/
  system/
  support/
.env.example
.gitignore
.rubocop.yml
Procfile.dev
README.md
```

The database schema will be stored in `db/structure.sql` to preserve PostgreSQL extensions and constraints, including overlap protection.

The controller receives the request, Pundit checks authorization, a service performs the operation, and a HAML template displays the result. Specific names for additional files will be added during implementation within this structure.

### 11.1 Slovak Text

All user-facing text will be stored in Rails I18n files under `config/locales/`, including screen text, validations, error messages, booking and invitation statuses, emails, and text used by Stimulus components.

Text will not be duplicated directly in HAML, Ruby, and JavaScript. Stimulus receives the required translated text from the server, for example through data attributes.

The MVP's default and only user-facing locale is `sk`. This organization of text does not introduce multilingual support into the MVP.

## 12. PWA and Internet Connection

- The app will have a manifest with a name, icons, and `standalone` display mode. It can be added to the home screen on supported devices and browsers.
- Launching from the icon opens a standalone window without the normal browser address bar on platforms supporting this mode.
- The production app uses HTTPS.
- Losing the internet connection displays the message "Nie ste pripojený k internetu. Skontrolujte pripojenie a skúste to znova."
- Bookings cannot be created or cancelled, and data cannot be edited, without an internet connection.
- Changes are not queued for automatic submission when the connection returns.
- The service worker caches only static assets and the offline message, not authenticated pages, personal data, or bookings.
- If the connection drops during confirmation and the operation's outcome is unclear, the current state is loaded first after reconnection so the user can see whether the operation succeeded.

## 13. Emails, Configuration, and Operations

### 13.1 Emails

- Action Mailer composes invitations and password recovery emails in Slovak.
- Active Job and Solid Queue handle background delivery through SMTP.
- Before sending an invitation, the job rechecks client activity and the current invitation's validity.
- Delivery is retried after a temporary error. Retrying does not create a new token or extend its validity.
- The "odoslané" status means acceptance by the email service, not guaranteed delivery to the inbox.

### 13.2 Configuration

- Database connection details, `SECRET_KEY_BASE`, SMTP details, and other secrets will be provided through environment variables or Rails credentials.
- Secrets do not belong in Git or logs.
- The app's public URL is configured separately so email links point to the correct location.
- The project includes `.env.example` with required variable names and safe example values. A local `.env` is loaded through `dotenv-rails` only in development and test environments and is not committed to Git.
- Development, test, and production environments use separate databases and email delivery configuration.

### 13.3 Development and Production

- The local environment requires Ruby, PostgreSQL, the Rails app, and a worker.
- The `letter_opener` gem is used to view development emails in the browser, only in the development environment. Local emails are not sent to real recipients through production SMTP.
- Production requires a Rails web process, a worker, PostgreSQL with the required extensions, HTTPS, backups, logs, and an email provider.

### 13.4 Setup and Startup

`bin/setup` handles:

1. Installing Ruby dependencies.
2. Checking required local configuration.
3. Preparing the database and database extensions.
4. Preparing the required static assets.

Repeated execution must not delete existing data without an explicit choice.

`bin/dev` starts the Rails server, Tailwind CSS watcher, and Solid Queue worker through Foreman and `Procfile.dev`. Importmap does not require a separate JavaScript watcher. Development emails open through `letter_opener` after their delivery jobs are processed.

`README.md` lists prerequisites and exact commands for setup, startup, testing, and project checks.

### 13.5 Sample Development Data

The project provides a separate command to create a sample trainer, clients, availability, and bookings. This command:

- is available only in the development environment,
- uses entirely fictional data,
- does not send real emails,
- does not create uncontrolled duplicates when repeated.

## 14. Testing

Testing uses RSpec. Tests are stored in `spec/`.

### 14.1 Test Categories

| Area | What Is Verified |
|---|---|
| Models | Data validations and database constraints |
| Services | Booking, cancellation, availability, and time boundaries |
| Policies and HTTP requests | Data isolation, deactivation, and rejection of unauthorized actions |
| Emails and background jobs | Invitations, password recovery, and delivery retries |
| System tests | Main browser user flows through Capybara |
| Concurrent requests | Protection against overlapping bookings and conflicting changes |

- `rspec-rails` and Capybara with RSpec system tests will be used.
- System tests use Selenium through `selenium-webdriver` with headless Chrome. Chrome and a compatible driver must be available locally and in CI; this driver also runs Turbo and Stimulus interaction tests.
- Regular tests use the Active Job test adapter and execute jobs in a controlled manner through test helpers, without starting a Solid Queue worker. Solid Queue integration is verified through separate integration tests.
- Tests use PostgreSQL, including database constraint and concurrency tests.
- Test cases are derived from the features and acceptance criteria in `SPEC.MD`.
- Time boundaries are tested with controlled time, without waiting for real hours to pass.

### 14.2 Main Verification Areas

- Registration, login, logout, and data isolation.
- Invitations: acceptance, expiry, resending, and email changes.
- Deactivation, including an existing login session and a valid invitation.
- Exact 6-hour and 12-hour boundaries, including crossing a boundary during confirmation.
- Overlapping and adjacent sessions, and adjacent availability ranges.
- Concurrent booking attempts and races with availability changes or deactivation.
- Cancellations, history, and marker removal without restoring a booking.
- Protection of past availability and sessions already in progress.
- Single-use password recovery and 15-minute expiry.
- Mobile interaction and basic PWA behavior.
- Exceeding the request limits in section 5.3, the 429 response, and the ability to retry after the limit period expires.
- The same generic password recovery confirmation for existing and nonexistent emails.

### 14.3 Test Data

FactoryBot provides factories for trainers, clients before invitation acceptance, clients with completed accounts, deactivated clients, availability, and confirmed or cancelled bookings.

Tests do not depend on sample development data. Each test prepares its own required records and does not send real emails.

### 14.4 Quality Checks

RuboCop checks Ruby code using Rails rules, HAML through `rubocop-haml`, and RSpec through `rubocop-rspec`. Brakeman and `bundler-audit` provide automated security checks.

Checks can be run locally and in CI using the same commands.

### 14.5 GitHub Actions

Pull requests and pushes to the main branch automatically run:

1. Ruby, PostgreSQL, and test database setup.
2. Static asset build.
3. RuboCop.
4. Brakeman and `bundler-audit`.
5. RSpec, including Capybara system tests.

CI provides Chrome and a compatible driver for headless Selenium system tests according to section 14.1. Diagnostic output, such as screenshots, is saved when tests fail.

CI uses its own test settings without production passwords, databases, or email credentials.

## 15. Open Technical Decisions

This section contains unresolved choices. The listed options are not binding decisions. Their implementation must respect `SPEC.MD` and the other sections of this document.

### 15.1 Interface and Application Identity

- Specific visual styling: colors, typography, icons, and spacing.
- Application name and PWA icon.
- Choosing a separate page or dialog for individual forms and details. Screen content and navigation remain as defined in section 7.
- Other visual details beyond the trainer calendar layout.

### 15.2 Database Schema

- Exact data types, defaults, indexes, and complete constraint definitions for the model in section 8.
- The specific database mechanism for overlap protection. One option under consideration is PostgreSQL exclusion constraints with a time range and the `btree_gist` extension; booking overlap protection must apply only to confirmed records.

### 15.3 Emails and Tokens

- The exact number of attempts and retry intervals for temporary delivery errors. Retrying must not create a new token or extend its validity.
- The technical assignment of timestamps during background delivery. According to `SPEC.MD`, an invitation must remain valid for 3 days after sending, and a password reset link for 15 minutes after creation; these periods do not change.
- Securely passing a token to an email job while respecting the prohibition on plaintext token storage and logging in section 5.2.

### 15.4 Environment and Deployment

- Specific hosting and SMTP providers.
- Specific application, PostgreSQL, and worker installation commands and settings within the `bin/setup`, `bin/dev`, and development data approach defined in section 13.
- Specific backup and operational logging settings.
- Selecting compatible patch versions and locking them according to section 3.
- Specific request limits and shared production counter storage for Rails `rate_limit` according to section 5.3.
- Selecting an optional production error tracking tool, such as Sentry.

### 15.5 Behavior Requiring Specification Clarification

The following questions affect application behavior. They cannot be resolved solely through technical implementation without alignment with `SPEC.MD`:

- The default activity of a new client record. One option under consideration: the client is active by default but cannot log in before accepting the invitation.
- Placement of a session in progress within the booking overview. One option under consideration: it remains among current and future sessions until it ends, then moves to past sessions.
