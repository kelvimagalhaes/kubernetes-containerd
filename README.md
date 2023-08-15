<h1 align="center"> kubernetes-containerd</h1>
Implementação do cluster kubernetes com Containerd como container runtime

## Descrição do Projeto

**Primeiro de tudo o que é o Kubernetes popularmente chamado de K8S ?**

É uma plataforma desenvolvida pela Google para gerenciar aplicativos em contêineres através de múltiplos hosts de um cluster e assim, ampliar a estabilidade e controle do serviço. Com ele se tornou possível o que por muito tempo não era possível, nativamente, através do Docker: criar balanceamento de cargas e a migração de contêineres sem perda de dados. Além disto é possível automatizar estas ações para ter alta disponibilidade dos serviços.

**Mesmo depois do Docker ter lançado o Swarm mode, porque o Kubernetes ainda é uma opção mais interessante?**

Sem dúvida o Docker swarm é uma opção interessante para criarmos clusters de contêineres e gerenciarmos de forma simples, mas o Kubernetes traz uma proposta bem mais automatizada para fazermos isto. Enquanto através do Docker Swarm você criará manualmente um cluster, o Kubernetes fará isto tudo automaticamente para você. Ele seguirá suas regras predefinidas (de uso de recursos ou de quantidade de Pods) para manter a estabilidade do serviço. Um segundo ponto é o isolamento dos volumes que garante que os dados de um Pod estejam da forma que estavam quando ele precisou ser reiniciado. Não mencionei anteriormente , mas, pelo Kubernetes também é possível definir namespaces para criarmos ambientes isolados de produção e testes. Assim, podemos limitar os recursos de cada ambiente, para que um não interfira no outro. E para finalizar, um ponto que achei interessante é a flexibilidade dele trabalhar com outros modelos de contêiner que não seja o Docker, como o rockets, containerid, cri-o.

Abaixo está a arquitetura do K8S, falaremos de cada uma delas no decorrer da instalação.

![Arquitetura-K8S](https://github.com/kelvimagalhaes/kubernetes-containerd/assets/69023011/ac0812c5-205e-4a8b-85a7-bb917255f9cb)


**O que é um cluster Kubernetes?**

Um cluster Kubernetes é um conjunto de nodes que trabalham juntos para executar todos os nossos pods. Um cluster Kubernetes é composto por nodes que podem ser tanto control plane quanto workers. O control plane é responsável por gerenciar o cluster, enquanto os workers são responsáveis por executar os pods que são criados no cluster pelos usuários.

Quando estamos pensando em um cluster Kubernetes, precisamos lembrar que a principal função do Kubernetes é orquestrar containers. O Kubernetes é um orquestrador de containers, sendo assim quando estamos falando de um cluster Kubernetes, estamos falando de um cluster de orquestradores de containers. Eu sempre gosto de pensar em um cluster Kubernetes como se fosse uma orquestra, onde temos uma pessoa regendo a orquestra, que é o control plane, e temos as pessoas musicistas, que estão executando os instrumentos, que são os workers.

Sendo assim, o control plane é responsável por gerenciar o cluster, como por exemplo:

Criar e gerenciar os recursos do cluster, como por exemplo, **namespaces, deployments, services, configmaps, secrets**, etc.

Gerenciar os **workers** do cluster.

Gerenciar a rede do cluster.

O **etcd** desempenha um papel crucial na manutenção da estabilidade e confiabilidade do cluster. Ele armazena as informações de configuração de todos os componentes do control plane, incluindo os detalhes dos serviços, pods e outros recursos do cluster. Graças ao seu design distribuído, o etcd é capaz de tolerar falhas e garantir a continuidade dos dados, mesmo em caso de falha de um ou mais nós. Além disso, ele suporta a comunicação segura entre os componentes do cluster, usando criptografia TLS para proteger os dados.

O **scheduler** é o componente responsável por decidir em qual nó os pods serão executados, levando em consideração os requisitos e os recursos disponíveis. O scheduler também monitora constantemente a situação do cluster e, se necessário, ajusta a distribuição dos pods para garantir a melhor utilização dos recursos e manter a harmonia entre os componentes.

O **controller-manager** é responsável por gerenciar os diferentes controladores que regulam o estado do cluster e mantêm tudo funcionando. Ele monitora constantemente o estado atual dos recursos e compara-os com o estado desejado, fazendo ajustes conforme necessário.

Onde está o **api-server** é o componente central que expõe a API do Kubernetes, permitindo que outros componentes do control plane, como o controller-manager e o scheduler, bem como ferramentas externas, se comuniquem e interajam com o cluster. O api-server é a principal interface de comunicação do Kubernetes, autenticando e autorizando solicitações, processando-as e fornecendo as respostas apropriadas. Ele garante que as informações sejam compartilhadas e acessadas de forma segura e eficiente, possibilitando uma colaboração harmoniosa entre todos os componentes do cluster.

Já no lado dos **workers**, as coisa são bem mais simples, pois a principal função deles é executar os pods que são criados no cluster pelos usuários. Nos workers nós temos, por padrão, os seguintes componentes do Kubernetes:

O **kubelet** é o agente que funciona em cada nó do cluster, garantindo que os containers estejam funcionando conforme o esperado dentro dos pods. Ele assume o controle de cada nó, garantindo que os containers estejam sendo executados conforme as instruções recebidas do control plane. Ele monitora constantemente o estado atual dos pods e compara-os com o estado desejado. Caso haja alguma divergência, o kubelet faz os ajustes necessários para que os containers sigam funcionando perfeitamente.

O **kube-proxy**, que é o componente responsável fazer ser possível que os pods e os services se comuniquem entre si e com o mundo externo. Ele observa o control plane para identificar mudanças na configuração dos serviços e, em seguida, atualiza as regras de encaminhamento de tráfego para garantir que tudo continue fluindo conforme o esperado.

Todos os **pods** de nossas aplicações.

## Instalação do Kubeadm

Nós iremos realizar a instalação do Kubernetes utilizando o kubeadm, que é uma das formas mais antigas para a criação de um cluster Kubernetes. Mas existem outras formas de instalar o Kubernetes, vou detalhar algumas delas aqui:

kubeadm: É uma ferramenta para criar e gerenciar um cluster Kubernetes em vários nós. Ele automatiza muitas das tarefas de configuração do cluster, incluindo a instalação do control plane e dos nodes. É altamente configurável e pode ser usado para criar clusters personalizados.

Kubespray: É uma ferramenta que usa o Ansible para implantar e gerenciar um cluster Kubernetes em vários nós. Ele oferece muitas opções para personalizar a instalação do cluster, incluindo a escolha do provedor de rede, o número de réplicas do control plane, o tipo de armazenamento e muito mais. É uma boa opção para implantar um cluster em vários ambientes, incluindo nuvens públicas e privadas.

Cloud Providers: Muitos provedores de nuvem, como AWS, Google Cloud Platform e Microsoft Azure, oferecem opções para implantar um cluster Kubernetes em sua infraestrutura. Eles geralmente fornecem modelos predefinidos que podem ser usados para implantar um cluster com apenas alguns cliques. Alguns provedores de nuvem também oferecem serviços gerenciados de Kubernetes que lidam com toda a configuração e gerenciamento do cluster.

Kubernetes Gerenciados: São serviços gerenciados oferecidos por alguns provedores de nuvem, como Amazon EKS, o GKE do Google Cloud e o AKS, da Azure. Eles oferecem um cluster Kubernetes gerenciado onde você só precisa se preocupar em implantar e gerenciar seus aplicativos. Esses serviços lidam com a configuração, atualização e manutenção do cluster para você. Nesse caso, você não tem que gerenciar o control plane do cluster, pois ele é gerenciado pelo provedor de nuvem.

Kops: É uma ferramenta para implantar e gerenciar clusters Kubernetes na nuvem. Ele foi projetado especificamente para implantação em nuvens públicas como AWS, GCP e Azure. Kops permite criar, atualizar e gerenciar clusters Kubernetes na nuvem. Algumas das principais vantagens do uso do Kops são a personalização, escalabilidade e segurança. No entanto, o uso do Kops pode ser mais complexo do que outras opções de instalação do Kubernetes, especialmente se você não estiver familiarizado com a nuvem em que está implantando.

Minikube e kind: São ferramentas que permitem criar um cluster Kubernetes localmente, em um único nó. São úteis para testar e aprender sobre o Kubernetes, pois você pode criar um cluster em poucos minutos e começar a implantar aplicativos imediatamente. Elas também são úteis para pessoas desenvolvedoras que precisam testar suas aplicações em um ambiente Kubernetes sem precisar configurar um cluster em um ambiente de produção.

Existem diversas formar de criar um cluster K8S, aqui, o objetivo vai ser criar o cluster de forma on-premisse utilizando o kubeadm. O kubeadm é uma ferramenta com o objetivo de facilitar a criação de um cluster K8S padrão, que segue todos os requisitos de um cluster certificado. Ou seja, vamos ter o básico de um cluster K8S validado pela Cloud Native Computing Foundation. Mas também vamos usar o kubeadm pra alguns processos de manutenção do cluster, como renovação de certificados e atualizações do cluster. Podemos utilizar o kubeadm em qualquer abordagem onpremisse de uso do K8S, seja máquinas virtuais, máquinas baremetal e até mesmo Raspberry Pi.

## Setup do Ambiente

Vamos criar um cluster Kubernetes utilizando 3 máquinas, uma máquina vai ter o papel de Control Plane e as outras duas de Worker Nodes. Lembrando que dessa forma eu não estou criando um cluster com alta disponibilidade ou HA. Pois eu tenho apenas um control plane e caso ele fique fora do ar, o cluster vai ficar inoperável. Por isso usaremos esse ambiente apenas para estudo. NUNCA em PRODUÇÃO

## Requisitos da Instalação

Abaixo segue os requisitos mínimos pra cada máquina:

- Máquina Linux (aqui no caso vou utilizar Debian 11)
- 2 GB de memória RAM
- 2 CPUs
- Conexão de rede entre as máquinas
- Hostname, endereço MAC e product_uuid únicos pra cada nó.
- Swap desabilitado
- 

**Certas portas terão que ser liberadas caso os servidores não estejam no mesmo enlace de rede;**

- Porta 6443: É a porta padrão usada pelo Kubernetes API Server para se comunicar com os componentes do cluster. É a porta principal usada para gerenciar o cluster e deve estar sempre aberta.

- Portas 10250-10255: Essas portas são usadas pelo kubelet para se comunicar com o control plane do Kubernetes. A porta 10250 é usada para comunicação de leitura/gravação e a porta 10255 é usada apenas para comunicação de leitura.

- Porta 30000-32767: Essas portas são usadas para serviços NodePort que precisam ser acessíveis fora do cluster. O Kubernetes aloca uma porta aleatória dentro desse intervalo para cada serviço NodePort e redireciona o tráfego para o pod correspondente.

- Porta 2379-2380: Essas portas são usadas pelo etcd, o banco de dados de chave-valor distribuído usado pelo control plane do Kubernetes. A porta 2379 é usada para comunicação de leitura/gravação e a porta 2380 é usada apenas para comunicação de eleição.

![Portas-Default](https://github.com/kelvimagalhaes/kubernetes-containerd/assets/69023011/d0151d15-6703-4b0b-86e5-c1540f356f32)

Além dessas portas, é preciso ter a porta do Container Network Interface(CNI) aberta também, mas isso vária pra cada solução. Aqui no caso, vamos usar o Weave Net, as portas que é preciso liberar são as UDP 6783/6784 , TCP 6783.

- O cluster será implementado nos seguintes servidores;

```
BA125L - Node K8S Control-Plane
BA126L - Node K8S Worker
BA127L - Node K8S Worker
```
## Instalação

- Agora vamos para o passo a passo da instalação

## Instalando os pacotes do Kubernetes

- Vamos desativar a utilização de swap no sistema. Isso é necessário porque o Kubernetes não trabalha bem com swap ativado:

```
sudo swapoff -a
```

- Vamos carregar os módulos do kernel necessários para o funcionamento do kubernetes

## Carregando os módulos do kernel

```
~#cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF
sudo modprobe overlay
sudo modprobe br_netfilter
```

## Configurando parâmetros do sistema

- Configuração dos parâmetros do sysctl, fica mantido mesmo com reboot da máquina.

```
~#cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF


# Aplica as definições do sysctl sem reiniciar a máquina
~#sysctl --system
```

## Instalação

- Pacotes do Kubernetes

```
~#apt update && apt-get install apt-transport-https curl gnupg2
~#curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
~#touch /etc/apt/sources.list.d/k8s.list
~#echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/k8s.list
~#apt-get update && apt-get install -y kubeadm kubectl kubelet

OBS: Escolher versão específica do pacote ;
~#apt list --all-versions "pacote" 

EX:
apt-get install kubectl=1.26.0-00 kubeadm=1.26.0-00 kubelet=1.26.0-00
```

- Comando para que não atualize os pacotes abaixo de forma automática e quebre nosso cluster.

```
~#apt-mark hold kubelet kubeadm kubectl
```

## Instalando Container Runtime (Containerd)

O primeiro passo, é instalar em TODAS as máquinas o container runtime, ou seja, quem vai executar os containers solicitados pelo kubelet. Aqui o container runtime utilizado é o Containerd, mas você também pode usar o Docker e o CRI-O.

OBS: A partir da versão 1.26 do Kubernetes, foi removido o suporte ao CRI v1alpha2 e ao Containerd 1.5. E até o momento, o repositório oficial do debian não tem o Containerd 1.6, então precisamos usar o repositório do Docker pra instalar o ContainerD.

https://kubernetes.io/blog/2022/12/09/kubernetes-v1-26-release/#cri-v1alpha2-removed

- Instalando pacotes necessários

```
~#apt-get install lsb-release ca-certificates sudo software-properties-common
```
- LINK para instalação do containerd 1.6

https://blog.kubesimplify.com/kubernetes-126

```
~#curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
~#sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable" 
~#sudo apt update -y
~#sudo apt install -y containerd.io
~#sudo mkdir -p /etc/containerd
~#containerd config default | sudo tee /etc/containerd/config.toml
```
- Restart o serviço containerd usando o comando abaixo.

```
sudo systemctl restart containerd
sudo systemctl enable containerd
```

- verifique o status do serviço containerd.

```
sudo systemctl status containerd
```

# Configurando o Containerd

- Configuração padrão do Containerd

```
~#sudo mkdir -p /etc/containerd && containerd config default | sudo tee /etc/containerd/config.toml
```

- Alterar o arquivo de configuração pra configurar o systemd cgroup driver. Sem isso o Containerd não gerencia corretamente os recursos computacionais e vai reiniciar em loop.

```
~#sed -i 's/SystemdCgroup = false/SystemdCgroup = true/g' /etc/containerd/config.toml
```

- Agora é preciso reiniciar o containerd

```
~#systemctl restart containerd
~#systemctl enable containerd
```

- Um detalhe muito importante é fazer o containerd fazer o uso do registry privado da AGETEC, toda configuração foi feita através do arquivo config.toml, dessa forma achei relevante deixar ele aqui;

```
~#cat /etc/containerd/config.toml
```

```
disabled_plugins = []
imports = []
oom_score = 0
plugin_dir = "" 
required_plugins = []
root = "/var/lib/containerd" 
state = "/run/containerd" 
temp = "" 
version = 2

[cgroup]
  path = "" 

[debug]
  address = "" 
  format = "" 
  gid = 0
  level = "" 
  uid = 0

[grpc]
  address = "/run/containerd/containerd.sock" 
  gid = 0
  max_recv_message_size = 16777216
  max_send_message_size = 16777216
  tcp_address = "" 
  tcp_tls_ca = "" 
  tcp_tls_cert = "" 
  tcp_tls_key = "" 
  uid = 0

[metrics]
  address = "" 
  grpc_histogram = false

[plugins]

  [plugins."io.containerd.gc.v1.scheduler"]
    deletion_threshold = 0
    mutation_threshold = 100
    pause_threshold = 0.02
    schedule_delay = "0s" 
    startup_delay = "100ms" 

  [plugins."io.containerd.grpc.v1.cri"]
    device_ownership_from_security_context = false
    disable_apparmor = false
    disable_cgroup = false
    disable_hugetlb_controller = true
    disable_proc_mount = false
    disable_tcp_service = true
    enable_selinux = false
    enable_tls_streaming = false
    enable_unprivileged_icmp = false
    enable_unprivileged_ports = false
    ignore_image_defined_volumes = false
    max_concurrent_downloads = 3
    max_container_log_line_size = 16384
    netns_mounts_under_state_dir = false
    restrict_oom_score_adj = false
    sandbox_image = "registry.k8s.io/pause:3.6" 
    selinux_category_range = 1024
    stats_collect_period = 10
    stream_idle_timeout = "4h0m0s" 
    stream_server_address = "127.0.0.1" 
    stream_server_port = "0" 
    systemd_cgroup = false
    tolerate_missing_hugetlb_controller = true
    unset_seccomp_profile = "" 

    [plugins."io.containerd.grpc.v1.cri".cni]
      bin_dir = "/opt/cni/bin" 
      conf_dir = "/etc/cni/net.d" 
      conf_template = "" 
      ip_pref = "" 
      max_conf_num = 1

    [plugins."io.containerd.grpc.v1.cri".containerd]
      default_runtime_name = "runc" 
      disable_snapshot_annotations = true
      discard_unpacked_layers = false
      ignore_rdt_not_enabled_errors = false
      no_pivot = false
      snapshotter = "overlayfs" 

      [plugins."io.containerd.grpc.v1.cri".containerd.default_runtime]
        base_runtime_spec = "" 
        cni_conf_dir = "" 
        cni_max_conf_num = 0
        container_annotations = []
        pod_annotations = []
        privileged_without_host_devices = false
        runtime_engine = "" 
        runtime_path = "" 
        runtime_root = "" 
        runtime_type = "" 

        [plugins."io.containerd.grpc.v1.cri".containerd.default_runtime.options]

      [plugins."io.containerd.grpc.v1.cri".containerd.runtimes]

        [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
          base_runtime_spec = "" 
          cni_conf_dir = "" 
          cni_max_conf_num = 0
          container_annotations = []
          pod_annotations = []
          privileged_without_host_devices = false
          runtime_engine = "" 
          runtime_path = "" 
          runtime_root = "" 
          runtime_type = "io.containerd.runc.v2" 

          [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
            BinaryName = "" 
            CriuImagePath = "" 
            CriuPath = "" 
            CriuWorkPath = "" 
            IoGid = 0
            IoUid = 0
            NoNewKeyring = false
            NoPivotRoot = false
            Root = "" 
            ShimCgroup = "" 
            SystemdCgroup = true

      [plugins."io.containerd.grpc.v1.cri".containerd.untrusted_workload_runtime]
        base_runtime_spec = "" 
        cni_conf_dir = "" 
        cni_max_conf_num = 0
        container_annotations = []
        pod_annotations = []
        privileged_without_host_devices = false
        runtime_engine = "" 
        runtime_path = "" 
        runtime_root = "" 
        runtime_type = "" 

        [plugins."io.containerd.grpc.v1.cri".containerd.untrusted_workload_runtime.options]

    [plugins."io.containerd.grpc.v1.cri".image_decryption]
      key_model = "node" 

    [plugins."io.containerd.grpc.v1.cri".registry]
      config_path = "" 

      [plugins."io.containerd.grpc.v1.cri".registry.auths]

      [plugins."io.containerd.grpc.v1.cri".registry.configs."registry.campogrande.ms.gov.br".auth]
            auth = "amVua2luc19sb2NhbDpFbWNBdGo=" 

      [plugins."io.containerd.grpc.v1.cri".registry.headers]

      [plugins."io.containerd.grpc.v1.cri".registry.mirrors]
        [plugins."io.containerd.grpc.v1.cri".registry.mirrors."registry.campogrande.ms.gov.br"]
          endpoint = ["https://registry.campogrande.ms.gov.br"]
    [plugins."io.containerd.grpc.v1.cri".x509_key_pair_streaming]
      tls_cert_file = "" 
      tls_key_file = "" 

  [plugins."io.containerd.internal.v1.opt"]
    path = "/opt/containerd" 

  [plugins."io.containerd.internal.v1.restart"]
    interval = "10s" 

  [plugins."io.containerd.internal.v1.tracing"]
    sampling_ratio = 1.0
    service_name = "containerd" 

  [plugins."io.containerd.metadata.v1.bolt"]
    content_sharing_policy = "shared" 

  [plugins."io.containerd.monitor.v1.cgroups"]
    no_prometheus = false

  [plugins."io.containerd.runtime.v1.linux"]
    no_shim = false
    runtime = "runc" 
    runtime_root = "" 
    shim = "containerd-shim" 
    shim_debug = false

  [plugins."io.containerd.runtime.v2.task"]
    platforms = ["linux/amd64"]
    sched_core = false

  [plugins."io.containerd.service.v1.diff-service"]
    default = ["walking"]

  [plugins."io.containerd.service.v1.tasks-service"]
    rdt_config_file = "" 

  [plugins."io.containerd.snapshotter.v1.aufs"]
    root_path = "" 

  [plugins."io.containerd.snapshotter.v1.btrfs"]
    root_path = "" 

  [plugins."io.containerd.snapshotter.v1.devmapper"]
    async_remove = false
    base_image_size = "" 
    discard_blocks = false
    fs_options = "" 
    fs_type = "" 
    pool_name = "" 
    root_path = "" 

  [plugins."io.containerd.snapshotter.v1.native"]
    root_path = "" 

  [plugins."io.containerd.snapshotter.v1.overlayfs"]
    root_path = "" 
    upperdir_label = false

  [plugins."io.containerd.snapshotter.v1.zfs"]
    root_path = "" 

  [plugins."io.containerd.tracing.processor.v1.otlp"]
    endpoint = "" 
    insecure = false
    protocol = "" 

[proxy_plugins]

[stream_processors]

  [stream_processors."io.containerd.ocicrypt.decoder.v1.tar"]
    accepts = ["application/vnd.oci.image.layer.v1.tar+encrypted"]
    args = ["--decryption-keys-path", "/etc/containerd/ocicrypt/keys"]
    env = ["OCICRYPT_KEYPROVIDER_CONFIG=/etc/containerd/ocicrypt/ocicrypt_keyprovider.conf"]
    path = "ctd-decoder" 
    returns = "application/vnd.oci.image.layer.v1.tar" 

  [stream_processors."io.containerd.ocicrypt.decoder.v1.tar.gzip"]
    accepts = ["application/vnd.oci.image.layer.v1.tar+gzip+encrypted"]
    args = ["--decryption-keys-path", "/etc/containerd/ocicrypt/keys"]
    env = ["OCICRYPT_KEYPROVIDER_CONFIG=/etc/containerd/ocicrypt/ocicrypt_keyprovider.conf"]
    path = "ctd-decoder" 
    returns = "application/vnd.oci.image.layer.v1.tar+gzip" 

[timeouts]
  "io.containerd.timeout.bolt.open" = "0s" 
  "io.containerd.timeout.shim.cleanup" = "5s" 
  "io.containerd.timeout.shim.load" = "5s" 
  "io.containerd.timeout.shim.shutdown" = "3s" 
  "io.containerd.timeout.task.state" = "2s" 

[ttrpc]
  address = "" 
  gid = 0
  uid = 0
```
# Habilitando o serviço do kubelet

```
~#sudo systemctl enable --now kubelet
```

# Iniciando o cluster Kubernetes

- Agora que todos os elementos estão instalados, tá na hora de iniciar o cluster Kubernetes, então vamos executar o comando de inicialização do cluster. Esse comando vai ser executado APENAS NA MÁQUINA QUE VAI SER O CONTROL PLANE !!!

# Comando de inicialização

```
~#kubeadm init

OBS: Recomendo especificar a rede em que o cluster irá trabalhar. EX;
~#kubeadm init --pod-network-cidr=50.50.0.0/16 --apiserver-advertise-address=10.0.0.125
~#kubeadm init --pod-network-cidr=50.50.0.0/16 --upload-certs --kubernetes-version=v1.26.0 --cri-socket unix:///run/containerd/containerd.sock
```

- Podemos incluir alguns parâmetros:

- --apiserver-cert-extra-sans ⇒ Inclui o IP ou domínio como acesso válido no certificado do kube-api. Se você tem mais de 1 adaptador de rede no cluster (um interno e um externo por exemplo) é importante que você utilize.

- --apiserver-advertise-address ⇒ Define o adaptador de rede que vai ser responsável por se comunicar com o cluster.

Iniciado o cluster, é preciso copiar as configurações de acesso do cluster para o kubectl

# Configurando o kubectl

```
~#mkdir -p $HOME/.kube
~#cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
~#chown $(id -u):$(id -g) $HOME/.kube/config
```
Agora que o próximo passo é incluir os worker nodes no cluster, pra isso no output de inicialização do cluster já aparece o comando kubeadm join pra executar nos worker nodes, caso perda ou precisar do comando de novo, é só executar no control plane o comando token create

# Gerando o comando join e executando nos nodes

```
~#kubeadm token create --print-join-command
```

- Agora, é só executar o kubectl get nodes, vamos ver que o control plane e os nodes não estão prontos, pra resolver isso, é preciso instalar o Container Network Interface ou CNI, vamos usar o Weave Net, ele utiliza o protocolo de roteamento de pacotes do kubernetes para criar uma rede entre os pods.

# Instalação do CNI 

```
~#kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
```

**IMPORTANTE: firewall ---> Deve ser liberada as portas TCP 6783 e UDP 6783/6784 para o Weave Net**

Agora sim, o cluster Kubernetes instalado e funcionando. Segue a imagem dos NODES e NAMESPACES;

![Captura de tela de 2023-08-15 16-36-12](https://github.com/kelvimagalhaes/kubernetes-containerd/assets/69023011/afe8875e-b693-4dfc-a845-9dee23756679)

Antes de mais nada, acho interessante abordar os métodos de serviços para que quando começarmos a colocar a mão na massa, não fique difícil a compreensão. Lembrando que todo o laboratório será gerenciado através do serviço LoadBalancer.

# Services

Os Services no Kubernetes são uma abstração que define um conjunto lógico de Pods e uma política para acessá-los. Eles permitem que você exponha uma ou mais Pods para serem acessados por outros Pods, independentemente de onde eles estejam em execução no cluster.

Os Services são definidos usando a API do Kubernetes, normalmente através de um arquivo de manifesto YAML.

# Tipos de Services

Existem quatro tipos principais de Services:

- ClusterIP (padrão): Expõe o Service em um IP interno no cluster. Este tipo torna o Service acessível apenas dentro do cluster.

- NodePort: Expõe o Service na mesma porta de cada Node selecionado no cluster usando NAT. Torna o Service acessível de fora do cluster usando :.

- LoadBalancer: Cria um balanceador de carga externo no ambiente de nuvem atual (se suportado) e atribui um IP fixo, externo ao cluster, ao Service.

- ExternalName: Mapeia o Service para o conteúdo do campo externalName (por exemplo, foo.bar.example.com), retornando um registro CNAME com seu valor.

# Como os Services funcionam

Os Services no Kubernetes fornecem uma abstração que define um conjunto lógico de Pods e uma política para acessá-los. Os conjuntos de Pods são determinados por meio de seletores de rótulo (Label Selectors). Embora cada Pod tenha um endereço IP único, esses IPs não são expostos fora do cluster sem um serviço.

Sempre é bom reforçar a importância dos Labels no Kubernetes, pois eles são a base para a maioria das operações no Kubernetes, então vamos tratar com carinho as Labels.

# Dica de OURO !!!

Página que ajudou muito durante o curso foi a https://kubernetes.io/pt-br/docs/reference/kubectl/cheatsheet/ . Com ela ficou mais fácil gerenciar o cluster, também descobrir o que cada comando faz por debaixo dos panos. Verdadeiro canivete suíço !!!!

Se estiver com dúvida sobre a apiVersion de cada manifesto, através do comando kubectl api-resources terá todas as respostas.
