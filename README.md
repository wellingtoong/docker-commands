# Docker

Docker é um conjunto de produtos de plataforma como serviço que usam virtualização de nível de sistema operacional para entregar software em pacotes chamados contêineres. Os contêineres são isolados uns dos outros e agrupam seus próprios softwares, bibliotecas e arquivos de configuração.

**Referências:**
<br>

Alguns links que podem ajudar entender melhor sobre Docker, comandos e fluxos.

- [Docker](https://stack.desenvolvedor.expert/appendix/docker/oquee.html)
- [Docker commandline](https://docs.docker.com/engine/reference/commandline/docker/)

<br>

## Comandos básicos com containers

| Comando                             | Descrição                                                                                                                           |
| ----------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `docker ps `                        | Lista todos containers ativos.                                                                                                      |
| `docker ps -a`                      | Lista todos containers parados.                                                                                                     |
| `docker ps -q`                      | Lista todos ids dos containers                                                                                                      |
| `docker images`                     | Lista todas as imagens.                                                                                                             |
| `docker network connect`            | Conecta um container em uma rede.                                                                                                   |
| `docker network create`             | Cria uma rede.                                                                                                                      |
| `docker network disconnect`         | Disconecta um container de uma rede.                                                                                                |
| `docker network inspect`            | Mostra detalhes e informações da rede.                                                                                              |
| `docker network ls`                 | Lista todas as redes.                                                                                                               |
| `docker network prune`              | Lista todas as redes não usadas.                                                                                                    |
| `docker network rm`                 | Lista uma rede.                                                                                                                     |
| `docker rmi <nome-da-imagem>`       | Remove uma imagem.                                                                                                                  |
| `docker run <nome-da-imagem>`       | Cria um container a partir da imagem.                                                                                               |
| `docker run -it <nome-da-imagem>`   | Cria um container deixando o terminal atual interativo.                                                                             |
| `docker start <id-container>`       | Inicia um container parado.                                                                                                         |
| `docker start -a -i <id-container>` | Inicia um container e conecta o terminal ao container (-a attach; -i interactive).                                                  |
| `docker exec`                       | Executa um novo comando em um conteiner em execução (Ex.: docker exec -it <nome-do-container> "echo a && echo b").                  |
| `docker port <id-container>`        | Exibe porta do container.                                                                                                           |
| `docker inspect <id-container>`     | Inspecionando container.                                                                                                            |
| `docker stop`                       | Para um container em execução. Por padrão o stop demora 10 segundos para finalizar o container, você pode modificar passando `-t 0` |
| `docker rm <id-container>`          | Remove container parado.                                                                                                            |
| `docker container prune`            | Remove todos os container parados.                                                                                                  |

<br>

## Docker Compose

| Comando                | Descrição                                                    |
| ---------------------- | ------------------------------------------------------------ |
| `docker-compose up`    | Sobe todos os serviços especificados no docker-compose.yaml. |
| `docker-compose down`  | Para todos os serviços especificados no docker-compose.yaml. |
| `docker-compose ps`    | Lista todos os serviços que estão ativos.                    |
| `docker-compose build` | Build com docker compose.                                    |

<br>

## Docker Hub

| Comando                        | Descrição                                                                          |
| ------------------------------ | ---------------------------------------------------------------------------------- |
| `docker login`                 | Configura as credenciais de acesso via terminal.                                   |
| `docker push <nome-da-imagem>` | Sobe imagem para repositorio. Ex.: docker push wellingtoong/node                   |
| `docker pull <nome-da-imagem>` | Faz download da imagem a partir do repositorio. Ex.: docker pull wellingtoong/node |

<br>

## Atalhos básicos

| Comando                            | Descrição                    |
| ---------------------------------- | ---------------------------- |
| `pwd`                              | Exibe caminho root.          |
| `ctrl + d`                         | Encerra o container.         |
| `docker stop -t 0 $(docker ps -q)` | Parando todos os containers. |

<br>

## Criando um site estático

A execução do comando realizara o download da imagem dando o attach ao terminal do container, para não permitir o attach atribua a tag `-d` (detached).

```
docker run -d dockersamples/static-site
```

Faz o link de uma porta interna para externa (`-P` atribui portas aleatórias).

```
docker run -d -P dockersamples/static-site
```

Mapeando as portas de um container (`-p` mapeia portas pela sequencia `local:container`).

```
docker run -d -p 12345:80 dockersamples/static-site
```

Atribuindo um nome durante a criação da imagem (`--name` seta um nome).

```
docker run -d -P --name <nome-da-imagem> dockersamples/static-site
```

Informando variaveis de ambiente na criação do container.

```
docker run -d -P -e AUTHOR="<nome-do-autor>" dockersamples/static-site
```

<br>

## Criando volumes no Ubuntu

Cria um continer a partir da imagem do ubuntu (`-v` indica o caminho do volume)

```
docker run -v "/var/www" ubuntu
```

Cria um continer a partir da imagem do ubuntu (`-v` mapeia o caminho do volume `local:container`).
Tudo aquilo que for escrito em `"/var/www"` sera mapeado para `"/home/wellingtoong/Área de Trabalho/"`.

```
docker run -it \
    -v "/home/wellingtoong/Área de Trabalho/:/var/www" ubuntu
```

<br>

## Executando comandos no container

Cria um continer para executar a aplicação, mapeia os volumes e por fim executa o comando `node npm start`.

```
docker run -d -p 8080:3000 \
    -v "/home/wellingtoong/Área de Trabalho/volume-exemplo/:/var/www" node npm start
```

Também é possivel especificar qual pasta executar o comando apenas passando a tag `-w` (WORK por padrão caso não informe um path ele escolhera aleatóriamente).
A pasta onde se encontram os arquivos é "/home/wellingtoong/Área de Trabalho/volume-exemplo/" mas dentro do container ela é a "/var/www", por isso o WORK é "/var/www".

```
docker run -d -p 8080:3000 \
    -v "$(pwd)/Área de Trabalho/volume-exemplo/:/var/www" \
    -w "/var/www" node npm start
```

<br>

## Template Dockerfile

Template padrão para criação de um arquivo `Dockerfile`.

```
FROM <imagem base>
MAINTAINER <pessoa que esta mantendo a imagem>
ENV <seta as variaveis de ambiente ex.: NODE_ENV=production>

COPY <origem> <destino>
WORKDIR <WORKDIRECT indica o path da execução do container>

RUN <comandos de dependencia ex.: npm install>

ENTRYPOINT <comandos que inicia aplicação ex.: npm start>
EXPOSE <expõe a porta do container>
```

Após criar um Dockerfile é necessário realizar o build da imagem.
Caso queira informar um nome que seja diferente do padrão `Dockerfile` utilize a tag `-f`.
Já a tag `-t` talha a imagem ao nome de usuário.

```
docker build -f <nome-dockerfile-diferente> -t <nome-usuario>/<nome-da-imagem> <path-dockerfile>
```

Exemplo final:

```
docker build -f Dockerfile -t wellingtoong/node .
```

Agora que tenho uma imagem é possivel criar um container a partir da mesma.

```
docker run -d -p 8080:3000 wellingtoong/node
```

## Networking no Docker

Criando uma rede (Bridge)

```
docker network create --driver bridge <nome-da-rede>
```

Atrelando um container a uma rede.

```
docker run -it --name <nome-do-container> --network <nome-da-rede> ubuntu
```
