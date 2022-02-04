Docker:
* List all the images:
  > ```docker images```
* List all the containers:
  > ```docker ps```
* List all the active containers:
  > ```docker ps -a```

* Build docker image:
  * docker build <image_name> .
  > ```docker build --tag=expense-manager-api:latest .```
* Run docker image: Nothing but spinning up a container
  * Note: first port 9090 refers to client port
  > ```docker run -p9090:8080 expense-manager-api:latest```
̊
* Start docker-compose:
  * Note: If file name is docker-compose.yml
  > ```docker-compose up```
  * Note: If file name is other than "docker-compose.yml" then specify the file name say for example "mysql.yml"
  > ```docker-compose -f mysql.yml up```

* Stop docker-compose:
  * Note: If docker file name is "docker-compose.yml"
  > ``` docker-compose down ```
  * Note: If file name is other than "docker-compose.yml" say "mysql.yml"
  > ```docker-compose -f mysql.yml down```

* Delete a container:
    > ``` docker rm <containerid>```

* Delete image:
    > ``` docker rmi <imageid> ```

* Remove all stopped containers and all unused images:
    > ``` docker system prune -a ```