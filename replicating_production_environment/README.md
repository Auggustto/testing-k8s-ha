### 1. Configuração do Kind com Balanceador de Carga
A configuração do kind deve permitir que o tráfego de fora do cluster seja redirecionado para o Ingress Controller, que por sua vez irá rotear para os serviços no cluster. As portas 80 (HTTP) e 443 (HTTPS) devem ser expostas na máquina do host usando extraPortMappings. <br>
Arquivo: config-Ingress.yml <br>
Comando para aplicar o Service:
```
kind create cluster --name k8s-ha --config config-Ingress.yml
```

### 2. Instalação do Ingress Controller
Você precisa instalar o Ingress Controller (geralmente o NGINX é o mais comum) para fazer o roteamento do tráfego externo para os serviços do cluster. Se você já tiver o Ingress Controller instalado, não precisa instalar novamente. Se ainda não, você pode instalar com o seguinte comando:
```
    kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```
Isso criará o NGINX Ingress Controller e permitirá o roteamento do tráfego para os serviços.

### 3. Configuração do Service para a API (hello-world)
A API pequena é configurada através de um Service do tipo ClusterIP, que permite que o Ingress Controller encaminhe as requisições para a API: <br>
Arquivo: config-service.yml

### 4. Configuração do Deployment da API (hello-world)
Aqui está a configuração do Deployment para a API. Para produção, é interessante aumentar a quantidade de réplicas, por exemplo, 2 ou 3 réplicas para garantir alta disponibilidade:<br>
Arquivo: deploy-hello-world.yml<br>
Comando para aplicar o Deployment:
```
kubectl apply -f deploy-hello-world.yml
```

### 5. Configuração do Ingress para Roteamento de Tráfego Externo
Agora, configuramos o Ingress para expor a API com o domínio hello-world.local. O tráfego HTTP que chega ao Ingress Controller será roteado para o Service hello-world.<br>
Arquivo: config-external-traffic-routing.yml<br>
Comando para aplicar o Ingress:
```
kubectl apply -f onfig-external-traffic-routing.yml
```

### 6. Adicionando o hello-world.local ao Arquivo de Hosts (Somente para Teste Local)
Em um ambiente local, você deve adicionar hello-world.local ao seu arquivo /etc/hosts para que ele aponte para 127.0.0.1 (localhost). Isso simula o comportamento de um domínio real.<br>
Adicione a seguinte linha no seu arquivo /etc/hosts:<br>
```
127.0.0.1 hello-world.local
```

### 7. Verificação Final
Após aplicar as configurações, use os comandos a seguir para verificar se o seu cluster está configurado corretamente:

Verifique os pods:
```
kubectl get pods
```
Verifique os serviços:
```
kubectl get svc
```
Verifique o Ingress:
```
kubectl get ingress
```
Teste o acesso: No terminal, execute:
```
curl http://hello-world.local
```