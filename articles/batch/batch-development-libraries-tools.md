<properties
	pageTitle="Библиотеки и средства разработки для пакетной службы Azure | Microsoft Azure"
	description="Библиотеки и инструменты, необходимые для разработки приложений пакетной службы Azure."
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/24/2015"
	ms.author="danlep"/>


# Библиотеки и инструменты разработки Пакетной службы Azure
<p> В этой статье приведены ссылки на библиотеки и инструменты для разработки приложений пакетной службы Azure.

## Пакетная служба

+ [Клиентская библиотека пакетной службы для .NET](http://www.nuget.org/packages/Azure.Batch/) (NuGet) позволяет разрабатывать клиентские приложения, выполняющие задания в пакетной службе.
+ [Библиотека управления пакетной службой](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet) позволяет управлять учетными записями пакетной службы.
+ [Обозреватель пакетной службы](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer) (GitHub). Приложение с графическим интерфейсом и пример кода для просмотра и изменения основных элементов в учетной записи пакетной службы (например, задания, задачи, вычислительные узлы, пулы, файлы на вычислительном узле и др.). См. [публикацию в блоге](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx).


## Приложения Пакетной службы

+ [Пакет SDK облачных технологий для приложений пакетной службы](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/1.1.1-preview) (NuGet) — с помощью данного пакета приложения смогут выгружать задания в пакетную службу.
+ [Расширение приложений пакетной службы для Visual Studio](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a) (коллекция Visual Studio) — приложения Visual Studio получат возможность работать с облаком через пакет SDK облачных технологий приложений пакетной службы.
+ [Пакет SDK клиента приложений пакетной службы](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/2.3.0-preview) (NuGet) — пакет для взаимодействия с приложениями, выгружающими задания в пакетную службу.
+ [Клиент Python приложений пакетной службы](https://github.com/Azure/azure-batch-apps-python) (GitHub) — модуль клиента Python для взаимодействия с приложениями, настроенными в службе приложений пакетной службы.

>[AZURE.IMPORTANT]Для Azure интерфейс API приложений пакетной службы будет доступен только в виде предварительной версии. Разработку приложений с этим API следует выполнять только для тестирования или проверки концепции проекта. Ключевые возможности будут интегрированы в API пакетных приложений в следующих релизах.

## Дополнительные ресурсы

+ [Примеры кода](https://github.com/Azure/azure-batch-samples) (GitHub)
+ [Форум по пакетной службе Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=August15_HO6-->