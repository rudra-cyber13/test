pipeline {
    agent any
    environment {
        PATH = "/usr/bin:${env.PATH}"
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/rudra-cyber13/test.git', branch: 'main'
            }
        }
        stage('Build') {
            steps {
                echo 'Building application...'
                sh 'mvn clean package'
            }
        }
        stage('Sign') {
            steps {
                echo 'Signing application...'
                withCredentials([
                    file(credentialsId: 'code_signing_key', variable: 'KEY_PATH'),
                    file(credentialsId: 'code_signing_cert', variable: 'CERT_PATH')
                ]) {
                    sh '''
                    # Convert key and cert to a format OpenSSL understands (PKCS#12)
                    openssl pkcs12 -export -inkey $KEY_PATH -in $CERT_PATH -out signing.p12 -password pass:mysecurepassword
                    
                    # Use OpenSSL to sign the JAR file
                    openssl dgst -sha256 -sign $KEY_PATH -out target/myapp.sig target/myapp.jar
                    '''
                }
            }
        }
        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'mvn test'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying signed application...'
                sh 'scp target/myapp.jar target/myapp.sig kali@192.168.234.129:/opt/deploy/'
            }
        }
    }
}
