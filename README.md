# Wine Park Application

This Spring Boot application manages wines within a wine park.

## Requirements

To build and run this application, you need the following installed:

- JDK 17
- Maven
- MySQL

## Explore REST APIs

The application offers the following CRUD APIs:

### `GET /wine/getAllWines`

- **Description:** Retrieve all wines.
- **Usage:** Get a list of all wines available in the wine park.

### `POST /wine/add`

- **Description:** Add a new wine.
- **Usage:** Add a new wine to the wine park.

### `UPDATE /wine/updateWine/{id}`

- **Description:** Update wine by ID.
- **Usage:** Modify the details of a specific wine using its unique ID.

### `DELETE /wine/deleteWine/{id}`

- **Description:** Delete wine by ID.
- **Usage:** Remove a particular wine from the wine park using its ID.

These APIs allow you to perform essential CRUD operations on the wines managed by this application.

## Running the Application

1. Ensure you have JDK 17, Maven, and MySQL installed.
2. Clone this repository.
3. Configure the MySQL database settings in the application properties.
4. Run the application using `mvn spring-boot:run`.
5. Access the APIs via the defined endpoints to interact with the wine park.

Feel free to explore and interact with these endpoints to manage the wines within the wine park.


# Continuous Integration

## Workflow for Feature-Shantanu

This workflow automates various tasks including building, testing, code coverage, security scanning, and deploying Docker images using GitHub Actions.

## Workflow Triggers

This workflow runs on:
- Push to the `feature-shantanu` branch
- Pull request to the `feature-shantanu` branch

## Steps

1. **Checkout code**
   - Uses: `actions/checkout@v2`
   - Description: Checks out the repository's code into the workflow's workspace.

2. **Set up JDK 17**
   - Uses: `actions/setup-java@v2`
   - Description: Sets up Java Development Kit (JDK) version 17 in the GitHub Actions environment.

3. **Run Unit Tests**
   - Command: `mvn test jacoco:report`
   - Description: Runs unit tests for the Java project using Maven. Generates a code coverage report using JaCoCo.

4. **Code Coverage Report**
   - Uses: `actions/upload-artifact@v2`
   - Description: Uploads the generated code coverage report as an artifact (`target/site/jacoco/index.html`) within the project's directory.

5. **SonarQube Scan**
   - Uses: `kitabisa/sonarqube-action@v1.2.0`
   - Description: Performs a SonarQube scan on the project, connecting to the specified SonarQube server for analysis.

6. **Configure AWS Credentials**
   - Uses: `aws-actions/configure-aws-credentials@v4`
   - Description: Configures AWS credentials to assume a role using AWS IAM, setting the AWS region to `us-east-1`.

7. **Packaging Artifact**
   - Command: `mvn package -Dmaven.test.skip`
   - Description: Packages the artifact using Maven, skipping tests.

8. **Login to Amazon ECR**
   - Command: `aws ecr get-login-password | docker login --username AWS --password-stdin`
   - Description: Logs in to Amazon ECR to authenticate the Docker client for pushing images.

9. **Run Trivy vulnerability scanner**
   - Uses: `aquasecurity/trivy-action@master`
   - Description: Scans the specified Docker image in Amazon ECR for vulnerabilities using Trivy. Checks for critical and high severity vulnerabilities.

10. **Build, tag, and push Docker image**
    - Commands:
      - `docker build -t <image>:<commit_SHA> .`
      - `docker push <image>:<commit_SHA>`
    - Description: Builds the Docker image using the code from the repository, tags it with the commit SHA, and pushes it to the Amazon ECR repository.


# Continuous Deployment

## Deploy to EC2 Workflow

This workflow automates the deployment process to an EC2 instance on AWS whenever changes are pushed to the `feature-shantanu123` branch.

## Workflow Triggers

This workflow runs on:
- Push to the `feature-shantanu` branch

## Steps

1. **Checkout code**
   - Uses: `actions/checkout@v2`
   - Description: Checks out the repository's code into the workflow's workspace.

2. **Configure AWS Credentials**
   - Uses: `aws-actions/configure-aws-credentials@v4`
   - Description: Configures AWS credentials to assume a role using AWS IAM, setting the AWS region to `us-east-1`.

3. **Create SSH directory and known_hosts file**
   - Command: `mkdir -p ~/.ssh && touch ~/.ssh/known_hosts`
   - Description: Creates SSH directory and a known_hosts file.

4. **Extract SSH private key**
   - Command: `echo "${{ secrets.PRIVATE_SSH_KEY }}" > ~/.ssh/id_rsa`
   - Description: Adds the private SSH key to `id_rsa`.

5. **Set proper permissions for the private key**
   - Command: `chmod 600 ~/.ssh/id_rsa`
   - Description: Sets appropriate permissions for the private key.

6. **Install SSH Client**
   - Command: `sudo apt-get install -y openssh-client`
   - Description: Installs the SSH client tool.

7. **Deploy to EC2 with Docker**
   - Description: Deploys the application on an EC2 instance using Docker.
   - Commands:
     - Adds EC2 instance's SSH key to `known_hosts`.
     - Logs in to Amazon ECR to authenticate the Docker client.
     - Connects to the EC2 instance via SSH using the private key.
     - Updates and installs Docker on the EC2 instance.
     - Pulls the Docker image from Amazon ECR and runs it on the EC2 instance.


