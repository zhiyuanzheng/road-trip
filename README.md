Instal Docker, preserve at least 6GB memory for Docker
Start Vespa Container: docker run -m 10G --detach --name vespa --hostname vespa-tutorial \
    --privileged --volume `pwd`:/app \
    --publish 8080:8080 --publish 19112:19112 vespaengine/vespa
Make sure that the configuration server is running by: docker exec vespa bash -c 'curl -s --head http://localhost:19071/ApplicationStatus'
Mkdir application: copy what is inside application folder to here
Deploy the package docker exec vespa bash -c '/opt/vespa/bin/vespa-deploy prepare /app/application && \
    /opt/vespa/bin/vespa-deploy activate'

See browser:  http://localhost:8080/ApplicationStatus

Feed three document into the server docker exec vespa bash -c 'java -jar /opt/vespa/lib/jars/vespa-http-client-jar-with-dependencies.jar \
    --verbose --file /app/<json_file> --host localhost --port 8080'




View in the query builder http://localhost:8080/querybuilder/






















Sorry my computer’s microphone is not working, so I will present my project in this manner.

My project is about building a road trip tool on the open-sourced retrieval system, called Vespa. Which is developed by Oath, former known as Yahoo. You can reach out to more detail in their website: vespa.ai. The data is collected from Kaggle dataset and simple ranking function is implemented inside Vespa. 

OK, so a nice thing about vespa is their integrity with Docker container, so we can develop everything on docker. First, let’s download docker and set memory of docker to at least 6GB.

Then we can start with a new Vespa container following by the following command:
Remember to run this command just inside the source folder. For instance, I will upload a zip folder named road_trip, so this operation should be performed just inside road_trip directory.

After we deploy the application, we can check its status by

Now you can see the container is running correctly when it says HTTP/1.0 200

Next, let’s deploy our source package in the application folder, let’s take a look what is inside.


So, in application/searchdefinitions/ folder, you will see three search definitions, these three files specify which parameter you need to use when you want to query the system.

Let’s go ahead to deploy it by 

After you successfully deploy, you should see the successful message

Next we need to feed the three json files inside the directory 

After feeding is successful, let’s use the query to play with.

Suppose I wanna to travel from San Francisco to Los Angeles, firstly, I wanna retrieve their latitude and longitude. 

Sorry, you should open up the query builder first

select * from sources uscities where city contains "San Francisco" and state_id contains "CA";


 "lat": 37.7561,
 "lng": -122.4429,

select * from sources uscities where city contains "Los Angeles" and state_id contains "CA";

"lat": 34.114,
"lng": -118.4068,

Next we can find cities between these two destinations, sorted by population

Select * from sources uscities where lat<37.7 and lat >34.1 and lng > -122.3 and lng<-118.4 order by population DESC;


Next if we are interested in business in a city Calgary in state AB, we can do the following retrieve.

Select * from sources business where city contains “Calgary” and state contains “AB”;

Sometimes i messed up with the quotes….

The result as you can see is from higher review and higher review count to lower…


Finally, we are interested in the review for the first business, we can do the retrival again.
Again ranked by keywords.

That’s a simple demo, thanks!

