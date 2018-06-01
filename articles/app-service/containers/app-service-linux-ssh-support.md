---
title: Поддержка SSH для службы приложений Azure в Linux | Документация Майкрософт
description: Сведения об использовании SSH для службы приложений Azure на платформе Linux.
keywords: служба приложений azure, веб-приложение, linux, oss
services: app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: wesmc
ms.openlocfilehash: c2beb67a27b667d31402b903f38dbf116e9425d0
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301081"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Поддержка SSH для службы приложений Azure в Linux

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) широко используется для удаленного выполнения административных команд из терминала командной строки. Служба приложений на платформе Linux предоставляет поддержку SSH в контейнере приложения в каждом из встроенных образов Docker, которые используются для стека времени выполнения новых веб-приложений. 

![Стеки времени выполнения](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

В пользовательских образах Docker необходимо настроить сервер SSH.

Вы также можете подключиться к контейнеру непосредственно из локального компьютера, где ведется разработка, используя SSH и SFTP.

## <a name="open-ssh-session-in-browser"></a>Открытие сеанса SSH в браузере

Чтобы настроить клиентское подключение SSH к контейнеру, необходимо запустить приложение.

Вставьте следующий URL-адрес в браузер и замените \<app_name> именем вашего приложения:

```
https://<app_name>.scm.azurewebsites.net/webssh/host
```

Если вы еще не прошли аутентификацию, это потребуется сделать, воспользовавшись подпиской Azure, чтобы подключиться. После выполнения проверки подлинности отобразится оболочка в браузере. Здесь можно запускать команды внутри контейнера.

![SSL-подключение](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="use-ssh-support-with-custom-docker-images"></a>Применение поддержки SSH в пользовательских образах Docker

Чтобы пользовательский образ Docker поддерживал обмен данными по протоколу SSH между контейнером и клиентом на портале Azure, выполните следующие действия с образом Docker.

Эти действия показаны в репозитории службы приложений Azure в качестве [примера](https://github.com/Azure-App-Service/node/blob/master/6.9.3/).

1. Добавьте установку `openssh-server` в инструкцию [`RUN` ](https://docs.docker.com/engine/reference/builder/#run)в Dockerfile для образа и установите пароль для учетной записи привилегированного пользователя `"Docker!"`.

    > [!NOTE]
    > Эта конфигурация не разрешает внешние подключения к контейнеру. SSH-подключение доступно только для Kudu и сайта SCM, который аутентифицирован с помощью учетных данных для публикации.

    ```docker
    # ------------------------
    # SSH Server support
    # ------------------------
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

1. Добавьте [инструкцию `COPY`](https://docs.docker.com/engine/reference/builder/#copy) в Dockerfile, чтобы скопировать файл [sshd_config](http://man.openbsd.org/sshd_config) в каталог */etc/ssh/*. Ваш файл конфигурации должен быть создан на основе файла sshd_config из репозитория GitHub Azure-App-Service, который доступен [здесь](https://github.com/Azure-App-Service/node/blob/master/8.2.1/sshd_config).

    > [!NOTE]
    > Файл *sshd_config* должен содержать приведенные ниже компоненты, иначе установить подключение будет невозможно. 
    > * `Ciphers` должен содержать по крайней мере одно из следующих значений: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` должен содержать по крайней мере одно из следующих значений: `hmac-sha1,hmac-sha1-96`.

    ```docker
    COPY sshd_config /etc/ssh/
    ```

1. Добавьте порт 2222 в инструкцию [`EXPOSE` ](https://docs.docker.com/engine/reference/builder/#expose)для Dockerfile. Даже при указании пароля привилегированного пользователя порт 2222 недоступен из Интернета. Это внутренний порт, который доступен только контейнерам внутри мостовой сети виртуальной частной сети.

    ```docker
    EXPOSE 2222 80
    ```

1. Запустите службу SSH с помощью сценария оболочки (см. пример в файле [init_container.sh](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh)).

    ```bash
    #!/bin/bash
    service ssh start
    ```

Dockerfile использует инструкцию [`ENTRYPOINT` ](https://docs.docker.com/engine/reference/builder/#entrypoint)для выполнения этого сценария.

    ```docker
    COPY startup /opt/startup
    ...
    RUN chmod 755 /opt/startup/init_container.sh
    ...
    ENTRYPOINT ["/opt/startup/init_container.sh"]
    ```

## <a name="open-ssh-session-from-remote-shell"></a>Открытие сеанса SSH из удаленной оболочки

> [!NOTE]
> Эта функция сейчас доступна в режиме предварительной версии.
>

С помощью TCP-туннеля можно создать сетевое подключение между веб-приложением для контейнеров и компьютером, на котором выполняется разработка, с проверкой подлинности через WebSocket. Это позволяет открыть из необходимого клиента сеанс SSH с контейнером, запущенным в службе приложений.

Чтобы начать работу, нужно установить [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Чтобы ознакомиться с принципами работы без установки Azure CLI, перейдите к статье [Обзор Azure Cloud Shell](../../cloud-shell/overview.md). 

Добавьте последнее расширение службы приложений, запустив команду [az extension add](/cli/azure/extension?view=azure-cli-latest#az-extension-add):

```azurecli-interactive
az extension add -–name webapp
```

Если вы уже выполняли команду `az extension add`, запустите вместо нее [az extension update](/cli/azure/extension?view=azure-cli-latest#az-extension-update).

```azurecli-interactive
az extension update -–name webapp
```

Откройте удаленное подключение к приложению, используя команду [az webapp remote-connection create](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create). Замените _\<group\_name>_ и \_<app\_name> нужными именем группы и приложения, а \<port> — номером локального порта.

```azurecli-interactive
az webapp remote-connection create --resource-group <group_name> -n <app_name> -p <port> &
```

> [!TIP]
> Знак `&` в конце команды предназначен исключительно для удобства при использовании Cloud Shell. Он позволяет запустить процесс в фоновом режиме и выполнять следующую команду в той же оболочке.

В выходных данных команды содержатся сведения, необходимые для того, чтобы открыть сеанс SSH.

```
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Откройте сеанс SSH с контейнером с помощью необходимого клиента, используя локальный порт. В следующем примере используется команда по умолчанию [ssh](https://ss64.com/bash/ssh.html).

```azurecli-interactive
ssh root@127.0.0.1 -p <port>
```

Если отобразится запрос, введите `yes`, чтобы продолжить установку подключения. Затем появится запрос на ввод пароля. Используйте показанный ранее `Docker!`.

```
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

После выполнения проверки подлинности появится приветственный экран сеанса.

```
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  >
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
```

Вы подключены к соединителю. 

Попробуйте выполнить команду [top](https://ss64.com/bash/top.html). В списке процессов должен содержаться процесс приложения. В приведенном ниже примере выходных данных — это процесс с `PID 263`.

```
Mem: 1578756K used, 127032K free, 8744K shrd, 201592K buff, 341348K cached
CPU:   3% usr   3% sys   0% nic  92% idle   0% io   0% irq   0% sirq
Load average: 0.07 0.04 0.08 4/765 45738
  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    1     0 root     S     1528   0%   0   0% /sbin/init
  235     1 root     S     632m  38%   0   0% PM2 v2.10.3: God Daemon (/root/.pm2)
  263   235 root     S     630m  38%   0   0% node /home/site/wwwroot/app.js
  482   291 root     S     7368   0%   0   0% sshd: root@pts/0
45513   291 root     S     7356   0%   0   0% sshd: root@pts/1
  291     1 root     S     7324   0%   0   0% /usr/sbin/sshd
  490   482 root     S     1540   0%   0   0% -ash
45539 45513 root     S     1540   0%   0   0% -ash
45678 45539 root     R     1536   0%   0   0% top
45733     1 root     Z        0   0%   0   0% [init]
45734     1 root     Z        0   0%   0   0% [init]
45735     1 root     Z        0   0%   0   0% [init]
45736     1 root     Z        0   0%   0   0% [init]
45737     1 root     Z        0   0%   0   0% [init]
45738     1 root     Z        0   0%   0   0% [init]
```

## <a name="next-steps"></a>Дополнительная информация

Если у вас возникли вопросы, опубликуйте их на [форуме Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

Дополнительные сведения о платформе "Веб-приложения для контейнеров" см. в статьях:

* [Общие сведения об удаленной отладке приложений Node.js в службе приложений Azure из VS Code](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Использование пользовательского образа Docker для платформы "Веб-приложения для контейнеров"](quickstart-docker-go.md).
* [Использование .NET Core в службе приложений Azure на платформе Linux](quickstart-dotnetcore.md).
* [Использование Ruby в службе приложений Azure на платформе Linux](quickstart-ruby.md).
* [Вопросы и ответы о платформе "Веб-приложения для контейнеров" в службе приложений Azure](app-service-linux-faq.md)