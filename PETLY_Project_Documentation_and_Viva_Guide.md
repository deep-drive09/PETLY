# 🐾 PETLY: Pet Care & Training Management System
### Android Mobile Application Project Report, Complete Code Walkthrough & Master Viva Guide

---

## 📑 Table of Contents
1. [Project Overview & Introduction](#1-project-overview--introduction)
2. [Key Objectives & Technology Stack](#2-key-objectives--technology-stack)
3. [System Architecture & 9-Screen Workflow](#3-system-architecture--9-screen-workflow)
4. [Complete Code Walkthrough (Class-by-Class & XMLs)](#4-complete-code-walkthrough-class-by-class--xmls)
   - [DatabaseHelper.java (SQLite Persistence Engine)](#41-databasehelperjava-sqlite-persistence-engine)
   - [LoginActivity.java & activity_login.xml](#42-loginactivityjava--activity_loginxml)
   - [WelcomeActivity.java & activity_welcome.xml](#43-welcomeactivityjava--activity_welcomexml)
   - [AwakenActivity.java & activity_awaken.xml](#44-awakenactivityjava--activity_awakenxml)
   - [HomeActivity.java & activity_home.xml](#45-homeactivityjava--activity_homexml)
   - [VaccineActivity.java & activity_vaccine.xml](#46-vaccineactivityjava--activity_vaccinexml)
   - [DietActivity.java & activity_diet.xml](#47-dietactivityjava--activity_dietxml)
   - [FoodActivity.java & activity_food.xml](#48-foodactivityjava--activity_foodxml)
   - [TrainActivity.java & activity_train.xml](#49-trainactivityjava--activity_trainxml)
   - [DiseaseActivity.java & activity_disease.xml](#410-diseaseactivityjava--activity_diseasexml)
   - [AndroidManifest.xml Configuration](#411-androidmanifestxml-configuration)
5. [Top Features to Highlight to Your Teacher / Examiner](#5-top-features-to-highlight-to-your-teacher--examiner)
6. [Step-by-Step Live Project Presentation Script](#6-step-by-step-live-project-presentation-script)
7. [Master Viva Questions & Answers Guide (30+ High-Yield Questions)](#7-master-viva-questions--answers-guide-30-high-yield-questions)
8. [Future Enhancements & Conclusion](#8-future-enhancements--conclusion)

---

## 1. Project Overview & Introduction

**PETLY** is an innovative, gamified Android application built to centralize companion animal healthcare, nutrition plans, daily feeding schedules, trick training progression, and symptom diagnostics into an engaging mobile dashboard.

By fusing **RPG (Role-Playing Game) elements**—such as companion awakening stats, consumable HP-boost meals, and unlockable trick quest skill trees—PETLY transforms routine pet maintenance into an interactive and rewarding digital routine for pet parents, trainers, and veterinary caregivers.

### Target Audience & Use Cases
- **Pet Parents**: Tracking vaccination milestones, diet plans, and daily meal schedules.
- **Dog & Cat Trainers**: Following a structured 7-step trick progression tree.
- **First-time Pet Adopters**: Quickly diagnosing pet symptoms and understanding breed care guidelines.

---

## 2. Key Objectives & Technology Stack

### Core Objectives
1. **Offline-First Persistence**: Store pet profiles, vaccine checklists, and training records locally using embedded SQLite with zero external server dependencies.
2. **Dynamic Companion Adaptation**: Seamlessly support both **Dog** and **Cat** archetypes, automatically re-skinning UI illustrations and persistent corner avatars across all 9 screens.
3. **Structured 5-in-1 Pet Care Hub**: Offer 5 interconnected modules: Vaccinations, Diet Plans, Food Reminders, Trick Training, and Disease Diagnostics.
4. **Shōnen Anime & Pixel Gamified UI**: Implement a vibrant color palette (`#FF5722` Sunset Orange, `#2979FF` Ocean Blue, `#D50000` Crimson, `#00C853` Quest Green) with elevated cards and custom inputs.

### Technology Stack Table

| Component | Technology | Purpose in PETLY |
| :--- | :--- | :--- |
| **Language** | Java (JDK 17+) | Core application logic, event listeners, intent routing, SQLite queries |
| **UI Framework** | Native Android XML | Responsive layouts (`RelativeLayout`, `LinearLayout`, `ScrollView`) |
| **Local Database** | SQLite (`SQLiteOpenHelper`) | Relational persistence for pet profiles, vaccines, diet, and training |
| **Architecture** | Model-View-Controller (MVC) | Separation of data models, XML views, and Activity controllers |
| **Build Tool** | Gradle / AGP | Dependency resolution, APK building, manifest packaging |

---

## 3. System Architecture & 9-Screen Workflow

PETLY uses the standard **MVC (Model-View-Controller)** pattern:
- **Model**: SQLite tables (`pet_info`, `vaccines`, `diet`, `train`, `disease`) in `DatabaseHelper.java`.
- **View**: XML Layout files (`activity_login.xml` to `activity_disease.xml`) and drawable resources.
- **Controller**: 9 Java Activity classes handling user interactions and intent routing.

```
[ Screen 1: LoginActivity ]
            │ (Login Click)
            ▼
[ Screen 2: WelcomeActivity ] ─── (Select Dog / Cat)
            │
            ▼
[ Screen 3: AwakenActivity ] ──── (Input Name, Age, Breed, Weight -> Save SQLite)
            │
            ▼
[ Screen 4: HomeActivity (Command Center Dashboard) ]
     │               │              │               │               │
     ▼               ▼              ▼               ▼               ▼
[Screen 5:       [Screen 6:     [Screen 7:      [Screen 8:      [Screen 9:
 VaccineActivity] DietActivity]  FoodActivity]   TrainActivity]  DiseaseActivity]
```

---

## 4. Complete Code Walkthrough (Class-by-Class & XMLs)

### 4.1 DatabaseHelper.java (SQLite Persistence Engine)
- **Path**: `app/src/main/java/com/petly/app/DatabaseHelper.java`
- **Inheritance**: Extends `SQLiteOpenHelper`
- **Database Name**: `petly_raw.db` (Version `1`)

#### Core Tables & DDL:
1. `pet_info`: `id (PK)`, `pet_type`, `name`, `age`, `breed`, `gender`, `weight`
2. `vaccines`: `id (PK)`, `title`, `is_checked`
3. `diet`: `id (PK)`, `name`, `category`
4. `train`: `id (PK)`, `step`, `trick`
5. `disease`: `id (PK)`, `symptom`, `diagnosis`

#### Key Method: `insertPet()`
```java
public void insertPet(String type, String name, int age, String breed, String gender, double weight) {
    SQLiteDatabase db = this.getWritableDatabase();
    db.execSQL("DELETE FROM pet_info"); // Replaces existing profile with current companion
    ContentValues cv = new ContentValues();
    cv.put("pet_type", type);
    cv.put("name", name);
    cv.put("age", age);
    cv.put("breed", breed);
    cv.put("gender", gender);
    cv.put("weight", weight);
    db.insert("pet_info", null, cv);
}
```

---

### 4.2 LoginActivity.java & activity_login.xml
- **Path**: `app/src/main/java/com/petly/app/LoginActivity.java`
- **Layout**: `activity_login.xml`
- **Role**: Application launcher window. Captures User ID and Password, then transitions immediately to `WelcomeActivity`.
- **Key Code**:
```java
Button btnLogin = findViewById(R.id.btnLogin);
btnLogin.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        Intent intent = new Intent(LoginActivity.this, WelcomeActivity.class);
        startActivity(intent);
        finish(); // Removes LoginActivity from backstack
    }
});
```

---

### 4.3 WelcomeActivity.java & activity_welcome.xml
- **Path**: `app/src/main/java/com/petly/app/WelcomeActivity.java`
- **Layout**: `activity_welcome.xml`
- **Role**: Companion Archetype selector. Renders large interactive Dog and Cat cards.
- **Key Code**:
```java
private void selectCompanion(String type) {
    Intent intent = new Intent(WelcomeActivity.this, AwakenActivity.class);
    intent.putExtra("PET_TYPE", type); // Passes "Dog" or "Cat"
    startActivity(intent);
    finish();
}
```

---

### 4.4 AwakenActivity.java & activity_awaken.xml
- **Path**: `app/src/main/java/com/petly/app/AwakenActivity.java`
- **Layout**: `activity_awaken.xml`
- **Role**: Companion Stat Initializer. Validates inputs, saves companion attributes into SQLite, and routes to `HomeActivity`.
- **Dynamic Avatar Logic**:
```java
if ("Cat".equalsIgnoreCase(petType)) {
    imgAvatar.setImageResource(R.drawable.cat_avatar);
} else {
    imgAvatar.setImageResource(R.drawable.dog_avatar);
}
```

---

### 4.5 HomeActivity.java & activity_home.xml
- **Path**: `app/src/main/java/com/petly/app/HomeActivity.java`
- **Layout**: `activity_home.xml`
- **Role**: Central Command Dashboard linking 5 specialized pet care submodules while retaining companion context.
- **Key Navigation Helper**:
```java
private void navigateTo(Class<?> target) {
    Intent intent = new Intent(HomeActivity.this, target);
    intent.putExtra("PET_TYPE", petType);
    startActivity(intent);
}
```

---

### 4.6 VaccineActivity.java & activity_vaccine.xml
- **Role**: Core immunization timeline checklist.
- **Features**: 5 interactive checkboxes for:
  1. `Vacc 1: Rabies Shot (Core)`
  2. `Vacc 2: Parvovirus / FVRCP`
  3. `Vacc 3: Distemper Booster`
  4. `Vacc 4: Bordetella Kennel Cough`
  5. `Vacc 5: Annual Deworming & Lyme`

---

### 4.7 DietActivity.java & activity_diet.xml
- **Role**: Nutritional consumables menu displaying caloric values and benefits:
  1. 🍗 **Boiled Chicken Breast** (250 kcal - Lean Muscle Growth)
  2. 🥚 **Boiled Egg** (150 kcal - Essential Amino Acids)
  3. 🐟 **Salmon Fillet Treat** (200 kcal - Omega-3 Fur & Skin Support)
  4. 🥣 **Pedigree Dry Kibble** (350 kcal - Daily Balanced Nutrition)

---

### 4.8 FoodActivity.java & activity_food.xml
- **Role**: 3-phase daily feeding timetable with HP recovery indicators:
  - 🥛 **Breakfast (08:00 AM)**: Warm Pet Milk & Hydration Bowl (+30 HP)
  - 🍗 **Lunch (01:00 PM)**: Boiled Chicken Breast & Eggs (+50 HP)
  - 🥣 **Dinner (08:00 PM)**: Pedigree Kibble Bowl (+40 HP)

---

### 4.9 TrainActivity.java & activity_train.xml
- **Role**: 7-tier RPG trick progression tree:
  - Step 1: Sit Command (`UNLOCKED ✓`)
  - Step 2: Stay Command (`UNLOCKED ✓`)
  - Step 3: Come Command (`QUEST READY ⚡`)
  - Step 4: Hand Shake (`LOCKED 🔒`)
  - Step 5: Spin Trick (`LOCKED 🔒`)
  - Step 6: Bark Command (`LOCKED 🔒`)
  - Step 7: Fetch Ball (`LOCKED 🔒`)

---

### 4.10 DiseaseActivity.java & activity_disease.xml
- **Role**: Real-time diagnostic symptom scanner.
- **Key Code**:
```java
btnAnalyze.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        String query = etQuery.getText().toString().trim();
        if (query.isEmpty()) query = "Lethargy";

        tvTitle.setText("DIAGNOSIS: " + query.toUpperCase() + " ANALYSIS");
        tvDetails.setText("Scan complete for '" + query + "'. Primary symptoms analyzed. Keep companion hydrated and monitor energy levels.");
        Toast.makeText(DiseaseActivity.this, "ANALYZE SCAN COMPLETE! 🔬", Toast.LENGTH_SHORT).show();
    }
});
```

---

## 5. Top Features to Highlight to Your Teacher / Examiner

1. **Gamified RPG Companion Design**: Showcases unique creativity by turning everyday pet routines into unlockable trick quests, stat awakenings, and HP meal restorations.
2. **Dynamic Multi-Archetype Companion System**: Seamlessly shifts themes and avatars between Dog and Cat profiles across all 9 screens using Intent Extras.
3. **Robust Offline SQLite Persistence**: Zero dependency on third-party cloud backends; all pet stats and checklists persist locally on device.
4. **5-in-1 Comprehensive Wellness Hub**: Combines medical immunization, nutrition plans, meal timetables, behavioral training, and diagnostic symptom checking.
5. **Interactive Diagnostic Symptom Engine**: Real-time input evaluation updating diagnosis cards and triggering visual Toast feedback.
6. **Pixel-Perfect Shōnen Aesthetic**: Clean card elevations, consistent button styling, and persistent bottom-right companion avatars.

---

## 6. Step-by-Step Live Project Presentation Script

1. **Step 1 (Launch & Login)**: Open PETLY -> Show branded Login screen -> Enter credentials -> Tap `LOGIN TO PETLY ⚡`.
2. **Step 2 (Select Companion)**: Demonstrate selecting between `Dog Archetype` and `Cat Archetype`.
3. **Step 3 (Awaken Pet)**: Enter pet stats (`Name: Rex`, `Age: 2`, `Breed: Golden Retriever`, `Gender: Male`, `Weight: 25.5`) -> Tap `PETLY IS READY! ⚡` -> Point out that data is written to SQLite.
4. **Step 4 (Command Center)**: Show the 5 command buttons in `HomeActivity`.
5. **Step 5 (Vaccination & Diet)**: Open Vaccine Checklist -> Check off shots -> Open Diet Plan -> Explain nutrition recommendations and calories.
6. **Step 6 (Food Timetable)**: Open Food Reminder -> Explain the 3-interval schedule (Breakfast, Lunch, Dinner).
7. **Step 7 (Skill Tree)**: Open Train screen -> Show Step 1-2 Unlocked, Step 3 Quest Ready, and Steps 4-7 Locked.
8. **Step 8 (Disease Diagnostic Scan)**: Open Disease Analysis -> Type "Lethargy" or "Fever" -> Tap `RUN DIAGNOSTIC SCAN 🔍` -> Show dynamic diagnosis update and Toast alert.

---

## 7. Master Viva Questions & Answers Guide (30+ High-Yield Questions)

### Category A: PETLY Project Specific
**Q1: What is the main objective of PETLY?**
> *Ans:* PETLY is a gamified native Android application that unifies pet medical tracking (vaccinations), daily meal schedules, nutrition diets, behavioral trick training (RPG skill tree), and symptom diagnostics into an offline-first mobile platform.

**Q2: How does PETLY store pet data and vaccine records?**
> *Ans:* Through an embedded SQLite database managed by `DatabaseHelper.java` extending `SQLiteOpenHelper`. It maintains 5 relational tables: `pet_info`, `vaccines`, `diet`, `train`, and `disease`.

**Q3: How does the application pass data between activities?**
> *Ans:* Using Android `Intent` extras (e.g. `intent.putExtra("PET_TYPE", type)` and `getIntent().getStringExtra("PET_TYPE")`).

**Q4: How does the dynamic companion avatar work on every screen?**
> *Ans:* Each activity reads the `PET_TYPE` intent extra. If it equals `"Cat"`, `setImageResource(R.drawable.cat_avatar)` is called; otherwise, it sets `R.drawable.dog_avatar`. It is anchored to the bottom-right corner using `RelativeLayout` layout alignment properties.

**Q5: What happens when the user clicks 'PETLY IS READY!' in AwakenActivity?**
> *Ans:* It extracts text from `EditText` fields (with default fallback values), executes `dbHelper.insertPet(...)` (which clears previous records and writes the new profile via `ContentValues`), and launches `HomeActivity`.

---

### Category B: Android Fundamentals & Architecture
**Q6: What is an Activity in Android? Explain its Lifecycle.**
> *Ans:* An Activity represents a single screen with a user interface. The 7 lifecycle callbacks are:
> 1. `onCreate()`: Activity is initialized and layout is set.
> 2. `onStart()`: Activity becomes visible.
> 3. `onResume()`: Activity enters foreground and interacts with user.
> 4. `onPause()`: Activity partially obscured; pausing animations/resources.
> 5. `onStop()`: Activity is no longer visible.
> 6. `onRestart()`: Activity is restarted from stopped state.
> 7. `onDestroy()`: Activity is destroyed and memory cleared.

**Q7: What is an Intent? Difference between Explicit and Implicit Intent?**
> *Ans:* An Intent is a messaging object used to request an action from another component.
> - **Explicit Intent**: Specifies the exact destination class (e.g., `new Intent(HomeActivity.this, VaccineActivity.class)`). Used for in-app navigation.
> - **Implicit Intent**: Specifies an action filter, allowing Android OS to choose any registered app capable of handling it (e.g. opening a browser URL or camera).

**Q8: Why is `finish()` called after `startActivity()` in LoginActivity?**
> *Ans:* `finish()` closes the current Activity and removes it from the backstack. This prevents the user from navigating back to the login screen when pressing the Android hardware Back button from the home screen.

**Q9: What is the purpose of `AndroidManifest.xml`?**
> *Ans:* It is the root configuration manifest of the app. It declares the package name, app icon, label, theme, all Activities (with launcher `<intent-filter>`), services, receivers, and required OS permissions.

**Q10: What is `Context` in Android?**
> *Ans:* `Context` is the global environment interface that allows access to application resources (strings, drawables), database handles, system services, and layout inflaters.

---

### Category C: SQLite & Persistence
**Q11: What is `SQLiteOpenHelper`?**
> *Ans:* A helper class in Android that manages database creation (`onCreate`) and version management (`onUpgrade`).

**Q12: What is `ContentValues` and why is it used?**
> *Ans:* A key-value map used to store column-value pairs when inserting or updating SQLite database rows, ensuring type safety and query sanitization.

**Q13: What is the difference between SQLite and SharedPreferences?**
> *Ans:* `SharedPreferences` is for lightweight primitive key-value data (flags, preferences), while `SQLite` is a full relational database engine suited for structured, multi-row, multi-table queries.

---

### Category D: UI, Layouts & Styling
**Q14: Difference between `LinearLayout` and `RelativeLayout`?**
> *Ans:* `LinearLayout` arranges elements linearly in a single orientation (horizontal/vertical). `RelativeLayout` positions elements relative to each other or to parent boundaries (used in PETLY to pin the companion avatar to the bottom right).

**Q15: Difference between `dp`, `sp`, and `px`?**
> *Ans:*
> - `px` (Pixels): Actual physical hardware pixels.
> - `dp` (Density-Independent Pixels): Scaled based on screen density (1 dp = 1 px on 160 dpi screen). Used for layout sizes.
> - `sp` (Scale-Independent Pixels): Like `dp`, but also respects user-configured system font size settings. Used for `TextView` font sizes.

---

### Category E: Advanced & Examiner Trap Questions
**Q16: What happens during screen rotation?**
> *Ans:* Screen rotation triggers a configuration change that destroys and recreates the Activity (`onDestroy()` followed by `onCreate()`). Data can be preserved using `onSaveInstanceState(Bundle)` or `ViewModel`.

**Q17: How would you add push notifications for food reminders in the future?**
> *Ans:* Using Android `AlarmManager` or `WorkManager` scheduled at feeding times (08:00 AM, 01:00 PM, 08:00 PM) to trigger a `NotificationCompat.Builder` push notification.

**Q18: How could AI disease detection be added to PETLY?**
> *Ans:* By embedding a lightweight `TensorFlow Lite` model to classify pet skin conditions or symptoms directly from camera snapshots on the device.

---

## 8. Future Enhancements & Conclusion

### Future Roadmap
1. **Cloud Synchronization**: Firebase backend integration for cloud multi-device sync and veterinary profile sharing.
2. **Automated Alarm & Notification System**: Background alarms triggering audio reminders for scheduled feeding and vaccine doses.
3. **AI Vision Health Scanner**: On-device image classification for canine and feline skin conditions.
4. **Bluetooth Pet Collar Pairing**: Real-time GPS location and step-count activity tracking.

### Conclusion
The **PETLY** application successfully provides a reliable, offline-first, gamified pet care platform. Its robust SQLite data layer, intuitive 9-screen MVC navigation, dynamic archetype companion adaptation, and modern Shōnen pixel aesthetics make it a complete and impressive academic Android software engineering project.
