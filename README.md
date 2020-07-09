# Devops-Docs


Source Code Editor Eclipse

We have used Eclipse editor for the setup of building an application in our local machine from writing the code to the deployment of application in our local machine with the localhost URL to see the resultant output.

In our Organization We are using Spring boot frameworks for our Java applications to build the applications, as part of the build we have installed all the dependencies like spring boot, SonarQube, jacoco etc. Eclipse is really an amazing tool which provides so much flexibility to write source code and build the jar by integrating with multiple tools like git. And maven with the help of plugins.

Source code Management tool (or) Version Control tool Git hub

To maintain the Source code in Central repository we have created a Git hub repository which is used to maintain all the source code in a central place. Initially we have installed a Git bash and Git GUI in our local machines which is integrating with the Git hub repository in Cloud where we store all the source code of JAVA application in a central location.

Please find the below Screenshot for the repository Structure I have used for my source code in GitHub repository to keep all my source code. We have used branching strategy in git to create and update the code in Multiple branches for different features and for each update we have created versions with major, minor and patch versioning approaches using tags and release cuts provided by the GitHub to maintain the source in multiple versions. 

Branching strategy which we will use for differentiating the features and updating the code which avoids the changes in the stable code by creating a multiple branch helps in maintaining the stable code and code changes separately. Please refer to the git hub repository for further information https://github.com/Pavani-SoftWorldUSA?tab=repositories

Once we have done the changes in the test branch for new feature and tested in local and  we will raise a pull request to the master branch in the repository and removes the test repository once the changes merged into master branch with the approvals of our team members  and team lead working on same project.


Once we build an application locally, we will push it to the GitHub repository and now Jenkins will come into the Picture for Continuous Integration and continuous deployment. We have used Jenkins which is a Continuous integration and Continuous deployment tool will integrate with Source Code with the help of webhooks to trigger the Jenkins jobs automatically with the changes pushed to Git hub by discovering the changes in repository.  

Build Tool Maven

Once we write the source code, we need to perform different tasks with the different phases following a complete life cycle like validate, compile, test, package, verify, install, deploy. These are the stages we have implemented on our source code to build the jar package.
These are the main coordinates we will be using for Maven phases which will be given in Project Object. Model simply called as Pom.xml file for Java Projects.

	Group artifacts of a logical type under the same area considered as groupId
	Identify libraries under a specific name called artifactId
	Specify versions with each change called version
	Specifying different Packaging formats for an artifact (like jar, Zip).


we will tell maven to download whatever the dependencies we need to build our application in our local and in Jenkins machine as well with the help of pom.xml file.

<dependencymanagement>
<dependencies>
    <dependency>
        <groupId> groupId </groupId>
        <artifactId> artifactId </artifactId>
        <version> version</version>
        <scope>test</scope>
    </dependency>
<dependencies>
</dependencymanagement>

example:

<dependencymanagement>
<dependencies>   
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>2.1.0.RELEASE</version>
    </dependency>
</dependencies>
</dependencymanagement>

Like we added dependency management to download the considered dependencies for supporting Java Code. We have added plugins to extend the functionality or usage like packaging artifacts with necessary formats like Zip for that we will use maven-assembly-plugin. 

For example:

<plugins>
  <plugin>
    <artifactId>maven-assembly-plugin</artifactId>
    <version>3.1.0</version>
     <configuration>
             <descriptors>
	<descriptor>assembly.xml</descriptor>
             </descriptors>
    </configuration>
    <executions>
          <execution>
               <id>make-assembly</id>
               <phase>package</phase>
               <goals>
	<goal>single</goal>
                <goals>
          <execution>
       <executions>
  </plugin>
</plugins>

Simply we can say that the Maven reads the pom.xml file and perform the actions or phases based on the mvn Command we have given for the implementation. 

Code Coverage tool ‘SonarQube with Jacoco’

While running the tests we integrated Jenkins with jacoco with the help of plugins which helps in scans code and create reports for source code coverage of blocks, lines, methods, branches and place reports in target folder of the package.

SonarQube will collect those reports and push it to SonarQube User interface to display in web application for easy understanding for the users to find out fix the bugs and vulnerabilities which helps in finding the bugs in early stages to fix before the releases in live.
 

Artifact repository ‘Nexus’

Using Maven, we can deploy multiple versions of same artifact with the help of release and snapshot repositories considered in Nexus. So once build the jar we need to push or deploy it to the central artifact repository Nexus we are using in our organization.

Following the jar build with maven we need to store all the artifacts to the central repository. For that we are using Nexus in our Organization. In Nexus, we will have defined repositories for snapshots and release versions.


Snapshot repositories:	

As long as we are building jars locally, we will send all jar packages to the snapshot repositories and once we are ready for implementing these jars with pipeline which helps in automating deployment we will use Jenkins to push jars into the same snapshot repositories with incremental versions for differentiating multiple features of the application.
Example version: 1.0.0-SNAPSHOT-2344567483


Release Version repositories:
	
Until we are ready for the release with stable code, we will push all the jars to snapshot repositories and once we are ready for the deployment in AWS, we will build jars with. Stable code and push it to Release version repositories.
	Example version: release-1.0.0-9.

For connecting to nexus maven will use credentials in server section of settings.xml in customized defined path and publish the artifacts to the respective repositories with the help of distribution management section in Pom.xml for the destination location in nexus where exactly need to push the artifacts to. 

Distribution management section for destination location in Nexus

<distributionManagement>
    <repository>
      <id>nexus</id>
      <name>Releases</name>
      <url>https://nexus.softworldusa.com /repository/maven-releases</url>
    </repository>
    <snapshotRepository>
      <id>nexus</id>
      <name>Snapshot</name>
      <url> https://nexus.softworldusa.com /repository/maven-snapshots</url>
    </snapshotRepository>
</distributionManagement>


Server Section for credentials to login to nexus in settings.xml file

<servers>
  <server>
    <id>repos</id>
    <username>UserID</username>
    <password>Password</password>
  </server>
</servers>


 
Containerization tool ‘Docker’

As we are using the Containerization deployments, we need to build an image for running our application as a Docker container.  For creating Docker images, we have installed docker in our machines and Jenkins as well.

We have created Docker files where we will give commands to docker like pulling the jar from nexus and build an image based on that jar and installation of necessary tools and configurations for supporting the application running as a container in Kubernetes Environment.

We wrote shell scripts for the installation of supporting tools and updating configurations and secrets needed for the application to be up and running by communicating with multiple micro services. These scripts will be running at the time of container boot strapping for setting up all the mandatory actions needs to be done.

We have run multiple containers locally with the help of docker commands and docker images created locally and once we have tested everything locally, we have updated all the images to the Docker Hub.

Some of the docker commands we have used are as follows.

Build images and list images:

	docker build -t <docker-image-name>:<version> ( build an image)

	docker image ls (list the images)

	docker image rm <docker-image-name>:<version> (delete image)

Share images:

	docker login -u “Username” -p “Password” <registry-url>

	docker tag <image-name>:<version> repo/<image-name>:<version>

	docker push <image-name>:<version> repo/<image-name>:<version

	docker pull <image-name>:<version>

Run images:

	docker container run –name <app-name> -p <port number> <image-name>:<version>. (run container)

	docker container stop app-name (stop the container)

	docker container kill app-name (kill container)
 
	docker network ls (list the networks)

	docker container ls (list the running containers)

	docker container rm -f $(docker ps -aq) (delete all running and stopped containers)

	docker container logs <app-name> (print the logs)

External Registry ‘Docker Hub’

Docker Hub is a external registry to store all the docker images in the central repository. We have updated all the docker images we have created locally and in the Jenkins pipeline as well to Docker Hub with definite tags. 

We have used multiple docker commands to create docker images, tag that image, pushing that image to Docker Hub/ our organizational external registry and pulling that image to the elastic container instances of elastic container service, where we are running all our applications in the form of containers inside the container instances.


 
Docker Hub



Continuous Integration Continuous Deployment tool Jenkins.

For implementing the builds we have applied configurations in the Jenkins Configurations page available for project wide and job wide as well.  And to automate this we have used Jenkins Job builder with the help of groovy scripts to implement all the jobs in Jenkins.

For Example:
To build a job we followed the following skeleton Structure to implement the jobs using Groovy scripts.


pipeline {
	agent { label Jenkins-slave }

//To disable concurrent builds in Jenkins.
options {
	disableConcurrentBuilds()
}

//Read service name and version from pom.xml file in java code.
environment {
	Version = readMavenpom().getVersion()
	parentArtifactId. = readMavenPom().getArtifactId()
}

stages{
        stage(‘checkout’){
	steps {
	    script{
		def repo = “https://github.com/Pavani-SoftWorldUSA/aws-terraform-workshops.git”  
		git url: repo, credentialsId:  ‘Jenkins credential Id’, branch: “master”
	}
        }
}
stage(‘build and deploy’){
	steps {
	    script{
		
		mvn deploy; 
	}
        }
}
stage( ‘Sonar’ ){
	steps {
	    script{
		
		mvn package with sonar args to pass;
	}
        }
}
stage( ‘deployment’ ){
	steps {
	    script{
		
		deployment using aws Commands;
		}
        	    }
}
      }
}

Using the above-mentioned groovy scripts. We will update the configurations of Jenkins jobs as per the requirement of our application builds. Please find the below screenshot for the jobs we triggered by integrating with devops tools.

 
Jenkins Jobs for Builds and Deployment


In the same way with the help of master slave architecture in Jenkins, we will trigger multiple slaves for the jobs we are running every time. Here in Jenkins master will trigger slaves automatically using the configurations and provide the platform to run our job with the help of agent label provided in the groovy scripts to schedule the jobs in slaves.

And we were able to manage number of slaves with the number of jobs triggering simultaneously and were able to control the jobs to schedule on particular slaves with the help of labels provided for slaves and configured those slave labels to the jobs.

For adding external features to the Jenkins, we have used the concept of plugins to integrate with, for this purpose we have used manage plugins section provided in Jenkins and installed the available plugins in Jenkins. Once we installed the plugins, we were able to see the configuration section relating to that plugin in our Jenkins configuration section so that we will update the respective configurations to integrate with that related tools.




For example:

Manage Jenkins/Manage Plugins

To integrate with source code management tool, we need to install the git plugin from Jenkins plugin manager, and we need to update the related Configurations like credentials for the Jenkins user to login to GitHub and check out the code from GitHub.

Please find the below screenshot to install plugin and update the configurations of respective plugins to integrate with it.
Installing plugin using plugin manager to integrate with GitHub
 
Jenkins Plugin Manager for Git Plugin







To update the configurations of GitHub

 
Jenkins Configurations with installation of Git plugin

Similarly, we will do the integrations with Multiple DevOps tools with the help of plugins and configurations update.

To Automate these procedures, we are using Groovy scripts, bash shell scripts and python scripts to implement from Infrastructure to the deployment.


Deployment in ‘AWS Elastic Container Service’

Create AWS Infrastructure with ECS Cluster

AWS ECS is an AWS cloud provided Elastic container service which is used to deploy the applications on EC2 Container instances called ECS container instances. We wrote terraform scripts for creating the infrastructure for the ECS cluster in AWS Cloud environment. We have created ECS cluster with 2 Ec2 instances which are sufficient for hosting our applications with the help of AWS Command line tool and Terraform scripts.

As part of the cluster we have created VPC virtual private Cloud with the provided CIDR Ranges. We have created private and public subnets with the ec2 instances in subnets and restrict the accesses with the help of Access control list which restricts the traffic at subnet level and security groups which restricts the traffic at the Ec2 instances level. As part of the cluster we have created an Application load balancer with the target groups having the instances attached to the related target groups and controlled the traffic to the certain ec2 instances with the help of load balancing strategies.




Integrating AWS with Jenkins

Once we have created an Infrastructure with the help of terraform scripts which helps in automating the infrastructure creation repeatedly as we have created re-usable terraform scripts for creating AWS-ECS-clusters  and we have integrated the AWS with the Jenkins with the help of AWS plugins including the configurations needed for connecting to AWS. 

We wrote Groovy Scripts which is considered as declarative syntax in the Jenkins pipeline for running the containers in ECS- instances. And we gave aws commands as bash shell commands inside the groovy scripts by differentiating into different stages like running different actions in each stage.

 



Deployment of Applications using Jenkins and aws cli commands.

As part of running containers, we have created definite task-definitions which helps in pulling the docker images and running the containers as per the configurations we have given in the task-definition through a JSON file.

As part of running this application, we have attached volumes and mount those volumes to the containers which helps in providing all the necessary configurations and secrets needed for the successful running of an application inside the container.

We have created service to expose the application to the external world with the help of AWS-CLI commands given in the groovy scripts and this service will be integrating with load balancer for scheduling the tasks of a container which we can also called applications running as containers.

 





Monitoring tool ‘Cloud watch, Cloud trail and Prometheus’

For monitoring all our applications, we have installed agents in the containers at the time of boot strap which collects all the required information called as metrics that we need for tracking the status of the application.

we have integrated with cloud watch to collect all the logs from the containers with the help of agents and push all these logs to the Prometheus and Grafana which will give the graphical representation of all the resources and we have used SNS (Simple Notification Service) provided by the AWS cloud for sending an alerts to the respected users like application teams and the DevOps.

 

