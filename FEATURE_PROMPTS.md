# StepStyle E-Commerce - Feature-by-Feature Implementation Prompts

Use these prompts with an AI coding assistant to implement each missing feature.

---

## 1. USER AUTHENTICATION SYSTEM

### Prompt for Backend (Node.js/Express):
```
Create a complete user authentication system using Node.js/Express with the following:
- User registration endpoint (POST /api/auth/register) that:
  * Validates email uniqueness
  * Hashes password using bcrypt
  * Creates user record in database
  * Returns JWT token
  * Validates input (email format, password strength)
  
- User login endpoint (POST /api/auth/login) that:
  * Validates email exists
  * Validates password matches hashed password
  * Returns JWT token with 7 day expiry
  * Includes user info (id, email, name)
  
- JWT middleware for protected routes that:
  * Verifies token validity
  * Extracts user info
  * Passes to next middleware
  
- Get user profile endpoint (GET /api/user/profile)
  
- Update user profile endpoint (PUT /api/user/profile)

- Logout endpoint (POST /api/auth/logout)

- Password reset flow:
  * Send reset email with token
  * Validate reset token
  * Update password
  
Database schema should include: userId, email, passwordHash, name, phone, createdDate, updatedDate

Use environment variables for JWT secret and database credentials.
```

### Prompt for Frontend (HTML/JavaScript):
```
Create a user authentication UI system with:
- signup.html page with:
  * Name, email, password, confirm password fields
  * Password strength indicator
  * Real-time validation feedback
  * Submit button that calls /api/auth/register
  * Link to login page
  * Success/error messages
  * Redirect to profile on success
  
- login.html page with:
  * Email and password fields
  * "Remember me" checkbox
  * "Forgot password" link
  * Submit button that calls /api/auth/login
  * Error handling
  * Redirect to homepage on success
  * Link to signup
  
- user-profile.html showing:
  * User name, email, phone
  * Edit profile form
  * Change password form
  * Logout button
  * Saved addresses section
  * Order history link
  
- Auth utility functions in JavaScript that:
  * Store JWT token in localStorage
  * Add token to all API requests
  * Check if user is logged in
  * Redirect to login if unauthorized
  * Auto-logout on token expiry

Add login check to all protected pages (checkout, user pages)
```

---

## 2. PRODUCT VARIANTS (SIZES & COLORS)

### Prompt for Backend:
```
Extend the product system to support variants:
- Create ProductVariant schema with:
  * variantId (unique)
  * productId (reference to product)
  * size (6, 7, 8, 9, 10, 11, 12, 13)
  * color (e.g., "Red", "Blue", "Black")
  * colorCode (hex code for display)
  * imageUrl (specific variant image)
  * stock (inventory for this variant)
  * sku (stock keeping unit)
  * price (optional override if variant costs different)
  
- Modify product retrieval to:
  * Include all variants for each product
  * Show size/color availability
  * Calculate in-stock status based on variants
  
- Create size chart data:
  * Measurements for each size (length, width, width)
  * Fit info (narrow, standard, wide)
  
- Update cart to store variant info:
  * itemId, productId, size, color, quantity, price
  * Check variant stock on add to cart
```

### Prompt for Frontend:
```
Create product variant UI with:
- Size selector on product-detail.html:
  * Display all available sizes as buttons
  * Show "(Out of stock)" for unavailable sizes
  * Highlight selected size
  * Only allow adding to cart if size selected
  * Show "Size Chart" link that opens modal
  
- Size chart modal showing:
  * Table with size measurements (US, EU, CM)
  * Fit guide (narrow/standard/wide)
  * International size conversion
  * Printable size chart link
  
- Color selector showing:
  * Color swatches with hex codes
  * Color name on hover
  * Show different product image when color selected
  * Show "(Out of stock)" for unavailable colors
  
- Stock indicator:
  * "X items in stock" if < 10 items
  * "Limited availability" if < 5 items
  * "Out of stock" if 0 stock
  * "Only 3 left" urgency message
  
- Update cart display to show:
  * Product name + Selected Size + Selected Color
  * Variant-specific image
  * Price per variant
  
- Update checkout to confirm:
  * Selected size and color
  * Variant details on order confirmation
```

---

## 3. REAL PRODUCT IMAGES

### Prompt for Backend:
```
Create image upload system:
- Set up AWS S3 bucket for product images
- Create POST /api/products/:id/upload-image endpoint:
  * Accept multipart/form-data with image file
  * Validate file format (jpg, png, webp) and size (< 5MB)
  * Resize/optimize images (thumbnail, medium, large)
  * Upload to S3
  * Return secure URL
  * Update product record with image URL
  
- Create image gallery schema:
  * productId
  * imageUrls (array of URLs)
  * primaryImageIndex
  * altText for each image
  
- Support multiple images per product/variant
```

### Prompt for Frontend:
```
Create image display system on product-detail.html:
- Main image viewer:
  * Display large product image
  * Support zoom on hover (2-3x zoom)
  * Swipe to next/previous on mobile
  
- Thumbnail gallery:
  * Show 3-4 thumbnails below main image
  * Highlight active thumbnail
  * Scroll thumbnails if more than 4 images
  * Click thumbnail to update main image
  
- Image optimization:
  * Lazy load images
  * Use srcset for responsive images
  * Show placeholder while loading
  * Error handling for missing images
  
- Gallery features:
  * Fullscreen image view
  * Download image option
  * Share image on social media
```

---

## 4. REAL PAYMENT GATEWAY INTEGRATION (Stripe)

### Prompt for Backend:
```
Integrate Stripe payment processing:
- Install Stripe SDK: npm install stripe
- Create POST /api/payment/create-payment-intent:
  * Accept amount, currency, customer email
  * Create Stripe PaymentIntent
  * Return clientSecret to frontend
  
- Create POST /api/payment/confirm-payment:
  * Verify payment with Stripe
  * Check payment status
  * Create order record if successful
  * Return order details
  
- Create POST /api/payment/webhook:
  * Handle Stripe webhook events (charge.succeeded, charge.failed)
  * Update order status
  * Send confirmation emails
  
- Store payment records:
  * paymentId, orderId, amount, currency
  * status (pending, completed, failed)
  * stripeTransactionId
  * createdDate, completedDate
  
- Error handling:
  * Insufficient funds
  * Invalid card
  * Expired card
  * Network errors
  
Use environment variables for Stripe API keys
```

### Prompt for Frontend:
```
Create Stripe payment form on checkout.html:
- Install Stripe.js library
- Create payment form with:
  * Card element (from Stripe.js)
  * Show form validation errors
  * Cardholder name input
  * Billing address fields (prefilled if user logged in)
  
- Checkout process:
  * When user clicks "Place Order":
    - Validate all required fields
    - Call /api/payment/create-payment-intent
    - Get clientSecret
    - Use confirmCardPayment to process
    - Handle success/error responses
    
- Show payment status:
  * Loading state while processing
  * Success message with order number
  * Error message with retry option
  * Redirect to confirmation page on success
  
- Error handling:
  * Display card validation errors in real-time
  * Show network error messages
  * Provide "Try again" option
  * Store form data if error (don't lose data)
```

---

## 5. ORDER MANAGEMENT & TRACKING

### Prompt for Backend:
```
Create complete order management system:
- Create Order schema:
  * orderId (unique, e.g., SS-20250311001)
  * userId (if logged in)
  * customerInfo (name, email, phone, address)
  * shippingAddress (full address)
  * items (array with productId, variantId, quantity, price)
  * subtotal, tax, shippingCost, total
  * paymentMethod, paymentStatus
  * orderStatus (Pending, Confirmed, Processing, Shipped, Delivered)
  * trackingNumber (auto-generated)
  * estimatedDeliveryDate
  * createdDate, updateDate
  * notes (order special instructions)
  
- Create POST /api/orders:
  * Create new order from cart
  * Associate with user if logged in
  * Generate unique order ID
  * Calculate taxes (8%)
  * Calculate shipping based on address
  * Deduct stock from products
  * Clear user's cart
  * Return order details
  
- Create GET /api/orders:
  * Return all orders for logged-in user (if userId provided)
  * Include order items and status
  * Sort by date descending
  
- Create GET /api/orders/:orderId:
  * Return full order details
  * Verify user is owner of order
  * Include tracking info
  
- Create PUT /api/orders/:orderId/status:
  * Allow admin to update status
  * Trigger email notifications on status change
  
- Create POST /api/orders/:orderId/cancel:
  * Only if order not yet shipped
  * Process refund if applicable
  * Restore stock
  * Send cancellation email
  
- Order number generation:
  * Format: SS-20250311001 (SS-YYYYMMDDXXX)
  * Auto-increment XXX part
```

### Prompt for Frontend:
```
Create order tracking UI:
- Create order-tracking.html page showing:
  * Order number prominently
  * Order date
  * Estimated delivery date
  * Current status with visual timeline:
    - Pending → Confirmed → Processing → Shipped → Delivered
    - Show checkmark for completed steps
    - Highlight current step
  * Tracking number with copy button
  * Items ordered (product name, size, color, qty, price)
  * Subtotal, shipping, tax, total breakdown
  * Customer address used for delivery
  * Shipping carrier name (FedEx/UPS) with logo
  
- Create order-history.html page showing:
  * List of all past orders
  * Order number, date, status, total as table/cards
  * Quick filters (All, Pending, Shipped, Delivered)
  * Search by order number
  * Click to view full order details
  * "Reorder" button to add same items to cart
  
- Create order confirmation page after checkout:
  * Show "Order Confirmed!" message
  * Display order number
  * Show order summary
  * Message: "Confirmation email sent to [email]"
  * "Track your order" button
  * "Continue shopping" button
  
- Add order status email trigger:
  * When admin updates status in dashboard
  * Send email to customer with status update and tracking info
```

---

## 6. ADVANCED PRODUCT FILTERING & SEARCH

### Prompt for Backend:
```
Create advanced filtering and search API:
- Create GET /api/products/search endpoint:
  * Query parameters:
    - q: search query (searches name, description, category)
    - category: filter by category
    - minPrice: minimum price
    - maxPrice: maximum price
    - size: filter by available size
    - color: filter by color
    - sort: sortBy option (price_asc, price_desc, newest, popularity, rating)
    - page: pagination (default 1)
    - limit: items per page (default 12)
    
  * Return:
    - matching products array
    - totalCount
    - totalPages
    - currentPage
    
- Implement search filtering:
  * Full-text search database support
  * Case-insensitive matching
  * Partial matches ("run" matches "Runner")
  * Typo tolerance (optional fuzzy search)
  
- Implement sorting:
  * Price: ascending/descending
  * Newest: by createdDate
  * Popularity: by sales count
  * Rating: by average review rating
  * Best Sellers: by order count this month
  
- Optimization:
  * Index frequently searched fields
  * Cache popular searches
  * Pagination for large result sets
  
- Autocomplete support GET /api/search/autocomplete?q=
  * Suggest product names
  * Suggest categories
  * Return top 10 suggestions
```

### Prompt for Frontend (shop.html):
```
Create advanced filtering UI:
- Left sidebar with filters:
  * Category checkboxes (Running, Casual, Sports)
  * Price range slider ($50-$200)
    - Visual range display
    - Min/max input fields
  * Size multi-select (6, 7, 8, 9, 10, 11, 12, 13)
  * Color multi-select with color swatches
  * "Apply Filters" button
  * "Clear All" button
  
- Top toolbar:
  * Sort dropdown (Price Low-High, High-Low, Newest, Best Sellers, Rating)
  * View toggle (Grid 2/3/4 columns, List view)
  * Results count "Showing X-Y of Z products"
  * Search input with autocomplete dropdown
  
- Filter badges:
  * Show active filters as removable tags
  * "Category: Running", "Price: $50-$150"
  * Click X to remove individual filters
  * "Clear All Filters" option
  
- Product grid updates:
  * Dynamic filtering on filter change (no page reload needed)
  * Loading spinner while fetching
  * "No results" message if no products found
  * Pagination controls (Previous, page numbers, Next)
  
- URL management:
  * Update URL params when filters change
  * Allow sharing filtered URL
  * Restore filters when page loaded with params
  
- Search functionality:
  * Autocomplete suggestions as user types
  * Highlight search term in results
  * Search history in dropdown
```

---

## 7. PRODUCT REVIEWS & RATINGS

### Prompt for Backend:
```
Create complete review system:
- Create Review schema:
  * reviewId (unique)
  * productId
  * userId
  * rating (1-5 stars)
  * reviewTitle
  * reviewText
  * isVerifiedPurchase (boolean - user bought product)
  * isApproved (boolean - admin approved)
  * images (array of image URLs)
  * helpfulCount
  * unhelpfulCount
  * createdDate
  * updatedDate
  * staffReply (optional admin response)
  
- Create RatingSummary schema:
  * productId
  * averageRating
  * totalReviews
  * ratingDistribution (1star, 2star, 3star, 4star, 5star counts)
  
- Create POST /api/reviews:
  * Accept review data
  * Verify user purchased product (verified purchase)
  * Create review record
  * Auto-set isApproved to false (requires moderation)
  * Update product's rating summary
  * Return review details
  
- Create GET /api/reviews/:productId:
  * Return approved reviews for a product
  * Sort by: newest, helpful, rating
  * Include pagination
  * Show review count by rating
  
- Create PUT /api/reviews/:reviewId/helpful:
  * Increment helpful/unhelpful count
  * Prevent duplicate votes from same user (track voting history)
  
- Create PUT /api/reviews/:reviewId/reply (admin):
  * Add staff reply to review
  
- Create review moderation endpoints:
  * GET /api/admin/reviews/pending (unapproved reviews)
  * PUT /api/admin/reviews/:id/approve
  * DELETE /api/admin/reviews/:id (reject/delete)
```

### Prompt for Frontend (product-detail.html):
```
Create review UI on product page:
- Rating summary section:
  * Large average rating star (4.5 stars)
  * "(X customer reviews)" link
  * "Write a review" button
  * Rating distribution bar:
    - 5 stars: 60% filled bar
    - 4 stars: 25% filled bar
    - 3 stars: 10% filled bar
    - 2 stars: 3% filled bar
    - 1 star: 2% filled bar
  * "Verified purchase" badge for purchased product
  
- Review list section:
  * Display 3-5 reviews, load more button
  * Sort dropdown (Most Helpful, Most Recent, Highest Rating)
  * Filter: Show only verified purchases (checkbox)
  
- Individual review card showing:
  * Star rating
  * Review title
  * Review text (truncate long reviews with "Read more")
  * Review author name
  * "Verified Purchase" badge if applicable
  * Review date "2 weeks ago"
  * Helpful/Unhelpful buttons with counts
  * Review images/photos (if any)
  * Staff reply (if any) highlighted
  
- Write review modal/form:
  * Star rating selector (click to rate)
  * Review title input
  * Review text textarea
  * Image upload (optional, max 3 images)
  * Submit button
  * Success message: "Thank you! Your review is pending moderation"
  
- Only show write review button if:
  * User is logged in
  * User purchased the product (verified purchase)
```

---

## 8. WISHLIST / SAVE FOR LATER

### Prompt for Backend:
```
Create wishlist system:
- Create Wishlist schema:
  * wishlistId (unique)
  * userId
  * productId
  * variantId (optional - specific size/color)
  * notifyWhenInStock (boolean)
  * createdDate
  * updatedDate
  
- Create POST /api/wishlist:
  * Add product to logged-in user's wishlist
  * Check if already in wishlist (prevent duplicates)
  * Return success/already exists message
  
- Create GET /api/wishlist:
  * Return user's wishlist items
  * Include full product details
  * Return count of items
  * Order by newest first
  
- Create DELETE /api/wishlist/:itemId:
  * Remove item from wishlist
  
- Create PUT /api/wishlist/:itemId/notify:
  * Toggle notifyWhenInStock flag
  * When product comes back in stock, email user
  
- Create GET /api/wishlist/public/:userId (optional):
  * Allow sharing wishlist (if user allows)
  * Return shareable wishlist URL
```

### Prompt for Frontend:
```
Create wishlist UI:
- Add wishlist button to product cards:
  * Heart icon (outline = not wishlisted, filled = wishlisted)
  * Click to add/remove from wishlist
  * Show toast: "Added to wishlist" or "Removed from wishlist"
  * Only functional if user logged in, else prompt to login
  * Count badge showing total wishlist items
  
- Wishlist page (wishlist.html):
  * Total wishlist items count
  * Grid/list view toggle
  * Each wishlist item showing:
    - Product image
    - Product name, category
    - Price
    - Stock status
    - Heart icon to remove
    - "Add to Cart" button
    - "View Details" link
  * Empty state: "Your wishlist is empty. Start adding items!"
  * Share wishlist link (copy to clipboard)
  
- Wishlist features:
  * Move from wishlist to cart (drag, or "Add to Cart" button)
  * Remove from wishlist
  * Sort (newest, oldest, price low-high, high-low)
  * Filter by availability (In Stock, Out of Stock)
  * Email when item back in stock (checkbox per item)
  
- Wishlist sidebar (optional):
  * Show mini wishlist in nav
  * Show recently added items
  * Quick access to full wishlist page
```

---

## 9. COUPON & DISCOUNT SYSTEM

### Prompt for Backend:
```
Create coupon system:
- Create Coupon schema:
  * couponId
  * couponCode (unique, uppercase, e.g., "SAVE25")
  * description
  * discountType (percentage, flatAmount, bogo)
  * discountValue (25 for 25%, or 10 for $10 off)
  * minPurchaseAmount (minimum order total)
  * maxDiscountAmount (cap on discount)
  * maxUsagePerCoupon (e.g., max 100 uses total)
  * usagePerUser (max uses per user, default 1)
  * applicableCategories (array, empty = all categories)
  * applicableProducts (array, empty = all products)
  * startDate
  * expiryDate
  * isActive (boolean)
  
- Create CouponUsage tracking:
  * usageId, couponId, userId, orderId
  * appliedDate
  
- Create POST /api/coupons/validate:
  * Accept couponCode
  * Check if active, not expired
  * Check if user hasn't exceeded usage limit
  * Check if cart total meets minimum
  * Check if products in cart are applicable
  * Return discount amount or error
  
- Create POST /api/orders/apply-coupon:
  * Apply validated coupon to order
  * Recalculate order total with discount
  * Record coupon usage
  * Return updated order summary
  
- Create admin endpoints:
  * POST /api/admin/coupons (create new coupon)
  * GET /api/admin/coupons (list all coupons)
  * PUT /api/admin/coupons/:id (edit)
  * DELETE /api/admin/coupons/:id (deactivate)
  * GET /api/admin/coupons/:id/usage (view usage stats)
```

### Prompt for Frontend:
```
Create coupon UI on checkout page:
- Coupon section in checkout:
  * Text input: "Have a coupon code?"
  * "Apply" button
  * Show current coupon if applied (with X to remove)
  
- Order summary with discount breakdown:
  * Subtotal: $100.00
  * Coupon "SAVE25" discount: -$25.00 (green)
  * Shipping: $9.99
  * Tax: $6.72
  * Total: $91.71 (highlighted savings)
  
- Validation messages:
  * "Coupon applied successfully! Save $25"
  * "Invalid or expired coupon"
  * "Coupon not applicable to these items"
  * "Minimum purchase $50 required"
  * "You have already used this coupon"
  
- Coupon display on homepage:
  * Banner showing active promotions: "Save 25%! Use code SAVE25"
  * Limited time countdown if applicable
  * Only applicable products section
  
- Create admin coupon management page:
  * Create/edit coupon form
  * List all coupons with status
  * View usage stats per coupon
  * Activate/deactivate coupons
  * Export coupon usage report
```

---

## 10. MULTIPLE SHIPPING OPTIONS

### Prompt for Backend:
```
Create shipping options system:
- Create Shipping schema:
  * shippingId
  * shippingMethod (Standard, Express, Overnight, International)
  * daysToDeliver (e.g., "3-5 business days")
  * cost (base cost)
  * cosPerMile (optional variable pricing)
  * isActive
  
- Create POST /api/shipping/calculate:
  * Accept: shippingAddress (zip code), orderWeight, orderValue
  * Calculate shipping cost based on method and distance
  * Return available shipping methods with costs and delivery dates
  
- Shipping methods:
  * Standard: 3-5 days, free on orders >$75, else $9.99
  * Express: 1-2 days, $15.99
  * Overnight: Next day, $24.99
  * International: varies by country
  
- Estimated delivery calculation:
  * Add selected days to current date
  * Account for weekends/holidays
  * Return "Estimated delivery: March 15"
  
- Track shipment:
  * Generate tracking number when order ships
  * Partner with carrier APIs (FedEx, UPS if available)
  * Update tracking status periodically
```

### Prompt for Frontend:
```
Create shipping options UI in checkout:
- Shipping method selection:
  * Radio buttons for each method:
    ○ Standard (3-5 days) — FREE on orders over $75 / $9.99
    ○ Express (1-2 days) — $15.99
    ○ Overnight (Next day) — $24.99
  * Show estimated delivery date for each option
  * Visual badge "FREE" or discounted price in green
  * Selected method highlighted
  
- Shipping cost update:
  * Real-time update order total when shipping method selected
  * Show breakdown: Subtotal + Coupon - Discount + Shipping + Tax = Total
  
- Address validation:
  * Validate address before showing shipping options
  * Show any address issues that affect shipping
  
- Order tracking info:
  * On confirmation page show:
    - Shipping method selected
    - Estimated delivery date
    - Tracking number (once shipped)
    - Carrier logo (FedEx, UPS, etc.)
```

---

## 11. EMAIL NOTIFICATIONS

### Prompt for Backend:
```
Set up email notification system using SendGrid/Mailgun:
- Configure email service:
  * Install email SDK (SendGrid, Mailgun)
  * Store API keys in environment variables
  
- Email templates needed:
  1. Order Confirmation
     - Order number, date, items, total
     - Estimated delivery date
     - Button to track order
     
  2. Order Shipped
     - Tracking number
     - Carrier info
     - Estimated delivery date
     - Tracking link
     
  3. Order Delivered
     - Delivery confirmation
     - Request review link
     - "Reorder" quick link
     
  4. Payment Receipt
     - Transaction ID, amount, date
     - Payment method (last 4 digits)
     - Invoice attachment (PDF)
     
  5. Wishlist Back in Stock
     - Product name, image
     - Link to product page
     
  6. Password Reset
     - Reset link with token
     - Link expiry (24 hours)
     
  7. Account Confirmation
     - Welcome message
     - Confirm email link
     
- Create email trigger functions:
  * sendOrderConfirmation(order)
  * sendShippingNotification(order, trackingNumber)
  * sendDeliveryNotification(order)
  * sendBackInStockAlert(product, userEmail)
  * sendPasswordReset(user, resetToken)
  
- Email logging:
  * Log all sent emails in database
  * Store: recipientEmail, subject, type, sentDate, status
  * Allow users to view email history
```

### Prompt for Frontend:
```
Create email preferences UI:
- User notification settings page:
  * Checkboxes for notification preferences:
    ☑ Order confirmations
    ☑ Shipping updates
    ☑ Delivery notifications
    ☑ Review requests
    ☑ Promotional emails
    ☑ Wishlist back in stock alerts
    ☑ Newsletter
  * Frequency preference (Daily, Weekly, or Unsubscribe)
  * Save preferences button
  
- Email management:
  * Show previous emails (order confirmations, etc)
  * Resend confirmation email button
  * Unsubscribe from all link at bottom of email
```

---

## IMPLEMENTATION WORKFLOW

1. **Start with Authentication** (highest priority - blocks other features)
2. **Then Product Variants** (needed for realistic product data)
3. **Then Payment Integration** (critical for revenue)
4. **Then Order Management** (necessary for business operations)
5. **Then Filtering/Search** (improves UX)
6. **Then Reviews & Ratings** (builds trust)
7. **Then Wishlist** (increases engagement)
8. **Then Coupons** (drives sales)
9. **Then Shipping Options** (required for real operations)
10. **Finally Email Notifications** (supports all of the above)

Each feature builds on previous features and dependencies.

---

## DATABASE SETUP CHECKLIST

- [ ] Create users table
- [ ] Create products table
- [ ] Create product_variants table
- [ ] Create orders table
- [ ] Create order_items table
- [ ] Create reviews table
- [ ] Create reviews_summary table
- [ ] Create wishlist table
- [ ] Create coupons table
- [ ] Create coupon_usage table
- [ ] Create payments table
- [ ] Create email_logs table
- [ ] Add indexes for frequently queried fields
- [ ] Set up database backups

---

## SECURITY CHECKLIST

- [ ] Hash all passwords with bcrypt
- [ ] Use HTTPS only
- [ ] Implement CSRF tokens
- [ ] Validate all inputs server-side
- [ ] Use parameterized queries (prevent SQL injection)
- [ ] Implement rate limiting on APIs
- [ ] Store sensitive data encrypted
- [ ] Use environment variables for secrets
- [ ] Implement audit logging
- [ ] Regular security updates
- [ ] PCI-DSS compliance for payments
- [ ] GDPR compliance for user data

