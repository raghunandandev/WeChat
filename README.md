# WeChat - Video Meeting Application

A real-time video conferencing application built with React and Node.js, enabling users to host and join video meetings with real-time communication capabilities.

## 📋 Project Overview

WeChat is a full-stack web application that allows users to:
- Create and host video meetings
- Join existing meetings using a unique URL
- Authenticate with username and password
- View meeting history
- Experience real-time peer-to-peer video communication

## 🏗️ Project Structure

```
WeChat/
├── backend/                 # Node.js Express server
│   ├── src/
│   │   ├── app.js          # Express server setup & Socket.io configuration
│   │   ├── controllers/
│   │   │   ├── socketManager.js    # WebSocket connection management
│   │   │   └── user.controller.js  # User authentication logic
│   │   ├── models/
│   │   │   ├── user.model.js       # User schema & database model
│   │   │   └── meeting.model.js    # Meeting schema & database model
│   │   └── routes/
│   │       └── users.routes.js     # User authentication endpoints
│   └── package.json
│
└── frontend/                # React application
    ├── public/             # Static assets
    ├── src/
    │   ├── App.js          # Main routing component
    │   ├── contexts/
    │   │   └── AuthContext.jsx     # Authentication context provider
    │   ├── pages/
    │   │   ├── landing.jsx         # Welcome/landing page
    │   │   ├── authentication.jsx  # Login/signup page
    │   │   ├── home.jsx            # Home dashboard
    │   │   ├── history.jsx         # Meeting history page
    │   │   └── VideoMeet.jsx       # Video meeting component
    │   ├── utils/
    │   │   └── withAuth.jsx        # Authentication HOC for protected routes
    │   └── styles/
    │       └── videoComponent.module.css
    └── package.json
```

## 🚀 Tech Stack

### Backend
- **Runtime:** Node.js (ES6+ modules)
- **Framework:** Express.js
- **Database:** MongoDB Atlas
- **Real-time Communication:** Socket.io
- **Authentication:** JWT-based with bcrypt password hashing
- **Other:** Cors, Mongoose ODM

### Frontend
- **Framework:** React 18
- **Routing:** React Router DOM v6
- **UI Components:** Material-UI (MUI)
- **Styling:** Emotion, CSS Modules
- **Real-time Client:** Socket.io Client
- **HTTP Client:** Axios
- **Package Manager:** npm

## 📦 Dependencies

### Backend
```json
{
  "bcrypt": "^5.1.1",
  "cors": "^2.8.5",
  "express": "^4.18.2",
  "mongoose": "^8.0.3",
  "nodemon": "^3.0.2",
  "socket.io": "^4.7.3"
}
```

### Frontend
```json
{
  "react": "^18.2.0",
  "react-dom": "^18.2.0",
  "react-router-dom": "^6.21.1",
  "@mui/material": "^5.15.4",
  "@mui/icons-material": "^5.15.4",
  "axios": "^1.6.5",
  "socket.io-client": "^4.7.3"
}
```

## 🔧 Installation & Setup

### Prerequisites
- Node.js (v14+)
- npm or yarn
- MongoDB Atlas account
- Modern web browser with WebRTC support

### Backend Setup

1. Navigate to the backend directory:
```bash
cd backend
```

2. Install dependencies:
```bash
npm install
```

3. Configure environment variables (create `.env` file):
```
PORT=8000
MONGODB_URI=mongodb+srv://your_username:your_password@cluster0.cujabk4.mongodb.net/
```

4. Start the development server:
```bash
npm run dev
```

The backend will run on `http://localhost:8000`

### Frontend Setup

1. Navigate to the frontend directory:
```bash
cd frontend
```

2. Install dependencies:
```bash
npm install
```

3. Configure backend URL in [environment.js](frontend/src/environment.js):
```javascript
export const BACKEND_URL = 'http://localhost:8000';
```

4. Start the development server:
```bash
npm start
```

The frontend will run on `http://localhost:3000`

## 🎯 Features

### Authentication
- User registration with username and password
- Secure password hashing using bcrypt
- JWT-based session management
- Protected routes with authentication HOC

### Video Meeting
- Real-time peer-to-peer video and audio
- WebRTC for media streaming
- Socket.io for signaling and real-time events
- Unique meeting URL generation

### User Dashboard
- View active meeting history
- One-click meeting creation
- Copy meeting URL for sharing
- View meeting participants

## 📡 API Endpoints

### User Routes (`/api/v1/users`)
- `POST /register` - Register new user
- `POST /login` - Login user
- `GET /profile` - Get user profile (protected)

## 🔌 WebSocket Events

### Client to Server
- `join-call` - Join a video meeting
- `call-user` - Initiate video call
- `answer-call` - Answer incoming call
- `send-message` - Send chat message

### Server to Client
- `user-joined` - New participant joined
- `call-incoming` - Incoming call notification
- `user-left` - Participant disconnected
- `receive-message` - New chat message

## 🗄️ Database Schema

### User Model
```javascript
{
  name: String (required),
  username: String (required, unique),
  password: String (required),
  token: String
}
```

### Meeting Model
```javascript
{
  host_id: ObjectId,
  meeting_url: String,
  created_at: Date,
  participants: [ObjectId],
  duration: Number
}
```

## 📱 Routes

### Frontend Routes
- `/` - Landing page
- `/auth` - Authentication page (login/signup)
- `/home` - User dashboard (protected)
- `/history` - Meeting history (protected)
- `/:url` - Video meeting room (protected)

## 🔐 Security Features
- Password encryption with bcrypt
- CORS enabled for cross-origin requests
- JWT token validation for protected routes
- Request size limits (40KB)
- Secure WebSocket connections

## 🚀 Deployment

### Backend Deployment
- Use PM2 for production process management: `npm run prod`
- Deploy to services like Heroku, AWS, or DigitalOcean
- Update MongoDB connection string for production

### Frontend Deployment
- Build production bundle: `npm run build`
- Deploy to Vercel, Netlify, or AWS S3 + CloudFront
- Update API endpoints for production backend

## 📝 Scripts

### Backend
```bash
npm run dev    # Start with nodemon (development)
npm start      # Start production server
npm run prod   # Start with PM2 (production)
```

### Frontend
```bash
npm start      # Start development server
npm run build  # Create production build
npm test       # Run tests
npm run eject  # Eject from create-react-app (not recommended)
```

## 🐛 Known Issues & TODO

- [ ] Implement message persistence in database
- [ ] Add screen sharing capability
- [ ] Implement recording functionality
- [ ] Add end-to-end encryption
- [ ] Improve error handling and validation
- [ ] Add user notifications
- [ ] Mobile responsive optimization

## 👥 Contributing

This project is authored by apnacollege. To contribute:
1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## 📄 License

ISC License - See LICENSE file for details

## 🤝 Support

For issues, questions, or suggestions, please open an issue in the repository.

---

**Note:** Ensure MongoDB connection is properly configured before starting the application. The current connection string contains credentials that should be moved to environment variables for production use.
