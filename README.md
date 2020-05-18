# PaytmAssesment
1. Deploy the deployment first with below command for autoscaling if cpu is 50
   kubectl autoscale deployment paytm-assesment --max=3 --cpu-percent=50
2. As the horizozntal autscaling dosent have memory parameter script is needed for the autoscaling through     memory parameter.
3. Deploy the replica set for making sure that the 7 pods are running when there is any changes in the deployment.
4. For the higher priority than daemon set we need to create the high priority class as:
apiVersion: scheduling.k8s.io/v1beta1
kind: PriorityClass
metadata:
  name: high-priority
value: 1000000
globalDefault: false
description: "This priority class should be used for XYZ service pods only."
5. After the priority class is created it can be assigned to particular pod, it dosent work for deployment or replica set.
6. For ECR repository we need login to the repo and pull the image so for that we need to run below command:
kubectl create secret docker-registry us-west-2-ecr-registry \
 --docker-server=https://000000000000.dkr.ecr.us-west-2.amazonaws.com \
 --docker-username=AWS \
 --docker-password="`aws ecr --region=us-west-2 get-authorization-token --output text --query authorizationData[].authorizationToken | base64 -d | cut -d: -f2`" \
 --docker-email="kshitij.srivastava94@gmail.com"
 7. So this will be created in a  us-west-2-ecr-registry file which can be used in the imagepullsecret tag in deployment and repica set.
 8. For exposing the app to 3000 port use the above service kind which is mentioned in the assesment.yaml file.
 7. We are running deployment with 3 replicas and 7 as replicas set which in total will have 10 pods running.
 8. As i dont have the server available and the system is from the office through which i am not able to create the servers or run it, i wont be able to give the outputs, but these are the points  which will be taken care for the assesment given.
