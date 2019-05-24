pipeline {
     agent any
     environment {
         PATH = "/Users/njiratti/Documents/course-ci-cd-with-jenkins-master/apache-maven-3.6.0/bin:${env.PATH}"
         JAVA_HOME = "/Library/Java/JavaVirtualMachines/jdk1.8.0_212.jdk/Contents/Home/"
     }
     stages {
       stage('Pullcode') {
         steps {
             git 'https://github.com/up1/workshop-java-web-tdd.git'
         }
       }
       stage('Testing') {
          steps {
            sh "mvn clean test"
            junit 'target/surefire-reports/*.xml'
          }
        }
        stage('Package') {
          steps { 
            sh "mvn package"
          }
        }
        stage('Code coverage') {
          steps {
             cobertura autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: '**/target/site/cobertura/coverage.xml', failUnhealthy: false, failUnstable: false
          }
        }
        stage('SonarQube analysis') {
          steps {
            // requires SonarQube Scanner for Maven 3.2+
            sh 'mvn sonar:sonar'
          }
        }
        stage('UI-Test') {
          steps { 
            sh "robot atdd/*.robot"
          }
        }
      }
      post {
        always {
            junit 'target/surefire-reports/*.xml'
        }
      }
}
