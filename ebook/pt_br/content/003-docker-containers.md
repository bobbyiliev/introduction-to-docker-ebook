# Trabalhando com containers Docker

Depois de ter seu **Ubuntu Droplet** pronto, ssh para o servidor e siga em frente!

Então, vamos executar nosso primeiro container Docker! Para isso, basta executar o seguinte comando:

```
docker run hello-world
```

Você obterá a seguinte saída:

![](https://cdn.devdojo.com/posts/images/April2020/docker-run.png)

Acabamos de executar um contêiner com base na **hello-world Docker Image**, pois não tínhamos a imagem localmente, o docker extraiu a imagem do **[DockerHub](https://hub.docker.com)* * e, em seguida, usou essa imagem para executar o contêiner.
Tudo o que aconteceu foi: o**contêiner executou**, imprimindo algum texto na tela e saiu.

Em seguida, para ver algumas informações sobre a execução e os contêineres parados, execute:

```
docker ps -a
```

Você verá as seguintes informações para seu **contêiner hello-world** que você acabou de executar:

```
root@docker:~# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
62d360207d08        hello-world         "/hello"            5 minutes ago       Exited (0) 5 minutes ago                       focused_cartwright
```

Para listar as imagens do Docker disponíveis localmente em seu host, execute o seguinte comando:

```
docker images
```

## Puxando uma imagem do Docker Hub

Vamos executar um contêiner mais útil, como um contêiner **Apache**, por exemplo.

Primeiro, podemos extrair a imagem do hub do docker com o **comando pull docker**:

```
docker pull webdevops/php-apache
```

Você verá a seguinte saída:

![](https://cdn.devdojo.com/posts/images/April2020/docker-pull-php-apache.png)

Em seguida, podemos obter o ID da imagem com o comando docker images:

```
docker images
```

A saída ficaria assim:

![](https://cdn.devdojo.com/posts/images/April2020/docker-images.png)

> Observe que você não precisa necessariamente extrair a imagem, isso é apenas para fins de demonstração. Ao executar o comando `docker run`, se a imagem não estiver disponível localmente, ela será automaticamente extraída do Docker Hub.

Depois disso, podemos usar o comando **docker run** para ativar um novo contêiner:

```
docker run -d -p 80:80 IMAGE_ID
```

Resumo rápido dos argumentos que usei:

* `-d`: especifica que quero executar o container em segundo plano. Dessa forma, quando você fechar seu terminal, o contêiner permanecerá em execução.

* `-p 80:80`: significa que o tráfego do host na porta 80 seria encaminhado para o container. Dessa forma, você pode acessar a instância do Apache que está sendo executada dentro do contêiner docker diretamente pelo navegador.

Com o comando docker info agora podemos ver que temos 1 container em execução.

E com o comando `docker ps` podemos ver algumas informações úteis sobre o container como o ID do container, quando o container foi iniciado, etc.:

```
root@docker:~# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                                   NAMES
7dd1d512b50e        fd4f7e58ef4b        "/entrypoint supervi…"   About a minute ago   Up About a minute   443/tcp, 0.0.0.0:80->80/tcp, 9000/tcp   pedantic_murdock
```

## Parando e reiniciando um Docker Container

Em seguida, você pode interromper o contêiner em execução com o comando docker stop seguido pelo ID do contêiner:

```
docker stop CONTAINER_ID
```

Se precisar, você pode iniciar o contêiner novamente:

```
docker start CONTAINER_ID
```

Para reiniciar o contêiner, você pode usar o seguinte:

```
docker restart CONTAINER_ID
```

## Acessando um contêiner em execução

Se você precisar anexar ao contêiner e executar alguns comandos dentro do contêiner, use o comando `docker exec`:

```
docker exec -it CONTAINER_ID /bin/bash
```

Dessa forma, você chegará a um **bash shell** no contêiner e executará alguns comandos dentro do próprio contêiner.

Em seguida, para desanexar do shell interativo, pressione `CTRL+PQ`. Dessa forma, você não interromperá o contêiner, mas apenas o desconectará do shell interativo.

![](https://cdn.devdojo.com/posts/images/April2020/docker-exec-stop1.png)

## Excluindo um contêiner

Para excluir o contêiner, primeiro verifique se o contêiner não está em execução e execute:

```
docker rm CONTAINER_ID
```

Se você quiser excluir o contêiner e a imagem juntos, basta executar:

```
docker rmi IMAGE_ID
```

Com isso, agora você sabe como extrair imagens do Docker do **Docker Hub**, executar, parar, iniciar e até anexar aos contêineres do Docker!

Estamos prontos para aprender a trabalhar com **imagens do Docker!**
