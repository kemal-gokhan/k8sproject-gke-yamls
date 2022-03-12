### Notes

First I started to setup Kubernetes on GCP. It's free and default you can initialize 3 workers first.

### 1

First on gcp shell to connect k8s
gcloud container clusters get-credentials kubernetes-poc-kemalproject --zone us-central1-c --project graphite-nectar-343511

### 2

To review namespaces-(no namespaces in first, just k8s namespaces)
kubectl get namespaces
kubectl create namespace production
kubectl create namespace test

### 3
Create junior group and permit it to read/list/create permission them on test namespaces. 
Junior Group -> read/list/create on test & read/list on production namespace.
Senior Group -> read/list/create on test &  production namespace. read/list for other namesaces.



### 4
Nginx ingress
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.1.1/deploy/static/provider/cloud/deploy.yaml

kubectl get all -n ingress-nginx # (it'll create ingress-nginx namespace,cr, rl etc...)

kubectl get namespace

kubectl get no -o json

### 5
Schedule Nodes
Just 2 pods for production other one is for test.

kubectl get all -A

node0=$(kubectl get no -o jsonpath="{.items[0].metadata.name}")
node1=$(kubectl get no -o jsonpath="{.items[1].metadata.name}")
node2=$(kubectl get no -o jsonpath="{.items[2].metadata.name}")
kubectl taint node $node0 tier=production:NoSchedule
kubectl label node $node0 tier=production
kubectl label node $node1 tier=test
kubectl label node $node2 tier=test

kubectl taint node $node0 tier=production:NoSchedule- (untaint minus symbol)

kubectl taint node <nodename1> tier=prod:NoSchedule
kubectl taint node <nodename2> tier=prod
kubectl taint node <nodename3> tier=prod


kubectl create secret generic mysql-test-secret -n test --from-file=MYSQL_ROOT_PASSWORD=mysql_root_password.txt --from-file=MYSQL_USER=mysql_user.txt --from-file=MYSQL_PASSWORD=mysql_password.txt --from-file=MYSQL_DATABASE=mysql_database.txt

kubectl get secret -n test (check secret)

kubectl create secret generic mysql-prod-secret -n production --from-file=MYSQL_ROOT_PASSWORD=mysql_root_password.txt --from-file=MYSQL_USER=mysql_user.txt --from-file=MYSQL_PASSWORD=mysql_password.txt --from-file=MYSQL_DATABASE=mysql_database.txt

kubectl get secret -n production (check secret)

kubectl get secret mysql-test-secret -o jsonpath="{.data}" | base64 --decode
kubectl get secret default-token-sxftq  jsonpath="{.data.MYSQL_ROOT_PASSWORD}" | base64 --decode

kubectl get pods -o wide -A (to check with nodes)

kubectl exec <pod-name> -c <container-name> -- <cmd> <arg1> <arg2>
kubectl exec --stdin --tty my-pod -- /bin/sh 
kubectl exec -it <pod-name> -c <container-name> -- bash