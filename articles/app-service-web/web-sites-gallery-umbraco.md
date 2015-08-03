<properties 
	pageTitle="Создание веб-приложения Umbraco из Marketplace в Microsoft Azure" 
	description="Создавайте системы управления содержимым Umbraco и выполняйте развертывание веб-приложения службы приложений Azure." 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="tomfitz"/>

#Создание веб-приложения Umbraco из Marketplace в Microsoft Azure#

Umbraco CMS – это полнофункциональная система управления контентом с открытым исходным кодом, которую можно использовать для создания разнообразных приложений, от небольших до сложных. Azure Marketplace предоставляет широкий выбор популярных веб-приложений, разработанных корпорацией Майкрософт, сторонними компаниями и группами разработки программного обеспечения с открытым исходным кодом. Эта коллекция позволяет создать приложение Umbraco CMS буквально за несколько минут, либо применяя начальные наборы, либо интегрируя собственный проект.

В этой статье представляется портал предварительной версии Azure, который значительно упрощает управление ресурсами. Портал предварительной версии Azure разрабатывался для ускорения процесса предоставления ПО путем размещения межплатформенных инструментов, технологий и служб от корпорации Майкрософт и ее партнеров в одной рабочей области. Вместо использования изолированных ресурсов, таких как веб-приложения [службы приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714), проекты Visual Studio и базы данных, можно создавать приложение целиком, а также управлять им и анализировать его как одну группу ресурсов.

В этом учебнике вы узнаете следующее:

- Как создать новое веб-приложение посредством магазина с помощью портала предварительной версии Azure.
- Как построить веб-сайт блога с помощью Umbraco CMS. 

##Создание веб-приложения из Marketplace на портале предварительной версии Azure

1. Выполните вход на [портал предварительной версии Azure](https://portal.azure.com/).

2. Выберите значок **Marketplace**.
	
	![Выберите коллекцию веб-приложений.][01Startboard]
	
3. В **Marketplace** перейдите на вкладку **Веб-приложения**, а затем выберите **Umbraco CMS**.
	
	![Выберите Umbraco в коллекции веб-приложений][02WebGallery]
	
4. Чтобы создать новое веб-приложение Umbraco CMS, нажмите кнопку **Создать**.
	
	![Нажмите кнопку "Создать"][03UmbracoCMS]
	
5. Теперь необходимо настроить все ресурсы, связанные с Umbraco CMS. В данном случае это веб-приложение и база данных SQL Server. Сначала выберите **Веб-приложение**, чтобы настроить параметры веб-приложения, такие как **URL-адрес**, **План службы приложений**, **Параметры веб-приложения** и **Расположение**.
	
	![Настройка ресурсов][04AppSettings]
	
6. Теперь настройте базу данных. Выберите **База данных**, а затем **Сервер**. В этом примере создается сервер SQL для базы данных в Azure.
	
	![Создание SQL Server в Azure][05NewServer]
	
7. Теперь после настройки веб-приложения и базы данных можно начать развертывание приложения, нажав кнопку **Создать** внизу первой колонки **Umbraco CMS**, показанной на предыдущем рисунке.
	
	![Нажмите кнопку "Создать"][06UmbracoCMSGroup]
	
После завершения развертывания портал отобразит колонку для группы ресурсов веб-приложения Umbraco CMS. В разделе **Сводка** щелкните имя веб-приложения, чтобы просмотреть его свойства. Кроме того, в разделе **Свойства** можно выбрать ресурс базы данных, чтобы увидеть свойства соответствующей базы данных.
	
![][07UmbracoCMSGroupBlade]

## Запуск и настройка веб-приложения Umbraco CMS ##

1. В колонке подробных сведений для веб-приложения щелкните **Обзор** для перехода к веб-приложению.
	
	![Выбор сайта][08UmbracoCMSGroupRunning]
	
2. При переходе к веб-приложению Umbraco CMS запускает мастер установки. Введите необходимые сведения и нажмите кнопку **Настроить**.
	
	![Мастер установки Umbraco][09InstallUmbraco7]
	
3. Укажите данные для подключения и проверки подлинности для базы данных, которая будет использоваться Umbraco. В качестве типа базы данных выберите **Microsoft SQL Azure**. Строку подключения для базы данных можно получить в разделе **Параметры сайта** вашего веб-приложения.
	
	![Настройка базы данных][10ConfigureYourDatabase]
	
4. Если вы еще не знакомы с Umbraco CMS, можно выбрать начальный набор веб-сайта. В противном случае нажмите **No thanks, I do not want to install a starter website** (Я не хочу устанавливать начальный веб-сайт).
	
	![Установка начального веб-сайта][11InstallAStarterWebsite]
	
5. Установщик Umbraco выполнит установку для приложения. После настройки приложения произойдет перенаправление на административную панель мониторинга Umbraco CMS.
	
	![Панель мониторинга Umbraco CMS][14FriendlyCMS]
	
6. Теперь будет создаваться образец текстовой страницы для публикации. Последовательно выберите **Контент**, **Overflow** (Переполнение) и **TextPage**.
	
	![Создание текстовой страницы][15CreateItemUnderOverflow]
	
7. Введите заголовок и какое-нибудь содержание для текстовой страницы, как показано ниже. После завершения нажмите кнопку **Сохранить и опубликовать**.
	
	![Ввод заголовка и содержания][16EnterAName]
	
8. Подождите, пока страница будет опубликована. После выполнения публикации в правом нижнем углу экрана появится небольшое предупреждение. Теперь можно просмотреть новое содержание веб-приложения.
	
	![Страница опубликованного веб-сайта][17MyPage]
	

Это все! Вы успешно создали веб-приложение блога с использованием Umbraco CMS всего за несколько минут.

##Дополнительные ресурсы

[Документация по Umbraco](http://our.umbraco.org/documentation)

[Видеоучебники по Umbraco](https://umbraco.com/help-and-support/video-tutorials.aspx)

[Документация по порталу Azure](../preview-portal.md)

[Портал Azure (канал 9)](http://channel9.msdn.com/Blogs/Windows-Azure/Azure-Preview-portal)

[Документация по веб-приложениям службы приложений Azure](/documentation/services/websites/)

## Изменения
* Руководство по переходу от веб-сайтов к службе приложений см. в разделе [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).
* Руководство по смене старого портала на новый портал см. в разделе [Справочник по навигации на предварительной версии портала](http://go.microsoft.com/fwlink/?LinkId=529715)

>[AZURE.NOTE]Если вы хотите приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.


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
 

<!---HONumber=July15_HO4-->