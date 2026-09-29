URL of the application deployed on Kubernetes: http://ec2-3-210-67-113.compute-1.amazonaws.com:32093/api/students

Installation & Setup instructions:
Step 1: Create the Application Using Spring Boot in STS IDE
Install Spring Tool Suite (STS)
Download and install STS from Spring Tools 4 for Eclipse.

Download the Maven Project from Spring Initializr. Choose correct depecndencies like Spring Web, Spring DataJPA, MS SQL Server Driver  
Import the Maven Project in STS IDE choosing the root directory as the one containing pom.xml file. 
Create RDS database instance in AWS. Note down its name (in configuration), Username and Password for the database
Try connecting the database with SQL workbench
I also used workbench to add data to tables in the database.
Form APIs one by one and run the project with every addition
First create the Application properties file with database connection URL, name and Password.
Create main Java Application class.

Java Files
Main Application File (DemoApplication.java)
Purpose: This is the entry point of the Spring Boot application. It contains the main method which uses SpringApplication.run() to launch the application.

Controller File (StudentController.java)
Purpose: This file contains RESTful API endpoints for managing Student entities. It handles HTTP requests and responses, mapping client requests to service methods.

Service Interface (StudentService.java)
Purpose: This interface defines the service layer for Student operations. It declares methods for retrieving, creating, updating, and deleting Student entities.

Service Implementation (StudentServiceImpl.java)
Purpose: This class implements the StudentService interface. It uses StudentRepository to perform CRUD operations and contains the business logic for managing Student entities.

Repository File (StudentRepository.java)
Purpose: This file extends the JpaRepository interface to provide CRUD operations for the Student entity. It enables database interaction without the need for boilerplate code.

Exception File (ResourceNotFoundException.java)
Purpose: This file defines a custom exception that is thrown when a requested resource (like a Student entity) is not found in the database. It returns a 404 Not Found HTTP status.

Model File (Student.java)
Purpose: This file defines the Student entity, mapping it to a database table. It includes fields like firstName, lastName, and other survey-related attributes.

Other files:
Application Properties (application.properties)
Purpose: This file contains configuration settings for the Spring Boot application. It includes database connection details, Hibernate settings, and other properties.

Maven Configuration (pom.xml)
Purpose: This file manages the project's dependencies, build configuration, and plugins using Maven. It includes dependencies for Spring Boot, Spring Data JPA, MySQL, and others

Install POSTMAN in your system. 
After debugging the project and running it successfully. I tested GET all students, POST student to create new student, GET student by ID, Delete, PUT to update a student

Run mvn clean package command in project root directory. It saves the executable JAR file of application in Target folder. 

Create a Dockerfile in the root directory of your project

FROM openjdk:17-jdk-alpine
VOLUME /tmp
ARG JAR_FILE=target/Demo-0.0.1-SNAPSHOT.jar
ADD ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]


# Step 1: Build the Docker image
docker build -t springboot-app:latest .

# Step 2: Verify the Docker image
docker images

# Step 3: Tag the Docker image
docker tag springboot-app:latest shalini253/springboot-app:1.0

# Step 4: Push the Docker image to Docker Hub
docker push shalini253/springboot-app:1.0


Deploy the Application on Kubernetes: Create Kubernetes deployment and service file, mysql-secret (containing Database credentials) and apply them using kubectl.
Create Deployment and Service Yaml. Set target port in Service yaml as 8081 (my application is running on 8081)

kubectl apply -f deployment.yaml

kubectl create secret generic mysql-secret --from-literal=username=your-mysql-username --from-literal=password=your-mysql-password

kubectl apply -f service.yaml

kubectl get pods

kubectl get svc

kubectl get deployments

Access the application using the external IP of the EC2 instance and the NodePort, like:
http://<EC2-Public-IP>:<NodePort>/api/students


GIT
# Initialize git repository (if not already done)
git init

# Add remote repository
git remote add origin https://github.com/your-username/your-repo-name.git

# Add all files to staging
git add .

# Commit files
git commit -m "Initial commit"

# Push files to GitHub
git push -u origin master  # Use 'main' if your default branch is named 'main'


Set Up CI/CD Pipeline: Create a Jenkinsfile to automate the build, push, and deployment process
1. Create or Update Jenkins Pipeline
Open Jenkins Dashboard:

Go to your Jenkins dashboard.
Create a New Pipeline Job:

If you haven’t already, click "New Item" on the left side.
Enter a name for your pipeline job.
Select "Pipeline" and click "OK."
Configure Pipeline Job:

Click "Configure" on the left side of your pipeline job.

2. Configure SCM Integration
Go to Pipeline Section:

In the "Pipeline" section of the job configuration, select "Pipeline script from SCM."
Select SCM Type:

Choose "Git" from the SCM options.
Enter Repository Details:

Repository URL: Enter the URL of your GitHub repository (e.g., https://github.com/yourusername/your-repo.git).
Credentials: Add credentials if your repository is private. Click "Add" to provide your GitHub credentials.
Branch Specifier: Specify the branch you want Jenkins to build, typically main or master.
Script Path:

Specify the path to your Jenkinsfile in your repository. By default, this is usually Jenkinsfile.

3. Save and Build
Save Configuration:

Scroll down and click "Save" to apply the changes.
Run the Pipeline:

Go to your pipeline job page.
Click "Build Now" to run the pipeline.

Check the console output with every build. Keep debugging until you successfully run the pipeline. 

[Install Maven on Jenkins Server
sudo apt update
sudo apt install maven

Update Jenkins Configuration
If Maven is installed but Jenkins cannot find it, you might need to configure Jenkins to recognize Maven:

Configure Maven in Jenkins:

1. Access Jenkins Configuration
Open Jenkins Dashboard:

Navigate to your Jenkins dashboard.
Go to Global Tool Configuration:

Click on "Manage Jenkins" from the left sidebar.
Select "Global Tool Configuration."

2. Configure Maven in Jenkins
Find the Maven Section:

Scroll down to the "Maven" section.
Add Maven Installation:

Click "Add Maven."
Provide Maven Details:

Name: Enter a name for your Maven installation (e.g., Maven 3.8.6).

Install automatically: Leave unchecked if Maven is already installed on your EC2 instance.

Maven Home: Enter the path to your Maven installation directory. For example, if Maven was installed in /usr/share/maven, set it as follows:

/usr/share/maven]

IMPORTANT POINTS:
create mysql-secret which contains database username and password
We give reference of this MySQL secret in deployment.yaml
Change security rules in the EC2 instance to allow traffic from anywhere on 8081 port (our app is on 8081), in nodeport give access range from 30000-32767.
Create new Personal Access Token in dockerhub for allowing access to docker from Jenkins. Save it in 'Manage Jenkins' under credentials part. It will be later used in Jenkinsfile. 
Save the kubeconfig file as secret file in Jenkins. Save its ID for later referencing in Jenkinsfile. 


References of Tools used:

Spring Boot: spring.io
Spring Initializr: start.spring.io
Docker: docker.com
Kubernetes: kubernetes.io
Amazon RDS: aws.amazon.com/rds
MySQL: mysql.com
Postman: postman.com
Jenkins: jenkins.io


Demo link: https://drive.google.com/file/d/1NHoT3bXx8-2fhW_cMPT7SK9hG14eiyOk/view?usp=sharing