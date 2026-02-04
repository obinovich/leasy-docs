# Leasy Local Deployment Instructions

This guide provides detailed instructions for deploying the Leasy application locally for review purposes.

## Prerequisites

Before you begin, ensure you have the following installed:

- **Node.js** (v14.0.0 or higher)
- **npm** (v6.0.0 or higher)
- **MongoDB** (v4.0 or higher)
- **Git** (optional, for version control)

## Step 1: Set Up MongoDB

### Windows
1. Download and install MongoDB Community Server from [mongodb.com](https://www.mongodb.com/try/download/community)
2. Follow the installation wizard
3. Create a data directory: `mkdir -p C:\data\db`
4. Start MongoDB: `"C:\Program Files\MongoDB\Server\5.0\bin\mongod.exe" --dbpath="C:\data\db"`

### macOS
1. Install MongoDB using Homebrew: `brew tap mongodb/brew && brew install mongodb-community`
2. Start MongoDB: `brew services start mongodb-community`

### Linux
1. Follow the instructions for your distribution at [mongodb.com/docs/manual/administration/install-on-linux/](https://www.mongodb.com/docs/manual/administration/install-on-linux/)
2. Start MongoDB: `sudo systemctl start mongod`

## Step 2: Set Up the Backend

1. Navigate to the backend directory:
   ```bash
   cd leasy_project/backend
   ```

2. Create a `.env` file in the backend directory with the following content:
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

4. Initialize the database (optional):
   ```bash
   # Start MongoDB shell
   mongosh
   
   # In the MongoDB shell, run:
   load("scripts/db_setup.js")
   
   # Exit the shell
   exit
   ```

5. Start the backend server:
   ```bash
   npm run dev
   ```

6. Verify the backend is running by visiting [http://localhost:5000/api](http://localhost:5000/api) in your browser. You should see a message: "Leasy API is running"

## Step 3: Set Up the Frontend

1. Open a new terminal window and navigate to the frontend directory:
   ```bash
   cd leasy_project/frontend
   ```

2. Create a `.env` file in the frontend directory with the following content:
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

5. The application should automatically open in your browser at [http://localhost:3000](http://localhost:3000)

## Step 4: Create Test Users and Listings

1. Register a new user account through the application interface
2. Create a few property listings with different details
3. For testing the messaging system, create a second user account and interact between accounts

## Troubleshooting

### MongoDB Connection Issues

If you encounter MongoDB connection issues:

1. Ensure MongoDB is running: 
   - Windows: Check Task Manager for mongod.exe
   - macOS/Linux: Run `ps aux | grep mongo`

2. Verify connection string in `.env` file:
   - Default: `mongodb://localhost:27017/leasy`
   - For older MongoDB versions, you may need to modify the connection options in `config/db.js`

3. Check MongoDB logs:
   - Windows: `C:\data\db\mongod.log`
   - macOS/Linux: `/var/log/mongodb/mongod.log`

### Backend Server Issues

If the backend server fails to start:

1. Check for port conflicts:
   - Another application might be using port 5000
   - Change the PORT value in `.env` file if needed

2. Verify Node.js version:
   - Run `node -v` to check your version
   - Update if below v14.0.0

3. Check for dependency issues:
   - Run `npm install` again
   - Check for error messages during installation

### Frontend Issues

If the frontend fails to start or connect to the backend:

1. Verify the backend is running and accessible
2. Check the REACT_APP_API_URL in the frontend `.env` file
3. Clear browser cache or try in incognito/private mode
4. Check browser console for error messages

## Running in Production Mode Locally

To test the production build locally:

### Backend
```bash
cd backend
NODE_ENV=production npm start
```

### Frontend
```bash
cd frontend
npm run build
npx serve -s build
```

The production build will be available at [http://localhost:3000](http://localhost:3000) (or the port specified by serve).

## Next Steps

After successfully deploying and testing the application locally, you can:

1. Review the code and functionality
2. Provide feedback on any issues or improvements
3. Explore the deployment options in the documentation
4. Prepare for production deployment

For production deployment instructions, refer to the deployment documentation provided in the package.
