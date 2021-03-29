# Docker - Nextcloud
## Executando Nextcloud em docker com perfeição + Ngnix Proxy Manager + Redis + Postgres + Onlyoffice

Para quem deseja utilizar um servidor de arquivos privado e seguro de forma simples, rodando em conteiners.
<br />

### Configuração organizada e separada
Organizei toda a configuração em pastas, cada uma contendo um **docker-compose.yml**, exceto a pasta **Agendamento** que contém os arquivos para agendar tarefaz do Nextcloud no **cron** do Nextcloud utilizando o Systemd.

- Pasta **Nextcloud** (Contém o docker-compose.yml contendo os conteines: Nextcloud, Postgres, Redis e Onlyofice)
- Pasta **Ngnix Proxy Manager** (contém o docker-compose contendo o Ngnix Proxy Manager)
- Pasta **Agendamentos** (contém os arquivos do Systemd para manter o cron do nextcloud atualizado)

---
#### Pré-requesitos:

`docker`, `docker-compose` e `git`

Pelo menos dois domínios: um para o **Nextcloud** e um para o **Onlyoffice**. Opcionalmente pode ter mais um domínio para o **Ngnix Proxy Manager** (Local aonde serão configurados todos os domínios vinculados ao docker, já com SSL (*Let's Encrypt*)).
*Caso não queira gastar com um domínio, pode contratar um gratuitamente aqui: https://www.freenom.com/*
<br />
<br />
#### Baixar os arquivos para sua máquina

Comece clonando o repositório `nextcloud` com:  
`git clone https://github.com/talesam/nextcloud.git`

Todos os arquivos que precisa estará dentro do diretório `nextcloud`
<br />
<br />
#### Liberando Firewall
Caso esteja com firewall ativo, libere as portas 80 e 443. Se estiver usando o **ufw**, isso pode ser feito da seguinte maneira:

`sudo ufw allow 80,81,443/tcp`
<br />
<br />
### Configuração e execução do Ngnix Proxy Manager
Acesse o diretório **Ngnix Proxy Manager** e edite o arquivo **docker-compose.yml**. Altere **YOU_PASSWORD** para a senha que desejar e salve. Rode o seguinte comando para subir o Ngnix Proxy Manager:

`docker-compose up -d`

#### Abra o seu navegador para acessar o Ngnix Proxy Manager
| Acessando o NPM |
| --------------- |
| IP_HOST:**81**  |

Por exemplo: <br />
**121.225.31.48:81**

#### Na tela de login coloque os seguintes dados para acessar
login: *admin@example.com* <br />
Senha: *changeme*
<br />
![Captura de tela de 2021-03-28 19-51-11](https://user-images.githubusercontent.com/981368/112770938-ad307a80-8fff-11eb-9eff-55e09b65b94b.png)
<br />
Após a tela de login, insira um email válido e defina uma senha.
<br />
<br />
*OPCIONAL* - Definindo um domínio para o npm (Ngnix Proxy manager)<br />
**Não vou explicar aqui como configurar um domínio, caso não saiba como fazer, pesquise na web.**
<br />
Configure de acordo com a imagem abaixo, altere apenas o domínio para o seu. <br />
| Domínio para NPM                                   |
| -------------------------------------------------- |
| **Forward Hostname / IP:** ngnixproxymanager_app_1 |
| **Forward Port:** 81                               |
<br />

![Captura de tela de 2021-03-28 20-14-18](https://user-images.githubusercontent.com/981368/112771453-642df580-9002-11eb-9c3a-2a42e5e3dce4.png)
<br />

Na aba SSL deixe como na imagem a abaixo e clique em salvar.<br />
![Captura de tela de 2021-03-28 20-20-11](https://user-images.githubusercontent.com/981368/112771618-14036300-9003-11eb-8ace-10e33f12d2d5.png)
<br />

Você deverá ter uma imagem semelhante a essa:
![Captura de tela de 2021-03-28 20-22-29](https://user-images.githubusercontent.com/981368/112771686-647ac080-9003-11eb-85f0-8547769d067c.png)
<br />
<br />
### Configuração e execução do Nextcloud

Acesse o diretório **Nextcloud** e edite o arquivo **db.env**. Altere **YOU_PASSWORD_POSTGRES** para a senha que desejar e salve. Em seguida edite o arquivo **docker-compose.yml** e altere **YOU_PASSWORD_REDIS** para a senha que desejar e salve. Rode o seguinte comando para subir o Ngnix Proxy Manager:

`docker-compose up -d`
