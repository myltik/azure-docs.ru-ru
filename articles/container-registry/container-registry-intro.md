---
title: "Использование частных реестров контейнеров Docker в Azure | Документация Майкрософт"
description: "Общие сведения о службе реестра контейнеров Azure, предоставляющей облачные, управляемые и частные реестры Docker."
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: ee2b652b-fb7c-455b-8275-b8d4d08ffeb3
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 25301f1bbacdf2f1e3d04ed3470eafd31098ea32
ms.lasthandoff: 04/17/2017

---
# <a name="introduction-to-private-docker-container-registries"></a>Общие сведения о частных реестрах контейнеров Docker


Реестр контейнеров Azure — это управляемая служба [реестра Docker](https://docs.docker.com/registry/), основанная на реестре Docker 2.0 с открытым кодом. Создавайте и настраивайте реестры контейнеров Azure, чтобы хранить частные образы [контейнеров Docker](https://www.docker.com/what-docker) и управлять ими. Используйте реестры контейнеров Azure с имеющимися конвейерами разработки и развертывания контейнеров, изучив сведения, предоставленные сообществом Docker.

Общие сведения о Docker и контейнерах см. в следующих источниках:

* [Руководство пользователя Docker](https://docs.docker.com/engine/userguide/)




## <a name="use-cases"></a>Варианты использования
Извлекайте образы из реестра контейнеров Azure и отправляйте их в разные места назначения развертывания:

* **Масштабируемые системы управления**, управляющие контейнерными приложениями в кластерах узлов, в том числе [DC/OS](https://docs.mesosphere.com/), [Docker Swarm](https://docs.docker.com/swarm/) и [Kubernetes](http://kubernetes.io/docs/).
* **Службы Azure**, поддерживающие создание и выполнение масштабированных приложений, в том числе [служба контейнеров](../container-service/index.md), [служба приложений](/app-service/index.md), [пакетная служба](../batch/index.md), [Service Fabric](../service-fabric/index.md) и т. д.

Разработчики также могут отправлять образы в реестр контейнеров в рамках рабочего процесса разработки контейнера. Например, они могут получить доступ к реестру контейнеров из средства развертывания и обеспечения непрерывной интеграции, например [Visual Studio Team Services](https://www.visualstudio.com/docs/overview) или [Jenkins](https://jenkins.io/).





## <a name="key-concepts"></a>Основные понятия
* **Реестр.** Создайте один или несколько реестров контейнеров в своей подписке Azure. Для создания реестра используется стандартная [учетная запись хранения](../storage/storage-introduction.md) Azure в том же расположении. Создание реестра в том же расположении Azure, где находятся развертывания, позволяет воспользоваться преимуществами локального хранилища образов контейнеров с доступом по сети. Полное имя реестра имеет формат `myregistry.azurecr.io`.

  [Контроль доступа](container-registry-authentication.md) к реестру контейнеров осуществляется с помощью [субъекта-службы](../active-directory/active-directory-application-objects.md) на основе Azure Active Directory или предоставленной учетной записи администратора. Чтобы пройти проверку подлинности с помощью реестра, используется стандартная команда `docker login`.

* **Репозиторий.** Каждый реестр содержит по крайней мере один репозиторий, представляющий собой группу образов контейнеров. Реестр контейнеров Azure поддерживает многоуровневые пространства имен для репозитория. Благодаря этому можно группировать коллекции образов, связанные с определенным приложением, или коллекции приложений, связанные с определенным развертыванием или рабочими группами. Например:

  * `myregistry.azurecr.io/aspnetcore:1.0.1` представляет образ, используемый в организации;
  * `myregistry.azurecr.io/warrantydept/dotnet-build` представляет образ, используемый для создания приложений .NET, доступ к которым предоставляется отделу гарантий;
  * `myregistry.azrecr.io/warrantydept/customersubmissions/web` представляет веб-образ, сгруппированный в приложении customer submissions, которое принадлежит отделу гарантий.

* **Образ.** Это моментальный снимок контейнера Docker с доступом только для чтения, который хранится в репозитории. Реестры контейнеров Azure могут содержать образы Windows и Linux. Имена образов нужно указывать для всех развертываний контейнеров. Используйте стандартные [команды Docker](https://docs.docker.com/engine/reference/commandline/), чтобы отправлять образы в репозиторий и извлекать их из него.

* **Контейнер.** Контейнер определяет программное приложение и его зависимости, содержащиеся в полной файловой системе, которая включает в себя код, среду выполнения, служебные программы и библиотеки. Запустите контейнеры Docker на основе образов Windows или Linux, извлеченных из реестра контейнеров. Контейнеры, запущенные на одном компьютере, используют то же самое ядро операционной системы. Контейнеры Docker можно полностью перенести во все основные дистрибутивы Linux, Mac и Windows.




## <a name="next-steps"></a>Дальнейшие действия
* [Создание реестра контейнеров на портале Azure](container-registry-get-started-portal.md)
* [Создание реестра контейнеров с помощью Azure CLI](container-registry-get-started-azure-cli.md)
* [Отправка первого образа с помощью интерфейса командной строки Docker](container-registry-get-started-docker-cli.md)
* Сведения о том, как создать рабочий процесс непрерывной интеграции и развертывания с помощью Visual Studio Team Services, службы контейнеров Azure и реестра контейнеров Azure см. в [этом руководстве](../container-service/container-service-setup-ci-cd.md).
* Сведения о том, как настроить частный реестр Docker в Azure (без общедоступной конечной точки) см. в статье [Развертывание частного реестра Docker в Azure](../virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md).

