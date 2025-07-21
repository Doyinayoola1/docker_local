pipeline{
  agent any
    //{label 'docker-agent'
 // }
  //{label 'linux && x86'}
  tools {
    maven 'maven1'
  }
  triggers {
        githubPush()
    }
  stages {
    stage('Git Clone'){
      steps{
        git branch: 'main', url: 'https://github.com/Doyinayoola1/docker_local.git'
      }
    }
   
    stage('Packaging artifact'){
      steps{
        echo 'building package with maven '
        sh 'rm -rf ~/.m2/repository/ch/qos/logback'
        sh 'mvn clean install'
        sh 'mvn clean package'
      }
    }
    stage('Test artifact with sonarqube'){
      steps{
        echo "Analysing with Sonar"
        //sh 'mvn dependency:go-offline'
        sh 'mvn dependency:tree -Dincludes=ch.qos.logback'
        //sh 'echo mvn sonar:sonar -Dsonar.qualitygate.wait=true'
        withSonarQubeEnv('SonarQubeServer') {
          withCredentials([string(credentialsId: 'local-sonarqube', variable: 'sonarlog')]) {
            sh '''
              mvn sonar:sonar \
                -Dsonar.projectKey=jenkins-project \
                -Dsonar.projectName=jenkins-project \
                -Dsonar.host.url=http://localhost:9000 \
                -Dsonar.login=$sonarlog \
                -Dsonar.scanner.dontLoadExternalDependencies=true \
                -Dlogback.configurationFile=logback.xml \
                -Dsonar.verbose=true
        '''
          }
        }
      }
    }
    stage('Quality Gate'){
      steps{
        echo "Waiting for SonarQube Quality Gate"
        timeout(time: 1, unit: 'HOURS') {
          waitForQualityGate abortPipeline: true
        }
      }
    }
    stage('Build containers'){
      steps{
        echo "building container"
        sh 'docker build -t tomcat-jenk .'
      }
    }
    stage('Initializing container'){
      steps{
        sh 'echo waiting for all container services to be up'
        sh 'sleep 10'
      }
    }
    stage('Deploy to nexus'){
      steps{
        echo "deploy to nexus repo"
        //sh 'mvn deploy -s ./settings.xml'
      }
    }
   // stage('Tomcat'){
   //   steps {
   //    echo 'Pushing to Tomcat'
   //      //deploy adapters: [tomcat9(credentialsId: 'tomcat-login', path: '', url: 'http://172.17.0.1:8090/')], contextPath: null, war: '**/target/*war'
   //       echo 'Done finally finally'
   //   }
   //  }
  }
}
