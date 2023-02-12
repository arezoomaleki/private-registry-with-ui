# private-registry-with-ui
Create your own private registry and access is via UI
<h2> Step 1 </h2><br>
<h3> Install Docker and Docker Compose </h3>
<br>
You can use this link if your sever is ubntu 20.04 as mine is: <br>
<link>https://support.netfoundry.io/hc/en-us/articles/360057865692-Installing-Docker-and-docker-compose-for-Ubuntu-20-04</link>
<br>
<h2> Step 2 </h2><br>
<h3> Create "registry:2 container </h3>
<br>
<code>  mkdir docker_registry</code><br>
<code>  cd docker_registry</code><br><br>
Create and edit "docker-compose.yml" file <br><br>
<code>  nano docker-compose.yml</code><br>
<br><br>
<b>Please change the username and password before run the containers.</b><br>
<code>cat << EOF > docker-compose.yml
version: '3'
services:
    docker-registry:
        image: registry:2
        volumes:
        - "/tmp/docker_registry:/var/lib/registry"
        ports:
        - "5000:5000"
        restart: always
    docker-registry-ui:
        image: parabuzzle/craneoperator:latest
        ports:
        - "8086:80"
        environment:
        - REGISTRY_HOST=docker-registry
        - REGISTRY_PORT=5000
        - REGISTRY_PROTOCOL=http
        - SSL_VERIFY=false
        - USERNAME=admin
        - PASSWORD=mypassword
        restart: always
        depends_on:
        - docker-registry
EOF</code><br><br>
Build and run the containers using below command: <br>
<code>  docker-compose up -d</code>
<h2> Step 3</h2><br>
<h3> Test registry </h3>
<br>
Pull an image you wan to push: (do this step on your registry server) <br>
<code>  docker pull ubuntu:latest</code><br>
<code>  docker tag ubuntu:latest 127.0.0.1:5000/ubuntu:latest</code><br>
<code>  docker push 127.0.0.1:5000/ubuntu:latest</code><br>
<br>
<h2> Step 4</h2><br>
<h3> Test registry from remote server (gitlab runner in my case) </h3><br>
<br>
<code>  docker login public-or-private-IP-of-Registry-Server:5000</code><br><br>
Enter the "username" and "Password" you provided in Step 2. <br><br>
<code>  docker pull 127.0.0.1:5000/ubuntu:latest</code><br><br>
Also you can set this configuration at "Gitlab CICD" yml file.<br><br>

<b>P.S: If you face the error "http: server gave HTTP response to HTTPS client Error" when youwant to execute "Docker login" command plese do this on remote:</b><br>
<code>  vi /usr/lib/systemd/system/docker.service </code><br>
<code>  ExecStart=/usr/bin/dockerd --insecure-registry public-or-private-IP-of-Registry-Server:5000</code><br>
<code>  systemctl daemon-reload</code><br>
<code>  service docker restart</code><br>
<br>
<b>You can login the registry UI using http://public-or-private-IP-of-Registry-Server:8086/</b>

