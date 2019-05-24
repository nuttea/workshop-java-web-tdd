pipeline {
  agent any
  environment {
    PATH = "/Users/njiratti/Documents/course-ci-cd-with-jenkins-master/apache-maven-3.6.0/bin:${env.PATH}"
    JAVA_HOME = "/Library/Java/JavaVirtualMachines/jdk1.8.0_212.jdk/Contents/Home/"
  }
  stages {
    stage('Pullcode') {
      steps {
        git 'https://github.com/nuttea/workshop-java-web-tdd.git'
      }
    }
    stage('Test and Build') {
      steps {
        sh "mvn clean package"
        junit 'target/surefire-reports/*.xml'
      }
    }
    stage('Code coverage') {
      steps {
        cobertura autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: '**/target/site/cobertura/coverage.xml', failUnhealthy: false, failUnstable: false
      }
    }
    stage('SonarQube analysis') {
      steps {
        sh 'mvn sonar:sonar'
      }
    }
    stage('Deploy to Tomcat') {
      steps { 
        build 'deploy_pipeline'
      }
    }
    stage('UI Test') {
      steps {
        parallel Chrome: { 
          stage('Chrome') {
            steps {
              sh "robot -v BROWSER:chrome atdd/*.robot"
              step([$class: 'RobotPublisher', disableArchiveOutput: false, enableCache: true, logFileName: 'log.html', onlyCritical: true, otherFiles: '', outputFileName: 'output.xml', outputPath: '', passThreshold: 95.0, reportFileName: 'report.html', unstableThreshold: 90.0])
            }
          }
        },
        Firefox: {
          stage('Firefox') {
            steps {
              sh "robot -v BROWSER:ff -T atdd/*.robot"
              step([$class: 'RobotPublisher', disableArchiveOutput: false, enableCache: true, logFileName: 'log.html', onlyCritical: true, otherFiles: '', outputFileName: 'output.xml', outputPath: '', passThreshold: 95.0, reportFileName: 'report.html', unstableThreshold: 90.0])
            }
          }
        }
      }
    }
  }
  post {
    always {
      junit 'target/surefire-reports/*.xml'
    }
  }
}