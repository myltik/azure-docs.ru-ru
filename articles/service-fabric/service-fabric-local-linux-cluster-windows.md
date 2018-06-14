---
title: Установка кластера Azure Service Fabric на платформе Linux в Windows | Документация Майкрософт
description: В этой статье описывается установка кластеров Service Fabric на платформе Linux на компьютерах разработки Windows. Это особенно удобно для кроссплатформенной разработки.
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: ad0d383888c173ece5a7fbd3b0de690ed13074f7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206299"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Установка кластера Service Fabric на платформе Linux на компьютере разработчики Windows

В этом документе описывается настройка установка локального кластера Service Fabric на платформе Linux на компьютерах разработки Windows. Установка локального кластера Linux удобна для быстрого тестирования приложений, нацеленных на кластеры Linux, но разработанных на компьютере Windows.

## <a name="prerequisites"></a>предварительным требованиям
Изначально кластеры Service Fabric на платформе Linux не поддерживают запуск в среде Windows. Чтобы запустить локальный кластер Service Fabric, предоставляется предварительно настроенный образ контейнера Docker. Перед началом работы вам потребуются:

* не менее 4 ГБ ОЗУ;
* последняя версия [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows);

>[!TIP]
> * Чтобы установить Docker в среде Windows, можно выполнить инструкции, приведенные в официальной [документации](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) Docker. 
> * По завершении проверьте правильность установки, выполнив инструкции, приведенные [здесь](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine).


## <a name="create-a-local-container-and-setup-service-fabric"></a>Создание локального контейнера и настройка Service Fabric
Чтобы настроить локальный контейнер Docker и запустить его в кластере Service Fabric, выполните следующие действия в PowerShell:

1. Извлеките образ из репозитория Docker Hub:

    ```powershell
    docker pull microsoft/service-fabric-onebox
    ```

2. Обновите конфигурацию управляющей программы Docker на узле следующим образом и перезапустите управляющую программу Docker: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    Еще один способ обновления (предпочтительный): щелкните значок Docker > "Параметры" > "Управляющая программа" > "Дополнительно". Затем перезапустите управляющую программу Docker, чтобы изменения вступили в силу. 

3. Запустите экземпляр универсального контейнера Service Fabric, используя образ:

    ```powershell
    docker run -itd -p 19080:19080 --name sfonebox microsoft/service-fabric-onebox
    ```
    >[!TIP]
    > * Вы можете указать имя для экземпляра контейнера, которое будет более удобным для чтения. 
    > * Если приложение прослушивает определенные порты, их необходимо указать с помощью дополнительных тегов -p. Например, если приложение ожидает передачи данных через порт 8080, выполните docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox.

4. Войдите в контейнер Docker в интерактивном режиме SSH:

    ```powershell
    docker exec -it sfonebox bash
    ```

5. Запустите скрипт установки, который позволит получить необходимые зависимости, а после этого запустите контейнер в кластере.

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. Выполнив шаг 5, вы можете перейти по адресу ``http://localhost:19080`` на компьютере Windows и просмотреть Service Fabric Explorer. На этом этапе можно подключиться к кластеру с помощью любого инструмента на компьютере разработки Windows и развернуть приложение, нацеленное на кластеры Service Fabric на платформе Linux. 

    > [!NOTE]
    > Подключаемый модуль Eclipse сейчас не поддерживается в Windows. 

## <a name="next-steps"></a>Дополнительная информация
* Приступите к работе с [Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse).
* Просмотрите другие [примеры Java](https://github.com/Azure-Samples/service-fabric-java-getting-started).


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png