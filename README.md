# 🐾 PETLY — Pet Care & Training App

PETLY is an offline Android pet care app built in **Java**, **XML** and **SQLite**. Users choose a dog or cat, create their pet's profile, and use a game-style dashboard for vaccinations, diet, feeding schedules, training and symptom checks.

> Mobile Application Development (MAD) micro-project — Diploma in Computer Engineering

---

## Features

- **Login screen**: entry point of the app
- **Choose your companion**: pick a Dog or Cat; the matching avatar appears on every screen
- **Pet profile (Awaken)**: enter name, age, breed, gender and weight, saved to a local SQLite database
- **Dashboard**: one tap to reach five modules:
  - 💉 **Vaccination & Care**: 5-step vaccine checklist
  - 🍖 **Diet Plan**: recommended foods with calories
  - ⏰ **Food Reminder**: daily breakfast / lunch / dinner schedule
  - ⚡ **Train Your Pet**: 7-step trick progression
  - 🔬 **Disease Analysis**: enter a symptom and view the result
- **Fully offline**: no internet permission; all data stays on the device

## Tech Stack

| Item | Used |
|---|---|
| IDE | Android Studio |
| Language | Java 17 |
| UI | XML layouts (LinearLayout, RelativeLayout, ScrollView) |
| Database | SQLite (`SQLiteOpenHelper`) |
| Build | Gradle |
| SDK | minSdk 21 (Android 5.0), target/compile SDK 34 |
| Package | `com.petly.app` |

## App Flow

```
Login → Welcome (Dog / Cat) → Awaken (pet form → SQLite) → Home
                                                            ├── Vaccination
                                                            ├── Diet Plan
                                                            ├── Food Reminder
                                                            ├── Train Your Pet
                                                            └── Disease Analysis
```

The selected pet type is passed between screens using an Intent extra (`PET_TYPE`).

## Project Structure

```
app/src/main/
├── AndroidManifest.xml          # declares all 9 activities, Login is launcher
├── java/com/petly/app/
│   ├── DatabaseHelper.java      # creates 5 tables, saves pet profile
│   ├── LoginActivity.java
│   ├── WelcomeActivity.java
│   ├── AwakenActivity.java
│   ├── HomeActivity.java
│   ├── VaccineActivity.java
│   ├── DietActivity.java
│   ├── FoodActivity.java
│   ├── TrainActivity.java
│   └── DiseaseActivity.java
└── res/
    ├── layout/                  # 9 screen designs (activity_*.xml)
    ├── drawable/                # dog/cat avatars, logo, shape drawables
    └── values/colors.xml        # app colour palette
```

## Database

Database: `petly_raw.db` (version 1)

| Table | Purpose |
|---|---|
| `pet_info` | Pet profile (type, name, age, breed, gender, weight) |
| `vaccines` | Vaccine list with checked status (seeded) |
| `diet` | Diet items (seeded) |
| `train` | Training steps (seeded) |
| `disease` | Symptom → diagnosis (reserved for future use) |

View it while the app runs: **Android Studio → View → Tool Windows → App Inspection → Database Inspector**.

## How to Run

1. Clone or download this project.
2. Open the `PETLY` folder in **Android Studio**.
3. Let Gradle sync finish.
4. Start an emulator (Android 8.0+ recommended) or connect a phone.
5. Click **Run ▶**.

## Current Status

Version 1 is a working prototype. Navigation, dog/cat personalisation and saving the pet profile to SQLite are fully functional. The five module screens currently display their content from XML; their database tables are already created and seeded.

## Future Scope

- Connect vaccine, diet and training screens to their database tables (RecyclerView)
- Save vaccine checklist progress
- Login with user accounts and validation
- Food reminder notifications using AlarmManager
- Symptom-based lookup for disease analysis
- Support for multiple pets
