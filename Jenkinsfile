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
    // stage('Test artifact with sonarqube'){
    //   steps{
    //     echo "Analysing with Sonar"
    //     //sh 'mvn dependency:go-offline'
    //     sh 'mvn dependency:tree -Dincludes=ch.qos.logback'
    //     //sh 'echo mvn sonar:sonar -Dsonar.qualitygate.wait=true'
    //     withSonarQubeEnv('SonarQubeServer') {
    //       withCredentials([string(credentialsId: 'local-sonarqube', variable: 'sonarlog')]) {
    //         sh '''
    //           mvn sonar:sonar \
    //             -Dsonar.projectKey=jenkins-project \
    //             -Dsonar.projectName=jenkins-project \
    //             -Dsonar.host.url=http://localhost:9000 \
    //             -Dsonar.login=$sonarlog \
    //             -Dsonar.scanner.dontLoadExternalDependencies=true \
    //             -Dlogback.configurationFile=logback.xml \
    //             -Dsonar.verbose=true
    //     '''
    //       }
    //     }
    //   }
    // }
    // stage('Quality Gate'){
    //   steps{
    //     echo "Waiting for SonarQube Quality Gate"
    //     timeout(time: 1, unit: 'HOURS') {
    //       waitForQualityGate abortPipeline: true
    //     }
    //   }
    // }
    stage('Check for Dockerfile changes') {
      steps {
        script {
          def changes = sh(script: 'git diff --name-only HEAD~1 HEAD | grep -E "^src/|^Dockerfile$"', returnStatus: true) == 0
          if (changes) {
            echo 'Dockerfile was changed will create a new build'
            env.PUSH_IMAGE = 'TRUE'
            return
          }
          else {
            echo 'Dockerfile was not changed, skipping build'
            env.PUSH_IMAGE = 'FALSE'
            return
          }
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
   
    stage('Push to Docker Hub'){
      when {
        expression { env.PUSH_IMAGE == 'TRUE' }
      }
      steps{
        withCredentials([usernamePassword(credentialsId: 'Docker-login', passwordVariable: 'Docker_pass', usernameVariable: 'Docker_user')]) {
          sh '''
            mkdir -p tmp_docker_home
            export DOCKER_CONFIG=$PWD/tmp_docker_home
            echo 'Pushing to Docker Hub'
            set -e
            echo "$Docker_pass" | docker login -u "$Docker_user" --password-stdin
            docker tag tomcat-jenk doyinayoola1/tomcat-jenk:build-${BUILD_ID}
            docker push doyinayoola1/tomcat-jenk:build-${BUILD_ID}
            echo 'Pushed to Docker Hub successfully'
            docker logout
            rm -rf tmp_docker_home
          '''
        }
      }
    }
  }
  post {
    success {
        emailext (attachLog: true, subject:'', body:'', to: 'unmask3230@gmail.com' )
    }
    failure {
        emailext (attachLog: true, subject:'', body:'', to: 'unmask3230@gmail.com')
    }
  }
  // add none default messages
  // post {
  //   success {
  //       emailext (attachLog: true, body: """Dear Developer,
  //       Build number ${env.BUILD_ID} was completed Successfully

  //       You can view details at: ${env.BUILD_URL}

  //       Thank you.
  //       Admin""", subject: "Build number ${env.BUILD_ID} Successful", to: 'unmask3230@gmail.com' )
  //   }
  //   failure {
  //       emailext (attachLog: true, body: """Dear Developer,

  //       Build number ${env.BUILD_ID} was completed Successfully

  //       You can view details at: ${env.BUILD_URL}

  //       Thank you.
  //       Admin""", subject: "Build number ${env.BUILD_ID} Successful", to: 'unmask3230@gmail.com')
  //   }
  // }
      

}
