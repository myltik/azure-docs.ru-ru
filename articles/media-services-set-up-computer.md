<properties 
	pageTitle="Настройка компьютера для служб мультимедиа - Azure" 
	description="Сведения о требованиях для разработки служб мультимедиа с помощью пакета SDK служб мультимедиа для .NET. Сведения о создании приложений Visual Studio." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="juliako"/>





<h1><a name="setup-dev"> </a><span class="short header">Настройка компьютера для разработки с использованием служб мультимедиа</span></h1> 

В этом разделе содержатся общие предварительные требования, необходимые для разработки с использованием пакета SDK служб носителей для .NET. Здесь также показано, как разработчики могут создать приложение Visual Studio с использованием пакета Media Services SDK. 

### Предварительные требования

-   Учетная запись служб мультимедиа в новой или существующей подписке Azure. Дополнительные сведения см. в разделе [Создание учетной записи служб мультимедиа](../media-services-create-account/).
-   Операционные системы: Windows 7, Windows 2008 R2 или Windows 8.
-   .NET Framework 4.
-   Visual Studio 2013, Visual Studio 2012 или Visual Studio 2010 с пакетом обновления 1 (Professional, Premium, Ultimate или Express). 
-   С помощью пакета [windowsazure.mediaservices Nuget](http://nuget.org/packages/windowsazure.mediaservices) установите пакет Azure SDK для .NET. В следующем разделе показано, как использовать **Nuget**, чтобы установить пакет Windows Azure SDK.
  
<h2><a name="setup-account"></a><span class="short header">Настройка учетной записи Azure для служб мультимедиа</span></h2>

Для настройки учетной записи служб мультимедиа следует воспользоваться порталом управления Azure (рекомендуется). Дополнительные сведения см. в раздел [Создание учетной записи служб мультимедиа][]. После создания учетной записи на портале управления можно приступить к настройке компьютера для разработки с использованием служб носителей. 

### Создание приложения в Visual Studio

В этом разделе показано, как создать проект в Visual Studio и настроить его для разработки с использованием служб мультимедиа.  В этом случае проект представляет собой консольное приложение Windows на C#, но те же действия для установки применяются для других типов проектов, которые можно создать для приложений служб мультимедиа (например, приложения Windows Forms или веб-приложения ASP.NET).

   1. Создайте новое **консольное приложение** C# в  Visual Studio 2013, Visual Studio 2012 или Visual Studio 2010 SP1. Введите значения в поля **Имя**, **Расположение** и **Имя решения**, а затем нажмите кнопку **ОК**.
   2. Убедитесь, что в качестве целевой платформы задана .NET Framework 4. Для этого щелкните правой кнопкой мыши в окне проекта Visual Studio и выберите "Свойства". На вкладке "Приложения" в качестве целевой платформы задайте .NET Framework 4.   
   3. Добавьте ссылку на сборку **System.Configuration**. Чтобы добавить ссылку на **System.Configuration**, в **обозревателе решений** щелкните правой кнопкой мыши узел **Ссылки** и выберите команду **Добавить ссылку...**. В диалоговом окне **Управление ссылками** выберите **System.Configuration** и нажмите кнопку **ОК**.
   4. С помощью пакета [Nuget windowsazure.mediaservices](http://nuget.org/packages/windowsazure.mediaservices) добавьте ссылки на **пакет Azure SDK для .NET**. (также будут установлены все прочие зависимые сборки).  

	Чтобы добавить ссылки, с помощью Nuget, выполните следующие действия. В **главном меню** Visual Studio выберите **Сервис** -> **Диспетчер пакетов библиотеки** -> **Консоль диспетчера пакетов**. В окне консоли введите **Install-Package windowsazure.mediaservices** и нажмите клавишу **ВВОД**.
   4. Замените существующие инструкции using в начале файла Program.cs на следующий код.

   		using System;
		using System.Linq;
		using System.Configuration;
		using System.IO;
		using System.Text;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Collections.Generic;
		using Microsoft.WindowsAzure.MediaServices.Client;

На этом этапе вы готовы начать разработку приложения служб мультимедиа.    
   
<h2><a name="setup-account"></a><span class="short header">Подключение к службам мультимедиа</span></h2> 

Практически все задачи программирования служб мультимедиа требуют ссылку на объект контекста сервера. Контекст сервера предоставляет программный доступ ко всем объектам программирования служб мультимедиа.

Чтобы получить ссылку на контекст сервера, создайте новый экземпляр типа контекста, как показано в следующем примере кода. Передайте конструктору имя учетной записи и ключ учетной записи служб мультимедиа (полученные во время настройки учетной записи). 

    // Create and cache the Media Services credentials in a static class variable.
	_cachedCredentials = new MediaServicesCredentials(
	                _mediaServicesAccountName,
	                _mediaServicesAccountKey);

	// Use the cached credentials to create CloudMediaContext.
	_context = new CloudMediaContext(_cachedCredentials);


Часто бывает полезным определить переменную уровня модуля типа **CloudMediaContext** для хранения ссылки на контекст сервера. Дополнительные сведения см. в разделе [Подключение к службам мультимедиа с помощью пакета SDK служб мультимедиа для .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

Остальные примеры кода в этом разделе используют переменную с именем **_context** для ссылки на контекст сервера. 

<h2>Дальнейшие действия</h2>
Теперь после настройки компьютера и создания решения Visual Studio для программирования служб мультимедиа, перейдите к разделу [Создание зашифрованного актива и его загрузка в хранилище][].
[Создание учетной записи служб мультимедиа]: ../media-services-create-account/
[Создание зашифрованного актива и его загрузка в хранилище]: ../media-services-create-encrypted-asset-upload-storage/


<!--HONumber=42-->
