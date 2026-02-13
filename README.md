# 🚀 Kubernetes End-to-End Project on Amazon EKS  
## Deploy da Aplicação 2048 Game na AWS

![EKS](https://imgur.com/oADneqS.png)

---

# 📌 Sobre o Projeto

Este projeto demonstra a implementação completa (End-to-End) de uma aplicação containerizada no Amazon EKS (Elastic Kubernetes Service), utilizando Kubernetes como orquestrador e AWS como infraestrutura base.

O objetivo foi simular um ambiente real de produção, envolvendo:

- Containerização da aplicação
- Provisionamento de cluster Kubernetes gerenciado
- Configuração de IAM e permissões
- Deploy de workloads
- Exposição segura via Load Balancer
- Escalabilidade horizontal

A aplicação utilizada foi o clássico **2048 Game**, servindo como workload para demonstrar o fluxo completo de deploy em Kubernetes na AWS.

---

# 🏗️ Arquitetura da Solução

A arquitetura segue o seguinte fluxo:

Usuário → AWS Load Balancer → Kubernetes Service → Pod → Container (2048 App)

Componentes principais:

- Amazon EKS (Control Plane Gerenciado)
- Node Group (Amazon EC2 - Worker Nodes)
- Kubernetes Pod
- Kubernetes Service (LoadBalancer)
- IAM Roles para Cluster e Nodes
- VPC padrão da AWS

---

# 🧠 Decisões Arquiteturais

- Uso de EKS para abstrair a gestão do Control Plane
- Separação de IAM Roles entre cluster e node group
- Uso de Service tipo LoadBalancer para exposição pública
- Escalabilidade via Kubernetes (replicas)
- Execução em subnets múltiplas para alta disponibilidade
- Uso de Amazon Linux 2 como AMI padrão

---

# ⚙️ Stack Tecnológica

- **Amazon EKS**
- **Kubernetes**
- **Docker**
- **AWS CLI**
- **eksctl**
- **kubectl**
- **IAM**
- **EC2**
- **Elastic Load Balancer**

---

# 🔧 Pré-requisitos

Ferramentas utilizadas:

- AWS CLI configurado
- kubectl
- eksctl

Validação de identidade AWS:

```bash
aws sts get-caller-identity
```

Configuração do kubeconfig:

```bash
aws eks update-kubeconfig --region us-east-1 --name seu-cluster
```

Verificação dos nodes:

```bash
kubectl get nodes
```

---

# 🐳 Containerização

A aplicação 2048 foi containerizada utilizando Docker.

Exemplo de imagem utilizada:

```yaml
image: blackicebird/2048
```

A containerização garante:

- Portabilidade
- Padronização de ambiente
- Facilidade de deploy em Kubernetes

---

# 🚀 Provisionamento do Cluster EKS

## 1️⃣ Criação do Cluster

- Endpoint público habilitado
- Subnets padrão (2 ou 3)
- Versões padrão de:
  - VPC CNI
  - CoreDNS
  - kube-proxy

Tempo médio de criação: 10–12 minutos.

---

## 2️⃣ Configuração de IAM

### Role do Cluster
- AmazonEKSClusterPolicy

### Role do Node Group
- AmazonEKSWorkerNodePolicy
- AmazonEC2ContainerRegistryReadOnly
- AmazonEKS_CNI_Policy

Essa separação segue o princípio do menor privilégio.

---

## 3️⃣ Node Group

- AMI: Amazon Linux 2
- Desired/Min/Max: 1
- SSH habilitado
- Security Group liberando portas:
  - 22
  - 80
  - 8080

---

# 📦 Deploy da Aplicação

## Criação do Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
   name: 2048-pod
   labels:
      app: 2048-ws
spec:
   containers:
   - name: 2048-container
     image: blackicebird/2048
     ports:
       - containerPort: 80
```

Aplicação do manifesto:

```bash
kubectl apply -f 2048-pod.yaml
kubectl get pods
```

---

# 🌍 Exposição da Aplicação

## Service tipo LoadBalancer

```yaml
apiVersion: v1
kind: Service
metadata:
   name: mygame-svc
spec:
   selector:
      app: 2048-ws
   ports:
   - protocol: TCP
     port: 80
     targetPort: 80
   type: LoadBalancer
```

Aplicação:

```bash
kubectl apply -f mygame-svc.yaml
kubectl describe svc mygame-svc
```

O Kubernetes automaticamente provisiona um Elastic Load Balancer na AWS.

Após alguns minutos, o DNS público do ELB fica disponível para acesso via navegador.

---

# 📈 Escalabilidade

Escalonamento horizontal pode ser realizado ajustando o número de réplicas:

```bash
kubectl scale deployment 2048 --replicas=3
```

Isso permite absorver picos de tráfego com balanceamento automático entre pods.

---

# 📊 Validação e Testes

Verificação de recursos:

```bash
kubectl get pods
kubectl get svc
kubectl describe nodes
```

Teste de acesso:

```bash
curl <DNS_DO_LOAD_BALANCER>
```

---

# 🔐 Segurança Implementada

- IAM Roles segregadas
- Comunicação via Security Groups
- Cluster isolado dentro de VPC
- Controle de acesso via kubeconfig autenticado

---

# 📈 Resultados Técnicos

✔ Cluster Kubernetes gerenciado com alta disponibilidade  
✔ Deploy funcional de aplicação containerizada  
✔ Exposição segura via Load Balancer  
✔ Separação adequada de permissões IAM  
✔ Estrutura pronta para escalar workloads  

---

# 📚 Aprendizados Aplicados

- Provisionamento de Kubernetes na AWS
- Gerenciamento de permissões IAM para EKS
- Deploy e exposição de aplicações via Service
- Integração entre Kubernetes e Elastic Load Balancer
- Conceitos práticos de escalabilidade horizontal

---

# ⭐ Se este projeto foi útil

Considere:

- Dar uma estrela ⭐
- Compartilhar
- Contribuir com melhorias

---

> Este projeto demonstra um fluxo completo de containerização, provisionamento e deploy em Kubernetes utilizando Amazon EKS, simulando um cenário real de ambiente corporativo.
