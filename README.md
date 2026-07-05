# ✈️ Travel Memory

A full-stack **MERN** application that allows users to store and manage their travel memories. This project demonstrates deployment of a scalable MERN application on **AWS** using **EC2**, **Application Load Balancers**, **MongoDB Atlas**, **Nginx**, and **Cloudflare**.

---

# 📚 Tech Stack

### Frontend
- React.js
- HTML5
- CSS3
- JavaScript

### Backend
- Node.js
- Express.js

### Database
- MongoDB Atlas

### Cloud & DevOps
- AWS EC2
- AWS Application Load Balancer (ALB)
- Nginx
- Cloudflare DNS

---

# 🏗️ Architecture

```
User Browser
      │
      ▼
 Cloudflare DNS
      │
      ▼
Frontend Load Balancer (ALB)
      │
 ┌────┴────┐
 ▼         ▼
Frontend EC2 #1    Frontend EC2 #2
      │
      ▼
Backend Load Balancer (ALB)
      │
 ┌────┴────┐
 ▼         ▼
Backend EC2 #1     Backend EC2 #2
      │
      ▼
MongoDB Atlas
```

---

# 🚀 Features

- Add travel memories
- Store hotel information
- Save places visited
- Upload trip images
- Track total trip cost
- Featured trips
- Responsive UI
- Scalable AWS deployment

---

# 📁 Project Structure

```
TravelMemory
│
├── backend
│   ├── controllers
│   ├── models
│   ├── routes
│   ├── conn.js
│   ├── index.js
│   └── package.json
│
├── frontend
│   ├── public
│   ├── src
│   └── package.json
│
└── README.md
```

---

# ⚙️ Environment Variables

## Backend

Create a `.env` file inside the `backend` directory.

```env
MONGO_URI=YOUR_MONGODB_ATLAS_CONNECTION_STRING
PORT=3000
```

Example

```env
MONGO_URI=mongodb+srv://username:password@cluster.mongodb.net/travelmemory
PORT=3000
```

---

## Frontend

Create a `.env` file inside the `frontend` directory.

```env
REACT_APP_BACKEND_URL=http://localhost:3000
```

---

# 📦 Sample Data

```json
{
  "tripName": "Incredible India",
  "startDateOfJourney": "19-03-2022",
  "endDateOfJourney": "27-03-2022",
  "nameOfHotels": "Hotel Namaste, Backpackers Club",
  "placesVisited": "Delhi, Kolkata, Chennai, Mumbai",
  "totalCost": 800000,
  "tripType": "Leisure",
  "experience": "Lorem ipsum...",
  "image": "https://example.com/image.jpg",
  "shortDescription": "India is a wonderful country with rich culture and good people.",
  "featured": true
}
```

---

# 💻 Running the Backend

Navigate to the backend directory.

```bash
cd backend
```

Install dependencies.

```bash
npm install
```

Start the server.

```bash
node index.js
```

The backend will be available at:

```
http://localhost:3000
```

---

# 💻 Running the Frontend

Navigate to the frontend directory.

```bash
cd frontend
```

Install dependencies.

```bash
npm install
```

Start the application.

```bash
npm start
```

The frontend will be available at:

```
http://localhost:3000
```

---

# ☁️ AWS Deployment

The application is deployed using:

- AWS EC2
- AWS Application Load Balancer
- MongoDB Atlas
- Cloudflare
- Nginx Reverse Proxy

---

## Backend Deployment

### Launch EC2

| Setting | Value |
|----------|-------|
| AMI | Ubuntu Server 22.04 LTS |
| Instance Type | t2.micro |
| Auto Public IP | Enabled |

---

### Install Dependencies

```bash
sudo apt update
sudo apt install nodejs npm nginx -y
```

Clone the repository.

```bash
git clone https://github.com/UnpredictablePrashant/TravelMemory.git
```

Go to the backend folder.

```bash
cd TravelMemory/backend
```

Install packages.

```bash
npm install
```

Create `.env`

```env
MONGO_URI=YOUR_CONNECTION_STRING
PORT=3000
```

Run the backend.

```bash
node index.js
```

---

## Configure Nginx

Create a new configuration.

```nginx
server {
    listen 80;

    server_name _;

    location / {
        proxy_pass http://localhost:3000;

        proxy_http_version 1.1;

        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;

        proxy_cache_bypass $http_upgrade;
    }
}
```

Restart Nginx.

```bash
sudo nginx -t
sudo systemctl restart nginx
```

---

# Frontend Deployment

Clone the repository.

```bash
git clone https://github.com/UnpredictablePrashant/TravelMemory.git
```

Navigate to the frontend.

```bash
cd TravelMemory/frontend
```

Install dependencies.

```bash
npm install
```

Create `.env`

```env
REACT_APP_BACKEND_URL=http://BACKEND_PUBLIC_IP
```

Build the application.

```bash
npm run build
```

Serve the build using Nginx.

---

# 🔄 Application Flow

```
User
   │
   ▼
Cloudflare
   │
   ▼
Frontend ALB
   │
   ▼
Frontend EC2
   │
   ▼
Backend ALB
   │
   ▼
Backend EC2
   │
   ▼
MongoDB Atlas
```

---

# 📝 End-to-End Workflow

1. User opens the application.
2. Cloudflare resolves the domain.
3. Traffic reaches the Frontend Load Balancer.
4. React application loads from a healthy Frontend EC2 instance.
5. User submits a travel memory.
6. React sends a POST request to the backend.
7. Backend Load Balancer forwards the request.
8. Node.js processes the request.
9. Data is stored in MongoDB Atlas.
10. Backend returns a success response.
11. React updates the UI.

---

# 📸 Screenshots

<img width="800" height="546" alt="Screenshot 2026-07-05 210438" src="https://github.com/user-attachments/assets/5f17bd82-3c72-4385-a708-33861c3b95c3" />


<img width="436" height="74" alt="Screenshot 2026-07-05 210447" src="https://github.com/user-attachments/assets/26b43195-9800-43cc-aef4-218a58bff2a7" />

<img width="636" height="145" alt="Screenshot 2026-07-05 210502" src="https://github.com/user-attachments/assets/35406284-24b2-4cf1-b991-0ee3ea5e0353" />

<img width="1217" height="605" alt="Screenshot 2026-07-05 211244" src="https://github.com/user-attachments/assets/9ea58989-2a45-456b-a608-4d41f03e0c48" />


<img width="1338" height="704" alt="Screenshot 2026-07-05 211428" src="https://github.com/user-attachments/assets/8adebe56-e2c8-453c-82bd-434dc7428734" />

<img width="1365" height="681" alt="Screenshot 2026-07-05 211537" src="https://github.com/user-attachments/assets/794525f1-c458-467a-9133-3595f486381b" />

<img width="631" height="481" alt="Screenshot 2026-07-05 211545" src="https://github.com/user-attachments/assets/c14cdd29-946d-475d-85b8-114ea452e903" />


<img width="1112" height="519" alt="Screenshot 2026-07-05 211632" src="https://github.com/user-attachments/assets/1b543c76-4d54-4ead-9382-8f89d5d4fb3e" />


<img width="1352" height="540" alt="Screenshot 2026-07-05 212005" src="https://github.com/user-attachments/assets/4172053a-dfa0-4159-865c-775d600b0ace" />


```




docs/
├── architecture.png
├── frontend.png
├── backend.png
├── aws-alb.png
└── mongodb.png
```

---

# 🔗 Repository

**GitHub Repository**

https://github.com/UnpredictablePrashant/TravelMemory

---

# 📄 License

This project is intended for educational and learning purposes.
