<properties 
	pageTitle="Создание базы данных в последней версии 12 обновления базы данных SQL (предварительная версия)" 
	description="Создание базы данных в последней версии 12 обновления базы данных SQL (предварительная версия)" 
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


# Создание базы данных в последней версии 12 обновления базы данных SQL (предварительная версия)

[Зарегистрируйтесь](https://portal.azure.com) для получения последней версии базы данных SQL (12), чтобы воспользоваться преимуществами базы данных SQL нового поколения в Microsoft Azure. Чтобы приступить к работе, требуется подписка на Microsoft Azure. Зарегистрируйтесь, чтобы получить [бесплатную пробную версию Azure](http://azure.microsoft.com/pricing/free-trial) и просмотреть информацию о [ценах](http://azure.microsoft.com/pricing/details/sql-database). 


| Создание базы данных | Снимок экрана |
| :--- | :--- |
| 1. Войдите на портал [http://portal.azure.com/](http://portal.azure.com/). | ![New Azure Portal][1] |
| 2. В нижнем левом углу страницы щелкните **Создать**. | ![Initiate New service][2]|
| 3. Выберите элемент **База данных SQL**.| ![Different services to select from][3] |
| 4. Откроется колонка **База данных SQL**. В поле **Имя** укажите имя базы данных. | ![Name the database][4] |
| 5. В **колонке базы данных SQL** щелкните **СЕРВЕР**. Откроется колонка **Сервер** с двумя вариантами выбора: можно создать новый сервер или использовать существующий.| ![select type of server][4] |
|5а. Если вы выбрали параметр **Использовать существующий сервер**, выберите необходимый сервер и щелкните **Выбрать**. Затем выполните все действия шага 6 далее.|| ![select a server from the list][5]| 
|5б.   В случае выбора параметра **Создать сервер** откроется колонка **Новый сервер**. Укажите имя сервера, имя для входа администратора сервера и пароль. Щелкните **Расположение**,   чтобы выбрать расположение сервера. | ![Complete create new server options][9]| 
|5в. В колонке **Новый сервер** вы можете выбрать создание нового сервера с обновлениями версии 12. Дополнительную информацию о возможностях серверов версии 12 см. в статье [Новые возможности базы данных SQL Azure](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/).| ![Select V12 server][6]|
|5г. Выберите необходимые параметры в колонке **Новый сервер** и нажмите кнопку **ОК**. После этого вы вернетесь к колонке **База данных SQL**, чтобы выполнить оставшиеся действия по созданию базы данных. | ![Complete New Server blade actions][8]|
|6. Нажмите кнопку **Выбрать источник**. Чтобы создать базу данных, вы можете выбрать различные типы источников, например: пустую базу данных, пример базы данных или резервную копию базы данных.| ![Select the source for the database][10]|
|7. Затем в колонке **База данных SQL** щелкните **ЦЕНОВАЯ КАТЕГОРИЯ**. Можно выбрать рекомендуемую ценовую категорию или просмотреть все доступные категории. Сделав выбор, выберите элемент **Выбрать**. <p> Дополнительную информацию о ценовых категориях см. в статьях [Обновление базы данных SQL Web или Business до новых уровней обслуживания](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/) и [Уровни служб и производительности базы данных SQL Azure](http://msdn.microsoft.com/library/azure/dn741336.aspx). |![Select a pricing tier][7]
| 8. Затем в колонке **База данных SQL** щелкните **Дополнительная настройка**, выберите необходимые значения и нажмите кнопку **ОК**. 
| 9. В колонке **База данных SQL** щелкните **Группа ресурсов**. Создайте группу ресурсов или выберите существующую группу ресурсов в списке. Нажмите кнопку **ОК**. За дополнительной информацией обратитесь к разделу [Использование групп ресурсов для управления вашими ресурсами Azure](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups).|![Specify Resource group][11]
| 10. В колонке **База данных SQL** щелкните **ПОДПИСКА**. Откроется колонка **Подписка**. Выберите подписку Azure с поддержкой выбранных вами параметров сервера. Обратите внимание, что параметры сервера могут меняться в зависимости от выбранной подписки.| ![Select subscription.][13]
| 11. Щелкните **Создать**. Будет создана база данных SQL с функциями обновления 12. |![Creates a new database][12]

# Связанные ссылки  #

-  [Новые возможности базы данных SQL Azure](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/)
- [Планирование и подготовка к обновлению базы данных SQL Azure до версии 12](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)

<!--Image references-->
[1]: ./media/sql-database-preview-create/firstscreenportal.png
[2]: ./media/sql-database-preview-create/new.png
[3]: ./media/sql-database-preview-create/sqldatabase.png
[4]: ./media/sql-database-preview-create/databasename.png
[5]: ./media/sql-database-preview-create/useexistingserver.PNG
[6]: ./media/sql-database-preview-create/v12server.PNG
[7]: ./media/sql-database-preview-create/pricingtierdetails.png
[8]: ./media/sql-database-preview-create/finishnewserverblade.png
[9]: ./media/sql-database-preview-create/createnewserver.png
[10]: ./media/sql-database-preview-create/selectsource.png
[11]: ./media/sql-database-preview-create/resourcegroup.png
[12]: ./media/sql-database-preview-create/create.png
[13]: ./media/sql-database-preview-create/subscription.PNG


<!--HONumber=47-->
