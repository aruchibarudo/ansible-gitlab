# Ansible Role: GitLab

Установка gitlab с внешним postgresql. Пароль root будет запрошен при первом подключении к веб. 
Поддерживается RedOS 7.3.1

## Requirements

Доступ к репозиториям gitlab и ОС

## Role Variables

```yaml
# конфигурация веб лица
gitlab_external_url: https://git.devzone.local # внешний адрес сервера gitlab

# конфигурвция для подключения к внешнему postresql серверу
gitlab_db_external: yes # используем внешнюю СУБД
gitlab_db_host: localhost # адрес сервера
gitlab_db_port: 15432 # порт СУБД
gitlab_db_username: gitlab # имя пользователя
gitlab_db_password: # пароль

# конфигурация git
gitlab_git_data_dir: "/var/opt/gitlab/git-data" # путь для данных приложения

# SSL Configuration.
gitlab_redirect_http_to_https: true
gitlab_ssl_certificate: "/etc/gitlab/ssl/gitlab.crt"
gitlab_ssl_certificate_key: "/etc/gitlab/ssl/gitlab.key"

# Gitlab timezone.
gitlab_time_zone: "Europe/Moscow"

# Настройка proxy
use_proxy: yes
proxy: http://proxy.local:3128
no_proxy: .local
```
## Playbook example
```yaml
- hosts: servers
    become: yes
    vars_files:
    - gitlab-devzone.yml
    roles:
    - gitlab
```
*Inside `gitlab-devzone.yml`*:
```yaml
gitlab_external_url: "https://gitlab.example.com/"
```

## Cleanup
```shell
systemctl stop gitlab-runsvdir
systemctl disable gitlab-runsvdir
rm -f /usr/lib/systemd/system/gitlab-runsvdir.service
systemctl daemon-reload

dnf remove gitlab-ce
rm -rf /opt/gitlab/ && \
rm -rf /var/opt/gitlab/ && \
rm -rf /etc/gitlab/gitlab* /etc/gitlab/initial_root_password  /etc/gitlab/trusted-certs/ && \
rm -rf /var/log/gitlab/ && \
rm -rf /app/gitlab/git-data/ && \
rm -rf /app/gitlab-backup/*

# on database server
drop database gitlabhq_production;
```
## License

MIT / BSD

## Author Information

This role was created in 2014 by [Jeff Geerling](http://jeffgeerling.com/), author of [Ansible for DevOps](http://ansiblefordevops.com/).
Adapted for RedOS by Artem Batalov [batalov.av@gazprom-neft.ru](mailto:batalov.av@gazprom-neft.ru)
