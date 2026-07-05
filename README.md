# Travel Memory

`.env` file to work with the backend after creating a database in mongodb: 

```
MONGO_URI='ENTER_YOUR_URL'
PORT=3001
```

Data format to be added: 

```json
{
    "tripName": "Incredible India",
    "startDateOfJourney": "19-03-2022",
    "endDateOfJourney": "27-03-2022",
    "nameOfHotels":"Hotel Namaste, Backpackers Club",
    "placesVisited":"Delhi, Kolkata, Chennai, Mumbai",
    "totalCost": 800000,
    "tripType": "leisure",
    "experience": "Lorem Ipsum, Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum, ",
    "image": "https://t3.ftcdn.net/jpg/03/04/85/26/360_F_304852693_nSOn9KvUgafgvZ6wM0CNaULYUa7xXBkA.jpg",
    "shortDescription":"India is a wonderful country with rich culture and good people.",
    "featured": true
}
```


For frontend, you need to create `.env` file and put the following content (remember to change it based on your requirements):
```bash
REACT_APP_BACKEND_URL=http://localhost:3001
```

## How to run BE

Note: Make sure you have the .env file already added
```bash
cd backend
npm install
node index.js
```

## How to run FE
Note: Make sure you have the .env file already added
```bash
cd frontend
npm install
npm start
```




#####################################################

Travel Memory App Deployment on AWS

GitHub Repository: https://github.com/UnpredictablePrashant/TravelMemory

In this deployment, we run the Frontend and Backend on separate EC2 servers, and the Database lives on MongoDB Atlas (a managed cloud database). Traffic is distributed across multiple server copies using AWS Load Balancers.

Architecture Overview


User's Browser 
│ 
▼
[Cloudflare DNS] ←  (your custom domain)
│ 
▼ [Frontend Load Balancer -- ALB] ←  LB 
│
┌──┴──┐
▼▼ 
[Frontend EC2 #1] [Frontend EC2 #2] (React, port 80 via Nginx reverse proxy) 
│
│ (url.js points to backend domain) 
▼ [Backend Load Balancer -- ALB] ←  (Cloudflare CNAME) 
│ 
┌──┴──┐ 
▼ ▼ [Backend EC2 #1] [Backend EC2 #2] (Node.js on port 3000, Nginx reverse proxy on 80) │ ▼ [MongoDB Atlas -- travelmemory cluster] (Cloud database, access whitelisted)


Instance Reference Table

Server
Instance Name
Public IP
Private IP
Zone

Server
Instance Name
Public IP
Private IP
Zone

Task 1 — Backend EC2 Setup


1.1 Launch the Backend EC2 Instance
Analogy: You're renting a server in the cloud.
Go to AWS Console → EC2 → Launch Instance:

Setting                Value

Instance Name           rajesh_mern_backend
AMI                     Ubuntu Server 22.04 LTS (64-bit x86)
Instance Type            t2.micro (Free Tier eligible)
Key Pair                rajesh_key (create new if needed)
Network                 Default VPC vpc-0c5a8881cff1146d8
Auto-assign Public IP     Enable
Security Group            Select existing: launch-wizard-46



1.2 User Data Script (Auto-setup on Boot)

In the Advanced → User Data section, paste the following script. This script runs automatically when the server starts .


1.3 Configure the Backend Environment
SSH into the backend instance and set up the .env file:
# SSH into your instance
ssh -i rajesh_key.pem ubuntu@10.0.1.50
# Navigate to the backend folder
cd /home/ubuntu/TravelMemory/backend
# List files to confirm you're in the right place
ls
# Output: conn.js controllers index.js models package-lock.json package.json routes
# Create the environment file
nano .env
Add these values to the .env file:
MONGO_URI='mongodb+srv://rajesh_devops:SecurePass@123@travelmemory.cluster0.mongodb.net/travelmemory'
PORT=3000
Analogy: The .env file is like giving the kitchen staff a locked recipe book with the secret sauce formula (database credentials) and which window to serve from (PORT 3000). Never share this file publicly.
Verify it was saved:
cat .env
# Should show: MONGO_URI='mongodb+srv://...' and PORT=3000


1.4 Install Dependencies and Start the Backend
# Install all Node.js packages (like stocking the kitchen with ingredients)
npm install
# Start the backend server
sudo node index.js
Expected terminal output:
Node.js v18.17.1
Server started at http://localhost:3000
Verify in browser: Open http://10.0.1.50:3000 You will see Cannot GET / — this is correct. It means the server is running but there's no route at /. The API routes (e.g., /trip , /hello) will respond properly.


1.5 Whitelist Backend IP in MongoDB Atlas
Analogy: MongoDB Atlas is a locked pantry. By default, nobody can access it from the outside. You need to add your backend server's IP to the "allowed list" — like giving the kitchen a key to the pantry.
1. Login to cloud.mongodb.com
2. Go to your travelmemory cluster → Network Access
3. Click Add IP Address
4. Enter 10.0.1.50 (your backend EC2 public IP)
5. Click Confirm
Important: Do this for BOTH backend instances when you scale later (10.0.1.50 and 10.0.1.51). Without this step, the backend cannot connect to the database.


1.6 Install Nginx as Reverse Proxy on Backend
Analogy: Right now, your kitchen serves food through window #3000. But customers expect to come to the main entrance (port 80). Nginx is a receptionist who stands at the front door (port 80) and quietly redirects all orders to window #3000.


# Install Nginx 
sudo apt install nginx -y 
# Verify Nginx is running 
sudo systemctl status nginx

You should see Active: active (running) in the output.

Verify in browser: Open http://10.0.1.50 — you should see the "Welcome to Nginx!" default page.

Now configure Nginx as a reverse proxy


# Remove the default site config 
sudo unlink /etc/nginx/sites-enabled/default
# Go to available sites directory 
cd /etc/nginx/sites-available/
# Create a new config file 
sudo nano custom_server.conf
Paste this configuration:
server { listen 80; location / { proxy_pass http://localhost:3000; } }


What this means: "Nginx, whenever anyone visits port 80, forward their request to port 3000 where Node.js is running."


# Enable the config by creating a symlink 
sudo ln -s /etc/nginx/sites-available/custom_server.conf \ /etc/nginx/sites-enabled/custom_server.conf
# Test the configuration for syntax errors 
sudo service nginx configtest 
# Expected: [ OK ] 
sudo nginx -t 
# Restart Nginx to apply changes 
sudo service nginx restart

Verify reverse proxy: Open http://10.0.1.50 in browser. You should now see Cannot GET / — that's the Node.js backend responding through Nginx on port 80.

Task 2 — Frontend EC2 Setup
2.1 Launch the Frontend EC2 Instance

Analogy: You're renting a server in the cloud.
Go to AWS Console → EC2 → Launch Instance:

Setting                Value

Instance Name           rajesh_mern_frontend
AMI                     Ubuntu Server 22.04 LTS (64-bit x86)
Instance Type            t2.micro (Free Tier eligible)
Key Pair              (create new if needed)
Network                 Default VPC vpc-0c5a8881cff1146d8
Auto-assign Public IP     Enable
Security Group            Select existing: launch-wizard-46

2.2 Configure Frontend to Point to Backend

cd /home/ubuntu/TravelMemory/frontend ls 
# Output: README.md package-lock.json package.json public src 
Edit the src/url.js file: 
nano src/url.js

Update it to point to the backend's IP (or later, the backend domain):
export const baseUrl = "http://10.0.1.50"
Initially we point to the backend's direct IP. Later, after we set up Cloudflare, we'll update this to http://back.rajeshdevops.in



2.3 Install Dependencies and Run Frontend
cd /home/ubuntu/TravelMemory/frontend
npm install
# Start the React development server
npm start
Expected output:
Compiled successfully!
You can now view frontend in the browser.
Local: http://localhost:3000
On Your Network: http://172.31.12.91:3000
webpack compiled successfully
Verify: Open http://10.0.2.60:3000 in browser — you should see the Travel Memory application form.


2.4 Set Up Nginx Reverse Proxy on Frontend
Same steps as backend. Nginx will forward port 80 → port 3000 (where React runs).

sudo apt install nginx -y

sudo unlink /etc/nginx/sites-enabled/default 
cd /etc/nginx/sites-available/ 
sudo nano custom_server.conf 
server { listen 80; location / { proxy_pass http://localhost:3000; } } 
sudo ln -s /etc/nginx/sites-available/custom_server.conf \ /etc/nginx/sites-enabled/custom_server.conf 
sudo service nginx configtest 
sudo service nginx restart

Verify: Open http://10.0.2.60 — you should see the Travel Memory application running at port 80.

Task 3 — Load Balancing
3.1 Create Target Groups
A Target Group is a list of servers the load balancer can send traffic to. We create one for frontend servers and one for backend servers.

3.2 Create Application Load Balancers (ALB)
 The load balancer is the actual traffic decision maker that makes the decision in real time.
Frontend Load Balancer
EC2 → Load Balancers → Create Load Balancer → Application Load Balancer
backend Load Balancer
EC2 → Load Balancers → Create Load Balancer → Application Load Balancer


Task 4 — Domain Setup with Cloudflare
4.1 Add DNS Records in Cloudflare
Login to cloudflare.com → select domain rajeshdevops.in → DNS
Add two CNAME records:


Final Verification
Access the Application
Open your browser and visit: http://rajeshdevops.in
You should see the Travel Memory application with:
● A navigation bar: Travel Memory | Add Experience
● A form to submit travel entries (Trip Name, Trip Date, Hotels, etc.)
Test End-to-End Flow


Verify in MongoDB Atlas:
1. Login to cloud.mongodb.com
2. Go to cluster → Collections → travelmemory → tripdetails
3. You should see a new document:

Full Data Flow Summary

Here's exactly what happens when a user submits a travel memory on rajeshdevops.in:

Step 1: User visits http://rajeshdevops.in in browser

Step 2: Cloudflare DNS resolves → Frontend Load Balancer

Step 3: Frontend LB picks a healthy Frontend EC2 (rajesh_mern_frontend or frontend2)

Step 4: React app loads in the user's browser

Step 5: User fills the form and clicks Submit

Step 6: React sends a POST request to http://back.rajeshdevops.in/api/trips

Step 7: Cloudflare DNS resolves → Backend Load Balancer

Step 8: Backend LB routes to a healthy Backend EC2 (rajesh_mern_backend or backend2)

Step 9: Node.js (port 3000, via Nginx) processes the request

Step 10: Node.js connects to MongoDB Atlas using MONGO_URI in .env

Step 11: Trip data is saved in the 'travelmemory.tripdetails' collection

Step 12: Node.js sends a 200 OK response back

Step 13: React app shows the updated list of trips to the user
