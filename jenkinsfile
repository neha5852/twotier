pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh '''
                    oc get bc cart-fe
                    if [ $? -ne 0 ]
                    then
                       oc new-build php~https://github.com/mohanraz81/twotier.git --context-dir='src' --env MY_DB_HOST_WRITE=mysql-0.mysql MY_DB_HOST_READ=mysql  MY_DB_NAME=mydatadb  MY_DB_USER=mysqluser  MY_DB_PASS=password --name cart-fe
                    else
                       oc start-build cart-fe
                    fi 
                    sleep 10
                    oc logs bc/cart-fe

                '''
            }
        }
        stage('deploydatabase') {
            steps {
                sh '''
                    oc apply -f database
                    sleep 20
                    oc get pods -n mysql-0 |grep -i running
                    if [ $? -eq 0 ]
                    then
                       echo "Deployment of Mysql is success"
                    else
                        echo "deployment if failure"
                        exit 10
                    fi
                '''
            }
        }
        stage('deployfrontend') {
            steps {
                sh '''
                    oc apply -f frontend
                    sleep 20
                    oc get pods -n mycart |grep -i running
                    if [ $? -eq 0 ]
                    then
                       echo "Deployment of frontend is success"
                    else
                        echo "deployment if failure"
                        exit 10
                    fi
                '''
            }
        }
    }
}
