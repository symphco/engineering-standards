# SonarQube

SonarQube is an open-source platform developed by SonarSource for continuous inspection of code quality to perform automatic reviews with static analysis of code to detect bugs, code smells, and security vulnerabilities.

## How SonarQube works

SonarQube provides a detailed report of bugs, code smells, and security vulnerabilities. It also provides a way to manage the health of applications, as well as facilitating the visualization of this information within a project.

Common parts of a SonarQube analysis:

- **Project**: A project is the unit of work in SonarQube. It's a set of files analyzed together.

- **Issues**: Issues (bugs, vulnerabilities, code smells) are what SonarQube raises when the code doesn't comply with a coding rule.

- **Rules**: Rules are the coding standards that SonarQube will enforce on your project.

## How to analyze a project with SonarQube

- Install and start the SonarQube server.

- Install and configure SonarScanner according to your build system/requirements.

- Run the analysis with the appropriate SonarScanner command.

- View your analyzed project in the SonarQube web interface.

![Guide](./path_to_your_image.gif)

Here's a sample command to run the analysis:

```bash
sonar-scanner -Dsonar.projectKey=my_project -Dsonar.sources=. -Dsonar.host.url=http://localhost:9000 -Dsonar.login=admin -Dsonar.password=admin
```

By analyzing your projects with SonarQube, you can ensure that your code adheres to certain quality standards, and you can catch and fix issues before they become problems.