# Choose the base image
FROM python:3.9-slim
# Set the working directory
WORKDIR /app
# Copy the current directory contents into the container at /app
COPY . /app
# Update the package lists
RUN apt-get update && apt-get upgrade -y && rm -rf /var/lib/apt/lists/*
# Insstall required Python libraries
RUN pip install --no-cache-dir -r requirements.txt
# Expose port 5000
EXPOSE 5000
# Set the environment variables
ENV FLASK_APP=app.py
ENV STUDENT="Tomislav Keščec"
ENV COLLEGE="Algebra University College"
# Start flask application
CMD ["flask", "run", "--host=0.0.0.0"]