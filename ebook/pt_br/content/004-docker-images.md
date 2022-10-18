# O que são imagens do Docker

Uma imagem do Docker é apenas um modelo usado para criar um contêiner do Docker em execução, semelhante aos arquivos ISO e às máquinas virtuais. Os contêineres são essencialmente a instância em execução de uma imagem. As imagens são usadas para compartilhar aplicativos em contêiner. As coleções de imagens são armazenadas em registros como o DockerHub ou registros privados.

## Trabalhando com imagens do Docker

O comando `docker run` baixa e executa imagens ao mesmo tempo. Mas também só poderíamos baixar imagens se quiséssemos com o comando docker pull. Por exemplo:

```
docker pull ubuntu
```

Ou se você deseja obter uma versão específica, também pode fazer isso com:

```
docker pull ubuntu: 14.04
```

Em seguida, para listar todas as suas imagens, use o comando docker images:

```
docker images
```

Você obteria uma saída semelhante a:

![](https://cdn.devdojo.com/posts/images/April2020/docker-images-list.png)

As imagens são armazenadas localmente em sua máquina host do docker.

Para dar uma olhada no hub do docker, acesse: [https://hub.docker.com](https://hub.docker.com) e você poderá ver de onde as imagens foram baixadas.

Por exemplo, aqui está um link para a **imagem do Ubuntu** que acabamos de baixar:

[https://hub.docker.com/\_/ubuntu](https://hub.docker.com/_/ubuntu)

Lá você pode encontrar algumas informações úteis.

Como o Ubuntu 14.04 está realmente desatualizado, para deletar a imagem use o comando `docker rmi`:

```
docker rmi ubuntu: 14.04
```

## Modificando imagens ad hoc

Uma das maneiras de modificar imagens é com comandos ad-hoc. Por exemplo, basta iniciar seu contêiner do Ubuntu.

```
docker run -d -p 80:80 IMAGE_ID
```

Depois disso, para anexar ao seu contêiner em execução, você pode executar:

```
docker exec -it container_name /bin/bash
```

Instale os pacotes necessários e saia do container apenas pressione `CTRL+P+Q`.

Para salvar suas alterações, execute o seguinte:

```
docker container commit ID_HERE
```

Em seguida, liste suas imagens e anote o ID da imagem:

```
docker images ls
```

O processo ficaria da seguinte forma:

![](https://cdn.devdojo.com/posts/images/April2020/docker-commit.png)

Como você notaria, sua imagem recém-criada não teria um nome nem uma tag, então, para marcar sua imagem, execute:

```
docker tag IMAGE_ID YOUR_TAG
```

Agora, se você listar suas imagens, verá a seguinte saída:

![](https://cdn.devdojo.com/posts/images/April2020/docker-tag.png)

## Enviando imagens para o Docker Hub

Agora que temos nossa nova imagem localmente, vamos ver como podemos enviar essa nova imagem para o DockerHub.

Para isso, você precisaria de uma conta do Docker Hub primeiro. Depois de ter sua conta pronta, para autenticar, execute o seguinte comando:

```
docker login
```

Em seguida, envie sua imagem para o **Docker Hub**:

```
docker push your-docker-user/name-of-image-aqui
```

A saída ficaria assim:

![](https://cdn.devdojo.com/posts/images/April2020/docker-push.png)

Depois disso, você poderá ver sua imagem docker em sua conta do hub do docker, no meu caso, estaria aqui:

[https://cloud.docker.com/repository/docker/bobbyiliev/php-apache](https://cloud.docker.com/repository/docker/bobbyiliev/php-apache)

![](https://cdn.devdojo.com/posts/images/April2020/docker-hub.png)

## Modificando imagens com Dockerfile

Vamos aprofundar um pouco mais o Dockerfile na próxima postagem do blog, para esta demonstração usaremos apenas um Dockerfile simples apenas como exemplo:

Crie um arquivo chamado `Dockerfile` e adicione o seguinte conteúdo:

```
FROM alpine
RUN apk update
```

Tudo o que este `Dockerfile` faz é atualizar a imagem Alpine base.

Para construir a imagem execute:

```
docker image build -t alpine-updated:v0.1 .
```

Em seguida, você pode listar novamente sua imagem e enviar a nova imagem para o **Docker Hub**!

## Verificação de conhecimento de imagens do Docker

Depois de ler este post, certifique-se de testar seus conhecimentos com este Docker Images Quiz:

[https://quizapi.io/predefined-quizzes/common-docker-images-questions](https://quizapi.io/predefined-quizzes/common-docker-images-questions)

Agora que você sabe como extrair, modificar e enviar **imagens do Docker**, estamos prontos para aprender mais sobre o `Dockerfile` e como usá-lo!
