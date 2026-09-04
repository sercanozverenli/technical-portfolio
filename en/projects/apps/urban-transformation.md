# Urban Transformation Assistant (Loan Calculator & Simulation Tool)

Developed in alignment with current legislation from the Ministry of Environment, Urbanisation and Climate Change and the Urban Transformation Directorate in Turkey, **Urban Transformation Assistant** is an independent mobile simulation tool that allows property owners to calculate government-subsidized urban regeneration loans, interest rate discount tiers, monthly installments, and minimum household income eligibility instantly.

### ⚙️ Key Features & Simulation Engine

The application streamlines complex legal regulations through interactive calculation algorithms and structured guidance:

* **Dynamic Loan Simulation:** Users select loan amounts up to 3,000,000 TRY via flexible sliders and choose loan maturities up to 180 months (15 years). The algorithm calculates monthly installments, total repayment, and overall interest load in real time.

* **Automated Subsidy Category Calculations:** Automatically calculates legal interest discount tiers up to the annual 1.25% maximum cap:
  1. *Category 1 (Single Home Owner):* 0.25% annual discount.
  2. *Category 2 (Low/Middle Income Households):* 0.25% annual discount based on provincial income caps across 81 cities.
  3. *Category 3 (Special Discount Group):* 0.25% annual discount for retirees, seniors (65+), disabled individuals, and building custodians.
  4. *Category 4 (Energy Performance Certificate):* Additional 0.50% discount for Class A energy rating or 0.25% for Class B.

* **Minimum Household Income Threshold (70% Rule):** Calculates the required minimum monthly household income based on bank creditworthiness criteria specifying that monthly loan installments must not exceed 70% of total household income.

* **5-Step Application Guide & Pilot Cities:** Features step-by-step application guidelines and coverage details for World Bank-supported *Climate and Disaster Resilient Cities Project* pilot provinces (Istanbul, Izmir, Manisa, Kahramanmaras, Tekirdag).

* **100% Local Storage & Modern UI:** Inputs are stored locally on the device (SharedPreferences) without external server transmission. Includes a sleek Dark and Light theme toggle.


### 📱 Application Screenshots

![Loan Simulator Screen](../../../docs/assets/kentsel-donusum/telefon1.png ':size=22%')  ![Guide & Info Screen](../../../docs/assets/kentsel-donusum/telefon2.png ':size=22%')


### 🛠️ Tech Stack

* **Mobile Framework:** Flutter (Dart)
* **State Management:** Provider
* **Local Storage & Architecture:** SharedPreferences *(Serverless / Local Data Storage)*
* **Build & Performance Optimization:** R8 Full Mode optimization, ProGuard Class Repackaging (`-repackageclasses ''`), AndroidX Room & WorkManager crash shields
* **UI & Styling:** Google Fonts, Material Design 3 (Dark/Light Theme), Google Mobile Ads (AdMob)

*(UI design, simulation algorithms, R8/ProGuard optimizations, and Play Store publishing workflows were developed with pair-programming assistance from **Google Antigravity / Gemini AI**.)*

---

### 📦 Store Links

Explore the live production build on Google Play:

**[View on Google Play Store](https://play.google.com/store/apps/details?id=com.kentsel.donusum)**

---

> <small>*⚠️ **Legal Disclaimer**<br> This application is an independent simulation tool and is not affiliated with, endorsed by, or representing any government ministry, official urban transformation authority, or financial institution. Calculations, rate simulations, and guidelines provided within the app are strictly for informational purposes. Final loan approvals, interest subsidy rates, and eligibility criteria are subject to official government regulations and partner bank procedures.*</small>
