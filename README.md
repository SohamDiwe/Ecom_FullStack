# 🛒 E-Commerce Web Application

[![Live Demo](https://img.shields.io/badge/Live-Demo-brightgreen)](https://ecom-7811f.web.app)

A modern, fully responsive e-commerce web application built with **React + Vite**, featuring product listings, cart management, checkout flow, and mobile-first design.

## 🌐 Live Demo
👉 https://ecom-7811f.web.app

---

## 🚀 Features

- 📱 **Fully Responsive Design**
  - Optimized for mobile, tablet, and desktop
  - Responsive grid layouts
  - Mobile-friendly navigation with hamburger menu

- 🛍️ **Product Management**
  - Product listing with images, ratings, prices
  - Category filtering
  - Product details pages

- 🛒 **Cart & Checkout**
  - Add/remove products from cart
  - Cart summary with total price
  - Optimized checkout flow for mobile users

- 🔐 **Authentication**
  - User authentication using Firebase
  - Secure login and signup flow

- 🎨 **UI & UX Enhancements**
  - Smooth transitions and animations
  - Beautiful gradient-based UI
  - Touch-optimized buttons and form inputs
  - 16px password inputs (prevents iOS auto-zoom)

- ⚡ **Fast Performance**
  - Built with Vite for fast development and builds
  - Optimized images and components

---

## 🧰 Tech Stack

### Frontend
- React
- JavaScript
- Vite
- CSS (mobile-first & responsive)

### Backend
- **Firebase Authentication** – User login & signup
- **Firebase Firestore** – User data, cart data, and orders
- **Firebase Hosting** – Production deployment
- **Environment Variables (.env)** – Secure configuration

### State Management
- React Context API

### Assets
- SVG & Image assets

---

## 📁 Project Structure

```txt
src/
 ├── assets/            # Images & SVGs
 ├── components/        # Reusable UI components
 ├── context/           # Auth, Cart, Coupon, Toast contexts
 ├── data/              # Product data
 ├── pages/             # Page-level components
 ├── scripts/           # Seed & utility scripts
 ├── firebase.js        # Firebase configuration
 └── main.jsx           # App entry point
