---
title: "Настройка узла Docker с помощью VirtualBox | Документация Майкрософт"
description: "Пошаговые инструкции по настройке экземпляра Docker по умолчанию с помощью машины Docker и VirtualBox."
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 0b1335a2-7720-42a8-8260-4e06fc00c9f6
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: e9465afb560a73d74f853c19094b3ee75b8c470c
ms.contentlocale: ru-ru
ms.lasthandoff: 02/16/2017

---
# <a name="configure-a-docker-host-with-virtualbox"></a>Настройка узла Docker с помощью VirtualBox
## <a name="overview"></a>Обзор
В этой статье приводятся инструкции по настройке экземпляра Docker по умолчанию с помощью машины Docker и VirtualBox. При использовании [бета-версии Docker для Windows](http://beta.docker.com/)эта настройка не требуется.

## <a name="prerequisites"></a>Предварительные требования
Должны быть установлены следующие средства.

* [Docker Toolbox](https://www.docker.com/products/overview#/docker_toolbox)

## <a name="configuring-the-docker-client-with-windows-powershell"></a>Настройка клиента Docker с помощью Windows PowerShell
Чтобы настроить клиент Docker, просто откройте Windows PowerShell и выполните указанные ниже действия:

1. Создайте экземпляр узла Docker по умолчанию.
   
    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
2. Убедитесь в том, что экземпляр по умолчанию настроен и выполняется. (Должен быть запущен экземпляр с именем default.
   
    ```PowerShell
    docker-machine ls 
    ```
   
    ![Вывод: docker-machine ls][0]
3. Задайте экземпляр по умолчанию в качестве текущего узла и настройте оболочку.
   
    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```
4. Отобразите активные контейнеры Docker. Список должен быть пустым.
   
    ```PowerShell
    docker ps
    ```
   
    ![Вывод: docker ps][1]

> [!NOTE]
> Каждый раз, когда перезагружается компьютер разработки, необходимо перезапускать локальный узел Docker.
> Для этого выполните в командной строке следующую команду: `docker-machine start default`.
> 
> 

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png

