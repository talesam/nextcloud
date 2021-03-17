# Docker - Nextcloud
## Executando Nextcloud em docker com perfeição + Ngnix Proxy Manager + Redis + Postgres + Onlyoffice

Para quem deseja utilizar um servidor de arquivos privado e seguro de forma simples, rodando em conteiners.

### Configuração organizada e separada
Organizei toda a configuração em pastas, cada uma contendo um **docker-compose.yml**, exceto a pasta **Agendamento** que contém os arquivos para agendar tarefaz do Nextcloud no **cron** do Nextcloud utilizando o Systemd.

- Pasta Nextcloud (Contém o docker-compose.yml contendo os conteines: Nextcloud, Postgres, Redis e Onlyofice)
- Paste Ngnix Proxy Manager (contém o docker-compose contendo o Ngnix Proxy Manager)
