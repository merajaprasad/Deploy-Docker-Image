
# Docker Project
Deploying custom Docker images and push to DockerHub using jenkins Declarative pipeline.
# Deployment
### Dockerfile
```

FROM httpd:latest
COPY index.html /usr/local/apache2/htdocs/
EXPOSE 80

```
### Jenkins pipeline
```
pipeline {
    agent { label "rptech-dev-app" }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('merajaprasd-dockerhub')
        DOCKERHUB_USERNAME = 'merajaprasd'
        DOCKERHUB_PASSWORD = 'dckr_pat_g1o9ozdy67YnGct7SSB5E1HIxbI'
    }

    stages {
        stage("git checkout") {
            steps {
                checkout scm
            }
        }
        
        stage("Build image") {
            steps{
                sh "docker build -t merajaprasd/dev-server:$BUILD_NUMBER ."
            }
        }

        stage('DockerHub Login') {
            steps {
                sh "docker login -u ${DOCKERHUB_USERNAME} -p ${DOCKERHUB_PASSWORD}"
            }
        }
  

        stage("push images") {
            steps {
                sh "docker push merajaprasd/dev-server:$BUILD_NUMBER"
            }
        }

    }
    
post {
        always {
            sh 'docker rm -f dev-server'
        }
    }
}

```
