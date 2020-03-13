- [pull vs fetch](#pull-vs-fetch)
- [Полезные команды Git](#полезные-команды-git)
- [Git flow](#git-flow)
- [SSH Github & Gitlab](#ssh-github--gitlab)
- [Git Config](#git-config)
- [Git Bash](#git-bash)

## pull vs fetch

**Команда git fetch** используется для того, чтобы получить информацию о последних изменениях на удалённой ветке (`origin/`). Таким образом, узнать, были ли изменения вообще.
```js
git fetch
/*
From github.com:YourName/RepositoryName
   2eefe71..ac391ab  develop    -> origin/develop
   fca7c62..c31477c  feature/1 -> origin/feature/1
 * [new branch]      feature/2 -> origin/feature/2
 * [new branch]      feature/3 -> origin/feature/3
*/
````
Выше можно видеть, что ветки `develop` и `feature/1` отличаются от своих одноимённых удалённых веток хэшем последних коммитов, а ветки `feature/2` и `feature/3` новые и их нет локально.

Команда `git fetch` помимо информации об изменениях скачивает и сами изменения, но их не слияние с локальной веткой не происходит. Это можно проверить командой `git diff`.
```js
git diff origin/develop
/*
diff --git a/Git.md b/Git.md
index abe21d5..e207862 100644
+++ b/client/Dockerfile
- Hello
+ Hello, Notes!
*/
```

Повторный вызов команды `git fetch` ничего не выведет, поскольку изменения уже были подгружены.

**Команда git pull** также, как и `git fetch` получает изменения и информацию о них, но дополнительно сливает изменения в локальную ветку.

По сути, `git pull` объединяет в себе две команды: `git fetch` и `git merge`.

## Полезные команды Git

### Команда rebase

### Команда reset

### Команда stash

### Отмена последнего коммита
```sh
git reset HEAD~
```

### Перенос коммитов из одной локальной ветки в другую

Например, перенос N коммитов из локальной ветки A и локальную ветку B.
```sh
git checkout B
git merge from A
git checkout A
git reset --hard HEAD~N # удаление N коммитов из ветки A
```

## Git flow

**Фича** (Feature) — новая функциональность. 

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
* Сгенерировать ключ для Github при помощи `ssh-keygen -t rsa -C "email" -f ~/.ssh/id_rsa_github`, где нужно заменить `email` на свой. `id_rsa_github` - название файла, где будет лежать приватный ключ. Команда также автоматически генерирует публичный ключ `id_rsa_github.pub` в той же папке.
* Ввести ключевую фразу и повторить её.

* Сгенерировать ключ для Gitlab при помощи `ssh-keygen -t rsa -C "email" -f ~/.ssh/id_rsa_gitlab`, где нужно заменить `email` на свой.
* Ввести ключевую фразу и повторить её.

### Cоздание ключа на Github
* Скопировать публичный ключ `~/.ssh/id_rsa_github.pub` для Github. Например, вывести его на экран при помощи `cat ~/.ssh/id_rsa_github.pub` и скопировать через `Ctrl + C`.
* Открыть на Github [Settings > SSH keys](https://github.com/settings/keys) и нажать на добавление нового ключа.
* Вставить скопированный ключ, придумать название для него и сохранить.

### Cоздание ключа на Gitlab
* Скопировать публичный ключ `~/.ssh/id_rsa_gitlab` для Gitlab.
* Открыть на Gitlab `Settings > SSH keys`.
* Вставить скопированный ключ, придумать название для него и сохранить.

### Добавление ключа в SSH-agent
* Добавить SSH-ключ для GitHub в SSH-agent `ssh-add ~/.ssh/id_rsa_github`. Если агент не запущен, то нужно сперва его запустить `eval $(ssh-agent -s)` или `ssh-agent bash`.
* Добавить SSH-ключ для GitLab в SSH-agent `ssh-add ~/.ssh/id_rsa_gitlab`.
* Проверить, что ключи добавлены через `ssh-add -L`.

Если для каждой новой консоли запускать агент и добавлять ключ приходиться заново, то можно настроить *псевдоним*.

**Псевдоним** (Alias) — аббревиатура, позволяющая избежать написания длинной последовательности команд.
* Создадим файл `.bashrc` в корневой папке (в Windows: `C:/Users/<USERNAME>/) и поместим там следующее.
```bash
alias sa="eval `ssh-agent -s` ssh-add ~/.ssh/id_rsa_gitlab"
```
Теперь каждый запуск команды `sa` в любой консоли Bash будет выполнять запуск агента и добавление ключа.

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

## Git Config

### Вывод конфига
```
git config -l
```

### Имя пользователя и почта
* Установить имя пользователя.
```js
/* имя пользователя */
git config --global user.name "Your Username"
/* электронная почта */
git config --global user.email "your@email"
```

## Git Bash
**Командная оболочка** (Shell) — терминальное приложение (terminal app), используемое для взаимодействия с ОС посредством письменных команд.

**Bash** (Bourne again shell, "Born again shell", "возрождённый" shell) — *усовершенствованная версия* ранней *командной оболочки Bourne shell*, исполняющей файлы формата `.sh` в `UNIX`. *Bash* является командной оболочкой *по умолчанию* для *Linux* и *macOS*.

**Git Bash** — пакет, *устанавливающий Bash*, некоторые *базовые утилиты* и *Git* на *Windows*.

### Убить запущенный процесс
Чтобы на *определённом порте* *убить запущенный процесс*, нужно узнать его **PID** (Process Identifier).
```css
netstat -ano | findstr :PORT /* например, :3000 */
/* Скопировать PID из последнего стобца результата и вставить в команду ниже */
tskill PID
```
