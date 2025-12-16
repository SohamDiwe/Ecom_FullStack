# Email & Invoice System Setup Guide

## Overview
Your ecommerce app now sends order confirmation emails with PDF invoices to customers after successful payment. This guide explains how to set it up.

## Features
✅ Automatic order confirmation emails  
✅ Professional PDF invoices included  
✅ User-specific order history  
✅ Real order details (not mock data)  
✅ Beautiful HTML email templates  

## Setup Instructions

### 1. Enable Gmail App Password (for email sending)

**Important:** Using your Gmail password directly is insecure. Use an App Password instead:

#### Step-by-step:
1. Go to your Google Account: https://myaccount.google.com
2. Click **"Security"** in the left menu
3. Enable **2-Step Verification** (if not already enabled)
4. Go back to Security → **App passwords** (appears only after 2FA is enabled)
5. Select **Mail** and **Windows Computer**
6. Google will generate a 16-character password
7. Copy this password (remove spaces)

#### Update your .env file:
```env
GMAIL_USER=your-gmail@gmail.com
GMAIL_PASSWORD=xxxx xxxx xxxx xxxx  # Remove spaces: xxxxxxxxxxxxxxxx
```

### 2. Start Both Servers

**Terminal 1 - Frontend (Vite):**
```bash
npm run dev
```
Runs on http://localhost:5173 or 5174

**Terminal 2 - Backend (Express + Email Service):**
```bash
npm run server
```
Runs on http://localhost:5001

### 3. Test the Complete Flow

1. **Create an Account** → Navigate to `/auth`
2. **Browse Products** → Go to Shop and add items to cart
3. **Proceed to Checkout** → Fill in shipping details
4. **Complete Payment** → Use test Razorpay credentials
5. **Check Email** → Look for order confirmation with PDF invoice

### 4. What Happens Behind the Scenes

```
User places order
    ↓
Payment processed (Razorpay)
    ↓
Order saved to Firestore
    ↓
PDF invoice generated (server)
    ↓
Email sent with invoice attachment
    ↓
User receives confirmation
```

## API Endpoints

### Send Order Email
**POST** `/api/send-order-email`

Request body:
```json
{
  "order": {
    "id": "order-doc-id",
    "items": [...],
    "totals": { "subtotal": 1000, "shipping": 0, "tax": 50, "total": 1050 },
    "shipping": { "name": "John", "email": "john@example.com", "address": "...", "phone": "..." },
    "status": "Confirmed",
    "createdAt": "2025-12-15T10:30:00Z"
  },
  "user": {
    "email": "john@example.com",
    "displayName": "John Doe"
  }
}
```

Response:
```json
{ "success": true, "message": "Email sent successfully" }
```

## Firestore Rules (Important!)

Make sure your Firestore security rules allow saving orders:

```firestore
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /orders/{document=**} {
      allow read: if request.auth != null && request.auth.uid == resource.data.userId;
      allow create: if request.auth != null;
      allow write: if request.auth != null && request.auth.uid == resource.data.userId;
    }
  }
}
```

## Environment Variables

Required in `.env`:

```env
# Gmail Email Service
GMAIL_USER=your-email@gmail.com
GMAIL_PASSWORD=your-16-char-app-password

# Backend Server
PORT=5001

# API Base URL (for frontend to call backend)
VITE_API_BASE_URL=http://localhost:5001
```

## Troubleshooting

### Email not sending?
- Check Gmail credentials in `.env`
- Ensure 2FA is enabled on Google account
- Verify app password (not regular password)
- Check server logs for errors

### PDF not attaching?
- Verify `pdfkit` is installed: `npm list pdfkit`
- Check invoice generator logs

### Order not appearing in history?
- Ensure Firestore has the `orders` collection
- Check if `userId` is correctly saved in order
- Verify Firestore security rules allow reading own orders

## Production Deployment

### Gmail Limitations
Gmail SMTP has rate limits (~500/day). For production, consider:
- **SendGrid** - 100 free emails/day, then paid
- **Mailgun** - Free tier available
- **AWS SES** - Pay as you go
- **Firebase Cloud Functions** - Integrate with nodemailer

### Example with SendGrid (recommended):
Replace nodemailer configuration with SendGrid:
```javascript
import sgMail from '@sendgrid/mail'
sgMail.setApiKey(process.env.SENDGRID_API_KEY)

const msg = {
  to: user.email,
  from: 'noreply@ecomcollective.com',
  subject: 'Order Confirmation',
  html: htmlContent,
  attachments: [{ filename: 'invoice.pdf', content: pdfBuffer, type: 'application/pdf' }]
}

await sgMail.send(msg)
```

## Success Indicators

✅ User receives email immediately after payment  
✅ Email contains beautiful HTML layout  
✅ PDF invoice is attached with correct order details  
✅ Each user only sees their own orders in Account page  
✅ Order history matches Firestore data  

## Files Modified

- `server/index.js` - Email service endpoint
- `server/invoice-generator.js` - PDF invoice generation
- `src/pages/Checkout.jsx` - Trigger email after payment
- `src/pages/Account.jsx` - Fetch real order history
- `src/services/orders.js` - Firestore order queries
- `package.json` - Added nodemailer and pdfkit
- `.env.example` - Email configuration template
