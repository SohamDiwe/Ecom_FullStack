## Firestore Index for Orders

For server-side sorting of user orders, create a composite index on the `orders` collection:

- Fields: `userId` (Ascending), `createdAt` (Descending)
- Usage: Query with `where('userId','==', uid)` and `orderBy('createdAt','desc')`

Without this index, the app will still show orders using a client-side fallback, but enabling the index reduces read costs and improves performance.
# Ecom Collective – React + Firebase + Razorpay

Complete ecommerce experience with product browsing, cart, checkout, Firebase Auth/Firestore, and Razorpay payments.

## Stack
- Vite + React 19
- React Router
- Firebase Auth + Firestore (backend for auth/data)
- Razorpay order/checkout (Express server)

## Quick start
1) Install deps
```bash
npm install
```

2) Configure environment
- Copy `.env.example` to `.env` and set Firebase + Razorpay keys.

3) Run frontend
```bash
npm run dev
```

4) Run backend for Razorpay
```bash
npm run server
```
API server runs on `http://localhost:5001` by default.

## Environment variables
Frontend (`.env`):
```
VITE_FIREBASE_API_KEY=
VITE_FIREBASE_AUTH_DOMAIN=
VITE_FIREBASE_PROJECT_ID=
VITE_FIREBASE_STORAGE_BUCKET=
VITE_FIREBASE_MESSAGING_SENDER_ID=
VITE_FIREBASE_APP_ID=

VITE_RAZORPAY_KEY_ID=
VITE_API_BASE_URL=http://localhost:5001
```

Backend (`.env`):
```
RAZORPAY_KEY_ID=
RAZORPAY_KEY_SECRET=
PORT=5001
```

## Firebase setup
1. Create a Firebase project.
2. Enable Email/Password and Google providers under Authentication.
3. Create a Firestore database.
	 - For quick dev, use Test mode. For production, secure your rules, e.g.:
```
rules_version = '2';
service cloud.firestore {
	match /databases/{database}/documents {
		match /products/{id} {
			allow read: if true;
			allow write: if request.auth != null; // limit writes to authenticated users
		}
		match /orders/{id} {
			allow read, write: if request.auth != null && request.auth.uid == request.resource.data.userId;
		}
	}
}
```
4. Add a web app and copy the config into the frontend env vars.
5. Seed products: open the app, go to Shop, and click “Seed sample catalog” if Firestore is empty.

## Razorpay setup
1. Create Razorpay test mode keys.
2. Add `RAZORPAY_KEY_ID` and `RAZORPAY_KEY_SECRET` to `.env` for the server, and `VITE_RAZORPAY_KEY_ID` for the client.
3. Start the server (`npm run server`) so `/api/razorpay/order` is available.

## Development notes
- Cart state persists in `localStorage`.
- Firestore is the source of truth; a curated sample catalog is used as a fallback when Firestore is empty or unreachable.
- Checkout requires auth (email/password or Google).

## Scripts
- `npm run dev` – start Vite dev server
- `npm run build` – production build
- `npm run preview` – preview built site
- `npm run server` – Express API for Razorpay order creation
