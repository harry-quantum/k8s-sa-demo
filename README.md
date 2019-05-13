
Install minikube
brew cask install minikube

brew link --overwrite --dry-run kubernetes-cli

minikube start -p sa

kubectl get nodes

create all resources in one shot:
kubectl apply -f .


Or we can create/update one file at a time:
kubectl apply -f sa-front-deploy.yaml --record
kubectl apply -f sa-front-svc.yaml

kubectl apply -f sa-logic-deploy.yaml --record
kubectl apply -f sa-logic-svc.yaml

kubectl apply -f sa-webapp-deploy.yaml --record
kubectl apply -f sa-webapp-svc.yaml

kubectl get deploy
kubectl get pod
kubectl get svc
minikube service list -p sa

we need to change app.js to paste the new IP for sa-webapp, and push the new docker image. After that, we need to change the sa-front-deploy.yaml to use a new tag, e.g., blue. And we need to apply this change.

kubectl apply -f sa-front-deploy.yaml --record
kubectl rollout status deployment sa-frontend

We can see the history of this deploy:
kubectl rollout history deploy sa-frontend

Now let's change App.css to red and build/push sa-frontend:red; change sa-front-deploy.yaml to use red tag; apply the change. And check the application is using the new red style.


kubectl rollout undo deployment sa-frontend --to-revision=2

We can ssh into the minikube vm,
minikube ssh -p sa
