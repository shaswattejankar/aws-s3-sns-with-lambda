# aws-s3-sns-with-lambda

This is the Lambda code that uses boto3 to:

1. Create a s3 bucket

2. Create a sns topic, add a message field to it, and attaches a sns topic policy to allow the created s3 bucket to publish message (Note)

3. Sends a subscription email to given email in the code

4. And configures the bucket with the created sns topic


+ Do create and attach a role to Lambda for s3 and sns full access.

+ Put your bucket name instead of <code>your-bucket-name</code> and your email id instead of <code>test@email.com</code>

+ This code is only for ObjectCreate event. Adding other triggers is easy:

  ```bash
    'Events': [
                's3:ObjectCreated:*',
                's3:ObjectRemoved:*',
                's3:ObjectRestore:*',
                's3:ObjectCreated:Put',
                's3:ObjectCreated:Post',
                's3:ObjectCreated:Copy',
                .
                .
                .
            ]
  ```
