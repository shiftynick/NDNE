# Running after cloning the repo

1. `.\nodebb.bat start`
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
            - CREATE ROLE nodebb WITH LOGIN PASSWORD 'yourpassword';
            - GRANT ALL PRIVILEGES ON DATABASE nodebb TO nodebb;
- Create a Web App
    - Node 20 LTS
    - Set Environment variables under Settings -> Environment variables
        - DATABASE=postgres
        - POSTGRES__HOST=yourservername.postgres.database.azure.com
        - POSTGRES__PORT=5432
        - POSTGRES__DATABASE=nodebb
        - POSTGRES__USERNAME=nodebb
        - POSTGRES__PASSWORD=yourpassword
        - URL=https://ndne.space
        - SECRET=a_guid
        - ADMIN__USERNAME=admin
        - ADMIN__EMAIL=an_email
        - ADMIN__PASSWORD=a_password
        - NODE_ENV=production
    - Set startup command under Settings -> Configuration
        - ./nodebb build && ./nodebb start
    - Setup CI/CD in Deployment -> Deployment center
        - point to github or wherever branch
        - add a workflow

