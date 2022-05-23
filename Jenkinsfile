pipeline {
    agent any
    environment {
    phpImage = ""
    nginxImage = ""
    registryCredential = 'dockerhub'
    test_env = "test"
    prod_env = "prod"
    php_registry = "registry.hub.docker.com/rakeshpb90/php-application"
    php_image_name =  "${php_registry}:${env.BUILD_NUMBER}"
    nginx_registry = "registry.hub.docker.com/rakeshpb90/php-application"
    nginx_image_name =  "${nginx_registry}:${env.BUILD_NUMBER}"
    }  

    stages {
        stage ("SCM checkout") {
            steps {
                git "https://github.com/reshma93new/projCert"
                
            }
        }
        stage('Building PHP Docker Image') {
            steps{
                script {
                phpImage = docker.build("rakeshpb90/php-application", "./php/")
                }
            }
        }

        stage('Building Nginx Docker Image') {
            steps{
                script {
                nginxImage = docker.build("rakeshpb90/nginx", "./nginx/")
                }
            }
        }

        stage('Push Docker Image') { 
            steps{
                script {
                    docker.withRegistry('https://registry.hub.docker.com', registryCredential) {
                    phpImage.push("${env.BUILD_NUMBER}")
                    nginxImage.push("${env.BUILD_NUMBER}")

                    }
                }
            }
        }
        stage("Deploy Test Server") {
           steps {
                ansiblePlaybook colorized: true, credentialsId: 'ubuntu', disableHostKeyChecking: true, installation: 'Default', inventory: 'ansible-setup/inventory/dynamic.aws_ec2.yml',extras: '-e host_group=\"tag_Env_${test_env}\" -e php_docker_image_name=\"${php_image_name}\" -e nginx_docker_image_name=\"${nginx_image_name}\"', playbook: 'ansible-setup/playbook/php-deploy.yaml'
            }    
        }    
    }
}