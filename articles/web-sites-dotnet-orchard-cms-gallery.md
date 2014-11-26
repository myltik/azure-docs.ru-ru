<properties linkid="develop-dotnet-website-from-gallery" urlDisplayName="Website from Gallery" pageTitle="Create an Orchard CMS website from the gallery in Azure" metaKeywords="Azure build website, manage website Azure" description="A tutorial that teaches you how to create a new website in Azure. Also learn how to launch and manage your site using the Management Portal." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create an Orchard CMS website from the gallery in Azure" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Создание веб-сайта на CMS Orchard из коллекции в Azure

Коллекция предоставляет широкий спектр популярных веб-приложений, разработанных корпорацией Майкрософт, сторонними компаниями и группами разработки программного обеспечения с открытым исходным кодом. Веб-приложения, созданные из коллекции, не требуют установки программного обеспечения помимо браузера, используемого для подключения к порталу управления [Windows Azure][Windows Azure]. Дополнительные сведения о веб-приложениях в коллекции см. в разделе [Коллекция веб-приложений Windows][Коллекция веб-приложений Windows]

В этом учебнике вы узнаете следующее:

-   Как создать новый веб-сайт из коллекции.

-   Как запустить веб-сайт и управлять им с помощью портала управления.

Вам предстоит создать сайт на CMS Orchard, который использует шаблон по умолчанию. [Orchard][Orchard] — это бесплатное CMS-приложение на платформе .NET с открытым исходным кодом, которое позволяет создавать настраиваемые веб-сайты на основе содержимого. CMS Orchard включает платформу расширения, при помощи которой можно [загружать дополнительные модули и темы][загружать дополнительные модули и темы] для настройки вашего веб-сайта. На следующем рисунке показан сайт CMS Orchard, который вам предстоит создать.

![Блог Orchard][Блог Orchard]

[WACOM.INCLUDE [создать-учетная запись-и-веб-сайт-примечание](../includes/create-account-and-websites-note.md)]

## Создание веб-сайта на CMS Orchard из коллекции

1.  Войдите на [портал управления Azure][Windows Azure].

2.  Щелкните значок **Создать** в нижнем левом углу портала.

    ![Создать][Создать]

3.  Щелкните по значку **Веб-сайт** и выберите **Из коллекции**.

    ![Создание из коллекции][Создание из коллекции]

4.  Найдите и выберите значок **CMS Orchard** в списке, а затем нажмите кнопку со стрелкой, чтобы продолжить.

    ![Orchard из списка][Orchard из списка]

5.  На странице **Настройка приложения** введите или выберите значения для всех полей:

-   Введите имя URL-адреса по вашему выбору.
-   Выберите ближайший к вашим пользователям регион. (Это обеспечит лучшую производительность).

    ![настройка приложения][настройка приложения]

1.  Установите флажок в правом нижнем углу окна, чтобы запустить развертывание нового веб-сайта на CMS Orchard.

В Azure будут начаты операции построения и развертывания. Во время создания и развертывания веб-сайта состояние этих операций отображается в нижней части портала управления веб-сайтами. После выполнения всех операций появляется сообщение о том, что веб-сайт был создан.

## Запуск сайта Orchard и управление им

1.  Выберите имя нового сайта на странице **Веб-сайты** и нажмите кнопку **Обзор** в нижней части портала, чтобы открыть начальную страницу веб-сайта.

    ![запуск панели мониторинга][запуск панели мониторинга]

    ![Кнопка "Обзор"][Кнопка "Обзор"]

2.  Введите необходимые сведения о конфигурации, запрошенные Orchard, и нажмите кнопку **Завершить установку**, чтобы завершить настройку и открыть главную страницу веб-сайта.

    ![вход в Orchard][вход в Orchard]

    Вы создадите новый сайт Orchard, похожий на тот, что показан на снимок экрана ниже.

    ![Ваш сайт Orchard][Блог Orchard]

3.  Дополнительные сведения о платформе Orchard и настройке нового сайта см. в [документации по Orchard][документации по Orchard].

## <span class="short-header">Дальнейшие действия</span>Дальнейшие действия

-   [Разработка и развертывание веб-сайта с помощью Microsoft WebMatrix][Разработка и развертывание веб-сайта с помощью Microsoft WebMatrix] -- Узнайте, как редактировать веб-сайт Azure в WebMatrix.
-   [Развертывание безопасного приложения ASP.NET MVC с Membership, OAuth и базой данных SQL на веб-сайте Azure][Развертывание безопасного приложения ASP.NET MVC с Membership, OAuth и базой данных SQL на веб-сайте Azure]-- Узнайте, как создать веб-сайт в Visual Studio.

  [Windows Azure]: http://manage.windowsazure.com
  [Коллекция веб-приложений Windows]: http://www.microsoft.com/web/gallery/categories.aspx
  [Orchard]: http://www.orchardproject.net/
  [загружать дополнительные модули и темы]: http://gallery.orchardproject.net/
  [Блог Orchard]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png
  [Создать]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
  [Создание из коллекции]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
  [Orchard из списка]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
  [настройка приложения]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
  [запуск панели мониторинга]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
  [Кнопка "Обзор"]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
  [вход в Orchard]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
  [документации по Orchard]: http://docs.orchardproject.net/
  [Разработка и развертывание веб-сайта с помощью Microsoft WebMatrix]: /ru-ru/develop/net/tutorials/website-with-webmatrix/
  [Развертывание безопасного приложения ASP.NET MVC с Membership, OAuth и базой данных SQL на веб-сайте Azure]: /ru-ru/develop/net/tutorials/web-site-with-sql-database/
