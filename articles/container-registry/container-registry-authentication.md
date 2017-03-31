---
title: "Проверка подлинности в реестре контейнеров Azure | Документация Майкрософт"
description: "Сведения о входе в реестр контейнеров Azure с помощью субъекта-службы Azure Active Directory или учетной записи администратора"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 128a937a-766a-415b-b9fc-35a6c2f27417
ms.service: container-registry
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 774c844375df89864274e3376ed07dd565c8ecff
ms.lasthandoff: 03/27/2017

---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Аутентификация с помощью частного реестра контейнеров Docker
Чтобы приступить к работе с образами контейнеров в реестре контейнеров Azure, нужно войти в систему, выполнив команду `docker login`. Вы можете войти, используя **[субъект-службу Azure Active Directory](../active-directory/active-directory-application-objects.md)** или специальную **учетную запись администратора**. В этой статье содержатся подробные сведения об этих удостоверениях.



## <a name="service-principal"></a>Субъект-служба

Вы можете [назначить своему реестру субъект-службу](container-registry-get-started-azure-cli.md#assign-a-service-principal) и использовать ее для базовой проверки подлинности Docker. Мы рекомендуем использовать субъект-службу в большинстве сценариев. Укажите идентификатор и пароль приложения для субъекта-службы в команде `docker login`, как показано в следующем примере.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

После входа в систему Docker кэширует учетные данные, поэтому вам не нужно запоминать идентификатор приложения.

> [!TIP]
> При необходимости пароль субъекта-службы можно создать повторно, выполнив команду `az ad sp reset-credentials`.
>


Субъекты-службы поддерживают [доступ на основе ролей](../active-directory/role-based-access-control-configure.md) к реестру. Доступны следующие роли: читатель (доступ только на получение), участник (получение и отправка), владелец (получение, отправка и назначение ролей другим пользователям). Для реестра можно назначить несколько субъектов-служб. Это позволяет определять доступ для разных пользователей и приложений. Субъекты-службы также позволяют выполнять автономное подключение к реестру в сценариях DevOps и разработки, указанных ниже.

  * Развертывания контейнеров из реестра в системы управления, в том числе DC/OS, Docker Swarm и Kubernetes. Реестры контейнеров также можно отправлять в связанные службы Azure, например в [службу контейнеров](../container-service/index.md), [службу приложений](../app-service/index.md), [пакетную службу](../batch/index.md) и [Service Fabric](../service-fabric/index.md).

  * Решения непрерывной интеграции и развертывания (например, Visual Studio Team Services или Jenkins), создающие образы контейнеров и отправляющие их в реестр.





## <a name="admin-account"></a>Учетная запись администратора
При создании реестра вместе с ним автоматически создается учетная запись администратора. По умолчанию эта учетная запись отключена, но ее можно включить, а также можно настроить учетные данные, например на [портале](container-registry-get-started-portal.md#manage-registry-settings) или с помощью [команд Azure CLI 2.0](container-registry-get-started-azure-cli.md#manage-admin-credentials). Если учетная запись включена, укажите в команде `docker login` имя пользователя и пароль для базовой проверки подлинности в реестре. Например:

```
docker login myregistry.azurecr.io -u myAdminName -p myPassword
```

> [!IMPORTANT]
> Учетная запись администратора предоставляет доступ к реестру одному пользователю. Она предназначена, главным образом, для тестирования. Мы не рекомендуем обмениваться учетными данными учетной записи администратора с другими пользователями. В реестре все пользователи отображаются как один пользователь. Изменив или отключив эту учетную запись, вы ограничите доступ к реестру для пользователей, использующих эти учетные данные.
>


### <a name="next-steps"></a>Дальнейшие действия
* [Отправьте свой первый образ с помощью интерфейса командной строки Docker.](container-registry-get-started-docker-cli.md)
* Дополнительные сведения о проверке подлинности в предварительной версии реестра контейнеров см. в [этой записи блога](https://blogs.msdn.microsoft.com/stevelasker/2016/11/17/azure-container-registry-user-accounts/).

