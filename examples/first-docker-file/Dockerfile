# Use a slim, official Python image for smaller size and security
FROM python:3.11-slim

# Set the working directory
WORKDIR /app

# Copy only requirements first to leverage Docker layer caching
COPY requirements.txt .

# Install dependencies without saving cache to keep image small
RUN pip install --no-cache-dir -r requirements.txt

# Copy the rest of the application files
COPY . .

# Set environment variables
ENV NAME=World

# Run the application
CMD ["python3", "app.py"]
