# 01_ Verificando as informações do Locale (Localidade) do Sistema Operacional Ubuntu Server
```bash
#verificando as informações detalhas de localidade no Ubuntu Server
sudo localectl
Entendendo a saída do comando: localectl (NÃO COMENTADO NO VÍDEO)

#verificando as informações de localidades instaladas no Ubuntu Server 
#opção do comando locale: -a (all-locales)
sudo locale -a
```

# 02_ Configurando o Locale (Localidade) do Brasil no Sistema Operacional Ubuntu Server


OBSERVAÇÃO IMPORTANTE: O pt_BR.UTF-8 é uma codificação de caractere que indica o uso da língua portuguesa (pt) como falada no Brasil (BR) com a codificação UTF-8. UTF-8 (Unicode Transformation Format - 8 bits) é uma codificação de caracteres que pode representar qualquer caractere no conjunto Unicode, o que inclui praticamente todos os caracteres de todas as línguas do mundo.

```bash
#instalando o suporte ao Locales
sudo apt install locales

#gerando a localidade do Português do Brasil
sudo locale-gen pt_BR.UTF-8

#configurando a localidade Português do Brasil
#opção do comando localectl: set-locale (Set the system locale)
sudo localectl set-locale LANG=pt_BR.UTF-8

#atualizando as localidades do Português do Brasil e Linguagem do Sistema
sudo update-locale LANG=pt_BR.UTF-8 LC_ALL=pt_BR.UTF-8 LANGUAGE="pt_BR:pt:en"

#recomendado rebootar o sistema para testar as localidades
sudo reboot
#verificando as mudanças de localidades do sistema
#opção do comando locale: -a (all-locales)
sudo localectl
sudo locale -a
```

# 03_ Verificando as informações do Timezone (Fuso Horário) do Sistema Operacional Ubuntu Server

OBSERVAÇÃO IMPORTANTE: no sistema operacional Ubuntu Server temos basicamente 03 (três) configurações de hora (time) sendo elas:

Local time (Hora Local do Servidor - Software/OS);
Universal time (Hora Universal - UTC Horário Universal Coordenado);
RTC (Real-time clock) time (Relógio de Tempo Real - BIOS/Hardware).

```bash
#verificando as informações de fuso horário do sistema no Ubuntu Server
sudo timedatectl
```
# 04_ Configurando o Timezone (Fuso Horário) de São Paulo no Sistema Operacional Ubuntu Server
OBSERVAÇÃO IMPORTANTE: geralmente mudar para o Time Zone de America/Sao_Paulo a hora fica errada no sistema, nesse caso podemos mudar para America/Fortaleza ou America/Bahia esse error é por causa do Fuso Horário em relação ao Horário de Verão que não existe mais no Brasil (foi criado em 1931 pelo Governo Getúlio Vargas, só começou a ser aplicado no Brasil em 1985 no Governo José Sarney e foi cancelado em 2018 no Governo Bolsonaro).

OBSERVAÇÃO IMPORTANTE: Até o momento (25/06/2025), o horário de verão 2025 está em Processo de Avaliação pelo Governo Federal. De acordo com o ministro de Minas e Energia, Alexandre Silveira, a volta da medida será analisada com base na situação hídrica e na segurança energética. "Nós temos a segurança energética assegurada, há o início de um processo de restabelecimento ainda muito modesto da nossa condição hídrica. Temos condições de chegar depois do verão em condição de avaliar, sim, a volta dessa política em 2025"

OBSERVAÇÃO: ALTERAR O LOCALE CONFORME A LOCALIDADE DO SEU SERVIDOR, MAIS INFORMAÇÕES SOBRE TIMEZONE ACESSE: https://en.wikipedia.org/wiki/List_of_tz_database_time_zones

```bash

#listando os Timezones disponíveis do comando timedatectl (PARA SAIR PRESSIONE Q (quit))
#opção do comando timedatectl: list-timezones (List available time zones, one per line)
sudo timedatectl list-timezones

#configurando o fuso horário de America São Paulo no Ubuntu Server
#opção do comando timedatectl: set-timezone (set the system time zone to the specified value)
sudo timedatectl set-timezone "America/Sao_Paulo"

#verificando as mudanças do Timezone no Sistema do Ubuntu Server
sudo timedatectl
```

# 05_ Configurando o Sincronismo de Data e Hora com o Protocolo NTP no Ubuntu Server
OBSERVAÇÃO: O NTP (Network Time Protocol) é um protocolo para sincronização dos relógios dos computadores baseado no protocolo UDP sob a porta 123. É utilizado para sincronização do relógio de um conjunto de computadores e dispositivos em redes de dados com latência variável.

```bash
#editando o arquivo de configuração timesyncd.conf
#mais informações veja a documentação oficial em: https://documentation.ubuntu.com/server/how-to/networking/timedatectl-and-timesyncd/
sudo vim /etc/systemd/timesyncd.conf

#entrando no modo de edição do editor de texto VIM
INSERT
```
OBSERVAÇÃO IMPORTANTE: no Brasil sempre utilizar o site: https://ntp.br/ para o sincronismo de Data e Hora de forma correta nos servidores. O ntp.br é o serviço oficial de sincronização de horário do Brasil, mantido pelo Observatório Nacional (ON), em parceria com o NIC.br (Núcleo de Informação e Coordenação do Ponto BR).
```bash 
#descomentar e alterar os valores das variáveis a partir da linha: 14
[Time]
NTP=a.st1.ntp.br
FallbackNTP=a.ntp.br
#salvar e sair do arquivo
ESC SHIFT : x <Enter>

```
# 06_ Reinicializar o serviço do Systemd Timesyncd (Sincronismo de Data e Hora) no Ubuntu Server
```bash 
#reiniciar o serviço do Timesyncd
#opção do comando systemctl: restart (Stop and then start one or more units specified on the command line)
sudo systemctl restart systemd-timesyncd

#verificar o status do serviço do Timesyncd
#opção do comando systemctl: status (Show terse runtime status information about one or more units)
sudo systemctl status systemd-timesyncd

#verificando as informações de data e hora atualizada
sudo timedatectl

#verificar as informações do sincronismo do Timesyncd (NÃO COMENTADO NO VÍDEO)
#opção do comando timedatectl: timesync-status
sudo timedatectl timesync-status
```


# 08_ Sincronizando Data e Hora do Sistema Operacional com o Hardware (BIOS) no Ubuntu Server
OBSERVAÇÃO IMPORTANTE: mesmo cenário da utilização do comando date, a da Data e hora da BIOS do Hardware é mantida pela CMOS e Bateria que mantém essa hora armazenada, caso a Data e Hora de BIOS esteja errada, recomendo verificar a Bateria pois já é um sinal de falha de Hardware, no GNU/Linux você pode sincronizar a Data e Hora de Software para o Hardware e vice-versa, também, não é recomendo a sua utilização.
```bash
#opção do comando hwclock: --systohc (system clock to hardware clock), --hctosys (hardware 
#clock to system clock)
sudo hwclock --show
sudo hwclock --systohc
sudo hwclock --hctosys
```
_______________________________________________________________________________________________________________________

# 02_ Atualizando e editando os arquivos de configuração do OpenSSH Server e do Banner

```bash
#fazendo o backup do arquivo de configuração do OpenSSH Server
#opção do comando cp: -v (verbose)
sudo cp -v /etc/ssh/sshd_config /etc/ssh/sshd_config.old

#atualizando o arquivo de configuração do OpenSSH Server do Github
#opção do comando wget: -v (verbose), -O (output file)
sudo wget -v -O /etc/ssh/sshd_config https://raw.githubusercontent.com/datocarneiro/Config-Servidores/refs/heads/main/sshs.config

#atualizando arquivo de configuração do Banner do Ubuntu Server do Github
#opção do comando wget: -v (verbose), -O (output file)
sudo wget -v -O /etc/issue.net https://raw.githubusercontent.com/datocarneiro/Config-Servidores/refs/heads/main/banner-dato-menor.txt

#editando o arquivo de configuração do OpenSSH Server
sudo vim /etc/ssh/sshd_config

#entrando no modo de edição do editor de texto VIM
INSERT
#alterar a variável ListenAddress na linha: 27 
#ListenAddress 172.16.1.xxx para: SEU_ENDEREÇO_IPV4_DO_UBUNTU
#OBSERVAÇÃO: ALTERAR O ENDEREÇO IPv4 CONFORME A SUA NECESSIDADE
ListenAddress SEU_ENDEREÇO_IPV4_DO_UBUNTU

#alterar a variável AllowUsers na linha: 77
#OBSERVAÇÃO: ALTERAR O USUÁRIO DE ACESSO CONFORME A SUA NECESSIDADE
AllowUsers SEU_USUÁRIO

#alterar a variável AllowGroups na linha: 83
#OBSERVAÇÃO: ALTERAR O GRUPO DE ACESSO CONFORME A SUA NECESSIDADE
AllowGroups SEU_GRUPO_DO_USUÁRIO
#salvar e sair do arquivo
ESC SHIFT :x <Enter>

#testando o arquivo de configuração do OpenSSH SERVER (NÃO COMENTADO NO VÍDEO)
#opção do comando sshd: -t (text mode check configuration)
sudo sshd -t

#editando o arquivo de configuração do Banner do Ubuntu Server
sudo vim /etc/issue.net

#entrando no modo de edição do editor de texto VIM
INSERT
#alterar a linha 5: Servidor e Admin
#OBSERVAÇÃO: ALTERAR O BANNER CONFORME A SUA NECESSIDADE
Servidor: ctnseunome - Admin: SEU NOME E SOBRENOME
#salvar e sair do arquivo
ESC SHIFT :x <Enter>

#reiniciar e verificar o status do serviço do OpenSSH Server
sudo systemctl restart ssh
sudo systemctl status ssh

#analisando os Log's e mensagens de erro do Servidor do OpenSSH (NÃO COMENTADO NO VÍDEO)
#opção do comando journalctl: -t (identifier), x (catalog), e (pager-end), u (unit)
sudo journalctl -t sshd
sudo journalctl -xeu ssh