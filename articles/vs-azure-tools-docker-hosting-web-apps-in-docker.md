<properties
   pageTitle="Развертывание контейнера ASP.NET на удаленном узле Docker | Microsoft Azure"
   description="Узнайте, как использовать средства Visual Studio для Docker для публикации веб-приложения ASP.NET 5 в контейнере Docker, работающем на виртуальной машине узла Docker в Azure"   
   services="visual-studio-online"
   documentationCenter=".net"
   authors="allclark"
   manager="douge"
   editor=""/>

<tags
   ms.service="visual-studio-online"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/08/2016"
   ms.author="allclark"/>

# Развертывание контейнера ASP.NET на удаленном узле Docker

## Обзор
Docker — это облегченная платформа контейнеров, чем-то похожая на виртуальную машину, которую можно использовать для размещения приложений и служб. Visual Studio поддерживает Docker в Ubuntu, CoreOS и Windows. В данном учебнике пошагово описывается использование расширения [Средства Visual Studio 2015 для Docker](http://aka.ms/DockerToolsForVS) для публикации приложения ASP.NET 5 на узле Docker в Azure.

## 1\. Предварительные требования
Для работы с этим учебником необходимо выполнить следующие действия:

- Создать виртуальную машину для узла Docker в Azure, как описано в статье [Использование машины Docker с Azure](./virtual-machines/virtual-machines-linux-classic-docker-machine.md).
- Установить [Visual Studio 2015](https://www.visualstudio.com/ru-RU/downloads/download-visual-studio-vs.aspx).
- Установить [Средства Visual Studio 2015 для Docker — предварительная версия](http://aka.ms/DockerToolsForVS).

## 2\. Создание веб-приложения ASP.NET 5
Выполнив следующие шаги, вы создадите простое приложение ASP.NET 5, которое будет использоваться в этом учебнике.

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## 3\. Добавление поддержки Docker

[AZURE.INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## 4\. Указывание на удаленный узел Docker

1.  В **обозревателе решений** Visual Studio найдите папку **Свойства** и разверните ее.
1.  Откройте файл *Docker.props*.

    ![Открытие файла Docker.props][0]

1.  Измените значение **DockerMachineName** на имя вашего удаленного узла Docker. Если вы не знаете имя удаленного узла Docker, выполните ```docker-machine ls``` в командной строке Windows PowerShell. Используйте значение, указанное в столбце **Имя** для нужного узла.

    ![Изменение имени машины Docker][1]

1.  Перезапустите Visual Studio.

## 5\. Настройка конечной точки узла Docker в Azure
Перед развертыванием приложения из Visual Studio в Azure добавьте конечную точку 80 на виртуальную машину узла Docker, чтобы впоследствии можно было просматривать приложение в браузере. Это можно сделать на [классическом портале Azure](http://go.microsoft.com/fwlink/?LinkID=213885) или с помощью Windows PowerShell.

- **Настройка конечной точки узла Docker в Azure с помощью [классического портала Azure](http://go.microsoft.com/fwlink/?LinkID=213885)**

    1.  Перейдите на [классический портал Azure](http://go.microsoft.com/fwlink/?LinkID=213885). 
    
    1.  Выберите **ВИРТУАЛЬНЫЕ МАШИНЫ**.
    
    1.  Выберите свою виртуальную машину узла Docker.
    
    1.  Перейдите на вкладку **КОНЕЧНЫЕ ТОЧКИ**.
    
    1.  Нажмите кнопку **ДОБАВИТЬ** (в нижней части страницы).
    
    1.  Следуйте инструкциям для предоставления порта 80, который используется скриптом развертывания по умолчанию.

- **Использование Windows PowerShell для настройки конечной точки узла Docker в Azure**

    1. Откройте Windows PowerShell
    1. Введите в командной строке Windows PowerShell следующую команду (изменив значения в угловых скобках в соответствии с вашей средой):  

        ```PowerShell
        C:\PS>Get-AzureVM -ServiceName "<your_cloud_service_name>" -Name "<your_vm_name>" | Add-AzureEndpoint -Name "<endpoint_name>" -Protocol "tcp" -PublicPort 80 -LocalPort 80 | Update-AzureVM
        ```

## 6\. Выполните сборку и запустите приложение.
При развертывании на удаленных узлах функция сопоставления тома, используемая для разработки редактирования и обновления, работать не будет. Поэтому, чтобы не настраивать сопоставление тома, для компиляции приложения нужно использовать *конфигурацию выпуска*. Выполните следующие действия, чтобы запустить свое приложение.

1.  На панели инструментов Visual Studio выберите конфигурацию **Выпуск**.

1.  Измените целевой объект для запуска на **Docker**.

1.  Щелкните значок **Docker**, чтобы выполнить сборку и запустить приложение.

![Запуск приложения][2]

Вы должны увидеть результаты, аналогичные приведенным ниже.

![Просмотр приложения][3]

[0]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png

<!---HONumber=AcomDC_0608_2016-->