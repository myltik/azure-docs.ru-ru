<properties urlDisplayName="Website from Gallery" pageTitle="Создание веб-сайта на CMS Orchard из коллекции в Azure" metaKeywords="создание веб-сайта Azure, управление веб-сайтом Azure" description="A tutorial that teaches you how to create a new website in Azure. Also learn how to launch and manage your site using the Management Portal." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create an Orchard CMS website from the gallery in Azure" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/21/2014" ms.author="tomfitz" />

# Создание веб-сайта на CMS Orchard из коллекции в Azure

Коллекция предоставляет широкий спектр популярных веб-приложений, разработанных корпорацией Майкрософт, сторонними компаниями и группами разработки программного обеспечения с открытым исходным кодом. Веб-приложения, созданные из коллекции, не требуют установки программного обеспечения помимо браузера, используемого для подключения к порталу управления [Windows Azure](http://manage.windowsazure.com). Дополнительные сведения о веб-приложениях в коллекции см. в разделе [Коллекция веб-приложений Windows](http://www.microsoft.com/web/gallery/categories.aspx)

В этом учебнике вы узнаете следующее:

- Как создать новый веб-сайт из коллекции.

- Как запустить веб-сайт и управлять им с помощью портала управления.
 
Вам предстоит создать сайт на CMS Orchard, который использует шаблон по умолчанию. [Orchard](http://www.orchardproject.net/) - это бесплатное CMS-приложение на платформе .NET с открытым исходным кодом, которое позволяет создавать настраиваемые веб-сайты на основе содержимого. CMS Orchard включает платформу расширения, при помощи которой можно [загружать дополнительные модули и темы](http://gallery.orchardproject.net/) для настройки вашего веб-сайта. На следующем рисунке показан сайт CMS Orchard, который вам предстоит создать.

![Orchard blog][13]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

<h2>Создание веб-сайта на CMS Orchard из коллекции</h2>

1. Выполните вход на [портал управления Azure](http://manage.windowsazure.com).

2. Щелкните значок **Создать** в левом нижнем углу портала.
	
	![Create New][1]

3. Щелкните значок **Веб-сайт**, а затем - **Из коллекции**.
	
	![Create From Gallery][2]

4. Найдите и выберите значок **CMS Orchard** в списке, а затем нажмите кнопку со стрелкой, чтобы продолжить.
	
	![Orchard from list][3]

5. На странице **Настройка приложения** введите или выберите значения для всех полей.
	
- Введите URL-адрес по своему выбору.	
- Выберите ближайший к вашим пользователям регион. (Это обеспечит наилучшую производительность.)

	![configure your app][4]

6. Установите флажок в правом нижнем углу окна, чтобы запустить развертывание нового веб-сайта на CMS Orchard.

В Azure будут начаты операции построения и развертывания. Во время создания и развертывания веб-сайта состояние этих операций отображается в нижней части портала управления веб-сайтами. После выполнения всех операций появляется сообщение о том, что веб-сайт был создан.

<h2>Запуск сайта Orchard и управление им</h2>

1. Выберите имя нового сайта на странице **Веб-сайты** и нажмите кнопку **Обзор** в нижней части портала, чтобы открыть начальную страницу веб-сайта.

	![launch dashboard][5]

	![browse button][12]

2. Введите необходимые сведения о конфигурации, запрошенные Orchard, и нажмите кнопку **Завершить установку**, чтобы завершить настройку и открыть главную страницу веб-сайта.

	![login to Orchard][7]

	У вас будет новый сайт Orchard, похожий на тот, что показан на снимке экрана ниже.  

	![your Orchard site][13]

3. Дополнительные сведения о платформе Orchard и настройке нового сайта см. в [документации по Orchard](http://docs.orchardproject.net/).

<h2>Дальнейшие действия</h2>
* [Разработка и развертывание веб-сайта с помощью Microsoft WebMatrix](/ru-ru/develop/net/tutorials/website-with-webmatrix/) -- Узнайте, как редактировать веб-сайт Azure в WebMatrix. 
* [Развертывание безопасного приложения ASP.NET MVC с Membership, OAuth и базой данных SQL на веб-сайте Azure](/ru-ru/develop/net/tutorials/web-site-with-sql-database/)-- Сведения о создании нового веб-сайта в Visual Studio.

[1]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
[2]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
[3]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
[4]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
[5]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
[7]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
[12]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
[13]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png


