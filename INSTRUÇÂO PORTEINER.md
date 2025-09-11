# Porteiner


Site Oficial do Portainer.io: https://www.portainer.io/

O QUE É E PARA QUE SERVER O PORTAINER.IO: 
Portainer Community Edition (CE) é a nossa base. Com mais de meio milhão de usuários regulares, o CE é um poderoso conjunto de ferramentas de código aberto que permite criar e gerenciar facilmente contêineres no Docker, Docker Swarm, Kubernetes e Azure ACI.

video instrução: https://www.youtube.com/watch?v=xe_ZZ_hrudw

## 01_ Pesquisando o Container do Portainer.io CE no Docker Hub


Pesquisar de forma manual via Navegador link de consulta do Docker Hub: https://hub.docker.com/

via CLI: 
```bash 
docker search portainer/portainer-ce:latest`
```

## 02_ Criando o Volume do Portainer.io CE no Docker-CE
criação do volume do Portainer.io CE

Documentação do Docker-CE: https://docs.docker.com/reference/cli/docker/volume/

opção do comando docker:
 volume (Manage volumes),
  create (Creates a new volume that 
#containers can consume and store data in), portainer_data (Volume Name)

```bash 
#criação do volume do Portainer.io CE
docker volume create portainer_data

#listando o volume criado do Portainer.io CE
docker volume ls

# inspecionando o volume criado do Portainer.io CE
docker volume inspect portainer_data
```
# 03_ Criando o Container do Portainer.io CE e utilizando o Volume criado no Docker-CE

Documentação do Docker-CE: https://docs.docker.com/reference/cli/docker/container/
```bash
#criação do container do Portainer.io CE utilizando o volume criado
docker run --name portainer -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock \
-v portainer_data:/data portainer/portainer-ce:latest

#listando todas as imagens de containers no Docker-CE
docker images

#filtrando o container do Portainer.io CE no Docker-CE
docker container ls -q -f name=portainer
```
# 05_ Verificando a Porta de Conexão do Portainer.io no Docker-CE
> ⚠ OBSERVAÇÃO IMPORTANTE: no Ubuntu Server as Regras de Firewall utilizando o comando: iptables ou: ufw está desabilitado por padrão (INACTIVE), caso você tenha habilitado algum recurso de Firewall é necessário fazer a liberação do Fluxo de Entrada (INPUT), Porta (PORT) e Protocolo (PROTOCOL) TCP do Serviço corresponde nas tabelas do firewall e testar a conexão.

```bash
#verificando a porta padrão TCP-22 do OpenSSH Server
#opção do comando lsof: -n (network number), -P (port number), -i (list IP Address), -s (alone directs)
sudo lsof -nP -iTCP:'9000' -sTCP:LISTEN
```

# 06_ Criando o arquivo de Serviço do Portainer.io CE no Ubuntu Server
```bash
#baixado o arquivo de serviço do Portainer.io do Github
#opção do comando wget: -v (verbose), -O (output file)
sudo wget -v -O /etc/systemd/system/portainer.service https://raw.githubusercontent.com/vaamonde/ubuntu-2404/main/conf/portainer.service
