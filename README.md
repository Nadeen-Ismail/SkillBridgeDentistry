# SkillBridge Dentistry

An AI-assisted dental consultation platform that connects newly graduated dentists with experienced consultants. A graduate uploads a photo of a case, an AI model suggests a likely diagnosis, and the case is routed to consultants in the matching specialty for their review.

**Graduation project, B.Sc. Computer Science and Artificial Intelligence, Misr University for Science & Technology. Grade: A\*.**

![.NET](https://img.shields.io/badge/.NET-6.0-512BD4?logo=dotnet&logoColor=white)
![SQL Server](https://img.shields.io/badge/SQL%20Server-CC2927?logo=microsoftsqlserver&logoColor=white)
![Hangfire](https://img.shields.io/badge/Hangfire-background%20jobs-1E90FF)

This repository is the backend API. The AI model runs as a separate service in [DentalAIApp](https://github.com/Nadeen-Ismail/DentalAIApp).

## How a case flows through the system

1. A fresh graduate uploads a case image, optionally with their own diagnosis.
2. The API sends the image to the AI service and stores the predicted condition and confidence.
3. The predicted condition is mapped to a dental specialty, and suggested treatment guidance is attached.
4. Consultants in that specialty are assigned to the case and notified.
5. Consultants respond with their own assessment, and the graduate rates the consultants afterwards.

## Features

- **Two user roles**, fresh graduates and consultants, each with their own registration flow.
- **JWT authentication** on ASP.NET Core Identity, with role seeding on startup.
- **Password reset by email** using a one-time code sent through MailKit.
- **AI-assisted case analysis** by calling the image-classification service over HTTP.
- **Specialty-based routing** that assigns each case to the right consultants.
- **In-app notifications** with unread counts and read tracking, scheduled through Hangfire background jobs.
- **Consultant ratings and levels** so graduates can give feedback after a case.

## Architecture

The solution uses a layered Clean Architecture approach.

| Project | Responsibility |
| --- | --- |
| `CoreLayer` | Entities, identity models, and service interfaces. |
| `RepositoryLayer` | EF Core `DbContext`, entity configurations, migrations, and role seeding. |
| `ServiceLayer` | AI client, email, OTP, token, and notification services. |
| `SkillBridgeDentistry1` | ASP.NET Core Web API, controllers, and DTOs. |

## API overview

| Area | Endpoints |
| --- | --- |
| Auth | Register graduate, register consultant, login, forgot password, verify OTP, reset password, profile |
| Cases | Upload a case request, respond to a case, list case responses and assigned consultants |
| Notifications | List, unread count, mark one or all as read, delete |
| Ratings | Rate a consultant, list consultants available to rate, consultant levels |

Swagger with JWT support is available when running in Development.

## Tech stack

ASP.NET Core 6, Entity Framework Core, SQL Server, ASP.NET Core Identity, JWT, Hangfire, MailKit, Swagger.

## Getting started

### Prerequisites

- .NET 6 SDK
- SQL Server
- An SMTP account for password-reset emails

### Configuration

Create `SkillBridgeDentistry1 soll/SkillBridgeDentistry1/appsettings.json`. It is git-ignored so secrets stay out of the repository.

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=.;Database=SkillBridge;Trusted_Connection=True;TrustServerCertificate=True"
  },
  "Jwt": {
    "Key": "<a long random signing key>",
    "Issuer": "https://localhost:7000",
    "Audience": "https://localhost:7000",
    "DurationInDays": 2
  },
  "EmailConfiguration": {
    "From": "<sender address>",
    "SmtpServer": "<smtp host>",
    "Port": 587,
    "UserName": "<smtp user>",
    "Password": "<smtp password>"
  }
}
```

### Run

```bash
cd "SkillBridgeDentistry1 soll"
dotnet run --project SkillBridgeDentistry1
```

Migrations and roles are applied automatically on startup. The Hangfire dashboard is served at `/hangfire`.
