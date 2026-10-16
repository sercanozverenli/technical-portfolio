---
# Urban Transformation Assistant (Mobile & Web Information and Simulation Platform)

Developed in alignment with the official public regulatory frameworks and guidelines of the Ministry of Environment, Urbanisation and Climate Change and the Urban Transformation Directorate, **Urban Transformation Assistant (Kentsel Dönüşüm Asistanı)** is an independent, multi-platform (Android & Web) simulation and guidance tool designed to empower property owners, tenants, and contractors with accessible, transparent insights into urban transformation processes, financial grants, state-backed loans, and legal rights.

### ⚙️ Key Features & Modular Architecture

The platform is architected around a centralized entry Hub that streamlines complex regulatory procedures into intuitive, standalone modules:

* **1. Financial Support Programs & Simulation Engines:**
  * **World Bank-Supported Low-Interest Loan Simulator:** Calculates monthly installments, total repayment obligations, a 12-month grace period benefit, and the 70% maximum debt-to-income (minimum household income) threshold for loan amounts up to 3,000,000 TRY and repayment terms up to 180 months (15 years).
  * **4-Tier Regulatory Interest Subsidy Engine:** Dynamically calculates state interest reductions within the maximum statutory cap of 1.25% per annum based on: *Single Property Ownership* (0.25 pts), *Low/Medium Income Households* (0.25 pts), *Special Inclusivity Groups (Retirees, disabled citizens, martyrs' and veterans' families)* (0.25 pts), and *Energy Performance Certificate (Class A: 0.50 pts / Class B: 0.25 pts)*.
  * **"Half on Us" (Yarısı Bizden) Campaign Guide:** A comprehensive financial overview of the 1,875,000 TRY total urban transformation support package (700,000 TRY non-refundable grant, 700,000 TRY subsidized loan with grace period, and 100,000 TRY relocation allowance), outlining payment milestones, eligibility conditions, and application procedures.
  * **Rental Subsidies & Municipal Support (Interactive Accordion):** An interactive summary detailing statutory rental assistance under Law No. 6306 (up to 18 months for titleholders and a one-off 2-month relocation payment for tenants), the 1-year e-Government (e-Devlet) application window, and supplemental logistic/financial contributions provided by metropolitan and district municipalities.

* **2. Demand & Proposal Coordination Module:**
  * A neutral digital coordination framework enabling property owners to register building renewal requests (parcel/island data) and contractors to submit structured project proposals based on clear urban renewal criteria.

* **3. Free Live Information & Inquiry Bridge:**
  * Direct deep-link integration with the WhatsApp Business API for instant, one-click access to live guidance and neutral informational support.

* **4. Cross-Platform & Adaptive Responsive Design:**
  * Unified Flutter codebase delivering native performance on Android devices and a responsive Single Page Application (SPA/PWA) experience across desktop and mobile web browsers.
  * Adaptive grid system that dynamically reflows layout constraints based on viewport width, validated with Google Search Console indexing.

* **5. 100% Client-Side Privacy & Modern User Experience:**
  * Absolute user privacy: zero cloud tracking of financial inputs, with all preferences and calculations stored strictly locally on-device via SharedPreferences.
  * Accessible, ergonomic Light and Dark themes engineered using a modern Slate & Emerald design system.


### 📱 Web & App Showcase

![Home Screen](../../../docs/assets/kentsel-donusum/telefon3.png ':size=22%')  ![Half on Us Screen](../../../docs/assets/kentsel-donusum/telefon4.png ':size=22%')  
![Loan Simulator Screen](../../../docs/assets/kentsel-donusum/telefon1.png ':size=22%')  ![Guide & Info Screen](../../../docs/assets/kentsel-donusum/telefon2.png ':size=22%')


### 🛠️ Technology Stack

* **Core Framework:** Flutter 3.x (Dart 3) — *Cross-Platform Android (AAB/APK) & Web (SPA/PWA)*
* **State Management:** Provider
* **Local Storage & Architecture:** SharedPreferences *(Serverless / Client-Side Persistence)*
* **UI & Typography:** Material Design 3, Google Fonts (Plus Jakarta Sans), Responsive Adaptive Layouts (`LayoutBuilder`, `MediaQuery`)
* **External Integrations:** URL Launcher (WhatsApp API, e-Devlet, and official regulatory portals)
* **Build & Performance Optimization:** R8 Full Mode optimization, ProGuard Class Repackaging (`-repackageclasses ''`), Icon Tree-Shaking, Web WASM / CanvasKit rendering compatibility

*(Architected and developed in collaboration with autonomous AI coding agents following modern pair-programming methodologies.)*

---

### 📦 Store & Web Links

Explore the live releases of the platform via the links below:

* **[Google Play Store (Android)](https://play.google.com/store/apps/details?id=com.kentsel.donusum)**
* **[Visit Web Platform](https://kentseldonusumasistani.com)** *(or deployed web endpoint)*

---

> <small>*⚠️ **Legal Disclaimer**<br> 
> Urban Transformation Assistant (Kentsel Dönüşüm Asistanı) is an independent informational and simulation platform with no official affiliation with the Ministry of Environment, Urbanisation and Climate Change, the Urban Transformation Directorate, or any government agency/commercial bank. In compliance with Law No. 6306 and its Implementing Regulations, the platform solely offers guidance, simulations, and informative content derived from publicly available government data; it does not engage in formal consultancy or broker services. Definitive loan approvals, interest subsidies, grant disbursements, and formal eligibility decisions remain entirely subject to the final discretion of official public bodies and authorized financial institutions.*</small>
