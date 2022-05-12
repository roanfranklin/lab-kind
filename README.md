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
