For K8 canary deployment

This relates to project: https://github.com/sbudaraj/InsightProject

These are the yaml files used with kubectl to create the aws infra needed for k8 canary deployement of app mentioned in jenkinstest folder.

pv-claim and storage yaml files create a persistent EBS volume that will be used by postgress

pgc deploys posgress pod that just has a postgres container but with persistent volume mentioned above.

dbsvc exposes the db service on above pod.

spcanary and spstable create 2 distinct pod templates that each have a flask app and nginx containers. They also leverage the config map that maps the reverseproxy file to conf.d file that helps point nginx to flaskapp.

elb yaml file exposes these services so they app can be accessed from outside the cluster.

------------------------------------------
Some extra notes for anyone who wants to experiment with something like this in future:
1. I found this link very useful to lookup Kubectl commands for various things like checking pod status, container logs, scaling up/down, exposing services etc.
https://kubernetes.io/docs/reference/kubectl/cheatsheet/
2.I played around with init Container to ensure my Nginx was pointing my flask app but figured later the ConfigMaps were much simpler and better way to go for this need.
https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-initialization/
3. I initially setup my app in EC2 by registring docker images to AWS ECR but switched to docker hub when I started playing with K8 - seemed much simpler atleast for the demo needs.
4. I had a seperate pod for postgres as I wanted the flexibility of scaling the fornt end and though deployment was easier. I setup postgres manually and used a busybox (see cmd below) to test service, connectivity and initalize the db. If I had to do this over, postgres operators would be the right way to go. Easy to set up read replicas, failover etc. I debated the logic of having DB in K8 vs. RDS but given cost savaings (even a simple setup might run up 55-60% more for RDS) and progress on stateful apps in K8, I think this is the way to go for new apps.
kubectl run -i --tty busybox --image=busybox --restart=Never -- sh


5. I never used the K8 dashboard but set this up only for demo/screenshot. I considered demoing canary setup using something like split screen and cmd below but given 5 min demo limit, I chose to just talk to a screenshot of dahsboard. I also had a demo of Jenkins webhook setup but decided to skip that too due to time constraints. 
Kubectl create -f podcluster.yml && watch -n1 kubectl get pods 


