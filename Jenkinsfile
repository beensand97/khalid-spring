pipeline {

    agent any
    // options { 
    //     buildDiscarder logRotator(daysToKeepStr: '1', numToKeepStr: '4')
    // }

    tools { 
        maven 'my-maven' 
    }
    // environment {
    //     DOCKERHUB_CREDENTIALS=credentials('dockerhub')
    // }
    stages {
        // stage('Scan & Review with Sonar') {
        //     steps {
        //         withSonarQubeEnv(installationName: 'my-sonar') {
        //             sh 'mvn clean package -Dmaven.test.failure.ignore=true sonar:sonar '
        //         }
        //     }
        // }
        stage('Build with Maven') {
            steps {
                sh 'mvn --version'
                sh 'java -version'
                sh 'mvn clean package -Dmaven.test.failure.ignore=true'
                stash includes : 'target/*.jar', name: 'app'
            }
        }

        stage('Package with Docker') {

            steps {
                unstash 'app' 
                sh 'ls -la'
                sh 'ls -la target'
                sh 'docker build -t khaliddinh/spring-mysql .'
                sh 'docker network create test || echo "network exists" '
                sh 'docker container run -d --name khalid-java --network test -p 8080:8080 khaliddinh/spring-mysql'
            }
        }
        // stage('Pushing  to DockerHub') {
        //     steps {
        //         echo 'Start pushing.. with credential'
        //         sh 'echo $DOCKERHUB_CREDENTIALS'
        //         sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
        //         sh 'docker push khaliddinh/springboot:1.0'
                
        //     }
        // }
        // stage('Deploy to QA server') {
        //     steps {
        //         echo 'Deploying and cleaning'
        //         sh 'docker  rm khaliddinh/springboot:1.0 || echo "this  does not exist" '
        //         sh 'docker container stop my-demo-springboot || echo "this container does not exist" '
        //         sh 'docker network create jenkins || echo "this network exists"'
        //         sh 'echo y | docker container prune '
        //         sh 'echo y | docker image prune'
        //         sh 'docker container run -d --rm --name my-demo-springboot -p 8082:8080 --network jenkins khaliddinh/springboot:1.0'
        //     }
        // }
    }
}
