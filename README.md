# 🛒 ApexMarket — E-Commerce Storefront & Deduplicated Wishlist Engine (v1)

A modern, high-performance, single-page e-commerce technical prototype built with vanilla JavaScript, HTML5, and Tailwind CSS. The platform enables users to browse a 18-product catalog, manage multiple dynamic wishlists in `localStorage`, and execute **item-level deduplicated merges** between source collections.

---

## 📌 Table of Contents

1. [Overview](https://www.google.com/search?q=%23-overview)
2. [Key Features](https://www.google.com/search?q=%23-key-features)
3. [Technical Specification (v1 Alignment)](https://www.google.com/search?q=%23-technical-specification-v1-alignment)
4. [Architecture & Algorithmic Logic](https://www.google.com/search?q=%23-architecture--algorithmic-logic)
* [Merging & Deduplication Algorithm](https://www.google.com/search?q=%231-merging--deduplication-algorithm)
* [Reactive UI Rendering Pipeline](https://www.google.com/search?q=%232-reactive-ui-rendering-pipeline)
* [Data Persistence](https://www.google.com/search?q=%233-data-persistence)


5. [UI / UX Highlights](https://www.google.com/search?q=%23-ui--ux-highlights)
6. [Getting Started & Local Execution](https://www.google.com/search?q=%23-getting-started--local-execution)
7. [GitHub Pages Deployment](https://www.google.com/search?q=%23-github-pages-deployment)
8. [Project Structure](https://www.google.com/search?q=%23-project-structure)

---

## 🌟 Overview

In modern e-commerce applications, users often maintain disjointed saved-for-later lists across devices or categories (e.g., "Office Upgrades" vs. "Audio Setup"). **ApexMarket** solves list fragmentation by providing an intuitive control panel that merges any two selected wishlists into a single consolidated list while guaranteeing zero product redundancy.

---

## ⚡ Key Features

* **📦 Responsive Product Catalog (18 Items):**
* Styled cards displaying item categories, live prices, image fallbacks, and target list selector dropdowns.


* **🔁 De-duplicated Wishlist Merging Engine:**
* Combines items from List A and List B.
* Prevents duplicate product insertions using set-based `$id$` lookup verification ($O(N + M)$ execution).


* **🧹 Dynamic Source Cleanup (Optional Flag):**
* Includes a UI checkbox option: *"Delete original source wishlists after successful merge"*.


* **🛡 Validation & State Safety:**
* Real-time input monitoring prevents merging a wishlist with itself.
* Disables execution triggers and surfaces alert blocks when selections are invalid.


* **💾 Local Storage Persistence:**
* Persists all additions, dynamic list creations, deletions, and merges across page refreshes.


* **🎨 70/30 Screen Space Utilization & Dual-Scroll Architecture:**
* Responsive 12-column layout (8 columns for Product Catalog, 4 columns for Control Center).
* Independent vertical scroll containers preventing UI layout shift.


* **🔔 Native Custom Overlays & Toasts:**
* Zero default browser `alert()` or `confirm()` interruptions—uses custom animated HTML modals and non-blocking toast popups.



---

## 📐 Technical Specification (v1 Alignment)

### Data Entities

#### Product Shape

```typescript
interface Product {
  id: string;          // e.g., "prod-101"
  name: string;        // Item display title
  price: number;       // Floating point number (e.g., 249.99)
  category: string;    // Category tag (e.g., "Audio", "Peripherals")
  image: string;       // Direct URL link or SVG fallback string
}

```

#### Wishlist Shape

```typescript
interface Wishlist {
  id: string;          // Dynamic timestamp identifier (e.g., "wl-171000000")
  name: string;        // Custom collection title (e.g., "Dream Studio Rig")
  items: Product[];    // Array containing Product objects
}

```

---

## 🧠 Architecture & Algorithmic Logic

### 1. Merging & Deduplication Algorithm

The merge routine combines two wishlists ($A$ and $B$) without duplicating items shared by both lists.

$$A = \{p_{A1}, p_{A2}, \dots\}, \quad B = \{p_{B1}, p_{B2}, \dots\}$$

* **Step 1:** Create a Hash Set (`seenIds`) seeded with all product IDs from `Wishlist A`.
* **Step 2:** Deep clone `Wishlist A.items` into `deduplicatedItems` array (preserving original placement order).
* **Step 3:** Iterate through `Wishlist B.items`. For each item $p$:
* If $p.id \notin seenIds$:
* Add $p.id$ to `seenIds`.
* Append $p$ to `deduplicatedItems`.




* **Step 4:** Construct the new merged entity and write updated collections directly to `localStorage`.

```javascript
// High-Performance Deduplication Implementation
const seenIds = new Set(listA.items.map(item => item.id));
const deduplicatedItems = [...listA.items];

listB.items.forEach(item => {
  if (!seenIds.has(item.id)) {
    seenIds.add(item.id);
    deduplicatedItems.push(item);
  }
});

```

### 2. Reactive UI Rendering Pipeline

Every state-mutating operation (`handleAddToWishlist`, `removeItemFromWishlist`, `executeDelete`, `handleMerge`, `handleCreateWishlist`) triggers a single unified renderer:

```
State Change Trigger ──> saveWishlists() ──> renderAllComponents()
                                              ├── renderProducts()
                                              ├── renderWishlistsView()
                                              ├── populateDropdowns()
                                              └── validateMergeInputs()

```

### 3. Data Persistence

All operations synchronize with the browser's storage layer:

* **Storage Key:** `app_wishlists_v1`
* **Default Seeding:** On first boot, if `localStorage` contains no records, the system populates initial preset wishlists (*"Dream Studio Rig"*, *"Productivity Office"*, and *"Audio Engineer Essentials"*).

---

## 🎨 UI / UX Highlights

* **Resilient Image Fallback Engine:** Every image element incorporates an inline vector generator (`onerror="handleImageError(this, ...)"`). If an external asset link is blocked by ad-blockers or CORS rules, the site seamlessly renders an SVG data-URI vector graphic without breaking layout card proportions.
* **Empty State Visuals:** Lists without items display cleanly formatted empty state graphics encouraging catalog additions.
* **Color Hierarchy:** Slate-900 header navigation, Indigo-600 active triggers, Emerald-500 status indicators, and Rose-600 delete alerts provide clear visual affordances.

---

## 🚀 Getting Started & Local Execution

Because **ApexMarket** is built as a single-file application with zero external framework dependencies, no Node.js runtime or build pipelines (npm/webpack) are required.

### Steps to Run Locally

1. **Clone the repository:**
```bash
git clone https://github.com/YOUR_USERNAME/apexmarket-wishlist.git
cd apexmarket-wishlist

```


2. **Launch the application:**
* Double-click `index.html` to open it directly in any modern standard web browser (Chrome, Firefox, Edge, Safari).
* Alternatively, serve it using VS Code's **Live Server** extension or Python's HTTP module:
```bash
python3 -m http.server 8000

```


Navigate to `http://localhost:8000` in your browser.



---

## 🌐 GitHub Pages Deployment

To deploy this project online via **GitHub Pages**:

1. Push your code to your GitHub repository's main branch:
```bash
git add index.html README.md
git commit -m "Deploy v1 production release"
git push origin main

```


2. Navigate to your repository on GitHub.
3. Click **Settings** -> **Pages** (under the Code and automation section).
4. Under **Build and deployment**:
* **Source:** Select `Deploy from a branch`.
* **Branch:** Select `main` / `/ (root)`.


5. Click **Save**.
6. Your site will be live within 1–2 minutes at:
`https://<YOUR_GITHUB_USERNAME>.github.io/<REPOSITORY_NAME>/`

---

## 📁 Project Structure

```text
├── index.html         # Main entry point containing standard layout, CSS, and JS logic
└── README.md          # Project technical documentation & assessment overview

```

---

*Developed for technical assessment evaluation.*
