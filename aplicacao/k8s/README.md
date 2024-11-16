# Deploy da aplicação: 
- Aqui está o comando para aplicar o projeto, com todos os arquivos. Será necessário que entre no diretório k8s/ para executar os comando kubeclt
```bash
# inciando a aplicação
kubectl apply -f ./deploy/app.yml -f ./configmap/configmaps.yml -f ./deploy/db-mysql/statefulsets-dbmysql.yml -f ./secrets/secrets.yml -f ./acesso-nodeport/service-nodeport.yml -f ./deploy/db-mysql/configmap-db.yml -f ./deploy/db-mysql/secret-db.yml

# deletando a aplicação
kubectl delete -f ./deploy/app.yml -f ./configmap/configmaps.yml -f ./deploy/db-mysql/statefulsets-dbmysql.yml -f ./secrets/secrets.yml -f ./acesso-nodeport/service-nodeport.yml -f ./deploy/db-mysql/configmap-db.yml -f ./deploy/db-mysql/secret-db.yml

```

# Kubernetes Dashboard com minikube:
```bash
# adicionando addons do metrics-server
minikube addons enable metrics-server

# iniciando a dashboard
minikube dashboard
```
- Caso queira montar uma dashboard sem ser com o minikube, no diretório **kubernetes-dashboard/** tem um tutorial.

# Explicando as configurações da aplicação.

- Entre no diretório da aplicação e execute os seguintes comandos:
```bash

# Realizei o build da imagem que coloquei no meu docker hub. Pois o kubernetes não tem a opção de build igual tem no docker, então será necessário fazer um build da imamge e aidicionar a um repositório

docker login
#username: *seu user do docker-hub*
#password: * sua senha do docker hub

# saída esperada após realizar o login:
Username: crisdockerz
Password: 
WARNING! Your password will be stored unencrypted in /home/cris/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded!


docker build -t crisdockerz/vollmed-api:1 . # build
docker push crisdockerz/vollmed-api:1  # adicionando ao repositório
```
- Todos os arquivos de manifesto, eu peguei da documentação oficial do kubernetes e fui modificando de acordo da minha preferencia
- Criei um diretório **deploy** para facilitar a organização


# Configurando as variáveis de ambiente com o configmap:

- Diretório **configmap**, para realizar a configuração do configmap eu peguei algumas variáveis de ambiente do **.env** e adicionei no manifesto
```bash
kubectl apply -f configmaps.yml
```


# Configurando Secrets

- No diretório **secrets**, Criei as secrets para guardar os dados do banco, como usuario e senha. As secrets, dependendo do tipo de dados que for armazenar será necessário adicionar em base64. Mas, vou deixar que o kubernetes faça essa criptografia de forma automática pra mim adicionando o tipo de dados stringData.
```bash
kubectl apply -f 
```

# Configurando o acesso na aplicação com NodePort

- No diretório, **acesso-nodeport** criei um arquivo manifesto para configurar o acesso a aplicação por um nodepot. Uma aplicação pode ser acessado por nodeport ou ingress. Mas neste meio de estudo, utilizo o nodePort pois estou usando o minikube que só tem apenas um node.
- Para definir um **NodePort**, é necessário criar um serviço que contem um tipo NodePort para que possamos acessar a aplicação por fora do cluster
- Por padrão, as portas que é utilizada no NodePort é de 30000-32767
```bash
kubectl apply -f service-nodeport.yml
```

# Configurando o banco de dados

- Criei um diretório **db-mysql** dentro de **deploy**
- Para configurar um banco de dados, utilizei o **statefulsets**. 
- Escolhi o StatefulSets pois ele foi feito principalmente para os bancos de dados, caso um pod do mysql tenha alguma falha, ele vai ser criado com o mesmo nome e ip garantindo que o banco de dados não seja quebrado.
- Para utilizar um **StatefulSets**, preciso definir qual **volume** ele vai usar, se excluir um **statefulsets**, por conta desses volumes definidos **não vão ser perdidos** e quando iniciar um novo StatefulSets ele vai usar o mesmo **volume**.
- Os banco de dados não precisam ser acessado externamente sendo ingress ou nodePort, neste caso, utilizei o serviço ClusterIP para que o banco de dados seja acessado apenas por dentro do cluster
- Para fins didáticos, criei os volumes no mesmo arquivo do StateFfulsets

## StorageClass:

- O storageclass que utilizei no banco de dados foi: **csi-hostpath-sc.** Utilizei essa forma de armazenamento para não ficar especificando quais tipo de armazenamento minha aplicação vai ter. E este é o método que o **miniukube** utiliza
- **csi**: Novo Método de acesso do kubernetes, Container Storage Interface. Fazendo com que por padrão os continainers podem armazenar informações, sem precisar de ficar especificando o tipo de armazenamento. 

#### Exemplo de tipos de armazenamentos: 

- Persistent Disk (GCEPersistentDisk) => ( Google Cloud)
- Managed Disk (AzureDisk) => ( Azure ) 
- Elastic Block Store (EBS) => ( AWS )
- hostPath => ( Local )

## Ativiando o tipo de armazenamento no minikube
```bash
minikube addons list

# saída esperada|-----------------------------|----------|--------------|--------------------------------|
|         ADDON NAME          | PROFILE  |    STATUS    |           MAINTAINER           |
|-----------------------------|----------|--------------|--------------------------------|
| ambassador                  | minikube | disabled     | 3rd party (Ambassador)         |
| auto-pause                  | minikube | disabled     | minikube                       |
| cloud-spanner               | minikube | disabled     | Google                         |
| csi-hostpath-driver         | minikube | disabled     | Kubernetes                     |
| dashboard                   | minikube | disabled     | Kubernetes                     |
| default-storageclass        | minikube | enabled ✅   | Kubernetes                     |
| efk                         | minikube | disabled     | 3rd party (Elastic)            |
| freshpod                    | minikube | disabled     | Google                         |
| gcp-auth                    | minikube | disabled     | Google                         |
| gvisor                      | minikube | disabled     | minikube                       |
| headlamp                    | minikube | disabled     | 3rd party (kinvolk.io)         |
| helm-tiller                 | minikube | disabled     | 3rd party (Helm)               |
| inaccel                     | minikube | disabled     | 3rd party (InAccel             |
|                             |          |              | [info@inaccel.com])            |
| ingress                     | minikube | disabled     | Kubernetes                     |
| ingress-dns                 | minikube | disabled     | minikube                       |
| inspektor-gadget            | minikube | disabled     | 3rd party                      |
|                             |          |              | (inspektor-gadget.io)          |
| istio                       | minikube | disabled     | 3rd party (Istio)              |
| istio-provisioner           | minikube | disabled     | 3rd party (Istio)              |
| kong                        | minikube | disabled     | 3rd party (Kong HQ)            |
| kubeflow                    | minikube | disabled     | 3rd party                      |
| kubevirt                    | minikube | disabled     | 3rd party (KubeVirt)           |
| logviewer                   | minikube | disabled     | 3rd party (unknown)            |
| metallb                     | minikube | disabled     | 3rd party (MetalLB)            |
| metrics-server              | minikube | disabled     | Kubernetes                     |
| nvidia-device-plugin        | minikube | disabled     | 3rd party (NVIDIA)             |
| nvidia-driver-installer     | minikube | disabled     | 3rd party (NVIDIA)             |
| nvidia-gpu-device-plugin    | minikube | disabled     | 3rd party (NVIDIA)             |
| olm                         | minikube | disabled     | 3rd party (Operator Framework) |
| pod-security-policy         | minikube | disabled     | 3rd party (unknown)            |
| portainer                   | minikube | disabled     | 3rd party (Portainer.io)       |
| registry                    | minikube | disabled     | minikube                       |
| registry-aliases            | minikube | disabled     | 3rd party (unknown)            |
| registry-creds              | minikube | disabled     | 3rd party (UPMC Enterprises)   |
| storage-provisioner         | minikube | enabled ✅   | minikube                       |
| storage-provisioner-gluster | minikube | disabled     | 3rd party (Gluster)            |
| storage-provisioner-rancher | minikube | disabled     | 3rd party (Rancher)            |
| volcano                     | minikube | disabled     | third-party (volcano)          |
| volumesnapshots             | minikube | disabled     | Kubernetes                     |
| yakd                        | minikube | disabled     | 3rd party (marcnuri.com)       |
|-----------------------------|----------|--------------|--------------------------------|

# Ative o csi-hostpath-driver
minikube addons enable csi-hostpath-driver
# Assim que ativar, ele pode demorar um pouquinho

# saída na log do minikube esperada após ativar
🔎  Verifying csi-hostpath-driver addon...
🌟  The 'csi-hostpath-driver' addon is enabled

```

- Depois de ativiar o armazenamento, fiz a conexão do PVC ao banco de dados

# Configurando o serviço do banco de dados
- Na maioria dos casos, o banco de dados possui informações confidenciais e não pode ser acessado externamente pelo mundo, então criei um serviço de ClusterIP para que o banco só possa ser acessado por **dentro do cluster**.
- Dentro do diretório **db-mysql** criei as configurações necessárias para o funcionamento do banco de dados, e segui boas práticas criando configmap e secret


# LoadBalancer
- No momento, a aplicaçãoe vollmed está rodando como **nodePort**, essa é uma ótima opção para quando esta usando apenas um node. Para uma situação real de um ambiente em produção com **25 nodes por exemplo**, o NodePort pode ser um problema. Pois, quando define um NodePort na porta 30000 ele vai abrir a mesma porta nas 25 outras máquinas. Então não **obrigatoriamente** a aplicação vão estar dentro de todas essas **máquinas**.
- Por exemplo, se tiver uma aplicação com 5 replicas, teria que caçar em cada maquina onde essas 5 replicas estão.
- Solução, **LoadBalancer**. O uso LoadBalancer deixa usar qualquer porta, como a **443, 80, 3000...** etc. Já o NodePort só pode usar portas entre **30000 - 32767**
- Dito isso, implementei o LoadBalancer na aplicação que seria basicamente mudar o tipo do serviço de NodePort para **LoadBalancer**

### Cuidados: 
- O uso de um **LoadBalancer** vai ta disponibilizado pelo **ambiente**.
- **Exemplo:** Na AWS, usa o AWS Elastic Load Balancer, no Google Cloud, usa Cloud Load Balancing.
- Neste cenário de estudo, vou utilizar o LoadBalancer do minikube, que é o tunnel


