# dotnet-demo

          .NET DEPLOYMENT
I had used below tools the Deployment
1.Git
2.Jenkins
3.Docker
4.Kubernetes EKS
5.Loadbalancer
6.Ec2
7.Docker Hub
Git:
Pushed the source code to github with code, Dockerfile and Kuberneties Deployment files (Yaml)
Jenkins: 
Created Jenkins pipeline job and integrated git, Docker, Kubernetes for the Continuous integration and deployment.
Docker:
Created Docker image from Dockerfile and retagged the image
Docker Hub:
Once after renaming the Docker image, I can push the Docker image to save the Docker image
Kubernetes:
Created Deployment and service yaml files for the deployment and we have used service type as load balancer to access the application from outside










JenkinsFile: Declerative pipeline
pipeline {
    environment {     
    DOCKERHUB_CREDENTIALS= credentials('dockerhub-login')     
    }
    agent any

    stages {
        stage('Build') {
            steps {
                // Get some code from a GitHub repository
                git branch: 'main', url: 'https://github.com/aveen9966/dotnet-demo.git'
            }
        }
    
        stage('Building Docker image') {
            steps {
                // Creating Docker image
                sh 'docker build --no-cache -t dotnet/demo-app .'
            }
        }
        stage('Login to Docker Hub') {      	
            steps{                       	
	            sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'                		
	            echo 'Login Completed'      
            }           
        }      
        stage('DockerTag to Docker Push') {      	
            steps{                       	
	            sh 'docker tag dotnet/demo-app:latest naveen9182/dotnetapp:latest'
	            sh 'docker push naveen9182/dotnetapp:latest'
            }           
        }  
        stage('Deploy on K8s') {      	
            steps{                       	
	            sh'kubectl apply -f deployment.yaml '
	            sh 'kubectl apply -f service.yaml'
            }           
        }      
    }
} 

Kuberneties Yaml files:
Deployment.yaml- It will pull the Docker image from docker hub and then it  will create the deployment object with 2(pods) replicas and will expose on 80 port
apiVersion: apps/v1
	kind: Deployment
	metadata:
	  name: dotnet-deployment
	  labels:
	    app: dotnet
	spec:
	  replicas: 2
	  selector:
	    matchLabels:
	      app: dotnet
	  template:
	    metadata:
	      labels:
	        app: dotnet
	    spec:
	      containers:
	      - name: dotnet-demo-app
	        image: naveen9182/dotnetapp:latest
	        ports:
	        - containerPort: 80



Service.yaml:
It will create the service and type as loadbalancer to access the application from outside
apiVersion: v1
	kind: Service
	metadata:  
	  name: dotnet-demo-service
	spec:
	  selector:    
	    app: dotnet
	  type: LoadBalancer
	  ports:  
	  - name: http
	    port: 80
	    targetPort: 80


Application Output:
Welcome to Demo .NET App - aspnetapp (a87a06d97866e4a69820dc8695a27acc-219844410.ap-south-1.elb.amazonaws.com)

 
Soure Code aveen9966/dotnet-demo (github.com)




