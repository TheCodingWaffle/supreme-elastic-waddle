
# ElasticSearch

The following is for installing the following technologies:

[ElasticSearch](https://www.elastic.co/)

[Kibana](https://www.elastic.co/kibana)

[LOGSTASH](https://www.elastic.co/logstash) (configured to work the SQL Server)



The majority of this is using Docker to spin up the images and has been configured in the
repos docker-compose.yml file.

### How do I get set up? ###

 1.  Copy all files in /elasticsearch folder into the host (same name folder or diff doesn't matter)
 2.  All the following commands ran while in above folder (ie you cd ~/elasticsearch)
 3. Generate the certificates (only needed once):
`docker-compose -f create-certs.yml run --rm create_certs`
 4. Start docker-compose
	- to run this in the background:
        `docker-compose up -d`
    
    - to run and see logs but will kill it when you exit (ie you have to keep it up to keep it running)
    `docker-compose up`

 5. Access the Elasticsearch API over SSL/TLS using the bootstrapped password:

    `docker run --rm -v es_certs:/certs --network=es_default docker.elastic.co/elasticsearch/elasticsearch:7.12.0 curl --cacert /certs/ca/ca.crt -u elastic:CHANGEME https://es01:9200`

 6. Generate new passwords
 `docker exec es01 /bin/bash -c "bin/elasticsearch-setup-passwords \ auto --batch \ --url https://localhost:9200"`

 7. Save all generated passwords in LastPass (or a sticky note ¯\\__(ツ)_/¯ )
 
 8. The above will change ALL passwords. So when you bring the service down and up the docker compose one will no long be valid. Change / Update the .env -> `KIBANA_PASSWORD`, `ELASTIC_PASSWORD`, & `LOGSTASH_PASSWORD` values with their generated counterparts.

 9. Make sure all processes are running and then navigate to [Kibana](https://your-url:5601/). Use your elastic user generated credentials (this may take a couple of minute to be accessable upon service start)

 10. In Kibana navigate to Management -> Dev tools. Create/Add the logstash_writer roll + user with the commands found in management.commands. (hint: use the password generated from the above code)

 11. Update the logstacs.conf password with logstash_writer user you just set

 12.  Stop and restart the docker containers

    docker-compose stop
    
    docker-compose up -d

 13. Depending on your logstacs.conf settings the next itteration your data wills start to stream to ElasticSearch
 

### OH SH!T IT BROKE ###

Sometimes you're going to need some help after it done broke. This section will help you with that.

* map / memory errors when elasticsearch starts just run :

    sysctl -w vm.max_map_count=262144

* Need to just start fresh
	* 	Tear docker down:
		* `docker-compose down -v`
		* < or >
		* `docker kill <process name>`
	* Delete all Docker images:
		* `docker images`
		* `docker rmi -f <image name>`
	* Remove Docker containers
		* `docker container prune`

* Other:
	* Docker logs?
		* `docker-compose logs --follow`
	* SSH into Docker Container
		* `docker exec -it <container name> /bin/bash`

### Helpful Links ###

* [3 node ElasticSearch](https://www.elastic.co/guide/en/elastic-stack-get-started/current/get-started-docker.html)
* [2 node ElasticSearch](https://www.elastic.co/guide/en/elasticsearch/reference/current/configuring-tls-docker.html)
* [Elasticsearch usernames](https://www.elastic.co/guide/en/elasticsearch/reference/current/built-in-users.html)
