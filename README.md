# How to setup telegraf, influxdb and grafana using docker-compose.
In order to setup the TIG monitoring stack using [ docker compose](https://docs.docker.com/compose/install/ "docker-compose") , first of all, you have to make sure that you have the latest version installed on your docker machine. Then you can then begin to write you [docker compose file](https://github.com/Osaschrist/TIG-stack/blob/main/docker-compose-arm.yml "docker compose file for arm architecture") which will contain the tree tools we are going to use.

**NOTE:** When selecting an image for your container you need to take note of the architecture because in some cases, you will have to indicate it using the the following:
 `platform: linux/amd64`

## Telegraf configuration file (telegraf.conf)
You can define your telegraf initial configuration outside of you telegraf container and in order to do so, you have to add the following line in your docker compose file:
` volumes:`</br>
      `- ./telegraf.conf:/etc/telegraf/telegraf.conf:ro` <br>
## .ENV file
A .env file or dotenv file is a simple text configuration file for controlling your Applications environment constants. Between Local, Staging and Production environments, the majority of your Application will not change. However in many Applications there are instances in which some configuration will need to be altered between environments. Common configuration changes between environments may include, but not limited to, URL's and API keys. Here you can find the example of how it was applied.

```bash
$cat .env
#Influxdb Variables
MODE=setup
USERNAME=admin
PASSWORD=P@ssw0rd
```
```bash
$cat docker-compose.yml
 environment:
      - DOCKER_INFLUXDB_INIT_MODE=${MODE}
      - DOCKER_INFLUXDB_INIT_USERNAME=${USERNAME}
      - DOCKER_INFLUXDB_INIT_PASSWORD=${PASSWORD}
```
After making all the neccessary configurations, you can then spin up your docker compose file with the `docker-compose up - d` command.
 Or with this command ` docker-compose --env-file .env -f docker-compose-arm.yml up -d` <br/>

 Where `-f ` is used to indicate the docker compose file to spin up and `--env-file` to indicate you .env file. But it only useful if you change the standard name of both file.

 ### **It is a good practice to use the this `docker-compose --env-file .env -f docker-compose-arm.yml config` command to check if you `.env` file configuration is correct**
 
## How to get Influxdb working

Once you have install influxdb you will have to edit the telegraf.conf archive to change the ip and the token.
To change both of them you have to go to edit the telegraf.conf archive and where puts "hostname" and "urls" you put
your actual ip, be carefull, in the urls part you only have to change the ip don't touch the other parts of the url.

To continue you have to change the token, to achive this you will have to search "yourip:8086" on the internet browser and
enter the credentials. Here you have to go to the option called "Data" and then to "API Token" there you will find your
token wich you only have to copy and paste in the telegraf.conf archive in the part that is called "token". With this
configuration we have allowed influxdb to take metrics from our machine.

Finally to start taking metrics with influxdb you will have to enter to the telegrafs container and insert a comand,
to do this you will have to put "sudo docker exec -it telegraf /bin/bash" to enter to the container and once here you
have to put "/usr/bin/telegraf -config /etc/telegraf/telegraf.conf -config-directory /etc/telegraf/telegraf.d -debug"
to start taking metrics from the machine


## Conect telegraf with grafana

To conect telegraf with grafana you should go to telegrafs graphic interface, you have to get in a option called "Explore
", once you are in you have to select from where do you want to take this metrics and what metrics do you want to see
in the graphic, when you finish selecting all the things you have to clic on the button called "script editor" this will
show you a script which you have to copy. Now with the script copyed you have to go to grafanas graphic interface and
enter in to the option called "Create", inside this option you have to clic the button called "add panel" to create a
new dashboard, once you create the dashboard and you are inside you only have to paste the code in the dashboard.

