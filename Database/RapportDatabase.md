# Answers Database

## Question 1
### Build the database application
In the DevOps/TP/Database, run the command `docker build -t bladerunner0/tp_database .` This will build the 
application.

### Run the database
To see the database, first run Adminer with command : 
`docker run -p "8090:8080" --net=app-network --name=adminer -d adminer`

When Adminer is launched, run the database application :
`docker run --network app-network -p 8080:8080 --name tp_database bladerunner0/tp_database`
It is possible to add `-d`so we don't see all the logs of the run command.
Once the database is launched, go to `localhost:8090` to see the database.

### Access Adminer
On the login screen of Adminer :
- Système : PostgreSQL
- Serveur : tp_database
- Utilisateur : usr
- Mot de passe : pwd
- Base de données : db

If the authentification is successful, you should see :
`Insert image`

### Get SQL files to run when running the application
Write and put your SQL files at the same level of your Dockerfile.
In the Docker file, add `COPY <YOUR_FILE>.sql /docker-entrypoint-initdb.d`
Be careful, the SQL files will be called in alphabetical order.
