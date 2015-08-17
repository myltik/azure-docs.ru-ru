<properties 
	pageTitle="Развертывание DocumentDB и веб-приложений службы приложений Azure с помощью шаблона диспетчера ресурсов Azure | Microsoft Azure" 
	description="В этом учебном курсе показано, как выполнять развертывание учетной записи DocumentDB, веб-приложений службы приложений Azure и примера веб-приложения с использованием шаблона диспетчера ресурсов Azure." 
	services="documentdb, app-service\web" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/28/2015" 
	ms.author="stbaro"/>

# Развертывание DocumentDB и веб-приложений службы приложений Azure с помощью шаблона диспетчера ресурсов Azure #

В этом учебнике показано, как использовать шаблон диспетчера ресурсов Azure для развертывания и интеграции [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/), веб-приложения [службы приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714), а также примера веб-приложения.

После изучения этого учебника вы сможете ответить на следующие вопросы.

-	Как можно использовать шаблон диспетчера ресурсов Azure для развертывания и интеграции учетной записи DocumentDB и веб-приложения в службе приложений Azure?
-	Как можно использовать шаблон диспетчера ресурсов Azure для развертывания и интеграции учетной записи DocumentDB, веб-приложения в веб-приложениях службы приложений и приложения веб-развертывания?

<a id="Prerequisites"></a>
## Предварительные требования ##
> [AZURE.TIP]Хотя этот учебник не требует наличия опыта работы с шаблонами диспетчера ресурсов Azure, JSON или Azure PowerShell, однако, если возникнет необходимость изменить ссылки шаблонов или варианты развертывания, потребуются знания в каждой из этих областей.

Перед выполнением инструкций в этом учебнике убедитесь в наличии следующих ресурсов.

- Подписка Azure. Azure — это платформа на основе подписок. Дополнительные сведения о получении подписки см. в разделах [Варианты приобретения](http://azure.microsoft.com/pricing/purchase-options/), [Предложения для участников](http://azure.microsoft.com/pricing/member-offers/) или [Бесплатное пробное использование](http://azure.microsoft.com/pricing/free-trial/).
- Учетная запись хранения Azure Инструкции см. в разделе [Сведения об учетных записях хранения Azure](../storage-whatis-account.md).
- Рабочая станция с Azure PowerShell. Инструкции см. в разделе [Установка и настройка Azure PowerShell](../install-configure-powershell.md).

##<a id="CreateDB"></a>Шаг 1: Загрузка и распаковка файлов примера ##
Давайте начнем с загрузки файлов примеров, которые будем использовать в этом уроке.

1. Скачайте [Пример создания учетной записи DocumentDB, веб-приложений и развертывания демонстрационного приложения](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip) в локальную папку (например, C:\\DocumentDBTemplates) и распакуйте файлы. В этом примере будут развернуты учетная запись DocumentDB, веб-приложение службы приложений и веб-приложение. Также будет произведена автоматическая настройка веб-приложения для подключения к учетной записи DocumentDB.

2. Загрузите [Пример создания учетной записи DocumentDB и веб-приложений](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip) в локальную папку (например, C:\\DocumentDBTemplates) и распакуйте файлы. В этом примере будут развернуты учетная запись DocumentDB, веб-приложение службы приложений и изменена конфигурация веб-приложения для упрощения предоставления информации о подключении DocumentDB, но в него не входит веб-приложение.

> [AZURE.TIP]Обратите внимание, что в зависимости от настроек безопасности компьютера может понадобиться разблокировать распакованные файлы, для чего следует щелкнуть правой кнопкой мыши на них, выбрать пункт **Свойства**, а затем пункт **Разблокировать**.

![Снимок экрана окна "Свойства" с выделенной кнопкой «Разблокировать»](./media/documentdb-create-documentdb-website/image1.png)

<a id="Build"></a>
##Шаг 2: пример развертывания учетной записи Document, веб-приложения службы приложений и демонстрационного приложения ##

Теперь давайте развернем первый шаблон.

> [AZURE.TIP]Шаблон не проверяет, являются ли имя веб-приложения и имя учетной записи DocumentDB: а) действующими и б) доступными. Перед запуском сценария развертывания PowerShell настоятельно рекомендуется проверить наличие доступности имен, которые планируется использовать.

1. Откройте Microsoft Azure PowerShell и перейдите к папке, в которую был скачан и распакован [Пример создания учетной записи DocumentDB, веб-приложения службы приложений и развертывания демонстрационного приложения](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip) (например, C:\\DocumentDBTemplates\\CreateDocDBWebsiteTodo).


2. Будет выполнен сценарий PowerShell CreateDocDBWebsiteTodo.ps1. Для него существуют следующие обязательные параметры:
	- WebsiteName: задает имя веб-приложения службы приложений и используется для создания URL-адреса, который будет применяться для доступа к веб-приложению (например, если указать "mydemodocdbwebapp", то URL-адрес для доступа к веб-приложению будет иметь вид mydemodocdbwebapp.azurewebsites.net).

	- ResourceGroupName: задает имя группы ресурсов Azure для развертывания. Если заданная группа ресурсов не существует, то она будет создана.

	- docDBAccountName: задает имя создаваемой учетной записи DocumentDB.

	- location: задает расположение Azure, в котором будут создаваться ресурсы DocumentDB и веб-приложения. Возможные значения: Восточная Азия, Западная Европа, Запад США, Восток США, Северная Европа, Юго-Восточная Азия (обратите внимание, что значение должно задаваться с учетом регистра).


3. Вот пример команды для запуска сценария.

    	PS C:\DocumentDBTemplates\CreateDocDBWebAppTodo> .\CreateDocDBWebsiteTodo.ps1 -WebSiteName "mydemodocdbwebapp" -ResourceGroupName "myDemoResourceGroup" -docDBAccountName "mydemodocdbaccount" -location "West US"

	> [AZURE.TIP]Обратите внимание, что в рамках выполнения скрипта будет предложено ввести имя пользователя и пароль учетной записи Azure. Полное развертывание займет от 10 до 15 минут.

4. А вот пример полученного вывода:

		VERBOSE: 1:06:00 PM - Created resource group 'myDemoResourceGroup' in location westus'
		VERBOSE: 1:06:01 PM - Template is valid.
		VERBOSE: 1:06:01 PM - Create template deployment 'Microsoft.DocumentDBWebSiteTodo'.
		VERBOSE: 1:06:08 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is running
		VERBOSE: 1:06:10 PM - Resource Microsoft.Web/serverFarms 'mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:14 PM - Resource microsoft.insights/alertrules 'CPUHigh mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/autoscalesettings 'mydemodocdbwebapp-myDemoResourceGroup' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/alertrules 'LongHttpQueue mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:21 PM - Resource Microsoft.Web/Sites 'mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:23 PM - Resource microsoft.insights/alertrules 'ForbiddenRequests mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/alertrules 'ServerErrors mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/components 'mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:24 PM - Resource Microsoft.Web/Sites/config 'mydemodocdbwebapp/web' provisioning status is succeeded
		VERBOSE: 1:16:27 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebapp/MSDeploy' provisioning status is running
		VERBOSE: 1:16:35 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebapp/MSDeploy' provisioning status is succeeded

		ResourceGroupName : myDemoResourceGroup
		Location          : westus
		Resources         : {mydemodocdbaccount, CPUHigh mydemodocdbwebapp, ForbiddenRequests mydemodocdbwebapp, LongHttpQueue mydemodocdbwebapp...}
		ResourcesTable    :
                    Name                                    Type                                   Location
                    ======================================  =====================================  =========
                    mydemodocdbaccount                      Microsoft.DocumentDb/databaseAccounts  westus
                    CPUHigh mydemodocdbwebapp              microsoft.insights/alertrules          eastus
                    ForbiddenRequests mydemodocdbwebapp    microsoft.insights/alertrules          eastus
                    LongHttpQueue mydemodocdbwebapp        microsoft.insights/alertrules          eastus
                    ServerErrors mydemodocdbwebapp         microsoft.insights/alertrules          eastus
                    mydemodocdbwebapp-myDemoResourceGroup  microsoft.insights/autoscalesettings   eastus
                    mydemodocdbwebapp                      microsoft.insights/components          centralus
                    mydemodocdbwebapp                      Microsoft.Web/serverFarms              westus
                    mydemodocdbwebapp                      Microsoft.Web/sites                    westus

		ProvisioningState : Succeeded


5. Прежде чем перейти к рассмотрению примера приложения, давайте разберемся, как осуществляется развертывание шаблона.

	- Создано веб-приложение службы приложений.

	- Создана учетная запись DocumentDB.

	- Пакет веб-развертывания развернут в веб-приложении службы приложений

	- Конфигурация веб-приложения изменена таким образом, чтобы конечная точка и первичный главный ключ DocumentDB были отражены в настройках приложения.

	- Был создан ряд правил мониторинга по умолчанию.

	
6. Для того чтобы использовать приложение, просто перейдите по URL-адресу веб-приложения (в приведенном выше примере URL-адрес будет иметь вид http://mydemodocdbwebapp.azurewebsites.net)). Вы увидите следующее веб-приложение.

	![Пример приложения Todo](./media/documentdb-create-documentdb-website/image2.png)

7. Далее создадим несколько задач, а затем откроем [портал предварительной версии Microsoft Azure](https://portal.azure.com).

8. Выберите просмотр групп ресурсов и выберите созданную во время развертывания группу ресурсов (в приведенном выше примере это myDemoResourceGroup).

	![Снимок экрана на портале Azure с выделенной группой myDemoResourceGroup](./media/documentdb-create-documentdb-website/image3.png)
9.  Обратите внимание, что вкладка «Сведения» показывает все связанные ресурсы (учетную запись DocumentDB, веб-приложение службы приложений, мониторинг).

	![Снимок экрана с областью "Сводка"](./media/documentdb-create-documentdb-website/image4.png)
10.  Выберите учетную запись DocumentDB и запустите обозреватель запросов (в нижней части вкладки учетных данных).

	![Снимок экрана с колонками «Группы ресурсов» и «Учетная запись» с выделенной плиткой обозревателя запросов](./media/documentdb-create-documentdb-website/image8.png)

11. Запустите запрос по умолчанию, «SELECT * FROM c», и ознакомьтесь с результатами. Обратите внимание, что запрос вернул JSON-представление элементов списка задач, которые были созданы на шаге 7 выше. Поэкспериментируйте с запросами, например попробуйте выполнить запрос «SELECT * FROM c WHERE c.isComplete = true», он вернет все элементы списка задач, которые были помечены как завершенные.


	![Снимок экрана для колонок «Обозреватель запросов» и «Результаты», где отображаются результаты запросов](./media/documentdb-create-documentdb-website/image5.png)
12. Не стесняйтесь исследовать портал экспериментов DocumentDB или вносить изменения в пример приложения Todo. Когда вы закончите, давайте развернем еще один шаблон.
	
<a id="Build"></a>
## Шаг 3: пример развертывания учетной записи Document и веб-приложения ##

Теперь давайте развернем второй шаблон.

> [AZURE.TIP]Шаблон не проверяет, являются ли имя веб-приложения и имя учетной записи DocumentDB: а) действующими и б) доступными. Перед запуском сценария развертывания PowerShell настоятельно рекомендуется проверить наличие доступности имен, которые планируется использовать.

1. Откройте Microsoft Azure PowerShell и перейдите к папке, в которую был скачан и распакован [Пример создания учетной записи DocumentDB и веб-приложения](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip) (например, C:\\DocumentDBTemplates\\CreateDocDBWebsite).


2. Будет выполнен сценарий PowerShell CreateDocDBWebsite.ps1. Сценарий принимает те же самые параметры, что и в первом развернутом шаблоне, а именно:
	- WebsiteName: задает имя веб-приложения службы приложений и используется для создания URL-адреса, который будет применяться для доступа к веб-приложению (например, если указать "myotherdocumentdbwebapp", то URL-адрес для доступа к веб-приложению будет иметь вид myotherdocumentdbwebapp.azurewebsites.net).

	- ResourceGroupName: задает имя группы ресурсов Azure для развертывания. Если заданная группа ресурсов не существует, то она будет создана.

	- docDBAccountName: задает имя создаваемой учетной записи DocumentDB.

	- 	location: задает расположение Azure, в котором будут создаваться ресурсы DocumentDB и веб-приложения. Возможные значения: Восточная Азия, Западная Европа, Запад США, Восток США, Северная Европа, Юго-Восточная Азия (обратите внимание, что значение должно задаваться с учетом регистра).

3. Вот пример команды для запуска сценария.

    	PS C:\DocumentDBTemplates\CreateDocDBWebSite> .\CreateDocDBWebSite.ps1 -WebSiteName "myotherdocumentdbwebapp" -ResourceGroupName "myOtherDemoResourceGroup" -docDBAccountName "myotherdocumentdbdemoaccount" -location "East US"

	> [AZURE.TIP]Обратите внимание, что в рамках выполнения скрипта будет предложено ввести имя пользователя и пароль учетной записи Azure. Полное развертывание займет от 10 до 15 минут.

4. Выходные данные развертывания очень похожи на первый пример шаблона.
5. Прежде чем открыть портал предварительной версии Azure, давайте разберемся, как осуществляется развертывание шаблона.

	- Создано веб-приложение службы приложений.

	- Создана учетная запись DocumentDB.

	- 	Конфигурация веб-приложения изменена таким образом, чтобы конечная точка и вторичный главный ключ Azure DocumentDB были отражены в настройках приложения.

	- 	Был создан ряд правил мониторинга по умолчанию.

6. Давайте откроем [портал предварительной версии Azure](https://portal.azure.com), выберем просмотр групп ресурсов, а затем выберем созданную во время развертывания группу ресурсов (в примере выше это myOtherDemoResourceGroup).
7. В области «Сведения» щелкните развернутое веб-приложение.

	![Снимок экрана для колонки «Сводка» с выделенным веб-приложением myotherdocumentdbwebapp](./media/documentdb-create-documentdb-website/image6.png)
8. В колонке веб-приложения нажмите **Все параметры**, затем **Параметры приложения** и ознакомьтесь с имеющимися там параметрами приложения для конечной точки и каждого из главных ключей DocumentDB.

	![Снимок экрана веб-приложения, колонок «Параметры» и параметров приложения](./media/documentdb-create-documentdb-website/image7.png)
9. Вы можете продолжить просмотр портала предварительной версии Azure либо перейти к одному из наших [примеров](http://go.microsoft.com/fwlink/?LinkID=402386) DocumentDB для создания собственного приложения DocumentDB.

	
	
<a name="NextSteps"></a>
## Дальнейшие действия

Поздравляем! Мы выполнили развертывание DocumentDB, веб-приложения службы приложений и примера веб-приложения с использованием шаблонов диспетчера ресурсов Azure.

- Для получения дополнительных сведений о DocumentDB щелкните [здесь](http://azure.com/docdb).
- Дополнительные сведения о веб-приложениях службы приложений Azure см. [здесь](http://go.microsoft.com/fwlink/?LinkId=325362).
- Дополнительные сведения о шаблонах диспетчера ресурсов Azure см. [здесь](https://msdn.microsoft.com/library/azure/dn790549.aspx).


## Изменения
* Указания по изменениям при переходе от веб-сайтов к службе приложений см. в разделе [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).
* Информацию о смене старого портала на новый см. в [справочнике по навигации на портале Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

>[AZURE.NOTE]Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.
 

<!---HONumber=August15_HO6-->