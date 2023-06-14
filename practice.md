###  15/Apr/2023 (Evening)

(.) dot is current directory

* Docker Volume contd..

* Persisting Data using Volumes

   ` docker volume create mysqldb `

     * Lets create an explicit volume for mysqldb

       * Lets use volume type to mount the mysqldb

     * Create a mysql container

     * open this link for official doc..  https://docs.docker.com/storage/volumes/#start-a-container-with-a-volume

     ` docker contauiner run -d --name mysqldb -v mysqldb:/var/lib/mysql -P mysql `
     ![preview](images/today1.png)
     ` docker container ls `
     ![preview](images/today2.png)     

    * docker container is not running

      ` docker container rm -f $(docker container ls -a -q) `  ---deleted
     
       ` docker container run -d --name mysqldb -v mysqldb:/var/lib/mysql -P mysql:5.6 ` 

      ![preview](images/today3.png) 
      * container not running 

      ` docker container ls ` --- here alo container not running
      ![preview](images/today5.png)
       
       ` docker container logs mysqldb `
       ![preview](images/today6.png)
       
       ` docker container rm -f $(docker container ls -a -q) `
    
      ` docker container run -d --name mysqldb -v mysqldb:/var/lib/mysql -P -e MYSQL_ROOT_PASSWORD=rootroot -e MYSQL_DATABASE=employees -e MYSQL_USER=qtdevops -e MYSQL_PASSWORD=rootroot mysql `

      ![preview](images/today7.png)

      ` docker container ls `
      ![preview](images/today8.png)
      
     ` docker container exec -it mysqldb mysql --password=rootroot `
     ![preview](images/today9.png)

     ` use employees; `
      it will show database changed
      
        ` CREATE TABLE Persons (
              PersonID int,
              LastName varchar(255),
              FirstName varchar(255),
              Address varchar(255),
              City varchar(255)
          ); `

          ` insert into Persons Values ( 1,'test','test','test','test' ); `
          ` Select * from Persons; `
     ![preview](images/today10.png)
        
         exit 

         ` docker container rm -f mysqldb `
         ` docker container run -d --name mysqldb --mount "source=mysqldb,target=/var/lib/mysql,type=volume" -P -e MYSQL_ROOT_PASSWORD=rootroot -e MYSQL_DATABASE=employees -e MYSQL_USER=qtdevops -e MYSQL_PASSWORD=rootroot mysql `

         creating mount container creating 10 touch files

         Lets use bindmount to mount /tmp on docker host to the container /tmp
         ` docker container run -d --name exp1 -v /tmp:/tmp ubuntu:22.04 sleep 1d `
         ` docker container run -d --name exp1 --mount "source=/tmp,target=/tmp,type=bind" ubuntu:22.04 sleep 1d `

          ` docker container run -d --name exp1 -v /tmp:/tmo ubuntu:22.04 sleep 1d `
    ![preview](images/today11.png)

         ` docker container exec -it exp1 /bin/bash
          ` df -h `
    ![preview](images/today12.png)
          
          ` ls /tmp `
          ` touch /tmp/{1..10}.txt `
          ` ls /tmp `
    ![preview](images/today13.png)
          
          we created 10 mounted touch files


          bind mode... docker host ki  container ki same folder undali ankunnappudu bind mode use cheyali... 
          all the data stored in RAM only once system is restart all the data is gone...
          RAM is a valitality storage..

    ![preview](images/today14.png)
    ![preview](images/today15.png)

      Creating volume as part of Dockerfile
      Gameoflife:

      refer here for the changeset with volume instruction for gameoflife container
      https://github.com/asquarezone/DockerZone/commit/f58812733781b7ebed7b2a8d0b0584fe0338c4e6


         Dockerfile for NOPCommers....

         mkdir gol
        ` cd gol `
        vi Dockerfile 

         FROM tomcat:9-jdk8
         LABEL author="raju" organization="qt"
         ARG GOL_URL=https://referenceapplicationskhaja.s3.us-west-2.amazonaws.com/gameoflife.war
         ADD ${GOL_URL} /user/local/tomcat/webapps/gameoflife.war
         VOLUME "/urs/local/tomcat"
         EXPOSE 8080
         # INGORING CMD as i want base image's CMD

       past the abow dockerfile in vi Docker editor...:wq--enter

       ` docker container rm -f $(docker container ls -a -q) `
       ` docker volume prune `
       ` docker volume ls `
       ` docker image build -t raju:1.0 .`
    ![preview](images/today16.png)
       
       ` docker volume ls `
       ` docker container run -d --name raju1 -P raju:1.0 `
    ![preview](images/today19.png)
       ` docker container ls `
    ![preview](images/today17.png)
       ` docker volume ls `
    ![preview](images/today18.png)
        
         when ever we start a container volume get created
         as wel as mysql aslo automatically creating volumes
        
        ` docker volume inspect 742043d71b501376d943b2e65214205693b08c1440c7e24d32d5be2caaf990c4 `
        volume creating data storing inthis location
    ![preview](images/today20.png)
     
        ` docker container rm -f $(dockeer container ls -a -q) `
        ` docker volume prune `
        ` docker image rm -f $(docker image ls -a -q) `


    Shell file to clean everything
     
     Create a shell file with following content

      ` #!/bin/bash
        docker container rm -f $(docker container ls -a -q)
        docker volume prune
        docker image rm -f $(docker image ls -q) `


     Entrypoint and CMD

       Lets create two docker images

       1 first

       FROM alpine
       CMD [ "sleep","1d" ]

      `  mkdir entrypoint1 `

      ` cd entrypoint1 `
      ` vi first `
      ` docker image build -t g1 -f c1 . `
      ` docker container run g1 ping -c 4 google.com `
    ![preview](images/today21.png)   
      
      2 Second

      FROM alpine
      ENTYPOINT [ "sleep" ]
      CMD [ "1d" ]
     
     ` mkdir second `
     ` cd second2 `
     ` vi second2 `
     ` docker image build -t second2 -f c2 . `
    ![preview](images/today22.png)
      
     ` docker container run second2 ping -c 4 google.com ` 
        
           it wil show error because of " ping -c 4 " becomes to argumets to sleep doesnt know what ping is...
    ![preview](images/today23.png)

     ` docker container run second2 10s `
       
       it will run 10s seconds after that its stoping...
    ![preview](images/today24.png)

      container not allowing entrypoint if you want pass the entrypoint you have to pass argumet..

      ` docker container run --entrypoint ping second2 -c 4 google.com
    ![preview](images/today25.png)

      image name tharvatha manam ami rashina adhi CND ni mathrame change chesthundhi entrypoint nu kadhu
      entrypoint is also we can change but we have to pass to argument...

      ` docker container ls -a `
    ![preview](images/today26.png)

      ` docker container rm -f $(docker container ls -a -q) `
      ` alias prunvol='docker volume prune' `
      ` alias delimages='docker image rm -f $(docker image ls -a -q)' `
      ` prunvol `
      ` delimages `
   ![preview](images/today27.png)

      Now we understand why sleep container 1day        
      
      ` docker container run -d --name c20 alpine sleep 1d `
      ` docker container run -d --name c21 alpine sleep 1d `
    ![preview](images/today28.png)

      ` docker container inspect c20 `
    ![preview](images/today29.png)

      ` docker container inspect c21 `
    ![preview](images/today30.png)

      ` docker container exec c20 ping -c 1 172.17.0.4 `
      
      Now login into C1 and ping C2 by using its ipaddress
      Observation Results
         ping by name is not working
         ping by ip is working
    ![preview](images/today31.png)
      
       c20 container is speak over the c21 container...

    


    Take a new VM

      ` sudo apt update `
      ` sudo apt install net-tools -y `
    ![preview](images/today33.png)

      ` ifconfig `
      network inter faces...
    ![preview](images/today32.png)

       Now install docker and check network interfaces again...
    
     ` curl -fsSL https://test.docker.com -o test-docker.sh `
      `  sh test-docker.sh `
    ![preview](images/today34.png)
       
       A docker0 network interface is added.

      ` docker container run -it ubuntu:22.04 /bin/bash `

      ` apt update `
      ` apt install net-tools -y `
      ` ifconfig `
    ![preview](images/today34.png) 
        its totally run in inside the containers...
      

      ` ping directdevops.blogs `
    ![preview](images/today36.png)

        we knowing ip address ....

     for name servers ip...
     ` nslookup directdevops.blog `
    ![preview](images/today37.png)
     
     Docker Networking Architecture

     Docker Networking Architecture is based on Container Networking Model.

    ![preview](images/today38.png)

     CNM Constructs – Sandbox

        Contains the configuration of the containers network stack.
        Includes routing tables, container’s interfaces, DNS setting etc.
        Sample implementation could be Linux Network Namespace or any other similar concept.

    CNM Constructs – Endpoint
        Joins sandbox to the Network.
        This abstracts actual connection to the network away from application.
        Maintains portability for applications, so that they can use different network drivers  
    CNM Constructs – Network
        Collection of endpoints having connectivity b/w themselves

    CNM Driver Interfaces
        CNM provides two pluggable & open interfaces to leverage additional functionality and control in the network
           Network Drivers
           IPAM Drivers
    ![preview](images/today39.png)

      ` docker network ls `
    ![preview](images/today39.png)

    Docker Native Network Drivers

    for docker network artical link...https://directdevops.blog/2019/10/05/docker-networking-series-i/

        Host
          Container uses the networking stack of the host.
        Bridge
          Creates a bridge on the host that is managed by Docker.
          All containers on Bridge Driver can communicate among themselves.
          Default Driver.
        Overlay
          Used for multi-host networks.
          uses local linux bridges & VXLAN to overlay container-to-container networking.
        MACVLAN
          uses Linux MACVLAN bridge to establish connection b/w container interfaces & parent host interfaces.
          MAC address can be attached to each container.

       ` docker container run -d --name c1 alpine 1d `
       ` docker container inspect c1 `
       c1  is a bridge network...
    ![preview](images/today41.png)

       ` docker network inspect bridge `
    ![preview](images/today42.png)

     
      ` docker network create -d bridge --subnet "10.0.0.0/24" my_bridge `
          
          -d  means driver  
    ![preview](images/today43.png)

        ` docker network ls `
    ![preview](images/today44.png)

     ` docker container run -d --name c2 --network my_bridgealpine sleep 1d `
     ` docker container run -d --name c3 --network my_bridgealpine sleep 1d `
    ![preview](images/today45.png)

     ` docker container exec c2 ping -c 2 c3 `
    ![preview](images/today46.png) 

      ` docker container exec c2 ping -c 2 10.0.0.3 `
    ![preview](images/today47.png)  

    
    Lets create a mysql container in my_bridge network

      ` docker container run -d --name mysqldb -v mysqldb:/var/lib/mysql -P -e MYSQL_ROOT_PASSWORD=rootroot -e MYSQL_DATABASE=employees -e MYSQL_USER=qtdevops -e MYSQL_PASSWORD=rootroot --network my_bridge mysql `
    ![preview](images/today48.png)

       ` docker volume ls `
    ![preview](images/today49.png)

       Lets run phpmyadmin
    
     ` docker container run --name phpmyadmin --network my_bridge -d -e PMA_HOST=mysqldb -P phpmyadmin `
    ![preview](images/today50.png)

     ` docker container ls `
    ![preview](images/today51.png)
    ![preview](images/today52.png)


       Connect container C1 to my_bridge network

     ` docker network connect my_bridge c1 `

     ` docker exec c2 ping c3 `

       its a my bridge network thats why ip is 10.0.0.2 , 3....

      use controle  c  other wise it will contune...
    ![preview](images/today53.png)

       now we connect only bridge network  

      ` docker container exec -it c2 /bin/sh `
    ![preview](images/today54.png)

    we want to dissconnect the bridge network 
      ` docker network dissconnect bridge c2 `
      ` docker container exec c2 ip addr `
    ![preview](images/today55.png)

      Connect container C1 to my_bridge network

     ` docker container exec C1 ip addr `
     ` docker network connect my_bridge C1 `
     ` docker container exec C1 ip addr `
     ` docker network disconnect bridge C1 `
     ` docker container exec C1 ip addr ` 
     
      



    






       




