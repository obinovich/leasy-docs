# Production Deployment Guide for Leasy

This guide provides detailed instructions for deploying the Leasy application to production environments after you've completed your local review.

## Preparing for Production Deployment

Before deploying to production, ensure you have:

1. Completed local testing and are satisfied with the application functionality
2. Decided on your hosting platforms (see deployment_options.md for recommendations)
3. Set up accounts with the chosen hosting providers
4. Registered a domain name (optional but recommended)
5. Prepared your production database (MongoDB Atlas recommended)
6. Set up cloud storage for images (AWS S3 recommended)

## Production Environment Variables

### Backend Environment Variables

Create a `.env.production` file in the backend directory with the following variables:

```
PORT=5000
NODE_ENV=production
MONGO_URI=mongodb+srv://<username>:<password>@<cluster>.mongodb.net/leasy
JWT_SECRET=<your_strong_production_secret>
CLIENT_URL=https://your-domain.com
AWS_ACCESS_KEY_ID=<your_aws_access_key>
AWS_SECRET_ACCESS_KEY=<your_aws_secret_key>
AWS_REGION=<your_aws_region>
S3_BUCKET_NAME=<your_s3_bucket_name>
```

### Frontend Environment Variables

Create a `.env.production` file in the frontend directory with:

```
REACT_APP_API_URL=https://api.your-domain.com/api
GENERATE_SOURCEMAP=false
```

## Recommended Deployment: Netlify (Frontend) + Render (Backend)

### Deploying Frontend to Netlify

1. Create a Netlify account at [netlify.com](https://www.netlify.com/)
2. Install Netlify CLI: `npm install -g netlify-cli`
3. Build the frontend:
   ```bash
   cd frontend
   npm run build
   ```
4. Deploy to Netlify:
   ```bash
   netlify deploy --prod
   ```
   Or connect your GitHub repository for continuous deployment

5. Configure environment variables in the Netlify dashboard
6. Set up your custom domain (if applicable)

### Deploying Backend to Render

1. Create a Render account at [render.com](https://render.com/)
2. Create a new Web Service
3. Connect your GitHub repository or upload the backend code
4. Configure the service:
   - Name: leasy-api
   - Environment: Node
   - Build Command: `npm install`
   - Start Command: `npm start`
   - Plan: Select appropriate plan (Free for testing, Standard for production)
5. Add environment variables from your `.env.production`
6. Deploy the service
7. Set up your custom domain for the API (if applicable)

## Alternative Deployment: DigitalOcean App Platform

### Deploying Full Stack to DigitalOcean

1. Create a DigitalOcean account
2. Create a new App
3. Connect your GitHub repository or upload the code
4. Configure the app with two components:
   - Frontend (Static Site)
   - Backend (Web Service)
5. Add environment variables for both components
6. Deploy the app
7. Set up your custom domain

## Setting Up MongoDB Atlas for Production

Follow the detailed instructions in `mongodb_atlas_setup.md` to:

1. Create a MongoDB Atlas account
2. Set up a cluster
3. Configure database access
4. Set up network access
5. Run the database initialization script
6. Set up monitoring and backups

## Setting Up AWS S3 for Image Storage

Follow the detailed instructions in `aws_s3_setup.md` to:

1. Create an AWS account
2. Create an S3 bucket
3. Configure CORS
4. Create an IAM user
5. Set up bucket policies
6. Configure environment variables

## Setting Up a Custom Domain

### Domain Registration

1. Register a domain with a provider like Namecheap, Google Domains, or AWS Route 53
2. Configure DNS settings to point to your hosting providers:
   - Frontend: Create an A record or CNAME pointing to your Netlify/Vercel deployment
   - Backend API: Create an A record or CNAME pointing to your Render/Heroku deployment

### SSL Configuration

1. Most hosting providers offer automatic SSL certificate generation
2. For custom setups, use Let's Encrypt to generate free SSL certificates
3. Ensure all resources are served over HTTPS

## Post-Deployment Tasks

After deploying to production:

1. **Verify all functionality** works in the production environment
2. **Set up monitoring** using services like Sentry for error tracking
3. **Configure analytics** to track user behavior
4. **Set up regular backups** of your database
5. **Implement CI/CD** for automated deployments
6. **Create a maintenance plan** for updates and security patches

## Security Checklist

Ensure your production deployment has:

- [ ] Strong, unique passwords for all services
- [ ] JWT secret that is long, complex, and unique to production
- [ ] Rate limiting enabled on the API
- [ ] Security headers configured (Helmet is included in the backend)
- [ ] CORS properly configured
- [ ] Database access restricted to application servers
- [ ] AWS credentials with minimal required permissions
- [ ] Regular security updates for all dependencies

## Scaling Considerations

As your application grows:

1. **Database Scaling**:
   - Upgrade MongoDB Atlas tier as needed
   - Implement caching for frequently accessed data

2. **Backend Scaling**:
   - Move to auto-scaling plans on your hosting provider
   - Consider containerization with Docker and Kubernetes for larger scale

3. **Frontend Performance**:
   - Implement a CDN for static assets
   - Further optimize image loading and caching

## Troubleshooting Production Issues

### Common Issues:

1. **CORS Errors**:
   - Verify the CLIENT_URL in backend environment variables
   - Check CORS configuration in server.js
   - Ensure frontend is making requests to the correct API URL

2. **Database Connection Issues**:
   - Check MongoDB Atlas network access settings
   - Verify connection string in environment variables
   - Check for MongoDB Atlas service outages

3. **Image Upload Failures**:
   - Verify AWS credentials and permissions
   - Check S3 bucket CORS configuration
   - Ensure proper error handling in the frontend

## Maintenance and Updates

1. Regularly update dependencies to patch security vulnerabilities
2. Monitor application performance and error rates
3. Implement feature updates through your CI/CD pipeline
4. Maintain database backups and test restoration procedures
5. Document all configuration changes and deployments

## Rollback Procedures

In case of critical issues after deployment:

1. Keep previous deployment versions accessible
2. Document the steps to roll back to previous versions
3. Have database backup points before major updates
4. Test rollback procedures before they're needed

## Support and Resources

For additional help with deployment:

- Netlify Documentation: [docs.netlify.com](https://docs.netlify.com/)
- Render Documentation: [render.com/docs](https://render.com/docs)
- MongoDB Atlas Documentation: [docs.atlas.mongodb.com](https://docs.atlas.mongodb.com/)
- AWS S3 Documentation: [docs.aws.amazon.com/s3](https://docs.aws.amazon.com/s3)
