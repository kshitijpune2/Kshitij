1. Create a Kubernetes cluster which runs a node.js service. You can use any “hello world” node.js service.
-------------
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nodejs_honest_test
spec:
  selector:
    matchLabels:
      app: honest_test
	  env: prod
  minReadySeconds: 5
  replicas: 2
  strategy:
    type: RollingUpdate
	rollingUpdate:
	  maxSurge: 1
	  maxUnavailable: 0
  replicas: 1
  template:
    metadata:
      labels:
        app: honest_test
		env: prod
    spec:
	  volumes:[]
      containers:
	    - name: nodejs_honest_test
          image: docker.io/{username}/helloworld-nodejs
		  imagePullPolicy: Always
          ports:
		    - name :http
              containerPort: 3100

Expose the deployment to the internet using service as LoadBalancer:
kubectl expose deployment nodejs_honest_test --type="LoadBalancer"

So using the loadbalancer IP and 3100 port we will be able to get the application on the web.
-----------------------
2. How do you make the service scalable?
Using the kind as replica set we can scale our running pods and also replica set would make sure the amout of replica mentioned is running or not if not it will create the new pod.
And also:
Using Horizontal autoscaler we can create the pods scalable:
kubectl autoscale deployment nodejs_honest_test --cpu-percent= --min=1 --max=5

3. What CI/CD pipeline would you use (if not done in code, please describe every step from the commit of new code until the new code is running in production)?
a. Firstly i will assume that the jenkins is ready to use, after that i will create a single pipeline.
b. After clicking on the pipeline i will give my configurations of the github(credentials,branch and all the thing which is required) also add the aws credentials in jenkins crediatls tab and hook it up with the jenkins.
c. I will create a groovy script or the jenkins file in which i will give all my configurations to connect to the EKS cluster or GKE Cluster in stages 
d. So now what happens if i push my changes on the branch mostly master the pipeline which is hooked will automatically run and make the necessary changes as per the code pushed in the github.
e. The number of time you push the code to the brach which is hooked it will run the pipeline that many times.

4. How would you store and deploy secrets (such as API keys)?
First encode the API key by : echo -n <HT_API_KEY> | base64
apiVersion: v1
kind: Secret
metadata:
  name: honest_test_secret
  namespace: default
type: Opaque
data:
  api_key:
eyBoZWxsbzogInNkZmFzZCIsCnBhc3N3b3JkOiAid2hhdCIgfQo=

And use the above created scecret key in your deplyoment as below in the volume spec:
        secret:
          secretName: honest_test_secret
          items:
          - key: service-account-key
            path: key.json
It will store the key in the key.json file and will be stored in the /root/ location

4. How do you test how well your infrastructure scales (when many requests come in)?
We can monitor the Pods scaling in the GKE Dashboard or by checking the deplyoments(kubectl get deplyments <name of the deployment> -n <which namespace>) in that the Ready pods, up-to-date pods and available pods or in the replica set if created check the desired number and the current number and the ready number of the pods.

5. How do you provide an SSL certificate for your service?
Using ingress Service we can do the SSL certificate communication:
Firstly create tls private key and certificate in secrets:

apiVersion: v1
kind: Secret
metadata:
  name: honest_tls_secret
  namespace: default
data:
  tls.crt: "use base encoded secret cert"
  tls.key: "use base encoded secret key"
type: kubernetes.io/tls

After that create a Ingress service:

apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: honest_tls_ingress
spec:
  tls:
  - hosts:
      - honestfood.com
    secretName: honest_tls_secret
  rules:
  - host: honestfood.com
    http:
      paths:
      - path: /
        backend:
          serviceName: honest_service
          servicePort: 80

----------------Completed-----------------
