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

Existem três tipos principais de `probes` que podem ser definidas dentro de um pod:

- livenessProbe: Esta probe é usada para determinar se o contêiner dentro do pod está em um estado saudável. Se o contêiner falhar nesta verificação, o Kubernetes considera o contêiner não saudável e pode reiniciá-lo.

- readinessProbe: Esta probe é usada para determinar se o contêiner dentro do pod está pronto para servir o tráfego. Se um contêiner falhar nesta verificação, ele é removido do balanceador de carga do Kubernetes, impedindo que o tráfego seja direcionado para ele até que ele se torne pronto novamente.

- startupProbe: Esta probe é usada para determinar se o contêiner dentro do pod está iniciando corretamente. Ela difere da livenessProbe porque é usado apenas durante a inicialização do contêiner. Uma vez que o contêiner está em execução, a startupProbe é desabilitada.

Aplicar vários arquivos `yaml`

```
kubectl apply -f k8s/configmap-env.yaml -f k8s/secret.yaml -f k8s/configmap-family.yaml -f k8s/service.yaml -f k8s/deployment.yaml -f k8s/metrics-server.yaml -f k8s/hpa.yaml && watch -n1 kubectl get pods
kubectl get services
kubectl port-forward svc/goserver-service 8000:80
```

Resources e HPA

Instalando metrics-server, fazer download do arquivo yaml no site https://github.com/kubernetes-sigs/metrics-server, e renomear o arquivo para metrics-server.yaml

```
wget https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

Alterar o arquivo no kind: Deployment, inserir o argumento `--kubelet-insecure-tls`, e com a linha de comando `kubectl apply -f k8s/metrics-server.yaml`, será instalado o metrics-server

```
kubectl apply -f k8s/deployment.yaml -f k8s/metrics-server.yaml
#validar se foi aplicado e instalado o metrics-server
kubectl get apiservices
#verificar as metricas de um pod
kubectl top pod goserver-6ddb7c476-gs2w4
```

Aplicar HPA

```
kubectl apply -f k8s/hpa.yaml
#verificar o hpa foi referenciado no Deployment/goserver
kubectl get hpa
#ou
watch -n1 kubectl get hpa
```

Aplicando um stress test na aplicar

```
kubectl run -it fortio --rm --image=fortio/fortio -- load -qps 800 -t 120s -c 70 "http://goserver-service/healthz"
```
