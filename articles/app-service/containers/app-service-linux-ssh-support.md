---
title: "Поддержка SSH для службы приложений Azure в Linux | Документация Майкрософт"
description: "Сведения об использовании SSH для службы приложений Azure на платформе Linux."
keywords: "служба приложений azure, веб-приложение, linux, oss"
services: app-service
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: wesmc
ms.openlocfilehash: 7e6bb974565810ebb8d8e21d1c274d42d6d39e55
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2017
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Поддержка SSH для службы приложений Azure в Linux

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) —это сетевой протокол шифрования, предназначенный для безопасного использования сетевых служб. Чаще всего он используется для безопасного удаленного входа в систему из командной строки и удаленного выполнения административных команд.

Служба приложений на платформе Linux предоставляет поддержку SSH в контейнере приложения в каждом из встроенных образов Docker, которые используются для стека времени выполнения новых веб-приложений.

![Стеки времени выполнения](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Протокол SSH можно также использовать с помощью пользовательских образов Docker, добавив в них сервер SSH и настроив его, как описано в этом разделе.

## <a name="making-a-client-connection"></a>Создание подключения клиента

Для создания SSH-подключения клиента должен быть запущен основной сайт.

Вставьте конечную точку управления системой управления версиями (SCM) для веб-приложения в браузер, используя приведенный ниже формат.

```bash
https://<your sitename>.scm.azurewebsites.net/webssh/host
```

Если вы еще не прошли аутентификацию, это потребуется сделать, воспользовавшись подпиской Azure, чтобы подключиться.

![SSL-подключение](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="ssh-support-with-custom-docker-images"></a>Поддержка SSH в пользовательских образах Docker

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

1. Добавьте [инструкцию `COPY`](https://docs.docker.com/engine/reference/builder/#copy) в Dockerfile, чтобы скопировать файл [sshd_config](http://man.openbsd.org/sshd_config) в каталог */etc/ssh/*. Ваш файл конфигурации должен быть создан на основе нашего файла sshd_config из репозитория GitHub Azure-App-Service, который доступен [здесь](https://github.com/Azure-App-Service/node/blob/master/8.2.1/sshd_config).

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

1. Убедитесь, что [служба SSH запущена](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh) с помощью сценария оболочки в каталоге */bin*.

    ```bash
    #!/bin/bash
    service ssh start
    ```

Dockerfile использует инструкцию [`CMD` ](https://docs.docker.com/engine/reference/builder/#cmd)для выполнения этого сценария.

    ```docker
    COPY init_container.sh /bin/
    ...
    RUN chmod 755 /bin/init_container.sh
    ...
    CMD ["/bin/init_container.sh"]
    ```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о веб-приложениях Azure для контейнеров см. в следующих ресурсах. Если у вас возникли вопросы, опубликуйте их на [нашем форуме](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Использование пользовательского образа Docker для платформы "Веб-приложения для контейнеров"](quickstart-custom-docker-image.md).
* [Использование .NET Core в службе приложений Azure на платформе Linux](quickstart-dotnetcore.md).
* [Использование Ruby в службе приложений Azure на платформе Linux](quickstart-ruby.md).
* [Вопросы и ответы о платформе "Веб-приложения для контейнеров" в службе приложений Azure](app-service-linux-faq.md)