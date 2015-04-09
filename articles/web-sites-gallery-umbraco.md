<properties 
	pageTitle="Создание веб-сайта Umbraco из коллекции в Microsoft Azure" 
	description="Создавайте системы управления содержимым Umbraco и выполняйте развертывание на веб-сайте Azure." 
	services="web-sites" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/25/2015" 
	ms.author="tomfitz"/>

#Создание веб-сайта Umbraco из коллекции в Microsoft Azure#

Umbraco CMS - это полнофункциональная система управления контентом с открытым исходным кодом, которую можно использовать для создания разнообразных приложений, от небольших до сложных. Коллекция приложений веб-сайтов Azure предоставляет широкий спектр популярных веб-приложений, разработанных корпорацией Майкрософт, сторонними компаниями и группами разработки программного обеспечения с открытым исходным кодом. Эта коллекция позволяет создать сайт Umbraco CMS буквально за несколько минут, либо применяя начальные наборы, либо интегрируя собственный проект. 

В этой статье представляется новый портал предварительной версии Azure, который значительно упрощает управление ресурсами. Новый портал Azure разрабатывался для ускорения процесса предоставления ПО путем размещения кроссплатформенных инструментов, технологий и служб от корпорации Майкрософт и ее партнеров в одном рабочем пространстве. Вместо использования изолированных ресурсов, таких как веб-сайты Azure, проекты Visual Studio и базы данных, можно создавать все приложение, а также управлять им и анализировать его как одну группу ресурсов. 

В этом учебнике вы узнаете следующее:

- Как создать новый сайт из коллекции с помощью нового портала предварительной версии Azure.
- Как построить веб-сайт блога с помощью Umbraco CMS. 

##Создание веб-сайта из коллекции на портале Azure

1. Войдите на [портал управления Microsoft Azure](https://portal.azure.com/).

2. Щелкните значок **Коллекция Azure**.
	
	![Choose Web Gallery][01Startboard]
	
3. В **Коллекции** выберите вкладку **Веб**, затем выберите **Umbraco CMS**.
	
	![Select Umbraco in the Web Gallery][02WebGallery]
	
4. Чтобы создать новый веб-сайт Umbraco CMS, нажмите кнопку **Создать**.
	
	![Click Create][03UmbracoCMS]
	
5. Теперь необходимо настроить все ресурсы, связанные с Umbraco CMS. В данном случае это веб-сайт и база данных SQL Server. Сначала выберите **Веб-сайт**, чтобы настроить параметры веб-сайта, например **URL-адрес**, **План размещения веб-сайта**, **Параметры веб-приложения** и **Расположение**. 
	
	![Configure resources][04AppSettings]
	
6. Теперь настройте базу данных. Выберите пункт **База данных**, а затем выберите команду **Создать новую базу данных**. В этом примере создается сервер SQL для базы данных в Azure.
	
	![Create a SQL Server on Azure][05NewServer]
	
7. Теперь после настройки веб-сайта и базы данных можно начать развертывание приложения, нажав кнопку **Создать** в нижней части первой колонки **Umbraco CMS** (см. предыдущий рисунок).
	
	![Click Create][06UmbracoCMSGroup]
	
После завершения развертывания начальная панель на портале показывает, что ваша группа ресурсов для Umbraco CMS (в данном случае **UmbracoCMSgroup**) создана. В разделе **Сводка** щелкните имя веб-сайта (в данном случае **umbracocmsgroup**), чтобы просмотреть его свойства. Кроме того, в разделе **Сводка** вы можете выбрать ресурс базы данных, чтобы увидеть свойства соответствующей базы данных.
	
![][07UmbracoCMSGroupBlade]

## Запуск и настройка веб-сайта Umbraco CMS ##

1. В колонке сведений для веб-сайта нажмите кнопку **Обзор**, чтобы выбрать свой сайт (в данном случае это umbracocmsgroup.azurewebsites.net.)
	
	![Browse to your site][08UmbracoCMSGroupRunning]
	
2. Когда вы переходите на веб-сайт, Umbraco CMS запускает мастер установки. Введите необходимые сведения и нажмите кнопку **Настроить**.
	
	![Install Umbraco wizard][09InstallUmbraco7]
	
3. Укажите данные для подключения и проверки подлинности для базы данных, которая будет использоваться Umbraco. В качестве типа базы данных выберите **Microsoft SQL Azure**.  Строку подключения для базы данных см. в разделе **Параметры сайта** своего веб-сайта.
	
	![Configure your database][10ConfigureYourDatabase] 
	
4. Если вы еще не знакомы с Umbraco CMS, можно выбрать начальный набор веб-сайта. В противном случае нажмите **Нет, спасибо, я не хочу устанавливать начальный веб-сайт**.
	
	![Install a starter website][11InstallAStarterWebsite]
	
5. Установщик Umbraco выполнит установку для приложения. После настройки приложения произойдет перенаправление на административную панель мониторинга Umbraco CMS.
	
	![Umbraco CMS dashboard][14FriendlyCMS]
	
6. Теперь будет создаваться образец текстовой страницы для публикации. В меню **Содержимое** выберите пункт **Переполнение**, а затем - **TextPage**.
	
	![Create a text page][15CreateItemUnderOverflow]
	
7. Введите заголовок и какое-нибудь содержание для текстовой страницы, как показано ниже. После завершения нажмите кнопку **Сохранить и опубликовать**.
	
	![Enter a title and some content][16EnterAName]
	
8. Подождите, пока страница будет опубликована. После выполнения публикации в правом нижнем углу экрана появится небольшое предупреждение. Теперь можно просмотреть новое содержание веб-сайта. 
	
	![Published web site page][17MyPage]
	

Это все! Вы успешно создали веб-сайт блога с использованием Umbraco CMS всего за несколько минут. 

##Дополнительные ресурсы

[Документация по Umbraco](http://our.umbraco.org/documentation)

[Видеоучебники по Umbraco](https://umbraco.com/help-and-support/video-tutorials.aspx)

[Обзор портала предварительной версии Microsoft Azure](http://azure.microsoft.com/overview/preview-portal/)

[Документация по порталу предварительной версии Microsoft Azure](http://azure.microsoft.com/documentation/preview-portal/)

[Портал предварительной версии (Канал 9)](http://channel9.msdn.com/Blogs/Windows-Azure/Azure-Preview-portal) 

[Документация по веб-сайтам Microsoft Azure](http://azure.microsoft.com/documentation/services/web-sites/)


<!-- IMAGES -->
[01Startboard]: ./media/web-sites-gallery-umbraco/01Startboard.PNG
[02WebGallery]: ./media/web-sites-gallery-umbraco/02WebGallery.PNG
[03UmbracoCMS]: ./media/web-sites-gallery-umbraco/03UmbracoCMS.PNG
[04AppSettings]: ./media/web-sites-gallery-umbraco/04AppSettings.PNG
[05NewServer]: ./media/web-sites-gallery-umbraco/05NewServer.PNG
[06UmbracoCMSGroup]: ./media/web-sites-gallery-umbraco/06UmbracoCMSGroup.PNG
[07UmbracoCMSGroupBlade]: ./media/web-sites-gallery-umbraco/07UmbracoCMSGroupBlade.PNG
[08UmbracoCMSGroupRunning]: ./media/web-sites-gallery-umbraco/08UmbracoCMSGroupRunning.PNG
[09InstallUmbraco7]: ./media/web-sites-gallery-umbraco/09InstallUmbraco7.png
[10ConfigureYourDatabase]: ./media/web-sites-gallery-umbraco/10ConfigureYourDatabase.png
[11InstallAStarterWebsite]: ./media/web-sites-gallery-umbraco/11InstallAStarterWebsite.png
[12ConfigureYourDatabase]: ./media/web-sites-gallery-umbraco/12ConfigureYourDatabase.png
[14FriendlyCMS]: ./media/web-sites-gallery-umbraco/14FriendlyCMS.PNG
[15CreateItemUnderOverflow]: ./media/web-sites-gallery-umbraco/15CreateItemUnderOverflow.PNG
[16EnterAName]: ./media/web-sites-gallery-umbraco/16EnterAName.PNG
[17MyPage]: ./media/web-sites-gallery-umbraco/17MyPage.PNG

<!--HONumber=49-->