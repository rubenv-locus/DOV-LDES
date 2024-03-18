This example is used to set up a simple LDIO workbench and LDES server for the [Boorgat](https://implementatie.data.vlaanderen.be/doc/implementatiemodel/grondverzet/grondboringen/ontwerpstandaard/2023-10-01/#Boorgat) object.
This example uses docker, so be sure to have Docker Desktop installed!


To set up the LDES server and the LDIO workbench, run the following commands:


```bash
clear

# bring the systems up
docker compose up -d

# wait for the workbench
while ! docker logs $(docker ps -q -f "name=ldio-workbench-boorgat") 2> /dev/null | grep 'Started Application in' ; do sleep 1; done

# wait for the server
while ! docker logs $(docker ps -q -f "name=ldes-server-boorgat$") 2> /dev/null | grep 'Started Application in' ; do sleep 1; done

# define the LDES
curl -X POST -H "content-type: text/turtle" "http://localhost:9003/ldes/admin/api/v1/eventstreams" -d "@./definitions/boorgat.ttl"

# define the view
curl -X POST -H "content-type: text/turtle" "http://localhost:9003/ldes/admin/api/v1/eventstreams/boorgat/views" -d "@./definitions/boorgat.by-page.ttl"

```

Now you can send json-ld files of Boorgaten to the LDIO-workbench, which will process and then automatically send it to the LDES-server using the following command:


```bash
# send the message
curl -X POST -H "Content-Type: application/ld+json" "http://localhost:9004/boorgat" -d "@./data/message.jsonld"
```


You can now see the object in the local LDES server using the following command: 


```bash
curl "http://localhost:9003/ldes/boorgat/by-page?pageNumber=1"
```


When you are done viewing this example, you can bring the containers down and remove the private network:
```bash
docker compose down
```
