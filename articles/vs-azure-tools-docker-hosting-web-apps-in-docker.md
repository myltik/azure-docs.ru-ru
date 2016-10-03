<properties
   pageTitle="Развертывание контейнера ASP.NET Core в Linux на удаленном узле Docker | Microsoft Azure"
   description="Узнайте, как использовать средства Visual Studio для Docker для развертывания веб-приложения ASP.NET Core в контейнере Docker, работающем на виртуальной машине узла Docker под управлением Linux в Azure"   
   services="azure-container-service"
   documentationCenter=".net"
   authors="mlearned"
   manager="douge"
   editor=""/>

<tags
   ms.service="azure-container-service"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/08/2016"
   ms.author="mlearned"/>

# Развертывание контейнера ASP.NET на удаленном узле Docker

## Обзор
Docker — это облегченная платформа контейнеров, чем-то похожая на виртуальную машину, которую можно использовать для размещения приложений и служб. В этом руководстве пошагово описывается использование расширения [Средства Visual Studio 2015 для Docker](http://aka.ms/DockerToolsForVS) для развертывания приложения ASP.NET Core на узле Docker в Azure с помощью PowerShell.

## Предварительные требования
Для работы с этим учебником необходимо выполнить следующие действия:

- Создать виртуальную машину для узла Docker в Azure, как описано в статье [Использование машины Docker с Azure](./virtual-machines/virtual-machines-linux-docker-machine.md).
- Установить [Visual Studio 2015 с обновлением 3](https://go.microsoft.com/fwlink/?LinkId=691129).
- Установить [пакет SDK для Microsoft ASP.NET Core 1.0](https://go.microsoft.com/fwlink/?LinkID=809122).
- Установить [Средства Visual Studio 2015 для Docker — предварительная версия](http://aka.ms/DockerToolsForVS).

## 1\. Создание веб-приложения ASP.NET Core
Выполнив следующие шаги, вы создадите простое приложение ASP.NET Core, которое будет использоваться в этом руководстве.

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## 2) Добавление поддержки Docker

[AZURE.INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## 3\. Использование сценария PowerShell DockerTask.ps1 

1.  В командной строке PowerShell перейдите в корневой каталог проекта.

    ```
    PS C:\Src\WebApplication1>
    ```

1.  Убедитесь, что удаленный узел работает. Должно отображаться состояние Running.

    ```
    docker-machine ls
    NAME         ACTIVE   DRIVER   STATE     URL                        SWARM   DOCKER    ERRORS
    MyDockerHost -        azure    Running   tcp://xxx.xxx.xxx.xxx:2376         v1.10.3
    ```

    > [AZURE.NOTE] Если вы используете бета-версию Docker, здесь не будет отображаться ваш узел.

1.  Создайте приложение с использованием параметра -Build.

    ```
    PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release -Machine mydockerhost
    ```  

    > [AZURE.NOTE] При использовании бета-версии Docker не указывайте аргумент -Machine.
    > 
    > ```
    > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release 
    > ```  


1.  Запустите приложение с помощью параметра -Run.

    ```
    PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release -Machine mydockerhost
    ```

    > [AZURE.NOTE] При использовании бета-версии Docker не указывайте аргумент -Machine.
    > 
    > ```
    > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release 
    > ```

	По завершении работы Docker должны отобразиться результаты, аналогичные приведенным ниже.

    ![Просмотр приложения][3]

[0]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png

<!---HONumber=AcomDC_0921_2016-->