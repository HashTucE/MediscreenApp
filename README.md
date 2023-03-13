# Mediscreen_App

<p align="center">
  <img src=https://user-images.githubusercontent.com/95872501/224155098-59ee106a-10cd-4189-a830-e957db28003c.png>
</p>


Medical webapp composed by the following microservices :

1. patient, is a rest Api that allows you to create, modify or delete clients from a medical practice stored in a mySQL database.
2. note, is a rest Api that allows you to create, modify or delete notes to keep track of visits in a mongodb database.
3. assessment](https://github.com/HashTucE/Assessment.git) is a rest api that communicates with Patient and Note to retrieve data to assess the risk for a patient of contracting type 2 diabetes.
4. front, is an interface user developed in html with thymeleaf and bootstrap.
5. library, groups common objects that will be shared by the repository manager, Nexus.
6. mysql allow to persist data from Patient.
7. mongoDB allow to persist data from Note.



# Install the project with Docker

Knowing that :
- The 4 microservices's images required are stored into my `Docker Registry`
- The 2 databases's images required are stored into my `Docker Hub`
- The Library's package required is stored into my `distant Nexus repository`

You can follow the 5 steps below, to use straight the application.

But if you want to use your own images of microservices, check below `How to upload an image's service to your Docker Registry`
And if you want to use your own package of library through Nexus, check below `Configuration of Nexus` and `Deployment of Libray to Nexus`

1. Install [Docker Desktop](https://www.docker.com/products/docker-desktop/).
2. Clone [Mediscreen_App](https://github.com/HashTucE/Mediscreen_App.git) on your local machine.
3. Check if environment's variables, paths and binding ports into the docker-compose.yml fits to you and save changes if necessary.
4. Then build the project opening a prompt, locating to the root of Mediscreen_App folder and running the following command :
    ```
    docker-compose up -d
    ```
5. When all services are operationals, you should be able to access to [Mediscreen App](http://localhost:8083/home) 


# What you can do

- Browse the list of registered patients.
- Create/update a patient with warning message when trying to validate empty mandatory fields.
- Delete a patient.
- Access to the record of a patient when clicking on his last name with his assessment and his visit's notes.
- The assessment change dynamically depending on trigger's words containing into the triggers.txt.
- Create/update a note with warning message when trying to validate empty mandatory field.
- Delete a note.

# How to modify triggers list 

1. First you need the id of the assessment.ms's container and there is 2 ways to find it :
- Execute the following command into a prompt :
    ```
    docker ps
    ```
- Or you can find it below "assessment.ms" container into Docker Desktop :
<p align="center">
  <img src=https://user-images.githubusercontent.com/95872501/224640844-edf8b9f5-097e-469a-a7b9-5f93f3d3da2b.png>
</p>

2. Execute this command to access to the shell of assessment.ms's container :
    ```
    docker exec -it [replace by assessment_id] /bin/sh
    ```
3. Execute this command to access to triggers.txt folder :
    ```
    cd app/
    ```
3. Execute this command to open triggers.txt with "vi" editor :
    ```
    vi triggers.txt
    ```
4. Make your modifications :
- Use the `arrow keys` to navigate through the file.
- Press `i` to enter insert mode and start editing the file.
- Make the necessary changes.
- Press the `Esc` key to return to command mode.
- Type `:wq` to save changes and exit the "vi" editor.
- Press `Enter` to run the command.

    Extra information, there are 3 ways to quit the "vi" editor :
    - Type `:q` to exit the editor if you haven't made any changes.
    - type `:wq` to save the changes and exit the editor.
    - type `:q!` to exit the editor without saving changes.


# How to upload an image's service to your Docker Registry

To make it easier to read, I write an example with Patient but you will have to do it with patient, note, assessment and front.

Prerequisites :
- Java 19
- Maven 4.0.0
- Spring Boot 3.0.2
- MySQL 8.0.29
- Docker 4.16.2

2. Using a prompt, once located to the root of `patient`, execute this command to compile, test, package and install the project :
  ```
  mvn clean install
  ```
3. Still at the same location (to point to the `Dockerfile`) build the image of `patient` using :
  ```
  docker build -t jar .
  (Make sure that the jar name inside the Dockerfile is identical to the jar name located to the target folder !)
  ```
  
4. You can then execute this command to link your local image with a repository of `Docker Registry` :
  ```
  docker tag [local_image_name]:latest [your_docker_username]/[distant_repository_name]:latest
  (Make sure to be connected to your account on Docker Desktop !)
  ```
5. And finally execute this command to upload your local image to your `Docker Registry` :
  ```
  docker push [your_docker_username]/[distant_repository_name]:latest
  ```
6. If you want to build [Mediscreen](https://github.com/HashTucE/Mediscreen.git) with the image you just created, make sure to modify his docker-compose.yml of replacing the `image path` inside `patient.ms` by your own :
  ```
  [your_docker_username]/[distant_repository_name]:latest
  ```

# Configuration of Nexus

1. Install [Nexus](https://help.sonatype.com/repomanager3/installation-and-upgrades/installation-methods).
I recommend you to install it with [his](https://hub.docker.com/r/sonatype/nexus3/) Docker image.
2. Keep in mind that on your local machine the port 8081 will be used with patient so when installing Nexus, bind the port like this 8084:8081. Once installed and ran, click on the port as you can see below or go to http://localhost:8084 to be redirected to the Nexus interface user :
<p align="center">
  <img src=https://user-images.githubusercontent.com/95872501/224268285-bc76f6de-5481-49a2-b50c-66041ea6a6f6.png>
</p>

3. The first time you will sign in, you will be invit to change your admin password. Your credentials should be username : `admin` and password : `admin123` but it depends of your version of nexus so if it won't work, this command should show you the intitial password :
    ```
    docker exec -it [nexus_container_id] cat /nexus-data/admin.password 
    ```
    
4. If it not exists, you will have to create a file named settings.xml on you maven configuration hidden folder `.m2`. As you can see for this project I am using the already existing `maven-snapshots (hosted)` repository, so you will just have to set your appropriate Nexus's username and password :

        <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                            https://maven.apache.org/xsd/settings-1.0.0.xsd">
        <servers>
          <server>
            <id>maven-snapshots</id>
            <username>admin</username>
            <password>pass</password>
          </server>
        </servers>

        </settings>

    
# Deployment of Library to Nexus

Prerequisites :
- Java 19
- Maven 4.0.0
- Spring Boot 3.0.2


1. To the root of `Library` execute this command to create a package :
    ```
    mvn package
    ```
2. Then enter this command to deploy `library` to your `Nexus` repository :
    ```
    mvn deploy
    ```
    And you should now see your `.jar` inside the repository `maven-snapshots` on your `Nexus` interface.



# Home of Mediscreen App
<p align="center">
  <img src=https://user-images.githubusercontent.com/95872501/224282867-7c9e5771-60ac-4471-8fef-8a19eac6606a.png>
</p>

# Project Structure
<p align="center">
  <img src=https://user-images.githubusercontent.com/95872501/224648604-4607c4f2-610b-4f8c-be34-c115c7220040.png>
</p>

# JaCoCo Code Coverage
<p align="center">
  <img src=https://user-images.githubusercontent.com/95872501/224291049-89ab3512-10df-4006-af61-78d42765efc6.png>
</p>

<p align="center">
  <img src=https://user-images.githubusercontent.com/95872501/224291163-5eeb831c-e47b-4fac-b0a1-89e2e3bdbff4.png>
</p>

<p align="center">
  <img src=https://user-images.githubusercontent.com/95872501/224291291-a5c11169-50e9-4e47-9c6f-a3710221dac7.png>
</p>

<p align="center">
  <img src=https://user-images.githubusercontent.com/95872501/224291384-c99e972a-e7f0-408b-94f7-1d8b180e5e12.png>
</p>


# Technology Stack
<p align="center">
  <img src=https://user-images.githubusercontent.com/95872501/224289092-fa6f6430-fcaa-42a9-a6cb-10505ac7007c.png>
</p>



