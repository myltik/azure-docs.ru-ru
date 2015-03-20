<properties 
	pageTitle="Развертывание DocumentDB и веб-сайтов Azure с использованием шаблона диспетчера ресурсов Azure | Azure" 
	description="В этом учебном курсе показано, как выполнять развертывание DocumentDB и веб-сайтов Azure с использованием шаблона диспетчера ресурсов Azure." 
	services="documentdb, websites" 
	authors="stephbaron" 
	manager="johnmac" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/09/2015" 
	ms.author="stbaro"/>

# Развертывание DocumentDB и веб-сайтов Azure с использованием шаблона диспетчера ресурсов Azure #

В этом учебном курсе показано, как использовать шаблон диспетчера ресурсов Azure для развертывания и интеграции [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/), [веб-сайтов Azure](http://azure.microsoft.com/services/websites/), а также пример простого веб-приложения.

После завершения этого учебника вы сможете ответить на следующие вопросы:  

-	Как можно использовать шаблон диспетчера ресурсов Azure для развертывания и интеграции учетной записи DocumentDB и веб-сайтов Azure?
-	Как можно использовать шаблон диспетчера ресурсов Azure для развертывания и интеграции учетной записи DocumentDB, веб-сайтов Azure и приложения веб-развертывания?

##<a id="Prerequisites"></a>Предварительные требования ##
> [AZURE.TIP] Хотя этот учебник не требует наличия опыта работы с шаблонами диспетчера ресурсов Azure, JSON или Azure PowerShell, однако если возникнет необходимость изменить ссылки шаблонов или варианты развертывания, то будет необходимо знание каждой из этих областей.

Перед выполнением указаний данного учебника убедитесь, что у вас есть следующее:

- Подписка Azure. Azure - платформа, основанная на подписках.  Дополнительные сведения о том, как оформить подписку, см. в разделах [Варианты приобретения](http://azure.microsoft.com/pricing/purchase-options/), [Предложения для участников](http://azure.microsoft.com/pricing/member-offers/) или [Бесплатная пробная версия](http://azure.microsoft.com/pricing/free-trial/).
- Учетная запись хранения Azure Инструкции см. в разделе [Сведения об учетных записях хранения Azure](../storage-whatis-account/) 
- Рабочая станция с Azure PowerShell. Инструкции см. в разделе [Установка и настройка Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/) 

##<a id="CreateDB"></a>Шаг 1. Загрузка и распаковка файлов примера ##
Давайте начнем с загрузки файлов примеров, которые будем использовать в этом уроке.

1. Загрузите [Пример создания учетной записи DocumentDB, веб-сайтов и развертывания демонстрационного приложения](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip) в локальную папку (например, C:\DocumentDBTemplates) и распакуйте файлы.  В этом примере будет развернута учетная запись DocumentDB, веб-сайты Azure и веб-приложение.  Также будет произведена автоматическая настройка веб-приложения для подключения к учетной записи DocumentDB.

2. Загрузите [Пример создания учетной записи DocumentDB и веб-сайтов](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip) в локальную папку (например, C:\DocumentDBTemplates) и распакуйте файлы.  В этом примере будет развернута учетная запись DocumentDB, веб-сайты Azure и изменена конфигурация веб-сайтов для упрощения подключения DocumentDB, но в него не входит веб-приложение.  

> [AZURE.TIP] Обратите внимание, что в зависимости от настроек безопасности компьютера может понадобиться разблокировать распакованные файлы, щелкнув правой кнопкой мыши на них, нажав **Свойства** и **Разблокировать**.

![Screenshot of the Properties window with the Unblock button highlighted](./media/documentdb-create-documentdb-website/image1.png)


##<a id="Build"></a>Шаг 2. Пример развертывания учетной записи Document, веб-сайтов и демонстрационного приложения ##

Теперь давайте развернем первый шаблон.

> [AZURE.TIP] Шаблон не проверяет, являются ли имя веб-сайтов и имя учетной записи DocumentDB: а) действующими и б) доступными.  Перед запуском сценария развертывания PowerShell настоятельно рекомендуется проверить наличие доступности имен, которые планируется использовать.

1. Откройте Microsoft Azure PowerShell и перейдите к папке, в которую был загружен и распакован [Пример создания учетной записи DocumentDB, веб-сайтов и развертывания демонстрационного приложения](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip) (например, C:\DocumentDBTemplates\CreateDocDBWebsiteTodo).


2. Будет выполнен сценарий PowerShell CreateDocDBWebsiteTodo.ps1.  Для него существуют следующие обязательные параметры:
	- WebsiteName. Задает имя веб-сайта и используется для создания URL-адреса, который будет использоваться для осуществления доступа к веб-сайту (например, если указать "mydemodocdbwebsite", то URL-адрес для доступа к веб-сайтами будет иметь вид mydemodocdbwebsite.azurewebsites.net).

	- ResourceGroupName. Задает имя группы ресурсов Azure для развертывания. Если заданная группа ресурсов не существует, то она будет создана.

	- docDBAccountName. Задает имя создаваемой учетной записи DocumentDB.

	- location. Задает расположение Azure, в котором будут создаваться ресурсы DocumentDB и веб-сайтов.  Возможные значения: Восточная Азия, Западная Европа, Запад США, Восток США, Северная Европа, Юго-Восточная Азия (обратите внимание, что значение должно задаваться с учетом регистра).


3. Вот пример команды для запуска сценария:

    	PS C:\DocumentDBTemplates\CreateDocDBWebsiteTodo> .\CreateDocDBWebsiteTodo.ps1 -WebSiteName "mydemodocdbwebsite" -ResourceGroupName "myDemoResourceGroup" -docDBAccountName "mydemodocdbaccount" -location "West US"

	> [AZURE.TIP] Обратите внимание, что будет предложено ввести имя пользователя и пароль учетной записи Azure в рамках выполнения скрипта.  Полное развертывание займет от 10 до 15 минут.  	

4. А вот пример полученного вывода: 

		VERBOSE: 1:06:00 PM - Created resource group 'myDemoResourceGroup' in location westus'
		VERBOSE: 1:06:01 PM - Template is valid.
		VERBOSE: 1:06:01 PM - Create template deployment 'Microsoft.DocumentDBWebSiteTodo'.
		VERBOSE: 1:06:08 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is running
		VERBOSE: 1:06:10 PM - Resource Microsoft.Web/serverFarms 'mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:14 PM - Resource microsoft.insights/alertrules 'CPUHigh mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/autoscalesettings 'mydemodocdbwebsite-myDemoResourceGroup' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/alertrules 'LongHttpQueue mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:21 PM - Resource Microsoft.Web/Sites 'mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:23 PM - Resource microsoft.insights/alertrules 'ForbiddenRequests mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/alertrules 'ServerErrors mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/components 'mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:24 PM - Resource Microsoft.Web/Sites/config 'mydemodocdbwebsite/web' provisioning status is succeeded
		VERBOSE: 1:16:27 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebsite/MSDeploy' provisioning status is running
		VERBOSE: 1:16:35 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebsite/MSDeploy' provisioning status is succeeded

		ResourceGroupName : myDemoResourceGroup
		Location          : westus
		Resources         : {mydemodocdbaccount, CPUHigh mydemodocdbwebsite, ForbiddenRequests mydemodocdbwebsite, LongHttpQueue mydemodocdbwebsite...}
		ResourcesTable    :
                    Name                                    Type                                   Location
                    ======================================  =====================================  =========
                    mydemodocdbaccount                      Microsoft.DocumentDb/databaseAccounts  westus
                    CPUHigh mydemodocdbwebsite              microsoft.insights/alertrules          eastus
                    ForbiddenRequests mydemodocdbwebsite    microsoft.insights/alertrules          eastus
                    LongHttpQueue mydemodocdbwebsite        microsoft.insights/alertrules          eastus
                    ServerErrors mydemodocdbwebsite         microsoft.insights/alertrules          eastus
                    mydemodocdbwebsite-myDemoResourceGroup  microsoft.insights/autoscalesettings   eastus
                    mydemodocdbwebsite                      microsoft.insights/components          centralus
                    mydemodocdbwebsite                      Microsoft.Web/serverFarms              westus
                    mydemodocdbwebsite                      Microsoft.Web/sites                    westus

		ProvisioningState : Succeeded


5. Прежде чем перейти к рассмотрению примера приложения, давайте разберемся, как осуществляется развертывание шаблона.

	- Создана служба веб-сайтов Azure.

	- Создана учетная запись DocumentDB.

	- Пакет веб-развертывания развернут на веб-сайтах Azure

	- Конфигурация веб-сайтов Azure изменена таким образом, чтобы конечная точка и первичный главный ключ Azure DocumentDB были отражены в настройках приложения.

	- Был создан ряд правил мониторинга по умолчанию.

	
6. Для того чтобы использовать приложение, просто перейдите по URL-адресу веб-сайта (в приведенном выше примере URL-адрес будет иметь вид http://mydemodocdbwebsite.azurewebsites.net).  Вы увидите следующее веб-приложение.

	![Sample Todo application](./media/documentdb-create-documentdb-website/image2.png)

7. Далее создадим несколько задач, а затем откроем [портал управления Azure (предварительная версия)](https://portal.azure.com).

8. Выберите просмотр групп ресурсов и выберите созданную во время развертывания группу ресурсов (в приведенном выше примере это myDemoResourceGroup).

	![Screenshot of the Azure Preview portal with the myDemoResourceGroup highlighted](./media/documentdb-create-documentdb-website/image3.png)
9.  Обратите внимание, что вкладка "Сведения" показывает все связанные ресурсы (учетную запись DocumentDB, веб-сайты, мониторинг).

	![Screenshot of the Summary lens](./media/documentdb-create-documentdb-website/image4.png)
10.  Выберите учетную запись DocumentDB и запустите обозреватель запросов (в нижней части вкладки учетных данных).

	![Screenshot of the Resource Group and Account blades with the Query Explorer tile highlighted](./media/documentdb-create-documentdb-website/image8.png)

11. Запустите запрос по умолчанию, "SELECT * FROM c", и ознакомьтесь с результатами.  Обратите внимание, что запрос вернул JSON-представление элементов списка задач, которые были созданы на шаге 7 выше.  Поэкспериментируйте с запросами, например попробуйте выполнить запрос "SELECT * FROM c WHERE c.isComplete = true", он вернет все элементы списка задач, которые были помечены как завершенные.


	![Screenshot of the Query Explorer and Results blades showing the query results](./media/documentdb-create-documentdb-website/image5.png)
12. Не стесняйтесь исследовать портал экспериментов DocumentDB или вносить изменения в пример приложения Todo.  Когда вы закончите, давайте развернем еще один шаблон.
	 
##<a id="Build"></a>Шаг 3. Пример развертывания учетной записи Document и веб-сайтов ##

Теперь давайте развернем второй шаблон.

> [AZURE.TIP] Шаблон не проверяет, являются ли имя веб-сайтов и имя учетной записи DocumentDB: а) действующими и б) доступными.  Перед запуском сценария развертывания PowerShell настоятельно рекомендуется проверить наличие доступности имен, которые планируется использовать.

1. Откройте Microsoft Azure PowerShell и перейдите к папке, в которую был загружен и распакован [Пример создания учетной записи DocumentDB и веб-сайтов](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip) (например, C:\DocumentDBTemplates\CreateDocDBWebsite).


2. Будет выполнен сценарий PowerShell CreateDocDBWebsite.ps1.  Сценарий принимает те же самые параметры, что и в первом развернутом шаблоне, а именно:
	- WebsiteName. Задает имя веб-сайта и используется для создания URL-адреса, который будет использоваться для осуществления доступа к веб-сайту (например, если указать "myotherdocumentdbwebsite", то URL-адрес для доступа к веб-сайтами будет иметь вид myotherdocumentdbwebsite.azurewebsites.net).

	- ResourceGroupName. Задает имя группы ресурсов Azure для развертывания.  Если заданная группа ресурсов не существует, то она будет создана.

	- docDBAccountName. Задает имя создаваемой учетной записи DocumentDB.

	- 	location. Задает расположение Azure, в котором будут создаваться ресурсы DocumentDB и веб-сайтов.  Возможные значения: Восточная Азия, Западная Европа, Запад США, Восток США, Северная Европа, Юго-Восточная Азия (обратите внимание, что значение должно задаваться с учетом регистра).

3. Вот пример команды для запуска сценария.

    	PS C:\DocumentDBTemplates\CreateDocDBWebSite> .\CreateDocDBWebSite.ps1 -WebSiteName "myotherdocumentdbwebsite" -ResourceGroupName "myOtherDemoResourceGroup" -docDBAccountName "myotherdocumentdbdemoaccount" -location "East US"

	> [AZURE.TIP] Обратите внимание, что будет предложено ввести имя пользователя и пароль учетной записи Azure в рамках выполнения скрипта.  Полное развертывание займет от 10 до 15 минут.  	

4. Выходные данные развертывания очень похожи на первый пример шаблона. 
5. Прежде чем перейти ко входу на портал Azure, давайте разберемся, как осуществляется развертывание шаблона.

	- Создана служба веб-сайтов Azure.

	- Создана учетная запись DocumentDB.

	- 	Конфигурация веб-сайтов Azure изменена таким образом, чтобы конечная точка и вторичный главный ключ Azure DocumentDB были отражены в настройках приложения.

	- 	Был создан ряд правил мониторинга по умолчанию.

6. Давайте откроем [портал Azure (предварительная версия)](https://portal.azure.com), выберем просмотр групп ресурсов и выберем созданную во время развертывания группу ресурсов (в примере выше это myOtherDemoResourceGroup).
7. На вкладке "Сведения" выберите развернутый веб-сайт Azure.

	![Screenshot of the Summary lens with the myotherdocumentdwebsite website highlighted](./media/documentdb-create-documentdb-website/image6.png)
8. На вкладке "Веб-сайт" нажмите **Все параметры**, затем **Параметры приложения** и ознакомьтесь с имеющимися там параметрами приложения для конечной точки и каждого из главных ключей DocumentDB.

	![Screenshot of the Website, Settings, and Site settings blades](./media/documentdb-create-documentdb-website/image7.png)
9. Вы можете продолжить просмотр портала Azure, либо перейти к одному из [примеров](http://go.microsoft.com/fwlink/?LinkID=402386) DocumentDB для создания собственного приложения DocumentDB.

	
	

##<a name="NextSteps"></a>Дальнейшие действия

Поздравляем! Мы выполнили развертывание DocumentDB, веб-сайтов Azure и примера веб-приложения с использованием шаблона диспетчера ресурсов Azure.

- Дополнительные сведения о DocumentDB см. [здесь](http://azure.com/docdb).
- Дополнительные сведения о веб-сайтах Azurе см. [здесь](http://go.microsoft.com/fwlink/?LinkId=325362).
- Дополнительные сведения о шаблонах диспетчера ресурсов Azure см. [здесь](https://msdn.microsoft.com/library/azure/dn790549.aspx).

<!--HONumber=47-->
