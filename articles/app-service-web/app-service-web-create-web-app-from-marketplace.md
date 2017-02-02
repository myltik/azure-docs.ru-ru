---
title: "Создание веб-приложения из Azure Marketplace | Документация Майкрософт"
description: "Узнайте, как создать веб-приложение WordPress из Azure Marketplace с помощью портала Azure."
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: wpickett
editor: 
ms.assetid: 972a296d-f927-470b-8534-0f2cb9eac223
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: a04c7129cd2e16c129f3e4b8e8e40f76ff37114d


---
# <a name="create-a-web-app-from-the-azure-marketplace"></a>Создание веб-приложения из Azure Marketplace
<!-- Note: This article replaces web-sites-php-web-site-gallery.md -->

[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Azure Marketplace предоставляет широкий спектр популярных веб-приложений, разработанных нами, сторонними компаниями и группами разработки программного обеспечения с открытым исходным кодом. Например, WordPress, Umbraco CMS, Drupal и т. д. Эти веб-приложения создаются на основе разных популярных платформ, включая [PHP] (в нашем примере с WordPress), [.NET], [Node.js], [Java], [Python] и др. Единственное программное обеспечение, которое понадобится для создания веб-приложения из магазина Azure, — это браузер, который вы будете использовать для входа на [портал Azure].

Из этого руководства вы узнаете, как:

* Найти и создать веб-приложение в службе приложений Azure на основе шаблона из Azure Marketplace.
* Настроить параметры службы приложений Azure для нового веб-приложения.
* Запустить веб-приложение и управлять им.

В рамках этого руководства вы развернете сайт блога WordPress из Azure Marketplace. После выполнения описанных здесь шагов у вас будет собственный сайт WordPress, работающий в облаке.

![Пример панели мониторинга веб-приложения WordPress][WordPressDashboard1]

Сайт WordPress, развертываемый в этом руководстве, использует MySQL для базы данных. Если вместо этого вы хотите использовать базу данных SQL, скачайте приложение [Project Nami], которое также доступно в Azure Marketplace.

> [!NOTE]
> Для работы с этим учебником необходимо использовать учетную запись Microsoft Azure. Если у вас нет учетной записи, можно [активировать преимущества для подписчиков Visual Studio][activate] или [подписаться на бесплатную пробную версию][free trial].
> 
> Чтобы приступить к работе со службой приложений Azure до регистрации и получения учетной записи Azure, перейдите на страницу [пробного использования службы приложений]. Там вы сможете немедленно создать кратковременное начальное веб-приложение в службе приложений. Для этого не требуется ни кредитная карта, ни какие-либо обязательства.
> 
> 

## <a name="find-and-create-a-web-app-in-azure-app-service"></a>Поиск и создание веб-приложения в службе приложений Azure
1. Войдите на [портал Azure].
2. Нажмите кнопку **Создать**.
   
    ![Создайте ресурс Azure][MarketplaceStart]
3. Выполните поиск по запросу **WordPress**, а затем щелкните приложение **WordPress**. (Если вы хотите использовать базу данных SQL вместо MySQL, выполните поиск по запросу **Project Nami**.)
   
    ![Выполните поиск WordPress в Marketplace][MarketplaceSearch]
4. После прочтения описания приложения WordPress нажмите кнопку **Создать**.
   
    ![Создайте веб-приложение WordPress][MarketplaceCreate]

## <a name="configure-azure-app-service-settings-for-your-new-web-app"></a>Настройка параметров службы приложений Azure для нового веб-приложения
1. После создания нового веб-приложения отобразится колонка параметров WordPress, в которой необходимо выполнить следующие шаги:
   
    ![Настройте параметры веб-приложения WordPress][ConfigStart]
2. Введите имя для веб-приложения в поле **Веб-приложение** .
   
    Это имя должно быть уникальным в домене azurewebsites.net, так как URL-адрес веб-приложения будет иметь такой формат: *{имя}*.azurewebsites.net. Если введенное имя не является уникальным, в текстовом поле отображается красный восклицательный знак.
   
    ![Настройте имя веб-приложения WordPress][ConfigAppName]
3. Если у вас есть несколько подписок, выберите ту, которую будете использовать.
   
    ![Настройте подписку для веб-приложения][ConfigSubscription]
4. Выберите **группу ресурсов** или создайте новую.
   
    Дополнительные сведения о группах ресурсов см. в статье [Общие сведения об Azure Resource Manager][ResourceGroups].
   
    ![Настройте группу ресурсов для веб-приложения][ConfigResourceGroup]
5. Выберите или создайте **план службы приложений или расположение** .
   
    Дополнительные сведения о планах службы приложений см. в [подробном обзоре планов службы приложений Azure][AzureAppServicePlans].
   
    ![Настройте план обслуживания для веб-приложения][ConfigServicePlan]
6. Щелкните элемент **База данных** и укажите в колонке **Новая база данных MySQL** значения для настройки базы данных MySQL.
   
    а. Введите новое имя или оставьте имя по умолчанию.
   
    b. Оставьте в поле **Тип базы данных** значение **Общая**.
   
    c. Выберите то же расположение, которое выбрано для веб-приложения.
   
    г) Выберите ценовую категорию. Для этого руководства подходит **Меркурий**, т. е. бесплатно с минимумом подключений и места на диске.
   
    д. В колонке **Новая база данных MySQL** примите условия использования и нажмите кнопку **ОК**.
   
    ![Настройте параметры базы данных для веб-приложения][ConfigDatabase]
7. В колонке **WordPress** примите условия использования и нажмите кнопку **Создать**.
   
    ![Завершите настройку параметров веб-приложения и нажмите кнопку "ОК"][ConfigFinished]
   
    Служба приложений Azure создаст веб-приложение (обычно это занимает меньше минуты). Можно отслеживать ход выполнения, щелкнув значок колокольчика в верхней части страницы портала.
   
    ![Индикатор хода выполнения][ConfigProgress]

## <a name="launch-and-manage-your-wordpress-web-app"></a>Запуск веб-приложения WordPress и управление им
1. После завершения создания веб-приложения перейдите на портале Azure к группе ресурсов, в которой было создано приложение, и вы увидите веб-приложение и базу данных.
   
    Дополнительный ресурс со значком лампочки — надстройка [Application Insights][ApplicationInsights], которая предоставляет службы мониторинга для веб-приложения.
2. В колонке **Группа ресурсов** щелкните строку веб-приложения.
   
    ![Выберите свое веб-приложение WordPress][WordPressSelect]
3. В колонке веб-приложения нажмите кнопку **Обзор**.
   
    ![Перейдите к веб-приложению WordPress][WordPressBrowse]
4. Если будет предложено выбрать язык для блога WordPress, выберите нужный язык и нажмите кнопку **Продолжить**.
   
    ![Настройте язык для веб-приложения WordPress][WordPressLanguage]
5. На странице **приветствия** WordPress введите параметры, которые требуются WordPress, после чего нажмите кнопку **Установить WordPress**.
   
    ![Настройте параметры веб-приложения WordPress][WordPressConfigure]
6. Войдите, используя учетные данные, созданные на странице **приветствия** .  
7. Откроется страница панели мониторинга сайта, в которой отображаются предоставленные сведения.    
   
    ![Просмотрите панель мониторинга WordPress][WordPressDashboard2]

## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве показано, как создать и развернуть пример веб-приложения из Azure Marketplace.

Дополнительные сведения о работе с веб-приложениями службы приложений см. по ссылкам в левой части страницы (для широких окон браузера) или в верхней части страницы (для узких окон браузера).

Дополнительные сведения о разработке веб-приложений WordPress в Azure см. в статье [Разработка блога WordPress в службе приложений Azure][WordPressOnAzure].

<!-- URL List -->

[PHP]: https://azure.microsoft.com/develop/php/
[.NET]: https://azure.microsoft.com/develop/net/
[Node.js]: https://azure.microsoft.com/develop/nodejs/
[Java]: https://azure.microsoft.com/develop/java/
[Python]: https://azure.microsoft.com/develop/python/
[activate]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[free trial]: https://azure.microsoft.com/pricing/free-trial/
[пробного использования службы приложений]: https://azure.microsoft.com/try/app-service/
[ResourceGroups]: ../azure-resource-manager/resource-group-overview.md
[AzureAppServicePlans]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[ApplicationInsights]: https://azure.microsoft.com/services/application-insights/
[портал Azure]: https://portal.azure.com/
[Project Nami]: http://projectnami.org/
[WordPressOnAzure]: ./develop-wordpress-on-app-service-web-apps.md

<!-- IMG List -->

[MarketplaceStart]: ./media/app-service-web-create-web-app-from-marketplace/marketplacestart.png
[MarketplaceSearch]: ./media/app-service-web-create-web-app-from-marketplace/marketplacesearch.png
[MarketplaceCreate]: ./media/app-service-web-create-web-app-from-marketplace/marketplacecreate.png
[ConfigStart]: ./media/app-service-web-create-web-app-from-marketplace/configstart.png
[ConfigAppName]: ./media/app-service-web-create-web-app-from-marketplace/configappname.png
[ConfigSubscription]: ./media/app-service-web-create-web-app-from-marketplace/configsubscription.png
[ConfigResourceGroup]: ./media/app-service-web-create-web-app-from-marketplace/configresourcegroup.png
[ConfigServicePlan]: ./media/app-service-web-create-web-app-from-marketplace/configserviceplan.png
[ConfigDatabase]: ./media/app-service-web-create-web-app-from-marketplace/configdatabase.png
[ConfigFinished]: ./media/app-service-web-create-web-app-from-marketplace/configfinished.png
[ConfigProgress]: ./media/app-service-web-create-web-app-from-marketplace/configprogress.png
[WordPressSelect]: ./media/app-service-web-create-web-app-from-marketplace/wpselect.png
[WordPressBrowse]: ./media/app-service-web-create-web-app-from-marketplace/wpbrowse.png
[WordPressLanguage]: ./media/app-service-web-create-web-app-from-marketplace/wplanguage.png
[WordPressDashboard1]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard1.png
[WordPressDashboard2]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard2.png
[WordPressConfigure]: ./media/app-service-web-create-web-app-from-marketplace/wpconfigure.png



<!--HONumber=Jan17_HO3-->


