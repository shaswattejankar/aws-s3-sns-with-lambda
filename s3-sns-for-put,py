import json
import boto3

def lambda_handler(event, context):
    print('function execution start...')
    
    # Initialize S3 and SNS clients
    s3_client = boto3.client('s3')
    sns_client = boto3.client('sns')
    
    # Define the bucket name
    bucket_name = 'your-bucket-name'

    # Create S3 bucket
    try:
        response = s3_client.create_bucket(
            Bucket=bucket_name,
            CreateBucketConfiguration={
                'LocationConstraint': 'us-east-2',
            },
        )
        print(f"\nBucket {bucket_name} created successfully.\n{response}")
    except Exception as e:
        print(f"\n\nError creating bucket: {e}\n")
        
        
    # Define the SNS topic name
    topic_name = 'S3_PutObject_Notification_Topic'

    # Create SNS topic
    try:
        response = sns_client.create_topic(Name=topic_name)
        topic_arn = response['TopicArn']
        print(f"\nSNS topic {topic_name} with topic arn {topic_arn} created successfully.")
    except Exception as e:
        print(f"\n\nError creating SNS topic: {e}\n")
    
    # Publish a message to the topic with email message attributes
    message = 'This is the message body.'
    subject = 'This is the email subject.'
    sns_client.publish(
        TopicArn=topic_arn,
        Message=message,
        Subject=subject,
    )
    
    # modify the SNS topic policy to allow the S3 bucket to publish messages   
    sns_client.set_topic_attributes(
    TopicArn=topic_arn,
    AttributeName='Policy',
    AttributeValue=json.dumps({
        'Version': '2008-10-17',
        'Statement': [
            {
                'Sid': 'AllowS3PutObjectNotification',
                'Effect': 'Allow',
                'Principal': {
                    'Service': 's3.amazonaws.com'
                },
                'Action': 'sns:Publish',
                'Resource': topic_arn,
                'Condition': {
                    'ArnLike': {
                        'aws:SourceArn': f'arn:aws:s3:::{bucket_name}'
                    }
                }
            }
        ]
    })
)
        
    # Subscribe email to the SNS topic
    try:
        email = 'your@email.com'
        response = sns_client.subscribe(
            TopicArn=topic_arn,
            Protocol='email',
            Endpoint=email
        )
        print(f"\nSubscribed {email} to SNS topic {topic_name}.")
    except Exception as e:
        print(f"\n\nError subscribing email to SNS topic: {e}\n")
        
    # Define S3 bucket notification configuration container that  will 
    # be used below for put_bucket_notification_configuration
    notification_config = {
        'TopicConfigurations': [
            {
                'TopicArn': topic_arn,
                'Events': ['s3:ObjectCreated:*']
            }
        ]
    }
    # Configure S3 bucket notification
    try:
        s3_client.put_bucket_notification_configuration(
            Bucket=bucket_name,
            NotificationConfiguration=notification_config
        )
        print(f"\nS3 bucket {bucket_name} notification configured successfully.")
    except Exception as e:
        print(f"\n\nError configuring S3 bucket notification: {e}\n")
    
    print('\nfunction \'return\' response reached')
    return {
        'statusCode': 200,
        'body': json.dumps('Hello from Lambda!')
    }
