# NeoLearn.AI Backend Integration Guide

Congratulations! The complete backend for your `NeoLearn.AI` project has been generated in your `backend/` folder. This guide will walk you through exactly how to run it, test it, and connect it to your existing HTML/CSS/JS frontend.

## 1. Exact Folder Structure Created
It follows standard backend architecture suited for Express.js + Mongoose projects:
```text
backend/
├── config/
│   └── db.js            # MongoDB Connection Logic
├── controllers/
│   ├── aiController.js        # Logic for OpenAI calls
│   ├── authController.js      # Logic for Signup/Login
│   ├── progressController.js  # Logic for saving tracking progress
│   └── resourceController.js  # Logic for querying free resources
├── middleware/
│   ├── authMiddleware.js      # JWT Verification
│   └── errorMiddleware.js     # Centralized error handling
├── models/
│   ├── Progress.js            # Mongoose Schema for progress
│   ├── Resource.js            # Mongoose Schema for resources
│   └── User.js                # Mongoose Schema for user profiles
├── routes/
│   ├── aiRoutes.js            
│   ├── authRoutes.js          
│   ├── progressRoutes.js      
│   └── resourceRoutes.js      
├── utils/
│   ├── generateToken.js       # Generates JWT payload
│   └── prompts.js             # Reusable OpenAI prompt strings
├── .env                       # Environment Variables
├── .env.example               # Template for version control
├── package.json               # Node App Metadata & dependencies
└── server.js                  # Main Express Entrypoint
```

## 2. Requirements & Setup

Before running the backend, you need Node.js and MongoDB installed on your system.
1. Open up your terminal.
2. Navigate into the backend directory:
   ```bash
   cd "backend"
   ```
3. Install all dependencies:
   ```bash
   npm install
   ```
4. Verify your [.env](file:///c:/Users/rajva_hsul3b9/Downloads/minor%20project/backend/.env) file matches your local configuration (Add your actual OpenAI API Key):
   ```
   PORT=5000
   NODE_ENV=development
   MONGO_URI=mongodb://localhost:27017/neolearn
   JWT_SECRET=your_jwt_strong_secret_key_here
   OPENAI_API_KEY=your_openai_api_key_here
   ```
5. Start the backend:
   ```bash
   npm run dev
   ```
   You should see:
   `Server running in development mode on port 5000`
   `MongoDB Connected: 127.0.0.1`

## 3. Postman / Testing API Routes

### User Authentication
**1. Register (POST /api/auth/signup)**
- **Body (JSON)**: `{"name":"Raj","email":"raj@example.com","password":"password123"}`
- **Response**: JWT Token + User object.

**2. Login (POST /api/auth/login)**
- **Body (JSON)**: `{"email":"raj@example.com","password":"password123"}`
- **Response**: JWT Token + User object.

### Progress Tracking (Requires JWT Bearer Token)
**1. Add Progress (POST /api/progress)**
- **Headers**: `Authorization: Bearer <your_token_here>`
- **Body (JSON)**: `{"topic": "React Hooks", "status": "in-progress", "score": 80, "notes": "Need to practice useEffect"}`

**2. Get Summary (GET /api/progress/summary/:userId)**
- **Headers**: `Authorization: Bearer <your_token_here>`
- **Response**: Returns statistics like `totalTopics`, `completedTopics`, `averageScore`, etc.

### AI Features
**1. Explain Topic (POST /api/ai/explain)**
- **Body (JSON)**: `{"topic": "Recursion in programming"}`
- **Response**: `{"success": true, "data": "Simple explanation..."}`

**2. Generate Roadmap (POST /api/ai/roadmap)**
- **Body (JSON)**: `{"goal": "Become a frontend dev", "currentLevel": "Intermediate", "availableTime": 15, "targetDuration": "3 months"}`

## 4. How to Connect Your Frontend (HTML/JS)
Since your frontend is raw HTML/JavaScript without a framework, you can use the native browser `fetch()` API.

### Common Mistake to Avoid
**Do NOT expose your OpenAI API Key or MongoDB URI in frontend JS files!** All requests should go to `http://localhost:5000/api/...`

### Example: Making a User Sign Up
Inside [script.js](file:///c:/Users/rajva_hsul3b9/Downloads/minor%20project/script.js) directly linked to [signup.html](file:///c:/Users/rajva_hsul3b9/Downloads/minor%20project/signup.html):
```javascript
const signupForm = document.querySelector('form'); // Your form

signupForm.addEventListener('submit', async (e) => {
    e.preventDefault();
    
    // Grab input values
    const name = document.getElementById('first-name').value + ' ' + document.getElementById('last-name').value;
    const email = document.getElementById('email').value;
    const password = document.getElementById('password').value;

    try {
        const response = await fetch('http://localhost:5000/api/auth/signup', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ name, email, password })
        });

        const data = await response.json();
        
        if (response.ok) {
            // Success! Save Token
            localStorage.setItem('token', data.token);
            localStorage.setItem('userId', data._id);
            alert("Signup successful!");
            window.location.href = 'index.html'; // Redirect to home
        } else {
            // Backend returned validation error
            alert(data.message);
        }
    } catch (error) {
        console.error("Fetch error:", error);
    }
});
```

### Example: Using AI to explain a topic
Inside the script linked to [index.html](file:///c:/Users/rajva_hsul3b9/Downloads/minor%20project/index.html) where you have the AI chat input:
```javascript
const askButton = document.querySelector('.fa-paper-plane').parentElement;
const inputField = document.querySelector('input[placeholder="Ask anything..."]');

askButton.addEventListener('click', async () => {
    const question = inputField.value;
    if(!question) return;

    // Show loading state here...

    try {
        const response = await fetch('http://localhost:5000/api/ai/explain', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ topic: question })
        });
        const data = await response.json();
        
        if(data.success) {
            console.log("AI Answer:", data.data);
            // Append data.data to your chat UI div
        }
    } catch (err) {
        console.error(err);
    }
});
```

Let me know if you would like me to configure the frontend fetch specifically into the frontend code for you!
