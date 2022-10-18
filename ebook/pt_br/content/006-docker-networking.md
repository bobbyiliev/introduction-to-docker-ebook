# Rede Docker

O Docker vem com um sistema de rede conectável. Existem vários plugins que você pode usar por padrão:

* `bridge`: O driver de rede padrão do Docker. Isso é adequado para contêineres autônomos que precisam se comunicar entre si.
* `host`: Este driver remove o isolamento de rede entre o container e o host. Isso é adequado para contêineres autônomos que usam a rede do host diretamente.
* `overlay`: Overlay permite conectar vários daemons do Docker. Isso permite que você execute serviços Docker swarm, permitindo que eles se comuniquem entre si.
* `none`: Desativa todas as redes.

Para listar as redes Docker atualmente disponíveis, você pode usar o seguinte comando:

```
docker network list
```

Você obteria a seguinte saída:

```
NETWORK ID     NAME      DRIVER    SCOPE
3194399146e4   bridge    bridge    local
cf7f50175100   host      host      local
590fb3abc0e1   none      null      local
```

Como você pode ver, já temos 3 redes disponíveis prontas para uso com 3 dos drivers de rede que discutimos acima.

## Criando uma rede Docker

Para criar uma nova rede Docker com o driver `bridge` padrão, você pode executar o seguinte comando:

```
docker network create myNewNetwork
```

O comando acima criaria uma nova rede com o nome de `myNewNetwork`.

Você também pode especificar um driver diferente adicionando o sinalizador `--driver=DRIVER_NAME`.

Se você deseja criar uma rede Docker com um intervalo específico, pode fazer isso adicionando o sinalizador `--subnet=` seguido pela sub-rede que deseja usar.

## Inspecionando uma rede Docker

Para obter algumas informações de uma rede Docker existente, como o driver que está sendo usado, a sub-rede, os contêineres conectados a essa rede, você pode usar o comando `docker network inspect` da seguinte forma:

```
docker network inspect myNewNetwork
```

A saída do comando estaria em JSON por padrão.

Você pode usar o comando `docker inspect` para inspecionar outros objetos do Docker, como contêineres, imagens e etc.

## Anexando contêineres a uma rede

Para praticar o que você acabou de aprender, vamos criar dois contêineres e adicioná-los a uma rede Docker para que eles possam se comunicar usando seus nomes de contêiner.

Aqui está um exemplo rápido de uma rede `bridge`:

![](https://github.com/bobbyiliev/introduction-to-docker-ebook/raw/main/ebook/en/assets/networking.png)

* Primeiro comece criando dois contêineres:

```
docker run -d --name web1 -p 8001:80 eboraas/apache-php
docker run -d --name web2 -p 8002:80 eboraas/apache-php
```

> É muito **importante** **especificar explicitamente um nome** com `--name` para seus contêineres, caso contrário, note que não funcionaria com os nomes aleatórios que o Docker atribui aos seus contêineres.

* Assim que os dois contêineres estiverem funcionando, crie uma nova rede:

```
docker network create myNetwork
```

* Depois disso, conecte seus contêineres à rede:

```
docker network connect myNetwork web1
docker network connect myNetwork web2
```

* Verifique se seus containers fazem parte da nova rede:

```
docker network inspect myNetwork
```

* Em seguida, teste a conexão:

```
docker exec -ti web1 ping web2
```

Novamente, lembre-se de que é muito importante especificar nomes explicitamente para seus contêineres, caso contrário, isso não funcionaria. Eu descobri isso depois de passar algumas horas tentando descobrir.

Para obter mais informações sobre o poder da rede Docker, verifique a documentação oficial [aqui](https://docs.docker.com/network/).
