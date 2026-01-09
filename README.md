# zsh

Роль для установки и настройки zsh с oh-my-zsh, популярными плагинами и темой Powerlevel10k. Делает всё необходимое для комфортной работы в терминале: устанавливает zsh, настраивает shell по умолчанию, устанавливает oh-my-zsh с плагинами, настраивает тему и дополнительные инструменты.

## Что делает эта роль

Роль выполняет настройку zsh в несколько этапов:

1. **Установка zsh и зависимостей** - устанавливает zsh, git, curl
2. **Настройка пользователя** - устанавливает zsh как shell по умолчанию для указанного пользователя
3. **Установка oh-my-zsh** - клонирует и настраивает oh-my-zsh framework
4. **Установка плагинов** - устанавливает популярные плагины (autosuggestions, syntax-highlighting)
5. **Установка темы** - устанавливает тему Powerlevel10k (или другую)
6. **Настройка конфигурации** - создаёт .zshrc и .p10k.zsh с оптимальными настройками
7. **Настройка инструментов** - настраивает tmux, nano, mc для работы с кириллицей

## Требования

- Ansible 2.9 или новее
- Debian 11+ (Bullseye, Bookworm, Trixie), Ubuntu 20.04+ (Focal, Jammy, Noble) или RHEL/CentOS 8+
- Права sudo на целевом хосте
- Доступ к интернету для клонирования репозиториев (oh-my-zsh, плагины, темы)

## Базовое использование

Самый простой способ - просто добавить роль в playbook:

```yaml
- hosts: servers
  become: yes
  roles:
    - zsh
```

Это установит и настроит zsh для пользователя, указанного в `ansible_user` (или root по умолчанию).

## Основные параметры

### Включение/выключение роли

```yaml
zsh_enabled: true  # включить роль (по умолчанию true)
```

Если поставить `false`, роль ничего не будет делать. Полезно когда нужно временно отключить настройку zsh в playbook.

### Пользователь для настройки

```yaml
zsh_user: "{{ ansible_user | default('root') }}"  # пользователь (по умолчанию ansible_user или root)
zsh_user_home: "{{ (zsh_user == 'root') | ternary('/root', '/home/' + zsh_user) }}"  # автоматически вычисляется
```

Роль настроит zsh для указанного пользователя. Если не указан, используется `ansible_user` или `root`.

### Установка zsh как shell по умолчанию

```yaml
zsh_set_default_shell: true  # установить zsh как shell по умолчанию (по умолчанию true)
```

Если `false`, zsh будет установлен, но не будет установлен как shell по умолчанию для пользователя.

### Установка пакетов

```yaml
zsh_install_packages: true  # устанавливать пакеты (по умолчанию true)
zsh_packages:
  - zsh
  - git
  - curl
```

Список пакетов для установки. Обычно менять не нужно.

### Установка oh-my-zsh

```yaml
zsh_install_ohmyzsh: true  # установить oh-my-zsh (по умолчанию true)
zsh_ohmyzsh_repo: "https://github.com/ohmyzsh/ohmyzsh.git"
zsh_ohmyzsh_version: "master"
```

Можно указать другую ветку или форк oh-my-zsh.

### Тема

```yaml
zsh_theme: "powerlevel10k/powerlevel10k"  # тема (по умолчанию powerlevel10k/powerlevel10k)
```

Популярные темы:
- `powerlevel10k/powerlevel10k` - быстрая и красивая тема (рекомендуется)
- `robbyrussell` - стандартная тема oh-my-zsh
- `agnoster` - популярная тема с иконками
- `spaceship` - современная тема

### Плагины

```yaml
zsh_plugins:
  - git
  - docker
  - docker-compose
  - kubectl
  - ansible
  - systemd
  - sudo
  - colored-man-pages
  - command-not-found
  - history
  - extract
  - z
  - zsh-autosuggestions
  - zsh-syntax-highlighting
```

Список плагинов для активации. Первые плагины в списке - это стандартные плагины из репозитория oh-my-zsh. Последние два (`zsh-autosuggestions` и `zsh-syntax-highlighting`) - внешние плагины, которые будут установлены автоматически.

### Внешние плагины

```yaml
zsh_install_external_plugins: true  # устанавливать внешние плагины (по умолчанию true)
zsh_external_plugins:
  - name: zsh-autosuggestions
    repo: "https://github.com/zsh-users/zsh-autosuggestions.git"
    dest: "custom/plugins/zsh-autosuggestions"
    version: "master"
  - name: zsh-syntax-highlighting
    repo: "https://github.com/zsh-users/zsh-syntax-highlighting.git"
    dest: "custom/plugins/zsh-syntax-highlighting"
    version: "master"
```

Можно добавить свои внешние плагины, указав репозиторий и путь установки.

### Настройка Powerlevel10k

```yaml
zsh_install_powerlevel10k: true  # установить тему Powerlevel10k (по умолчанию true)
zsh_create_p10k_config: true  # создать базовый конфиг .p10k.zsh (по умолчанию true)
zsh_powerlevel10k_repo: "https://github.com/romkatv/powerlevel10k.git"
zsh_powerlevel10k_version: "master"
```

После установки можно запустить `p10k configure` для интерактивной настройки темы.

### Настройки истории

```yaml
zsh_history_size: 10000  # размер истории в памяти
zsh_history_save_size: 10000  # размер истории для сохранения
zsh_history_options:
  - SHARE_HISTORY
  - HIST_IGNORE_DUPS
  - HIST_IGNORE_ALL_DUPS
  - HIST_REDUCE_BLANKS
  - HIST_VERIFY
```

Настройки истории команд в zsh.

### Настройка дополнительных инструментов

```yaml
zsh_configure_tmux: true  # настроить tmux (по умолчанию true)
zsh_configure_nano: true  # настроить nano (по умолчанию true)
zsh_configure_mc: true  # настроить mc (по умолчанию true)
```

Роль может настроить tmux, nano и Midnight Commander для работы с zsh и кириллицей.

### Кастомные настройки

```yaml
# Кастомные алиасы
zsh_custom_aliases:
  - name: "myalias"
    command: "mycommand"

# Кастомные функции
zsh_custom_functions:
  - "myfunction() { echo 'Hello'; }"

# Кастомный конфиг (добавится в конец .zshrc)
zsh_custom_config: |
  export MY_VAR="value"
```

Можно добавить свои алиасы, функции и настройки в .zshrc.

### Резервное копирование

```yaml
zsh_backup_existing_configs: true  # делать резервные копии (по умолчанию true)
```

Если `true`, существующие `.zshrc` и `.p10k.zsh` будут скопированы с временной меткой перед перезаписью.

## Примеры использования

### Простая установка для текущего пользователя

```yaml
- hosts: servers
  become: yes
  roles:
    - zsh
```

### Установка для конкретного пользователя

```yaml
- hosts: servers
  become: yes
  roles:
    - role: zsh
      vars:
        zsh_user: "developer"
```

### Установка с другой темой

```yaml
- hosts: servers
  become: yes
  roles:
    - role: zsh
      vars:
        zsh_theme: "agnoster"
        zsh_install_powerlevel10k: false
```

### Установка только zsh без oh-my-zsh

```yaml
- hosts: servers
  become: yes
  roles:
    - role: zsh
      vars:
        zsh_install_ohmyzsh: false
        zsh_set_default_shell: true
```

### Установка с дополнительными плагинами

```yaml
- hosts: servers
  become: yes
  roles:
    - role: zsh
      vars:
        zsh_external_plugins:
          - name: zsh-autosuggestions
            repo: "https://github.com/zsh-users/zsh-autosuggestions.git"
            dest: "custom/plugins/zsh-autosuggestions"
            version: "master"
          - name: zsh-syntax-highlighting
            repo: "https://github.com/zsh-users/zsh-syntax-highlighting.git"
            dest: "custom/plugins/zsh-syntax-highlighting"
            version: "master"
          - name: my-custom-plugin
            repo: "https://github.com/user/my-plugin.git"
            dest: "custom/plugins/my-plugin"
            version: "main"
```

### Установка для нескольких пользователей

```yaml
- hosts: servers
  become: yes
  roles:
    - role: zsh
      vars:
        zsh_user: "{{ item }}"
  loop:
    - user1
    - user2
    - user3
```

### Кастомные настройки

```yaml
- hosts: servers
  become: yes
  roles:
    - role: zsh
      vars:
        zsh_custom_aliases:
          - name: "kns"
            command: "kubectl config set-context --current --namespace"
          - name: "kgp"
            command: "kubectl get pods"
        zsh_custom_functions:
          - "kexec() { kubectl exec -it $1 -- /bin/bash; }"
        zsh_custom_config: |
          export KUBECONFIG="$HOME/.kube/config"
          export PATH="$PATH:$HOME/.local/bin"
```

## Теги

Роль использует теги для запуска отдельных частей:

### Основные теги

- `zsh` - все задачи роли
- `always` - всегда выполняемые задачи (проверки)

### Теги по функциональности

- `install` или `zsh:install` - только установка пакетов
- `user` или `zsh:user` - только настройка пользователя и shell
- `ohmyzsh` или `zsh:ohmyzsh` - только установка oh-my-zsh
- `plugins` или `zsh:plugins` - только установка плагинов
- `theme` или `zsh:theme` - только установка темы
- `config` или `zsh:config` - только настройка конфигурации
- `tools` или `zsh:tools` - только настройка дополнительных инструментов

### Теги для инструментов

- `tmux` - настройка tmux
- `nano` - настройка nano
- `mc` - настройка mc


### Примеры использования тегов

Запустить только установку пакетов:

```bash
ansible-playbook playbook.yml --tags install
```

Запустить только настройку конфигурации:

```bash
ansible-playbook playbook.yml --tags config
```

Запустить всё кроме настройки инструментов:

```bash
ansible-playbook playbook.yml --skip-tags tools
```

Запустить только настройку tmux:

```bash
ansible-playbook playbook.yml --tags tmux
```

## Структура роли

Роль разделена на логические модули:

- `tasks/install.yml` - установка zsh и зависимостей
- `tasks/user.yml` - настройка пользователя и shell
- `tasks/ohmyzsh.yml` - установка oh-my-zsh
- `tasks/plugins.yml` - установка внешних плагинов
- `tasks/theme.yml` - установка темы Powerlevel10k
- `tasks/config.yml` - настройка конфигурации (.zshrc, .p10k.zsh)
- `tasks/tools.yml` - настройка дополнительных инструментов (tmux, nano, mc)
- `handlers/main.yml` - обработчики событий
- `templates/zshrc.j2` - шаблон конфигурации .zshrc
- `templates/p10k.zsh.j2` - шаблон конфигурации .p10k.zsh

Все параметры находятся в `defaults/main.yml`.

## Поддерживаемые дистрибутивы

- Debian 11 (Bullseye)
- Debian 12 (Bookworm)
- Debian 13 (Trixie)
- Ubuntu 20.04 (Focal)
- Ubuntu 22.04 (Jammy)
- Ubuntu 24.04 (Noble)
- RHEL/CentOS 8+
- Rocky Linux 8+

## Применение изменений

После выполнения роли для применения изменений:

1. Перезайдите в систему, или
2. Выполните: `exec zsh`, или
3. Откройте новый терминал

Если используете tmux, перезапустите tmux или выполните `exec zsh` в сессии.

## Настройка Powerlevel10k

После установки можно запустить интерактивную настройку темы:

```bash
p10k configure
```

Это запустит мастер настройки, который поможет настроить внешний вид промпта.

## Устранение неполадок

### Zsh не стал shell по умолчанию

Проверьте, что пользователь существует и роль выполнилась с правами root:

```bash
getent passwd username
chsh -s /usr/bin/zsh username
```

### Oh-my-zsh не установился

Проверьте:
1. Установлен ли git: `which git`
2. Есть ли доступ к интернету: `ping github.com`
3. Права на домашнюю директорию пользователя

### Плагины не работают

Убедитесь, что:
1. Плагины установлены в правильные директории: `ls ~/.oh-my-zsh/custom/plugins/`
2. Плагины добавлены в список `zsh_plugins` в `.zshrc`
3. Выполнили `exec zsh` или перезашли в систему

### Тема не применяется

Проверьте:
1. Тема установлена: `ls ~/.oh-my-zsh/custom/themes/` или `ls ~/.oh-my-zsh/themes/`
2. Правильное имя темы в `.zshrc`: `grep ZSH_THEME ~/.zshrc`
3. Выполнили `exec zsh` или перезашли в систему

## Важные замечания

1. **Права пользователя**: Роль должна выполняться с правами root (`become: yes`), но некоторые задачи выполняются от имени пользователя (`become_user`).

2. **Резервные копии**: Роль автоматически создаёт резервные копии существующих конфигураций перед перезаписью. Они сохраняются с временной меткой.

3. **Git**: Для установки oh-my-zsh и плагинов требуется git. Роль проверяет наличие git и предупреждает, если его нет.

4. **Интернет**: Роль требует доступа к интернету для клонирования репозиториев GitHub.

5. **Множественные пользователи**: Для настройки zsh для нескольких пользователей запустите роль несколько раз с разными значениями `zsh_user` или используйте `loop`.

6. **Обновление oh-my-zsh**: Роль обновляет oh-my-zsh при каждом запуске. Если нужно отключить обновление, используйте тег `--skip-tags ohmyzsh`.

## Лицензия

ISC
