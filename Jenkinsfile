pipeline{
    agent any
    
    environment{
        APP_VERSION="1.0.${env.BUILD_NUMBER}"
        IMAGE_NAME = "cjson-lab6"
    }
    
    stages{
        stage('Clone'){
        steps{
            echo "debug clone \n"
            deleteDir()
                git branch: 'master', url: 'https://github.com/Nbartek/cJSON.git'
        
        }
        }
        
        stage('Build i Testy'){
         steps{
             echo "debug build/test \n"
            //--target deploy, wymusza przejście przez etapy Build i Test
            sh 'docker build --target deploy -t ${IMAGE_NAME}:${APP_VERSION} -f Dockerfile.ci .'
         }
        }
        
        stage('Deploy'){
        steps{
            echo "debug deply \n"
            sh 'docker run --rm ${IMAGE_NAME}:${APP_VERSION}'
        }
        }
        stage('Publikacja'){
            steps{
                echo "debug publi \n"
                // Wyciągamy pliki z obrazu Dockera na zewnątrz do środowiska Jenkinsa
                sh '''
                    docker create --name extractor ${IMAGE_NAME}:${APP_VERSION}
                    docker cp extractor:/app/lib/. ./
                    docker rm extractor
                    
                    # Pakowanie do archiwum .tar.gz (Nasz oficjalny artefakt)
                    tar -czvf cJSON-lib-${APP_VERSION}.tar.gz *.o *.so* *.h
                '''
                
                // archiwizacja w Jenkinsie
                archiveArtifacts artifacts: '*.tar.gz', fingerprint: true
            }
        }
    }
    
        post {
        always {
            echo "Czyszczenie środ."
            sh 'docker image prune -f'
        }
    }
    
}
