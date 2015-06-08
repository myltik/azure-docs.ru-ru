<properties 
	pageTitle="Скопируйте выходные данные из учебника в базу данных локального сервера SQL Server" 
	description="Пошаговое руководство в этом учебнике расширяет учебник по фабрике данных, описывающий копирование данных эффективности кампании в базу данных локального сервера SQL Server."
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
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="spelluru"/>


# Пошаговое руководство: копирование данных эффективности кампании на локальный сервер SQL Server
В этом пошаговом руководстве вы узнаете, как настроить среду для включения конвейера, работающего с вашими локальными данными.
 
В последнем шаге сценария обработки журнала из первого пошагового руководства был описан рабочий процесс "секционирование -> обогащение -> анализ", где выходные данные эффективности маркетинговой кампании были скопированы в базу данных SQL Azure. Вы также могли переместить эти данные на локальный сервер SQL Server для аналитического анализа внутри вашей организации.
 
Чтобы скопировать данные эффективности маркетинговой кампании из большого двоичного объекта Azure на локальный сервер SQL, необходимо создать дополнительную локальную связанную службу, таблицу и конвейер, используя тот же набор командлетов, представленный в первом пошаговом руководстве.

## Предварительные требования

Перед тем как приступить к пошаговому руководству, описанному в этом разделе, **необходимо** выполнить пошаговое руководство [Учебник. Перемещение и обработка файлов журналов с помощью фабрики данных][datafactorytutorial].

**(Рекомендуется.)** Чтобы выполнить пошаговое руководство по созданию конвейера для перемещения данных из локального сервера SQL Server в хранилище больших двоичных объектов Azure, прочитайте и выполните инструкции, изложенные в статье [Использование конвейера для работы с локальными данными][useonpremisesdatasources].


В этом пошаговом руководстве выполняются следующие действия.

1. [Шаг 1. Создание шлюза управления данными](#OnPremStep1). Шлюз управления данными — это агент клиента, который предоставляет доступ к локальным источникам данных в вашей организации из облака. Шлюз обеспечивает передачу данных между локальным сервером SQL Server и хранилищами данных Azure.	

	Вам необходимо установить по крайней мере один шлюз в корпоративной среде, а также зарегистрировать его в фабрике данных Azure, прежде чем добавлять локальную базу данных SQL Server как связанную службу в фабрику данных Azure.

2. [Шаг 2. Создание связанной службы для локального сервера SQL Server.](#OnPremStep2). На этом шаге вы сначала создадите на локальном компьютере SQL Server базу данных и таблицу, а затем создадите связанную службу **OnPremSqlLinkedService**:
3. [Шаг 3. Создание таблицы и конвейера](#OnPremStep3). На этом шаге вы создадите таблицу **MarketingCampaignEffectivenessOnPremSQLTable** и конвейер **EgressDataToOnPremPipeline**. 

4. [Шаг 4. Мониторинг конвейера и просмотр результата](#OnPremStep4) На этом шаге вы выполните мониторинг конвейеров, таблиц и срезов данных с помощью портала Azure.


## <a name="OnPremStep1"></a> Шаг 1. Создание шлюза управления данными

Шлюз управления данными — это агент клиента, который предоставляет доступ к локальным источникам данных в вашей организации из облака. Шлюз обеспечивает передачу данных между локальным сервером SQL Server и хранилищами данных Azure.
  
Вам необходимо установить по крайней мере один шлюз в корпоративной среде, а также зарегистрировать его в фабрике данных Azure, прежде чем добавлять локальную базу данных SQL Server как связанную службу в фабрику данных Azure.

Если у вас есть шлюз данных, который можно использовать, пропустите этот шаг.

1.	Создайте логический шлюз данных. В **предварительной версии портала Azure**, щелкните **связанные службы** на **ФАБРИКА данных** выноски.
2.	В панели команд щелкните **Добавить (+) шлюз данных**.  
3.	В колонке **Создать шлюз данных** щелкните **СОЗДАТЬ**.
4.	В колонке **Создать** введите **MyGateway** в поле **имя** шлюза данных.
5.	Щелкните **ВЫБРАТЬ РЕГИОН** и, при необходимости, измените регион. 
6.	Нажмите кнопку **ОК** в колонке **Создать**. 
7.	Должна появиться колонка **Настройка**. 
8.	В колонке **Настройка** щелкните **Установить непосредственно на этот компьютер**. Это приведет к скачиванию, установке и настройке шлюза на компьютере и к регистрации шлюза в службе. Если у вас на компьютере уже установлен существующий шлюз, который вы хотите связать с этим логическим шлюзом на портале, используйте ключ в этой колонке для повторной регистрации шлюза с помощью диспетчера конфигураций шлюза управления данными (предварительная версия).

	![Диспетчер конфигурации шлюза управления данными][image-data-factory-datamanagementgateway-configuration-manager]

9. Нажмите кнопку **ОК**, чтобы закрыть колонку **Настройка**, затем снова **ОК**, чтобы закрыть колонку **Создать**. Подождите, пока состояние **MyGateway** в колонке **Связанные службы** не изменится на **ХОРОШО**. Также можно запустить инструмент **Диспетчер конфигураций шлюза управления данными (предварительная версия)**, чтобы подтвердить, что имя шлюза совпадает с именем на портале, а **состояние** — **Зарегистрировано**. Может потребоваться закрыть и снова открыть колонку "Связанные службы", чтобы увидеть последнее состояние. Может пройти несколько минут, пока последнее состояние не обновится на экране.

## <a name="OnPremStep2"></a> Шаг 2. Создание связанной службы для локального сервера SQL Server


На этом шаге вы сначала создадите на локальном компьютере SQL Server необходимые вам базу данных и таблицу, а затем создадите связанную службу.

### Подготовка локальной базы данных и таблицы

Прежде всего необходимо создать базу данных SQL Server, таблицу, пользовательские типы и хранимые процедуры. Они будут использоваться для перемещения результатов **MarketingCampaignEffectiveness** из большого двоичного объекта Azure в базу данных SQL Server.

1.	В **проводнике Windows** перейдите к вложенной папке **OnPremises** в папке **C:\ADFWalkthrough** (или в расположении, в которое вы извлекли примеры файлов).
2.	Откройте **prepareOnPremDatabase&Table.ps1** в любом редакторе, замените выделенные данные информацией своего сервера SQL Server и сохраните файл (укажите данные **проверки подлинности SQL**. Для примера в этом учебнике включите проверку подлинности SQL для базы данных. 
			
		$dbServerName = "<servername>"
		$dbUserName = "<username>"
		$dbPassword = "<password>"

3. В **Azure PowerShell** перейдите к папке **C:\ADFWalkthrough\OnPremises**.
4.	Запустите **prepareOnPremDatabase&Table.ps1** **(знак & в двойных кавычках или как показано ниже)**.
			
		& '.\prepareOnPremDatabase&Table.ps1'

5. Если сценарий выполнится успешно, вы увидите следующее:
			
		PS E:\ADF\ADFWalkthrough\OnPremises> & '.\prepareOnPremDatabase&Table.ps1'
		6/10/2014 10:12:33 PM Script to create sample on-premises SQL Server Database and Table
		6/10/2014 10:12:33 PM Creating the database [MarketingCampaigns], table and stored procedure on [.]...
		6/10/2014 10:12:33 PM Connecting as user [sa]
		6/10/2014 10:12:33 PM Summary:
		6/10/2014 10:12:33 PM 1. Database 'MarketingCampaigns' created.
		6/10/2014 10:12:33 PM 2. 'MarketingCampaignEffectiveness' table and stored procedure 


### Создание связанной службы

1.	В **предварительной версии портала Azure**, щелкните **связанные службы** на плитке **ФАБРИКА данных** выноски для **LogProcessingFactory**.
2.	В **связанные службы** стоечный модуль, щелкните **Добавить (+) в хранилище данных**.
3.	В **новое хранилище данных** выноску, введите **OnPremSqlLinkedService** для **имя**. 
4.	Щелкните **типа (параметры, необходимые)** и выберите **SQL Server**. Вы увидите **ШЛЮЗ данных**, **сервера**, **базы данных**, и **учетные данные** параметров в **новое хранилище данных** теперь выноски. 
5.	Щелкните **ШЛЮЗ данных (настроить необходимые параметры)** и выберите **MyGateway** был создан ранее. 
6.	Введите **имя** сервера базы данных, на котором размещена **MarketingCampaigns** базы данных. 
7.	Введите **MarketingCampaigns** для базы данных. 
8.	Щелкните **учетные данные**. 
9.	В **учетные данные** стоечный модуль, щелкните **щелкните здесь, чтобы задать учетные данные безопасно**.
10.	Он устанавливает приложение одним щелчком в первый раз, а затем запускает **Установка учетных данных **диалоговое окно. 11.	В **Установка учетных данных** диалоговом окне введите **имя пользователя** и **пароль**, и нажмите кнопку **ОК**. Подождите, пока закроется диалоговое окно. 
12.	Щелкните **ОК** в **новое хранилище данных** выноски. 
13.	В **связанные службы** выноску, убедитесь, что **OnPremSqlLinkedService** отображается в списке и **состояние** связанные службы является **хорошим**.

## <a name="OnPremStep3"></a> Шаг 3. Создание таблицы и конвейера.

### Создание локальной логической таблицы

1.	В **Azure PowerShell**, переключитесь в **C:\ADFWalkthrough\OnPremises** папки. 
2.	С помощью командлета **Создать AzureDataFactoryTable** следующим создание таблиц для **MarketingCampaignEffectivenessOnPremSQLTable.json**.

			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessOnPremSQLTable.json
	 
#### Создание конвейера для копирования данных из большого двоичного объекта Azure на сервер SQL Server

1.	С помощью командлета **Создать AzureDataFactoryPipeline** следующим Создание конвейера для **EgressDataToOnPremPipeline.json**.

			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EgressDataToOnPremPipeline.json
	 
2. С помощью командлета **набора AzureDataFactoryPipelineActivePeriod** чтобы указать активный период для **EgressDataToOnPremPipeline**.

			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name EgressDataToOnPremPipeline

	Нажмите клавишу **«Y»** для продолжения.
	
## <a name="OnPremStep4"></a> Шаг 4. Мониторинг конвейера и просмотр результата

Теперь можно использовать те же шаги, представленные в [Шаг 6: мониторинга таблиц и конвейеры](#MainStep6) для отслеживания новых конвейера и срезы данных для новой локальной таблицы ADF.
 
Изменение состояния среза таблицы **MarketingCampaignEffectivenessOnPremSQLTable** на Ready означает, что конвейер закончил выполнение среза. Чтобы просмотреть результаты, запросите таблицу **MarketingCampaignEffectiveness** в базе данных **MarketingCampaigns** на сервере SQL Server.
 
Поздравляем! Вы успешно прошли пошаговое руководство по использованию локального источника данных.
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[datafactorytutorial]: data-factory-tutorial-using-powershell.md
[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-use-onpremises-datasources.md
[usepigandhive]: data-factory-pig-hive-activities.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx

[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial-extend-onpremises-using-powershell/DataManagementGatewayConfigurationManager.png

<!---HONumber=GIT-SubDir-->