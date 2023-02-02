# Voting System Application

## Introduction

Voting System Application is an API for managing voting sessions.

Through this system you can allow your associates to vote on voting agendas with a pre-established duration. You can then check the voting results.

As a security measure, each user can only vote once per agenda and agendas have a minimum duration of one minute.

The system is composed of four services: associates, agendas, votes and results. Each service is responsible for managing its own domain and business rules. These services work together to ensure the full functioning of the system and communicate through HTTP requests and asynchronous messages.

## Requirements to run the system

To run the system you need to have on your machine:

- Git
- Docker
- A HTTP client, like Postman or Insomnia

## How to run the system


First, clone the repository and then go into the repository directory:

```jsx
git clone https://github.com/cararax/voting-system-app && cd voting-system-app
```

Next, start the system via docker compose with the command:

```js
docker compose up
```

There you go, the system will be up and running using ports 8081, 8082, 8083 and 8084.

## System Description

Voting System Application is a system that runs from a set of containers described in a docker-compose file.

The system is built with the following components:

- [Associates Service](https://github.com/cararax/associate-service)
- [Agendas Service](https://github.com/cararax/agenda-service)
- [Votes Sevices](https://github.com/cararax/vote-service)
- [Result Services](https://github.com/cararax/result-service)
- A Postgres container
- A RabbitMQ container

The docker-compose file  downloads the service images from dockerhub and connects them with the database and the message broker.

When the system starts, it checks the database for existing data and if not, the DataLoader class of each component instances initial data in the system.

This repository contains information about how the system works and how to run it.

Each service was developed in its own repository, where each step of the development process was described in the commits of the project, which can be accessed by clicking on the links above.


### Technologies used

This system was developed using the following technologies:

- Java 17
- Lombok
- Spring Boot
    - Web
    - Data Jpa
    - AMQP
    - Validation
    - SpringDoc-OpenApi
- PostgreSQL
- RabbitMQ
- Maven
- Docker

## System Components Description

### Associates Service

Service responsible for creating associates.

You can create a new associate by entering its CPF and e-mail. To view the associate, search by your associateID.

The service does not allow the creation of users with invalid or repeated CPF.

To see the repository of the service access: [https://github.com/cararax/associate-service](https://github.com/cararax/associate-service)

To see the documentation for this API access: [http://localhost:8081/swagger-ui/index.html](http://localhost:8081/swagger-ui/index.html)


### Agendas Service

Service responsible for creating the agendas of voting sessions.

You can create new agendas informing the description of the agenda and you can also set its end time. If you do not inform the end time, the system will automatically set the duration to 1 minute. In addition, it is possible to query an agenda by its agendaID.

The system runs a task every minute that checks if any agenda has exceeded its voting time. When this occurs, the system sends an asynchronous message to the results service, requesting the computation of the voting result.

To see the service repository, access: [https://github.com/cararax/agenda-service](https://github.com/cararax/agenda-service)

To see the documentation for this API access: [http://localhost:8082/swagger-ui/index.html](http://localhost:8082/swagger-ui/index.html)

### Votes Service

Service responsible for creating votes for a agenda.

You can create a new vote by entering the associateID, agendaID and your choice of vote (YES or NO).

When inserting a new vote the service performs consults through HTTP requests to associate service and agenda service, in order to verify that both exist and that the voting period has not expired. In addition, the voting service ensures that members can only vote once in a schedule.

The service sends the voting data when requested by the results service.

To see the service repository go to: [https://github.com/cararax/vote-service](https://github.com/cararax/vote-service)

To see the documentation for this API go to: [http://localhost:8083/swagger-ui/index.html](http://localhost:8083/swagger-ui/index.html)

### Results Service

Service responsible for calculating the voting results of an agenda.

You can check the result of an agenda by searching the resultID.

The calculation of the result works as follows:

- The results service receives an asynchronous message from the agenda service stating that the voting period has expired and requests the computation of the voting result.
- The results service sends an asynchronous message to the voting service, which returns with the voting data of a vote.
- With the voting data in hands, the results service counts the votes and defines the winning option of the vote. The result can be YES, NO or DRAW.

To see the service repository go to: [https://github.com/cararax/result-service](https://github.com/cararax/result-service)

To see the documentation for this API access: [http://localhost:8084/swagger-ui/index.html](http://localhost:8084/swagger-ui/index.html)

## Author

Pedro Carara, feel free to contact me at [Linkedin](https://linkedin.com/in/carara/) or at cararax@gmail.com