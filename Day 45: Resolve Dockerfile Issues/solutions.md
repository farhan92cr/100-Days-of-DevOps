Step 1: Check the Current Dockerfile
cd /opt/docker
cat -n Dockerfile
Step 2: Identify the Issue
The issue is in lines 3, 5, 7, and 9 - they're using conf.d/httpd.conf instead of the correct path /usr/local/apache2/conf/httpd.conf.

Step 3: Fix the Dockerfile
# Create a backup first
sudo cp Dockerfile Dockerfile.backup

# Fix the Dockerfile with correct paths
sudo tee Dockerfile > /dev/null <<'EOF'
FROM httpd:2.4.43

RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf

RUN sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' /usr/local/apache2/conf/httpd.conf

RUN sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' /usr/local/apache2/conf/httpd.conf

RUN sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' /usr/local/apache2/conf/httpd.conf

COPY certs/server.crt /usr/local/apache2/conf/server.crt

COPY certs/server.key /usr/local/apache2/conf/server.key

COPY html/index.html /usr/local/apache2/htdocs/
EOF


Step 4: Verify the Fixed Dockerfile
cat -n Dockerfile
The output should show:

     1  FROM httpd:2.4.43
     2  
     3  RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf
     4  
     5  RUN sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' /usr/local/apache2/conf/httpd.conf
     6  
     7  RUN sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' /usr/local/apache2/conf/httpd.conf
     8  
     9  RUN sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' /usr/local/apache2/conf/httpd.conf
    10  
    11  COPY certs/server.crt /usr/local/apache2/conf/server.crt
    12  
    13  COPY certs/server.key /usr/local/apache2/conf/server.key
    14  
    15  COPY html/index.html /usr/local/apache2/htdocs/



Step 5: Verify Required Files Exist
# Check if the required files exist
ls -la certs/
ls -la html/



Step 6: Build the Image
sudo docker build -t httpd-custom .

Step 7:
You can verify it works by running:
sudo docker run -d --name test-httpd -p 8080:8080 httpd-custom
sudo docker ps
curl http://localhost:8080




Key Changes Made:
I only fixed the incorrect file paths in the sed commands:

Line 3: Changed conf.d/httpd.conf to /usr/local/apache2/conf/httpd.conf

Line 5: Changed conf.d/httpd.conf to /usr/local/apache2/conf/httpd.conf

Line 7: Changed conf.d/httpd.conf to /usr/local/apache2/conf/httpd.conf

Line 9: Changed conf.d/httpd.conf to /usr/local/apache2/conf/httpd.conf

What I preserved:

Base image: httpd:2.4.43

All configuration changes (Listen port, SSL module loading, etc.)

All COPY commands and their destinations

The data files (index.html, server.crt, server.key)

The Dockerfile should now build successfully since the sed commands are pointing to the correct httpd.conf file location.

