# Configurando o Kubernetes dashboard para melhor visualização de workloads
```bash
kubectl apply -f dashboard.yml # criando a dashboard
kubectl apply -f admin.yml # criando acesso de amin para logar na dashboard
kubectl -n kubernetes-dashboard create token admin-user # pegando o token, copie o token
kubectl proxy & # exoporta a dashboard para acessar, ctrl + c para destravar o terminal, o (proxy não vai ser encerrado)


#entre na URL: http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login

#Marque a opção Token, cole o token copiado e clique em login.
```

# Dashboard encerrando a sessão

- Se a dashboard ficar muito tempo ocioso, vai ser necessário executar comando de pegar o token novamente e logar. Caso queira que o navegador execute a dashboard inserido o token de forma automática, execute:

```bash
kubectl proxy --accept-hosts='.*' --address='0.0.0.0' &
``` 
- caso o comando acima não funcione, edite o arquivo yml da dashboard da seguinte maneira:

```bash
# Edite a implantação do Dashboard:
kubectl edit deployment kubernetes-dashboard -n kubernetes-dashboard

# Procure pelo container do Dashboard e adicione o argumento --token-ttl=0

containers:
- name: kubernetes-dashboard
  args:
    - --token-ttl=0

# saída esperada ao editar:
deployment.apps/kubernetes-dashboard edited
``` 


# Dashboard com minikube:
```bash
# adicionando addons do metrics-server
minikube addons enable metrics-server

# iniciando a dashboard
minikube dashboard
```