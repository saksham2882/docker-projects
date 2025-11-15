# Base Image
FROM python:3.9

# Working directory
WORKDIR /app

# Copy src code to container
COPY . .

# Run the build commands
RUN pip install -r requirements.txt

# Serve the app / run
ENTRYPOINT ["python", "run.py"]
