# Simple CRM

A minimal contact management app: add users with address and birth date, browse the list, view and edit each contact's details — backed live by Firestore.

![Angular](https://img.shields.io/badge/Angular-14-DD0031?logo=angular&logoColor=white)
![Firebase](https://img.shields.io/badge/Firebase-Firestore-FFCA28?logo=firebase&logoColor=black)
![Angular Material](https://img.shields.io/badge/Angular_Material-14-009688?logo=angular&logoColor=white)

---

## Features

- **Contact list** — all users streamed live from Firestore, no manual refresh needed
- **Add contact** — name, email, birth date and address via a Material dialog
- **Contact details** — dedicated route per contact (`/user/:id`)
- **Edit in place** — separate dialogs for editing personal details vs. address, each pre-filled from the current contact
- **Dashboard route** — present in the routing table as the landing page, currently an empty placeholder (see *Status*)

---

## Tech Stack

| Technology | Version |
|---|---|
| Angular (NgModules) | 14 |
| Angular Material + CDK | 14 |
| Firebase (Firestore) | `@angular/fire` 7.4 |

No custom backend — Firestore is used directly from the client, same pattern as join and Ring of Fire. No authentication: the app has no login at all, so in its current form it isn't gating access to the contact data in any way.

---

## Getting Started

### Prerequisites

- Node.js ≥ 14
- Angular CLI 14 (`npm install -g @angular/cli@14`)

### Installation

```bash
git clone https://github.com/AndreasBandusch/simple-crm.git
cd simple-crm
npm install
```

### Development server

```bash
ng serve
```

Open [http://localhost:4200](http://localhost:4200). `src/environments/environment.ts` already points at the project's own Firebase instance, so the contact list is functional immediately.

### Build

```bash
ng build
```

Output goes to `dist/`.

---

## Architecture

### Data model

`User` (`src/models/user.class.ts`) is a plain class with a constructor that accepts an optional raw object — used both for "new, empty user" (`new User()`) and "hydrate from Firestore" (`new User(rawData)`) — plus a `toJSON()` that strips it back down to a plain object for writes. Every component that touches a contact goes through this class rather than passing Firestore's raw document data around directly.

### Live list, live detail

`UserComponent` subscribes to the whole `users` collection via `valueChanges({idField: 'userId'})`; `UserDetailsComponent` subscribes to a single document by route id the same way. Both re-render automatically on any Firestore change — there's no manual "reload" anywhere, edits made in one browser tab appear in another without a refresh.

### Editing

Editing is split across two separate dialogs — `DialogEditUserComponent` for name/email/birth date, `DialogEditAddressComponent` for street/zip/city — rather than one combined form. Both are opened from `UserDetailsComponent`, which hands each dialog a fresh `User` copy (`new User(this.user.toJSON())`) so an opened-but-cancelled dialog can't leave stray edits on the live object.

---

## Project Structure

```
simple-crm/
└── src/
    ├── app/
    │   ├── dashboard/             # Landing route, currently a placeholder
    │   ├── user/                  # Contact list + "add user" trigger
    │   ├── user-details/          # Single contact view, opens edit dialogs
    │   ├── dialog-add-user/       # Add-contact form
    │   ├── dialog-edit-user/      # Edit name/email/birth date
    │   └── dialog-edit-address/   # Edit street/zip/city
    └── models/
        └── user.class.ts          # Contact data model + Firestore (de)serialization
```

## Status

Completed bootcamp project from the Developer Akademie, not under further active development. Worth knowing if you look at the code: the `/dashboard` route (the app's landing page) is an empty component with no template content beyond scaffolding — the app is really only the `/user` list and detail views. There's also no authentication anywhere, and `src/environments/environment.ts` commits the real Firebase config including the client API key — typical for client-side Firebase apps, since access is meant to be gated by Firestore rules rather than key secrecy, same situation as join and Ring of Fire.

## Author

**Andreas Bandusch**
