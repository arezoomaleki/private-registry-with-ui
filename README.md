# private-registry-with-ui
Create your own private registry and access is via UI
<h2> Step 1 </h2><br>
<h3> Install Docker and Docker Compose </h3>
<br>
<br>
<h2> Step 2 </h2><br>
<h3> Create "registry:2 container </h3>
<br>
<code>  mkdir docker_registry</code><br>
<code>  cd docker_registry</code><br>
<code>  nano docker-compose.yml</code><br>
<br>
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
EOF</code><br>
<code>  docker-compose up -d</code>
<h2> Step 3</h2><br>
<h3> Test registry </h3>
<br>

