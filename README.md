#criar cluster
kind create cluster --config=k8s/kind.yaml --name fullcycle

#listar informações do cluster
kind get cluster
kubectl cluster-info --context kind-{name-cluster}

#listar todos os clusters
kubectl config get-clusters

#mudar contexto do cluster
kubectl config use-context {name-cluster}

#listar clusters
kind get clusters

#deletar cluster
kind delete clusters {name-cluster}

#criar imagem docker
docker build -t aralvesandrade/hello-go .

#publicar imagem docker
docker push aralvesandrade/hello-go

#criar pod
kubectl apply -f k8s/pod.yaml

#listar pods
kubectl get pods

#expor pod na porta 8080
kubectl port-forward pod/{name-pod} 8080:8000

#visualizar logs do pod
kubectl logs pod/{name-pod} -f

#listar informações do pod
kubectl describe pod {name-pod}

#acessar pod
kubectl exec -it {name-pod} -- bash

#criar replicas
kubectl apply -f k8s/replicaset.yaml
kubectl get pods
kubectl get replicasets
kubectl delete pod {name-pod}
#ou deletar todos pods
kubectl delete pod --all
kubectl get replicasets

#criar imagem docker v2 e publicar
docker build -t aralvesandrade/hello-go:v2 .
docker push aralvesandrade/hello-go:v2

kubectl get replicasets
kubectl delete replicaset {name-replicaset}
kubectl get replicasets
kubectl get pods
kubectl apply -f k8s/deployment.yaml
kubectl get deployments
kubectl get replicasets
kubectl get pods

#mudar versão no deployment
kubectl apply -f k8s/deployment.yaml
kubectl get pods

#listar informação do deployment
kubectl get deployments
kubectl describe deployment {name-deployment}

#listar informação do replicaset
kubectl get replicasets
kubectl describe replicaset {name-replicaset}

#rollout historico
kubectl get deployments
kubectl rollout history deployment {name-deployment}
#undo retorna revisão anterior a atual
kubectl rollout undo deployment {name-deployment}
kubectl get pods
kubectl describe pod {name-pod}
kubectl get replicasets
kubectl describe replicaset {name-replicaset}
#ou
kubectl rollout undo deployment goserver --to-revision=1

#criando serviço ClusterIP
kubectl apply -f k8s/service.yaml
kubectl get services
#expor service na porta 8080
kubectl port-forward svc/{name-service} 8080:80

kubectl get svc
kubectl delete svc {name-service}

#acessando api do k8s
kubectl proxy --port=8081
curl http://127.0.0.1:8081/api/v1/namespaces/default/services/goserver-service

#aplicando variaveis e variaveis secretas
kubectl apply -f k8s/configmap-env.yaml
kubectl apply -f k8s/secret.yaml
#injetando um arquivo txt
kubectl apply -f k8s/configmap-family.yaml
#aplicar alterações feitas no arquivo deployment.yaml
kubectl apply -f k8s/deployment.yaml
