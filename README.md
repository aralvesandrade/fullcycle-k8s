Criar cluster usando cli `KIND`(https://kind.sigs.k8s.io/)

```
kind create cluster --config=k8s/kind.yaml --name fullcycle
```

Listar informações do cluster

```
kind get clusters
kubectl cluster-info --context kind-{name-cluster}
kubectl cluster-info --context kind-fullcycle
```

Listar todos os clusters

```
kubectl config get-clusters
```

Mudar contexto do cluster, por exemplo, acessar um cluster da GCP, AWS, Azure, etc

```
kubectl config use-context {name-cluster}
```

Deletar cluster

```
kind delete clusters {name-cluster}
```

Criar imagem e publicar imagem docker

```
docker build -t aralvesandrade/hello-go .
docker push aralvesandrade/hello-go
```

Criar pod, listar pods, expor na porta 8080, visualizar logs, listar informações e acessar via bash

```
kubectl apply -f k8s/pod.yaml
kubectl get pods
kubectl port-forward pod/{name-pod} 8080:8000
kubectl logs pod/{name-pod} -f
kubectl describe pod {name-pod}
kubectl exec -it {name-pod} -- bash
```

Criar replicas, listar replicas, deletar pod ou todos

```
kubectl apply -f k8s/replicaset.yaml
kubectl get pods
kubectl get replicasets
kubectl delete pod {name-pod}
#ou deletar todos pods
kubectl delete pod --all
kubectl get replicasets
```

No exemplo iremos criar imagem docker v2 e publicar

```
docker build -t aralvesandrade/hello-go:v2 .
docker push aralvesandrade/hello-go:v2
```

Criar arquivo do tipo `Deployment` objeto que gerencia a implantação de aplicaticos no cluster

No caso iremos simular alterar a versão da imagem docker, aplicar o deployment e com isso todas as replicas/pods são finalizadas e novos pods são criados com a nova versão do docker

```
kubectl get replicasets
kubectl delete replicaset {name-replicaset}
kubectl get replicasets
kubectl get pods
#mudar versão no deployment
kubectl apply -f k8s/deployment.yaml
kubectl get deployments
kubectl get replicasets
kubectl get pods
```

Listar informações do deployment

```
kubectl get deployments
kubectl describe deployment {name-deployment}
```

Listar informações do replicaset

```
kubectl get replicasets
kubectl describe replicaset {name-replicaset}
```

Voltar versão do histórico do deployment

```
kubectl get deployments
kubectl rollout history deployment {name-deployment}
#undo retorna revisão anterior a atual
kubectl rollout undo deployment {name-deployment}
kubectl get pods
kubectl describe pod {name-pod}
kubectl get replicasets
kubectl describe replicaset {name-replicaset}
#ou
kubectl rollout undo deployment goserver --to-revision={revision}
```

Criando arquivo do tipo `Service` objeto que define um conjunto lógico de pods e uma política de acesso a esses pods e expor na porta 8080

Tipos de Services: ClusterIP (IP Interno), NodePort e LoadBalancer (IP Externo)

- port: porta do service
- targetPort: porta do container
- nodePort: usado para NodePort, expoe um range de portas

```
kubectl apply -f k8s/service.yaml
kubectl get services
#expor service na porta 8080
kubectl port-forward svc/{name-service} 8080:80
```

Listar informações do service e deletar service

```
kubectl get svc
#ou kubectl get service
kubectl delete svc {name-service}
```

Expor API do k8s na porta 8081

```
kubectl proxy --port=8081
curl http://127.0.0.1:8081/api/v1/namespaces/default/services/goserver-service
```

Aplicar variaveis, variaveis secretas e injetar arquivo txt, usando tipo `ConfigMap` na aplicação

```
kubectl apply -f k8s/configmap-env.yaml
kubectl apply -f k8s/secret.yaml
kubectl apply -f k8s/configmap-family.yaml
#aplicar alterações feitas no arquivo deployment.yaml
kubectl apply -f k8s/deployment.yaml
```
