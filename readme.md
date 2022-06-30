# 1. Kubernetes
<br>

- Realiza a implantação, escalonamento, gerenciamento e orquestração de containers;
- - trabalha com qualquer container runtime;

Requisitos: Docker for Windows, minikube, kubectl;

----

# 2. Sumário
<br>

- [1. Kubernetes](#1-kubernetes)
- [2. Sumário](#2-sumário)
- [3. Pod](#3-pod)
- [4. Node/Nós](#4-nodenós)
- [5. Arquitetura do Kubernetes](#5-arquitetura-do-kubernetes)
- [6. Minikube](#6-minikube)
- [7. Kubectl](#7-kubectl)
- [8. Arquivo manifesto - yaml](#8-arquivo-manifesto---yaml)
- [9. Replicaset.yaml](#9-replicasetyaml)
  - [9.1. Criando replicaset1.yml](#91-criando-replicaset1yml)
  - [9.2. Escalando aplicação](#92-escalando-aplicação)
- [Deployment](#deployment)
  - [Estratégia](#estratégia)
  - [Criando o Deployment](#criando-o-deployment)
  - [Alterando o Deployment](#alterando-o-deployment)
- [Services](#services)
  - [Tipos de serviço](#tipos-de-serviço)
  - [Como descobrir e acessar serviços no Kubernetes?](#como-descobrir-e-acessar-serviços-no-kubernetes)
  - [Criar o Services1.yaml](#criar-o-services1yaml)

----

# 3. Pod
<br>

É a menor e mais básica estrutura do Kubernetes, semelhante ao container no Docker;

O pod consiste de um ou mais containers, recursos de armazenamento e um único endereço IP;

Um pod representa uma única instância de um processo em execução no seu cluster.

Pods não são projetados para permanecer em execução para sempre, depois de encerrados não podem ser recuperados.

**Ciclo de vida** -> Pending, Running, Succeeded, Failed, Unknow;

- **Pending** - criado e aceito pelo cluster, mas um ou mais contêineres ainda não estão em execução;
- **Running** - pod foi alocado a um Nó, e todos os contêineres foram criados. Pelo menos um contêiner está em execução, no processo de iniciar ou reiniciando;
- **Failed** - Todos os contêineres no pod foram encerrados e pelo menos um com falha;
- **Unknow** - O estado não pode ser determinado;

---

# 4. Node/Nós
<br>

Por padrão, os pods são executados nos Nodes ou Nós, ou seja, no pool de nós padrão do cluster;

Os nós são recrusos de hardware e podem ser uma maquina virtual ou uma máquina física;

Os pods não saõ reestritos a um Nó, logo pode existir mais de um Nó.

O conjunto de Nós se chama **Cluster**;

![cluster](cluster.png)

No cluster existem **2 tipos de nós**:

- **Worker** - Nós que executam os containers nos pods;
- **Master** - Executa os componentes do plano de controle;

Os comando são enviados ao Nó Master (interagimso com o Nó master) e ele define os Nós Workers.

---

# 5. Arquitetura do Kubernetes
<br>

- **kubeadm** - É a ferramenta que automatiza grande parte do processo de criação do cluster.
- **kubelet** - Componente essencial que lida com a execução de pods; Atua como um agente em cada node, intermediando as trocas de mensagens entre API Server e o Docker runtime;
- **kubectl** - É a ferramenta CLI de interação com o cluster kubernetes. Permite executar comandos em clusters, implantar aplicativos, inspecionar e gerenciar recursos de cluster e visualizar logs.
- **etcd** - Provê um sistema distribuído e compartilhado para armazenar informações do estado do cluster. Armazena as informações no formato chave-valor;
- **API Server** - Provê todos os serviços do kubernetes. É um serviço REST. Valida e configura dados para os objetos de API que incluem pods, serviços, controladores de replicação e outros;
- **Controll Manager** - Executa funções em nível de cluster, como replicar componentes, acompanhar nós de trabalho, lidar com falhas, e assim por diante;
- **Scheduler** - Excalona os pods para serem executados nos Nodes.

![arquitetura](arquitetura.png)

---

# 6. Minikube
<br>

Existem vários utilitários para executar o kubernetes, o minikube é um deles. Sua vantagem é que é fácil de usar, oferece suporte a diversas plataformas, tem muitas features e muita documentação escrita. Mas não é possível trabalhar com múltiplos Nós e o tempo de inicialização é lento.

Para verificar e iniciar o Minikube (deve ter sido instalado previamente), abrir um terminal e usar os comandos:

    minikube status
    minikube start

Obs. o Docker precisa estar rodando.

# 7. Kubectl
<br>

Este também precisa ser instalado, mas é utilizado para realizar comandos no kubernetes (clusters, nós, pods).

Exemplo de Comandos:

    kubectl cluster infor
    kubectl get all
    kubectl run nginx --imagem nginx
    kubectl apply -f newpod.yaml


----

# 8. Arquivo manifesto - yaml
<br>

Arquivo com definições de deplyments, services, pods, namespaces, replicasets, configmaps, secrets, nodes e outros objetos que desejamos definir.

Ex.:
```yml
apiVersion: v1 #indica a versão da API Kubernetes usada para criar o objeto

Kind: Pod #define o tipo de objeto a ser criado: Pod, Deployments, ReplicaSet, Service, Namescpace

metadata:
    name: pod-ngix #define dados do objeto como name, labels....

spec:               #define especificações do objeto a ser  
    containers:     #criado, varia de acordo com o objeto
        - image:    #containers é uma lista ou array,
        name:       #pois um pod pode conter múltiplos
        ports:      #containers (listas são indicadas com "-")
            - containerPort:
```
---

# 9. Replicaset.yaml
<br>

Esse arquivo é semelhante ao docker-compose. Aqui são descritas as orientações para subir os Pods(contêineres) dentro do Node e Cluster.
Aqui é possível configurar para que um Pod seja recriado automáticamente, assim em caso de parada não será necessário recria-lo manualmente.

O replicaset consegue diferenciar os Pods través de labels;

**Labels** são pares chave-valor anexados a Pods, ReplicaSet e serviços. São usados como atributos de identificação;

----

## 9.1. Criando replicaset1.yml

Aqui é interessante ter a extensão Kubernetes para facilitar, criando um esquema inicial no arquivo.
Crie o arquivo replicaset1.yaml e nele digite "replica"  com a extensão instalada ele vai te sugerir um arquivo a ser criado.

```yaml
apiVersion: apps/v1 #define a versão
kind: ReplicaSet #tipo
metadata:
  name: redis-replicaset #nome do replicaset
spec:
  template:
    metadata:
      name: mypod #definições do pod (nome)
      labels:
        app: myApp #label é a identificação do pod
        type: database
    spec:
      containers:
        - name: cont-redis #nome do contêiner
          image: redis #imagem que será utilizada
          ports:
            - containerPort: 80 #porta
  replicas: 3 #quantidade de replicas
  selector:
    matchLabels:
      type: database #qual tipo de label será replicado
```

Comandos para executar o replicaset.yaml:

    kubectl apply -f replicaset1.yaml
    kubectl create - f replicaset1.yaml

Comando para ver os pods em funcionamento:

    kubectl get all

Caso queria deletar um pod para testar se outro será criado:

    kubectl delete pod redis-replicaset-<id_pod>

O número do pod você verifica com o `kubectl get all`;

Caso seja feita alguma alteração no replicaset1.yaml, por exemplo alterar a versão do redis, os Pods só terão sua versão alterada após sua destruição, o que **não ocorre automáticamnete**.

---

## 9.2. Escalando aplicação
<br>

Se quiser aumentar o número de pods use o comando abaixo informando nome e quantidade:

    kubectl scale replicaset redis-replicaset --replicas=6

> O replicaset não permite acrescentar pods, somente pelo que foi atribuido no arquivo e pelo scale. Pods criados manualmente terão status "terminated";



----

# Deployment
<br>

Um deployment é um conceito de nível superior que gerencia ReplicaSets e fornce atualizações declarativas para Pods, juntamente com outros recursos úteis.

Pode-se utilizar o Deploymente para aplicar atualizações continuas de aplicativos em Pods e, da mesma forma, reverter um atualização que falhou.

----

## Estratégia
<br>

Uma estratégia de Deployment é uma maneira de alterar ou atualizar um aplicativo. O objetivo é fazer a alteração sem tempo de inatividade de forma que o usuário mal perceba as alterações feitas.

1. **Recreate**: Encerra as instâncias do Pod atualmente em execução e as recria com a nova versão;
2. **RollingUpdate**: Permite uma migração ordenada de uma versão de um aplicativo para uma versão mais recente.(Estratégia padrão do Kubernetes);
3. **Blue/Green**: São criados dois ambientes separados, mas idênticos. Um ambiente (azul) que executa a versão atual do aplicativo e um ambiente (verde) está executando a nova versão do aplicativo;
4. **Canary**: É uma estratégia de implantação que libera um aplicativo ou serviço de forma incremental para um subconjunto de usuários.

----

## Criando o Deployment
<br>

Crie o arquivo "deployment1.yaml" com o seguinte conteúdo:

```yml
apiVersion: apps/v1
kind: Deployment #tipo deployment
metadata:
  name: nginx-deploy
spec:
  replicas: 2
  template:
    metadata:
      name: nginx-pod
      labels:
        app: nginx1
    spec:
      containers:
      - name: nginx-container
        image: nginx
        ports:
        - containerPort: 80
  selector:
    matchLabels:
      app: nginx1
```

Execute o comando:

  kubectl apply -f deployment1.yaml

---

## Alterando o Deployment
<br>

Altera o valor da imagem nginx para `nginx:1.14.2`

```yml
apiVersion: apps/v1

  #...
        image: nginx:1.14.2
  #...
```

Execute o comando para aplicar as alterações, e na sequência um "get all" para ver as alterações:

    kubectl apply -f deployment1.yaml
    kubectl get all

Se precisar retornar a versão anterior use o comando abaixo:

    kubectl rollout undo deploy nginx-deploy -n default
    kubectl rollout undo deploy <name_metadata> -n <namespace>

---

# Services
<br>

É uma implantação lógica para um grupo implantado de pods em um cluster.

Um service permite que um grupo de pods receba um nome e um endereço de IP exclusivo(clusterIP) e estável;

Enquanto o serviço estiver em execução esse endereço de IP não será alterado mesmo se o Pod morrer ou for excluído;

Conectam um conjunto de pods a um nome de serviço abstrato e um endereço IP e fornecem a descoberta e roteamento entre pods.

Expõe uma Interface a esses pods que permite o acesso à rede de dentro do cluster ou entre processos externos e o serviço.

Principais atributos:


1. **Seletor de labels** que localiza os pods;
2. O endereço IP do **ClusterIP** e o número de porta atribuído;
3. Definições de porta;
4. Mapeamento opcional de portas de entrada para um **targetPort**;
   
-------

## Tipos de serviço
<br>

1. **ClusterIP** - Estabelece uma conexão entre diferentes serviços e aplicativos usando um IP Virtual de cluster interno. Esse tipo de serviço só é acessível dentro do cluster;
2. **NodePort** - Permite acessibilidade externa a um Pod em um nó recebendo uma solicitação externa de clientes ou usuários e mapeando na porta de destino e na porta de serviço do Pod. Também expõe um aplicativo externamente com a ajuda de um NodeIp e NodePort que expões uma porta em cada Node.
3. **Loadbalancer** - Compartilha as solicitações do cliente nos servidores de forma contínua, eificiente e uniforme para proteger contra o uso excessivo de recursos do servidor;
4. **ExternalName** - Expõe o serviço usando um nome arbitrário retornando um registro **CNAME** com o nome

------

## Como descobrir e acessar serviços no Kubernetes?
<br>

1. **Por meio do DNS do cluster**

Esse método é o recomendado de descoberta de serviços. Para usar esse método, primeiro um servidor DNS deve ser instalado no cluster.

O Servidor DNS monitora a API do kubernetes e quando um novo serviço é criado, seu nome fica disponível para facilitar a resolução dos aplicativos solicitados.

2. **Por meio da variáveis de ambiente nos Nodes/Nós**

Este método depende de usar *kubelet* e adicionar variáveis de ambinte para cada serviço ativo para acda nó em que um Pod está sendo executado.

--------

## Criar o Services1.yaml
<br>

Antes vamos criar um novo Deploysment chamado "apachedploy.yaml":

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: apache-deploy
  labels:
    app: apache-deploy
spec:
  replicas: 3
  selector:
    matchLabels:
      app: apache-webserver
  template:
    metadata:
      labels:
        app: apache-webserver
    spec:
      containers:
      - name: apache
        image: bitnami/apache:latest
        ports:
        - containerPort: 80
```

Rode esse Deployment:

    kubectl apply -f .\apachedeploy.yaml 


Agora crie o arquivo apacheservice.yaml abaixo, para ser aplicado no deploy:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: apache-service
spec:
  type: ClusterIP
  selector:
    app: apache-deploy
  ports:
  - port: 8080
    protocol: TCP
    targetPort: 8080
```

Execute o Service:

    kubectl apply -f .\apacheservice.yaml

----