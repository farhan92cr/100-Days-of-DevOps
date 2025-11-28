Day 20 - Task Context & Explanation:

🎯 What is the Task About?
We're setting up a PHP-based web application using Nginx + PHP-FPM stack on App Server 1. This is a common modern web hosting setup where:

Nginx handles static content and reverse proxies PHP requests

PHP-FPM (FastCGI Process Manager) processes PHP scripts efficiently

🔧 Technical Requirements:
Web Server: Nginx configured on non-standard port 8091

PHP Processor: PHP-FPM 8.3 using Unix socket communication

Integration: Nginx and PHP-FPM working together seamlessly

Document Root: /var/www/html (where PHP files are placed)

🌐 How It Works:
text
User Request → Nginx (Port 8091) → PHP-FPM (Unix Socket) → PHP Processing → Response to User
📊 What We Achieve:
High Performance: PHP-FPM is faster than traditional mod_php

Better Security: Unix socket communication is more secure than TCP

Resource Efficiency: Separate processes for web server and PHP processing

Modern Stack: Industry-standard PHP deployment architecture

🚀 End Result:
After completion, we'll have:

A functioning PHP application server

Ability to serve both static and dynamic content

Properly configured security and permissions

Testable via curl http://stapp01:8091/index.php

💡 Why This Setup?
Scalability: Can handle more traffic than Apache + mod_php

Flexibility: Easy to configure different PHP versions

Performance: Optimized for PHP applications

Reliability: Stable production-ready environment

This setup is what powers many modern PHP applications including WordPress, Laravel, and other PHP-based frameworks!

