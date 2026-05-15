# DevSecOps Pipeline Roadmap

## Current Source Of Truth

Jenkins is the pipeline owner. The current setup uses a Freestyle job that checks out the repo, runs Maven, and then runs the standalone SonarQube Scanner.

This keeps operational configuration in Jenkins while keeping scanner configuration in version control through `sonar-project.properties`.

## Baseline Gates

The current base should gate changes with:

- Maven compile and unit tests
- SonarQube static analysis
- optional SonarQube quality gate after the baseline is stable

## Credentials

Use Jenkins credentials instead of checked-in values:

- SonarQube token: configured in Jenkins SonarQube server settings
- Future Snyk token: secret text credential named `snyk-token`
- Runtime app/database credentials: environment variables in deployment, not source control

## Snyk Phase

Add Snyk after the Sonar baseline is stable.

Recommended first pass:

```sh
snyk test --file=pom.xml --package-manager=maven --severity-threshold=high
```

Start non-blocking or manually triggered until the findings are triaged. After that, make high/critical dependency findings blocking on protected branches.

## GitHub Actions Phase

Use GitHub Actions later only for quick feedback:

- PR compile/test
- optional lightweight Sonar or dependency checks
- no duplicate release/deployment logic while Jenkins owns the main pipeline

The Action should reuse the same Maven command as Jenkins:

```sh
mvn -B -ntp -f billing/pom.xml clean verify
```

## Next Security Layers

Once Snyk is active, add these in order:

1. Secret scanning before build
2. Container image build
3. Image scanning with Trivy or Snyk Container
4. SBOM generation
5. Deployment environment promotion gates
6. DAST against a deployed test environment
