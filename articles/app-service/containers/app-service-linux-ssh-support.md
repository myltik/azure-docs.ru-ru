---
title: "Поддержка SSH для веб-приложения службы приложений Azure для контейнеров | Документация Майкрософт"
description: "Сведения об использовании SSH для веб-приложения Azure для контейнеров."
keywords: "служба приложений azure, веб-приложение, linux, oss"
services: app-service
documentationcenter: 
author: wesmc7777
manager: erikre
editor: 
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: wesmc
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 7ce9b23e8925d4c79827c7c4e8bec63067ce33e0
ms.contentlocale: ru-ru
ms.lasthandoff: 09/20/2017

---
# <a name="ssh-support-for-azure-web-app-for-containers"></a>Поддержка SSH для веб-приложения Azure для контейнеров

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) —это сетевой протокол шифрования, предназначенный для безопасного использования сетевых служб. Чаще всего он используется для безопасного удаленного входа в систему из командной строки и удаленного выполнения административных команд.

Веб-приложение для контейнеров предоставляет поддержку SSH в контейнере приложения в каждом из встроенных образов Docker, используемых для стека времени выполнения новых веб-приложений. 

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

[Здесь](https://github.com/Azure-App-Service/node/blob/master/6.9.3/) в качестве примера эти действия показаны в репозитории службы приложений Azure.

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

1. Добавьте [инструкцию `COPY`](https://docs.docker.com/engine/reference/builder/#copy) в Dockerfile, чтобы скопировать файл [sshd_config](http://man.openbsd.org/sshd_config) в каталог */etc/ssh/*. Ваш файл конфигурации должен быть создан на основе нашего файла sshd_config из репозитория GitHub Azure-App-Service, который доступен [здесь](https://github.com/Azure-App-Service/node/blob/master/6.11.0/sshd_config).

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

1. Обязательно запустите службу ssh. [Этот](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh) пример использует сценарий оболочки в каталоге */bin*.

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

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о веб-приложениях Azure для контейнеров см. в следующих ресурсах. Если у вас возникли вопросы, опубликуйте их на [нашем форуме](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Использование пользовательского образа Docker Hub для веб-приложений Azure для контейнеров](quickstart-custom-docker-image.md)
* [Создание веб-приложения .NET Core в контейнере Linux в Azure](quickstart-dotnetcore.md)
* [Создание приложения Ruby с помощью веб-приложений на платформе Linux](quickstart-ruby.md)
* [Вопросы и ответы о платформе "Веб-приложения для контейнеров" в службе приложений Azure](app-service-linux-faq.md)
