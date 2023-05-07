To create an image processing infrastructure using Docker, you can follow these steps:

1. Create a new directory for your project and navigate to it:

```bash
mkdir image_processing && cd image_processing
```

2. Create a `Dockerfile` in the project directory:

```Dockerfile
# Use an official Python runtime as a parent image
FROM python:3.9-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt
RUN pip install --trusted-host pypi.python.org -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

3. Create a `requirements.txt` file in the project directory to list required packages:

```
opencv-python-headless
Pillow
Flask
```

4. Create a sample `app.py` file in the project directory, which will serve as a placeholder for your image processing scripts:

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello, World!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=80)
```

5. Build the Docker image:

```bash
docker build -t image_processing_app .
```

6. Run the Docker container:

```bash
docker run -p 4000:80 image_processing_app
```

Now you have a basic infrastructure for an image processing application in a Docker container. You can replace the `app.py` file with your actual image processing scripts and update the `requirements.txt` file as needed to include additional libraries.

Remember to rebuild the Docker image and run the container again after making changes to the scripts or the requirements file.