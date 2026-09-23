pipeline {
    agent any

    // Poll GitHub every 5 minutes; a new commit triggers a build
    triggers {
        pollSCM('H/5 * * * *')
    }

    environment {
        APP_NAME       = 'finsecure-connect'
        STAGING_SERVER = 'ec2-staging-au-southeast-2'
        PROD_SERVER    = 'ec2-prod-au-southeast-2'
    }

    stages {

        stage('Build') {
            steps {
                echo "Task: compile the ${env.APP_NAME} source and package it into a deployable artefact."
                echo 'Tool: Maven. It resolves dependencies, compiles, and produces a versioned JAR/WAR from a single pom.xml.'
            }
        }

        stage('Unit and Integration Tests') {
            steps {
                echo 'Task: run unit tests to verify individual components, then integration tests to verify they work together.'
                echo 'Tools: JUnit 5 for unit tests, and REST Assured for API-level integration tests.'
            }
        }

        stage('Code Analysis') {
            steps {
                echo 'Task: analyse the source for maintainability issues, code smells, duplication and coverage against industry standards.'
                echo 'Tool: SonarQube. It enforces a quality gate that can fail the build when new code drops below threshold.'
            }
        }

        stage('Security Scan') {
            steps {
                echo 'Task: scan source and dependencies for known vulnerabilities before anything is deployed.'
                echo 'Tool: OWASP Dependency-Check, cross-referencing declared dependencies against the National Vulnerability Database.'
            }
        }

        stage('Deploy to Staging') {
            steps {
                echo "Task: deploy the built artefact to the staging server ${env.STAGING_SERVER}."
                echo 'Tool: AWS CLI driving CodeDeploy onto an EC2 instance, so the deployment is repeatable and version-tracked.'
            }
        }

        stage('Integration Tests on Staging') {
            steps {
                echo 'Task: exercise the deployed application in a production-like environment to catch issues that only appear once it is running.'
                echo 'Tool: Selenium WebDriver for end-to-end browser flows, with Newman running the Postman API collection.'
            }
        }

        stage('Deploy to Production') {
            steps {
                echo "Task: promote the validated build to the production server ${env.PROD_SERVER}."
                echo 'Tool: AWS CodeDeploy using a blue/green deployment so traffic shifts only after health checks pass.'
            }
        }
    }

    post {
        success {
            echo "SUCCESS: ${env.APP_NAME} passed all seven stages and was promoted to ${env.PROD_SERVER}."
        }
        failure {
            echo 'FAILURE: the pipeline stopped before production. No release was made.'
        }
    }
}
