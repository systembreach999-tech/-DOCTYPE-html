<!DOCTYPE html>
<html>
<head>
  <title>Login</title>
</head>
<body>

<h2>تسجيل الدخول</h2>

<input id="email" type="email" placeholder="البريد الإلكتروني" />
<input id="password" type="password" placeholder="كلمة المرور" />

<button onclick="login()">تسجيل دخول</button>
<button onclick="signup()">إنشاء حساب</button>

<br><br>

<button onclick="loginWithGoogle()">تسجيل عبر Google</button>
<button onclick="loginWithApple()">تسجيل عبر Apple</button>

<script type="module" src="app.js"></script>

</body>
</html>

import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.0/firebase-app.js";
import { 
  getAuth, 
  createUserWithEmailAndPassword, 
  signInWithEmailAndPassword,
  GoogleAuthProvider,
  OAuthProvider,
  signInWithPopup
} from "https://www.gstatic.com/firebasejs/10.12.0/firebase-auth.js";

// إعداد Firebase
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
};

const app = initializeApp(firebaseConfig);
const auth = getAuth(app);

// تسجيل حساب جديد
window.signup = function () {
  const email = document.getElementById("email").value;
  const password = document.getElementById("password").value;

  createUserWithEmailAndPassword(auth, email, password)
    .then(user => alert("تم إنشاء الحساب ✅"))
    .catch(err => alert(err.message));
};

// تسجيل دخول
window.login = function () {
  const email = document.getElementById("email").value;
  const password = document.getElementById("password").value;

  signInWithEmailAndPassword(auth, email, password)
    .then(user => alert("تم تسجيل الدخول ✅"))
    .catch(err => alert(err.message));
};

// تسجيل Google
window.loginWithGoogle = function () {
  const provider = new GoogleAuthProvider();

  signInWithPopup(auth, provider)
    .then(result => alert("تم تسجيل Google ✅"))
    .catch(err => alert(err.message));
};

// تسجيل Apple
window.loginWithApple = function () {
  const provider = new OAuthProvider('apple.com');

  signInWithPopup(auth, provider)
    .then(result => alert("تم تسجيل Apple ✅"))
    .catch(err => alert(err.message));
};

<div id="status">تم الطلب</div>

<script>
let steps = ["تم الطلب", "في الطريق", "تم التوصيل"];
let i = 0;

setInterval(() => {
  if (i < steps.length - 1) {
    i++;
    document.getElementById("status").innerText = steps[i];
  }
}, 5000); // كل 5 ثواني (غيرها حسب رغبتك)
</script>

function notifyUser(message) {
  if (Notification.permission === "granted") {
    new Notification(message);
  } else {
    Notification.requestPermission();
  }
}

// مثال
notifyUser("تم شحن طلبك 🚚");

app.post("/handle-response", (req, res) => {
  const digit = req.body.Digits;

  if (digit == "1") {
    console.log("تم الاستلام");
  } else if (digit == "2") {
    console.log("لم يتم الاستلام");
  }
});

<input id="orderId" placeholder="رقم الطلب">
<button onclick="search()">بحث</button>

<div id="result"></div>

<script>
async function search() {
  let id = document.getElementById("orderId").value;

  let res = await fetch("http://localhost:5000/order/" + id);
  let data = await res.json();

  document.getElementById("result").innerHTML = `
    📦 الطلب: ${data.id} <br>
    👤 العميل: ${data.email} <br>
    📱 الهاتف: ${data.phone}
  `;
}
</script>

<!DOCTYPE html>
<html lang="ar">
<head>
<meta charset="UTF-8">
<title>حالة الطلب</title>

<style>
body {
  font-family: Arial;
  background: #f5f5f5;
  text-align: center;
}

.container {
  margin-top: 100px;
}

.step {
  margin: 10px;
  padding: 10px;
  background: #ddd;
  display: inline-block;
  border-radius: 5px;
}

.active {
  background: #28a745;
  color: white;
}

button {
  margin-top: 20px;
  padding: 10px;
  cursor: pointer;
}
</style>
</head>

<body>

<div class="container">

<h2>📦 حالة الطلب</h2>

<div id="status1" class="step active">تم الطلب</div>
<div id="status2" class="step">في الطريق</div>
<div id="status3" class="step">تم التوصيل</div>

<br>

<button onclick="nextStep()">تحديث الحالة</button>

</div>

<script>
let step = 1;

function nextStep() {
  if (step === 1) {
    document.getElementById("status2").classList.add("active");
    step++;
  } else if (step === 2) {
    document.getElementById("status3").classList.add("active");
    step++;
  }
}
</script>

</body>
</html>

import express from "express";
import mongoose from "mongoose";
import bcrypt from "bcryptjs";
import jwt from "jsonwebtoken";
import cors from "cors";

const app = express();
app.use(express.json());
app.use(cors());

mongoose.connect("mongodb://127.0.0.1:27017/shopswift");

// ===== Model =====
const User = mongoose.model("User", {
  email: String,
  password: String,
});

// ===== Register =====
app.post("/register", async (req, res) => {
  const { email, password } = req.body;

  const hashedPassword = await bcrypt.hash(password, 10);

  const user = await User.create({
    email,
    password: hashedPassword,
  });

  res.json({ message: "تم إنشاء الحساب" });
});

// ===== Login =====
app.post("/login", async (req, res) => {
  const { email, password } = req.body;

  const user = await User.findOne({ email });

  if (!user) {
    return res.json({ error: "المستخدم غير موجود" });
  }

  const isMatch = await bcrypt.compare(password, user.password);

  if (!isMatch) {
    return res.json({ error: "كلمة المرور غلط" });
  }

  const token = jwt.sign({ id: user._id }, "SECRET_KEY");

  res.json({ message: "تم تسجيل الدخول", token });
});

// ===== Protected Route =====
app.get("/profile", (req, res) => {
  const token = req.headers.authorization;

  try {
    const decoded = jwt.verify(token, "SECRET_KEY");
    res.json({ userId: decoded.id });
  } catch {
    res.json({ error: "غير مصرح" });
  }
});

app.listen(5000, () => console.log("Server running 🔥"));

<div id="orderMsg" style="display:none; background:#e6ffed; padding:15px; margin-top:10px;">
</div>

<button onclick="orderNow()">إتمام الطلب</button>

<script>
function orderNow() {
  let products = ["لابتوب", "كيبورد"]; // المنتجات (تتغير حسب السلة)

  let message = `
  ✅ تم استلام طلبك بنجاح <br><br>
  🛒 المنتجات: ${products.join(", ")} <br>
  🚚 التوصيل خلال 3 أيام
  `;

  document.getElementById("orderMsg").innerHTML = message;
  document.getElementById("orderMsg").style.display = "block";
}
</script>

<button onclick="orderNow()">شراء</button>

<script>
function orderNow() {
  alert("✅ تم تأكيد طلبك!\n🚚 سيصلك الطلب خلال 3 أيام");
}
</script>

<!DOCTYPE html>
<html lang="ar">
<head>
<meta charset="UTF-8">
<title>Sidebar</title>

<style>
body {
  margin: 0;
  font-family: Arial;
}

/* الزر */
.menu-btn {
  font-size: 20px;
  padding: 10px;
  background: #232f3e;
  color: white;
  border: none;
  cursor: pointer;
}

/* القائمة */
.sidebar {
  position: fixed;
  top: 0;
  left: -300px;
  width: 300px;
  height: 100%;
  background: white;
  overflow-y: auto;
  transition: 0.3s;
  box-shadow: 2px 0 5px rgba(0,0,0,0.3);
}

.sidebar.active {
  left: 0;
}

/* عنوان */
.section-title {
  padding: 15px;
  background: #f3f3f3;
  font-weight: bold;
}

/* عناصر */
.item {
  padding: 12px 15px;
  cursor: pointer;
}

.item:hover {
  background: #eee;
}

/* زر الإغلاق */
.close-btn {
  font-size: 25px;
  padding: 10px;
  cursor: pointer;
}
</style>

</head>

<body>

<button class="menu-btn" onclick="toggleMenu()">☰ القائمة</button>

<div class="sidebar" id="sidebar">

<div class="close-btn" onclick="toggleMenu()">✖</div>

<div class="section-title">الأكثر شيوعاً</div>
<div class="item">الأكثر مبيعاً</div>
<div class="item">الإصدارات الحديثة</div>
<div class="item">الأكثر تحركاً</div>

<div class="section-title">الأجهزة والمحتوى الرقمي</div>
<div class="item">Echo و Alexa</div>
<div class="item">قارئات Kindle الإلكترونية والكتب</div>
<div class="item">الحماية وأمن المنزل</div>

<div class="section-title">تسوق حسب القسم</div>
<div class="item">الهواتف المحمولة والأجهزة اللوحية والإكسسوارات</div>
<div class="item">أجهزة الكمبيوتر والمستلزمات المكتبية</div>
<div class="item">التلفزيون والإلكترونيات</div>
<div class="item">الأزياء النسائية</div>
<div class="item">الأزياء الرجالية</div>
<div class="item">أزياء الأطفال</div>
<div class="item">الصحة والجمال والعطور</div>
<div class="item">السوبر ماركت</div>
<div class="item">المنزل والمطبخ والحيوانات الأليفة</div>
<div class="item">أدوات تطوير المنزل</div>
<div class="item">الألعاب ومنتجات الأطفال</div>
<div class="item">الرياضة واللياقة</div>
<div class="item">الكتب</div>
<div class="item">ألعاب الفيديو</div>
<div class="item">مستلزمات السيارات</div>

<div class="section-title">البرامج والميزات</div>
<div class="item">أمازون ناو</div>
<div class="item">بازار</div>
<div class="item">منفذ أمازون</div>
<div class="item">البيع على أمازون</div>

<div class="section-title">المساعدة والإعدادات</div>
<div class="item">حسابك</div>
<div class="item">العربية</div>
<div class="item">الإمارات العربية المتحدة</div>
<div class="item">إعدادات العملة</div>
<div class="item">المساعدة</div>
<div class="item">تسجيل الخروج</div>

</div>

<script>
function toggleMenu() {
  document.getElementById("sidebar").classList.toggle("active");
}
</script>

</body>
</html>

import express from "express";
import mongoose from "mongoose";
import cors from "cors";
import Stripe from "stripe";

const app = express();
const stripe = new Stripe("YOUR_SECRET_KEY");

app.use(cors());
app.use(express.json());

// الاتصال بقاعدة البيانات
mongoose.connect("mongodb://127.0.0.1:27017/shopswift");

// ===== Models =====
const User = mongoose.model("User", {
  email: String,
  password: String,
});

const Product = mongoose.model("Product", {
  name: String,
  price: Number,
  image: String,
  category: String,
});

// ===== USERS =====
app.post("/register", async (req, res) => {
  const user = await User.create(req.body);
  res.json(user);
});

app.post("/login", async (req, res) => {
  const user = await User.findOne(req.body);
  res.json(user);
});

// ===== PRODUCTS =====
app.get("/products", async (req, res) => {
  const products = await Product.find();
  res.json(products);
});

app.post("/products", async (req, res) => {
  const product = await Product.create(req.body);
  res.json(product);
});

// ===== STRIPE PAYMENT =====
app.post("/checkout", async (req, res) => {
  const session = await stripe.checkout.sessions.create({
    payment_method_types: ["card"], // يدعم Apple Pay تلقائي
    line_items: req.body.items.map(item => ({
      price_data: {
        currency: "aed",
        product_data: { name: item.name },
        unit_amount: item.price * 100,
      },
      quantity: item.qty,
    })),
    mode: "payment",
    success_url: "http://localhost:3000/success",
    cancel_url: "http://localhost:3000/cancel",
  });

  res.json({ url: session.url });
});

app.listen(5000, () => console.log("Server running on 5000"));

shopswift/
 ├── backend/
 ├── frontend/

import express from "express";
import mongoose from "mongoose";
import cors from "cors";
import Stripe from "stripe";

const stripe = new Stripe("YOUR_SECRET_KEY");

const app = express();
app.use(cors());
app.use(express.json());

mongoose.connect("mongodb://127.0.0.1:27017/shopswift");

// ===== Models =====
const User = mongoose.model("User", {
  email: String,
  password: String,
  isAdmin: Boolean
});

const Product = mongoose.model("Product", {
  name: String,
  price: Number,
  image: String,
  category: String,
  rating: Number
});

const Order = mongoose.model("Order", {
  items: Array,
  total: Number,
  status: String
});

// ===== Auth (بسيط) =====
app.post("/register", async (req, res) => {
  const user = await User.create(req.body);
  res.json(user);
});

app.post("/login", async (req, res) => {
  const user = await User.findOne(req.body);
  res.json(user);
});

// ===== Products =====
app.get("/products", async (req, res) => {
  res.json(await Product.find());
});

app.post("/products", async (req, res) => {
  res.json(await Product.create(req.body));
});

// ===== Stripe Payment =====
app.post("/pay", async (req, res) => {
  const session = await stripe.checkout.sessions.create({
    payment_method_types: ["card"],
    line_items: req.body.items.map(item => ({
      price_data: {
        currency: "aed",
        product_data: { name: item.name },
        unit_amount: item.price * 100,
      },
      quantity: item.qty,
    })),
    mode: "payment",
    success_url: "http://localhost:3000/success",
    cancel_url: "http://localhost:3000/cancel",
  });

  res.json({ url: session.url });
});

app.listen(5000, () => console.log("Backend running"));

import { useEffect, useState } from "react";

function App() {
  const [products, setProducts] = useState([]);
  const [cart, setCart] = useState([]);

  useEffect(() => {
    fetch("http://localhost:5000/products")
      .then(res => res.json())
      .then(setProducts);
  }, []);

  const addToCart = (p) => {
    setCart([...cart, { ...p, qty: 1 }]);
  };

  const checkout = async () => {
    const res = await fetch("http://localhost:5000/pay", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ items: cart })
    });

    const data = await res.json();
    window.location = data.url;
  };

  return (
    <div>
      <h1>ShopSwift</h1>

      <h2>Products</h2>
      {products.map(p => (
        <div key={p._id}>
          <h3>{p.name}</h3>
          <p>{p.price} AED</p>
          <button onClick={() => addToCart(p)}>Add</button>
        </div>
      ))}

      <button onClick={checkout}>Checkout</button>
    </div>
  );
}

export default App;

const express = require("express");
const mongoose = require("mongoose");
const cors = require("cors");
const stripe = require("stripe")("YOUR_STRIPE_SECRET_KEY");

const app = express();
app.use(express.json());
app.use(cors());

// اتصال قاعدة البيانات
mongoose.connect("mongodb://localhost:27017/shopswift");

// نموذج منتج
const Product = mongoose.model("Product", {
  name: String,
  price: Number,
  image: String,
  rating: Number,
});

// جلب المنتجات
app.get("/products", async (req, res) => {
  const products = await Product.find();
  res.json(products);
});

// إضافة منتج (أدمن)
app.post("/products", async (req, res) => {
  const product = new Product(req.body);
  await product.save();
  res.json(product);
});

// الدفع (Stripe + Apple Pay)
app.post("/create-checkout-session", async (req, res) => {
  const session = await stripe.checkout.sessions.create({
    payment_method_types: ["card"],
    line_items: req.body.items.map(item => ({
      price_data: {
        currency: "aed",
        product_data: { name: item.name },
        unit_amount: item.price * 100,
      },
      quantity: item.qty,
    })),
    mode: "payment",
    success_url: "http://localhost:3000/success",
    cancel_url: "http://localhost:3000/cancel",
  });

  res.json({ id: session.id });
});

app.listen(5000, () => console.log("Server running"));

const express = require("express");
const mongoose = require("mongoose");
const cors = require("cors");
const stripe = require("stripe")("YOUR_STRIPE_SECRET_KEY");

const app = express();
app.use(express.json());
app.use(cors());

// اتصال قاعدة البيانات
mongoose.connect("mongodb://localhost:27017/shopswift");

// نموذج منتج
const Product = mongoose.model("Product", {
  name: String,
  price: Number,
  image: String,
  rating: Number,
});

// جلب المنتجات
app.get("/products", async (req, res) => {
  const products = await Product.find();
  res.json(products);
});

// إضافة منتج (أدمن)
app.post("/products", async (req, res) => {
  const product = new Product(req.body);
  await product.save();
  res.json(product);
});

// الدفع (Stripe + Apple Pay)
app.post("/create-checkout-session", async (req, res) => {
  const session = await stripe.checkout.sessions.create({
    payment_method_types: ["card"],
    line_items: req.body.items.map(item => ({
      price_data: {
        currency: "aed",
        product_data: { name: item.name },
        unit_amount: item.price * 100,
      },
      quantity: item.qty,
    })),
    mode: "payment",
    success_url: "http://localhost:3000/success",
    cancel_url: "http://localhost:3000/cancel",
  });

  res.json({ id: session.id });
});

app.listen(5000, () => console.log("Server running"));

Create a full professional e-commerce platform very similar to Amazon in structure and features (but not copying branding). The website must support multiple languages (Arabic and English) with a language switch button.

Include:
- User system (signup, login, profiles)
- Admin dashboard (add/edit/delete products, manage users and orders)
- Product pages with images, price, description, ratings
- Search system like Amazon with filters (price, category, rating)
- Shopping cart and checkout system
- Order tracking and history
- Clean modern UI similar to large e-commerce platforms
- Fully responsive design (mobile + desktop)

Use:
Frontend: React
Backend: Node.js
Database: MongoDB

Add basic security, authentication, and fast performance. Make it scalable and structured like a real production website.

import express from "express";
import fetch from "node-fetch";

const app = express();

const CLIENT_ID = "AUV_hKpELHslPzAXtD195";
const SECRET = "AUV_hKpELHslPzAXtD195RqyIdDAgRLI0HrvnSIU8oJx6ByK5uCFVWS4LtjMGkyMfPjaTIpMJFQOlrZG";

async function getAccessToken() {
  const res = await fetch("https://api-m.sandbox.paypal.com/v1/oauth2/token", {
    method: "POST",
    headers: {
      "Authorization": "Basic " + Buffer.from(CLIENT_ID + ":" + SECRET).toString("base64"),
      "Content-Type": "application/x-www-form-urlencoded"
    },
    body: "grant_type=client_credentials"
  });

  const data = await res.json();
  return data.access_token;
}

// إنشاء طلب
app.get("/create-order", async (req, res) => {
  const token = await getAccessToken();

  const order = await fetch("https://api-m.sandbox.paypal.com/v2/checkout/orders", {
    method: "POST",
    headers: {
      "Authorization": `Bearer ${token}`,
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      intent: "CAPTURE",
      purchase_units: [{
        amount: { currency_code: "AED", value: "50.00" }
      }]
    })
  });

  const data = await order.json();
  res.json(data);
});

app.listen(5000, () => console.log("Server 🔥"));

<p id="shipping">🚚 رسوم التوصيل: 10 درهم</p>

<script>
function completeOrder() {
  document.getElementById("shipping").innerText = "🚚 التوصيل مجاني 🎉";
}
</script>

<button onclick="completeOrder()">إتمام الشراء</button>

<p style="color:red;">❗ هذا المنتج غير قابل للإرجاع</p>

<select onchange="changeLang(this.value)">
  <option value="ar">العربية</option>
  <option value="en">English</option>
</select>

<h1 id="title">متجر ShopSwift</h1>

<script>
function changeLang(lang) {
  if (lang === "ar") {
    document.getElementById("title").innerText = "متجر شوب سويفت";
  } else {
    document.getElementById("title").innerText = "ShopSwift Store";
  }
}
</script>

<div style="position:fixed; top:10px; left:10px; display:flex; align-items:center;">
  
  <img src="logo.png" style="width:40px; height:40px; border-radius:50%;">
  
  <span id="logoText" style="margin-left:8px;">ShopSwift</span>

</div>

<script>
function updateLogo(lang) {
  if (lang === "ar") {
    document.getElementById("logoText").innerText = "شوب سويفت";
  } else {
    document.getElementById("logoText").innerText = "ShopSwift";
  }
}
</script>

<button onclick="addProduct()">➕ إضافة منتج</button>

<script>
const adminPassword = "1234"; // غيرها

function addProduct() {
  let pass = prompt("ادخل كلمة مرور الأدمن");

  if (pass === adminPassword) {
    alert("✅ تقدر تضيف منتج");
  } else {
    alert("❌ ممنوع");
  }
}
</script>

<div id="products"></div>

<script>
function createProduct(name, price) {
  let product = `
    <div style="border:1px solid #ccc; padding:10px; margin:10px;">
      <h3>${name}</h3>
      <p>السعر: ${price} درهم</p>
      <p style="color:red;">❗ غير قابل للإرجاع</p>
    </div>
  `;

  document.getElementById("products").innerHTML += product;
}

// مثال
createProduct("حذاء", 120);
</script>

function changeLang(lang) {
  updateLogo(lang);

  if (lang === "ar") {
    document.getElementById("title").innerText = "متجر شوب سويفت";
  } else {
    document.getElementById("title").innerText = "ShopSwift Store";
  }
}

<select onchange="setLang(this.value)">
  <option value="ar">العربية</option>
  <option value="en">English</option>
</select>

<h2 data-key="home">الرئيسية</h2>
<h2 data-key="account">حسابك</h2>
<h2 data-key="cart">العربة</h2>

<script>
const translations = {
  ar: {
    home: "الرئيسية",
    account: "حسابك",
    cart: "العربة"
  },
  en: {
    home: "Home",
    account: "Your Account",
    cart: "Cart"
  }
};

function setLang(lang) {
  localStorage.setItem("lang", lang);

  document.documentElement.dir = lang === "ar" ? "rtl" : "ltr";

  document.querySelectorAll("[data-key]").forEach(el => {
    el.innerText = translations[lang][el.dataset.key];
  });

  updateMenuPosition(lang);
}

// تحميل اللغة عند فتح الموقع
window.onload = () => {
  let lang = localStorage.getItem("lang") || "ar";
  setLang(lang);
};
</script>

<div id="menu" style="position:fixed; bottom:0; width:100%; background:#222; color:white; display:flex; justify-content:space-around; padding:10px;">
  
  <span data-key="home">الرئيسية</span>
  <span data-key="account">حسابك</span>
  <span data-key="cart">العربة</span>

</div>

<script>
function updateMenuPosition(lang) {
  let menu = document.getElementById("menu");

  if (lang === "ar") {
    menu.style.flexDirection = "row-reverse";
  } else {
    menu.style.flexDirection = "row";
  }
}
</script>

<div id="bottomNav" style="
position:fixed;
bottom:0;
width:100%;
background:#111;
display:flex;
justify-content:space-around;
color:white;
padding:10px;
">

  <div data-key="home">الرئيسية</div>
  <div data-key="account">حسابك</div>
  <div data-key="cart">العربة</div>
  <div>☰ القائمة</div>

</div>

function setLang(lang) {
  localStorage.setItem("lang", lang);

  // اتجاه الصفحة
  document.documentElement.dir = lang === "ar" ? "rtl" : "ltr";

  // ترجمة النصوص
  document.querySelectorAll("[data-key]").forEach(el => {
    el.innerText = translations[lang][el.dataset.key];
  });

  // تحديث القائمة
  updateMenuPosition(lang);

  // تحديث الشعار
  updateLogo(lang);
}

<!DOCTYPE html>
<html lang="ar">
<head>
<meta charset="UTF-8">
<title>صفحة المنتج</title>

<style>
body {
  font-family: Arial;
  text-align: center;
}

.product {
  border: 1px solid #ccc;
  padding: 20px;
  width: 300px;
  margin: auto;
}

button {
  margin: 5px;
  padding: 10px;
  cursor: pointer;
}

.star {
  font-size: 25px;
  cursor: pointer;
  color: gray;
}

.active {
  color: gold;
}
</style>
</head>

<body>

<div class="product">

  <h2 id="productName">حذاء رياضي</h2>
  <p id="productDesc">حذاء مريح وعصري</p>
  <p>السعر: 120 درهم</p>

  <!-- ⭐ التقييم -->
  <div id="stars">
    <span class="star" onclick="rate(1)">★</span>
    <span class="star" onclick="rate(2)">★</span>
    <span class="star" onclick="rate(3)">★</span>
    <span class="star" onclick="rate(4)">★</span>
    <span class="star" onclick="rate(5)">★</span>
  </div>

  <p id="ratingText">لا يوجد تقييم</p>

  <!-- 🛒 الأزرار -->
  <button onclick="addToCart()">🛒 إضافة إلى العربة</button>
  <button onclick="buyNow()">⚡ شراء الآن</button>

</div>

<script>
// ⭐ التقييم
function rate(stars) {
  let allStars = document.querySelectorAll(".star");

  allStars.forEach((star, index) => {
    star.classList.toggle("active", index < stars);
  });

  document.getElementById("ratingText").innerText = "تم التقييم: " + stars + " ⭐";
}

// 🛒 إضافة للسلة
function addToCart() {
  let product = {
    name: document.getElementById("productName").innerText,
    price: 120
  };

  let cart = JSON.parse(localStorage.getItem("cart")) || [];
  cart.push(product);

  localStorage.setItem("cart", JSON.stringify(cart));

  alert("✅ تم إضافة المنتج إلى العربة");
}

// ⚡ شراء الآن
function buyNow() {
  alert("🚀 جاري تحويلك للدفع...");
  
  // هنا تربطه مع PayPal أو Stripe
  window.location.href = "checkout.html";
}
</script>

</body>
</html>

<!DOCTYPE html>
<html lang="ar">
<head>
<meta charset="UTF-8">
<title>ShopSwift - المنتج</title>

<style>
body { font-family: Arial; display:flex; gap:40px; padding:20px; }

.images img {
  width: 80px;
  cursor: pointer;
  margin:5px;
  border:2px solid transparent;
}

.images img:hover { border:2px solid orange; }

.main-img {
  width: 300px;
}

.product { max-width: 400px; }

button {
  padding:10px;
  margin:5px;
  cursor:pointer;
}

.star { font-size:25px; cursor:pointer; color:gray; }
.active { color:gold; }

.review-box {
  margin-top:20px;
}

.review {
  border-top:1px solid #ccc;
  padding:5px;
}
</style>
</head>

<body>

<!-- 🖼 الصور -->
<div>
  <img id="mainImage" class="main-img" src="https://via.placeholder.com/300">

  <div class="images">
    <img src="https://via.placeholder.com/300" onclick="changeImg(this.src)">
    <img src="https://via.placeholder.com/300/ff0000" onclick="changeImg(this.src)">
    <img src="https://via.placeholder.com/300/00ff00" onclick="changeImg(this.src)">
  </div>
</div>

<!-- 🛒 المنتج -->
<div class="product">

<h2 id="name">حذاء رياضي</h2>
<p>سعر: 120 درهم</p>

<!-- ⭐ تقييم -->
<div>
  <span class="star" onclick="rate(1)">★</span>
  <span class="star" onclick="rate(2)">★</span>
  <span class="star" onclick="rate(3)">★</span>
  <span class="star" onclick="rate(4)">★</span>
  <span class="star" onclick="rate(5)">★</span>
</div>

<p id="ratingText">لا يوجد تقييم</p>

<!-- 💬 تعليق -->
<div class="review-box">
  <input id="reviewInput" placeholder="اكتب تعليقك">
  <button onclick="addReview()">إرسال</button>

  <div id="reviews"></div>
</div>

<!-- 🛒 أزرار -->
<button onclick="addToCart()">🛒 إضافة للعربة</button>
<button onclick="buyNow()">⚡ شراء الآن</button>

</div>

<script>
// تغيير الصورة
function changeImg(src) {
  document.getElementById("mainImage").src = src;
}

// ⭐ تقييم
function rate(num) {
  let stars = document.querySelectorAll(".star");

  stars.forEach((s, i) => {
    s.classList.toggle("active", i < num);
  });

  localStorage.setItem("rating", num);
  document.getElementById("ratingText").innerText = "التقييم: " + num;
}

// تحميل التقييم
window.onload = () => {
  let saved = localStorage.getItem("rating");
  if (saved) rate(saved);

  loadReviews();
};

// 💬 إضافة تعليق
function addReview() {
  let text = document.getElementById("reviewInput").value;

  let reviews = JSON.parse(localStorage.getItem("reviews")) || [];
  reviews.push(text);

  localStorage.setItem("reviews", JSON.stringify(reviews));

  loadReviews();
}

// عرض التعليقات
function loadReviews() {
  let reviews = JSON.parse(localStorage.getItem("reviews")) || [];

  let html = "";
  reviews.forEach(r => {
    html += `<div class="review">💬 ${r}</div>`;
  });

  document.getElementById("reviews").innerHTML = html;
}

// 🛒 إضافة للعربة
function addToCart() {
  let cart = JSON.parse(localStorage.getItem("cart")) || [];

  cart.push({ name: "حذاء", price: 120 });

  localStorage.setItem("cart", JSON.stringify(cart));

  alert("تمت الإضافة ✅");
}

// ⚡ شراء
function buyNow() {
  window.location.href = "checkout.html";
}
</script>

</body>
</html>

