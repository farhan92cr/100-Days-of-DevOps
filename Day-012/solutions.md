# Day 12 - Complete Solutions

## Task 1 Solution: Docker Installation
```bash
# Install Docker on stapp01
ssh tony@stapp01 "sudo yum install docker -y"
ssh tony@stapp01 "sudo systemctl enable docker --now"
ssh tony@stapp01 "sudo usermod -aG docker tony"

Task 2 Solution: Container Deployment:
# Deploy nginx container
ssh tony@stapp01 "sudo docker run -d --name webapp -p 8080:80 nginx:alpine"
ssh tony@stapp01 "sudo docker ps"

Task 3 Solution: Container Management
# Management scripts
ssh tony@stapp01 "sudo tee /scripts/container_mgmt.sh << 'EOF'
#!/bin/bash
case \$1 in
    start) sudo docker start webapp ;;
    stop) sudo docker stop webapp ;;
    restart) sudo docker restart webapp ;;
    status) sudo docker ps -f name=webapp ;;
    *) echo \"Usage: \$0 {start|stop|restart|status}\" ;;
esac
EOF"

