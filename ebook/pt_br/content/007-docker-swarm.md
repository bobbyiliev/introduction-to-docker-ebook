# O que é o modo Docker Swarm

De acordo com os documentos oficiais do **Docker**, um Swarm é um grupo de máquinas que estão executando o **Docker** e unidas em um cluster. Se você estiver executando um **Docker Swarm**, seus comandos serão executados em um cluster por um gerenciador de swarm. As máquinas em um swarm podem ser físicas ou virtuais. Depois de ingressar em um swarm, eles são chamados de nós. Eu faria uma demonstração rápida em breve na minha conta **DigitalOcean**!

O **Docker Swarm** consiste em **nós de gerenciador** e **nós de trabalho**.

Os nós do gerenciador despacham tarefas para os nós do trabalhador e, do outro lado, os nós do trabalhador apenas executam essas tarefas. Para alta disponibilidade, é recomendável ter nós de gerenciador **3** ou **5**.

## Serviços Docker

Para implantar uma imagem de aplicativo quando o Docker Engine estiver no modo swarm, você deve criar um serviço. Um serviço é um grupo de contêineres da mesma `imagem:tag`. Os serviços simplificam o dimensionamento de seu aplicativo.

Para ter **serviços do Docker**, primeiro você deve ter seu **Docker Swarm** e os nós prontos.

![](https://cdn.devdojo.com/posts/images/May2020/services-diagram.jpg)

## Construindo um Swarm

Farei uma demonstração bem rápida de como construir um **Docker Swarm com 3 gerentes e 3 trabalhadores**.

Para isso vou implantar 6 droplets na DigitalOcean:

![](https://cdn.devdojo.com/posts/images/May2020/docker-swarm.png)

Então, quando estiver pronto, **instale o docker** exatamente como fizemos na **[Introdução ao Docker Parte 1](https://devdojo.com/tutorials/introduction-to-docker-part-1 )** e depois é só seguir os passos aqui:

### Passo 1

Inicialize o Docker Swarm em seu primeiro nó de gerenciador:

```
docker swarm init --advertise-addr your_dorplet_ip_here
```

### Passo 2

Então, para obter o comando que você precisa para se juntar ao resto dos gerenciadores, basta executar isto:

```
docker swarm join-token manager
```

> Nota: Isso forneceria o comando exato que você precisa para executar no restante dos nós do gerenciador de enxame. Exemplo:

![](https://cdn.devdojo.com/posts/images/May2020/docker-swarm-join-managers-bobby-iliev.png)

### Etapa 3

Para obter o comando que você precisa para ingressar nos trabalhadores, basta executar:

```
docker swarm join-token worker
```

O comando para trabalhadores seria bastante semelhante ao comando para gerentes de junção, mas o token seria um pouco diferente.

A saída que você obteria ao ingressar em um gerenciador seria assim:

![](https://cdn.devdojo.com/posts/images/May2020/docker-join-manager.png)

### Passo 4

Depois de ter seus comandos de junção, **ssh para o resto de seus nós e junte-se a eles** como trabalhadores e gerentes de acordo.

# Gerenciando o cluster

Depois de executar os comandos join em todos os seus trabalhadores e gerentes, para obter algumas informações sobre o status do cluster, você pode usar estes comandos:

* Para listar todos os nós disponíveis, execute:

```
docker node ls
```

> Nota: Este comando só pode ser executado a partir de um **gerenciador de enxames**!Saída:

![](https://cdn.devdojo.com/posts/images/May2020/docker-node-ls.png)

* Para obter informações sobre o estado atual, execute:

```
docker info
```

Resultado:

![](https://cdn.devdojo.com/posts/images/May2020/docker-info-bobby-iliev.png)

## Promover um trabalhador a gerente

Para promover um trabalhador a gerente, execute o seguinte em **um** de seus nós de gerente:

```
docker node promote node_id_here
```

Observe também que cada gerente também atua como um trabalhador, portanto, na saída de informações do docker, você deve ver 6 trabalhadores e 3 nós de gerenciador.

## Usando serviços

Para criar um serviço, você precisa usar o seguinte comando:

```
docker service create --name bobby-web -p 80:80 --replicas 5 bobbyiliev/php-apache
```

Observe que eu já tenho minha imagem bobbyiliev/php-apache enviada para o hub do Docker, conforme descrito nas postagens anteriores do blog.

Para obter uma lista de seus serviços, execute:

```
docker service ls
```

Resultado:

![](https://cdn.devdojo.com/posts/images/May2020/docker-create-service.png)

Então, para obter uma lista dos contêineres em execução, você precisa usar o seguinte comando:

```
docker services ps name_of_your_service_here
```

Resultado:

![](https://cdn.devdojo.com/posts/images/May2020/docker-service-ps.png)

Então você pode visitar o endereço IP de qualquer um de seus nós e poderá ver o serviço! Podemos basicamente visitar qualquer nó do swarm e ainda obteremos o serviço.

## Escalando um serviço

Poderíamos tentar desligar um dos nós e ver como o enxame iria automaticamente ativar um novo processo em outro nó para que ele correspondesse ao estado desejado de 5 réplicas.

Para fazer isso, vá ao seu painel de controle **DigitalOcean** e aperte o botão de desligar para um de seus Droplets. Em seguida, volte para o seu terminal e execute:

```
docker services ps name_of_your_service_here
```

Resultado:

![](https://cdn.devdojo.com/posts/images/May2020/docker-replicas.png)

Na captura de tela acima, você pode ver como desliguei o droplet chamado worker-2 e como a réplica bobby-web.2 foi reiniciada instantaneamente em outro nó chamado worker-01 para corresponder ao estado desejado de 5 réplicas.

Para adicionar mais réplicas, execute:

```
docker service scale name_of_your_service_here=7
```

Resultado:

![](https://cdn.devdojo.com/posts/images/May2020/docker-more-replicas.png)

Isso geraria automaticamente mais 2 contêineres, você pode verificar isso com o comando docker service ps:

```
docker service ps name_of_your_service_here
```

Então, como teste, tente iniciar o nó que encerramos e verifique se ele pegou alguma tarefa?

**Dica**: trazer novos nós para o cluster não distribui automaticamente as tarefas em execução.

## Excluindo um serviço

Para excluir um serviço, tudo o que você precisa fazer é executar o seguinte comando:

```
docker service rm name_of_your_service
```

Resultado:

![](https://cdn.devdojo.com/posts/images/May2020/docker-delete-service.png)

Agora você sabe como inicializar e dimensionar um cluster docker swarm! Para obter mais informações, consulte a documentação oficial do Docker [aqui](https://docs.docker.com/engine/swarm/).

## Verificação de conhecimento do Docker Swarm

Depois de ler este post, certifique-se de testar seus conhecimentos com este **[Docker Swarm Quiz](https://quizapi.io/predefined-quizzes/common-docker-swarm-interview-questions)**:

[https://quizapi.io/predefined-quizzes/common-docker-swarm-interview-questions](https://quizapi.io/predefined-quizzes/common-docker-swarm-interview-questions)
