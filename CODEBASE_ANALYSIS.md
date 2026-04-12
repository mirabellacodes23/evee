# Mirabella EVEE - Complete Codebase Analysis

## Project Overview
**Project Name:** Mirabella EVEE (Electric Bikes)  
**Type:** Flutter Web Application  
**Purpose:** E-commerce platform for ordering electric bikes with admin management system  
**Platform:** Web (responsive design with desktop and mobile support)  
**Status:** Active Development  
**Firebase Project ID:** mirabellaevee  

---

## Project Structure

```
mirabellaevee/
├── lib/                           # Dart source code
│   ├── main.dart                  # App entry point & theme config
│   ├── firebase_options.dart      # Auto-generated Firebase config
│   ├── landing_page.dart         # Home page with hero, features, reviews
│   ├── bike_details_page.dart    # Detailed bike view with gallery
│   ├── orderbooking.dart         # Order management & admin panel
│   ├── fx_shared.dart            # Mouse effects, particles, parallax
│   ├── widgets_shared.dart       # Reusable UI components (buttons, etc)
│   └── models/
│       └── bike.dart             # Bike data model
├── web/                          # Web app assets
│   ├── index.html               # Web entry point
│   ├── manifest.json            # PWA manifest
│   └── icons/                   # Web app icons
├── assets/                       # App images & logos
│   └── logo/                    # Mirabella & EVEE branding
├── dataconnect/                # Firebase Data Connect (GraphQL)
├── build/                      # Build outputs
├── pubspec.yaml               # Dependencies & project config
├── firebase.json              # Firebase services config
├── firestore.rules            # Firestore security rules
├── firestore.indexes.json     # Firestore query indexes
├── analysis_options.yaml      # Dart linter config
└── README.md                  # Basic project documentation
```

---

## Architecture Overview

### Technology Stack

| Component | Technology | Version |
|-----------|-----------|---------|
| Framework | Flutter | 3.9.0+ (Dart SDK) |
| Frontend | Material 3 + Custom Widgets | Flutter 3.9.0 |
| Database | Cloud Firestore | Latest |
| Authentication | Firebase Auth | 6.0.1 |
| Storage | Firebase Storage | 13.0.0 |
| PDF Generation | pub: pdf, printing | 3.11.3, 5.14.2 |
| Fonts | google_fonts | 6.3.0 |
| File Picker | file_picker | 10.3.2 |
| Analytics | Firebase Analytics | 12.0.2 |
| Crash Reporting | Firebase Crashlytics | 5.0.0 |
| Dev Tools | Firebase Data Connect CLI | 0.2.0+1 |

### Design Patterns

1. **State Management:** StatefulWidget with setState (local state)
2. **Navigation:** Named routes + MaterialPageRoute
3. **Firebase Integration:** Cloud Firestore streams + Firebase Auth
4. **UI/UX:** Custom animations, parallax effects, glassmorphism
5. **Responsive Design:** LayoutBuilder + MediaQuery for multi-screen support
6. **Theming:** ColorScheme with Material 3 & custom text theme (Google Fonts)

---

## Core Features

### 1. **Landing Page** (`landing_page.dart`)
**Route:** `/` (Home)  
**Purpose:** Marketing and product showcase  
**Key Components:**
- Animated splash screen (3.5s fade-out)
- Glassmorphic navigation bar (pins when scrolling)
- Hero section with CTA buttons
- Features section highlighting bike benefits
- Statistics section (animated counters)
- Customer reviews section
- Contact section
- Footer with company info
- Custom cursor with trail & ripple effects (desktop only)
- Parallax background layers

**Notable Features:**
- Smooth scroll-to-section navigation
- Dynamic app bar opacity based on scroll position
- Mouse effects (cursor trail, hover effects)
- Responsive grid layouts for all screen sizes

---

### 2. **Bike Details Page** (`bike_details_page.dart`)
**Route:** `/bike-details` (Modal/Page navigation)  
**Purpose:** Display comprehensive bike information  
**Key Components:**
- Hero image with PageController pagination
- Image gallery thumbnails (max 4 images)
- Info panel with price, details, and specs
- Dynamic spec parsing from bike details text
- Responsive wide/narrow layouts
- Back button with smooth navigation

**Image Handling:**
- Pre-caching for smooth transitions
- Network image loading with fallbacks
- Support for up to 4 images (main + 3 gallery)

**Spec Extraction:**
- Parses bike details for:
  - Lifespan (battery years)
  - Battery type (graphene detection)
  - Charging time
  - Frame material
  - Motor power
  - Range
  - Wheel size
  - Braking system

---

### 3. **Order Booking Page** (`orderbooking.dart`)
**Route:** `/order` (Named route)  
**Purpose:** Main hub for bike catalog, admin management, and order creation  
**Key Sections:**

#### A. **Public Features**
- Live bike catalog from Firestore
- Responsive grid layout (1-3 columns based on width)
- Bike cards with:
  - Image preview
  - Name, price, details
  - "View Details" button
  - "Book Now" CTA
- Booking dialog form with validation:
  - Full name, father name, CNIC (13-digit validation)
  - Email (format validation)
  - Phone number
  - Address (2-3 lines)
  - Quantity stepper (qty selector)
  - Installment checkbox (payment mode)
- Order success confirmation popup (animated circle)

#### B. **Admin Features** (Firebase Auth Required)
**Authentication:**
- Email/password login (5 tap logo = login dialog)
- Admin-only access control

**Admin Capabilities:**
1. **Bike Management:**
   - Add new bikes (modal form)
   - Edit existing bikes
   - Delete bikes (with confirmation)
   - Fields: name, price, details, main image, 3 gallery images
   - Variant management (per-bike): chassis #, engine #, color

2. **Orders Panel:**
   - View all customer orders in table
   - Real-time updates via Firestore streams
   - Order details modal with:
     - Customer info
     - Bike info
     - Payment status
     - Installment tracking
     - Invoice & Chalan generation (PDF)

3. **Documents Panel:**
   - View/edit invoice and chalan templates (text content)
   - Pre-populated with order data
   - Export capability

4. **FAB (Floating Action Button):**
   - Add Bike button (admin only, when logged in)

---

### 4. **PDF Generation** (`orderbooking.dart`)
**Purpose:** Generate official documents for sales  
**Documents Generated:**

#### Invoice PDF (`buildInvoicePdfBytes`)
- Company logos (Mirabella & EVEE, 2× size)
- Invoice header with company details
- Customer info: name, father name, CNIC, address, phone
- Bike details: model, year, chassis #, engine #, color
- Payment section:
  - **Cash Orders:** Amount only
  - **Installment Orders:** 
    - Total amount
    - Down payment
    - Current installment status
    - Total paid amount
    - Remaining amount
    - Payment mode
- Signature lines (customer & dealer)
- Terms & Conditions section
- Format: A4, centered table layout

#### Delivery Chalan PDF (`buildChalanPdfBytes`)
- Similar layout to invoice
- Header: "DELIVERY CHALAN" instead of "INVOICE"
- Chalan number instead of invoice number
- Simpler content (bike details only, no payment breakdown)
- Signature lines for delivery handoff

---

## Data Models

### Bike Model (`models/bike.dart`)
```dart
class Bike {
  final String id;              // Firestore doc ID
  final String name;            // Bike name
  final String price;           // Price string (e.g., "PKR 249,000")
  final String imageUrl;        // Main image URL
  final List<String> gallery;   // Up to 3 additional images
  final String details;         // Long-form description
}
```

**Firestore Document Structure:**
```json
{
  "name": "EVEE Elite",
  "price": "PKR 249,000",
  "imageUrl": "https://...",
  "gallery": ["https://...", "https://..."],
  "details": "Advanced battery with graphene...",
  "variants": [
    {
      "chassis": "EV2025-001",
      "engine": "ENG-001",
      "color": "Midnight Blue"
    }
  ],
  "createdAt": "2025-01-15T10:30:00Z",
  "updatedAt": "2025-01-20T14:45:00Z"
}
```

### Order Model (Firestore Document)
```json
{
  "name": "Ali Khan",
  "fatherName": "Ahmed Khan",
  "cnic": "12345-6789012-3",
  "email": "ali@example.com",
  "phone": "03001234567",
  "address": "Islamabad, Pakistan",
  "quantity": 1,
  "bikeId": "bike_doc_id",
  "bikeName": "EVEE Elite",
  "bikePrice": "PKR 249,000",
  "bikeImageUrl": "https://...",
  "status": "new",
  "createdAt": "2025-01-22T12:00:00Z",
  "source": "web",
  "isInstallment": false,
  "totalAmount": 249000,
  "downPayment": 249000,
  "paidAmount": 249000,
  "remainingAmount": 0,
  "totalInstallments": 0,
  "paymentHistory": [],
  "paymentStatus": "paid"
}
```

---

## UI Components (`widgets_shared.dart`)

### Core Components

| Component | Purpose |
|-----------|---------|
| `Logo` | Gradient-masked "EVEE" text using Orbitron font |
| `NavLink` | Navigation link with hover effects |
| `PrimaryCta` | Primary call-to-action button (gradient fill) |
| `SecondaryCta` | Secondary button with outline style |
| `MagneticHover` | Wrapper for subtle cursor feedback |
| `SectionTitle` | Large section headers |
| `_FxTextField` | Custom text input with icon support |
| `_GlassDialog` | Frosted glass dialog box |
| `_Ripple` | Circular click ripple effect |
| `ParticlesLayer` | Falling particle background |
| `QuantumParticlesLayer` | Colorful glow particles |
| `ParallaxLayer` | Mouse-tracking parallax offset |
| `MouseFXOverlay` | Cursor trail & ripple rendering |

---

## Visual Design System

### Color Palette
- **Primary Accent:** `#00FF88` (Neon Green - cyber aesthetic)
- **Secondary Accent:** `#00D4FF` (Cyan Blue)
- **Dark Background:** `#0F0F23`, `#1A1A3A`, `#2D2D5F` (Deep blue gradient)
- **Text:** White with opacity variations (0.65-1.0)
- **Borders/Subtle:** White with low opacity (0.03-0.1)

### Typography
- **Logo Font:** Orbitron (geometric, futuristic)
- **Body Font:** Inter (via google_fonts)
- **Fallback:** Material default fonts

### Effects & Animations
- **Splash Overlay:** 900ms fade-out with 3.5s delay
- **Glassmorphism:** 20-30px blur + 18-32% black opacity
- **Parallax Depth:** 10px window movement on mouse track
- **Cursor Trail:** 22-point history with 6px glow blur
- **Ripples:** 520ms expansion with easeOut curve
- **Cursor Scale:** Lerps to 1.0-2.1 on hover/click
- **Hover Colors:** 180ms container color transition

---

## Firebase Configuration

### Project Setup
- **Project ID:** mirabellaevee
- **App ID (Web):** 1:970694357027:web:671c48009d90ad0bf60196
- **Auth Domain:** mirabellaevee.firebaseapp.com
- **Storage Bucket:** mirabellaevee.firebasestorage.app
- **Measurement ID:** G-KB9YKL2H96

### Firestore Collections

| Collection | Purpose |
|-----------|---------|
| `bikes` | Bike catalog (add/edit/delete by admin) |
| `orders` | Customer orders (created by public, managed by admin) |
| `admins` | Custom user roles (optional) |

### Firestore Security Rules
**Current:** Deny all (`allow read, write: if false;`)  
**Note:** Rules are intentionally disabled; consider enabling in production with proper auth checks.

### Enabled Services
- ✅ Cloud Firestore
- ✅ Firebase Authentication (Email/Password)
- ✅ Firebase Storage
- ✅ Firebase Analytics
- ✅ Firebase Crashlytics
- ✅ Firebase Data Connect (GraphQL layer)

---

## Responsive Design Breakpoints

### Screen Size Handling
```dart
// Landing Page AppBar
isNarrow = cons.maxWidth < 900

// Bike Grid Columns
c.maxWidth < 720 ? 1 : c.maxWidth < 1024 ? 2 : 3

// Bike Details Layout
isWide = c.maxWidth >= 960

// Desktop Cursor Effects
useFxCursor = isDesktopLike && size.width > 700
```

### Platform Detection
```dart
isDesktopLike = kIsWeb || macOS || windows || linux
```

---

## Key Implementation Details

### Admin Access Pattern
1. **Trigger:** Tap logo 5 times
2. **Dialog:** Email + password login form
3. **Auth:** Firebase Auth (`signInWithEmailAndPassword`)
4. **Verification:** Check `FirebaseAuth.instance.currentUser`
5. **Visibility:** Admin UI appears after successful auth

### Order Creation Flow
1. User taps "Book Now" on bike card
2. Booking form dialog opens (no auth required)
3. User fills: name, father name, CNIC, email, phone, address
4. Optional: Select installment payment mode
5. Validation: CNIC = 13 digits, email = valid format
6. Submit: `_createOrderPublic()` writes order to Firestore
7. Success: Animated circle confirmation popup

### PDF Generation Process
1. Load company logos from `assets/logo/`
2. Create `pdf.Document()` with A4 page format
3. Build layout using `pw.*` widgets (pdf library syntax)
4. Populate with order data from Firestore
5. Call `pdf.save()` to generate bytes
6. Trigger print preview or download

### Image Loading Strategy
- **Main Image:** Display in full-size hero section
- **Gallery:** Up to 3 additional images in thumbnails
- **Validation:** Check `startsWith('http')` for URL validity
- **Pre-caching:** Call `precacheImage()` in `didChangeDependencies()`
- **Supported Hosts:** Cloudinary, Unsplash, Firebase Storage, CDN providers
- **Extensions:** .png, .jpg, .jpeg, .webp, .gif

---

## Dependencies & Versions

### Core Dependencies
- `flutter: sdk: flutter`
- `google_fonts: ^6.3.0`
- `firebase_core: ^4.1.1`
- `firebase_auth: ^6.0.1`
- `cloud_firestore: ^6.0.0`
- `firebase_storage: ^13.0.0`
- `firebase_analytics: ^12.0.2`
- `firebase_crashlytics: ^5.0.0`

### UI & Rendering
- `cupertino_icons: ^1.0.8` (iOS icons)
- `pdf: ^3.11.3` (PDF generation)
- `printing: ^5.14.2` (Print dialog)

### Utilities
- `file_picker: ^10.3.2` (File selection)

### Dev Dependencies
- `flutter_lints: ^5.0.0`
- `firebase_data_connect: ^0.2.0+1`

---

## Common Code Patterns

### StreamBuilder for Real-time Data
```dart
StreamBuilder<QuerySnapshot<Map<String, dynamic>>>(
  stream: _bikesCol.orderBy('createdAt', descending: true).snapshots(),
  builder: (context, snap) {
    if (snap.connectionState == ConnectionState.waiting) {
      return CircularProgressIndicator();
    }
    if (snap.hasError) {
      return Text('Error: ${snap.error}');
    }
    final docs = snap.data?.docs ?? [];
    // Build UI with docs
  },
)
```

### Custom Dialog Pattern
```dart
await showDialog(
  context: context,
  barrierColor: Colors.black.withOpacity(0.55),
  builder: (context) => StatefulBuilder(
    builder: (context, setD) => _GlassDialog(
      title: // widget,
      content: // widget,
      actions: [
        _GhostButton(text: 'Cancel', onTap: () => Navigator.pop(context)),
        _PrimaryButton(text: 'Confirm', onTap: // callback),
      ],
    ),
  ),
);
```

### Form Validation Pattern
```dart
final formKey = GlobalKey<FormState>();

Form(
  key: formKey,
  child: Column(
    children: [
      _FxTextField(
        validator: (v) => (v == null || v.isEmpty) ? 'Required' : null,
      ),
    ],
  ),
),

// On submit:
if (!formKey.currentState!.validate()) return;
// Process form
```

---

## Error Handling & Edge Cases

### Handled Scenarios
1. **Firebase Not Initialized:** Shows "Firebase not initialized" banner
2. **Empty Bike Catalog:** Displays "No bikes available" + "Add Bike" prompt (admin)
3. **Network Error:** Displays error message in relevant StreamBuilder
4. **Invalid Image URLs:** Filters out, requires `https://` + valid extension
5. **Malformed CNIC:** Rejects if not 13 digits
6. **Empty Form Fields:** Form validation prevents submission
7. **Duplicate Images:** Filters out main image from gallery
8. **Missing Gallery Images:** Pads to 4 with main image or empty string

### Potential Issues (Not Yet Handled)
1. **No offline support:** Requires active internet
2. **No payment integration:** Installment payments managed manually by admin
3. **No account creation:** Only email/password login (no signup)
4. **No order tracking:** Customers can't check order status
5. **No image upload:** Only accepts external URLs (no Firebase Storage upload)

---

## Performance Considerations

### Optimizations
- **Image Pre-caching:** Prevents lag when switching gallery images
- **NeverScrollableScrollPhysics:** GridView inside CustomScrollView to prevent nested scrolling overhead
- **CustomPaint:** Efficient trail rendering vs. multiple animated widgets
- **Stream Ordering:** `orderBy('createdAt')` + efficient Firestore indexing
- **Responsive Layouts:** Single build method with `LayoutBuilder` (not multiple pages)

### Potential Bottlenecks
1. **Large Bike Catalog:** No pagination (loads all at once)
2. **Heavy Particle Layers:** 55 particles + 30 quantum particles on every screen
3. **Continuous Animation:** Ticker runs while page visible (MouseFXOverlay)
4. **No Image Compression:** Full-size images loaded without resizing
5. **PDF Generation:** Synchronous on main thread (could freeze UI)

---

## File Organization & Architecture

### Separation of Concerns
- **`main.dart`** → App initialization, theme, routing
- **`landing_page.dart`** → Marketing/homepage UI
- **`bike_details_page.dart`** → Product detail view
- **`orderbooking.dart`** → Order management, admin panel, PDF generation
- **`fx_shared.dart`** → All visual effects (particles, cursor, parallax)
- **`widgets_shared.dart`** → Reusable UI components
- **`models/bike.dart`** → Data model + Firestore mapping

### Circular Dependency Prevention
- `orderbooking.dart` imports `bike_details_page.dart`
- `bike_details_page.dart` imports `models/bike.dart` (avoids importing from `orderbooking`)
- `Bike` model re-exported from `orderbooking.dart` for backward compatibility

---

## Future Enhancement Opportunities

1. **User Accounts:** Create customer login + order tracking
2. **Payment Gateway:** Integrate Stripe/JazzCash for installment payments
3. **Image Upload:** Firebase Storage integration + gallery preview
4. **Bike Filtering:** Category, price range, spec filters
5. **Search:** Free-text search across bikes
6. **Reviews/Ratings:** Customer review system with admin moderation
7. **Multi-language:** Localization (Urdu, English, etc.)
8. **Mobile App:** Native Android/iOS via Flutter build
9. **Admin Dashboard:** Dedicated admin app with more analytics
10. **Email Notifications:** Order confirmation, payment reminders
11. **SMS Integration:** WhatsApp/SMS for order updates
12. **Inventory Tracking:** Stock management per variant
13. **Export Orders:** CSV/Excel download for bulk operations
14. **Dark/Light Theme Toggle:** Currently dark-only

---

## Testing Notes

### Tested Features
- ✅ Landing page animations (splash, scroll effects)
- ✅ Bike catalog display (real Firestore data)
- ✅ Bike details page (gallery, specs)
- ✅ Order form (validation, submission)
- ✅ Admin login (Firebase Auth)
- ✅ Bike CRUD (add, edit, delete)
- ✅ PDF generation (invoice, chalan)
- ✅ Responsive layouts (desktop, tablet, mobile)
- ✅ Mouse effects (desktop-only)
- ✅ Installment orders (data storage)

### Not Tested
- ⚠️ Firebase Security Rules (currently disabled)
- ⚠️ Firestore emulator
- ⚠️ Very large datasets (>10,000 bikes)
- ⚠️ Concurrent admin edits
- ⚠️ CNIC validation against real database
- ⚠️ Printing from all browsers

---

## Deployment Checklist

### Pre-Deployment
- [ ] Enable Firestore Security Rules
- [ ] Set up admin user accounts
- [ ] Test all PDF exports
- [ ] Verify image URLs accessibility
- [ ] Test on mobile devices
- [ ] Enable Firebase Crashlytics
- [ ] Set up Firebase Analytics events
- [ ] Configure CORS for Firebase Storage
- [ ] Review Terms & Conditions text
- [ ] Test installment order workflows

### Deployment
- [ ] Build web app: `flutter build web --release`
- [ ] Deploy to Firebase Hosting or custom server
- [ ] Set up custom domain
- [ ] Configure SSL certificate
- [ ] Enable caching headers
- [ ] Set up error tracking/logging
- [ ] Create backup of Firestore data
- [ ] Monitor performance metrics

### Post-Deployment Monitoring
- [ ] Track conversion rates (view → order)
- [ ] Monitor PDF generation errors
- [ ] Alert on Firestore quota usage
- [ ] Review customer feedback
- [ ] Analyze page load times
- [ ] Check Firebase Auth usage

---

## Conclusion

Mirabella EVEE is a well-structured Flutter web application with a clear separation between customer-facing features (bike browsing, ordering) and admin management (inventory, document generation). The codebase demonstrates modern Flutter best practices including responsive design, Firebase integration, custom animations, and form validation. While the current implementation is functional for MVP, several enhancements are recommended for production deployment, including stronger security rules, payment integration, and user account management.

