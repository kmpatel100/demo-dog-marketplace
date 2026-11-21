# Architecture

Status: bootstrap. No implementation yet. Content comes from human-provided inputs, not from code.

## Purpose
A web platform where dog sellers and shelters manage their store and marketplace listings, and dog lovers browse them.

## Actors
- **Seller:** manages their own store and listings.
- **Shelter:** manages their own listings (see A3).
- **Dog lover:** browses listings.
- **Staff/admin:** operates the platform through Django admin.

## Components
- **Client:** React web app.
- **Server:** Django app exposing a REST API. It owns all business logic and all database access.
- **Data store:** one relational database (see A4). Engine: UNKNOWN.

The client talks to the server only through the REST API.

## Assumptions (to confirm)
- A1: The React client is served separately from the Django API, not rendered by Django templates.
- A2: "Standard" authentication means Django's built-in user and session model.
- A3: Sellers and shelters are separate roles that can both create listings.
- A4: One relational database holds all domain data.

## Open Decisions
- Deployment model: TO BE DECIDED
- Scale expectations: TO BE DECIDED
- RDBMS engine: UNKNOWN
- External integrations: UNKNOWN

Decisions with security impact (auth mechanism, origin topology, roles, payments, photo uploads, personal data) are tracked in [SECURITY.md](SECURITY.md#open-decisions).
