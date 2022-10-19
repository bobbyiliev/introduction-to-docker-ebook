# Instalando o Docker

Hoje em dia você pode rodar o Docker no Windows, Mac e, claro, no Linux. Estarei apenas passando pela instalação do Docker para Linux, pois este é o meu sistema operacional de escolha.

Vou implantar um **servidor Ubuntu na DigitalOcean**, então sinta-se à vontade para fazer o mesmo:

[Criar um Droplet DigitalOcean](https://docs.digitalocean.com/products/droplets/how-to/create)

Quando seu servidor estiver funcionando, faça SSH no Droplet e acompanhe!

Se você não tiver certeza de como usar o SSH, siga as etapas aqui:

[https://www.digitalocean.com/docs/droplets/how-to/connect-with-ssh/](<https://www.digitalocean.com/docs/droplets/how-to/connect-with-ssh> /)

A instalação é realmente simples, você pode simplesmente executar o seguinte comando, ele deve funcionar em todas as principais distribuições **Linux**:

```
wget -qO- https://get.docker.com | sh
```

Ele fará tudo o que é necessário para instalar o **Docker em sua máquina Linux**.

Depois disso, configure o Docker para que você possa executá-lo como um usuário não root com o seguinte comando:

```
sudo usermod -aG docker ${USER}
```

Para testar o **Docker**, execute o seguinte:

```
docker version
```

Para obter mais informações sobre seu Docker Engine, você pode executar o seguinte comando:

```
docker info
```

Com o comando `docker info`, podemos ver quantos containers em execução temos e algumas informações do servidor.

A saída que você obteria do comando da versão do docker deve ser algo assim:

![saída da versão do docker](https://imgur.com/tuGSemS.png)

Caso você queira instalar o Docker no seu PC Windows ou no seu Mac, você pode visitar a documentação oficial do Docker aqui:

[https://docs.docker.com/docker-for-windows/install/](https://docs.docker.com/docker-for-windows/install/)

E:

[https://docs.docker.com/docker-for-mac/install/](https://docs.docker.com/docker-for-mac/install/)

É mais ou menos isso! Agora você tem o Docker rodando na sua máquina!

Agora estamos prontos para começar a trabalhar com contêineres! Vamos extrair uma **imagem do Docker** do **DockerHub**, executar um contêiner, pará-lo, destruí-lo e muito mais!
