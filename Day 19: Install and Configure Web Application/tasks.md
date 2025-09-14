xFusionCorp Industries is planning to host two static websites on their infra in Stratos Datacenter. The development of these websites is still in-progress, but we want to get the servers ready. Please perform the following steps to accomplish the task:



a. Install httpd package and dependencies on app server 2.


b. Apache should serve on port 3004.


c. There are two website's backups /home/thor/ecommerce and /home/thor/apps on jump_host. Set them up on Apache in a way that ecommerce should work on the link http://localhost:3004/ecommerce/ and apps should work on link http://localhost:3004/apps/ on the mentioned app server.


d. Once configured you should be able to access the website using curl command on the respective app server, i.e curl http://localhost:3004/ecommerce/ and curl http://localhost:3004/apps/