# elastic-apm-docker-compose
A docker-compose setup to deploy a local stack for elastic-apm https://www.elastic.co/apm to receive and visualise metrics from instrumented applications for performance monitoring.

# pre-requirements
 - docker must be installed

# how to start
run `docker-compose up` from the commandline inside of the main directory of this project.
The stack will take ~1 minute to start up.

Open http://localhost:5601 in your local browser see the overview of Kibana.
Select APM from the menu and follow the instructions to instrument your application.

# Java instrumentation

Download the agent jar file from Maven central https://mvnrepository.com/artifact/co.elastic.apm/elastic-apm-agent and store it in a local directory.
Don't add it as a build dependency to your application.

Start your application with the `-javaagent` flag pointing to the downloaded jar and configure the required parameters.
use the same token for `elastic.apm.secret_token` that is configured in the `docker-compose.yml`
```
java -javaagent:/path/to/elastic-apm-agent-<version>.jar \
      -Delastic.apm.service_name=my-java-application \
      -Delastic.apm.server_urls=http://localhost:8200 \
      -Delastic.apm.secret_token=xxVpmQB2HMzCL9PgBHVrnxjNXXw5J7bd79DFm6sjBJR5HPXDhcF8MSb3vv4bpg45 \
      -Delastic.apm.application_packages=org.example \
      -jar my-application.jar
```

When using the Servlet API in combination with a dispatcher servlet you should add the parameter
`-Delastic.apm.use_path_as_transaction_name=true` to name the transactions by the requested URL instead of the class/method name, which would always be the dispatcher for all requests.
In addition, you can combine multiple request URLs into one group with a regex. This makes sense if changing IDs are part of the URL.
`-Delastic.apm.url_groups=/user/*,/model/*,/directory/*`

you can find a list of all configuration parameters here: https://www.elastic.co/guide/en/apm/agent/java/current/configuration.html

After starting and interacting with the instrumented application, you should find the service name in the APM listing.

# Node.JS instrumentation

Install the agent as NPM dependency `npm install elastic-apm-node --save`

Add this to the top of your first loaded file in your application. 
```
import apm from 'elastic-apm-node'
apm.start({
  serviceName: 'my-nodejs-application',
  secretToken: 'xxVpmQB2HMzCL9PgBHVrnxjNXXw5J7bd79DFm6sjBJR5HPXDhcF8MSb3vv4bpg45',
  serverUrl: 'http://localhost:8200',
})
```
The configuration may vary based on the used frameworks:

https://www.elastic.co/guide/en/apm/agent/nodejs/current/set-up.html

https://www.elastic.co/guide/en/apm/agent/nodejs/current/es-modules.html