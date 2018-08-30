For K8 canary deployment

This relates to project: https://github.com/sbudaraj/InsightProject

These are the yaml files used with kubectl to create the aws infra needed for k8 canary deployement of app mentioned in jenkinstest folder.

pv-claim and storage yaml files create a persistent EBS volume that will be used by postgress

pgc deploys posgress pod that just has a postgres container but with persistent volume mentioned above.

dbsvc exposes the db service on above pod.

spcanary and spstable create 2 distinct pod templates that each have a flask app and nginx containers. They also leverage the config map that maps the reverseproxy file to conf.d file that helps point nginx to flaskapp.

elb yaml file exposes these services so they app can be accessed from outside the cluster.