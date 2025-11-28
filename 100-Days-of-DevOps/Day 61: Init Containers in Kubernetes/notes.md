What are Init Containers?
Init Containers are special containers that run before the main application containers in a Pod. They are designed to perform setup tasks that need to be completed before your main application starts.

Key Characteristics:
1. Execution Order:
text
Init Container 1 → Init Container 2 → ... → Main Containers
Init containers run sequentially (one after another)

All must complete successfully before main containers start

If any init container fails, the Pod restarts (retries)

2. Common Use Cases:
Database migrations before app starts

Configuration setup (creating files, setting permissions)

Dependency checks (waiting for external services)

Secret/credential retrieval

Data pre-loading or initialization

3. In Our Lab Example:
Init Container (ic-msg-nautilus):

yaml
command: ['/bin/bash', '-c', 'echo Init Done - Welcome to xFusionCorp Industries > /ic/media']
Task: Creates a file with a welcome message

Runs: First, before main container

Main Container (ic-main-nautilus):

yaml
command: ['/bin/bash', '-c', 'while true; do cat /ic/media; sleep 5; done']
Task: Reads and displays the file continuously

Runs: Only after init container completes

Real-World Examples:
Example 1: Database Setup
yaml
initContainers:
- name: init-db
  image: postgres-client
  command: ['/bin/sh', '-c', 'until pg_isready -h database; do sleep 2; done; psql -h database -f /scripts/setup.sql']
Example 2: Config Download
yaml
initContainers:
- name: download-config
  image: curl
  command: ['sh', '-c', 'curl https://config-server/app-config > /app/config.yaml']
Example 3: Permission Fix
yaml
initContainers:
- name: fix-permissions
  image: busybox
  command: ['chmod', '-R', '755', '/app/data']
Benefits:
Separation of Concerns: Setup logic separate from application logic

Security: Can run with different permissions than main app

Reliability: Ensures dependencies are ready before app starts

Flexibility: Can use different images optimized for setup tasks

In Our Lab:
The init container prepares the environment (creates a file), and the main container uses that prepared environment. This demonstrates the "prepare then use" pattern that init containers enable.

Without Init Container: Main container would need complex startup logic
With Init Container: Clean separation - init prepares, main consumes

