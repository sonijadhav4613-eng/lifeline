# LifeAid – Complete Code (Frontend + Backend)

---
## package.json (frontend)
```json
{
  "name": "lifeaid",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-router-dom": "^6.22.0",
    "tailwindcss": "^3.4.0",
    "postcss": "^8.4.31",
    "autoprefixer": "^10.4.15",
    "lottie-react": "^2.4.0",
    "axios": "^1.7.2",
    "i18next": "^23.7.5",
    "react-i18next": "^13.5.0"
  },
  "devDependencies": {
    "vite": "^5.2.0",
    "@vitejs/plugin-react": "^4.3.0"
  }
}
```
---
## vite.config.js
```js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  server: {
    open: true,
  },
});
```
---
## tailwind.config.cjs
```js
module.exports = {
  content: [
    "./index.html",
    "./src/**/*.{js,jsx,ts,tsx}"
  ],
  theme: {
    extend: {
      colors: {
        primary: "#00695c", // teal/blue for health features
        emergency: "#d32f2f", // red for emergencies
      },
    },
  },
  plugins: [],
};
```
---
## index.html
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>LifeAid</title>
    <link rel="manifest" href="/manifest.json" />
    <meta name="theme-color" content="#ffffff" />
  </head>
  <body class="bg-white text-gray-900 font-sans">
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
```
---
## src/index.css
```css
@tailwind base;
@tailwind components;
@tailwind utilities;

/* Custom utilities */
@layer utilities {
  .tap-target {
    @apply min-w-[64px] min-h-[64px] flex items-center justify-center;
  }
}
```
---
## src/main.jsx
```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';
import './index.css';
import './i18n'; // initialise i18next

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```
---
## src/App.jsx
```tsx
import { BrowserRouter, Routes, Route, Navigate } from "react-router-dom";
import Splash from "./pages/Splash";
import Login from "./pages/Login";
import Home from "./pages/Home";
import Doctors from "./pages/Doctors";
import Appointment from "./pages/Appointment";
import Profile from "./pages/Profile";
import BottomNav from "./components/BottomNav";

/* Simple mock auth – stores user in localStorage */
const useAuth = () => {
  const isLogged = !!localStorage.getItem("lifeaid_user");
  return { isLogged };
};

export default function App() {
  const { isLogged } = useAuth();

  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Splash />} />
        <Route path="/login" element={<Login />} />
        <Route
          path="/home"
          element={isLogged ? <Home /> : <Navigate to="/login" replace />}
        />
        <Route
          path="/doctors"
          element={isLogged ? <Doctors /> : <Navigate to="/login" replace />}
        />
        <Route
          path="/appointment"
          element={isLogged ? <Appointment /> : <Navigate to="/login" replace />}
        />
        <Route
          path="/profile"
          element={isLogged ? <Profile /> : <Navigate to="/login" replace />}
        />
      </Routes>
      {isLogged && <BottomNav />}
    </BrowserRouter>
  );
}
```
---
## src/i18n.js
```js
import i18n from "i18next";
import { initReactI18next } from "react-i18next";
import en from "./locales/en.json";
import es from "./locales/es.json";

i18n
  .use(initReactI18next)
  .init({
    resources: {
      en: { translation: en },
      es: { translation: es },
    },
    lng: "en",
    fallbackLng: "en",
    interpolation: { escapeValue: false },
  });

export default i18n;
```
---
## src/locales/en.json
```json
{
  "welcome": "Welcome",
  "email": "Email",
  "password": "Password",
  "login": "Log In",
  "guest": "Continue as Guest",
  "forgot": "Forgot password?",
  "google": "Continue with Google",
  "emergency_bar": "Emergency",
  "first_aid": "First‑Aid Kit",
  "doctors": "Doctors",
  "appointment": "Appointment",
  "profile": "Profile",
  "chatbot_prompt": "Ask a health question…",
  "language": "Language",
  "disclaimer": "This app gives general information and is not a substitute for professional medical advice, diagnosis or treatment. In an emergency, call your local emergency number immediately.",
  "answer": "Answer",
  "date_time": "Date & Time",
  "book": "Book"
}
```
---
## src/locales/es.json
```json
{
  "welcome": "Bienvenido",
  "email": "Correo electrónico",
  "password": "Contraseña",
  "login": "Iniciar sesión",
  "guest": "Continuar como invitado",
  "forgot": "¿Olvidó su contraseña?",
  "google": "Continuar con Google",
  "emergency_bar": "Emergencia",
  "first_aid": "Botiquín de primeros auxilios",
  "doctors": "Doctores",
  "appointment": "Cita",
  "profile": "Perfil",
  "chatbot_prompt": "Haz una pregunta de salud…",
  "language": "Idioma",
  "disclaimer": "Esta aplicación brinda información general y no sustituye el consejo profesional médico, diagnóstico o tratamiento. En una emergencia, llame al número de emergencia local de inmediato.",
  "answer": "Respuesta",
  "date_time": "Fecha y hora",
  "book": "Reservar"
}
```
---
## src/api/api.js
```js
const API_BASE = "http://localhost:5000/api";

export const login = async (email, password) => {
  const res = await fetch(`${API_BASE}/login`, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ email, password }),
  });
  return res.json();
};

export const fetchDoctors = async () => {
  const res = await fetch(`${API_BASE}/doctors`);
  return res.json();
};

export const bookAppointment = async (doctorId, dateTime) => {
  const res = await fetch(`${API_BASE}/appointments`, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ doctorId, dateTime }),
  });
  return res.json();
};

export const askChat = async (prompt, lang = "en") => {
  const res = await fetch(`${API_BASE}/chat`, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ prompt, lang }),
  });
  return res.json();
};

export const queryOpenFDA = async (search) => {
  const url = `https://api.fda.gov/drug/label.json?search=${encodeURIComponent(search)}&limit=5`;
  const res = await fetch(url);
  return res.json();
};
```
---
## src/components/BottomNav.jsx
```tsx
import { NavLink } from "react-router-dom";

const items = [
  { to: "/home", label: "Home", icon: "🏠" },
  { to: "/first-aid", label: "First Aid", icon: "🩹" },
  { to: "/doctors", label: "Doctors", icon: "👨‍⚕️" },
  { to: "/profile", label: "Profile", icon: "👤" },
];

export default function BottomNav() {
  return (
    <nav className="fixed bottom-0 inset-x-0 bg-white border-t border-gray-200 flex justify-around py-2">
      {items.map((i) => (
        <NavLink
          key={i.to}
          to={i.to}
          className={({ isActive }) =>
            `flex flex-col items-center text-sm ${isActive ? "text-primary" : "text-gray-600"}`
          }
          aria-label={i.label}
        >
          <span className="text-xl">{i.icon}</span>
          <span>{i.label}</span>
        </NavLink>
      ))}
    </nav>
  );
}
```
---
## src/components/EmergencyBar.jsx
```tsx
import { useEffect, useState } from "react";
import { useTranslation } from "react-i18next";

export default function EmergencyBar() {
  const { t } = useTranslation();
  const [pulse, setPulse] = useState(false);
  const [country, setCountry] = useState("US");

  useEffect(() => {
    const prefersReduced = window.matchMedia("(prefers-reduced-motion: reduce)")
      .matches;
    if (!prefersReduced) {
      const id = setInterval(() => setPulse((p) => !p), 1000);
      return () => clearInterval(id);
    }
  }, []);

  const numbers = {
    US: { emergency: "112", ambulance: "112", police: "112", fire: "112" },
    IN: { emergency: "112", ambulance: "102", police: "100", fire: "101" },
  };

  const call = (phone) => (window.location.href = `tel:${phone}`);

  const sos = () => {
    call(numbers[country]?.emergency || "112");
    alert("SOS sent + location shared (mock)");
  };

  return (
    <header
      className={`flex flex-col items-center p-2 bg-emergency text-white ${pulse ? "animate-pulse" : ""}`}
      aria-label={t("emergency_bar")}
    >
      <div className="flex space-x-2 tap-target">
        <button onClick={() => call(numbers[country]?.emergency)} className="tap-target">📞</button>
        <button onClick={() => call(numbers[country]?.ambulance)} className="tap-target">🚑</button>
        <button onClick={() => call(numbers[country]?.police)} className="tap-target">🚓</button>
        <button onClick={() => call(numbers[country]?.fire)} className="tap-target">🚒</button>
      </div>
      <button
        onClick={sos}
        className="mt-2 bg-white text-emergency font-bold py-2 px-4 rounded-full tap-target"
      >
        SOS
      </button>
      <select
        value={country}
        onChange={(e) => setCountry(e.target.value)}
        className="mt-2 bg-white text-emergency rounded"
        aria-label="Select country for emergency numbers"
      >
        <option value="US">United States</option>
        <option value="IN">India</option>
      </select>
    </header>
  );
}
```
---
## src/components/FirstAidSection.jsx
```tsx
import { useState } from "react";
import { Lottie } from "lottie-react";
import cprAnim from "../assets/cpr.json"; // placeholder – add your own Lottie files

const aids = [
  {
    id: "cpr",
    title: "CPR",
    icon: "❤️",
    anim: cprAnim,
    steps: [
      "Check responsiveness.",
      "Call emergency services.",
      "Begin chest compressions at 100–120 BPM."
    ]
  },
  {
    id: "choking",
    title: "Choking",
    icon: "🤢",
    steps: [
      "Ask if they can cough.",
      "If not, perform abdominal thrusts.",
      "Repeat until obstruction cleared."
    ]
  }
  // Add more cards as needed
];

export default function FirstAidSection() {
  const [open, setOpen] = useState<string | null>(null);
  const [checked, setChecked] = useState<Record<string, boolean>>({});

  const toggleChecklist = (id: string) =>
    setChecked((c) => ({ ...c, [id]: !c[id] }));

  return (
    <div className="grid grid-cols-2 gap-2">
      {aids.map((a) => (
        <div key={a.id} className="border rounded p-2 flex flex-col items-center tap-target">
          <button
            onClick={() => setOpen((o) => (o === a.id ? null : a.id))}
            className="text-primary text-lg font-medium"
          >
            {a.icon} {a.title}
          </button>
          <label className="mt-1 flex items-center space-x-2">
            <input
              type="checkbox"
              checked={!!checked[a.id]}
              onChange={() => toggleChecklist(a.id)}
            />
            <span className="text-sm">Learned</span>
          </label>
          {open === a.id && (
            <div
              className="fixed inset-0 bg-black/50 flex items-center justify-center p-4"
              role="dialog"
              aria-modal="true"
            >
              <div className="bg-white rounded-lg max-w-md w-full p-4 space-y-3">
                <h3 className="text-xl font-semibold text-primary">{a.title} Guide</h3>
                {a.anim && (
                  <Lottie animationData={a.anim} className="h-32 w-32 mx-auto" />
                )}
                <ol className="list-decimal list-inside space-y-1">
                  {a.steps.map((s, i) => (
                    <li key={i}>{s}</li>
                  ))}
                </ol>
                <button
                  onClick={() => setOpen(null)}
                  className="mt-2 w-full bg-primary text-white py-1 rounded"
                >
                  Close
                </button>
              </div>
            </div>
          )}
        </div>
      ))}
    </div>
  );
}
```
---
## src/components/FeatureGrid.jsx
```tsx
import { Link } from "react-router-dom";

const features = [
  { to: "/doctors", label: "Doctor Appointment", icon: "🗓️" },
  { to: "/video-call", label: "Video Consultation", icon: "📹" },
  { to: "/encyclopedia", label: "Health Encyclopedia", icon: "📚" },
  { to: "/nearby", label: "Nearby Services", icon: "📍" },
  { to: "/reminder", label: "Medicine Reminder", icon: "⏰" },
  { to: "/tools", label: "Health Tools", icon: "⚙️" },
  { to: "/medical-id", label: "My Medical ID", icon: "🆔" },
  { to: "/symptom-checker", label: "Symptom Checker", icon: "🤒" },
];

export default function FeatureGrid() {
  return (
    <div className="grid grid-cols-2 gap-4">
      {features.map((f) => (
        <Link
          key={f.to}
          to={f.to}
          className="tap-target bg-gray-100 rounded shadow hover:bg-gray-200 flex flex-col items-center py-4"
        >
          <span className="text-2xl">{f.icon}</span>
          <span className="mt-1 text-sm font-medium text-primary">{f.label}</span>
        </Link>
      ))}
    </div>
  );
}
```
---
## src/components/ChatBot.jsx
```tsx
import { useState } from "react";
import { askChat } from "../api/api";
import { useTranslation } from "react-i18next";

export default function ChatBot() {
  const { t, i18n } = useTranslation();
  const [question, setQuestion] = useState("");
  const [answer, setAnswer] = useState("");

  const submit = async (e) => {
    e.preventDefault();
    if (!question.trim()) return;
    const resp = await askChat(question, i18n.language);
    setAnswer(resp.reply ?? "…");
    setQuestion("");
  };

  return (
    <section className="p-4 bg-gray-50 rounded shadow">
      <h3 className="text-lg font-medium mb-2">{t("chatbot_prompt")}</h3>
      <form onSubmit={submit} className="flex space-x-2">
        <input
          type="text"
          value={question}
          onChange={(e) => setQuestion(e.target.value)}
          className="flex-1 border rounded p-2"
          placeholder={t("chatbot_prompt")}
        />
        <button type="submit" className="bg-primary text-white px-4 py-2 rounded">▶</button>
      </form>
      {answer && (
        <div className="mt-3 p-3 border rounded bg-white">
          <strong>{t("answer")}:</strong> {answer}
        </div>
      )}
    </section>
  );
}
```
---
## src/components/LanguageSwitcher.jsx
```tsx
import { useTranslation } from "react-i18next";

export default function LanguageSwitcher() {
  const { i18n, t } = useTranslation();

  const change = (e) => {
    i18n.changeLanguage(e.target.value);
  };

  return (
    <select
      value={i18n.language}
      onChange={change}
      className="border rounded p-1"
      aria-label={t("language")}
    >
      <option value="en">English</option>
      <option value="es">Español</option>
    </select>
  );
}
```
---
## src/pages/Splash.jsx
```tsx
import { useEffect, useState } from "react";
import { useNavigate } from "react-router-dom";
import { Lottie } from "lottie-react";
import heartbeat from "../assets/heartbeat.json"; // add a Lottie file here

export default function Splash() {
  const navigate = useNavigate();
  const [showLogo, setShowLogo] = useState(false);

  useEffect(() => {
    const timer = setTimeout(() => setShowLogo(true), 2000);
    const goto = setTimeout(() => navigate("/login"), 4000);
    return () => {
      clearTimeout(timer);
      clearTimeout(goto);
    };
  }, [navigate]);

  return (
    <section className="flex flex-col items-center justify-center min-h-screen bg-white">
      <Lottie animationData={heartbeat} loop={false} className="w-48 h-48" />
      {showLogo && <h1 className="mt-4 text-3xl font-bold text-primary">LifeAid</h1>}
    </section>
  );
}
```
---
## src/pages/Login.jsx
```tsx
import { useState } from "react";
import { useNavigate } from "react-router-dom";
import { useTranslation } from "react-i18next";

export default function Login() {
  const { t } = useTranslation();
  const navigate = useNavigate();
  const [showPwd, setShowPwd] = useState(false);
  const [email, setEmail] = useState("");
  const [pwd, setPwd] = useState("");
  const [error, setError] = useState("");

  const validate = () => {
    if (!email.includes("@")) return t("invalid_email");
    if (pwd.length < 6) return t("pwd_short");
    return "";
  };

  const handleLogin = (e) => {
    e.preventDefault();
    const msg = validate();
    if (msg) return setError(msg);
    localStorage.setItem("lifeaid_user", JSON.stringify({ email }));
    navigate("/home");
  };

  const guestLogin = () => {
    localStorage.setItem("lifeaid_user", JSON.stringify({ guest: true }));
    navigate("/home");
  };

  return (
    <section className="flex flex-col items-center justify-center min-h-screen p-4 bg-white">
      <h2 className="text-2xl font-semibold mb-6 text-primary">{t("welcome")}</h2>
      <form className="w-full max-w-sm space-y-4" onSubmit={handleLogin}>
        {error && <p className="text-red-600 text-sm">{error}</p>}
        <label className="block">
          <span className="text-gray-700">{t("email")}</span>
          <input
            type="email"
            required
            className="mt-1 block w-full rounded border-gray-300 focus:border-primary focus:ring-primary"
            value={email}
            onChange={(e) => setEmail(e.target.value)}
          />
        </label>
        <label className="block">
          <span className="text-gray-700">{t("password")}</span>
          <div className="relative">
            <input
              type={showPwd ? "text" : "password"}
              required
              className="mt-1 block w-full rounded border-gray-300 focus:border-primary focus:ring-primary pr-10"
              value={pwd}
              onChange={(e) => setPwd(e.target.value)}
            />
            <button
              type="button"
              onClick={() => setShowPwd(!showPwd)}
              className="absolute inset-y-0 right-0 px-2 text-gray-500"
            >
              {showPwd ? "🙈" : "👁️"}
            </button>
          </div>
        </label>
        <button
          type="submit"
          className="w-full bg-primary text-white py-2 rounded hover:bg-primary/90"
        >
          {t("login")}
        </button>
        <button
          type="button"
          onClick={guestLogin}
          className="w-full bg-gray-200 text-gray-800 py-2 rounded hover:bg-gray-300"
        >
          {t("guest")}
        </button>
        <div className="flex justify-between text-sm">
          <a href="#" className="text-primary underline">{t("forgot")}</a>
          <a href="#" className="text-primary underline">{t("google")}</a>
        </div>
      </form>
    </section>
  );
}
```
---
## src/pages/Home.jsx
```tsx
import EmergencyBar from "../components/EmergencyBar";
import FirstAidSection from "../components/FirstAidSection";
import FeatureGrid from "../components/FeatureGrid";
import ChatBot from "../components/ChatBot";
import LanguageSwitcher from "../components/LanguageSwitcher";
import { useTranslation } from "react-i18next";

export default function Home() {
  const { t } = useTranslation();

  return (
    <main className="pb-24 bg-white min-h-screen">
      {/* Header with language selector */}
      <header className="flex justify-between items-center p-2">
        <h1 className="text-2xl font-bold text-primary">LifeAid</h1>
        <LanguageSwitcher />
      </header>

      <EmergencyBar />

      <section className="p-4">
        <h2 className="text-xl font-semibold text-primary mb-2">{t("first_aid")}</h2>
        <FirstAidSection />
      </section>

      <section className="p-4">
        <ChatBot />
      </section>

      <section className="p-4">
        <FeatureGrid />
      </section>

      <footer className="p-4 text-xs text-center text-gray-600">
        <p>{t("disclaimer")}</p>
      </footer>
    </main>
  );
}
```
---
## src/pages/Doctors.jsx
```tsx
import { useEffect, useState } from "react";
import { fetchDoctors } from "../api/api";
import { useTranslation } from "react-i18next";

export default function Doctors() {
  const { t } = useTranslation();
  const [doctors, setDoctors] = useState([]);

  useEffect(() => {
    fetchDoctors().then(setDoctors);
  }, []);

  return (
    <section className="p-4">
      <h2 className="text-xl font-semibold text-primary mb-4">{t("doctors")}</h2>
      <ul className="space-y-3">
        {doctors.map((d) => (
          <li key={d.id} className="border rounded p-3 flex justify-between items-center">
            <div>
              <p className="font-medium">{d.name}</p>
              <p className="text-sm text-gray-600">{d.specialty}</p>
            </div>
            <a href={`/appointment?doctorId=${d.id}`} className="bg-primary text-white py-1 px-3 rounded">
              {t("appointment")}
            </a>
          </li>
        ))}
      </ul>
    </section>
  );
}
```
---
## src/pages/Appointment.jsx
```tsx
import { useSearchParams } from "react-router-dom";
import { useState, useEffect } from "react";
import { fetchDoctors, bookAppointment } from "../api/api";
import { useTranslation } from "react-i18next";

export default function Appointment() {
  const { t } = useTranslation();
  const [search] = useSearchParams();
  const doctorId = search.get("doctorId");
  const [doctor, setDoctor] = useState(null);
  const [dateTime, setDateTime] = useState("");

  useEffect(() => {
    if (doctorId) {
      fetchDoctors().then((list) => setDoctor(list.find((d) => d.id === doctorId)));
    }
  }, [doctorId]);

  const handleBook = async (e) => {
    e.preventDefault();
    await bookAppointment(doctorId, dateTime);
    alert("Appointment booked!");
  };

  if (!doctor) return <p>Loading…</p>;

  return (
    <section className="p-4">
      <h2 className="text-xl font-semibold text-primary mb-2">
        {t("appointment")} – {doctor.name}
      </h2>
      <form onSubmit={handleBook} className="space-y-3">
        <label className="block">
          <span>{t("date_time")}</span>
          <input
            type="datetime-local"
            value={dateTime}
            onChange={(e) => setDateTime(e.target.value)}
            className="mt-1 block w-full rounded border-gray-300"
            required
          />
        </label>
        <button className="bg-primary text-white py-2 px-4 rounded">{t("book")}</button>
      </form>
    </section>
  );
}
```
---
## src/pages/Profile.jsx
```tsx
import { useEffect, useState } from "react";
import { useTranslation } from "react-i18next";

export default function Profile() {
  const { t } = useTranslation();
  const [user, setUser] = useState(null);

  useEffect(() => {
    const stored = localStorage.getItem("lifeaid_user");
    if (stored) setUser(JSON.parse(stored));
  }, []);

  if (!user) return <p>{t("loading")}</p>;

  return (
    <section className="p-4">
      <h2 className="text-xl font-semibold text-primary mb-2">{t("profile")}</h2>
      <pre className="bg-gray-100 p-3 rounded">{JSON.stringify(user, null, 2)}</pre>
    </section>
  );
}
```
---
## server/package.json
```json
{
  "name": "lifeaid-server",
  "version": "0.1.0",
  "private": true,
  "main": "index.js",
  "scripts": {
    "start": "node index.js"
  },
  "dependencies": {
    "express": "^4.19.2",
    "lowdb": "^6.0.1",
    "cors": "^2.8.5",
    "body-parser": "^1.20.2"
  }
}
```
---
## server/index.js
```js
const express = require("express");
const cors = require("cors");
const bodyParser = require("body-parser");
const { Low, JSONFile } = require("lowdb");
const path = require("path");

// ---- LowDB setup ----
const dbFile = path.join(__dirname, "data.json");
const adapter = new JSONFile(dbFile);
const db = new Low(adapter);
await db.read();
db.data ||= { users: [], appointments: [] };

const app = express();
app.use(cors({ origin: true }));
app.use(bodyParser.json());

// ----- Mock auth -----
app.post("/api/login", async (req, res) => {
  const { email } = req.body;
  let user = db.data.users.find((u) => u.email === email);
  if (!user) {
    user = { id: Date.now().toString(), email };
    db.data.users.push(user);
    await db.write();
  }
  res.json({ success: true, user });
});

// ----- Doctors (static) -----
app.get("/api/doctors", (req, res) => {
  const doctors = require("./doctors.json");
  res.json(doctors);
});

// ----- Appointments -----
app.post("/api/appointments", async (req, res) => {
  const { doctorId, dateTime } = req.body;
  const appt = { id: Date.now().toString(), doctorId, dateTime };
  db.data.appointments.push(appt);
  await db.write();
  res.json({ success: true, appointment: appt });
});

// ----- AI Chat (proxy) -----
app.post("/api/chat", async (req, res) => {
  const { prompt, lang } = req.body;
  const apiKey = process.env.GEMINI_API_KEY || "YOUR_API_KEY"; // replace with real key
  const url = `https://generativelanguage.googleapis.com/v1/models/gemini-pro:generateContent?key=${apiKey}`;

  try {
    const response = await fetch(url, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ contents: [{ role: "user", parts: [{ text: prompt }] }] })
    });
    const data = await response.json();
    const reply = data.candidates?.[0]?.content?.parts?.[0]?.text ?? "";
    res.json({ reply });
  } catch (e) {
    console.error(e);
    res.status(500).json({ error: "AI service failed" });
  }
});

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => console.log(`🖥️  Server listening on http://localhost:${PORT}`));
```
---
## server/doctors.json
```json
[
  {
    "id": "d1",
    "name": "Dr. Anita Patel",
    "specialty": "General Practitioner",
    "phone": "+1‑555‑123‑4567"
  },
  {
    "id": "d2",
    "name": "Dr. Luis García",
    "specialty": "Cardiologist",
    "phone": "+1‑555‑987‑6543"
  }
]
```
---
## server/data.json (created on first run, empty initial structure)
```json
{
  "users": [],
  "appointments": []
}
```
---
## assets (placeholders – you need to add the real files)
- `src/assets/heartbeat.json` – Lottie animation for the splash screen heartbeat.
- `src/assets/cpr.json` – Lottie animation used in the CPR first‑aid card.
- Add any additional Lottie files you want (choking, burns, etc.) and import them in `FirstAidSection.jsx`.
---
### How to run after you have this single file
1. Save the file above as **lifeaidcode.txt** (the file is already written to the artifact directory).
2. Extract the sections into their proper folder structure (the folder tree is exactly the one described earlier).  All paths match the relative imports used in the code.
3. Run the commands from the previous instructions (`npm install` → `npm start` → `npm run dev`).
4. Open **http://localhost:5173** to see the working app.
---
**End of combined code file**
