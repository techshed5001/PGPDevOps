pipeline{
    environment {
    registry = "clong1969/abctech"
    registryCredential = 'dockerhub'
    }
    tools{
        maven 'mymaven'
    }
    agent any
    stages{
        stage ('CloneRepo')
        {
            steps{        
              git (branch: 'main', url:'https://github.com/clong1969/PGPDevOps.git')   
            }
        }
        stage ('Compile')
        {
            steps{
             sh  'mvn compile' 
            }
        }
        stage ('Test')
        {
            steps{     
             sh  'mvn test'
            }
        post{
            success{
                junit 'target/surefire-reports/*.xml'
            }
        }
        }
        stage('Package')
        {
            steps{
                sh 'mvn package'
            }
        }
        stage('Build Image')
        {
            steps{
            sh 'cp /var/lib/jenkins/workspace/CIPIPELINE/target/ABCtechnologies-1.0.war .'
            script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                    }
            }
        }
        stage('Deploy Image')
        {
            steps{
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                    dockerImage.run()
                    docker.withRegistry( '', registryCredential ) {
                    dockerImage.push()}
                    }
            }
        }
        stage('Deploying to Kubernetes') 
        {
            steps {
                script {
                    kubernetesDeploy(configs: "deployment.yaml", 
                                         "service.yaml")
                    }
            }
    
        }
    }
}