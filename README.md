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

Inside minikube vm, we can use docker cmds to find that all the services provided by k8s are run from containers, including master's apiServer, etcd, scheduler, and controller-manager!
docker ps
CONTAINER ID        IMAGE                       COMMAND                  CREATED             STATUS              PORTS               NAMES
3dc3bd9a1341        harrylawsmith/sa-frontend   "nginx -g 'daemon of…"   4 minutes ago       Up 4 minutes                            k8s_sa-frontend_sa-frontend-56dbb44f45-lwlnd_default_26a0a9cc-7518-11e9-99af-080027737ca2_0
c387b0c298fd        harrylawsmith/sa-frontend   "nginx -g 'daemon of…"   4 minutes ago       Up 4 minutes                            k8s_sa-frontend_sa-frontend-56dbb44f45-n6nsk_default_26974339-7518-11e9-99af-080027737ca2_0
7ef0eb2022cf        k8s.gcr.io/pause:3.1        "/pause"                 4 minutes ago       Up 4 minutes                            k8s_POD_sa-frontend-56dbb44f45-lwlnd_default_26a0a9cc-7518-11e9-99af-080027737ca2_0
2f2845f0eb1a        k8s.gcr.io/pause:3.1        "/pause"                 4 minutes ago       Up 4 minutes                            k8s_POD_sa-frontend-56dbb44f45-n6nsk_default_26974339-7518-11e9-99af-080027737ca2_0
a6f44c5a4af8        harrylawsmith/sa-webapp     "java -jar sentiment…"   17 minutes ago      Up 17 minutes                           k8s_sa-logic_sa-webapp-b7577f9df-dxcfd_default_f62d8e85-7515-11e9-99af-080027737ca2_0
39f9c3fc7a7f        harrylawsmith/sa-logic      "python3 sentiment_a…"   17 minutes ago      Up 17 minutes                           k8s_sa-logic_sa-logic-5b7f6bdf5f-q5b67_default_f6235395-7515-11e9-99af-080027737ca2_0
72f327c6ab3c        harrylawsmith/sa-webapp     "java -jar sentiment…"   17 minutes ago      Up 17 minutes                           k8s_sa-logic_sa-webapp-b7577f9df-4b2jq_default_f62fae4a-7515-11e9-99af-080027737ca2_0
65012801806c        harrylawsmith/sa-logic      "python3 sentiment_a…"   17 minutes ago      Up 17 minutes                           k8s_sa-logic_sa-logic-5b7f6bdf5f-qzb7s_default_f62353ae-7515-11e9-99af-080027737ca2_0
a6f96f301194        harrylawsmith/sa-webapp     "java -jar sentiment…"   17 minutes ago      Up 17 minutes                           k8s_sa-logic_sa-webapp-b7577f9df-wb7wj_default_f62fa4b4-7515-11e9-99af-080027737ca2_0
1b609da29f67        harrylawsmith/sa-logic      "python3 sentiment_a…"   18 minutes ago      Up 18 minutes                           k8s_sa-logic_sa-logic-5b7f6bdf5f-8hwd2_default_f62209cc-7515-11e9-99af-080027737ca2_0
c4b2d85285c7        k8s.gcr.io/pause:3.1        "/pause"                 20 minutes ago      Up 20 minutes                           k8s_POD_sa-webapp-b7577f9df-4b2jq_default_f62fae4a-7515-11e9-99af-080027737ca2_0
cb1f3caf9e0d        k8s.gcr.io/pause:3.1        "/pause"                 20 minutes ago      Up 20 minutes                           k8s_POD_sa-webapp-b7577f9df-wb7wj_default_f62fa4b4-7515-11e9-99af-080027737ca2_0
ac0eb00d2160        k8s.gcr.io/pause:3.1        "/pause"                 20 minutes ago      Up 20 minutes                           k8s_POD_sa-webapp-b7577f9df-dxcfd_default_f62d8e85-7515-11e9-99af-080027737ca2_0
3d242f8073b6        k8s.gcr.io/pause:3.1        "/pause"                 20 minutes ago      Up 20 minutes                           k8s_POD_sa-logic-5b7f6bdf5f-q5b67_default_f6235395-7515-11e9-99af-080027737ca2_0
df47d4057635        k8s.gcr.io/pause:3.1        "/pause"                 20 minutes ago      Up 20 minutes                           k8s_POD_sa-logic-5b7f6bdf5f-qzb7s_default_f62353ae-7515-11e9-99af-080027737ca2_0
d7feb2f9c415        k8s.gcr.io/pause:3.1        "/pause"                 20 minutes ago      Up 20 minutes                           k8s_POD_sa-logic-5b7f6bdf5f-8hwd2_default_f62209cc-7515-11e9-99af-080027737ca2_0
51fec06c2724        eb516548c180                "/coredns -conf /etc…"   32 minutes ago      Up 32 minutes                           k8s_coredns_coredns-fb8b8dccf-hdztk_kube-system_34b99afc-7514-11e9-99af-080027737ca2_1
e359778fc3e7        eb516548c180                "/coredns -conf /etc…"   32 minutes ago      Up 32 minutes                           k8s_coredns_coredns-fb8b8dccf-8kc2p_kube-system_34b8e276-7514-11e9-99af-080027737ca2_0
3a10a778a9c0        4689081edb10                "/storage-provisioner"   32 minutes ago      Up 32 minutes                           k8s_storage-provisioner_storage-provisioner_kube-system_3582ee76-7514-11e9-99af-080027737ca2_0
bfb325d5af01        k8s.gcr.io/pause:3.1        "/pause"                 32 minutes ago      Up 32 minutes                           k8s_POD_coredns-fb8b8dccf-8kc2p_kube-system_34b8e276-7514-11e9-99af-080027737ca2_0
4d8bc66488fe        k8s.gcr.io/pause:3.1        "/pause"                 32 minutes ago      Up 32 minutes                           k8s_POD_storage-provisioner_kube-system_3582ee76-7514-11e9-99af-080027737ca2_0
d53c73dde8cb        20a2d7035165                "/usr/local/bin/kube…"   32 minutes ago      Up 32 minutes                           k8s_kube-proxy_kube-proxy-n4qlv_kube-system_34b2cd40-7514-11e9-99af-080027737ca2_0
65154d2301eb        k8s.gcr.io/pause:3.1        "/pause"                 32 minutes ago      Up 32 minutes                           k8s_POD_coredns-fb8b8dccf-hdztk_kube-system_34b99afc-7514-11e9-99af-080027737ca2_0
237373f66f96        k8s.gcr.io/pause:3.1        "/pause"                 32 minutes ago      Up 32 minutes                           k8s_POD_kube-proxy-n4qlv_kube-system_34b2cd40-7514-11e9-99af-080027737ca2_0
9499e7efd3cf        efb3887b411d                "kube-controller-man…"   33 minutes ago      Up 33 minutes                           k8s_kube-controller-manager_kube-controller-manager-minikube_kube-system_14f21e8d22bf58a1aafff0a82de4f720_0
49c743aaf82b        2c4adeb21b4f                "etcd --advertise-cl…"   33 minutes ago      Up 33 minutes                           k8s_etcd_etcd-minikube_kube-system_59104f68e6c05517aa837a058eda9e90_0
6f17de7a96d4        cfaa4ad74c37                "kube-apiserver --ad…"   33 minutes ago      Up 33 minutes                           k8s_kube-apiserver_kube-apiserver-minikube_kube-system_03a8f6dfe7a83f025a6bf6c736ea60b6_0
7d5fed909875        8931473d5bdb                "kube-scheduler --bi…"   33 minutes ago      Up 33 minutes                           k8s_kube-scheduler_kube-scheduler-minikube_kube-system_f44110a0ca540009109bfc32a7eb0baa_0
e48c5b5e0dbf        119701e77cbc                "/opt/kube-addons.sh"    33 minutes ago      Up 33 minutes                           k8s_kube-addon-manager_kube-addon-manager-minikube_kube-system_0abcb7a1f0c9c0ebc9ec348ffdfb220c_0
2d8e382c5510        k8s.gcr.io/pause:3.1        "/pause"                 33 minutes ago      Up 33 minutes                           k8s_POD_kube-scheduler-minikube_kube-system_f44110a0ca540009109bfc32a7eb0baa_0
94b5fff152d9        k8s.gcr.io/pause:3.1        "/pause"                 33 minutes ago      Up 33 minutes                           k8s_POD_kube-controller-manager-minikube_kube-system_14f21e8d22bf58a1aafff0a82de4f720_0
b56498c237bd        k8s.gcr.io/pause:3.1        "/pause"                 33 minutes ago      Up 33 minutes                           k8s_POD_kube-apiserver-minikube_kube-system_03a8f6dfe7a83f025a6bf6c736ea60b6_0
3e98fd2a7cbc        k8s.gcr.io/pause:3.1        "/pause"                 33 minutes ago      Up 33 minutes                           k8s_POD_etcd-minikube_kube-system_59104f68e6c05517aa837a058eda9e90_0
580aae9bdfee        k8s.gcr.io/pause:3.1        "/pause"                 33 minutes ago      Up 33 minutes                           k8s_POD_kube-addon-manager-minikube_kube-system_0abcb7a1f0c9c0ebc9ec348ffdfb220c_0

