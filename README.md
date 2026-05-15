# DevSecOps Pipeline Base

This repository is a Jenkins-first DevSecOps lab for the `billing` Spring Boot service.

The current baseline provides:

- Jenkins Freestyle job support
- Maven build and test execution
- SonarQube analysis and quality gate support
- Local Jenkins + SonarQube platform through Docker Compose
- A simple path to add Snyk later

## Local Platform

Optional: create a local environment file if you want to change ports or local passwords:

```sh
cp .env.example .env
```

Then start Jenkins and SonarQube:

```sh
docker compose up -d --build
```

Services:

- Jenkins: http://localhost:8082
- SonarQube: http://localhost:9002

If those ports are already used, override them when starting the stack:

```sh
JENKINS_HOST_PORT=8090 SONARQUBE_HOST_PORT=9003 docker compose up -d --build
```

## Jenkins Setup

Configure Jenkins with:

- A Freestyle job connected to the GitHub repository
- SonarQube server name: `sonarqube`
- SonarQube token credential attached to that server
- SonarQube webhook: `http://<jenkins-host>/sonarqube-webhook/`

The provided Jenkins image installs Maven and the required plugins.

## Freestyle Build

Use this Maven build step:

```txt
Root POM: billing/pom.xml
Goals: -B -ntp clean verify
```

Then add an `Execute SonarQube Scanner` step:

```txt
Path to project properties: sonar-project.properties
```

## Future Snyk Setup

When you are ready to add Snyk:

1. Install the Snyk CLI in the Jenkins agent image or through a Jenkins tool step.
2. Add a Jenkins secret text credential named `snyk-token`.
3. Run the pipeline with `RUN_SNYK=true`.
4. Start with `--severity-threshold=high`, tune ignore policies, then decide whether Snyk should block every branch or only protected branches.

## Optional GitHub Actions Later

GitHub Actions or a self-hosted runner can be added later for fast PR feedback. Keep Jenkins as the release/security gate and reuse the same command:

```sh
mvn -B -ntp -f billing/pom.xml clean verify
```

That avoids maintaining two different pipeline definitions with different behavior.
