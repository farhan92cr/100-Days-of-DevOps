🌐 Simple Explanation: Nginx + PHP-FPM

🏗️ How Web Servers Work:
text
User → Web Server → PHP Processor → Response
🔄 Two Ways to Connect:
Old Way (Slow):
Nginx talks to PHP using files (like passing notes)

Like: Postal mail - slow and inefficient

New Way (Fast - Unix Socket):
Nginx talks to PHP using a direct pipe

Like: Phone call - instant communication

/var/run/php-fpm/default.sock = the phone line

🧩 What Each Does:
Nginx: Serves web pages (the receptionist)

PHP-FPM: Processes PHP code (the worker)

Unix Socket: Direct connection between them (internal phone)

⚡ Why Unix Socket?
Faster than network connections

More secure (local only)

Better performance for PHP apps

🔧 Simple Setup:
Install Nginx + PHP-FPM

Create socket file path

Tell PHP-FPM: "Use this socket"

Tell Nginx: "Send PHP files to this socket"

Start both services

That's it! The socket is just a fast communication channel between the web server and PHP processor. 🚀
