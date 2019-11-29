- [Git flow](#git-flow)
- [SSH Github & Gitlab](#ssg-github-&-gitlab)
- [Git Bash](#git-bash)

## Git flow

**Фича** (Feature) - новый функционал на проекте. 

### Разработка новой фичи
* Разработка новых фич начинается с ветки `develop`, от которой создаётся новая ветка `feature/name`, где `name` - название фичи.
* Разработчик переключается на новую ветку и начинает работать с ней.
* После завершения фичи создаётся Pull Request.
* Ветка `feature/name` сливается с (merge into) веткой `develop`. 
* Ветка `feature/name` удаляется.
* Разработчик переключается обратно на ветку `develop`.

### Релиз в production
* От ветки `develop` создаётся ветка `release/vX.X.X`.
* Ветка `release/vX.X.X` при необходимости помечается тэгом `vX.X.X`.
* Разрешены мелкие исправления (minor bug fixes) в ветке `release/vX.X.X`.
* Ветка `release/vX.X.X` сливается с веткой `master`.
* Ветка `release/vX.X.X` сливается обратно (back-merge) с веткой `develop`.
* Ветка `release/vX.X.X` удаляется.
* Разработчик переключается обратно на ветку `develop`.

## Hotfix в production
* Если в production найден серьёзный баг, который нужно быстро исправить, от ветки `master` создаётся ветка `hotfix/name`, в которой делаются необходимые исправления.
* Ветка `hotfix/name` сливается с веткой `master`.
* Ветка `hotfix/name` сливается с веткой `develop`.
* Ветка `hotfix/name` удаляется.


## SSH Github & Gitlab

* Открыть Bash.

### Генерация ключей
* Сгенерировать ключ для Github при помощи `ssh-keygen -t rsa -C "email" -f ~/.ssh/id_rsa_github`, где нужно заменить `email` на свой. `id_rsa_github` - название файла, где будет лежать приватный ключ. Также будет сгенерирован публичный ключ `id_rsa_github.pub` в той же папке.
* Ввести ключевую фразу и повторить её.

* Сгенерировать ключ для Gitlab при помощи `ssh-keygen -t rsa -C "email" -f ~/.ssh/id_rsa_gitlab`, где нужно заменить `email` на свой.
* Ввести ключевую фразу и повторить её.

### Cоздание ключа на Github
* Скопировать публичный ключ `~/.ssh/id_rsa_github` для Github. Например, вывести его на экран при помощи `cat ~/.ssh/id_rsa_github.pub` и скопировать через `Ctrl + C`.
* Открыть на Github [Settings > SSH keys](https://github.com/settings/keys) и нажать на добавление нового ключа.
* Вставить скопированный ключ, придумать название для него и сохранить.

### Cоздание ключа на Gitlab
* Скопировать публичный ключ `~/.ssh/id_rsa_gitlab` для Gitlab.
* Открыть на Gitlab `Settings > SSH keys`.
* Вставить скопированный ключ, придумать название для него и сохранить.

### Добавление ключа в SSH-agent
* Добавить SSH-ключ для GitHub в SSH-agent `ssh-add ~/.ssh/id_rsa_github`. Если он не запущен, то нужно сперва его запустить `eval $(ssh-agent -s)` или `ssh-agent bash`.
* Добавить SSH-ключ для GitLab в SSH-agent `ssh-add ~/.ssh/id_rsa_gitlab`.
* Проверить, что ключи добавлены через `ssh-add -L`.

### Конфигурация

* Создать файл `touch ~/.ssh/config`.
* Вставить туда следующее
```
# config for github
Host github.com
   HostName github.com
   User git
   IdentityFile ~/.ssh/id_rsa_github
# config for gitlab
Host gitlab.com
   HostName gitlab.com
   User git
   IdentityFile ~/.ssh/id_rsa_gitlab
```
* Сохранить изменения.

### Проверка
* Попробовать сделать `git pull` через SSH.
* Дополнительная проверка для Github: `ssh -T git@github.com` (режим отладки: `ssh -T git@github.com`).
* Дополнительная проверка для Gitlab: `ssh -T git@gitlab.com` (режим отладки: `ssh -T git@gitlab.com`).

## Git Bash

**Bash** (Bourne again shell, "Born again shell", "возрождённый" shell) — усовершенствованная версия ранней командной оболочки *Bourne shell*, исполняющей `.sh` файлы в `UNIX`.

Чтобы на *определённом порте* *убить запущенный процесс*, нужно узнать его **PID** (Process Identifier).
```css
netstat -ano | findstr :PORT /* например, :3000 */
/* Скопировать PID из последнего стобца результата и вставить в команду ниже */
tskill PID
```
