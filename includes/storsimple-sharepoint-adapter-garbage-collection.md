<!--author=SharS last changed: 9/17/15-->

В этой процедуре будут выполнены следующие действия.

1. [Подготовка к запуску исполняемого файла программы обслуживания](#to-prepare-to-run-the-maintainer).

2. [Подготовка базы данных содержимого и корзины к немедленному удалению потерянных больших двоичных объектов](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs).

3. [Запуск Maintainer.exe](#to-run-the-maintainer).

4. [Восстановление базы данных содержимого и параметров корзины](#to-revert-the-content-database-and-recycle-bin-settings).

#### Подготовка к запуску программы обслуживания

1. На интерфейсном веб-сервере откройте консоль управления SharePoint 2013 от имени администратора.

2. Перейдите в папку <boot drive>:\\Program Files\\Microsoft SQL Remote Blob Storage 10.50\\Maintainer.

3. Переименуйте **Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config** в **web.config**.

4. Используйте `aspnet_regiis -pdf connectionStrings` для расшифровки файла web.config.

5. В расшифрованном файле web.config в узле **<connectionStrings>** добавьте строку подключения для вашего экземпляра SQL Server и имя базы данных содержимого. См. указанный ниже пример.

    `<add name=”RBSMaintainerConnectionWSSContent” connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=";Remote Blob Storage Maintainer for WSS_Content";" providerName="System.Data.SqlClient" />`

6. Используйте `aspnet_regiis –pef connectionStrings` для повторного шифрования файла web.config.

7. Переименуйте web.config в Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config.

#### Подготовка базы данных содержимого и корзины для немедленного удаления потерянных больших двоичных объектов

1. На SQL Server в SQL Management Studio выполните следующие запросы на обновление для целевой базы данных содержимого: 

       `use WSS_Content`

       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`

       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`

2. На интерфейсном веб-сервере в разделе **Центр администрирования** измените **Общие параметры веб-приложения** для необходимой базы данных содержимого, чтобы временно отключить корзину. Это действие также очистит корзину для всех связанных коллекций веб-сайтов. Чтобы сделать это, щелкните **Центр администрирования** > **Управление приложением** > **Веб-приложения (управление веб-приложениями)** > **SharePoint - 80** > **Общие параметры приложения**. Для параметра **Состояние корзины** задайте значение **ВЫКЛ.**.

    ![Общие параметры веб-приложения](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### Запуск программы обслуживания

- На интерфейсном веб-сервере в консоли управления SharePoint 2013 запустите программу обслуживания следующим образом:

      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`

    >[AZURE.NOTE]В данный момент для StorSimple поддерживается только операция `GarbageCollection`. Также обратите внимание, что параметры для Microsoft.Data.SqlRemoteBlobs.Maintainer.exe чувствительны к регистру.
 
#### Восстановление базы данных содержимого и параметров корзины

1. На SQL Server в SQL Management Studio выполните следующие запросы на обновление для целевой базы данных содержимого:

      `use WSS_Content`

      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`

      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`

      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`

2. На интерфейсном веб-сервере в разделе **Центр администрирования** измените **Общие параметры веб-приложения** для необходимой базы данных содержимого, чтобы снова включить корзину. Чтобы сделать это, щелкните **Центр администрирования** > **Управление приложением** > **Веб-приложения (управление веб-приложениями)** > **SharePoint - 80** > **Общие параметры приложения**. Для параметра «Состояние корзины» задайте значение **ВКЛ.**.

<!---HONumber=Sept15_HO4-->