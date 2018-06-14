# kdb-rest-service

An open source REST service written in java used to connect to an instance of kdb using JSON. The REST service can provide a single query to run or call a function predefined on the instance of kdb.

## Pre-requisite Configuration 
##### Properties
There is an `application.properties` file in the resources folder, connect the rest service and the instance of kdb by updating the properties below:

    kdb.host=localhost
    kdb.port=6007
    kdb.username=admin
    kdb.password=admin
    server.port=8080
    
##### Certificates
The requests are sent in HTTPS format and to provide this the project has a self-signed certifiate embedded within. It is strongly recommended that you add your own certificate. Updating the certificate will require an update to the following properties in `application.properties`:

    security.require-ssl=true
    server.ssl.key-store-type=PKCS12
    server.ssl.key-store=classpath:keystore.p12
    server.ssl.key-store-password=aquaq2018
    server.ssl.key-alias=tomcat

##### Authentication
The rest service uses basic authentication and is using a single username and password which are configured in the `application.properties` file:

    basic.authentication.user=user
    basic.authentication.password=pass

These value are provided within the header of the request, it is strongly recommended to invoke your own security if you use the project.

## EndPoints

The kdb-rest-service provides two endpoints: executeFunction and executeQuery. 

##### ExecuteFunction Request
The executeFunction provides a means to call a predefined function and pass parameters to the kdb instance. 
For example this is the format of a request calling a function called plus which passes two arguments labelled "xarg" and "yarg" with values 96.3 and 9.7:

e.g. FunctionRequest
    
    {
    "function_name" : "plus",
    "arguments" : 
            {
                "xarg" : "96.3",
                "yarg" : "9.7"
             }
    }
    
##### ExecuteQuery Request
The executeQuery provides a means to provide a query to the kdb instance, by default this endpoint is disabled using the property freeform.query.mode.enabled, to enable change the value to true. 
For example this is the format of a synchronous query request where the user expects a response to be returned:

e.g. QueryRequest
       

    {
        "type" : "sync",   
        "query" : "select from table",
        "response" : true
    }

##### Response from Rest API
The json response for the endpoint calls will comprise of 4 parts: result, requestTime, responseTime and success. 

- Result is the response returned by KDB with some additional parsing.

- RequestTime is the time the time the request began processing on the api.

- ResponseTime is the time the response was returned from the api.

- Success is a boolean which lets user know if the call to kdb was a success.


e.g. "query" : "([]a:enlist\"hello world\")" this should return a single item
    
    
    [
        {
            "result": [
                {
                    "a": "hello world"
                }
            ],
            "requestTime": "2018-06-14T09:05:12.513Z",
            "responseTime": "2018-06-14T09:05:12.528Z",
            "success": true
        }
    ]
"query" : "([]a:(\"hello\";\"world\"))" this should return a list of strings

    [
        {
            "result": [
                {
                    "a": "hello"
                },
                {
                    "a": "world"
                }
            ],
            "requestTime": "2018-06-14T09:05:51.610Z",
            "responseTime": "2018-06-14T09:05:51.620Z",
            "success": true
        }
    ]   
    
Failure response will follow a similar pattern except result will the error returned

"query" : "([]a:enlist”hello world”) ",
    
    [
        {
            "result": "error: failed to run query on server localhost:34203: world",
            "requestTime": "2018-06-14T09:04:28.634Z",
            "responseTime": "2018-06-14T09:04:28.649Z",
            "success": false
        }
    ]
    

## Deploying 

There is a DockerFile within the project for deploying the project on docker.

Alternatively it can be run locally by providing the appropriate build configuration via command line or IDE. 

To run the application by command line:

Either pull the code from github, update `application.properties` and maven clean install to create the new jar then run:

    java -jar target\kdb-rest-service-1.1-SNAPSHOT.jar

 OR
 
Download the most recent jar from release section of github master (https://github.com/AquaQAnalytics/kdb-rest-service/releases/), create a new properties file in a location of your choice to override the default properties and run this command

    java -jar -Dspring.profiles.active=test -Dspring.config.location=C:\Programming\application-test.properties target\kdb-rest-service-1.1-SNAPSHOT.jar

* -Dspring.profiles.active would be the profile you wish to run the application with which would have its own `application-{profileName}.properties` file. 

* -Dspring.config.location would be the location of the custom properties file.

## Swagger UI
The application has incorporated the Swagger UI utilities, to access the swagger page load the application and hit the swagger url:

    https://<host>:<port>/swagger-ui.html

## Contributing 
The branch is currently locked down and will require a pull request reviewed by a member of the AquaQ team before any changes can be committed.