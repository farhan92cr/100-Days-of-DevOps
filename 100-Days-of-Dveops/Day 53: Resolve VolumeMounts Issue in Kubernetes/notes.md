What is ConfigMap in Kubernetes?
A ConfigMap is a Kubernetes object that stores configuration data as key-value pairs. It separates configuration from container images, allowing you to change config without rebuilding images.

Nginx Configuration Structure:
1. Main Configuration File (nginx.conf)
Location: /etc/nginx/nginx.conf

Contains: Global nginx settings

Required blocks:

nginx
events {
    worker_connections 1024;
}

http {
    # HTTP-level settings
    include /etc/nginx/conf.d/*.conf;  # Includes server blocks
}
2. Server Configuration Files (conf.d/ directory)
Location: /etc/nginx/conf.d/*.conf

Contains: Individual server blocks only

Example:

nginx
server {
    listen 80;
    server_name example.com;
    # Website-specific settings
}
The Problem We Had:
❌ Wrong ConfigMap (What we had):
nginx
events { }          # ← WRONG: Only allowed in main nginx.conf
http {              # ← WRONG: Only allowed in main nginx.conf
  server {          # ← CORRECT: This belongs in conf.d/
    listen 8099;
    # server config
  }
}
✅ Correct ConfigMap (What we fixed to):
nginx
server {            # ← CORRECT: Only server block in conf.d/
  listen 8099;
  # server config
}
Purpose of Each Block:
events Block
Purpose: Configures how nginx handles connections

Where: Only in main nginx.conf

Example:

nginx
events {
    worker_connections 1024;  # Max simultaneous connections
    use epoll;               # Event processing method
}
http Block
Purpose: Global HTTP server settings

Where: Only in main nginx.conf

Example:

nginx
http {
    include mime.types;
    default_type application/octet-stream;
    sendfile on;
    keepalive_timeout 65;
    
    # Include server configurations
    include /etc/nginx/conf.d/*.conf;
}
server Block
Purpose: Defines individual websites/virtual hosts

Where: In conf.d/ directory files

Example:

nginx
server {
    listen 80;
    server_name myapp.com;
    root /var/www/html;
    # Website-specific rules
}
Why Our Fix Worked:
Before: ConfigMap tried to put events+http in server config file → Syntax Error

After: ConfigMap only has server block → Valid for conf.d/ directory

Key Rule: events and http blocks go in main config; server blocks go in separate files in conf.d/

