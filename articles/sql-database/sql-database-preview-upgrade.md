<properties 
	pageTitle="Обновление до последней версии 12 базы данных SQL (предварительная версия)" 
	description="Обновление до последней версии 12 базы данных SQL (предварительная версия)" 
	services="sql-database" 
	documentationCenter="" 
	authors="sonalmm" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-management" 
	ms.date="12/11/2014" 
	ms.author="sonalm"/>



# Обновление до последней версии 12 базы данных SQL (предварительная версия)


[Зарегистрируйтесь](https://portal.azure.com) для получения последней версии базы данных SQL (12), чтобы воспользоваться преимуществами базы данных SQL  нового поколения в Microsoft Azure. Во-первых, вам требуется подписка на Microsoft Azure. Зарегистрируйтесь, чтобы получить [бесплатную пробную версию Azure](http://azure.microsoft.com/pricing/free-trial) и просмотреть информацию о [ценах](http://azure.microsoft.com/pricing/details/sql-database). 

## Локальное обновление сервера с последним обновлением базы данных SQL ##

| Обновление  | Снимок экрана |
| :--- | :--- |
| 1. Войдите на портал [http://portal.azure.com/](http://portal.azure.com/). | ![New Azure Portal][1] |
| 2. Щелкните **Просмотр**. | ![Browse Services][2] |
| 3.	Выберите **SQL servers** (Серверы SQL Server). Отобразится список имен серверов SQL Server. | ![Select SQL Server service][3] |
| 4. Выберите сервер, который необходимо скопировать на новый сервер с включенным обновлением базы данных SQL. | ![Shows a list of SQL Servers][4] |
| 5. Щелкните **Последнее обновление версии 12 базы данных SQL**. | ![Latest preview feature][5] |
| 6. Щелкните **UPGRADE THIS SERVER** (Обновить этот сервер). | ![Upgrades the SQL Server to the preview][6] |

> [AZURE.NOTE] **ВАЖНО**! После выбора параметра обновления для вашего сервера и баз данных, хранящихся на нем, будут включены функции обновления базы данных SQL версии 12, которые невозможно отключить. До версии 12 базы данных SQL можно обновить только серверы с уровнями служб Basic, Standard или Premium. Дополнительную информацию об уровнях служб см. в статье [Обновление баз данных SQL с уровнем служб Web или Business до новых уровней служб](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/).

> **ВАЖНО**! Георепликация не поддерживается в предварительной версии 12 обновления базы данных SQL. Дополнительные сведения см. в статье [Планирование и подготовка к обновлению до предварительной версии базы данных SQL Azure 12](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade).


После выбора параметра **UPGRADE THIS SERVER** (Обновить этот сервер) отобразится колонка с сообщением о процессе проверки. 

- В ходе этого процесса проверяется уровень служб вашей базы данных и состояние георепликации. В колонке отобразятся результаты выполненной проверки. 
- После завершения процесса проверки отобразится список имен баз данных, для которых требуется выполнить действие, необходимое для обновления до версии 12 базы данных SQL. **Потребуется выполнить действия для всех баз данных, чтобы обновить их до версии 12 базы данных SQL**.
- После щелчка по имени каждой базы данных открывается нова колонка с рекомендованной ценовой категорией на основании вашего текущего использования. Кроме того, можно выбрать другую ценовую категорию, соответствующую вашей среде. Для всех обновляемых баз данных необходимо остановить георепликацию. 
- Обратите внимание: рекомендованная ценовая категория не отображается, если недостаточно соответствующих данных. 

| Действие | Снимок экрана |
| :--- | :--- |
| 7. Завершив подготовку сервера к обновлению, введите имя сервера, который необходимо обновить, и нажмите кнопку **OK** (ОК). | ![Confirm the server name to upgrade][7] |
| 8. Будет запущен процесс обновления. Обновление может занять до 24 часов. В течение этого времени остаются доступными все базы данных на сервере, но операции управления сервером и базой данных ограничены. После завершения процесса в колонке сервера отобразится состояние **Enabled** (Включено). | ![Confirms preview features are enabled][8] |
 

# Связанные ссылки  #

-  [Новые возможности версии 12 обновления базы данных SQL (предварительная версия)](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/)
- [Планирование и подготовка к обновлению до последней версии 12 базы данных SQL (предварительная версия)](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)


<!--Image references-->
[1]: ./media/sql-database-preview-upgrade/firstscreenportal.png
[2]: ./media/sql-database-preview-upgrade/browse.png
[3]: ./media/sql-database-preview-upgrade/sqlserver.png
[4]: ./media/sql-database-preview-upgrade/sqlserverlist.png
[5]: ./media/sql-database-preview-upgrade/latestprview.png
[6]: ./media/sql-database-preview-upgrade/upgrade.png
[7]: ./media/sql-database-preview-upgrade/typeservername.png
[8]: ./media/sql-database-preview-upgrade/enabled.png
<!--HONumber=47-->
 