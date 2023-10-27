# Linux

## Команды
* `echo <данные>` - вывод данных в консоль.
* `echo $USER`, `whoami` - вывод имени текущего пользователя
* `mkdir directory_name` - создание папки.
* `ls` - список файлов и папок в текущей директории.
* `rm file_name` - удаление файла.
* `rm -rf directory_name` - удаление папки.
* `cat file_name` - вывод содержимого файла в консоль.
* `cd directory_name` - переход в папку.
* `cd ..` - переход в папку выше.
* `printenv` - вывести все переменные окружения (англ. `environment variables`)

```shell
# /app/src
cd ..
# /app
```
* `ssh <username>@<password>` - вход на сервер по ssh при помощи имени и пароля (интерактивный режим).
* `which <название команды>` - вывод месторасположения команды.
```shell
which docker-compose
# выведет /usr/bin/docker-compose
```

### sudo и переменные окружения

По умолчанию `sudo` отказывается принимать переменные окружения.
```
sudo: sorry, you are not allowed to set the following environment variables: POSTGRES_USER, POSTGRES_PASSWORD
```
Чтобы это исправить, необходимо установить доступ к этим переменным в `sudoers`.
```js
sudo nano /etc/sudoers.d/ld_preload
/* или */
sudo vim /etc/sudoers.d/ld_preload
```
Нужно дописать переменные в файл.
```js
/* /etc/sudoers.d/ld_preload */
Defaults env_keep += "POSTGRES_USER"
Defaults env_keep += "POSTGRES_PASSWORD"
```

# Windows

## Команды
* `dir` - список файлов и папок в текущей директории (аналог `ls` в Linux).
* `type` - вывод содержимого файла в консоль (аналог `cat` в Linux).
* `cls` - очистить консоль (аналог `clear` в Linux)
* `<disk_name>:` - переход на диск disk_name.
```shell
# переход на диск C
C:
# переход на диск D
D:
```

## PowerShell
