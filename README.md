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

Acesse o diretório **Nextcloud** e edite o arquivo **db.env**. Altere **YOU_PASSWORD_POSTGRES** para a senha que desejar e salve. Em seguida edite o arquivo **docker-compose.yml** e altere **YOU_PASSWORD_REDIS** para a senha que desejar e salve. Rode o seguinte comando para subir os conteiners do Nextcloud, Ngnix, Redis e OnlyOffice:

`docker-compose up -d`

#### Confiruração de domínio do NextCloud no NPM

Acesse **Hosts** -> **Proxy Hosts** -> **Add Proxy Host** para adicionar um novo domínio.

| Aba Details | Configuração |
| --- | --- |
| Domain Names | seu_dominio.com |
| Scheme | http |
| Forward Hostname / IP | nextcloud |
| Forward Port | 80 |
<br />

Ative as outras 3 opções como na imagema abaixo:<br />
![Captura de tela de 2021-03-29 00-25-01](https://user-images.githubusercontent.com/981368/112783225-3c9c5480-9025-11eb-81ea-2aa1c9d52caa.png)
<br />

Na aba **SSL**, deixe como na imagem abaixo ou ative mais alguma opção se preferir:<br />
![Captura de tela de 2021-03-29 00-26-21](https://user-images.githubusercontent.com/981368/112783351-86853a80-9025-11eb-9e5b-150e6f88d2eb.png)
<br />
 - Após finalizar, verifique se as opções de **SSL** estão ativas, senão ative-as.

Na aba **Advanced** insira o seguinte conteúdo e salve:
```
proxy_set_header Host $host;
proxy_set_header X-Forwarded-Proto $scheme;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_max_temp_file_size 16384m;
client_max_body_size 0;

location = /.well-known/carddav {
      return 301 $scheme://$host:$server_port/remote.php/dav;
    }
    location = /.well-known/caldav {
      return 301 $scheme://$host:$server_port/remote.php/dav;
    }
```

#### Instalação e configuração do sistema NextCloud

Acesse o domínio configurado, defina um nome de usuário e senha, e finalize a configuração inicial.

Edite o arquivo config.php do NextCloud para acrescentar algumas opções no final. Aqui irão entrar algumas correções, melhorias e ativação do Redis. <br />
`nano volumes/nextcloud/config/config.php`

Acrescente o seguinte conteúdo:
```
'redis' =>
   array (
     'host' => 'redis',
     'port' => 6379,
     'password' => 'YOU_PASSWORD_REDIS',
),

'default_phone_region' => 'BR',
'trashbin_retention_obligation' => '30, 60',
'overwriteprotocol' => 'https',
'maintenance' => false,
'default_language' => 'pt_BR',
'default_locale' => 'pt_BR',
```
<br />

#### Derrube e levante os conteiners para ativar as novas configurações:
```
docker-compose down
docker-compose up -d
```


