---
title: "Доступ к локальным ресурсам с помощью гибридных подключений в службе приложений Azure"
description: "Создание подключения между веб-приложением в службе приложений Azure и локальным ресурсом, который использует статический TCP-порт."
services: app-service
documentationcenter: 
author: cephalin
manager: wpickett
editor: mollybos
ms.assetid: a46ed26b-df8e-4fc3-8e05-2d002a6ee508
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: cb656dd952e76ee29e102fff531d34a45e4630e4


---
# <a name="access-on-premises-resources-using-hybrid-connections-in-azure-app-service"></a>Доступ к локальным ресурсам с помощью гибридных подключений в службе приложений Azure
Приложение служб приложений Azure можно подключить к любому локальному ресурсу, который использует статический TCP-порт: например, SQL Server, MySQL, веб-интерфейсы API HTTP и большинство настраиваемых веб-служб. В этой статье показано, как создать гибридное подключение между приложением в службе приложений и локальной базой данных SQL Server.

> [!NOTE]
> Часть функции гибридных подключений, которая называется "Веб-приложения", доступна только на [портале Azure](https://portal.azure.com). Сведения о создании подключения в службах BizTalk см. в статье [Обзор гибридных подключений](http://go.microsoft.com/fwlink/p/?LinkID=397274). 
> 
> Это содержимое также применяется к мобильным приложениям в службе приложений Azure. 
> 
> 

## <a name="prerequisites"></a>Предварительные требования
* Подписка Azure. Бесплатную подписку можно найти на сайте [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/). 
  
    Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](https://azure.microsoft.com/try/app-service/), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.
* Для использования локальной базы данных SQL Server или SQL Server Express с помощью гибридного подключения в статическом порте должен быть включен протокол TCP/IP. Мы рекомендуем использовать экземпляр SQL Server по умолчанию, так как он использует статический порт 1433. Информацию об установке и настройке SQL Server Express для использования с гибридными подключениями см. в разделе [Подключение к локальному SQL Server с веб-сайта Azure с помощью гибридных подключений](http://go.microsoft.com/fwlink/?LinkID=397979).
* Компьютер, на котором установлен локальный агент диспетчера гибридных подключений, описан далее в этой статье:
  
  * должен подключаться к Azure через порт 5671;
  * должен подключаться к *hostname*:*portnumber* локального источника. 

> [!NOTE]
> В шагах этой статьи предполагается, что вы используете браузер с компьютера, на котором размещается локальный агент гибридного подключения.
> 
> 

## <a name="create-a-web-app-in-the-azure-portal"></a>Создание веб-приложения на портале Azure
> [!NOTE]
> Если вы уже создали веб-приложение или серверную часть мобильного приложения на портале Azure и хотите использовать его в этом учебнике, можно пропустить этот шаг и начать с раздела [Создание гибридного подключения и службы BizTalk](#CreateHC) .
> 
> 

1. В левом верхнем углу [портала Azure](https://portal.azure.com) щелкните **Создать** > **Интернет + мобильные устройства** > **Веб-приложение**.
   
    ![Создание веб-приложения][NewWebsite]
2. В колонке **Веб-приложение** укажите URL-адрес и щелкните **Создать**. 
   
    ![Имя веб-сайта][WebsiteCreationBlade]
3. Через несколько секунд будет создано веб-приложение и появится его колонка. Выноска — это вертикальная панель мониторинга, которую можно прокручивать и которая позволяет управлять сайтом.
   
    ![Запущенный веб-сайт][WebSiteRunningBlade]
4. Чтобы проверить, что веб-сайт работает, можно щелкнуть значок **Обзор** для отображения страницы по умолчанию.
   
    ![Щелкните "Обзор", чтобы посмотреть веб-приложение][Browse]
   
    ![Страница веб-приложения по умолчанию][DefaultWebSitePage]

Далее вы создадите гибридное подключение и службу BizTalk для веб-приложения.

<a name="CreateHC"></a>

## <a name="create-a-hybrid-connection-and-a-biztalk-service"></a>Создание гибридного подключения и службы BizTalk
1. В своей колонке веб-приложения щелкните **Все настройки** > **Сеть** > **Настройка конечных точек гибридного подключения**.
   
    ![Гибридные подключения][CreateHCHCIcon]
2. На выноске «Гибридные подключения» щелкните кнопку **Добавить**.
   
    <!-- ![Add a hybrid connnection][CreateHCAddHC]
   -->
3. Откроется выноска **Добавление гибридного подключения** .  Так как это ваше первое гибридное подключение, параметр **Новое гибридное подключение** будет выбран автоматически, и откроется колонка **Создание гибридного подключения**.
   
    ![Создание гибридного подключения][TwinCreateHCBlades]
   
    На **выноске Создание гибридного подключения**:
   
   * в поле **Имя**укажите имя подключения;
   * в поле **Имя узла**введите имя локального компьютера, на котором размещается ваш ресурс;
   * в поле **Порт**введите номер порта, который использует ваш локальный ресурс (1433 для экземпляра SQL Server по умолчанию).
   * Щелкните кнопку **Служба BizTalk**
4. Откроется колонка **Создание службы BizTalk** . Введите имя службы BizTalk и нажмите кнопку **ОК**.
   
    ![Создание службы BizTalk][CreateHCCreateBTS]
   
    Колонка **Создание службы BizTalk** закроется, и вы вернетесь к колонке **Создание гибридного подключения**.
5. На выноске «Создание гибридного подключения» щелкните кнопку **ОК**. 
   
    ![Нажмите кнопку "ОК"][CreateBTScomplete]
6. После завершения процесса в области уведомлений на портале появится сообщение об успешном создании подключения.
   
    <!--- TODO
   
    На этом шаге ничего не получается. I can't create a BizTalk service in the dogfood portal. Я переключился на классический портал (полная версия портала) и создал службу BizTalk, но, кажется, она не позволяет подключить их. По завершении шага создания гибридного подключения открывается сообщение об ошибке "Не удалось создать гибридное подключение RelecIoudHC". Тип ресурса не удалось найти в пространстве имен "Microsoft.BizTaIkServices для API-интерфейса версии 2014-06-01".
   
    The error indicates it couldn't find the type, not the instance.
    ![Уведомление о выполнении][CreateHCSuccessNotification]
    -->
7. Теперь в колонке веб-приложения значок **Гибридные подключения** показывает, что создано одно гибридное подключение.
   
    ![Создано одно гибридное подключение][CreateHCOneConnectionCreated]

На этот момент вы завершили важную часть облачной инфраструктуры гибридных подключений. Далее вы создадите соответствующую локальную часть.

<a name="InstallHCM"></a>

## <a name="install-the-on-premises-hybrid-connection-manager-to-complete-the-connection"></a>Установка локального диспетчера гибридных подключений для завершения подключения
1. В колонке веб-приложения щелкните **Все настройки** > **Сеть** > **Настройка конечных точек гибридного подключения**. 
   
    ![Значок «Гибридные подключения»][HCIcon]
2. В колонке **Гибридные подключения** в столбце **Состояние** для недавно добавленных конечных точек отображается состояние **Не подключено**. Щелкните подключение, чтобы настроить его.
   
    ![Не подключено][NotConnected]
   
    Откроется выноска гибридного подключения.
   
    ![NotConnectedBlade][NotConnectedBlade]
3. Щелкните на выноске **Установка прослушивателя**.
   
    ![Щелкните «Установка прослушивателя»][ClickListenerSetup]
4. Откроется выноска **Свойства гибридного подключения** . В **локальном диспетчере гибридных подключений** выберите **Щелкните, чтобы установить**.
   
    ![Щелкните здесь, чтобы установить][ClickToInstallHCM]
5. В диалоговом окне предупреждения системы безопасности «Запуск приложения» нажмите кнопку **Запустить** , чтобы продолжить.
   
    ![Нажмите кнопку «Запустить», чтобы продолжить][ApplicationRunWarning]
6. В диалоговом окне **Контроль учетных записей пользователей** щелкните **Да**.
   
   ![Щелкните «Да»][UAC]
7. Диспетчер гибридных подключений скачан и установлен. 
   
    ![Установка][HCMInstalling]
8. После завершения установки щелкните кнопку **Закрыть**.
   
    ![Щелкните кнопку «Закрыть»][HCMInstallComplete]
   
    В колонке **Гибридные подключения** в столбце **Состояние** теперь отображается **Подключено**. 
   
    ![Состояние «Подключено»][HCStatusConnected]

Теперь по завершении инфраструктуры гибридных подключений вы можете создать гибридное приложение, которое использует ее. 

> [!NOTE]
> В следующих разделах показано, как использовать гибридное подключение в проекте серверной части мобильных приложений .NET.
> 
> 

## <a name="configure-the-mobile-app-net-backend-project-to-connect-to-the-sql-server-database"></a>Настройка проекта серверной части мобильного приложения .NET для подключения к базе данных сервера SQL Server
В службе приложений проект серверной части мобильного приложения .NET — это просто веб-приложение ASP.NET с установленным и инициализированным дополнительным пакетом SDK для мобильных приложений. Чтобы использовать веб-приложение в качестве серверной части мобильного приложения, необходимо [скачать и инициализировать пакет SDK серверной части .NET для мобильных приложений](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#install-sdk).  

Для мобильных приложений также необходимо определить строку подключения для локальной базы данных и изменить серверную часть для использования этого подключения. 

1. В обозревателе решений Visual Studio откройте файл Web.config для своей серверной части .NET мобильного приложения, найдите раздел **connectionStrings** и добавьте новую запись SqlClient (см. пример ниже), которая указывает на локальную базу данных SQL Server.
   
        <add name="OnPremisesDBConnection"
         connectionString="Data Source=OnPremisesServer,1433;
         Initial Catalog=OnPremisesDB;
         User ID=HybridConnectionLogin;
         Password=<**secure_password**>;
         MultipleActiveResultSets=True"
         providerName="System.Data.SqlClient" />
   
    Не забудьте заменить `<**secure_password**>` в этой строке паролем, созданным для объекта *HbyridConnectionLogin*.
2. Щелкните кнопку **Сохранить** в Visual Studio, чтобы сохранить файл Web.config.
   
   > [!NOTE]
   > Эти параметры подключения используются при запуске на локальном компьютере. При запуске в среде Azure они заменяются строкой подключения, определенной на портале.
   > 
   > 
3. Разверните папку **Модели** и откройте файл модели данных, заканчивающийся на *Context.cs*.
4. Измените конструктор экземпляра **DbContext** таким образом, чтобы он передавал значение `OnPremisesDBConnection` в базовый конструктор **DbContext**, с помощью кода наподобие приведенного ниже фрагмента.
   
        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDBConnection")
            {
            }
        }
   
    Теперь служба будет использовать новую строку подключения для базы данных SQL Server.

## <a name="update-the-mobile-app-backend-to-use-the-on-premises-connection-string"></a>Обновление серверной части мобильного приложения для использования локальной строки подключения
Далее, чтобы использовать новую строку подключения среды Azure, вам нужно добавить в нее параметр приложения.  

1. На [портале Azure](https://portal.azure.com) в коде серверной части веб-приложения для своего мобильного приложения щелкните **Все параметры** и выберите **Параметры приложения**.
2. В колонке **Параметры веб-приложения** прокрутите содержимое до раздела **Строки подключения** и добавьте новую строку подключения **SQL Server** с именем `OnPremisesDBConnection` и значением наподобие `Server=OnPremisesServer,1433;Database=OnPremisesDB;User ID=HybridConnectionsLogin;Password=<**secure_password**>`.
   
    Замените `<**secure_password**>` надежным паролем для локальной базы данных.
   
    ![Строка подключения для локальной базы данных](./media/web-sites-hybrid-connection-get-started/set-sql-server-database-connection.png)
3. Нажмите кнопку **Сохранить** , чтобы сохранить созданное гибридное подключение и строку подключения.

На этом этапе можно повторно опубликовать серверный проект и проверить новое подключение для существующих клиентов мобильных приложений. Данные будут считываться и записываться в локальную базу данных с помощью гибридного подключения.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Дальнейшие действия
* Сведения о создании веб-приложения ASP.NET, которое использует гибридное подключение, см. в разделе [Подключение к локальному SQL Server из веб-приложения, размещенного в службе приложений Azure с помощью гибридных подключений](http://go.microsoft.com/fwlink/?LinkID=397979). 

### <a name="additional-resources"></a>дополнительные ресурсы.
[Обзор гибридных подключений](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Джош Твист (Josh Twist) представляет гибридные подключения (видео Channel 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Веб-сайт гибридных подключений](https://azure.microsoft.com/services/biztalk-services/)

[Службы BizTalk: вкладки "Панель мониторинга", "Монитор", "Масштаб", "Настройка" и "Гибридные подключения"](../biztalk-services/biztalk-dashboard-monitor-scale-tabs.md)

[Создание реального облака с гибридным подключением с помощью простой переносимости приложений (видео Channel 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Подключение к локальному SQL Server с мобильных служб Azure с помощью гибридных подключений (видео Channel 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

## <a name="whats-changed"></a>Изменения
* Руководство по переходу от веб-сайтов к службе приложений см. в статье [Служба приложений Azure и существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

<!-- IMAGES -->
[New]:./media/web-sites-hybrid-connection-get-started/B01New.png
[NewWebsite]:./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
[WebsiteCreationBlade]:./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]:./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-get-started/B05Browse.png
[DefaultWebSitePage]:./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-get-started/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png



<!--HONumber=Feb17_HO3-->


