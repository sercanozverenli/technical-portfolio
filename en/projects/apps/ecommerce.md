# Mantı Mood - Boutique Handmade Manti & Gourmet E-Commerce Platform (Web & Mobile)

Developed to combine traditional culinary heritage with a modern digital commerce experience, **Mantı Mood** is a comprehensive cross-platform e-commerce ecosystem operating on both Web and Mobile (Android). It consolidates boutique handmade manti and gourmet product order management, dynamic product/pricing cataloging, and multi-channel order notifications via WhatsApp and email under a unified platform.

### ⚙️ System Mechanics & Key Features

The platform is built on a high-performance hybrid architecture, a cloud-native real-time database, and an interactive administrative dashboard designed to streamline business operations:

* **Real-Time Product Catalog & Dynamic Cart Management:** Customers can customize products based on weight, portion size, and sauce variations; experiencing instant cart recalculations with dynamic pricing, delivery fee algorithms, and minimum order threshold validation. Two-way synchronization with the backend database occurs in real time without requiring page reloads.

* **Multi-Channel Ordering & Automated Invoice Delivery:** Completed orders are instantly formatted and dispatched directly to the business WhatsApp order line along with customer delivery details and itemized order breakdowns. Concurrently, an SMTP service triggered via Supabase Edge Functions delivers an elegant HTML order confirmation and invoice directly to both the customer and management.

* **Role-Based Secure Admin Dashboard:** Accessible exclusively to authorized (whitelisted) administrator accounts, this management panel enables adding, updating, or removing products, adjusting prices on the fly, dynamically managing promotional campaign banners (with integrated image crop and zoom ratios), and tracking active order statuses in real time.

* **Seamless Multi-Platform (Web & Mobile) Integration:** Compiled from a single Flutter codebase, the system runs as a fast Single-Page Application (SPA/PWA) on web browsers while delivering native Android performance optimized with local network permissions, smart image caching (CachedNetworkImage), and pull-to-refresh live data synchronization.

* **Serverless & Secure Cloud Architecture:** Customer and order records are protected within Supabase PostgreSQL using strict Row Level Security (RLS) policies and encrypted connections. Sensitive backend processes, including transactional email dispatches, are executed securely via isolated Deno/TypeScript microservices without exposing private API keys to the client.


### 📱 Application Screenshots

![Home Screen & Menu](<../../../docs/assets/mantimood/tel1.png ':size=20%'>)  ![Product Details & Sauce Selection](<../../../docs/assets/mantimood/tel2.png ':size=20%'>)  ![Cart & Quick Checkout](<../../../docs/assets/mantimood/tel3.png ':size=20%'>)  ![Admin Dashboard](<../../../docs/assets/mantimood/tel4.png ':size=20%'>)


### 🛠️ Tech Stack

* **Cross-Platform Framework:** Flutter (Dart) — *Unified Codebase for Web & Native Android*
* **State Management:** Provider
* **Backend & Database:** Supabase (PostgreSQL, Realtime Data Sync, Row Level Security - RLS)
* **Serverless Functions:** Supabase Edge Functions (Deno / TypeScript)
* **Communication & Notification Services:** Gmail SMTP Integration, Custom HTML Email Template Engine, WhatsApp Business API Deep-Linking
* **Web Hosting & Deployment:** Cloudflare Pages (Global CDN & Edge Delivery)
* **Mobile Infrastructure & Security:** Android Keystore (V1/V2 Signed AAB & APK), ProGuard R8 Optimization, Google Sign-In (OAuth 2.0)
* **UI & Graphics:** Material Design 3, Google Fonts, Shimmer Loading Effects, CachedNetworkImage Architecture

*(Built in collaboration with AI-assisted autonomous software engineering agents.)*

---

### 📦 Live Demo & Source Code

Access the live web deployment and repository via the links below:

* **[Explore Live Web App](https://mantimood.pages.dev)**
* **[GitHub Repository](https://github.com/FabMorph/manti-mood)**

---

*⚠️ **Order & Service Legal Disclaimer***

*Orders placed through the Mantı Mood platform are freshly handmade in compliance with culinary hygiene and food safety standards. Delivery schedules may vary based on destination and active order volume. For allergen details and dietary preferences, please reach out via our WhatsApp support channel prior to checkout.*
