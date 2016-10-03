<properties
   pageTitle="Настройка узла Docker с помощью VirtualBox | Microsoft Azure"
   description="Пошаговые инструкции по настройке экземпляра Docker по умолчанию с помощью машины Docker и VirtualBox."
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="mlearned" />

# Настройка узла Docker с помощью VirtualBox

## Обзор
В этой статье приводятся инструкции по настройке экземпляра Docker по умолчанию с помощью машины Docker и VirtualBox. При использовании [бета-версии Docker для Windows](http://beta.docker.com/) эта настройка не требуется.

## Предварительные требования
Должны быть установлены следующие средства.

- [Docker Toolbox](https://www.docker.com/products/overview#/docker_toolbox)

## Настройка клиента Docker с помощью Windows PowerShell

Чтобы настроить клиент Docker, просто откройте Windows PowerShell и выполните указанные ниже действия:

1. Создайте экземпляр узла Docker по умолчанию.

    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
 
1. Убедитесь в том, что экземпляр по умолчанию настроен и выполняется. (Должен быть запущен экземпляр с именем default.

    ```PowerShell
	docker-machine ls 
    ```
		
	![Вывод: docker-machine ls][0]
 
1. Задайте экземпляр по умолчанию в качестве текущего узла и настройте оболочку.

    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```

1. Отобразите активные контейнеры Docker. Список должен быть пустым.

    ```PowerShell
	docker ps
    ```

	![Вывод: docker ps][1]
 
> [AZURE.NOTE] Каждый раз, когда перезагружается компьютер разработки, необходимо перезапускать локальный узел Docker. Для этого выполните в командной строке следующую команду: `docker-machine start default`.

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png

<!---HONumber=AcomDC_0921_2016-->