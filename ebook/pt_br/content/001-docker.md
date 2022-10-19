# Introdução ao Docker

É mais provável que o **Docker** e os contêineres façam parte de sua carreira de TI de uma forma ou de outra.

Depois de ler este e-book, você terá uma boa compreensão do seguinte:

* O que é Docker
* O que são containers
* O que são imagens do Docker
* O que é Docker Hub
* Como instalar o Docker
* Como trabalhar com contêineres Docker
* Como trabalhar com imagens do Docker
* O que é um Dockerfile
* Como implantar um aplicativo Dockerizado
* Rede Docker
* O que é Docker Swarm
* Como implantar e gerenciar um cluster do Docker Swarm

Usarei o **DigitalOcean** para todas as demonstrações, então recomendo fortemente que você crie uma conta do **DigitalOcean** para acompanhar. Você aprenderia mais fazendo!

Para tornar as coisas ainda melhores, você pode usar meu link de referência para obter um crédito gratuito de $ 100 que você pode usar para implantar suas máquinas virtuais e testar o guia você mesmo em alguns **servidores DigitalOcean**:

**[Crédito grátis de US$ 100 da DigitalOcean](https://m.do.co/c/2a9bba940f39)**

Depois de ter sua conta, veja como implantar seu primeiro Droplet/servidor:

[https://www.digitalocean.com/docs/droplets/how-to/create/](https://www.digitalocean.com/docs/droplets/how-to/create/)

Estarei usando o **Ubuntu 21.04**, então recomendo que você siga o mesmo para poder acompanhar.

No entanto, você pode executar o Docker em praticamente qualquer sistema operacional, incluindo Linux, Windows, Mac, BSD e etc.

## O que é um contêiner?

De acordo com a definição oficial do site [docker.com](docker.com), um contêiner é uma unidade padrão de software que empacota código e todas as suas dependências para que o aplicativo seja executado de forma rápida e confiável de um ambiente de computação para outro. Uma imagem de contêiner do Docker é um pacote de software leve, autônomo e executável que inclui tudo o que é necessário para executar um aplicativo: código, tempo de execução, ferramentas do sistema, bibliotecas do sistema e configurações.

As imagens de um contêiner se tornam contêineres em tempo de execução e, no caso de contêineres do Docker, as imagens se tornam contêineres quando são executadas no Docker Engine. Disponível para aplicativos baseados em Linux e Windows, o software em contêiner sempre será executado da mesma forma, independentemente da infraestrutura. Os contêineres isolam o software de seu ambiente e garantem que ele funcione uniformemente, apesar das diferenças, por exemplo, entre desenvolvimento e teste.

![](https://github.com/bobbyiliev/introduction-to-docker-ebook/raw/main/ebook/pt_br/assets/images/infrastructure.png)

## O que é uma imagem do Docker?

Uma **Imagem do Docker** é apenas um modelo usado para criar um Docker Container em execução, semelhante aos arquivos ISO e às Máquinas Virtuais. Os contêineres são essencialmente a instância em execução de uma imagem. As imagens são usadas para compartilhar aplicativos em contêiner. As coleções de imagens são armazenadas em registros como [DockerHub](https://hub.docker.com/) ou registros privados.

![](https://github.com/bobbyiliev/introduction-to-docker-ebook/raw/main/ebook/pt_br/assets/images/process.png)

## O que é o Docker Hub?

O DockerHub é o **registro de imagens do Docker** padrão, onde podemos armazenar nossas **imagens do Docker**. Você pode pensar nisso como GitHub para projetos Git.

Aqui está um link para o Docker Hub:

[https://hub.docker.com](https://hub.docker.com)

Você pode se inscrever para uma conta gratuita. Dessa forma, você pode enviar suas imagens do Docker de sua máquina local para o DockerHub.
