# Nellai Super Market - Setup Instructions

## Database Setup

### 1. Supabase Database Setup

1. **Go to your Supabase project dashboard**
   - Navigate to https://supabase.com/dashboard
   - Select your project

2. **Run the SQL commands**
   - Go to the SQL Editor in your Supabase dashboard
   - Copy and paste the entire content from `database-setup.sql`
   - Click "Run" to execute all the SQL commands

3. **Verify the setup**
   - Go to the Table Editor in your Supabase dashboard
   - You should see the following tables created:
     - `users`
     - `shipping_addresses`
     - `orders`
     - `payment_details`
   - You should also see the `order_summary` view

### 2. Environment Variables

Make sure your Supabase configuration in `src/lib/supabase.ts` has the correct URL and API key:

```typescript
const supabaseUrl = 'YOUR_SUPABASE_URL';
const supabaseAnonKey = 'YOUR_SUPABASE_ANON_KEY';
```

## Features Implemented

### 1. User Authentication
- **Fresh Login Required**: The app now clears any existing session on startup, requiring users to log in fresh each time
- **Session Management**: `persistSession: false` in Supabase config ensures no persistent sessions
- **Automatic Session Clearing**: On app start, all stored session data is cleared

### 2. Header Dropdown Menu
- **When Not Logged In**: Shows "Register" and "Login" options
- **When Logged In**: Shows "Hello, [User Name]", "My Orders", and "Logout" options
- **Mobile Responsive**: Dropdown works on both desktop and mobile

### 3. Cart Functionality
- **Login Required for Checkout**: "Proceed to Payment" button only appears when user is logged in
- **Redirect to Login**: If not logged in, clicking checkout redirects to login page
- **Session State**: After login, user is redirected back to checkout

### 4. Database Tables

#### Users Table
- Extends Supabase auth.users
- Stores user profile information
- Automatically created when user registers

#### Shipping Addresses Table
- Multiple addresses per user
- Default address functionality
- Full CRUD operations

#### Orders Table
- Complete order information
- Payment and order status tracking
- Razorpay integration support
- Automatic order number generation

#### Payment Details Table
- Detailed payment information
- Gateway response storage
- Payment status tracking

### 5. Row Level Security (RLS)
- Users can only access their own data
- Secure by default
- Proper permissions for all operations

## API Endpoints Available

### Order Service (`src/services/orderService.ts`)

#### Orders
- `createOrder(orderData)` - Create new order
- `getUserOrders()` - Get all user orders
- `getOrderById(orderId)` - Get specific order
- `updateOrderStatus(orderId, status)` - Update order status

#### Shipping Addresses
- `createShippingAddress(addressData)` - Add new address
- `getShippingAddresses()` - Get user addresses
- `updateShippingAddress(addressId, addressData)` - Update address
- `deleteShippingAddress(addressId)` - Delete address
- `setDefaultAddress(addressId)` - Set default address

#### Payment Details
- `createPaymentDetails(paymentData)` - Create payment record
- `updatePaymentDetails(paymentId, paymentData)` - Update payment
- `getPaymentDetailsByOrderId(orderId)` - Get payment for order

## Usage Examples

### Creating an Order
```typescript
import { orderService } from '../services/orderService';

const orderData = {
  customer_name: "John Doe",
  customer_email: "john@example.com",
  customer_phone: "1234567890",
  delivery_address: "123 Main St",
  city: "Chennai",
  state: "Tamil Nadu",
  pincode: "600001",
  items: cartItems,
  subtotal: 146,
  delivery_fee: 50,
  total_amount: 196,
  payment_method: "online"
};

const order = await orderService.createOrder(orderData);
```

### Managing Shipping Addresses
```typescript
// Add new address
const addressData = {
  address_line1: "123 Main Street",
  city: "Chennai",
  state: "Tamil Nadu",
  pincode: "600001",
  is_default: true
};

const address = await orderService.createShippingAddress(addressData);

// Get user addresses
const addresses = await orderService.getShippingAddresses();
```

## Security Features

1. **Row Level Security (RLS)**: All tables have RLS enabled
2. **User Isolation**: Users can only access their own data
3. **Authentication Required**: All operations require valid user session
4. **Input Validation**: Database constraints ensure data integrity
5. **Automatic Timestamps**: Created and updated timestamps are automatically managed

## Testing the Implementation

1. **Start the application**
2. **Verify fresh login requirement**: App should not show any logged-in state on startup
3. **Test registration**: Create a new account
4. **Test login**: Log in with credentials
5. **Test dropdown menu**: Verify correct options appear based on login state
6. **Test cart checkout**: Add items to cart and verify login requirement
7. **Test order creation**: Complete a test order
8. **Test address management**: Add and manage shipping addresses

## Troubleshooting

### Common Issues

1. **"User not authenticated" errors**
   - Ensure user is logged in
   - Check if session was cleared properly

2. **Database permission errors**
   - Verify RLS policies are correctly set up
   - Check if user has proper permissions

3. **Order creation fails**
   - Ensure all required fields are provided
   - Check database constraints

4. **Session not clearing**
   - Verify `persistSession: false` in Supabase config
   - Check if localStorage is being cleared properly

### Database Verification

Run these queries in Supabase SQL Editor to verify setup:

```sql
-- Check if tables exist
SELECT table_name FROM information_schema.tables 
WHERE table_schema = 'public' AND table_name IN ('users', 'orders', 'shipping_addresses', 'payment_details');

-- Check RLS policies
SELECT schemaname, tablename, policyname, permissive, roles, cmd, qual 
FROM pg_policies 
WHERE schemaname = 'public';
```

## Next Steps

1. **Implement Checkout Page**: Use the order service to create orders
2. **Add Payment Integration**: Integrate with Razorpay or other payment gateways
3. **Order Management**: Create admin interface for order management
4. **Email Notifications**: Add email notifications for order status changes
5. **Order Tracking**: Implement order tracking functionality

## Support

If you encounter any issues:
1. Check the browser console for error messages
2. Verify Supabase configuration
3. Ensure all SQL commands were executed successfully
4. Check that RLS policies are properly configured 