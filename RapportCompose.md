# Answers Compose

## Question 3

The most important commands of a docker-compose are the followings :

- docker-compose up: This command creates and starts all the services defined in your docker-compose.yml file.

- docker-compose down: This command stops and removes the containers, networks, and volumes defined in your docker-compose.yml file.

- docker-compose build: This command builds or rebuilds the services defined in your docker-compose.yml file.

- docker-compose start: This command starts the services defined in your docker-compose.yml file.

- docker-compose stop: This command stops the services defined in your docker-compose.yml file without removing them.

- docker-compose restart: This command restarts the services defined in your docker-compose.yml file.

- docker-compose pause: This command pauses all the services defined in your docker-compose.yml file.

- docker-compose unpause: This command unpauses all the services that were paused using the docker-compose pause command.

- docker-compose ps: This command lists all the running containers, along with their status and other information.

- docker-compose logs: This command displays the logs for the services defined in your docker-compose.yml file.



### Problem with starting docker compose
In the docker-compose.yml file, the container names are forced. If you notice in console when doing `docker compose up` that some of them are still generated with the forced name as
a base, stop and delete all containers created then do `docker-compose up --force-recreate --build`. This command will force Docker to delete all images and rebuild everything from 
scratch before starting the containers 


## Question 5
When laucnhing docker compose, 3 images are created. To publish on Doker hub, it is necessary to tag and push each one of them:
`docker tag <IMAGE_NAME> USERNAME/<IMAGE_NAME>:1.0`
`docker push USERNAME/<IMAGE_NAME>:1.0`

You can then see on the docker hub site your repositories 
