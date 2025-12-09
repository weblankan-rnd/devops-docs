# Jenkins

## Overview

Jenkins is an open-source automation platform for continuous integration and continuous delivery.

## Key Concepts

**Pipeline**: Defined set of automated steps

**Job**: Individual task or build

**Stage**: Logical grouping of steps

**Agent**: Execution environment

**Trigger**: Event that starts pipeline

## Pipeline Structure

```groovy
pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/example/repo.git'
            }
        }
        
        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }
        
        stage('Test') {
            steps {
                sh 'npm test'
            }
        }
        
        stage('Deploy') {
            steps {
                sh 'npm run deploy'
            }
        }
    }
    
    post {
        always {
            junit 'test-results.xml'
            publishHTML target: [reportDir: 'coverage', reportFiles: 'index.html']
        }
        failure {
            emailext to: 'team@example.com', subject: 'Build Failed'
        }
    }
}
```

## Common Plugins

- **Git** - Version control integration
- **Docker** - Container builds and deployment
- **Kubernetes** - K8s deployment
- **Email** - Email notifications
- **Slack** - Slack integration
- **CloudBees** - Enterprise features

## Best Practices

1. **Version control** - Store all configurations
2. **Declarative pipelines** - Use declarative syntax
3. **Shared libraries** - Reusable pipeline code
4. **Secrets management** - Use credential store
5. **Parallel execution** - Speed up builds
6. **Docker agents** - Isolated build environments

## Common Commands

```bash
# Start Jenkins
java -jar jenkins.war

# Execute job
curl -X POST http://localhost:8080/job/jobname/build

# Get build log
curl http://localhost:8080/job/jobname/1/consoleText

# Reload configuration
curl -X POST http://localhost:8080/reload
```

## Scaling

- **Master/Agent architecture** - Distribute load
- **Multiple agents** - Parallel job execution
- **Resource limits** - Prevent overload
- **Cloud integration** - Dynamic agent provisioning

