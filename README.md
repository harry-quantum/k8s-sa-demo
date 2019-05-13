minikube start -p sa

kubectl get nodes

create all resources in one shot:
kubectl apply -f .


Or we can create/update one file at a time:
kubectl apply -f sa-front-deploy-mini.yaml --record
kubectl apply -f sa-front-svc.yaml

kubectl apply -f sa-logic-deploy.yaml --record
kubectl apply -f sa-logic-svc.yaml

kubectl apply -f sa-webapp-deploy.yaml --record
kubectl apply -f sa-webapp-svc.yaml

minikube service list
kubectl get svc
kubectl get deploy

kubectl rollout status deployment sa-frontend
kubectl rollout undo deployment sa-frontend --to-revision=2

