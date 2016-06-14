
<properties
   pageTitle="Создание шлюза приложений с помощью шаблонов диспетчера ресурсов Azure | Microsoft Azure"
   description="На этой странице приводятся инструкции по созданию шлюза приложений Azure с помощью шаблона диспетчера ресурсов Azure."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/07/2016"
   ms.author="joaoma"/>


# Создание шлюза приложений с помощью шаблона диспетчера ресурсов Azure

Шлюз приложений — это балансировщик нагрузки уровня 7. Он отвечает за отработку отказов и эффективную маршрутизацию HTTP-запросов между разными серверами (облачными и локальными). Шлюз приложений выполняет такие функции доставки приложений: балансировка HTTP-нагрузки, определение сходства сеансов по файлам cookie, разгрузка SSL.

> [AZURE.SELECTOR]
- [Классическая модель — Azure PowerShell](application-gateway-create-gateway.md)
- [PowerShell и диспетчер ресурсов Azure](application-gateway-create-gateway-arm.md)
- [Шаблон диспетчера ресурсов Azure](application-gateway-create-gateway-arm-template.md)

<BR>

Вы узнаете, как скачивать и изменять существующий шаблон диспетчера ресурсов Azure (ARM) из GitHub, а также как развертывать шаблон из GitHub, PowerShell и интерфейса командной строки Azure.

Если вы развертываете шаблон ARM непосредственно из GitHub без изменений, перейдите к соответствующему разделу.


## Сценарий

Из этой статьи вы узнаете:

- как создать шлюз приложений с двумя экземплярами;
- как создать виртуальную сеть с именем VirtualNetwork1 и зарезервированным блоком CIDR (10.0.0.0/16);
- как создать подсеть с именем Appgatewaysubnet и блоком CIDR (10.0.0.0/28);
- как задать два ранее настроенных внутренних IP-адреса для веб-серверов, которые будут балансировать трафик. В этом примере в качестве внутренних IP-адресов используются адреса 10.0.1.10 и 10.0.1.11.

>[AZURE.NOTE] Эти значения используются в данном шаблоне. Чтобы настроить шаблон, можно обновить правила, прослушиватель и SSL, внеся необходимые изменения в файл azuredeploy.json.



![Сценарий](./media/application-gateway-create-gateway-arm-template/scenario-arm.png)



## Скачивание и использование шаблона диспетчера ресурсов Azure

Вы можете скачать из GitHub уже существующий шаблон диспетчера ресурсов Azure (ARM), чтобы создать виртуальную сеть и две подсети, а также внести в него нужные изменения и применить. Для этого сделайте следующее.

1. Перейдите к этапу [Создание шлюза приложений](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-create).
2. Щелкните **azuredeploy.json** и нажмите кнопку **RAW**.
3. Сохраните файл в локальную папку на своем компьютере.
4. Если вы знакомы с шаблонами ARM, перейдите к шагу 7.
5. Откройте только что сохраненный файл и просмотрите содержимое раздела **parameters** в строке 5. В параметрах шаблона ARM есть заполнитель для значений, которые могут подставляться во время развертывания.

	| Параметр | Описание |
	|---|---|
	| **location** | Регион Azure, в котором будет создан шлюз приложений. |
	| **VirtualNetwork1** | Имя новой виртуальной сети. |
	| **addressPrefix** | Адресное пространство виртуальной сети в формате CIDR. |
	| **ApplicationGatewaysubnet** | Имя подсети шлюза приложений. |
	| **subnetPrefix** | Блок CIDR для подсети шлюза приложений. |
	| **skuname** | Размер экземпляра SKU. |
	| **емкость** | Количество экземпляров. |
	| **backendaddress1** | IP-адрес первого веб-сервера |
	| **backendaddress2** | IP-адрес второго веб-сервера |


>[AZURE.IMPORTANT] Шаблоны ARM, хранящиеся в GitHub, со временем могут изменяться. Перед использованием шаблона обязательно его проверьте.

6. Проверьте содержимое раздела **resources** и обратите внимание на следующие параметры:

	- **type**. Тип ресурса, который создается шаблоном. В этом случае используется тип **Microsoft.Network/applicationGateways**, который представляет шлюз приложений.
	- **name**. Имя ресурса. Обратите внимание на фрагмент кода **[parameters('applicationGatewayName')]**. Он означает, что имя будет предоставлено пользователем или взято из файла параметров в процессе развертывания.
	- **properties**. Список свойств для ресурса. Во время создания шлюза приложений этот шаблон использует виртуальную сеть и общедоступный IP-адрес.

7. Вернитесь на страницу [https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create/](https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create).
8. Щелкните **azuredeploy paremeters.json** и нажмите кнопку **RAW**.
9. Сохраните файл в локальную папку на своем компьютере.
10. Откройте только что сохраненный файл и измените значения параметров. Чтобы развернуть шлюз приложений в соответствии с задачами этого руководства, используйте приведенные ниже значения.

		{
		  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
		{
    	"location" : {
        "value" : "West US"
    	},
    	"addressPrefix": {
        "value": "10.0.0.0/16"
    	},
    	"subnetPrefix": {
        "value": "10.0.0.0/24"
    	},
    	"skuName": {
        "value": "Standard_Small"
    	},
    	"capacity": {
        "value": 2
    	},
    	"backendIpAddress1": {
        "value": "10.0.1.10"
    	},
    	"backendIpAddress2": {
        "value": "10.0.1.11"
    	}
		}

11. Сохраните файл. Вы можете проверить шаблон JSON и шаблон параметров с помощью таких веб-инструментов проверки JSON, как [JSlint.com](http://www.jslint.com/).

## Развертывание шаблона диспетчера ресурсов Azure с помощью PowerShell

Если вы ранее не использовали Azure PowerShell, следуйте инструкциям в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md) до этапа входа в Azure и выбора подписки.

### Шаг 1

		Login-AzureRmAccount



### Шаг 2

Просмотрите подписки учетной записи.

		get-AzureRmSubscription

Вам будет предложено указать свои учетные данные для проверки подлинности.<BR>

### Шаг 3.

Выберите, какие подписки Azure будут использоваться. <BR>


		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Шаг 4.


При необходимости создайте новую группу ресурсов с помощью командлета **New-AzureResourceGroup**. В приведенном ниже примере создается группа ресурсов с именем AppgatewayRG, расположенная в регионе East US (восточная часть США).

	 New-AzureRmResourceGroup -Name AppgatewayRG -Location "East US"
		VERBOSE: 5:38:49 PM - Created resource group 'AppgatewayRG' in location 'eastus'


		ResourceGroupName : AppgatewayRG
		Location          : eastus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
	                 Actions  NotActions
	                 =======  ==========
	                  *

		ResourceId        : /subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/AppgatewayRG

Запустите командлет **New-AzureRmResourceGroupDeployment**, чтобы развернуть новую виртуальную сеть с помощью шаблона и файлов параметров, которые вы скачали и изменили ранее.

		New-AzureRmResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
 		   -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json

Выходные данные командной строки будут выглядеть так:

		DeploymentName    : testappgatewaydeployment
		ResourceGroupName : appgatewayRG
		ProvisioningState : Succeeded
		Timestamp         : 9/19/2015 1:49:41 AM
		Mode              : Incremental
		TemplateLink      :
		Parameters        :
                   Name             Type                       Value
                   ===============  =========================  ==========
                   location         String                     East US
                   addressPrefix    String                     10.0.0.0/16
                   subnetPrefix     String                     10.0.0.0/24
                   skuName          String                     Standard_Small
                   capacity         Int                        2
                   backendIpAddress1  String                     10.0.1.10
                   backendIpAddress2  String                     10.0.1.11

		Outputs           :


## Развертывание шаблона ARM с помощью интерфейса командной строки Azure

Развернуть шаблон ARM, скачанный с помощью Azure CLI, можно так.

1. Если вы еще не пользовались интерфейсом командной строки Azure, см. статью [Установка Azure CLI](../xplat-cli-install.md) и следуйте инструкциям до этапа выбора учетной записи Azure и подписки.
2. Выполните команду **azure config mode**, чтобы переключиться в режим диспетчера ресурсов, как показано ниже.

		azure config mode arm

Вот результат, ожидаемый для указанной выше команды:

		info:	New mode is arm

3. При необходимости выполните команду **azure group create**, как показано ниже, чтобы создать группу ресурсов. Обратите внимание на результат выполнения команды. В списке, который откроется после выполнения команды, будут указаны используемые параметры. Дополнительные сведения о группах ресурсов см. в статье [Общие сведения об Azure Resource Manager](../resource-group-overview.md).

		azure group create -n appgatewayRG -l eastus

**-n (или --name)**. Имя для новой группы ресурсов. В нашем случае это *appgatewayRG*.

**-l (или --location)**. Регион Azure, в котором будет создана новая группа ресурсов. В нашем случае это *eastus*.

4. Запустите командлет **azure group deployment create**, чтобы развернуть новую виртуальную сеть с помощью файлов шаблона и параметров, которые вы скачали и изменили ранее. В списке, который откроется после выполнения команды, будут указаны используемые параметры.

		azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

Вот результат, ожидаемый для указанной выше команды:

		azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json
		info:    Executing command group deployment create
		+ Initializing template configurations and parameters
		+ Creating a deployment
		info:    Created template deployment "TestAppgatewayDeployment"
		+ Waiting for deployment to complete
		data:    DeploymentName     : TestAppgatewayDeployment
		data:    ResourceGroupName  : appgatewayRG
		data:    ProvisioningState  : Succeeded
		data:    Timestamp          : 2015-09-21T20:50:27.5129912Z
		data:    Mode               : Incremental
		data:    Name               Type    Value
		data:    -----------------  ------  --------------
		data:    location           String  East US
		data:    addressPrefix      String  10.0.0.0/16
		data:    subnetPrefix       String  10.0.0.0/24
		data:    skuName            String  Standard_Small
		data:    capacity           Int     2
		data:    backendIpAddress1  String  10.0.1.10
		data:    backendIpAddress2  String  10.0.1.11
		info:    group deployment create command OK

**-g (или --resource-group)**. Имя группы ресурсов, в которой будет создана виртуальная сеть.

**-f (или --template-file)**. Путь к файлу шаблона диспетчера ресурсов Azure.

**-e (или --parameters-file)**. Путь к файлу параметров диспетчера ресурсов Azure.

## Развертывание шаблона ARM с помощью интерфейса кнопки развертывания

Развертывание с помощью кнопки развертывания — еще один способ использования шаблонов ARM. Он позволяет быстро и удобно работать с шаблонами на портале Azure.


### Шаг 1
Перейдите на страницу [Create an Application Gateway with Public IP](https://azure.microsoft.com/documentation/templates/101-application-gateway-public-ip/) (Создание шлюза приложений с использованием общедоступного IP-адреса).


### Шаг 2

Нажмите кнопку **Развернуть в Azure**.

![Развертывание в Azure](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)

### Шаг 3.

На портале заполните параметры шаблона развертывания и нажмите кнопку **OК**.

![Параметры](./media/application-gateway-create-gateway-arm-template/ibiza1.png)

### Шаг 4.

Выберите **Юридические условия** и щелкните **Купить**.

### Шаг 5

В колонке "Настраиваемое развертывание" щелкните **Создать**.



## Дальнейшие действия

Сведения о настройке разгрузки SSL см. в статье [Настройка шлюза приложений для разгрузки SSL с помощью классической модели развертывания](application-gateway-ssl.md).

Указания по настройке шлюза приложений для использования с внутренним балансировщиком нагрузки см. в статье [Создание шлюза приложений с внутренним балансировщиком нагрузки (ILB)](application-gateway-ilb.md).

Дополнительные сведения о параметрах балансировки нагрузки в целом см. в статьях:

- [Подсистема балансировщика нагрузки Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0608_2016-->