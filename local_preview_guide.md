# Leasy Local Preview Guide

This guide will help you run and review the Leasy application locally before deploying to production.

## Prerequisites

- Node.js (v14 or higher)
- MongoDB (v4.0 or higher)
- npm or yarn

## Setup Instructions

### 1. Start MongoDB

Make sure MongoDB is running on your local machine. If you haven't installed MongoDB yet, please follow the instructions in the `mongodb_setup_guide.md` file.

For Windows WSL users, refer to `mongodb_wsl_setup_guide.md`.

### 2. Backend Setup

1. Navigate to the backend directory:
   ```bash
   cd leasy_project/backend
   ```

2. Create a `.env` file with the following content:
   ```
   PORT=5000
   MONGO_URI=mongodb://localhost:27017/leasy
   JWT_SECRET=your_local_secret_key
   CLIENT_URL=http://localhost:3000
   NODE_ENV=development
   ```

3. Install dependencies:
   ```bash
   npm install
   ```

4. Start the backend server:
   ```bash
   npm run dev
   ```

5. The backend should now be running at http://localhost:5000

### 3. Frontend Setup

1. Open a new terminal window and navigate to the frontend directory:
   ```bash
   cd leasy_project/frontend
   ```

2. Create a `.env` file with the following content:
   ```
   REACT_APP_API_URL=http://localhost:5000/api
   ```

3. Install dependencies:
   ```bash
   npm install
   ```

4. Start the frontend development server:
   ```bash
   npm start
   ```

5. The frontend should automatically open in your browser at http://localhost:3000

## Testing the Application

### User Authentication

1. Register a new account using the Register page
2. Log in with your credentials
3. Test that you remain logged in when refreshing the page
4. Test the logout functionality

### Property Listings

1. Create a new property listing with images
2. View the listing details
3. Edit your listing
4. Delete your listing

### User Interactions

1. View other listings (you may need to create multiple accounts)
2. Test the messaging system between users
3. Test saving favorite listings

## Key Features to Review

- **Authentication System**: Registration, login, session persistence
- **Property Management**: Creating, editing, and deleting listings
- **Image Handling**: Uploading and displaying property images
- **User Interface**: Responsive design, navigation, forms
- **Search Functionality**: Filtering and searching for properties
- **Messaging System**: Communication between landlords and tenants

## Reviewing Code Changes

The main changes made to prepare the application for production include:

### Frontend Optimizations
- Added compression and chunk splitting for better performance
- Implemented environment-specific configuration
- Enhanced metadata and fixed code issues

### Backend Enhancements
- Added security middleware (helmet, rate limiting)
- Implemented comprehensive error handling
- Created environment-specific configuration
- Optimized static file serving with cache control

### Database Configuration
- Created MongoDB Atlas setup for production
- Implemented database initialization scripts
- Added indexes for better performance

### Image Storage
- Implemented flexible storage system (local/S3)
- Added proper file validation and handling

## Next Steps After Review

Once you've reviewed the application and are satisfied with its functionality, we can proceed with:

1. Deploying to production hosting platforms
2. Setting up a custom domain
3. Configuring SSL certificates
4. Setting up continuous integration/deployment

Please provide feedback on any issues or improvements needed before proceeding to production deployment.
