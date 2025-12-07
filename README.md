# Descomplicando_k8s

### Como configurar e utilizar o Kubernetes com Docker e Kind
---
* **Pré-requisitos:** 
1. Baixe e instale o [Docker Desktop](https://docs.docker.com/desktop/?_gl=1*1oryam2*_gcl_au*NTUyOTEyMTU2LjE3NjUxMTY1MTE.*_ga*Njg2NTk4NjQ1LjE3NjUxMTY1MTE.*_ga_XJWPQMJYHQ*czE3NjUxMTY1MTAkbzEkZzEkdDE3NjUxMTY1MTEkajU5JGwwJGgw) ou [Podman](https://podman.io/docs/installation) para Windows/Mac/Linux.
2. Habilite WSL2 e Kubernetes support quando aplicável 
3. Após instalação, faça login no Docker Desktop e confirme com docker version e docker info que o daemon está ativo.

---

* **Instalação do kind:** instale com 
```curl -Lo ./kind https://kind.sigs.k8s.io/dl/<versão>/kind-windows-amd64 && chmod +x ./kind && mv kind /usr/local/bin/ ```(ajuste para Windows PowerShell usando Invoke-WebRequest); confirme com kind version.
Ou acesse o site oficial do Kind para mais informações: [Kind](https://kind.sigs.k8s.io/docs/user/quick-start/#installation)
---
**Configuração do cluster:** utilize o manifesto de cluster em dia-01/kind/kind-cluster.yml:1-6, que criará:
 * 1 control-plane;
 * 2 workers.
 para criar o cluster com use o manifesto do dia-01 **kind-cluster.yml** 
 ```kind create cluster --name descomplicando --config dia-01/kind/kind-cluster.yml```  
 
 Obtendo a seguinte mensagem:  
 ![Alt Imagem saída do terminal ao criar cluster](https://github.com/Brunohfc/Descomplicando_k8s/blob/day-01/assets/kind-create-cluster.png)

 Após a criação, verifique os nós via ```kubectl get nodes```.  
 
 Obtendo a seguinte saída:  
 ![Alt Imagem saída do terminal ao verificar nós](https://github.com/Brunohfc/Descomplicando_k8s/blob/day-01/assets/kubectl-get-nodes.png)
