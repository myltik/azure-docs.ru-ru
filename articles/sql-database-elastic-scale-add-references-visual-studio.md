<properties title="Add Azure SQL DB Elastic Scale References to a Visual Studio Project" pageTitle="Добавление ссылок Azure SQL DB Elastic Scale в проект Visual Studio" description="How to add .NET references for Elastic Scale APIs to Visual Studio projects using Nuget." metaKeywords="Azure SQL Database, elastic scale, Nuget references" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Практическое руководство. Добавление ссылок Azure SQL DB Elastic Scale в проект Visual Studio 

###Необходимые компоненты: 

- Установка [коллекции расширения NuGet Visual Studio](http://docs.nuget.org/docs/start-here/installing-nuget) для Visual Studio 

###Добавление ссылки Elastic Scale в Visual Studio 

1. Создайте новый проект с помощью диалогового окна "Создать проект", расположенного **Файл** --> **Создать** --> **Проект** 
2. В обозревателе решений щелкните правой кнопкой мыши **Ссылки** и выберите **Управление пакетами NuGet**
3. В меню в левой части окна "Управление пакетами NuGet" выберите **Online**, а затем **nuget.org** или "Все"
4. В диалоговом окне **Поиск в Интернете** введите **Гибкое масштабирование**, выберите **Клиентские библиотеки Elastic Scale** и щелкните **Установить**.
4. Изучите лицензионное соглашение и нажмите кнопку **Принимаю**. 

Теперь в проект добавлены ссылки Azure SQL Database Elastic Scale. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
