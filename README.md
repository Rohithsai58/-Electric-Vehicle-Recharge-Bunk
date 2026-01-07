#  EV Recharge Bunk 

## Project Overview

**eVoltzo** is a full-stack web platform designed to connect EV owners with charging station providers through a sharing economy model. As the adoption of electric vehicles rises, many drivers still face accessibility challenges — especially those without home chargers. This platform enables individuals and businesses with underutilized chargers to list their stations, while allowing EV drivers to discover, book, and pay for nearby charging slots in real time. With role-based access, Firebase authentication, Stripe-powered payments, map-based station discovery, and a responsive React UI, the platform streamlines the EV charging experience. It also includes admin oversight for quality control and is fully Dockerized and deployed on AWS for scalability and reliability.

---

## Tech Stack

**Frontend**: React, Tailwind, SCSS  
**Backend**: Node.js, Express, MongoDB, Redis  
**Auth**: Firebase Authentication  
**Payments**: Stripe  
**Geolocation Services**: OpenRouteService API, Leaflet.js, and browser geolocation for map-based station discovery and routing  
**Deployment**: AWS S3, CloudFront, EC2, Docker, Nginx, GitHub Actions

---

## Course vs Independent Technologies

### Course Technologies

-   React (Frontend)
-   Redis (Cache layer)
-   Firebase Authentication (User identity and sessions)

### Independent Technologies

-   Stripe (Payment gateway for booking EV stations)
-   AWS (Deployment: S3, EC2, CloudFront)
-   Docker (Containerization for backend and database services)

---

## Deployment Architecture

-   **Frontend**: React app deployed to AWS S3, served via CloudFront over HTTPS
-   **Backend**: Node.js API containerized with Docker, reverse proxied via Nginx on EC2
-   **Database**: MongoDB
-   **Cache**: Redis (Docker)
-   **Payment**: Stripe Gateway

<img width="756" alt="image" src="https://github.com/user-attachments/assets/3ac35796-477a-43de-8905-e86eb50725e2" />

---

## Production

You can access the production version of the platform here: **[https://d1jbturyp4djjd.cloudfront.net/](https://d1jbturyp4djjd.cloudfront.net/)**

---

## Getting Started (Local)

### 1. Clone the Repo

```bash
git clone https://github.com/gvaswani98/cs-554-final-project
cd cs-554-final-project
```

### 2. Environment Setup Instructions

Create the following `.env` files before running the application:

#### `server/.env`

```env
CLIENT_ORIGIN=http://localhost:5173
PORT=3000
NODE_ENV=dev
MONGO_URL=<YOUR_MONGO_URL>
MONGO_DATABASE=<YOUR_MONGO_DATABASE>
SESSION_SECRET=<YOUR_SESSION_SECRET>
FIREBASE_PROJECT_ID=<YOUR_FIREBASE_PROJECT_ID>
FIREBASE_PRIVATE_KEY=<YOUR_FIREBASE_PRIVATE_KEY>
FIREBASE_CLIENT_EMAIL=<YOUR_FIREBASE_CLIENT_EMAIL>
REDIS_URL=redis://localhost:6379
ORS_BASE_URL=https://api.openrouteservice.org/v2/
ORS_API_KEY=<YOUR_ORS_API_KEY>
STRIPE_SECRET_KEY=<YOUR_STRIPE_SECRET_KEY>
STRIPE_WEBHOOK_SECRET=<YOUR_STRIPE_WEBHOOK_SECRET>
CLOUDINARY_CLOUD_NAME=<YOUR_CLOUDINARY_CLOUD_NAME>
CLOUDINARY_API_SECRET=<YOUR_CLOUDINARY_API_SECRET>
CLOUDINARY_API_KEY=<YOUR_CLOUDINARY_API_KEY>
MAPSCO_API_KEY=<YOUR_MAPSCO_API_KEY>
```

#### `client/.env`

```env
VITE_STRICT_MODE=false
VITE_SERVER_API=http://localhost:3000
VITE_FIREBASE_API_KEY=<YOUR_FIREBASE_API_KEY>
VITE_FIREBASE_AUTH_DOMAIN=<YOUR_FIREBASE_AUTH_DOMAIN>
VITE_FIREBASE_PROJECT_ID=<YOUR_FIREBASE_PROJECT_ID>
VITE_FIREBASE_STORAGE_BUCKET=<YOUR_FIREBASE_STORAGE_BUCKET>
VITE_FIREBASE_MESSAGING_SENDER_ID=<YOUR_FIREBASE_MESSAGING_SENDER_ID>
VITE_FIREBASE_APP_ID=<YOUR_FIREBASE_APP_ID>
VITE_GEO_CODING=<YOUR_GEO_CODING_KEY>
VITE_STRIPE_PUBLISHABLE_KEY=<YOUR_STRIPE_PUBLISHABLE_KEY>
```

---

### 3. Start the Frontend

Replace the contents of your `docker-compose.yml` in the root directory with the following:

```yaml
version: '3.8'

services:
    backend:
        build:
            context: ./server
        env_file:
            - ./server/.env
        depends_on:
            - redis
        environment:
            - REDIS_URL=redis://redis:6379
        ports:
            - '3000:3000'

    redis:
        image: redis/redis-stack:latest
        volumes:
            - redis-data:/data
        ports:
            - '6379:6379'

    stripe:
        image: stripe/stripe-cli
        depends_on:
            - backend
        environment:
            - STRIPE_API_KEY=<YOUR_STRIPE_API_KEY>
            - STRIPE_DEVICE_NAME=stripe-docker
        command: listen --forward-to http://backend:3000/webhooks/stripe

volumes:
    redis-data:
```

Then run:

```bash
cd client
npm install
npm run dev
```

### 4. Start the Backend with Docker

```bash
cd server
docker-compose up --build
```

---

### 5. Seed the Database

```bash
cd server
node seed.js
```

This script creates initial users, stations, and other required data to test the system.

---

## Test Users

| Role     | Email                 | Password      |
| -------- | --------------------- | ------------- |
| User     | user1@evoltzo.com     | User@1234     |
| Provider | provider1@evoltzo.com | Provider@1234 |
| Admin    | admin1@evoltzo.com    | Admin@1234    |

---

## Limitations

-   **Slower Map Search Responsiveness**  
    The application uses an open-source map provider (e.g., Leaflet with OpenStreetMap), which can occasionally be slower in processing search queries or resolving addresses compared to commercial services like Google Maps.

-   **Inaccurate or Missing Location Results**  
    You can search for popular locations such as Journal Square, Indian Square, 1 Castle Point Terrace, Penn Station, and others. However, some locations might not appear due to the limitations of the open-source data.

-   **Station Addition Fails for Unrecognized Addresses**  
    When adding a station, the same geocoding API is used. If the entered address cannot be resolved to valid coordinates, the station will not be added.

-   **Routing API Request Limits**  
    Directions are powered by a free open-source routing service with a monthly cap of 2,000 requests. If this limit is reached, route generation may stop working temporarily.

-   **No Real-Time Navigation Support**  
    The platform currently does not support live, turn-by-turn navigation to charging stations.

-   **Stripe Payment Limitations**  
    Stripe is a reliable and developer-friendly payment API, but it is not supported in all countries. This limits the platform's ability to scale globally.

-   **No IoT-Based Charging Measurement**  
    The current system assumes charging sessions are fixed-duration and priced accordingly. There is no integration with IoT hardware to track actual electricity consumption.

---

## Future Enhancements

Many of the above limitations can be addressed in future upgrades. Using paid services like the **Google Maps API** would improve the accuracy and performance of map search, routing, and navigation. **Integrating smart chargers or IoT devices** would enable accurate, real-time measurement of power usage, making billing more transparent and usage-based. Expanding support for **multiple payment gateways** could also improve international usability.

---

## Contributors

-   Alexander Lytvyn
-   Druv Mojila
-   Frank Cornacchiulo
-   Gautam Vaswani
-   Vijay Khot
