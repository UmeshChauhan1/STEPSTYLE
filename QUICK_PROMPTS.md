# Quick Reference Prompts for AI Code Generation

## COPY & PASTE THESE PROMPTS TO YOUR AI ASSISTANT

---

## TIER 1: ESSENTIAL FEATURES (Must Have)

### 1️⃣ USER LOGIN & SIGNUP SYSTEM

**Full Stack Prompt:**
```
Create a complete user authentication system with:

BACKEND (Node.js/Express):
- POST /api/auth/register endpoint:
  * Accept: email, password, name
  * Validate email uniqueness and password strength (min 8 chars, uppercase, number)
  * Hash password using bcrypt (salt rounds: 10)
  * Create user in MongoDB
  * Return JWT token (7 day expiry) and user info
  
- POST /api/auth/login endpoint:
  * Accept: email, password
  * Verify email exists and password matches
  * Return JWT token and user info
  * Handle errors: invalid email, wrong password, account locked

- JWT middleware for protected routes:
  * Verify token from Authorization header
  * Attach user data to req.user
  * Return 401 if invalid/expired

- GET /api/user/profile endpoint (protected):
  * Return logged-in user's profile data

Database schema:
{
  _id: ObjectId,
  email: string (unique),
  name: string,
  passwordHash: string,
  phone: string,
  createdDate: Date,
  isActive: boolean
}

FRONTEND (HTML/JavaScript):
- signup.html page with form validation
- login.html page with remember me option
- Store JWT in localStorage
- Add JWT to all API requests automatically
- Redirect to login if unauthorized
- Show current user name in navbar if logged in
- Logout button that clears token and redirects

Security: Use HTTPS, secure cookies, CORS, rate limiting
```

---

### 2️⃣ PRODUCT VARIANTS (Sizes & Colors)

**Full Stack Prompt:**
```
Extend products to support sizes and colors:

BACKEND:
- Add productVariants collection:
  {
    _id: ObjectId,
    productId: ObjectId,
    size: number (6-13),
    color: string,
    colorHex: string,
    imageUrl: string,
    stock: number,
    sku: string
  }

- Update product retrieval to include all variants
- GET /api/products/:id returns product with variants array
- Update cart to include: productId, variantId, size, color
- Check variant stock before adding to cart

FRONTEND:
- product-detail.html enhancements:
  * Size selector: buttons for each available size, disabled if out of stock
  * Color selector: color swatches, changes main image
  * Stock indicator: "Only 3 left" for low stock
  * Size chart modal with measurements
  
- Product cards show:
  * Available sizes
  * Available colors as dots
  
- Cart shows:
  * Product name - Size 10 - Black - Qty 2
```

---

### 3️⃣ PAYMENT WITH STRIPE

**Full Stack Prompt:**
```
Integrate Stripe payment processing:

BACKEND:
- npm install stripe
- Create POST /api/payment/create-intent:
  * Accept: amount, currency, email
  * Create Stripe PaymentIntent
  * Return clientSecret
  * Handle errors

- Create POST /api/payment/confirm:
  * Verify payment status with Stripe
  * If successful: create order and clear cart
  * Log transaction: {stripeId, orderId, amount, date, status}
  * Return order details

- Webhook handler for Stripe events:
  * charge.succeeded → update order status
  * charge.failed → notify user

FRONTEND:
- checkout.html:
  * Install @stripe/js
  * Create Stripe Elements form (cardElement)
  * On "Place Order": 
    - Get clientSecret from /api/payment/create-intent
    - Call stripe.confirmCardPayment()
    - On success: show confirmation page
    - On error: show error message

- Add test cards:
  * Success: 4242 4242 4242 4242
  * Decline: 4000 0000 0000 0002

Security: Never expose secret key, use environment variables
```

---

### 4️⃣ REAL-TIME ORDER TRACKING

**Full Stack Prompt:**
```
Create order management and tracking system:

BACKEND:
- Orders collection:
  {
    _id: ObjectId,
    orderId: "SS-20250311001",
    userId: ObjectId,
    items: [{productId, variantId, qty, price}],
    subtotal: number,
    tax: number,
    shipping: number,
    total: number,
    customerInfo: {name, email, phone, address},
    status: "Pending|Confirmed|Shipped|Delivered",
    trackingNumber: string,
    estimatedDelivery: Date,
    createdDate: Date
  }

- POST /api/orders:
  * Create from cart
  * Generate orderId: "SS-" + Date + random
  * Calculate tax (8%) and shipping
  * Deduct stock from variants
  * Save order
  * Clear user's cart
  
- GET /api/orders (protected): Return user's orders

- GET /api/orders/:orderId (protected): Return order details

- PUT /api/orders/:orderId/status (admin): Update status, send email notification

FRONTEND:
- order-tracking.html page showing:
  * Order number, date
  * Visual timeline: Pending → Confirmed → Shipped → Delivered
  * Current status highlighted
  * Tracking number with copy button
  * Items ordered with details
  * Estimated delivery date
  * Customer address
  
- order-history.html showing:
  * All user's orders as cards/table
  * Quick filters: All, Pending, Shipped, Delivered
  * Click order to see details
  
- Confirmation page after checkout showing:
  * "Order Confirmed!" message
  * Order number
  * "Track your order" button
```

---

### 5️⃣ ADVANCED PRODUCT FILTERING

**Full Stack Prompt:**
```
Add professional filtering to shop page:

BACKEND:
- GET /api/products/filter endpoint with query params:
  * q: search text (searches name, description)
  * category: filter by category
  * minPrice, maxPrice: price range
  * size: available size
  * color: available color
  * sort: price_asc, price_desc, newest, rating, popular
  * page: pagination (default 1)
  * limit: per page (default 12)
  
  Returns: {products: [], totalCount: number, totalPages: number}

- Implement full-text search using database text index
- Handle pagination with skip/limit

FRONTEND:
- shop.html enhancements:
  * Left sidebar with filters:
    - Category checkboxes
    - Price range slider (min $0, max $200)
    - Size checkboxes
    - Color swatches
  
  * Top toolbar:
    - Sort dropdown
    - Results showing "1-12 of 48"
    - Search input with autocomplete
  
  * Active filters shown as removable tags
  * Product grid updates dynamically
  * "No results" message if none found
  * Pagination buttons at bottom
  
  * URL updates with filter params (shareable filtered links)
```

---

## TIER 2: ENGAGEMENT FEATURES

### 6️⃣ PRODUCT REVIEWS & RATINGS

**Backend Prompt:**
```
Create review and rating system:

Collections:
Reviews:
{
  _id: ObjectId,
  productId: ObjectId,
  userId: ObjectId,
  rating: number (1-5),
  title: string,
  text: string,
  isVerifiedPurchase: boolean,
  isApproved: boolean (default false),
  images: string[],
  helpfulCount: number,
  createdDate: Date
}

RatingSummary:
{
  productId: ObjectId,
  averageRating: number,
  totalReviews: number,
  distribution: {1: 2, 2: 3, 3: 5, 4: 25, 5: 60}
}

API Endpoints:
- POST /api/reviews: Add review (protected, check verified purchase)
- GET /api/reviews/:productId: Get all approved reviews, sortable
- PUT /api/reviews/:reviewId/helpful: Mark helpful
- PUT /api/admin/reviews/:reviewId/approve: Approve review
- DELETE /api/admin/reviews/:reviewId: Delete review
```

**Frontend Prompt:**
```
Add review UI to product-detail.html:

- Rating summary section:
  * Large average star rating (4.5 ★)
  * "X customer reviews" link
  * "Write a review" button
  * Rating distribution bars (5★ 60%, 4★ 25%, etc.)

- Review list:
  * Sort: Most Helpful / Newest / Highest Rating
  * Each review shows:
    - Star rating
    - Title and review text
    - Author and date
    - "Verified Purchase" badge
    - Helpful/Unhelpful buttons with counts
    - Review photos if any

- Write review modal (if user logged in + purchased):
  * Star rating input (clickable)
  * Title input
  * Review text textarea
  * Optional image upload
  * Submit button
```

---

### 7️⃣ WISHLIST / SAVE FOR LATER

**Backend Prompt:**
```
Create wishlist system:

Wishlist collection:
{
  _id: ObjectId,
  userId: ObjectId,
  productId: ObjectId,
  variantId: ObjectId (optional),
  notifyWhenInStock: boolean,
  createdDate: Date
}

Endpoints:
- POST /api/wishlist: Add to wishlist (protected)
- GET /api/wishlist: Get user's wishlist (protected)
- DELETE /api/wishlist/:itemId: Remove from wishlist
- PUT /api/wishlist/:itemId/notify: Toggle stock alert
```

**Frontend Prompt:**
```
Add wishlist features:

- Wishlist button on each product:
  * Heart icon (outline/filled)
  * Click to add/remove
  * Show top wishlist count in navbar
  * Require login to save

- wishlist.html page:
  * Display all wishlisted items as cards
  * Show product image, name, price, stock status
  * "Add to Cart" button per item
  * Remove from wishlist (X button)
  * Empty state icon
  * Sort/filter options
```

---

### 8️⃣ COUPONS & DISCOUNTS

**Backend Prompt:**
```
Create coupon system:

Coupons collection:
{
  _id: ObjectId,
  code: string (unique, uppercase),
  description: string,
  discountType: "percent" | "flat",
  discountValue: number,
  minPurchase: number,
  maxUses: number,
  maxPerUser: number,
  applicableProducts: ObjectId[],
  startDate: Date,
  expiryDate: Date,
  isActive: boolean
}

CouponUsage:
{
  _id: ObjectId,
  couponId: ObjectId,
  userId: ObjectId,
  orderId: ObjectId,
  appliedDate: Date
}

Endpoints:
- POST /api/coupons/validate: Validate coupon code
  Returns: {valid: true, discountAmount: 25, message: "SAVE25 applied"}
  Or: {valid: false, message: "Expired coupon"}
- POST /api/admin/coupons: Create coupon (admin)
- GET /api/admin/coupons: List all coupons (admin)
```

**Frontend Prompt:**
```
Add coupon input to checkout.html:

- Coupon section:
  * Text input: "Do you have a coupon code?"
  * "Apply" button
  * Show applied coupon with remove option
  
- Show discount in order total:
  * Subtotal: $100
  * Coupon SAVE25: -$25
  * Shipping: $10
  * Tax: $6.80
  * Total: $91.80 ← Discounted amount highlighted

- Error messages:
  * "Invalid coupon"
  * "Coupon expired"
  * "Minimum $50 order required"
  * "Already used"
```

---

## TIER 3: OPERATIONAL FEATURES

### 9️⃣ MULTIPLE SHIPPING OPTIONS

**Backend Prompt:**
```
Create shipping calculation:

POST /api/shipping/calculate:
- Input: zipCode, orderTotal, weight
- Return available methods:
  [
    {method: "Standard", days: "3-5", cost: 0, estimatedDelivery: "March 15"},
    {method: "Express", days: "1-2", cost: 15.99, estimatedDelivery: "March 13"},
    {method: "Overnight", days: "Next day", cost: 24.99, estimatedDelivery: "March 12"}
  ]

Logic:
- Standard: Free if subtotal > $75, else $9.99
- Express: $15.99
- Overnight: $24.99
```

**Frontend Prompt:**
```
Add shipping selector to checkout.html:

- Shipping method section:
  * Radio buttons for each method:
    ○ Standard (3-5 days) FREE / $9.99
    ○ Express (1-2 days) $15.99
    ○ Overnight (Next day) $24.99
  * Show estimated delivery date
  * Selected method highlighted
  * Update total when selection changes
```

---

### 🔟 EMAIL NOTIFICATIONS

**Backend Prompt:**
```
Set up email sending with SendGrid:

npm install @sendgrid/mail

Email triggers:
1. After order placed:
   - sendOrderConfirmation(order)
   - Email: Order #{id}, Items, Total, Tracking link
   
2. After order ships:
   - sendShippingNotification(order, trackingNumber)
   - Email: Tracking number, Carrier, Estimated delivery
   
3. When product back in stock:
   - sendBackInStockAlert(product, userEmail)

Implementation:
- Create email template functions
- Send async (don't block response)
- Log all emails sent
- Handle send failures gracefully
```

**Frontend Prompt:**
```
Add email preferences section to user profile:

Checkboxes:
☑ Order confirmations
☑ Shipping updates
☑ Promotional offers
☑ Newsletter

Save preferences button
```

---

## QUICK IMPLEMENTATION CHECKLIST

### Before You Start:
- [ ] Set up backend database (MongoDB)
- [ ] Initialize Node.js project with Express
- [ ] Set up environment variables (.env file)
- [ ] Create Git repository

### Week 1-2: Foundation
- [ ] User authentication (login/signup)
- [ ] Protected routes middleware
- [ ] User profile page

### Week 3-4: Commerce
- [ ] Product variants system
- [ ] Stripe payment integration
- [ ] Order management

### Week 5-6: UX Enhancements
- [ ] Advanced product filtering
- [ ] Product reviews
- [ ] Wishlist feature

### Week 7-8: Monetization
- [ ] Coupon system
- [ ] Multiple shipping options
- [ ] Email notifications

---

## COPY-PASTE CONFIG SNIPPETS

### Environment Variables (.env)
```
NODE_ENV=development
PORT=5000

# Database
MONGODB_URI=mongodb://localhost:27017/stepstyle

# JWT
JWT_SECRET=your_super_secret_key_here_min_32_chars
JWT_EXPIRY=7d

# Stripe
STRIPE_SECRET_KEY=sk_test_xxxxx
STRIPE_PUBLIC_KEY=pk_test_xxxxx

# Email
SENDGRID_API_KEY=SG.xxxx

# AWS S3 (for product images)
AWS_ACCESS_KEY=xxx
AWS_SECRET_KEY=xxx
AWS_S3_BUCKET=stepstyle-products
```

### Database Connection (MongoDB)
```javascript
const mongoose = require('mongoose');

mongoose.connect(process.env.MONGODB_URI, {
  useNewUrlParser: true,
  useUnifiedTopology: true
})
.then(() => console.log('MongoDB connected'))
.catch(err => console.error('DB error:', err));
```

### Basic User Schema
```javascript
const userSchema = new mongoose.Schema({
  email: { type: String, required: true, unique: true },
  name: String,
  passwordHash: String,
  phone: String,
  createdDate: { type: Date, default: Date.now },
  isActive: { type: Boolean, default: true }
});
```

---

## HELPFUL LINKS

- Stripe Docs: https://stripe.com/docs/api
- SendGrid Docs: https://docs.sendgrid.com/
- MongoDB Docs: https://docs.mongodb.com/
- Express.js: https://expressjs.com/
- JWT Guide: https://jwt.io/

