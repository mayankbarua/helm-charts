/*
    This is an example pipeline that implement full CI/CD for a simple static web site packed in a Docker image.
    The pipeline is made up of 6 main steps
    1. Git clone and setup
    2. Build and local tests
    3. Publish Docker and Helm
    4. Deploy to dev and test
    5. Deploy to staging and test
    6. Optionally deploy to production and test
 */

/*
    Create the kubernetes namespace
 */
def createNamespace (name) {
    echo "Creating namespace ${name} if needed"

    sh "kubectl create namespace ${name} --dry-run -o yaml | kubectl apply -f -"
}

/*
    Helm install Backend application
*/
def helmInstallBackend () {
    echo "Installing backend application"

    script {
        backendReleaseName = "backend"
       // sh "helm repo add helm ${HELM_REPO}; helm repo update"
       sh "helm upgrade --install ${backendReleaseName} --namespace=api --set configmap.RDS_CONNECTION_STRING=${env.rdsString},configmap.S3_BUCKET_NAME=${env.S3BucketName},secret.awscred.aws_key=${env.awsKey},secret.awscred.secret_key=${env.awsSecret},secret.regcred.dockerconfigjson=${env.dockerString},redis.sentinel.enabled=${env.redisSentinel},redis.cluster.enabled=${env.redisCluster},redis.password=${env.redisPassword},secret.redis.password=${env.redisPassword},configmap.REDIS_SENTINEL_HOSTNAME=${backendReleaseName}-redis.api.svc.cluster.local --debug ./back-end/"
       sh "sleep 50"
       echo "Finding backendip"
       backendIp = sh(returnStdout: true, script: "kubectl describe services backend-back-end --namespace=api | grep elb.amazonaws.com | grep LoadBalancer | awk '{print \$3}' | tr -d '\n'")
       echo "${backendIp}"
    }
}

/*
    Helm install Frontend application
*/
def helmInstallFrontend () {
    echo "Installing frontend application"

    script {
       frontendReleaseName = "frontend"
       sh "helm upgrade --install ${frontendReleaseName} --namespace=ui --set intiContainer.backendDependencyEndpoint=${backendReleaseName}-back-end.api.svc.cluster.local,configmap.backendIp='http://${backendIp}:3000',secret.regcred.dockerconfigjson=${env.dockerString} --debug ./front-end/"
       sh "sleep 5"
    }
}


node {
     def backendIp

    stage('Clone repository') {
        /* Cloning the Repository to our Workspace */
                checkout scm
            }

    stage('Startup activities'){
     sh "kubectl cluster-info"

     // Init helm client
     sh "helm init"
    }

     stage('Deploy Backend'){
       createNamespace('api')
       helmInstallBackend()
      }

     stage('Deploy Frontend'){
      createNamespace('ui')
      helmInstallFrontend()
     }
 }