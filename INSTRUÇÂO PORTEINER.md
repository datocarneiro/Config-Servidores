# Porteiner


Site Oficial do Portainer.io: https://www.portainer.io/

O QUE É E PARA QUE SERVER O PORTAINER.IO: 
Portainer Community Edition (CE) é a nossa base. Com mais de meio milhão de usuários regulares, o CE é um poderoso conjunto de ferramentas de código aberto que permite criar e gerenciar facilmente contêineres no Docker, Docker Swarm, Kubernetes e Azure ACI.


Para verificar se o **Portainer** está instalado e rodando no servidor, você pode usar alguns métodos dependendo de como ele foi instalado (Docker, Docker Compose ou pacote nativo).

---

## Verificar se foi instalado via **Docker**

### Verificar container ativo

```bash
docker ps | grep portainer
```

* Se estiver rodando, verá algo como:

```
CONTAINER ID   IMAGE                    COMMAND       ...   NAMES
abc123         portainer/portainer-ce   "/portainer"  ...   portainer
```

* Se não houver saída, o container **não está ativo**.

### Verificar container existente (parado ou ativo)

```bash
docker ps -a | grep portainer
```

* Mostra containers mesmo que estejam parados.

### Verificar imagem

```bash
docker images | grep portainer
```

* Mostra se a imagem do Portainer está baixada no servidor.

---

## Verificar se foi instalado via **Docker Compose**

* Entre na pasta onde está o `docker-compose.yml`:

```bash
docker-compose ps
```

* Procure pelo serviço `portainer`.

---

## Verificar serviço systemd (se foi criado um service)

Se você criou um serviço systemd, como no `portainer.service`:

```bash
sudo systemctl status portainer
```

* **Ativo:** `Active: active (running)`
* **Parado:** `Active: inactive (dead)`
* **Não existe:** `Unit portainer.service could not be found`

---

## Teste via navegador

* Se o Portainer estiver rodando no servidor, normalmente escuta na porta `9000` (ou configurada por você).
* Acesse:

```
http://<IP_DO_SERVIDOR>:9000
```

* Se abrir a interface web, está instalado e ativo.

---

## 01_ Pesquisando o Container do Portainer.io CE no Docker Hub


Pesquisar de forma manual via Navegador link de consulta do Docker Hub: https://hub.docker.com/

via CLI: 
```bash 
docker search portainer/portainer-ce:latest
```

## 02_ Criando o Volume do Portainer.io CE no Docker-CE
criação do volume do Portainer.io CE

Documentação do Docker-CE: https://docs.docker.com/reference/cli/docker/volume/

Localização padrão onde vai ficar o volume:
```bash
/var/lib/docker/volumes/<nome_do_volume>/_data
```
Este caminho pertence ao sistema Docker, normalmente acessível apenas por root ou pelo usuário do Docker (docker group).

Não fica dentro de /home/usuario, a menos que você configure explicitamente um bind mount.

Bind mounts (montagem de diretório do host)

```bash 
#criação do volume do Portainer.io CE
docker volume create portainer_data

#listando o volume criado do Portainer.io CE
docker volume ls

# inspecionando o volume criado do Portainer.io CE
docker volume inspect portainer_data

# Acessar como root
sudo -i cd /var/lib/docker/volumes/


# ou apenas:
sudo ls /var/lib/docker/volumes/

# Mostra os volumes e seus detalhes sem precisar mudar para root.
sudo ls -l /var/lib/docker/volumes/
```
> Não é recomendável alterar permissões do Docker 

> Evite fazer chmod 777 /var/lib/docker/volumes/ ou algo parecido, pois quebra a segurança do Docker. 

> Sempre use sudo para acessar essa pasta.

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
sudo wget -v -O /etc/systemd/system/portainer.service https://raw.githubusercontent.com/datocarneiro/Config-Servidores/refs/heads/main/Arquivos%20de%20Configuracoes/portainer.service
```

# 07_ Habilitando o Serviço do Portainer.io no Ubuntu Server
```bash
#habilitando o serviço do Portainer.io
sudo systemctl daemon-reload
sudo systemctl enable portainer
sudo systemctl stop portainer
sudo systemctl start portainer
```
# 08_ Verificando o Serviço e Versão do Portainer.io no Ubuntu Server
```bash
#verificando o serviço do Apache Tomcat Server
sudo systemctl status portainer
sudo systemctl restart portainer
sudo systemctl stop portainer
sudo systemctl start portainer

#analisando os Log's e mensagens de erro do Servidor do Portainer.io
#opção do comando journalctl: x (catalog), e (pager-end), u (unit)
sudo journalctl -xeu portainer

```

# 09_ Liberando a Conexão de Entrada da Porta do Portainer no UFW do Ubuntu Server
```bash
#Liberando (allow) e Logando Tudo (LOG-ALL) da Sub-rede 172.16.1.0/24 (FROM) acessar o 
#servidor (TO) do Portainer na porta (PORT) 9000 via protocolo TCP (PROTO TCP)
sudo ufw limit log-all from <IP da REDE, ou SUBREDE OU HOST UNICO> to <IP SERVIDOR> port 9000 proto tcp comment 'Liberando a sub-rede para acessar o Portainer'

#Verificando as Regras Detalhadas padrão do UFW em modo Verboso
sudo ufw status verbose
```
# 10_ Acessando e configurando o Portainer.io via navegador

> OBSERVAÇÃO IMPORTANTE (NÃO COMENTADO NO VÍDEO): SE VOCÊ DEMORAR MUITO PARA ACESSAR PELA PRIMEIRA VEZ O PORTAINER O ERRO DE: Failure: Unable to resolve server settings and status, IRÁ APARECER NA TELA DO SEU NAVEGADOR, PARA RESOLVER ESSA FALHA VOCÊ PRECISAR REINICIAR O CONTAINER DO PORTAINER UTILIZANDO, POR EXEMPLO O COMANDO: sudo systemctl stop portainer E DEPOIS O COMANDO: sudo systemctl start portainer.

```bash
#utilizar os navegadores para testar o acesso ao Portainer.io 
firefox ou google chrome: http://endereço_ipv4_ubuntuserver:9000

#Informações que serão solicitadas na configuração via Web do Portainer.io
Please create the initial administrator user.
  Username: seu_usuário
  Password: sua_senha
  Confirm password: sua_senha
  (ON) Allow collection of anonymous statistics. You can find more information about this in our privacy policy.
<Create User>

#configurações iniciais do Portainer.io
Quick Setup
  Environment Wizard
    <Get Started>

#acessando as configurações do Docker-CE do Portainer
Home
  Enviroments
    Docker-CE (local)
      Dashboard

```