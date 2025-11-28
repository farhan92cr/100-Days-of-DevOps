Step 1: Connect to Application Server 3
bash
ssh banner@stapp03
Password: BigGr33n

Step 2: Navigate to the Python App Directory
bash
cd /python_app
ls -la
Step 3: Check the Existing Files
Verify the requirements.txt file exists and check for server.py:

bash
ls -la src/
cat src/requirements.txt
Step 4: Create the Dockerfile
Create the Dockerfile in the /python_app directory:

bash
sudo tee Dockerfile > /dev/null <<'EOF'
FROM python:3.9

WORKDIR /app

COPY src/requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY src/ .

EXPOSE 8089

CMD ["python", "server.py"]
EOF
Step 5: Verify the Dockerfile
Check that the Dockerfile was created correctly:

bash
cat Dockerfile
Step 6: Build the Docker Image
Build the image named nautilus/python-app:

bash
sudo docker build -t nautilus/python-app .
Step 7: Verify the Image was Built
Check that the image was successfully built:

bash
sudo docker images | grep nautilus/python-app
Step 8: Create and Run the Container
Create a container named pythonapp_nautilus with port mapping:

bash
sudo docker run -d --name pythonapp_nautilus -p 8097:8089 nautilus/python-app
Step 9: Verify the Container is Running
Check that the container is running:

bash
sudo docker ps | grep pythonapp_nautilus
Step 10: Test the Application
Test the application using curl:

bash
curl http://localhost:8097/

