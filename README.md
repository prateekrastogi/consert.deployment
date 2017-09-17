------Connect to Cluster------

gcloud container clusters get-credentials staging --zone us-east1-b --project consert-171717

gcloud container clusters get-credentials production --zone us-central1-a --project consert-171717

Use "kubectl <command> -h" for more information about a given command.


------Open Kubernetes Dashboard------

kubectl proxy                       #Must be continuously running to access the dashboard


------Creating a deployment------

kubectl create -f elasticsearch --recursive    #Recursive directory deployment

kubectl create -f consert.yaml      #File Deployment


------Updating a deployment----

kubectl apply -f consert.yaml                 #Update the whole deployment according to changes in consert.yaml. Does not perform deletion of removed configs that were present earlier. Only updates.

kubectl replace -f ingress                    #For updating a ingress always run this command on a modified Ingress yaml file not apply

------Deleting a deployment------

kubectl delete -f consert.yaml


------Convert config files between different API version of Kubernetes------

kubectl convert -f FILENAME [options]


------Managing a Particular Deployment/StatefulSets Rollout------

[ Use Mainly to avoid real-time catastrophic issues in a particular deployment/statefulsets i.e. should never be used ]

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