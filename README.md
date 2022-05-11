## LABORATÓRIO KIND

Iniciar o KUBERNETES na sua máquina para testes!

### Pre-requisitos

1. Aplicações:

- docker
- kind

2. Configurações:

- 4 GB memória RAM
- 2 Nucleos ( Processador )

### Install KIND
1. Baixar
2. Permissão de Execução
3. Move para diretório de execução local do sistema

```bash
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.12.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/sbin/
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

### Deletar o Cluster K8S

```bash
kind delete clusters mycluster
```

**OBS.:** Use o **kind** somente para testes!