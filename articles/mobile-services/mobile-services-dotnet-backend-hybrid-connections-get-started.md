<properties
	pageTitle="Подключение к локальному SQL Server из серверной мобильной службы .NET с использованием гибридных подключений | Мобильные службы Azure"
	description="Узнайте, как подключиться к локальному SQL Server из серверной мобильной службы .NET с помощью гибридных подключений Azure"
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="03/05/2016"
	ms.author="glenga"/>


# Соединение мобильных служб Azure с локальным сервером SQL Server с помощью гибридных подключений

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Аналогичные сведения для мобильных приложений см. в разделе [Доступ к локальным ресурсам с помощью гибридных подключений в службе приложений Azure](../app-service-web/web-sites-hybrid-connection-get-started.md).

При переходе на облачную платформу предприятию не всегда удается сразу перенести все свои ресурсы в среду Azure. С помощью гибридных подключений можно устанавливать защищенные соединения из мобильных служб Azure с локальными ресурсами. Благодаря этому ваши локальные данные будут доступны мобильным клиентам, использующим Azure. К поддерживаемым активам относятся любые ресурсы, работающие на статическом TCP-порту, в том числе Microsoft SQL Server, MySQL, веб-API HTTP и большинство настраиваемых веб-служб. Гибридные подключения используют авторизацию на основе подписанного URL-адреса (SAS), чтобы гарантировать безопасность подключений из службы мобильных приложений и локального диспетчера гибридных подключений к гибридному подключению. Подробнее см. [обзоре гибридных подключений](../biztalk-services/integration-hybrid-connection-overview.md).

В настоящем руководстве мы покажем, как изменять внутреннюю мобильную службу .NET, чтобы она использовала локальную базу данных SQL Server вместо базы данных SQL Azure по умолчанию, подготовленной с помощью вашей службы. Гибридные подключения также поддерживаются для серверной мобильной службы JavaScript (подробные сведения см. [в этой статье](http://blogs.msdn.com/b/azuremobile/archive/2014/05/12/connecting-to-an-external-database-with-node-js-backend-in-azure-mobile-services.aspx)).

##Предварительные требования##

Для работы с данным учебником необходимы перечисленные ниже компоненты и сведения.

- **Серверная мобильная служба .NET**. <br/>Выполните инструкции в учебнике [Приступая к работе с мобильными службами], чтобы создать и загрузить новую серверную мобильную службу .NET с [классического портала Azure].

[AZURE.INCLUDE [hybrid-connections-prerequisites](../../includes/hybrid-connections-prerequisites.md)]

## Установка экспресс-выпуска SQL Server, включение TCP/IP и создание локальной базы данных SQL Server

[AZURE.INCLUDE [hybrid-connections-create-on-premises-database](../../includes/hybrid-connections-create-on-premises-database.md)]

## Создание гибридного подключения

[AZURE.INCLUDE [hybrid-connections-create-new](../../includes/hybrid-connections-create-new.md)]

## Установка локального диспетчера гибридных подключений для завершения подключения

[AZURE.INCLUDE [hybrid-connections-install-connection-manager](../../includes/hybrid-connections-install-connection-manager.md)]

## Настройка проекта мобильной службы для подключения к базе данных SQL Server

На этом этапе вы задаете строку подключения к локальной базе данных, после чего настраиваете мобильную службу, чтобы она использовала это подключение.

1. В Visual Studio 2013 откройте проект, определяющий серверную мобильную службу .NET.

	Сведения о загрузке серверного проекта .NET см. в разделе [Приступая к работе с мобильными службами](mobile-services-dotnet-backend-windows-store-dotnet-get-started.md)

2. В обозревателе решений откройте файл Web.config, найдите раздел **connectionStrings** и добавьте новую запись SqlClient (см. пример ниже), которая указывает на локальную базу данных SQL Server:

	    <add name="OnPremisesDBConnection"
         connectionString="Data Source=OnPremisesServer,1433;
         Initial Catalog=OnPremisesDB;
         User ID=HybridConnectionLogin;
         Password=<**secure_password**>;
         MultipleActiveResultSets=True"
         providerName="System.Data.SqlClient" />

	Не забудьте заменить `<**secure_password**>` в этой строке на пароль, созданный для объекта *HbyridConnectionLogin*.

3. Щелкните кнопку **Сохранить** в Visual Studio, чтобы сохранить файл Web.config.

	> [AZURE.NOTE]Эти параметры подключения используются при запуске на локальном компьютере. При запуске в среде Azure они заменяются строкой подключения, определенной на портале.

4. Разверните папку **Модели** и откройте файл модели данных, заканчивающийся на *Context.cs*.

6. Измените конструктор экземпляра **DbContext** таким образом, чтобы он передавал значение `OnPremisesDBConnection` в базовый конструктор **DbContext**, с помощью кода наподобие следующего:

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDBConnection")
            {
            }
        }

	Теперь служба будет использовать новую строку подключения для базы данных SQL Server.

##Локальная проверка подключения к базе данных

Перед публикацией в среде Azure и использованием гибридного подключения рекомендуется проверить подключение к базе данных на локальном компьютере. Это упростит поиск и устранение неполадок подключения перед повторной публикацией и использованием гибридного подключения.

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]

## Внесение изменений в среду Azure для использования локальной строки подключения

После проверки подключения к базе данных необходимо добавить для новой строки подключения параметр приложения, чтобы ее можно было использовать из среды Azure, и опубликовать мобильную службу в Azure.

1. Перейдите к своей мобильной службе на [классическом портале Azure].

1. Откройте вкладку **Настройка** и найдите раздел **Строки подключения**.

	![Строка подключения для локальной базы данных](./media/mobile-services-dotnet-backend-hybrid-connections-get-started/11.png)

2. Добавьте новую строку подключения к **SQL Server** с именем `OnPremisesDBConnection` и значением, аналогичным этому:

		Server=OnPremisesServer,1433;Database=OnPremisesDB;User ID=HybridConnectionsLogin;Password=<**secure_password**>


	Замените `<**secure_password**>` на надежный пароль для *HybridConnectionLogin*.

2. Нажмите кнопку **Сохранить**, чтобы сохранить созданное гибридное подключение и строку подключения.

3. Используя Visual Studio, опубликуйте обновленный проект мобильной службы в Azure.

	Откроется страница запуска службы.

4. Нажмите кнопку **Попробовать сейчас** на начальной странице или используйте клиентское приложение, подключенное к вашей мобильной службе, чтобы выполнить несколько операций для внесения изменений в базу данных.

	>[AZURE.NOTE]При вызове страниц API справки с помощью кнопки **Попробовать сейчас** не забудьте указать в качестве пароля (с пустым именем пользователя) свой ключ приложения.

4. В SQL Server Management Studio подключитесь к своему экземпляру SQL Server, а затем в обозревателе объектов разверните базу данных **OnPremisesDB** и узел **Таблицы**.

5. Щелкните правой кнопкой мыши таблицу **hybridService1.TodoItems** и выберите команду **Выбрать первые 1000 строк**, чтобы просмотреть результаты.

	Обратите внимание, что изменения, созданные в вашем приложении, были сохранены мобильной службой в локальной базе данных с помощью гибридного подключения.

##См. также##

+ [Веб-сайт гибридных подключений](https://azure.microsoft.com/services/biztalk-services/)
+ [Обзор гибридных подключений](../biztalk-services/integration-hybrid-connection-overview.md)
+ [Службы BizTalk: вкладки "Панель мониторинга", "Монитор", "Масштаб", "Настройка" и "Гибридные подключения"](../biztalk-services/biztalk-dashboard-monitor-scale-tabs.md)
+ [Изменение модели данных в серверной мобильной службе для .NET](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)

<!-- IMAGES -->


<!-- Links -->
[классического портала Azure]: http://manage.windowsazure.com
[классическом портале Azure]: http://manage.windowsazure.com
[Приступая к работе с мобильными службами]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md

<!---HONumber=AcomDC_0504_2016-->