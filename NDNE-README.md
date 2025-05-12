# Running after cloning the repo

1. `.\nodebb.bat setup`
2. delete the file: `.docker\database\postgresql\data\.gitkeep`
3. `docker compose -f docker-compose-pgsql-only.yml up -d`
4. open `http://localhost:4567/`
5. postgresql username/password/database: nodebb/nodebb/nodebb
6. Install NodeBB
7. Login with the username/password you created

# Debugging in VSCode

Finsish the aboive steps and F5

1. `.\nodebb.bat stop`
2. press F5
3. open `http://localhost:4567/` 

# Setting up Azure

- Create a resource group
- Create a PostgresQL Flexible Server
    - save your admin login
    - allow azure connections and public connections, add your ip to firewall
    - create the nodebb database
        - use the azure CLI
        - connect using the psql command (look for it under the PostgreSQL resource under connecting or connections)
            - CREATE DATABASE nodebb;
            <!-- - CREATE ROLE nodebb WITH LOGIN PASSWORD 'yourpassword';
            - GRANT ALL PRIVILEGES ON DATABASE nodebb TO nodebb;
            - GRANT USAGE ON SCHEMA public TO nodebb;
            - GRANT CREATE ON SCHEMA public TO nodebb;
            - GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO nodebb;
            - GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA public TO nodebb;
            - ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON TABLES TO nodebb;
            - ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON SEQUENCES TO nodebb; -->
- Create a Web App
    - Node 20 LTS
    - Set Environment variables under Settings -> Environment variables
        - NODE_ENV=production
    - Set Environment variables under under the  Settings -> Environments -> EnvironmentsSectrets in your github repo
        NODEBB_ADMIN_PASSWORD
        NODEBB_ADMIN_EMAIL
        NODEBB_DB_PASSWORD
    - Set startup command under Settings -> Configuration
        - ./nodebb build && ./nodebb start
    - Setup CI/CD in Deployment -> Deployment center
        - point to github or wherever branch
        - add a workflow


- CI/CD firewalls
    - The Github runner has to be able to access the posgresql server. you can go to Settings -> Networking to add firewall rules.
    - You can either try to finmd the IP for the runner or temporarily add a rule for 0.0.0.0 - 255.255.255.255