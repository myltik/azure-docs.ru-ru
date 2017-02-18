---
title: "Использование образов Docker в реестре контейнеров Azure | Документация Майкрософт"
description: "Отправка образов Docker в реестр контейнеров Azure и их получение с помощью интерфейса командной строки Docker"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 64fbe43f-fdde-4c17-a39a-d04f2d6d90a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
translationtype: Human Translation
ms.sourcegitcommit: f299cff22d00a1c765a32838647818d18f3df85d
ms.openlocfilehash: df15eebf0052aa4713263a810df605fa1016c306

---
# <a name="push-your-first-image-to-a-container-registry-using-the-docker-cli"></a>Отправка образов в реестр контейнеров с помощью интерфейса командной строки Docker
В реестре контейнеров Azure хранятся частные образы контейнеров [Docker](http://hub.docker.com), а также осуществляется управление ими (подобно тому, как в [Docker Hub](https://hub.docker.com/) хранятся общедоступные образы Docker). [Интерфейс командной строки Docker](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) позволяет [входить](https://docs.docker.com/engine/reference/commandline/login/) в реестр контейнеров, а также выполнять [отправку](https://docs.docker.com/engine/reference/commandline/push/), [получение](https://docs.docker.com/engine/reference/commandline/pull/) и другие операции. 

Общие сведения и основные понятия см. в статье о [реестре контейнеров Azure](container-registry-intro.md).


> [!NOTE]
> В настоящее время доступна предварительная версия реестра контейнеров.
> 
> 

## <a name="prerequisites"></a>Предварительные требования
* **Реестр контейнеров Azure.** Создайте реестр контейнеров в своей подписке Azure. Это можно сделать на [портале Azure](container-registry-get-started-portal.md) или с помощью [предварительной версии Azure CLI 2.0](container-registry-get-started-azure-cli.md).
* **Интерфейс командной строки Docker.** Установите [подсистему Docker](https://docs.docker.com/engine/installation/), чтобы настроить локальный компьютер в качестве узла Docker и получить доступ к командам интерфейса командной строки Docker.

## <a name="log-in-to-a-registry"></a>Вход в раздел реестра
Выполните команду `docker login`, чтобы войти в реестр контейнеров с помощью [учетных данных реестра](container-registry-authentication.md).

Следующая команда передает идентификатор и пароль [субъекта-службы](../active-directory/active-directory-application-objects.md) Azure Active Directory. Например, назначение субъекта-службы для реестра позволяет автоматизировать некоторые сценарии. 

```
docker login myregistry-contoso.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

> [!TIP]
> Укажите полное доменное имя реестра (все знаки в нижнем регистре). В нашем примере поисковый запрос будет выглядеть так: `myregistry-contoso.azurecr.io`.

## <a name="steps-to-pull-and-push-an-image"></a>Отправка и получение образов
Следующий пример позволяет скачать образ Nginx из общедоступного реестра Docker Hub, поместить его в частный реестр контейнеров Azure, отправить его в свой реестр, а затем снова извлечь.

**1. Получите официальный образ Docker для Nginx.**

Сначала общедоступный образ Nginx нужно отправить на локальный компьютер.

```
docker pull nginx
```
**2. Запустите контейнер Nginx.**

Следующая команда запускает локальный контейнер Nginx в интерактивном режиме (благодаря чему отображаются выходные данные из Nginx) и прослушивает порт 8080. После остановки запущенный контейнер удаляется.

```
docker run -it --rm -p 8080:80 nginx
```

Перейдите по адресу [http://localhost:8080](http://localhost:8080), чтобы просмотреть запущенный контейнер. Появится экран, аналогичный показанному ниже.

![Nginx на локальном компьютере](./media/container-registry-get-started-docker-cli/nginx.png)

Чтобы остановить запущенный контейнер, нажмите клавиши [CTRL]+[C].

**3. Создайте псевдоним образа в реестре.**

Следующая команда создает псевдоним образа с полным путем к вашему реестру. Чтобы избежать беспорядка в корне реестра, эта команда указывает пространство имен `samples`.

```
docker tag nginx myregistry-exp.azurecr.io/samples/nginx
```  

**4. Отправьте образ в реестр.**

```
docker push myregistry-contoso.azurecr.io/samples/nginx
``` 

**5. Получите образ из реестра.**

```
docker pull myregistry-contoso.azurecr.io/samples/nginx
``` 

**6. Запустите контейнер Nginx из реестра.**

```
docker run -it --rm -p 8080:80 myregistry-exp.azurecr.io/samples/nginx
```

Перейдите по адресу [http://localhost:8080](http://localhost:8080), чтобы просмотреть запущенный контейнер.

Чтобы остановить запущенный контейнер, нажмите клавиши [CTRL]+[C].

**6. Извлеките ключ.**

```
docker rmi myregistry-contoso.azurecr.io/samples/nginx
```



## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы знаете основы, можно приступать к использованию реестра. Например, разверните образы контейнера в кластер [службы контейнеров Azure](https://azure.microsoft.com/documentation/services/container-service/).






<!--HONumber=Jan17_HO4-->


