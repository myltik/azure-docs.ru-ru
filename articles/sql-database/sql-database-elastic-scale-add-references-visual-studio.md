<properties 
	pageTitle="Добавление ссылок для эластичного масштабирования баз данных SQL Azure в проект Visual Studio" 
	description="Как добавить ссылки на .NET для эластичного масштабирования интерфейсов API для проектов Visual Studio с помощью Nuget." 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/16/2015" 
	ms.author="sidneyh"/>

# Практическое руководство. Добавление справочников по клиентской библиотеке в эластичную базу данных в проект Visual Studio 

### Предварительные требования: 

- Установка [коллекции расширений NuGet Visual Studio](http://docs.nuget.org/docs/start-here/installing-nuget) для Visual Studio 

### Добавление справочников по клиентской библиотеке в эластичную базу данных в проект Visual Studio 

1. Откройте существующий проект или создайте новый проект с помощью диалогового окна "Создать проект", которое можно открыть, щелкнув **Файл** --> **Создать** --> **Проект**. 
2. В обозревателе решений щелкните правой кнопкой мыши **Ссылки** и выберите **Управление пакетами NuGet**
3. В меню в левой части окна "Управление пакетами NuGet" выберите **Online**, а затем **nuget.org** или "All" 
4. В диалоговом окне **Поиск в Интернете** введите **Эластичная база данных**, выберите **Клиентская библиотека эластичной базы данных** и щелкните **Установить**.

	![Поиск в Интернете][1]
4. Изучите лицензионное соглашение и нажмите кнопку **Принимаю**. 

Справочник по клиентской библиотеке теперь добавлен в проект.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-add-references-visual-studio/search-online.png
<!--anchors-->

<!---HONumber=July15_HO2-->