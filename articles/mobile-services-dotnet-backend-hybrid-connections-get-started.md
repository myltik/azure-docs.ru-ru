<properties pageTitle="Подключение к локальному SQL Server из мобильной службы Azure с использованием гибридных подключений - мобильные службы Azure" description="Узнайте, как подключиться к локальному SQL Server из мобильной службы Azure с помощью гибридных подключений" services="" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor="mollybos"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/22/2014" ms.author="glenga"/>

  
# Подключение к локальному SQL Server из мобильной службы Azure с помощью гибридных подключений 

Когда предприятие переходит в облако, часто требуется оставить некоторые активы в локальной среде по техническим причинам, для соответствия требованиям или по соображениям безопасности. Мобильные службы позволяют легко создавать над этими активами уровень мобильности с размещением в облаке, с сохранением безопасного локального подключения к ним с помощью гибридных подключений. Поддерживаемые активы включают любые ресурсы, работающие в статическом TCP-порте, в том числе Microsoft SQL Server, MySQL, веб-API HTTP и большинство настраиваемых веб-служб. 

В настоящем руководстве мы покажем, как изменять внутреннюю мобильную службу .NET, чтобы она использовала локальную базу данных SQL Server вместо базы данных SQL Azure по умолчанию, подготовленной с помощью вашей службы. Поскольку гибридные подключения поддерживаются для внутренних мобильных служб JavaScript, в этом разделе рассматриваются только внутренние мобильные службы .NET.

В этом разделе рассматриваются следующие основные этапы.

1. [Необходимые условия](#Prerequisites)
2. [Установка экспресс-выпуска SQL Server, включение TCP/IP и создание локальной базы данных SQL Server](#InstallSQL)
3. [Создание гибридного подключения](#CreateHC)
4. [Установка локального диспетчера гибридных подключений для завершения подключения](#InstallHCM)
5. [Изменение мобильной службы для использования этого подключения](#CreateService)

<a name="Prerequisites"></a>
##Необходимые компоненты
Для работы с этим учебником вам потребуются следующие продукты. У всех этих продуктов есть бесплатные версии, так что можно начать разработку для Azure полностью бесплатно.

- **Visual Studio 2013** - о загрузке бесплатной пробной версии см. на странице [Загружаемые файлы для Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs). Прежде чем продолжить, установите этот продукт.

- **SQL Server 2014, экспресс-выпуск с инструментами** - загрузите экспресс-выпуск Microsoft SQL Server бесплатно на [странице базы данных веб-платформы Майкрософт](http://www.microsoft.com/web/platform/database.aspx). Выберите **экспресс-выпуск** (не LocalDB). Версия **экспресс-выпуск с инструментами** включает в себя продукт SQL Server Management Studio, который будет использоваться в этом учебнике.

Также потребуется локальный компьютер, который будет подключаться к Azure с помощью гибридных подключений. Этот компьютер должен соответствовать следующим условиям:

- Иметь возможность подключения к Azure через порт 5671.
- Иметь возможность получить доступ к *hostname*:*portnumber* для локального ресурса, к которому необходимо подключиться. Этот ресурс может размещаться как на этой же машине, так и на другой. 

<a name="InstallSQL"></a>
## Установка SQL Server Express, включение TCP/IP и создание локальной базы данных SQL Server

Для использования локальной базы данных SQL Server или SQL Server Express с помощью гибридного подключения в статическом порте должен быть включен протокол TCP/IP. Экземпляры SQL Server по умолчанию используют статический порт 1433, а именованные экземпляры не используют этот порт. 

Подробные инструкции по настройке SQL Server, удовлетворяющей описанным выше условиям, см. в разделе [Установка экспресс-выпуска SQL Server, включение TCP/IP и создание локальной базы данных SQL Server](/ru-ru/documentation/articles/web-sites-hybrid-connection-connect-on-premises-sql-server#InstallSQL). Если система SQL Server уже установлена с такой конфигурацией и в той среде, которые удовлетворяют описанным выше условиям, можно сразу перейти к этапу [Создание локальной базы данных SQL Server](/ru-ru/documentation/articles/web-sites-hybrid-connection-connect-on-premises-sql-server#CreateSQLDB). 

В рамках данного учебника мы будем считать, что база данных имеет имя **OnPremisesDB**, запущена через порт **1433**, а имя узла компьютера - **onPremisesServer**.

<a name="CreateHC"></a>
## Создание гибридного подключения
1. С локального компьютера выполните вход на [портал управления Azure](http://go.microsoft.com/fwlink/p/?linkid=213885&clcid=0x409).

2. В нижней части области навигации нажмите кнопку **+СОЗДАТЬ** и последовательно выберите пункты **Службы приложений**, **Служба BizTalk** и **Настраиваемое создание**.

	![Create BizTalk Service][CreateBTS]

3. Укажите **Имя службы BizTalk** и выберите **Выпуск**. 

	![Configure new BizTalk Service][ConfigureBTS]

	В этом учебнике используется значение **mobile1**. Необходимо предоставить уникальное имя для новой службы BizTalk.

4. После создания службы BizTalk перейдите на вкладку **Гибридные подключения** и нажмите кнопку **Добавить**.

	![Add Hybrid Connection][AddHC]

	Будет создано новое гибридное подключение.

5. Укажите **Имя** и **Имя узла** для этого гибридного подключения и установите в поле **Порт** значение `1433`. 
  
	![Configure Hybrid Connection][ConfigureHC]

	Имя узла - это имя локального сервера. Таким образом гибридное подключение настраивается для доступа к SQL Server, работающем в порте 1433.

6. После создания нового подключения оно появляется в следующей таблице. 
 
	![Hybrid Connection successfully created][HCCreated]
	
	В качестве состояния нового подключения отображается **Локальная установка не завершена**.

Теперь необходимо установить диспетчер гибридных подключений на локальном компьютере.

<a name="InstallHCM"></a>
## Установка локального диспетчера гибридных подключений для завершения подключения

Диспетчер гибридных подключений позволяет локальному компьютеру подключаться к Azure и ретранслировать трафик TCP. Это программное обеспечение необходимо устанавливать на локальном компьютере, к которому выполняется попытка доступа из Azure.

1. Выберите только что созданное подключение и на нижней панели выберите элемент **Локальная установка**.

	![On-Premises Setup][DownloadHCM]

4. Щелкните элемент **Установить и настроить**.

	![Install Hybrid Connection Manager][InstallHCM]

	Таким образом будет установлен пользовательский экземпляр диспетчера подключений, уже предварительно настроенный для работы с только что созданным гибридным подключением.

3. Выполните оставшиеся действия по установке диспетчера подключений.

	![Hybrid Connection Manager setup][HCMSetup]

	После завершения установки состояние гибридного подключения изменяется на **1 экземпляр подключен**. Может потребоваться обновить браузер и подождать несколько минут. Теперь локальная установка завершена.

	![Hybrid Connection connected][HCConnected]

<a name="CreateService"></a>
## Изменение мобильной службы для использования этого подключения
### Сопоставление гибридного подключения со службой
1. На вкладке **Мобильные службы** портала выберите существующую мобильную службу или создайте новую. 

	>[AZURE.NOTE]Необходимо либо выбрать службу, созданную с помощью серверной части .NET, либо создать новую серверную мобильную службу .NET. Сведения о создании новой серверной мобильной службы .NET см. в разделе [Приступая к работе с мобильными службами](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/) 

2. На вкладке **Настройка** для мобильной службы найдите раздел **Гибридные подключения** и выберите **Добавить гибридное подключение**.

	![Associate Hybrid Connection][AssociateHC]

3. Выберите только что созданное гибридное подключение на вкладке служб BizTalk и нажмите кнопку **ОК**. 

	![Pick associated Hybrid Connection][PickHC]

Теперь мобильная служба сопоставлена с новым гибридным подключением.

### Обновление службы для использования локальной строки подключения
Наконец, необходимо создать параметр приложения для хранения значения строки подключения к локальному SQL Server. Затем нужно будет изменить мобильную службу, чтобы она использовала эту строку подключения. 

1. На вкладке **Настройка** в окне **Строки подключения** добавьте новую строку подключения с именем `OnPremisesDatabase` и значением вида `Server=onPremisesServer,1433;Database=OnPremisesDB;User ID=sa;Password={password}`.

	![Connection string for on-premises database][ConnectionString]

	Замените `{password}` безопасным паролем для локальной базы данных.

2. Нажмите кнопку **Сохранить**, чтобы сохранить созданное гибридное подключение и строку подключения.

3. В Visual Studio 2013 откройте проект, определяющий вашу мобильную службу на основе .NET. 

	Сведения о загрузке серверного проекта .NET см. в разделе [Приступая к работе с мобильными службами](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/) .
 
4. В обозревателе решений разверните папку **Модели** и откройте файл модели данных, заканчивающийся на *Context.cs*.

6. Измените конструктор экземпляра **DbContext**, чтобы он был аналогичен следующему фрагменту кода:

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDatabase")
            {
            }
        
            // snipped
        }

	Служба будет использовать новую строку гибридного подключения, заданную в Azure.

5. Опубликуйте внесенные изменения и используйте клиентское приложение, подключенное к мобильной службе, для вызова некоторых операций, создающих изменения в базе данных.

6. Откройте SQL Management Studio на локальном компьютере, на котором работает SQL Server, а затем в обозревателе объектов разверните базу данных **OnPremisesDB** и узел **Таблицы**. 

9. Щелкните правой кнопкой мыши таблицу **hybridService1.TodoItems** и выберите команду **Выбрать первые 1000 строк**, чтобы просмотреть результаты.

	![SQL Management Studio][SMS]

Изменения, созданные в приложении, были отправлены мобильной службой в локальную базу данных.

##См. также
 
+ [Веб-сайт гибридных подключений](http://azure.microsoft.com/ru-ru/services/biztalk-services/)
+ [Обзор гибридных подключений](http://go.microsoft.com/fwlink/p/?LinkID=397274)
+ [Службы BizTalk: вкладки "Панель мониторинга", "Монитор", "Масштаб", "Настройка" и "Гибридные подключения"](http://azure.microsoft.com/ru-ru/documentation/articles/biztalk-dashboard-monitor-scale-tabs/)

<!-- IMAGES -->
 
[CreateBTS]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/1.png
[ConfigureBTS]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/2.png
[AddHC]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/3.png
[ConfigureHC]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/4.png
[HCCreated]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5.png
[InstallHCM]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/6.png
[HCMSetup]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/7.png
[HCConnected]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/8.png
[AssociateHC]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/9.png
[PickHC]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/10.png
[ConnectionString]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/11.png
[SMS]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/12.png
[DownloadHCM]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5-1.png


<!--HONumber=42-->
