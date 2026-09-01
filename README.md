# Doctor-Appointment-Scheduler

A Spring Boot web application that allows patients to register, log in, and schedule appointments with doctors. Doctors can register, log in, view and cancel appointments. The project uses Thymeleaf for templating, Spring Data JPA for persistence, and includes CI/CD pipelines for building and deploying to AWS EKS.

## Key Features

- Patient registration and login
- Doctor registration and login
- Patients can:
  - Book appointments
  - View their upcoming/past appointments
  - Cancel appointments
- Doctors can:
  - View appointments booked with them
  - Cancel appointments
- Cloud-native deployment (Docker + Kubernetes on EKS)
- Automated CI/CD pipelines
- Code quality scanning with SonarQube

## Technologies

- **Java 17** (Spring Boot 2.5.2)
- **Spring MVC** (Controller / Service / Repository layers)
- **Spring Data JPA** (ORM)
- **Thymeleaf** (server-side HTML templating)
- **H2** (in-memory database for development)
- **MySQL** (production database)
- **Maven** (build system)
- **Docker** (containerization)
- **Kubernetes (EKS)** (orchestration)
- **AWS CloudFormation** (infrastructure as code)
- **SonarQube** (code quality analysis)

## Prerequisites

- Java JDK 17+ installed and JAVA_HOME configured
- Maven 3.6+ installed
- Git (to clone the repo)
- Docker (for container builds)
- AWS CLI configured (for deployment)

---

## Architecture

### High-Level System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        User Browser                              │
│                   (Patient/Doctor UI)                            │
└────────────────────────────┬────────────────────────────────────┘
                             │
                    HTTP/HTTPS
                             │
┌────────────────────────────▼────────────────────────────────────┐
│                   AWS EKS / EC2 Cluster                          │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │           Spring Boot Application Pod(s)                 │   │
│  │                                                           │   │
│  │  ┌────────────────────────────────────────────────────┐  │   │
│  │  │         Spring Web MVC Controllers                 │  │   │
│  │  │   - Patient Registration & Authentication          │  │   │
│  │  │   - Doctor Registration & Authentication           │  │   │
│  │  │   - Appointment Management (Book/View/Cancel)      │  │   │
│  │  └────────────────────────────────────────────────────┘  │   │
│  │                         │                                 │   │
│  │  ┌────────────────────────────────────────────────────┐  │   │
│  │  │            Business Logic Layer                    │  │   │
│  │  │   - AppointmentService                             │  │   │
│  │  │   - PatientService                                 │  │   │
│  │  │   - DoctorService                                  │  │   │
│  │  └────────────────────────────────────────────────────┘  │   │
│  │                         │                                 │   │
│  │  ┌────────────────────────────────────────────────────┐  │   │
│  │  │         Spring Data JPA Repository Layer           │  │   │
│  │  │   - AppointmentRepository                          │  │   │
│  │  │   - PatientRepository                              │  │   │
│  │  │   - DoctorRepository                               │  │   │
│  │  └────────────────────────────────────────────────────┘  │   │
│  └────────────────────────────────────────────────────────┘   │
│                         │                                       │
│  ┌────────────────────────────────────────────────────────┐   │
│  │            Thymeleaf Template Engine                  │   │
│  │   - Registration Pages                                │   │
│  │   - Login Pages                                       │   │
│  │   - Appointment Booking Pages                         │   │
│  │   - Dashboard Pages                                   │   │
│  └────────────────────────────────────────────────────────┘   │
└────────────────────────────┬────────────────────────────────────┘
                             │
                    JDBC Connection
                             │
        ┌────────────────────┴────────────────────┐
        │                                         │
   ┌────▼─────┐                           ┌──────▼──────┐
   │    H2    │                           │   MySQL     │
   │ Database │                           │  Database   │
   │ (Dev)    │                           │  (Prod)     │
   └──────────┘                           └─────────────┘
```

### Application Package Structure

```
com.externship.appointment/
├── AppointmentApplication.java          (Spring Boot Entry Point)
├── ControllerClass.java                 (Request Handlers)
│
├── Person_storage/                      (Patient/User Entities)
│   ├── PatientEntity
│   ├── PatientRepository
│   └── PatientService
│
├── Doctor_storage/                      (Doctor Entities)
│   ├── DoctorEntity
│   ├── DoctorRepository
│   └── DoctorService
│
└── Appointment_storage/                 (Appointment Entities)
    ├── AppointmentEntity
    ├── AppointmentRepository
    └── AppointmentService

resources/
├── templates/                           (Thymeleaf HTML Pages)
│   ├── patient-registration.html
│   ├── doctor-registration.html
│   ├── login.html
│   ├── patient-dashboard.html
│   ├── doctor-dashboard.html
│   └── appointment-booking.html
│
├── static/                              (Static Assets)
│   ├── css/                             (Stylesheets)
│   ├── js/                              (JavaScript)
│   └── img/                             (Images)
│
├── application.properties                (Spring Configuration)
├── schema-h2.sql                        (H2 DB Schema)
└── data-h2.sql                          (H2 DB Sample Data)
```

---

## CI/CD Workflows

This project includes three automated GitHub Actions workflows for continuous integration and deployment:

### 1. Build Image Workflow (`build image.yaml`)

**Trigger**: Manual workflow dispatch

**Purpose**: Build and push Docker image to AWS ECR, run tests, and perform code quality analysis

**Steps**:
```
┌─────────────────────────────────────────────────────────────┐
│  1. Checkout Source Code                                    │
│     └─> Git clone the repository                            │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│  2. Setup JDK 17 & Maven Build                             │
│     └─> mvn clean verify (compile + test)                 │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│  3. SonarQube Code Quality Analysis                        │
│     └─> mvn sonar:sonar (scan code quality)               │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│  4. AWS Credentials Configuration                          │
│     └─> Configure AWS CLI with secrets                     │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│  5. Login to Amazon ECR                                     │
│     └─> Get authorization for ECR push                     │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│  6. Build Docker Image                                      │
│     └─> docker build -f Docker -t my-ecr-repo:latest .    │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│  7. Tag Image for ECR                                       │
│     └─> docker tag my-ecr-repo:latest <ECR-URI>           │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│  8. Push to Amazon ECR                                      │
│     └─> docker push <ECR-URI>                              │
└─────────────────────────────────────────────────────────────┘
```

**Environment Variables**:
- `AWS_REGION`: us-east-2
- `ECR_REPOSITORY`: my-ecr-repo

**Secrets Required**:
- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `SONAR_TOKEN`

---

### 2. Deploy to EKS Workflow (`Deploy to EKS.yml`)

**Trigger**: Manual workflow dispatch

**Purpose**: Deploy the application to AWS EKS cluster

**Steps**:
```
┌─────────────────────────────────────────────────────────────┐
│  1. Checkout Source Code                                    │
│     └─> Git clone the repository                            │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│  2. Configure AWS Credentials                              │
│     └─> Set up AWS CLI authentication                      │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│  3. Login to Amazon ECR                                     │
│     └─> Authorize Docker CLI for ECR access                │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│  4. Pull Docker Image from ECR                             │
│     └─> docker pull <ECR-URI>/my-ecr-repo:latest           │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│  5. Update kubeconfig                                       │
│     └─> aws eks update-kubeconfig (EKS cluster access)     │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│  6. Replace Image in K8s Manifest                          │
│     └─> sed -i (update K8-manifest.yml with ECR image)     │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│  7. Deploy to EKS                                           │
│     └─> kubectl apply -f K8-manifest.yml                   │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│  8. Verify Deployment Rollout                              │
│     └─> kubectl rollout status deployment/appointment-app  │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│  9. Check Pods & Services Health                           │
│     └─> kubectl get pods, kubectl get svc                  │
└─────────────────────────────────────────────────────────────┘
```

**Environment Variables**:
- `AWS_REGION`: us-east-2
- `ECR_REPOSITORY`: my-ecr-repo
- `EKS_CLUSTER`: my-eks-cluster
- `NAMESPACE`: default

**Secrets Required**:
- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`

---

### 3. CloudFormation Deployment Workflow (`Cloud Formation.yml`)

**Trigger**: Manual workflow dispatch with input parameters

**Purpose**: Create, update, or delete AWS infrastructure using CloudFormation

**Input Parameters**:
- `action`: choose-update (create/update stack) or delete
- `stack_name`: CloudFormation stack name (default: myapp-stack)
- `template_file`: Path to CloudFormation template (default: infrastructure/EKS-stack.yaml)

**Steps**:
```
┌────────────────────────────────────────────────────────────┐
│  1. Checkout Repository                                    │
│     └─> Git clone (only if action is create-update)        │
└────────────────┬─────────────────────────────────────────┘
                 │
     ┌───────────┴──────────┬──────────────────┐
     │                      │                  │
     │ (create-update)      │ (delete)         │
     │                      │                  │
┌────▼─────────────┐   ┌────▼──────────────┐   │
│ Validate Template │   │ Delete Stack      │   │
└────┬─────────────┘   └───────────────────┘   │
     │                                          │
┌────▼──────────────────────────────────────┐  │
│ 2. Install & Run CFN Lint                 │  │
│    └─> Validate YAML syntax               │  │
└────┬──────────────────────────────────────┘  │
     │                                          │
┌────▼──────────────────────────────────────┐  │
│ 3. AWS CloudFormation Validate Template    │  │
│    └─> Validate against AWS schema        │  │
└────┬──────────────────────────────────────┘  │
     │                                          │
┌────▼──────────────────────────────────────┐  │
│ 4. Check Stack Existence                  │  │
│    └─> Query existing stacks              │  │
└────┬──────────────────────────────────────┘  │
     │                                          │
     ├─────────────────┬──────────────────┐    │
     │                 │                  │    │
  EXISTS=false      EXISTS=true         │    │
     │                 │                  │    │
┌────▼───────┐    ┌────▼────────┐      │    │
│ 5a. Create │    │ 5b. Update  │      │    │
│    Stack   │    │    Stack    │      │    │
└────┬───────┘    └────┬────────┘      │    │
     │                 │                 │    │
     └─────────────────┼────────────┐   │    │
                       │            │   │    │
                ┌──────▼────────────▼─┐ │    │
                │ 6. Display Outputs  │ │    │
                └─────────────────────┘ │    │
                                        │    │
                                        └────┘
```

**Environment Variables**:
- `AWS_REGION`: us-east-2

**Secrets Required**:
- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`

---

## Getting started (local development)

1. **Clone the repository**

   ```bash
   git clone https://github.com/harikrishnan-creator/Doctor-Appointment-Scheduler.git
   cd Doctor-Appointment-Scheduler
   ```

2. **Build the project**

   ```bash
   mvn clean package
   ```

   or to run directly from Maven:

   ```bash
   mvn spring-boot:run
   ```

3. **Run the packaged JAR**

   After a successful build you can run the Spring Boot application with:

   ```bash
   java -jar target/*.jar
   ```

4. **Open the application in your browser**

   By default Spring Boot runs on port 8080. Open:

   ```
   http://localhost:8080
   ```

   Use the provided pages to register as a patient or doctor and to log in.

## H2 Console (in-memory DB)

While the application is running, you can access the H2 console to inspect the in-memory database (useful during development):

- URL: http://localhost:8080/h2-console
- JDBC URL (default for Spring Boot/H2 dev): jdbc:h2:mem:testdb
- Username: sa
- Password: (leave blank)

Note: If your application.properties configures a different URL or credentials, use those values instead.

## Application Structure (overview)

- **src/main/java** - Java source code (controllers, services, repositories, models)
- **src/main/resources/templates** - Thymeleaf HTML templates
- **src/main/resources/static** - static assets (CSS, JS, images)
- **src/main/resources/application.properties** - Spring Boot configuration (ports, datasource, JPA settings)

**Domain Model Entities**:
- **Patient** (registration info, contact details, appointments)
- **Doctor** (profile, specialization, appointments)
- **Appointment** (patient, doctor, date/time, status)

Repositories use Spring Data JPA to persist these entities into the H2 database during development.

## How to use the app (typical user flow)

1. **Register**
   - Patient: Fill the patient registration form and submit.
   - Doctor: Fill the doctor registration form and submit.

2. **Login**
   - After registration, use the login page to authenticate as patient or doctor.

3. **Book appointment (patient)**
   - Once logged in, navigate to the appointment booking page, select a doctor, choose date/time, and submit.

4. **Manage appointments**
   - Patients: View/cancel their appointments from the dashboard.
   - Doctors: View appointments scheduled with them and cancel if necessary.

If the UI has labeled links or navigation, follow those; otherwise check controllers and Thymeleaf templates to find the exact endpoints.

## Configuration

- To change port, database settings, or other Spring Boot properties, edit `src/main/resources/application.properties` or provide environment variables when running the app.
- Example change to run on port 9090 in application.properties:

  ```properties
  server.port=9090
  ```

## Docker Deployment

### Build Docker Image

```bash
docker build -t doctor-appointment-scheduler:latest -f Docker .
```

### Run Docker Container

```bash
docker run -p 8080:8080 doctor-appointment-scheduler:latest
```

## Kubernetes Deployment

### Prerequisites

- EKS cluster running
- kubectl configured
- Docker image pushed to ECR

### Deploy to EKS

```bash
kubectl apply -f K8-manifest.yml
```

### Verify Deployment

```bash
kubectl rollout status deployment/appointment-app
kubectl get pods -n default
kubectl get svc -n default
```

## Development tips

- Use your IDE (IntelliJ IDEA, Eclipse, VS Code) to import as a Maven project.
- Enable "auto-build" or run the application in debug mode for iterative development.
- Thymeleaf templates are under resources/templates — edit HTML files and refresh the browser to view changes.
- Run tests with `mvn test`

## Tests

If the project contains unit/integration tests, run them with:

```bash
mvn test
```

## Contributing

Contributions, bug reports, and improvements are welcome. To contribute:

1. Fork the repository
2. Create a new branch (feature/bugfix)
3. Make changes and add tests where appropriate
4. Commit and push, then open a pull request describing your changes

## License

Specify the license for the project here (e.g., MIT, Apache-2.0). If you don't have a LICENSE file yet, add one to clarify terms for contributors and users.

## Contact / Support

For questions about running or developing the application, open an issue in the repository with a clear description of the problem and steps to reproduce.
