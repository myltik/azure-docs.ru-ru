<properties linkid="develop-media-services-how-to-guides-set-up-computer" urlDisplayName="Set Up Computer for Media Services" pageTitle="How to Set Up Computer for Media Services - Azure" metaKeywords="" description="Learn about the prerequisites for Media Services using the Media Services SDK for .NET. Also learn how to create a Visual Studio app." metaCanonical="" services="media-services" documentationCenter="" title="Setting up your computer for Media Services development" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"/>

# <a name="setup-dev"> </a><span class="short header">Настройка компьютера для разработки с использованием служб мультимедиа</span>

В этом разделе содержатся общие предварительные требования, необходимые для разработки с использованием пакета SDK служб носителей для .NET. Здесь также показано, как разработчики могут создать приложение Visual Studio с использованием пакета Media Services SDK.

### Предварительные требования

-   Учетная запись служб мультимедиа в новой или существующей подписке Azure. Дополнительные сведения см. в разделе [Создание учетной записи служб мультимедиа][].
-   Операционные системы: Windows 7, Windows 2008 R2 или Windows 8.
-   .NET Framework 4.5 или .NET Framework 4.
-   Visual Studio 2012 или Visual Studio 2010 с пакетом обновления 1 (Professional, Premium, Ultimate или Express).
-   С помощью пакета [windowsazure.mediaservices Nuget][] установите пакет Windows Azure SDK для .NET. В следующем разделе показано, как использовать **Nuget**, чтобы установить пакет Windows Azure SDK.

## <a name="setup-account"></a><span class="short header">Настройка учетной записи Azure для служб мультимедиа</span>

Для настройки учетной записи служб мультимедиа следует воспользоваться порталом управления Azure (рекомендуется). Дополнительные сведения см. в разделе [Создание учетной записи служб мультимедиа][]. После создания учетной записи на портале управления можно приступить к настройке компьютера для разработки с использованием служб носителей.

### Создание приложения в Visual Studio

В этом разделе показано, как создать проект в Visual Studio и настроить его для разработки с использованием служб мультимедиа. В этом случае проект представляет собой консольное приложение Windows на C#, но те же действия для установки применяются для других типов проектов, которые можно создать для приложений служб мультимедиа (например, приложения Windows Forms или веб-приложения ASP.NET).

1.  Создайте **Консольное приложение** C# в Visual Studio 2012 или Visual Studio 2010 SP1. Введите значения в поля **Имя**, **Расположение** и **Имя решения**, а затем нажмите кнопку **ОК**.
2.  Добавьте ссылку на сборку **System.Configuration**. Чтобы добавить ссылку на **System.Configuration**, в **обозревателе решений** щелкните правой кнопкой мыши узел **Ссылки** и выберите команду **Добавить ссылку...**. В диалоговом окне **Управление ссылками** выберите **System.Configuration** и нажмите кнопку **ОК**.
3.  Добавьте ссылки на **Пакет Windows Azure SDK для .NET.** (Microsoft.WindowsAzure.StorageClient.dll), **Пакет Windows Azure Media Services SDK для .NET** (Microsoft.WindowsAzure.MediaServices.Client.dll) и библиотеки **Службы данных WCF 5.0 для OData V3** (Microsoft.Data.OData.dll) с помощью пакета [windowsazure.mediaservices Nuget][].

    Чтобы добавить ссылки, с помощью Nuget, выполните следующие действия. В **главном меню** Visual Studio выберите **Сервис** -\> **Диспетчер пакетов библиотеки** -\> **Консоль диспетчера пакетов**. В окне консоли введите **Install-Package windowsazure.mediaservices** и нажмите клавишу **ВВОД**.

4.  Замените существующие инструкции using в начале файла Program.cs на следующий код.

        using System;
        using System.Linq;
        using System.Configuration;
        using System.IO;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using System.Collections.Generic;
        using Microsoft.WindowsAzure;
        using Microsoft.WindowsAzure.MediaServices.Client;

На этом этапе вы готовы начать разработку приложения служб мультимедиа.

## <a name="setup-account"></a><span class="short header">Подключение к службам мультимедиа</span>

Практически все задачи программирования служб мультимедиа требуют ссылку на объект контекста сервера. Контекст сервера предоставляет программный доступ ко всем объектам программирования служб мультимедиа.

Чтобы получить ссылку на контекст сервера, извлеките новый экземпляр типа контекста, как показано в следующем примере кода. Передайте конструктору имя учетной записи и ключ учетной записи служб мультимедиа (полученные во время настройки учетной записи).

    static CloudMediaContext GetContext()
    {
        // Gets the service context. 
        return new CloudMediaContext(_accountName, _accountKey);
    } 

Часто бывает полезным определить переменную уровня модуля типа **CloudMediaContext** для хранения ссылки на контекст сервера, который возвращается методом, таким как **GetContext**. Остальные примеры кода в этом разделе используют переменную с именем \*\*\_context\*\* для ссылки на контекст сервера.

## Дальнейшие действия

Теперь после настройки компьютера и создания решения Visual Studio для программирования служб мультимедиа, перейдите к разделу [Создание зашифрованного актива и его отправка в хранилище][] статья.
[Создание учетной записи служб мультимедиа]: <http://go.microsoft.com/fwlink/?linkid=256662>
[Создание зашифрованного актива и его отправка в хранилище]:<http://go.microsoft.com/fwlink/?LinkID=301733&clcid=0x409>

  [Создание учетной записи служб мультимедиа]: http://www.windowsazure.com/en-us/manage/services/media-services/how-to-create-a-media-services-account/
  [windowsazure.mediaservices Nuget]: http://nuget.org/packages/windowsazure.mediaservices
