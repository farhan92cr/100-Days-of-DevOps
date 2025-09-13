Day 17 - Step-by-Step Solution:

Step 1: Connect to Database Server (stdb01)
bash
ssh peter@stdb01
# Password: Sp!dy
Step 2: Access PostgreSQL as Postgres User
bash
sudo -u postgres psql
Step 3: Create Database User with Password
sql
CREATE USER kodekloud_gem WITH PASSWORD 'GyQkFRVNr3';
Step 4: Create Database
sql
CREATE DATABASE kodekloud_db7;
Step 5: Grant Full Permissions to User on Database
sql
GRANT ALL PRIVILEGES ON DATABASE kodekloud_db7 TO kodekloud_gem;
Step 6: Verify the Configuration
sql
-- List all users
\du

-- List all databases
\l

-- Check privileges for the database
\c kodekloud_db7
\dp
Step 7: Exit PostgreSQL
sql
\q
Step 8: Test the Connection (Optional)
bash
# Test if user can connect to the database
sudo -u postgres psql -U kodekloud_gem -d kodekloud_db7 -h localhost -W
# Enter password: GyQkFRVNr3
Complete PostgreSQL Commands:
sql
-- Inside psql as postgres user:
CREATE USER kodekloud_gem WITH PASSWORD 'GyQkFRVNr3';
CREATE DATABASE kodekloud_db7;
GRANT ALL PRIVILEGES ON DATABASE kodekloud_db7 TO kodekloud_gem;

-- Verification:
\du
\l
Verification Commands:
bash
# Verify user exists
sudo -u postgres psql -c "\du" | grep kodekloud_gem

# Verify database exists  
sudo -u postgres psql -c "\l" | grep kodekloud_db7

# Verify privileges
sudo -u postgres psql -d kodekloud_db7 -c "\dp"
Note: The PostgreSQL server is already installed and running as mentioned. We're just creating the user and database without restarting the service, as required.