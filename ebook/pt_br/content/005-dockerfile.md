# O que é um Dockerfile

Um **Dockerfile** é basicamente um arquivo de texto que contém todos os comandos necessários para criar uma determinada **imagem Docker**.

A página de referência do **Dockerfile**:

[https://docs.docker.com/engine/reference/builder/](https://docs.docker.com/engine/reference/builder/)

Ele lista os vários comandos e detalhes de formato para Dockerfiles.

## Exemplo de arquivo Docker

Aqui está um exemplo realmente básico de como criar um `Dockerfile` e adicionar nosso código-fonte a uma imagem.

Primeiro, eu tenho um arquivo simples Hello world `index.html` no meu diretório atual que eu adicionaria ao contêiner com o seguinte conteúdo:

```
<h1>Hello World - Bobby Iliev</h1>
```

And I also have a Dockerfile with the following content:

```
FROM webdevops/php-apache-dev
MAINTAINER Bobby I.
COPY . /var/www/html
WORKDIR /var/www/html
EXPOSE 8080
```

Aqui está uma captura de tela do meu diretório atual e o conteúdo dos arquivos:

![](https://cdn.devdojo.com/posts/images/April2020/dockerfile-example.png)

Aqui está um resumo rápido do Dockerfile:

* `FROM`: A imagem que usaríamos como base

* `MAINTAINER`: A pessoa que manteria a imagem

* `COPY`: Copie alguns arquivos na imagem

* `WORKDIR`: O diretório onde você deseja executar seus comandos no início

* `EXPOSE`: Especifique uma porta na qual você gostaria de acessar o container

## Compilação do Docker

Agora, para construir uma nova imagem do nosso `Dockerfile`, precisamos usar o comando docker build. A sintaxe do comando docker build é a seguinte:

```
docker build [OPTIONS] PATH | URL | -
```

O comando exato que precisamos executar é este:

```
docker build -f Dockerfile -t your_user_name/php-apache-dev .
```

Após a conclusão da compilação, você pode listar suas imagens com o comando docker images e também executá-lo:

```
docker run -d -p 8080:80 your_user_name/php-apache-dev
```

E novamente, assim como fizemos na última etapa, podemos prosseguir e publicar nossa imagem:

```
docker login

docker push your-docker-user/name-of-image-aqui
```

Em seguida, você poderá ver sua nova imagem em sua conta do Docker Hub (<https://hub.docker.com>) que você pode extrair diretamente do hub:

```
docker pull your-docker-user/name-of-image-here
```

Para obter mais informações sobre a compilação do docker, verifique a documentação oficial aqui:

[https://docs.docker.com/engine/reference/commandline/build/](https://docs.docker.com/engine/reference/commandline/build/)

## Verificação de conhecimento do Dockerfile

Depois de ler este post, certifique-se de testar seus conhecimentos com este [**Dockerfile quiz**:](https://quizapi.io/predefined-quizzes/basic-dockerfile-quiz)

[https://quizapi.io/predefined-quizzes/basic-dockerfile-quiz](https://quizapi.io/predefined-quizzes/basic-dockerfile-quiz)

Este é um exemplo realmente básico, você pode ir além com seus Dockerfiles!

Agora você sabe como escrever um Dockerfile, como construir uma nova imagem de um Dockerfile usando o comando docker build!

Na próxima etapa, aprenderemos como configurar e trabalhar com o modo **Docker Swarm**!
