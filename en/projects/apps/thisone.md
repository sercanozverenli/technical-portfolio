# ThisOne - Quit Smoking & Habit Tracker

Designed with a focus on personal growth, daily discipline, and motivation, **ThisOne** is a feature-rich mobile application that helps users track their quit journey for smoking, alcohol, or any negative habit day by day, calculate money and time savings, and stay motivated via home screen widgets.

### ⚙️ System Architecture & Key Features

The app is built on a serverless, privacy-first architecture combined with engaging, dynamic UI components:

* **Live Habit Counter & Savings Calculator:** Users set their start date, daily cost, and consumption units for the habit they want to break. The system continuously tracks real-time progress (Days, Hours, Minutes, Seconds), unbroken streak counters, and accrued money/time savings.

* **Annual Activity Heatmap & Analytics:** Features a GitHub-style annual Heatmap that visualizes user consistency over 365 days. Successful days are rewarded with glowing green tiles. If a streak breaks, users receive supportive notifications to reset and try again.

* **Native Home Screen Widgets (Minimal & Interactive):** To enable frictionless tracking without opening the app, two Android home screen widgets were built natively (Minimal and Interactive widget options).

* **100% Local Data Privacy (Serverless Architecture):** All habit entries, timestamps, and savings calculations are saved strictly on the user's device (SharedPreferences) and never transmitted to external cloud servers.

* **Flexible Monetization Stack:** Integrated non-intrusive Google Mobile Ads (AdMob) with optional one-time Google Play Billing (IAP) Pro upgrade to disable ads permanently.


### 📱 Application Screenshots

![Home Screen & Counter](../../../docs/assets/thisone/mobile1_en.png ':size=20%')  ![Habit Selection](../../../docs/assets/thisone/mobile2_en.png ':size=20%')  ![Analytics & Heatmap](../../../docs/assets/thisone/mobile3_en.png ':size=20%')  ![Settings & Reminders](../../../docs/assets/thisone/mobile4_en.png ':size=20%')  ![Home Screen Widget](../../../docs/assets/thisone/widget_en.png ':size=25%')


### 🛠️ Tech Stack

* **Mobile Framework:** Flutter (Dart)
* **State Management:** Provider
* **Local Storage & Architecture:** SharedPreferences *(Serverless / Local Data Architecture)*
* **Native Android Integration:** Kotlin, `home_widget` SDK, WorkManager & Room Database ProGuard shields *(FlutterFragmentActivity platform channels)*
* **UI & Styling:** Google Fonts, Material Design 3 (Dark/Light Theme), Flutter Local Notifications
* **Monetization & Services:** Google Mobile Ads (AdMob), Google Play Billing (In-App Purchase), Google In-App Review

*(UI design, state management, Android native widget channels, R8/ProGuard optimizations, and Play Store publishing workflows were developed with pair-programming assistance from **Google Antigravity / Gemini AI**.)*

---

### 📦 Store Links

Explore the live production build on Google Play:

**[View on Google Play Store](https://play.google.com/store/apps/details?id=com.thisone.habittracker)**

---

> <small>*⚠️ **Medical Disclaimer**<br> ThisOne is not a medical device; it does not diagnose, treat, cure, or prevent any medical condition or disease. The application is intended solely for personal motivation, daily habit tracking, and savings estimation. Please consult a qualified healthcare professional for any medical advice or health-related decisions.*</small>
