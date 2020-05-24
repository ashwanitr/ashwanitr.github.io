---
layout: post
title: Creating Presigned URLs in AWS
category: Tutorial
---


 AWS S3 (Simple Storage Service), is one of the most popular storage solutions available in the market. One of the ways to allow the client side to store or download data to the AWS S3 is a pre-signed URL. You may also note that it is possible to upload a file (PutObject) to an S3 Bucket Using the AWS SDK. Not only does this approach require AWS Credentials, but also the inclusion of the AWS SDK in the client project, which is unnecessary to only upload a file. 

In AWS terminology a Presigned URL is defined as the URL that gives temporary access to the unauthorized users to the S3 objects. The URL is generated using IAM credentials or a role that has permissions to write to the bucket. Using these URLs, a user can either Download(Read) or Upload(Write) an object to the S3. These URLs remain valid for a limited period of time, and this time is defined during the creation of the pre-signed URL.
<br>
<br>
These "pre-signed URLs" include a cryptographically signed payload - by a secret key - that contains the details of the source or destination of the content.
To create a pre-signed URL we need three parameters:
1. Bucket: Name of the bucket in which the object is (in case of download) or in which it'll be uploaded.
1. Key: Name of the Object (file)
1. Expires: The time for which the URL will be valid. Clients won't be able to use the URL post the time limit.

In this tutorial, I'll demonstrate how to create a pre-signed URL using the AWS SDK on AWS Lambda. Before writing any code for the generation of Presigned URL takes care of the following points:
* Make sure that the S3 Bucket has GetObject and PUTObject policies.
* The lambda function has the permission to read and write in S3 buckets.

Here's a code snippet for creating a Presigned URL for Downloading Objects from the bucket:
<br>
<br>
{% highlight Python %}
# The following code will generate a Presigned URL for downloading.
def create_presigned_url(bucket_name, object_name, expiration=3600):
    """
    Generate a presigned URL to share an S3 object
    :param bucket_name: string
    :param object_name: string
    :param expiration: Time in seconds for the presigned URL to remain valid
    :return: Presigned URL as string. If error, returns None.   
    """
    # Generate a presigned URL for the S3 object
    s3_client = boto3.client('s3')
    try:
        response = s3_client.generate_presigned_url('get_object',
                              Params={'Bucket': bucket_name,'Key': object_name},
                              ExpiresIn=expiration)
    except Exception as e:
        print(e)
        return None
    # The response contains the presigned URL
    return response
{% endhighlight %}

<br>

Code snippet for creating a Presigned URL for Uploading Objects to the bucket:
<br>
<br>
{% highlight Python %}

def get_presigned_url(uuid,
            fields=None, conditions=None, expiration=3600):
    """ Return a presigned URL to upload a file"""

    try:
        object_name = "key_name_you_want_on_s3"
        response = S3_CLIENT.generate_presigned_post(s3_bucket_name,object_name,
                                          Fields=fields,
                                          Conditions=conditions,
                                          ExpiresIn=expiration)
    except ClientError as e:
        print(e)
        return None
    # The response contains the presigned URL and required fields
    return response

{% endhighlight %}
<br>
The above code will return a temporary URL for downloading or uploading a file to the S3 bucket. Now the client application can use this URL to download or Upload the files, without any exposure of the AWS credentials in the Client App.


So, In a nutshell, we can say that pre-signed Urls gives temporary access to the S3 objects to the User without exposing any auth data, and the above tutorial guide you to accomplish the same.