pipeline{
  agent {label 'linux && x86'}
  tools {
    maven 'maven_1'
  }
  stages {
    stage('Git Clone'){
      steps{
        git branch: 'main', url: 'https://github.com/Doyinayoola1/docker_local.git'
      }
    }
    // stage('Build containers'){
    //   steps{
    //     sh 'docker-compose up -d'
    //   }
    // }
    // stage('Initializing container'){
    //   steps{
    //     sh 'echo waiting for all container services to be up'
    //     sh 'sleep 60'
    //   }
    // }
    stage('Execute maven package'){
      steps{
        echo 'package artifact fully'
        //sh 'mvn clean package'
      }
    }
    stage('Test artifact with sonar'){
      steps{
        echo "Analysing with Sonar"
        //sh 'echo mvn sonar:sonar -Dsonar.qoalitygait.wait=true'
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
