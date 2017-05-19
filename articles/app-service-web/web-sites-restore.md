---
title: "Восстановление приложения в Azure"
description: "Узнайте, как восстановить приложение из резервной копии."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 49aa35a42e4f6dab2f8d556f7b1b10bfdef7b7db
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="restore-an-app-in-azure"></a>Восстановление приложения в Azure
В этой статье описывается, как в [службе приложений Azure](../app-service/app-service-value-prop-what-is.md) восстановить приложение, для которого ранее была создана резервная копия (ознакомьтесь с [архивацией приложения в Azure](web-sites-backup.md)). Вы можете восстановить предыдущее состояние приложения и связанных с ним баз данных по запросу или создать новое приложение на основе одной из резервных копий исходного приложения. Служба приложений Azure поддерживает следующие базы данных для архивации и восстановления:
- [База данных SQL](https://azure.microsoft.com/en-us/services/sql-database/)
- [база данных Azure для MySQL (предварительная версия)](https://azure.microsoft.com/en-us/services/mysql);
- [база данных Azure для PostgreSQL (предварительная версия)](https://azure.microsoft.com/en-us/services/postgres);
- [ClearDB MySQL](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/SuccessBricksInc.ClearDBMySQLDatabase?tab=Overview);
- [MySQL в приложении](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app).

Восстановление из резервных копий доступно для приложений, выполняемых на уровнях **Стандартный** и **Премиум**. Дополнительные сведения об увеличении масштаба приложения см. в статье [Увеличение масштаба приложения в Azure](web-sites-scale.md). Уровень **Премиум** позволяет создавать большее количество ежедневных резервных копий по сравнению с уровнем **Стандартный**.

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Восстановление приложения из существующей резервной копии
1. На портале Azure в колонке **Параметры** своего приложения щелкните **Резервные копии**. Откроется колонка **Резервные копии**. Затем щелкните **Восстановить сейчас** на панели команд.
   
    ![Выбор команды "Восстановить сейчас"][ChooseRestoreNow]
2. В колонке **Восстановить** выберите источник резервного копирования.
   
    ![](./media/web-sites-restore/021ChooseSource.png)
   
    Параметр **Архивная копия приложения** отображает все существующие резервные копии текущего приложения, позволяя легко выбрать одну из них.
    Параметр **Служба хранилища** позволяет выбрать ZIP-файл любой резервной копии из любых имеющихся учетной записи хранения Azure и контейнера в вашей подписке.
    Если вы пытаетесь восстановить резервную копию другого приложения, используйте параметр **Служба хранилища** .
3. После этого укажите место назначения для восстановления приложения в разделе **Место назначения для восстановления**.
   
    ![](./media/web-sites-restore/022ChooseDestination.png)
   
   > [!WARNING]
   > Если вы выберете **Перезаписать**, то все существующие данные в текущем приложении будут удалены и перезаписаны. Прежде чем нажимать кнопку **ОК**, убедитесь, что это именно то, что вы хотите сделать.
   > 
   > 
   
    Вы также можете выбрать **Существующее приложение** для восстановления резервной копии приложения в другое приложение в той же группе ресурсов. Прежде чем использовать этот параметр, необходимо создать другое приложение в группе ресурсов с конфигурацией базы данных, аналогичной конфигурации в резервной копии приложения.

4. Нажмите кнопку **ОК**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Скачивание и удаление резервной копии в учетной записи хранения
1. На портале Azure в колонке **Обзор** выберите **Учетные записи хранения**. Откроется список существующих учетных записей хранения.
2. Выберите учетную запись, в которой хранится резервная копия, которую вы хотите скачать или удалить. Откроется колонка для учетной записи хранения.
3. В ней выберите нужный контейнер.
   
    ![Просмотр контейнеров][ViewContainers]
4. Выберите файл резервной копии, который вы хотите скачать или удалить.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. Щелкните **Скачать** или **Удалить**, в зависимости от того, что нужно сделать.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Мониторинг операции восстановления
Чтобы просмотреть сведения о результате восстановления приложения (успешном или неудачном), перейдите к колонке **Журнал действий** на портале Azure.  
 

Прокрутите колонку вниз, чтобы найти нужную операцию восстановления, и выберите ее щелчком.

В колонке сведений отобразится вся доступная информация о выбранной операции восстановления.

## <a name="next-steps"></a>Дальнейшие действия
Для архивации и восстановления приложений службы приложений можно использовать REST API (ознакомьтесь со статьей [Использование REST для резервного копирования и восстановления приложений службы приложений](websites-csm-backup.md)).


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebSite]: ./media/web-sites-restore/09RestoredContosoWebSite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png

