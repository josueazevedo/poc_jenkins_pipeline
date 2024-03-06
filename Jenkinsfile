
pipeline {
    agent {
        docker {
            image 'node:18-alpine' // Imagem Docker com Node.js
            args '-u root' // Executar com permissões de root
        }
    }
    stages {
        stage('Testes Unitários') {
            steps {
                sh 'npm install' // Instalar dependências
                sh 'npm test'    // Executar testes unitários
            }
        }
        stage('Avaliação do Sonar') {
            steps {
                // Execute o scanner do SonarQube
                withSonarQubeEnv('http://localhost:9000') {
                    sh 'npm install -g sonarqube-scanner' // Instale o scanner do SonarQube
                    sh 'sonar-scanner' // Execute o scanner
                }
            }
        }
        stage('Build e Geração da Imagem Docker') {
            steps {
                script {
                    def version = sh(script: 'node -pe "require(\'./package.json\').version"', returnStdout: true).trim()
                    sh 'npm install'         // Instalar dependências
                    sh 'npm run build'       // Realizar o build da aplicação
                    // Construir a imagem Docker da aplicação com a versão do package.json
                    docker.build("sua_aplicacao:${version}")
                    docker.tag("sua_aplicacao:${version}", "sua_aplicacao:latest") // Adicionar uma tag 'latest'
                }
            }
        }
        // stage('Subir Imagem para Docker Hub') {
        //     steps {
        //         withCredentials([usernamePassword(credentialsId: 'ID_DOCKERHUB', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
        //             // Fazer login no Docker Hub
        //             sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
        //             // Enviar a imagem para o Docker Hub
        //             sh 'docker push sua_aplicacao:${version}'
        //             sh 'docker push sua_aplicacao:latest'
        //         }
        //     }
        // }
    }
}
