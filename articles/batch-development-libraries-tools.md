<properties 
	pageTitle="Библиотеки и инструменты разработки Пакетной службы Azure" 
	description="Получите библиотеки и средства, необходимые для разработки приложений пакета Azure и пакета приложений" 
	services="batch" 
	documentationCenter="" 
	authors="dlepow" 
	manager="timlt"
	editor="yidingz"/>

<tags 
	ms.service="batch" 
	ms.workload="big-compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="danlep"/>


# Библиотеки и инструменты разработки Пакетной службы Azure 
<p> Получение этих библиотек и средств для разработки приложений пакета Azure и пакета приложений.

## Пакетная служба

+ [Пакета клиентская библиотека для .NET](http://www.nuget.org/packages/Azure.Batch/) (NuGet) — Разрабатывайте клиентские приложения для запуска заданий со службой пакета
+ [Библиотека пакета управления](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet) — учетные записи служб для пакета управления
+ [Пакета обозревателя](https://code.msdn.microsoft.com/windowsazure/Azure-Batch-Explorer-c1d37768) (MSDN) — графического пользовательского интерфейса приложения и образец для обзора, доступа и обновления основных элементов в пределах учетной записи пакета, включая заданий и задач, задача виртуальных машин (TVMs) и пулы и файлы на TVM.

> [AZURE.TIP]Обозреватель пакета — отличное средство, если новый пакет или для наблюдения или устранения действия пакета. Поскольку образец кода, исходном коде показано, как функциональные возможности, реализованные с помощью API-интерфейса .NET пакета. В разделе [в блоге](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx).

<p>
## Приложения Пакетной службы

+ [Пакета SDK облачных приложений](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/) (NuGet) — позволяет разгрузить задания для пакета обновления приложения
+ [Расширение пакета приложений Visual Studio](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a) (коллекции Visual Studio —) Включение облачных приложений в Visual Studio с помощью пакета SDK облачных приложений пакета
+ [Пакета SDK клиента приложения](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/) (NuGet) — взаимодействовать с приложениями, которые включены для снятия задания для пакета обновления
+ [Пакета приложений Python клиента](https://github.com/Azure/azure-batch-apps-python) (GitHub) — настроить модуль Python клиента для взаимодействия с приложениями в пакете приложения службы
+ [Blender образец пакета приложения](https://github.com/Azure/azure-batch-apps-blender) (GitHub) — дополнительный компонент для Blender с открытым кодом отрисовки, использует клиента пакет SDK приложения и Python для настройки подготовки облачной платформы


## Дополнительные ресурсы

+ [Примеры кода](https://code.msdn.microsoft.com/site/search?f[0].Type=Topic&f[0].Value=Azure%20Batch&f[0].Text=Azure%20Batch) (MSDN)
+ [Форум Azure пакета](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)
+ [Начало работы с библиотекой пакета Azure для .NET](batch-dotnet-get-started.md)  

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=GIT-SubDir-->