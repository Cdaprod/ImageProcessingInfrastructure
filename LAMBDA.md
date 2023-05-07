To make your image processing application compatible with serverless functions, you can use a cloud provider like AWS Lambda. AWS Lambda supports custom runtime, so you can use the provided `Dockerfile` as a base to create a compatible container image. Here's how you can do it:

1. Modify the `Dockerfile` to use the AWS Lambda Python runtime as a base image:

```Dockerfile
# Use AWS Lambda Python runtime as a parent image
FROM public.ecr.aws/lambda/python:3.9

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt
RUN pip install --trusted-host pypi.python.org -r requirements.txt

# Set the default command to run your image processing function
CMD ["app.lambda_handler"]
```

2. Modify the `requirements.txt` file to include the AWS SDK for Python (Boto3):

```
opencv-python-headless
Pillow
boto3
```

3. Update your `app.py` file to create a Lambda handler function:

```python
import json
import os
import boto3
from PIL import Image

def lambda_handler(event, context):
    # Your image processing logic here

    response = {
        "statusCode": 200,
        "body": json.dumps("Image processing complete.")
    }
    return response

if __name__ == "__main__":
    # Local testing code
    pass
```

4. Build and tag the Docker image:

```bash
docker build -t image_processing_lambda .
```

5. Create an Amazon Elastic Container Registry (ECR) repository:

```bash
aws ecr create-repository --repository-name image-processing-lambda
```

Note the `repositoryUri` value from the output.

6. Authenticate Docker to your Amazon ECR registry:

```bash
aws ecr get-login-password --region <region> | docker login --username AWS --password-stdin <repositoryUri>
```

Replace `<region>` and `<repositoryUri>` with the appropriate values.

7. Tag the Docker image with the ECR repository URI:

```bash
docker tag image_processing_lambda:latest <repositoryUri>:latest
```

8. Push the Docker image to the ECR repository:

```bash
docker push <repositoryUri>:latest
```

9. Create an AWS Lambda function using the Docker image:

```bash
aws lambda create-function --function-name imageProcessingFunction \
    --package-type Image \
    --code ImageUri=<repositoryUri>:latest \
    --role <role_arn>
```

Replace `<repositoryUri>` with the ECR repository URI and `<role_arn>` with the ARN of the IAM role you've created for Lambda.

Now, your image processing application is compatible with serverless functions using AWS Lambda. You can write your image processing logic inside the `lambda_handler` function in `app.py` and deploy your application using the provided `Dockerfile`.