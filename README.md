
---
# Installing and Integrating Mininet, ONOS, and sFlow-RT


## Operating System Selection and Virtual Machine Setup

To ensure better stability and compatibility during testing, it is recommended to use Ubuntu 20.04 LTS (Focal Fossa). While it is possible to install Mininet and ONOS on other Ubuntu versions, several installation issues and version conflicts have been observed with more recent releases.

The system setup was performed using VirtualBox, a widely used virtualization tool for creating virtual machines. The Ubuntu ISO image was downloaded from the official Canonical repository:

https://releases.ubuntu.com/focal/

The Desktop 64-bit (AMD64) image was used.

### 1. Mininet Installation

Update system packages:

```bash
sudo apt update
```

Install dependencies:

```bash
sudo apt install python3-pip git
sudo pip3 install ryu
```

Clone the official Mininet repository:

```bash
git clone https://github.com/mininet/mininet
```

Install Mininet:

```bash
cd mininet/util
sudo ./install.sh -a
```

---

### 2. Install and Build ONOS with Bazel (version 1.14+)

Download Bazelisk:

```bash
wget https://github.com/bazelbuild/bazelisk/releases/download/v1.18.0/bazelisk-linux-amd64
chmod +x bazelisk-linux-amd64
sudo mv bazelisk-linux-amd64 /usr/local/bin/bazel
```

Clone the ONOS repository:

```bash
git clone https://gerrit.onosproject.org/onos
```

Set up the environment:

```bash
export ONOS_ROOT=~/onos
source $ONOS_ROOT/tools/dev/bash_profile
cd $ONOS_ROOT
```

Build ONOS:

```bash
bazel build onos
```

Run tests:

```bash
bazel query 'tests(//...)' | xargs bazel test
```

Start ONOS:

```bash
bazel run onos-local
```

---

### 3. Accessing ONOS (CLI and GUI)

#### Default credentials:

| Username | Password |
| -------- | -------- |
| onos     | rocks    |
| karaf    | karaf    |

#### CLI:

```bash
ssh -p 8101 karaf@localhost
```

#### GUI:

Open in your browser:
`http://<onos-system-ip>:8181/onos/ui`

---

### 4. Installing sFlow-RT

```bash
sudo apt install default-jre
wget https://inmon.com/products/sFlow-RT/sflow-rt_3.1-1715.deb
sudo dpkg -i sflow-rt_3.1-1715.deb
```

Enable and start the service:

```bash
sudo systemctl enable sflow-rt
sudo systemctl start sflow-rt
sudo systemctl status sflow-rt
```

Access in browser:
`http://localhost:8008/`

---

### 5. Configure sFlow-RT for ONOS

Download and extract the full package:

```bash
wget https://inmon.com/products/sFlow-RT/sflow-rt.tar.gz
tar -xzvf sflow-rt.tar.gz
cd sflow-rt
pip install -r extras/sflow.py/requirements.txt
```

(Optional) Move `extras` to `/usr/local/sflow-rt`. 

---

### 6. Activate OpenFlow and FWD on ONOS

#### CLI:

```bash
ssh -p 8101 karaf@localhost
app activate org.onosproject.openflow
app activate org.onosproject.fwd
```

#### GUI:

(To be added)

---

### 7. Mininet Test Command

This command needs to be issued in the sflow-rt directory, otherwise, the full path to the extras/sflow.py file must be provided.

```bash
sudo mn --custom ./extras/sflow.py --link tc,bw=10 --topo=tree,depth=3,fanout=2 --switch ovs,protocols=OpenFlow14 --controller remote,ip=<ONOS-IP>
```

This command creates a tree topology, connects it to the ONOS controller, and enables traffic monitoring via sFlow-RT.

TBD: Add images
---
# README em Português

---

# Seleção do Sistema Operacional
Para garantir maior estabilidade e compatibilidade durante os testes, recomenda-se a utilização do Ubuntu 20.04 LTS (Focal Fossa). Embora outras versões do Ubuntu possam ser utilizadas, foram identificados diversos problemas de instalação e conflitos de versionamento ao tentar configurar o Mininet e o ONOS em versões mais recentes.

A configuração foi realizada utilizando o VirtualBox, uma ferramenta de virtualização amplamente utilizada para criar máquinas virtuais. A imagem .iso da distribuição foi obtida no site oficial da Canonical:

https://releases.ubuntu.com/focal/

Foi utilizada a imagem Desktop 64-bit (AMD64).

# Instalação e Integração de Mininet, ONOS e sFlow-RT

### 1. Instalação do Mininet

Atualize os pacotes do sistema:

```bash
sudo apt update
```

Instale as dependências:

```bash
sudo apt install python3-pip git
sudo pip3 install ryu
```

Clone o repositório oficial do Mininet:

```bash
git clone https://github.com/mininet/mininet
```

Acesse o diretório de instalação e execute o script:

```bash
cd mininet/util
sudo ./install.sh -a
```

---

### 2. Instalação e Build do ONOS com Bazel (versão 1.14+)

Baixe o Bazelisk:

```bash
wget https://github.com/bazelbuild/bazelisk/releases/download/v1.18.0/bazelisk-linux-amd64
chmod +x bazelisk-linux-amd64
sudo mv bazelisk-linux-amd64 /usr/local/bin/bazel
```

Clone o repositório do ONOS:

```bash
git clone https://gerrit.onosproject.org/onos
```

Configure o ambiente:

```bash
export ONOS_ROOT=~/onos
source $ONOS_ROOT/tools/dev/bash_profile
cd $ONOS_ROOT
```

Compile o ONOS:

```bash
bazel build onos
```

Teste a instalação:

```bash
bazel query 'tests(//...)' | xargs bazel test
```

Inicie o ONOS:

```bash
bazel run onos-local
```

---

### 3. Acessando ONOS (CLI e GUI)

#### Credenciais padrão:

| Usuário | Senha |
| ------- | ----- |
| onos    | rocks |
| karaf   | karaf |

#### CLI:

```bash
ssh -p 8101 karaf@localhost
```

#### GUI:

Acesse via navegador:
`http://<IP-do-ONOS>:8181/onos/ui`

---

### 4. Instalação do sFlow-RT

```bash
sudo apt install default-jre
wget https://inmon.com/products/sFlow-RT/sflow-rt_3.1-1715.deb
sudo dpkg -i sflow-rt_3.1-1715.deb
```

Habilite e inicie o serviço:

```bash
sudo systemctl enable sflow-rt
sudo systemctl start sflow-rt
sudo systemctl status sflow-rt
```

Acesse via navegador:
`http://localhost:8008/`

---

### 5. Configurar sFlow-RT para ONOS

Baixe e extraia a versão completa do sFlow-RT:

```bash
wget https://inmon.com/products/sFlow-RT/sflow-rt.tar.gz
tar -xzvf sflow-rt.tar.gz
cd sflow-rt
pip install -r extras/sflow.py/requirements.txt
```

Opcional: mover a pasta `extras` para `/usr/local/sflow-rt`.

---

### 6. Ativar OpenFlow e FWD no ONOS

#### CLI:

```bash
ssh -p 8101 karaf@localhost
app activate org.onosproject.openflow
app activate org.onosproject.fwd
```

#### GUI:

(TBD)

---

### 7. Teste com Mininet

```bash
sudo mn --custom ./extras/sflow.py --link tc,bw=10 --topo=tree,depth=3,fanout=2 --switch ovs,protocols=OpenFlow14 --controller remote,ip=<IP-do-ONOS>
```

Este comando cria uma topologia árvore conectando o Mininet ao ONOS, permitindo visualização dos fluxos via sFlow-RT.

TBD: Add images

---