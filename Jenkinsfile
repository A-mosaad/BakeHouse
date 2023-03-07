pipeline {
    agent { label 'iti-aswan' }
    parameters {
        choice(name: 'ENV', choices: ['dev', 'test', 'prod',"release"])
    } 
    stages {
        stage('build') {
            steps {
                echo 'build'
                script {
                    if (params.ENV == "release") {
                        withCredentials([usernamePassword(credentialsId: 'iti-aswan-dockerhub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                            sh """
                                docker login -u ${USERNAME}  -p ${PASSWORD}
                                docker build -t ${USERNAME}/iti-aswan-bakehouse:${BUILD_NUMBER} .
                                docker push ${USERNAME}/iti-aswan-bakehouse:${BUILD_NUMBER}
                                echo ${BUILD_NUMBER} > ../bakehouse-build-number.txt
                            """
                        }
                    }
                }
            }
        }
        stage('deploy') {
            steps {
                echo 'deploy'
                script {
                    if (params.ENV == "dev" || params.ENV == "test"  || params.ENV == "prod"  ) {
                        withCredentials([file(credentialsId: 'iti-aswan-kubeconfig', variable: 'KUBECONFIG')]) {
                            sh """
                                export BUILD_NUMBER=\$(cat ../bakehouse-build-number.txt)
                                mv Deployment/deploy.yaml Deployment/deploy.yaml.tmp
                                cat Deployment/deploy.yaml.tmp | envsubst > Deployment/deploy.yaml
                                rm -f Deployment/deploy.yaml.tmp
                                kubectl apply -f Deployment --kubeconfig ${KUBECONFIG}
                            """
                        }
                    }
                }
            }
        }
    }
}
