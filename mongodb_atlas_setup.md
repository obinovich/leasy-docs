# MongoDB Atlas Setup Guide for Leasy Production Deployment

This guide provides instructions for setting up MongoDB Atlas as the production database for the Leasy application.

## Creating a MongoDB Atlas Account

1. Visit [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) and sign up for a free account
2. After signing up, create a new organization if prompted
3. Create a new project named "Leasy"

## Creating a Cluster

1. Click "Build a Database" 
2. Select the "FREE" tier option (M0 Sandbox)
3. Choose your preferred cloud provider (AWS, Google Cloud, or Azure)
4. Select a region closest to your target users for lowest latency
5. Click "Create Cluster" (this may take a few minutes to provision)

## Setting Up Database Access

1. In the left sidebar, click "Database Access" under SECURITY
2. Click "Add New Database User"
3. Create a user with the following settings:
   - Authentication Method: Password
   - Username: leasy_admin
   - Password: [Create a strong password]
   - Database User Privileges: Atlas admin
4. Click "Add User"

## Network Access Configuration

1. In the left sidebar, click "Network Access" under SECURITY
2. Click "Add IP Address"
3. For development, you can select "Allow Access from Anywhere" (not recommended for high-security production)
4. For production, add the specific IP addresses of your hosting provider
5. Click "Confirm"

## Connecting to Your Cluster

1. In the left sidebar, click "Database" under DEPLOYMENT
2. Click "Connect" on your cluster
3. Select "Connect your application"
4. Copy the connection string
5. Replace `<password>` with your database user's password
6. Update the MONGO_URI in your .env.production file with this connection string

## Running the Database Setup Script

1. Connect to your MongoDB Atlas cluster using MongoDB Shell:
   ```
   mongosh "mongodb+srv://leasy_admin:<password>@your-cluster-url/leasy" --apiVersion 1
   ```

2. Copy the contents of the db_setup.js script and run it in the MongoDB Shell
   OR
   Run the script directly using:
   ```
   mongosh "mongodb+srv://leasy_admin:<password>@your-cluster-url/leasy" --apiVersion 1 --file /path/to/db_setup.js
   ```

## Monitoring Your Database

1. In the MongoDB Atlas dashboard, you can monitor:
   - Metrics (operations, connections, etc.)
   - Alerts
   - Backup status

2. Set up alerts for:
   - High CPU usage
   - Connection limits
   - Storage capacity

## Backup Configuration

1. In the left sidebar, click "Backup" under DEPLOYMENT
2. For M0 free clusters, point-in-time recovery is not available, but you can:
   - Implement application-level backups
   - Upgrade to a paid tier for automated backups

## Performance Optimization

1. In the MongoDB Atlas dashboard, use the "Performance Advisor" to:
   - Identify slow queries
   - Get index recommendations

2. Monitor the "Metrics" tab to identify:
   - Connection issues
   - Query performance problems
   - Resource constraints

## Security Best Practices

1. Rotate database credentials regularly
2. Use IP allowlisting instead of allowing access from anywhere
3. Enable MongoDB Atlas Advanced Security features if using a paid tier
4. Implement database encryption for sensitive data
5. Regularly audit database access logs

## Scaling Considerations

When your application grows:

1. Upgrade from M0 to a paid tier (M10+) for:
   - More storage
   - Better performance
   - Additional features

2. Consider sharding for horizontal scaling if you expect:
   - Very large datasets
   - High write throughput

3. Implement read replicas for:
   - Distributing read operations
   - Improving read performance
   - Geographic distribution
