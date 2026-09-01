# Doctor-Appointment-Scheduler

A Spring Boot web application that allows patients to register, log in, and schedule appointments with doctors. Doctors can register, log in, view and cancel appointments. The project uses Thymeleaf for server-side templating and an in-memory H2 database for development.

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

## Technologies

- Java (Spring Boot)
- Spring MVC (Controller / Service / Repository layers)
- Spring Data JPA
- Thymeleaf templates (server-side HTML)
- H2 in-memory database (development)
- Maven build system

## Prerequisites

- Java JDK 11 or later installed and JAVA_HOME configured
- Maven 3.6+ installed
- Git (to clone the repo)

## Getting started (local development)

1. Clone the repository

   git clone https://github.com/harikrishnan-creator/Doctor-Appointment-Scheduler.git
   cd Doctor-Appointment-Scheduler

2. Build the project

   mvn clean package

   or to run directly from Maven:

   mvn spring-boot:run

3. Run the packaged JAR

   After a successful build you can run the Spring Boot application with:

   java -jar target/*.jar

4. Open the application in your browser

   By default Spring Boot runs on port 8080. Open:

   http://localhost:8080

   Use the provided pages to register as a patient or doctor and to log in.

## H2 Console (in-memory DB)

While the application is running, you can access the H2 console to inspect the in-memory database (useful during development):

- URL: http://localhost:8080/h2-console
- JDBC URL (default for Spring Boot/H2 dev): jdbc:h2:mem:testdb
- Username: sa
- Password: (leave blank)

Note: If your application.properties configures a different URL or credentials, use those values instead.

## Application Structure (overview)

- src/main/java - Java source code (controllers, services, repositories, models)
- src/main/resources/templates - Thymeleaf HTML templates
- src/main/resources/static - static assets (CSS, JS, images)
- src/main/resources/application.properties - Spring Boot configuration (ports, datasource, JPA settings)

Typical domain model includes entities for:
- Patient (registration info, contact details)
- Doctor (profile, specialization)
- Appointment (patient, doctor, date/time, status)

Repositories use Spring Data JPA to persist these entities into the H2 database during development.

## How to use the app (typical user flow)

1. Register
   - Patient: Fill the patient registration form and submit.
   - Doctor: Fill the doctor registration form and submit.
2. Login
   - After registration, use the login page to authenticate as patient or doctor.
3. Book appointment (patient)
   - Once logged in, navigate to the appointment booking page, select a doctor, choose date/time, and submit.
4. Manage appointments
   - Patients: View/cancel their appointments from the dashboard.
   - Doctors: View appointments scheduled with them and cancel if necessary.

If the UI has labeled links or navigation, follow those; otherwise check controllers and Thymeleaf templates to find the exact endpoints.

## Configuration

- To change port, database settings, or other Spring Boot properties, edit src/main/resources/application.properties or provide environment variables when running the app.
- Example change to run on port 9090 in application.properties:

  server.port=9090

## Development tips

- Use your IDE (IntelliJ IDEA, Eclipse, VS Code) to import as a Maven project.
- Enable "auto-build" or run the application in debug mode for iterative development.
- Thymeleaf templates are under resources/templates — edit HTML files and refresh the browser to view changes.

## Tests

If the project contains unit/integration tests, run them with:

  mvn test

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
