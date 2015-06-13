<properties
	pageTitle="Приступая к работе с фабрикой данных Azure"
	description="В этом учебнике показано, как создать пример конвейера данных, который копирует данные из большого двоичного объекта в экземпляр базы данных SQL Azure."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article" 
	ms.date="05/04/2015"
	ms.author="spelluru"/>

# Начало работы с фабрикой данных Azure
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)

Учебник, в этой статье поможет быстро приступить к работе с использованием службы фабрики данных Azure. В этом учебнике будет создать фабрику данных Azure и создать конвейер в фабрике данных для копирования данных из хранилища Azure blob в базу данных Azure SQL.

> [AZURE.NOTE]Подробный обзор службы фабрики данных см. [Введение в Azure данные фабрики][data-factory-introduction] статьи.

##Необходимые условия для учебника
Перед началом работы с этим учебником необходимо иметь следующее:

- **Подписка Azure**. Если нет подписки, можно создать бесплатную пробную учетную запись всего за несколько минут. В разделе [бесплатной пробной версии][azure-free-trial] Дополнительные сведения см.
- **Учетной записи хранилища azure**. Будет использовать хранилище BLOB-объектов как **источника** хранилище данных в этом учебнике. Если нет учетной записи хранилища Azure, см. [Создать учетную запись хранилища][data-factory-create-storage] статьи для действия, чтобы создать его.
- **База данных azure SQL**. Будет использовать базу данных Azure SQL как **назначения** хранилище данных в этом учебнике. При отсутствии в базе данных Azure SQL, можно использовать в этом учебнике, см. в разделе [как создать и настроить базу данных SQL Azure][data-factory-create-sql-database] для его создания.
- **SQL Server 2012 и 2014 г. или Visual Studio 2013**. SQL Server Management Studio или Visual Studio будет использовать для создания образца базы данных и просмотр результатов в базе данных.  

### Сбор данных, имя учетной записи и ключа учетной записи для вашей учетной записи хранилища Azure
Потребуется имя учетной записи и ключа учетной записи из вашей учетной записи хранилища Azure для прохождения данного учебника. Запишите **имя учетной записи** и **ключ учетной записи** для вашей учетной записи хранилища Azure, следуя приведенным ниже инструкциям:

1. Имя входа для [предварительную версию портала Azure][azure-preview-portal].
2. Щелкните **Обзор** концентратора слева и выберите **учетных записей хранилища**.
3. В **учетных записей хранилища** выноску, выберите **учетной записи хранилища Azure** которую требуется использовать в этом учебнике.
4. В **ХРАНЕНИЯ** стоечный модуль, щелкните **КЛЮЧЕЙ** плитки.
5. В **Управление ключами** стоечный модуль, щелкните **копирования** кнопка (изображение) рядом с **имя учетной записи ХРАНИЛИЩА** текста поле, сохранение и вставка его где-нибудь (например: в текстовом файле).  
6. Повторите предыдущий шаг, чтобы скопировать или запишите **первичный ключ доступа**.
7. Закройте все модули, щелкнув **X**.

### Сбор данных, имя сервера, имя базы данных и учетной записи пользователя базы данных Azure SQL
Необходимо будет имена сервера, базы данных и пользователей для прохождения данного учебника Azure SQL. Запишите имена **сервера**, **базы данных**, и **пользователя** базы данных Azure SQL, следуя приведенным ниже инструкциям:

1. В **предварительной версии портала Azure**, щелкните **Обзор** слева и выберите **баз данных SQL**.
2. В **выноску баз данных SQL**, выберите **базы данных** которую требуется использовать в этом учебнике. Запишите **имя базы данных**.  
3. В **базы данных SQL** стоечный модуль, щелкните **Свойства** плитки.
4. Запишите значения **имя сервера** и **имя входа АДМИНИСТРАТОРА сервера**.
5. Закройте все модули, щелкнув **X**.

### Разрешить службам Azure доступ к серверу Azure SQL
Убедитесь, что **Разрешить доступ к службам Azure** параметр выключен **ON** для сервера Azure SQL, чтобы службе фабрика данных можно получить доступ к серверу Azure SQL. Чтобы проверить и включите этот параметр, выполните следующие действия:

1. Щелкните **Обзор** концентратора слева, затем щелкните **серверов SQL Server**.
2. Выберите **сервера**, и нажмите кнопку **Параметры** на **SQL SERVER** выноски.
3. В **Параметры** стоечный модуль, щелкните **брандмауэра**.
4. В **параметры брандмауэра** стоечный модуль, щелкните **ON** для **Разрешить доступ к службам Azure**.
5. Закройте все модули, щелкнув **X**.

### Подготовка хранилища больших двоичных объектов Azure и базы данных SQL Azure для учебника
Теперь Подготовьте хранилище больших двоичных объектов и базы данных Azure SQL для учебника, выполнив следующие действия:

1. Запустите Блокнот и вставьте следующий текст сохраняется как **emp.txt** для **C:\ADFGetStarted** папку на жестком диске.

        John, Doe
		Jane, Doe

2. Использовать средства, такие как [обозревателя хранилищ Azure](https://azurestorageexplorer.codeplex.com/) для создания **adftutorial** контейнера и передачи **emp.txt** файла в контейнер.

    ![Обозреватель хранилищ Azure][image-data-factory-get-started-storage-explorer]
3. Используйте следующий сценарий SQL для создания **emp** таблицы в базе данных SQL Azure.  


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**SQL Server 2012/2014 на компьютере при наличии:** следуйте инструкциям из [Шаг 2: подключение к базе данных SQL, управление базы данных SQL Azure с помощью SQL Server Management Studio][sql-management-studio] статьи для подключения к серверу Azure SQL и запустите сценарий SQL. Обратите внимание, что в этой статье используется портал управления выпуска (http://manage.windowsazure.com), не в предварительной версии портала (http://portal.azure.com), для настройки брандмауэра для сервера Azure SQL.

	**При наличии на компьютере установлена Visual Studio 2013:** в [предварительной версии портала Azure](http://portal.azure.com), щелкните **Обзор** концентратора на левый, нажмите кнопку **серверов SQL Server**, выберите базу данных и нажмите кнопку **в среде Visual Studio** кнопки на панели инструментов для подключения к серверу Azure SQL и запустите сценарий. Если ваш клиент не может получить доступ к серверу Azure SQL, необходимо настроить брандмауэр разрешить доступ с компьютера (IP-адрес) сервера Azure SQL. См. в статье выше шаги по настройке брандмауэра для сервера Azure SQL.


Выполните следующие действия.

- Щелкните [с помощью редактора данных фабрики](data-factory-get-started-using-editor.md) в верхней для выполнения учебника с помощью редактора данных фабрики, который является частью портала Azure.
- Щелкните [с помощью PowerShell](data-factory-monitor-manage-using-powershell.md) в верхней для выполнения учебника с помощью Azure PowerShell.


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[data-factory-editor]: data-factory-editor.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[copy-activity]: data-factory-copy-activity.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database-create-configure.md


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-json]: ./media/data-factory-get-started/getstarted-editor-blob-storage-json.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-startboard]: ./media/data-factory-get-started/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started/getstarted-data-factory-not-available.png

<!---HONumber=GIT-SubDir--> 