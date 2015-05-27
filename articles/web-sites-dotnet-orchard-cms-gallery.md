<properties 
	pageTitle="Создание веб-приложения Orchard CMS из Azure Marketplace" 
	description="Учебник, в котором показано, как создать новое веб-приложение в Azure и использовать портал Azure для запуска веб-приложений и управления ими." 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="tomfitz"/>

# Создание веб-приложения Orchard CMS из Azure Marketplace

## Обзор

Marketplace предоставляет широкий выбор популярных веб-приложений, разработанных корпорацией Майкрософт, сторонними компаниями и группами разработки программного обеспечения с открытым исходным кодом. Веб-приложения, созданные из Azure Marketplace, не требуют наличия специального программного обеспечения. Все, что вам нужно, — это браузер для подключения к [порталу предварительной версии Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Дополнительные сведения о веб-приложениях из Marketplace см. в разделе [Azure Marketplace](/marketplace/).

В этом учебнике вы узнаете следующее:

- как создавать новые веб-приложения из Azure Marketplace;

- как использовать портал Azure для запуска веб-приложений и управления ими.
 
Вам предстоит создать веб-приложение Orchard CMS, которое использует шаблон по умолчанию. [Orchard](http://www.orchardproject.net/) — это бесплатное CMS-приложение на платформе .NET с открытым исходным кодом, которое позволяет создавать настраиваемые веб-приложения и веб-сайты на основе содержимого. Orchard CMS включает платформу расширения, при помощи которой можно [загружать дополнительные модули и темы](http://gallery.orchardproject.net/) для настройки вашего веб-приложения. На следующем рисунке показано веб-приложение Orchard CMS в [службе приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714), которое вам предстоит создать.

![Блог Orchard][13]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Если вы хотите приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.

## Создание веб-приложения Orchard из Marketplace

1. Выполните вход на [портал предварительной версии Azure](http://portal.azure.com).

2. Выберите **Создать** > **Веб + мобильные** > **Azure Marketplace**.
	
	![Создать][1]

3. Выберите **Веб-приложения** > **Orchard CMS**. В следующей колонке нажмите **Создать**.
	
	![Создание из Marketplace][2]

4. Настройте URL-адрес веб-приложения, план службы приложений, группу ресурсов и расположение. Когда все будет готово, нажмите **Создать**.
	
	![Настройка приложения][3]

	После создания веб-приложения на кнопке **Уведомления** загорится зеленым слово "УСПЕШНО", а также отобразится колонка веб-приложения.

## Запуск веб-приложения Orchard и управление им

1. В колонке веб-приложения нажмите **Обзор**, чтобы открыть начальную страницу веб-приложения.

	![Кнопка "Обзор"][12]

2. Введите необходимые сведения о конфигурации, запрошенные Orchard, и нажмите кнопку **Завершить установку**, чтобы закончить настройку и открыть домашнюю страницу веб-приложения.

	![вход в Orchard][7]

	У вас будет новое веб-приложение Orchard, похожее на то, что показано на снимке экрана ниже.

	![веб-приложение Orchard][13]

3. Дополнительные сведения о платформе Orchard и настройке нового веб-приложения см. в [документации по Orchard](http://docs.orchardproject.net/).

## Дальнейшие действия

* [Разработка и развертывание веб-приложения с помощью Microsoft WebMatrix](web-sites-dotnet-using-webmatrix.md) — узнайте, как редактировать веб-приложения службы Azure в WebMatrix. 
* [Создание приложения ASP.NET MVC с проверкой подлинности и базой данных SQL и развертывание в службу приложений Azure](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md) — узнайте, как создать новое веб-приложение в службе приложений Azure из Visual Studio.

## Изменения
* Руководство по переходу от веб-сайтов к службе приложений см. в разделе [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714)
* Руководство по смене старого портала на новый портал см. в разделе [Справочник по навигации на предварительной версии портала](http://go.microsoft.com/fwlink/?LinkId=529715)

[1]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
[2]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
[3]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
[4]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
[5]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
[7]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
[12]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
[13]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png



<!--HONumber=54-->