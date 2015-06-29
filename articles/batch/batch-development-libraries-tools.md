<properties 
	pageTitle="Библиотеки и инструменты разработки Пакетной службы Azure" 
	description="Библиотеки и инструменты, необходимые для разработки пакетной службы Azure и приложений пакетной службы." 
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
<p> Библиотеки и инструменты для разработки пакетной службы Azure и приложений пакетной службы.

## Пакетная служба

+ [Клиентская библиотека пакетной службы для .NET](http://www.nuget.org/packages/Azure.Batch/) (NuGet) позволяет разрабатывать клиентские приложения, выполняющие задания в пакетной службе.
+ [Библиотека управления пакетной службой](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet) — управление учетными записями пакетной службы.
+ [Обозреватель пакетной службы](https://code.msdn.microsoft.com/windowsazure/Azure-Batch-Explorer-c1d37768) (MSDN) — приложение с пользовательским интерфейсом, служащее примером, для просмотра, доступа и обновления основных элементов в учетной записи пакетной службы, включая задания и задачи, виртуальные машины задач (ВМЗ) и пулы, а также файлы на ВМЗ.

> [AZURE.TIP]Обозреватель пакетной службы — отличное средство, если вы не знакомы с пакетной службой или хотите наблюдать за действиями пакетной службы и устранять неполадки. Поскольку это пример кода, исходный код показывает, как реализуются эти функциональные возможности с помощью API .NET пакетной службы. См. [публикацию в блоге](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx).

<p>
## Приложения Пакетной службы

+ [Пакет SDK облачных технологий для приложений пакетной службы](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/) (NuGet) — с помощью данного пакета приложения смогут выгружать задания в пакетную службу.
+ [Расширение приложений пакетной службы для Visual Studio](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a) (коллекция Visual Studio) — приложения Visual Studio получат возможность работать с облаком через пакет SDK облачных технологий приложений пакетной службы.
+ [Пакет SDK клиента приложений пакетной службы](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/) (NuGet) — пакет для взаимодействия с приложениями, выгружающими задания в пакетную службу.
+ [Клиент Python приложений пакетной службы](https://github.com/Azure/azure-batch-apps-python) (GitHub) — модуль клиента Python для взаимодействия с приложениями, настроенными в службе приложений пакетной службы.
+ [Пример Blender приложений пакетной службы](https://github.com/Azure/azure-batch-apps-blender) (GitHub) — дополнительный компонент для программного обеспечения создания трехмерной графики Blender с открытым исходным кодом, использующая пакет SDK приложений пакетной службы и клиент Python для настройки облачной платформы создания трехмерной графики.


## Дополнительные ресурсы

+ [Примеры кода](https://code.msdn.microsoft.com/site/search?f[0].Type=Topic&f[0].Value=Azure%20Batch&f[0].Text=Azure%20Batch) (MSDN)
+ [Форум по пакетной службе Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)
+ [Приступая к работе с библиотекой пакетной службы Azure для .NET](batch-dotnet-get-started.md)  

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=58_postMigration-->