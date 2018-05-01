------Connect to Cluster------

gcloud container clusters get-credentials staging --zone us-east1-b --project consert-171717

gcloud container clusters get-credentials production --zone us-central1-a --project consert-171717

------Creating a deployment------

1. Install Helm & Kubectl   
#Create Helm's Tiller service account and patch tiller deploy using: https://gist.github.com/prateekrastogi/b7d703ad7052046032ebfa55da813606  

2. kubectl create -f init --recursive         #Recursive directory deployment

3. git clone https://github.com/clockworksoul/helm-elasticsearch.git elasticsearch

4. helm install -f elasticsearch/values.yaml elasticsearch/elasticsearch --name elastic

5. helm install -f mongo/values.yaml stable/mongodb-replicaset --name mongo

6. kubectl create -f consert.yaml

7. kubectl create -f ingress --recursive

------Accessing Services Dashboards------

1. kubectl proxy                       
    #Must be continuously running to access the proxy dashboards

2. Kubernetes Dashboard URL: http://localhost:8001/ui
    #Kubernetes dashboard will ask for config file to login. But, in windows its not properly working. So, open %USER%/.kube/config file and copy 'access-token' sub-field inside 'auth-provider' field, and provide that copied token to the login page token form field.

3. Kibana URL: http://localhost:8001/api/v1/proxy/namespaces/default/services/elastic-elasticsearch-kibana

4. NoSqlClient URL: http://localhost:8001/api/v1/proxy/namespaces/default/services/nosqlclient:http

# Below panels must be accessed only from the given urls, i.e. no proxying, to avoid 404 resources not found error.
5. seeder explorer: https://seeder.consert.live/explorer/

6. backend explorer: https://backend.consert.live/explorer/

7. backend graphiql: https://backend.consert.live/graphiql 

------Updating a deployment----

helm upgrade -f elasticsearch/values.yaml elastic elasticsearch/elasticsearch
#For updating elasticsearch helm release

helm upgrade -f mongo/values.yaml mongo stable/mongodb-replicaset
#For upgrading mongodb helm release 

kubectl replace -f ingress                    
#For updating a ingress always run this command on a modified Ingress yaml file not 'apply'

kubectl replace --force -f FILENAME
#Force replaces the deployment by deleting and then creating the resources. Use it for UPDATING DEPLOYMENTS.

#Below one is very rarely used. Read its description before using.
kubectl apply -f FILENAME [options e.g. --recursive]                 
#Update the whole deployment according to changes in consert.yaml. Does not perform deletion of removed configs that were present earlier. Only updates. Only use when there are actual changes in the deployment file.

------Deleting a deployment------

kubectl delete -f FILENAME [options e.g. --recursive]

helm delete mongo
helm del --purge mongo

helm delete elastic
helm del --purge elastic

------Convert config files between different API version of Kubernetes------

kubectl convert -f FILENAME [options]


------Managing a Particular Deployment/StatefulSets Rollout------

# Use Mainly to avoid real-time catastrophic issues in a particular deployment/statefulsets i.e.     should never be used

kubectl rollout history deployment/frontend-deployment

kubectl rollout undo deployment/frontend-deployment   #Rolling Back to a Previous Revision of the deployment

Available rollout Commands:
  history     View rollout history
  pause       Mark the provided resource as paused
  resume      Resume a paused resource
  status      Show the status of the rollout
  undo        Undo a previous rollout

kubectl edit deployment/frontend-deployment      #Opens the concerned deployment file window for editing [Real-time editing]

kubectl edit statefulsets/mongo


------Secret------

Kubernetes secret Kind in yaml requires base64 encoded strings while the certificate generated from cloudflare are in .pem format.
So, use online base64 encoder/decoder to get the desired formats.