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


# Shebang `!#`

**Шебанг** (англ. `shebang`, `sha-bang`, `sharp-exclamation`, `hashbang`, `pound-bang`, `hash-pling`) - последовательность из двух символов `#!` в начале скрипта. 

Если Unix выполняет скрипт с шебангом в начале скрипта, загрузчик программ рассматривает остаток строки после шебанга как имя файла программы-интерпретатора. 

Например, чтобы выполнить файл с помощью `sh` (`Bourne shell`), необходимо написать в начале скрипта.
```sh
#!/bin/sh
...
```

Поскольку `#` является символов начала комментария во многих скриптовых языках программирования, строка с шебангом обычно пропускается интерпретатором.

JavaScript не является таким языком, поскольку комментарии в нём объявляются через `/**/`, `//`. Однако в ES14 (ECMAScript2023) добавили поддержку шебанга.
```
#!/usr/bin/env node
console.log('Hello JavaScript!');
```
Скриншот из Chrome:
![image](https://github.com/Max-Starling/Notes/assets/22237384/c15eeac6-e9d6-495d-b9ad-6a54be56dc22)

