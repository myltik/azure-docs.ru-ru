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

Руководство, представленное в этой статье, поможет быстро перейти к использованию службы фабрики данных Azure. В этом учебнике будет создана фабрика данных Azure и конвейер в фабрике данных, который будет выполнять копирование данных из хранилища больших двоичных объектов Azure в базу данных SQL Azure.

> [AZURE.NOTE]Подробный обзор службы фабрики данных см. в статье [Введение в фабрику данных Azure][data-factory-introduction].

##Предварительные требования для прохождения этого учебника
Перед началом работы с этим учебником необходимо иметь следующее:

- **Подписка Azure**. Если у вас нет подписки, вы можете создать бесплатную пробную версию учетной записи всего за несколько минут. Дополнительные сведения см. в статье [Бесплатная пробная версия][azure-free-trial].
- **Учетная запись хранения Azure** В этом учебнике будет использоваться хранилище больших двоичных объектов в качестве **исходного** хранилища данных. Если у вас нет учетной записи хранения Azure, инструкции по ее созданию см. в статье [Создание учетной записи хранения][data-factory-create-storage].
- **База данных SQL Azure**. В этом учебнике будет использоваться база данных SQL Azure в качестве **конечного** хранилища данных. Если нет базы данных SQL Azure, которую можно использовать для изучения этого учебника, см. раздел [Как создать и настроить базу данных SQL Azure][data-factory-create-sql-database], чтобы создать такую базу данных.
- **SQL Server 2012/2014 или Visual Studio 2013**. Вы будете использовать SQL Server Management Studio или Visual Studio для создания образца базы данных и просмотра итоговых данных в базе данных.  

### Приготовьте имя и ключ учетной записи для своей учетной записи хранения Azure.
Имя и ключ вашей учетной записи хранения Azure потребуются в ходе изучения этого учебника. Запишите **имя** и **ключ** вашей учетной записи хранения Azure, следуя инструкциям ниже.

1. Войдите на [портал предварительной версии Azure][azure-preview-portal].
2. Щелкните раздел **ОБЗОР** слева и выберите **Учетные записи хранения**.
3. В колонке **Учетные записи хранения** выберите **учетную запись хранения Azure**, которая будет использоваться в ходе изучения этого учебника.
4. В колонке **ХРАНИЛИЩЕ** щелкните плитку **КЛЮЧИ**.
5. В колонке **Управление ключами** нажмите кнопку **копировать** (изображение) рядом с текстовым полем **ИМЯ УЧЕТНОЙ ЗАПИСИ ХРАНЕНИЯ** и сохраните и вставьте его куда-нибудь (например, в текстовый файл).  
6. Повторите предыдущий шаг, чтобы скопировать или записать **ПЕРВИЧНЫЙ КЛЮЧ ДОСТУПА**.
7. Закройте все колонки, нажав **X**.

### Приготовьте имя сервера, имя базы данных и учетную запись пользователя для базы данных SQL Azure
Вам потребуются имена сервера Azure SQL Server, базы данных и пользователя в ходе изучения этого учебника. Запишите имена **сервера**, **базы данных** и **пользователя** базы данных SQL Azure, следуя инструкциям ниже.

1. На **портале предварительной версии Azure** щелкните **ОБЗОР** слева и выберите **Базы данных SQL**.
2. В **колонке баз данных SQL** выберите **базу данных**, которую вы планируете использовать для этого учебника. Запишите **имя базы данных**.  
3. В колонке **БАЗА ДАННЫХ SQL** щелкните плитку **СВОЙСТВА**.
4. Запишите значения параметров **ИМЯ СЕРВЕРА** и **ИМЯ ВХОДА АДМИНИСТРАТОРА СЕРВЕРА**.
5. Закройте все колонки, нажав **X**.

### Разрешите службам Azure доступ к серверу Azure SQL Server
Убедитесь, что настройка **Разрешить доступ к службам Azure** имеет состояние **ВКЛ** для вашего сервера Azure SQL Server, чтобы служба фабрики данных могла иметь доступ к серверу Azure SQL Server. Чтобы проверить и при необходимости включить этот параметр, выполните следующие действия.

1. Щелкните раздел **ОБЗОР** слева и выберите пункт **Серверы SQL**.
2. Выберите **свой сервер** и нажмите **ПАРАМЕТРЫ** в колонке **SQL SERVER**.
3. В колонке **ПАРАМЕТРЫ** щелкните пункт **Брандмауэр**.
4. В колонке **Параметры брандмауэра** щелкните **ВКЛ** для параметра **Разрешить доступ к службам Azure**.
5. Закройте все колонки, нажав **X**.

### Подготовка хранилища больших двоичных объектов Azure и базы данных SQL Azure для учебника
Теперь подготовьте хранилище больших двоичных объектов Azure и базу данных SQL Azure к изучению этого учебника, выполнив следующие действия.

1. Запустите блокнот, вставьте следующий текст и сохраните файл на жестком диске под именем **emp.txt** в папке **C:\ADFGetStarted**.

        John, Doe
		Jane, Doe

2. При помощи таких средств, как [обозреватель хранилища Azure](https://azurestorageexplorer.codeplex.com/), создайте контейнер **adftutorial** и загрузите файл **emp.txt** в этот контейнер.

    ![Обозреватель хранилищ Azure][image-data-factory-get-started-storage-explorer]
3. Используйте следующий скрипт SQL, чтобы создать таблицу **emp** в базе данных SQL Azure.  


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**Если на вашем компьютере установлен SQL Server 2012/2014**, следуйте инструкциям в статье [Шаг 2. Подключение к базе данных SQL управляющей базы данных SQL Azure при помощи SQL Server Management Studio][sql-management-studio] для подключения к серверу Azure SQL Server и запуска скрипта SQL. Обратите внимание, что в этой статье используется портал управления выпусками (http://manage.windowsazure.com), а не портал предварительных версий (http://portal.azure.com) для настройки брандмауэра сервера Azure SQL Server.

	**Если на компьютере установлено программное обеспечение Visual Studio 2013**, на [портале предварительной версии Azure](http://portal.azure.com) щелкните концентратор **ПРОСМОТР** слева, щелкните **Серверы SQL**, выберите свою базу данных и нажмите кнопку **Открыть в Visual Studio** на панели инструментов, чтобы подключиться к серверу Azure SQL Server и запустить скрипт. Если клиенту не разрешен доступ к серверу Azure SQL Server, следует настроить брандмауэр вашего сервера Azure SQL Server, чтобы разрешить доступ с вашей машины (IP-адрес). См. статью выше, чтобы ознакомиться с шагами по настройке брандмауэра для сервера Azure SQL Server.


Выполните следующие действия.

- Щелкните ссылку [Использование редактора фабрики данных](data-factory-get-started-using-editor.md) вверху, чтобы пройти учебник, используя редактор фабрики данных, который является компонентом портала Azure.
- Щелкните ссылку [Использование PowerShell](data-factory-monitor-manage-using-powershell.md) вверху, чтобы пройти учебник с помощью Azure PowerShell.


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
 

<!---HONumber=62-->