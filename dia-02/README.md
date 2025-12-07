# Dia 02 – Pods com múltiplos contêineres e limites de recursos

## Pré-requisitos essenciais
1. Docker Desktop instalado e em execução (WSL2 habilitado no Windows). Valide com `docker version`.
2. kubectl 1.29+ disponível no PATH. Teste com `kubectl version --client`.
3. kind instalado. Confirme com `kind version`.
4. Opcional: namespace dedicado `kubectl create ns dia02 && kubectl config set-context --current --namespace=dia02`.

## Preparação do cluster KIND
1. Inicialize (se ainda não existir) com o arquivo do dia 01:  
   `kind create cluster --name descomplicando --config dia-01/kind/kind-cluster.yml`
2. Exporte o kubeconfig gerado pelo kind (normalmente automático).  
   `kubectl cluster-info --context kind-descomplicando`
3. Listar nós para garantir operação:  
   `kubectl get nodes -o wide`

## Arquivo dia-02/pod.yaml (dia-02/pod.yaml:1-12)
Propósito: pod simples com NGINX exposto na porta 80.
Passos:
1. `kubectl apply -f dia-02/pod.yaml`
2. Acompanhe até Running: `kubectl get pod my-giro-pod -w`
3. Logs rápidos: `kubectl logs my-giro-pod`
4. Remoção: `kubectl delete -f dia-02/pod.yaml`

## Arquivo dia-02/multi-container.yaml (dia-02/multi-container.yaml:1-32)
Propósito: pod com dois contêineres (NGINX + Alpine em sleep)
Passos:
1. `kubectl apply -f dia-02/multi-container.yaml`
2. Confirme contêineres: `kubectl get pod multi-container-pod -o jsonpath='{.spec.containers[*].name}'`
3. Entre no Alpine: `kubectl exec -it multi-container-pod -c girus-alpine-2 -- sh`
4. Limpeza: `kubectl delete -f dia-02/multi-container.yaml`

## Arquivo dia-02/multi-container-with-limits.yaml (dia-02/multi-container-with-limits.yaml:1-32)
Propósito: mesmo pod multi-contêiner adicionando limites e requests (QoS Guaranteed).
Passos:
1. `kubectl apply -f dia-02/multi-container-with-limits.yaml`
2. Cheque classe QoS: `kubectl get pod multi-container-pod -o jsonpath='{.status.qosClass}'`
3. Veja limites por contêiner:  
   `kubectl describe pod multi-container-pod | grep -A6 "Limits"`
4. Teste reinício por OOM simulando consumo (ex.: `stress-ng --vm 1 --vm-bytes 200M` após instalar no Alpine).
5. Remoção: `kubectl delete -f dia-02/multi-container-with-limits.yaml`

## Arquivo dia-02/pod-ubuntu-stress.yaml (dia-02/pod-ubuntu-stress.yaml:1-19)
Propósito: pod Ubuntu preparado para testes de stress com limites inferiores a 1 CPU / 256 MiB.
Passos:
1. `kubectl apply -f dia-02/pod-ubuntu-stress.yaml`
2. Instale ferramentas (uma vez por pod):  
   `kubectl exec -it ubuntu-stress -- bash`  
   `apt update && apt install -y stress`
3. Rode teste controlado: `stress --cpu 1 --timeout 30`
4. Observe throttling: `kubectl top pod ubuntu-stress`
5. Remoção: `kubectl delete -f dia-02/pod-ubuntu-stress.yaml`

## Troubleshooting rápido
- Erros de indentação YAML: valide com `kubectl apply --dry-run=client -f <file>`.
- Campo desconhecido `requests`: garantir que `requests` esteja dentro de `resources`.
- Pods pendentes: verificar `kubectl describe pod <pod>` e `kubectl get events`.
- Recursos insuficientes no KIND: aumente nós no arquivo `dia-01/kind/kind-cluster.yml`.

## Limpeza geral
1. `kubectl delete -f dia-02/pod.yaml --ignore-not-found`
2. `kubectl delete -f dia-02/multi-container.yaml --ignore-not-found`
3. `kubectl delete -f dia-02/multi-container-with-limits.yaml --ignore-not-found`
4. `kubectl delete -f dia-02/pod-ubuntu-stress.yaml --ignore-not-found`
5. (Opcional) `kind delete cluster --name descomplicando`