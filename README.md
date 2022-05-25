## LABORATÓRIO KIND

Iniciar o KUBERNETES na sua máquina para testes!

### Pre-requisitos

1. Aplicações:

- docker
- kind
- kubectl
- helm

2. Configurações:

- 4 GB memória RAM
- 2 Nucleos ( Processador )

### Install Docker

UBUNTU ( Debian )

```bash
sudo apt update
sudo apt install docker.io
``` 

Fedora ( Red Hat )

```bash
sudo dnf update
sudo dnf install docker
```

MANJARO ( ArchLinux )

```bash
sudo pacman -Syu
sudo pacman -S docker
```

### Install KIND

1. Baixar
2. Permissão de Execução
3. Move para diretório de execução local do sistema

```bash
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.12.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/sbin/
```

### Install kubectl

1. Baixar
2. Permissão de Execução
3. Move para diretório de execução local do sistema

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/sbin/
```

### Manifesto de criação do Cluster:

**Nome:** mycluster

**Config:**
- 1x Control Plane
- 3x Worker

**mycluster**
```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
- role: worker
  extraPortMappings:
  - containerPort: 30000
    hostPort: 8080
  - containerPort: 31111
    hostPort: 8081
  - containerPort: 80
    hostPort: 80
  - containerPort: 443
    hostPort: 443
```

**OBS.:** Existe 2 (dois) mapeamentos de portas. Em sua maquina (host) real você irá acessar a porta 8080 ou 8081 e terá acesso as portas do Service (k8s).

### Criar o Cluster K8S

```bash
kind create cluster --name mycluster --config mycluster.yaml
```

### Instalar HELM

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

### Deletar o Cluster K8S

```bash
kind delete clusters mycluster
```

**OBS.:** Use o **kind** somente para testes!


---

### metallb - Metal Load Balancer

Criar um namespace metallb

```bash
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.12.1/manifests/namespace.yaml
```

Aplicar manifesto metallb

```bash
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.12.1/manifests/metallb.yaml
```

**OBS.:** Espere todos os pods ficarem *running*:

```bash
kubectl get pods -n metallb-system --watch
```


### Manifesto *metallb-configmap.yaml*

Com o comando abaixo, verifique qual o ranger de IP do kind:

```bash
docker network inspect -f '{{.IPAM.Config}}' kind
```

**OBS.:** A saída conterá um cidr como 172.19.0.0/16. Queremos que nosso intervalo de IP do balanceador de carga venha dessa subclasse. Podemos configurar o metallb, por exemplo, para usar 172.19.255.200 a 172.19.255.250 criando o configmap.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - 172.19.255.200-172.19.255.250
```

ou execute:

```bash
kubectl apply -f https://kind.sigs.k8s.io/examples/loadbalancer/metallb-configmap.yaml
```


### Configure seu /etc/hosts

O IP (LoadBalancer) carregado em seu *Service*, pode ser setado no /etc/hosts para teste. 

- kubectl get service -A
```
NAMESPACE        NAME         TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)                      AGE
default          kubernetes   ClusterIP      10.96.0.1       <none>           443/TCP                      5h2m
kube-system      kube-dns     ClusterIP      10.96.0.10      <none>           53/UDP,53/TCP,9153/TCP       5h2m
traefik-system   traefik      LoadBalancer   10.96.143.199   172.19.255.200   80:30133/TCP,443:30507/TCP   126m
```

- /etc/hosts
```
# Host addresses
127.0.0.1  localhost
127.0.1.1  tessy
::1        localhost ip6-localhost ip6-loopback
ff02::1    ip6-allnodes
ff02::2    ip6-allrouters

########################

172.19.255.200 traefik.meudominio.com.br
```

