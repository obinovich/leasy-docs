# AWS S3 Setup Guide for Leasy Image Storage

This guide provides instructions for setting up Amazon S3 for storing images in the Leasy application's production environment.

## Creating an AWS Account

1. Visit [AWS](https://aws.amazon.com/) and sign up for an account if you don't already have one
2. Complete the verification process

## Creating an S3 Bucket

1. Log in to the AWS Management Console
2. Navigate to the S3 service
3. Click "Create bucket"
4. Enter a unique bucket name (e.g., "leasy-property-images")
5. Select the AWS Region closest to your target users
6. Configure bucket settings:
   - Uncheck "Block all public access" (since we need public read access for images)
   - Acknowledge the warning about making the bucket public
   - Enable versioning (optional but recommended)
   - Leave other settings as default
7. Click "Create bucket"

## Configuring CORS for the Bucket

1. Select your newly created bucket
2. Go to the "Permissions" tab
3. Scroll down to "Cross-origin resource sharing (CORS)"
4. Click "Edit" and add the following configuration:
   ```json
   [
     {
       "AllowedHeaders": ["*"],
       "AllowedMethods": ["GET", "PUT", "POST", "DELETE", "HEAD"],
       "AllowedOrigins": ["https://leasy-app.netlify.app", "http://localhost:3000"],
       "ExposeHeaders": []
     }
   ]
   ```
5. Click "Save changes"

## Creating an IAM User for S3 Access

1. Navigate to the IAM service in the AWS Management Console
2. Click "Users" in the left sidebar
3. Click "Add users"
4. Enter a username (e.g., "leasy-s3-user")
5. Select "Access key - Programmatic access" as the access type
6. Click "Next: Permissions"
7. Click "Attach existing policies directly"
8. Search for and select "AmazonS3FullAccess" (for development; use more restricted permissions for production)
9. Click "Next: Tags" (optional)
10. Click "Next: Review"
11. Click "Create user"
12. **IMPORTANT**: Download the CSV file or copy the Access Key ID and Secret Access Key - you will not be able to view the Secret Access Key again

## Setting Up Bucket Policy

1. Return to your S3 bucket
2. Go to the "Permissions" tab
3. Click "Bucket policy"
4. Add the following policy (replace `YOUR-BUCKET-NAME` with your actual bucket name):
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "PublicReadGetObject",
         "Effect": "Allow",
         "Principal": "*",
         "Action": "s3:GetObject",
         "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME/*"
       }
     ]
   }
   ```
5. Click "Save changes"

## Configuring Environment Variables

Add the following environment variables to your production environment:

```
AWS_ACCESS_KEY_ID=your_access_key_id
AWS_SECRET_ACCESS_KEY=your_secret_access_key
AWS_REGION=your_selected_region
S3_BUCKET_NAME=your_bucket_name
```

## Testing S3 Integration

1. Deploy your application with the S3 configuration
2. Upload an image through the application
3. Verify that the image is stored in your S3 bucket
4. Check that the image is accessible via its public URL

## Security Best Practices

1. **IAM Permissions**: Limit the IAM user's permissions to only what's needed:
   - s3:PutObject
   - s3:GetObject
   - s3:DeleteObject
   - Restrict to specific bucket resources

2. **Bucket Policy**: Consider restricting access by IP or referrer

3. **Access Keys**: Rotate access keys regularly

4. **Monitoring**: Set up CloudWatch alerts for unusual activity

5. **Encryption**: Enable server-side encryption for sensitive images

## Cost Management

1. **Lifecycle Rules**: Set up lifecycle rules to delete unused or temporary images
2. **Storage Class**: Use appropriate storage classes for different types of images
3. **Monitoring**: Set up billing alerts to monitor costs

## Troubleshooting

### Common Issues:

1. **403 Forbidden Errors**:
   - Check bucket policy
   - Verify IAM permissions
   - Ensure CORS is properly configured

2. **Slow Uploads**:
   - Check file sizes
   - Consider using multi-part uploads for large files
   - Verify network connectivity

3. **Missing Images**:
   - Verify the correct bucket name is being used
   - Check that the image path is correct
   - Ensure the image was successfully uploaded

## Scaling Considerations

As your application grows:

1. **CloudFront**: Consider using Amazon CloudFront as a CDN for faster image delivery
2. **Image Processing**: Implement server-side image processing with AWS Lambda
3. **Regional Buckets**: Create regional buckets for users in different geographic areas
