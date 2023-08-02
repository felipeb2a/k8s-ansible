# Install K8S com o Ansible

Projeto para instalação de um cluster Kubernetes utilizando o Ansible.

## Links de referência

### K8S
- [Doc Kubernetes](https://kubernetes.io/pt-br/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)
- [Ports Kubernetes](https://kubernetes.io/docs/reference/ports-and-protocols/)

### Ansible
- [Install Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#upgrading-ansible)

### RHEL Ansible
- [Yum module](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/yum_module.html#ansible-collections-ansible-builtin-yum-module)
- [Dnf module](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/dnf_module.html#requirements)
- [Systemd module](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/systemd_module.html#examples)
- [Firewalld module](https://docs.ansible.com/ansible/latest/collections/ansible/posix/firewalld_module.html)

### Ubuntu Ansible
[APT repositories](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_repository_module.html#requirements)

### Docker
- [Install Docker](https://docs.docker.com/engine/install/rhel/)

## Fases do Projeto
```
- Provisioning => Criar as instâncias/vms para o nosso cluster.
- Install_k8s => Criação do cluster, etc.
- Deploy_app => Deploy de uma aplicação exemplo
- Extra => Segredo
```

## Pre Requisitos


- [Ansible no ubuntu](https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html#installing-ansible-on-ubuntu)
    
    ```

    # versões mais novas do ubuntu
    $ sudo apt update
    $ sudo apt install software-properties-common
    $ sudo add-apt-repository --yes --update ppa:ansible/ansible
    $ sudo apt install ansible
    $ ansible-galaxy collection install ansible.posix

    # versões mais antigas do ubuntu (não necessário para versões mais novas do ubuntu)
    $ sudo apt install software-properties-common
    $ sudo apt update
    $ sudo add-apt-repository ppa:deadsnakes/ppa
    $ sudo apt install python3.9
    $ sudo apt install python3.9-distutils
    $ sudo apt install python3-pip
    $ sudo rm /usr/bin/python
    $ sudo ln -s python3.9 /usr/bin/python
    $ curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
    $ python get-pip.py --user
    $ sudo -H python -m pip install --upgrade pip

    # versões mais antigas do ubuntu (não necessário para versões mais novas do ubuntu)
    $ python -m pip install --user ansible
    $ python -m pip install --upgrade --user ansible
    $ ansible --version
    $ python -m pip show ansible
    $ ansible-galaxy collection list
    $ python -m pip install --user argcomplete
    $ ansible-galaxy collection install ansible.posix
    
    ```

- Criar uma chave RSA
    
    ```
    $ ssh-keygen
    $ ssh-copy-id -i ~/.ssh/id_rsa ubuntu@ip-host
    ```
- Remover solicitação de senha nos hosts
    
    ```
    $ sudo vi /etc/sudoers
    $ #User privilege specification
    $ user  ALL=(ALL:ALL) NOPASSWD:ALL
    $ %sudo   ALL=(ALL:ALL) NOPASSWD:ALL
    ```

- Desativar login por senha via SSH das vms linux

    ```
    $ sudo vi /etc/ssh/sshd_config
    
    #descomente as linhas abaixo e defina como no
    PermitRootLogin no
    PasswordAuthentication no
    PermitEmptyPasswords no
    ChallengeResponseAuthentication no
    ```

- Instalar python nos servers (para o ubuntu 22.04 ou superior não é necesssário)
    
    ```
    $ sudo yum install -y python
    $ sudo yum -y install python3.9
    $ sudo yum -y install python3.9-distutils
    $ sudo rm /usr/bin/python
    $ sudo ln -s python3.9 /usr/bin/python
    ```

- Criar arquivo hosts para testar a comunicação entre os servers
    
    ```
    $ sudo vi hosts
    Add os ips ou nome dos hosts no arquivo.
    ```
- Comando ad hoc de teste ansible
    
    ```
    $ ansible -i hosts all -m ping
    A saída desse comando tem que ser positiva.
    ```

## Provisioning

- Nas distribuições RHEL Foi necessário instalar o python-firewall para manipular o firewalld com o ansible.

- Nas distribuições RHEL Foi necessário instalar o libselinux-python para manipular o linux com o ansible.

- Nas distribuições RHEL Foi necessário instalar o python-dnf para manipular package manager dnf.

- Nas distribuições Debian Foi necessário instalar o python3-apt e python-apt para manipular os repo.

- Criar pastas e arquivos necessários 'projeto-k8s-ansible'

    ```
    $ mkdir projeto-k8s-ansible
    $ cd projeto-k8s-ansible
    $ mkdir provisioning
    $ cd provisioning
    $ mkdir roles
    $ touch hosts
    $ touch main.yml
    ```

- Criar roles provisioning

    ```
    $ cd projeto-k8s-ansible\provisioning\roles
    $ ansible-galaxy init configurando-instancias
    ```

- Adicionar configuração arquivo hosts

    ```
    $ vi projeto-k8s-ansible\provisioning\hosts
    $ Add
    [k8s-master]
    k8s-master

    [k8s-workers]
    k8s-worker1
    k8s-worker2
    ```

- Executanto o provisioning

    ```
    $ cd projeto-k8s-ansible\provisioning\
    $ ansible-playbook -i hosts main.yml
    ```

## Install_k8s

- Criar pastas e arquivos necessários 'install-k8s'

    ```
    $ cd projeto-k8s-ansible    
    $ mkdir install-k8s
    $ cd install-k8s
    $ mkdir roles
    $ touch hosts
    $ touch main.yml
    ```

- Criar roles install-k8s

    ```
    $ cd projeto-k8s-ansible\install-k8s\roles
    $ ansible-galaxy init install-k8s
    $ ansible-galaxy init create-cluster
    $ ansible-galaxy init join-workers
    ```

- Adicione seus hosts no arquivo hosts
    
    ```
    $ vi projeto-k8s-ansible\install-k8s\hosts
    [k8s-master]
    k8s-master

    [k8s-workers]
    k8s-worker1
    k8s-worker2

    [k8s-workers:vars]
    K8S_MASTER_NODE_IP= Adicionar o IP do seu MASTER
    K8S_API_SECURE_PORT=6443
    ```

- Adicionar a receita ao arquivo main.yml (Separamos em três roles)
  - Install-k8s
  - Create-cluster
  - Join-workers

- Adicionar a receita na role install-k8s
    
    ```
    $ cd projeto-k8s-ansible\install-k8s\roles\install-k8s
    $ ansible-galaxy init install-k8s
    $ ansible-galaxy init create-cluster
    $ ansible-galaxy init join-workers
    ```
## License
[MIT](https://choosealicense.com/licenses/mit/)