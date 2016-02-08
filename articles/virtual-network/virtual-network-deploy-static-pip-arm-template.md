<properties 
   pageTitle="Развертывание виртуальных машин с общедоступным статическим IP-адресом с помощью шаблона в диспетчере ресурсов | Microsoft Azure"
   description="Узнайте, как развернуть виртуальную машину со статическим общедоступным IP-адресом с помощью шаблона в диспетчере ресурсов"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/08/2016"
   ms.author="telmos" />

# Развертывание виртуальной машины со статическим общедоступным IP-адресом с помощью шаблона

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## Ресурсы общедоступного IP-адреса в файле шаблона

Вы можете просмотреть и скачать [образец шаблона](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json).

В разделе ниже показано определение ресурса общедоступного IP-адреса на основе описанного выше сценария.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[variables('webVMSetting').pipName]",
        "location": "[variables('location')]",
        "properties": {
          "publicIPAllocationMethod": "Static"
        },
        "tags": {
          "displayName": "PublicIPAddress - Web"
        }
      },

Обратите внимание на свойство **publicIPAllocationMethod**, которому присвоено значение *Static*. Это свойство может иметь значение либо *Dynamic* (значение по умолчанию), либо *Static*. Установка значения Static гарантирует, что этот общедоступный IP-адрес никогда не изменится.

В разделе ниже показана связь указанного выше общедоступного IP-адреса с сетевым адаптером.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/networkInterfaces",
        "name": "[variables('webVMSetting').nicName]",
        "location": "[variables('location')]",
        "tags": {
          "displayName": "NetworkInterface - Web"
        },
        "dependsOn": [
          "[concat('Microsoft.Network/publicIPAddresses/', variables('webVMSetting').pipName)]",
          "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
          "ipConfigurations": [
            {
              "name": "ipconfig1",
              "properties": {
                "privateIPAllocationMethod": "Static",
                "privateIPAddress": "[variables('webVMSetting').ipAddress]",
                "publicIPAddress": {
                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('webVMSetting').pipName)]"
                },
                "subnet": {
                  "id": "[variables('frontEndSubnetRef')]"
                }
              }
            }
          ]
        }
      },

Обратите внимание на свойство **publicIPAddress**, указывающее **идентификатор** ресурса с именем **variables('webVMSetting').pipName**. Это имя общедоступного IP-ресурса, показанного выше.

Наконец, приведенный выше сетевой адаптер указан в свойстве **networkProfile** создаваемой виртуальной машины.

      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('webVMSetting').nicName)]"
          }
        ]
      }

## Развертывание шаблона с помощью кнопки развертывания

Образец шаблона, который находится в общедоступном репозитории, использует файл параметров, содержащий значения по умолчанию для создания описанного выше сценария. Чтобы развернуть этот шаблон, перейдите по [данной ссылке](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/03-Static-public-IP), нажмите **Deploy to Azure** (Развернуть в Azure), при необходимости замените значения параметров по умолчанию и следуйте указаниям на портале.

## Развертывание шаблона с помощью PowerShell

Чтобы развернуть шаблон, загруженный с помощью PowerShell, выполните описанные ниже действия.

1. Если вы ранее не использовали Azure PowerShell, см. статью [Установка и настройка Azure PowerShell](powershell-install-configure.md) и следуйте инструкциям в шагах с 1 по 3.

2. При необходимости выполните командлет **New-AzureRMResourceGroup** в консоли PowerShell, чтобы создать новую группу ресурсов. Если вы уже создали группу ресурсов, перейдите к шагу 3.

		New-AzureRmResourceGroup -Name StaticPublicIP -Location westus

	Ожидаемые выходные данные:

		ResourceGroupName : StaticPublicIP
		Location          : westus
		ProvisioningState : Succeeded
		Tags              : 
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/StaticPublicIP

3. В консоли PowerShell, запустите командлет **New-AzureRmResourceGroupDeployment** для развертывания шаблона.

		New-AzureRmResourceGroupDeployment -Name DeployVM -ResourceGroupName PIPTEST `
		    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json `
		    -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json

	Ожидаемые выходные данные:

		DeploymentName    : DeployVM
		ResourceGroupName : PIPTEST
		ProvisioningState : Succeeded
		Timestamp         : 1/8/2016 7:04:44 PM
		Mode              : Incremental
		TemplateLink      : 
		                    Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/mas
		                    ter/IaaS-Story/03-Static-public-IP/azuredeploy.json
		                    ContentVersion : 1.0.0.0
		                    
		Parameters        : 
		                    Name                      Type                       Value     
		                    ========================  =========================  ==========
		                    vnetName                  String                     WTestVNet 
		                    vnetPrefix                String                     192.168.0.0/16
		                    frontEndSubnetName        String                     FrontEnd  
		                    frontEndSubnetPrefix      String                     192.168.1.0/24
		                    storageAccountNamePrefix  String                     iaasestd  
		                    stdStorageType            String                     Standard_LRS
		                    osType                    String                     Windows   
		                    adminUsername             String                     adminUser 
		                    adminPassword             SecureString                         
		                    
		Outputs           : 

## Развертывание шаблона с помощью интерфейса командной строки Azure

Чтобы развернуть шаблон с помощью интерфейса командной строки Azure, выполните следующие действия.

1. Если вы еще не пользовались Azure CLI, ознакомьтесь со статьей [Установка и настройка CLI Azure](xplat-cli.md) и следуйте инструкциям вплоть до выбора учетной записи Azure и подписки.
2. Выполните команду **azure config mode**, чтобы переключиться в режим диспетчера ресурсов, как показано ниже.

		azure config mode arm

	Вот результат, ожидаемый для указанной выше команды:

		info:    New mode is arm

3. Откройте [файл параметров](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json), выберите его содержимое и сохраните его в файл на своем компьютере. В этом примере мы сохранили файл параметров в файл *parameters.json*.

4. Выполните командлет **azure group deployment create**, чтобы развернуть новую виртуальную сеть с помощью файлов шаблона и параметров, которые вы загрузили и изменили выше. В списке, который откроется после выполнения команды, будут указаны используемые параметры.

		azure group create -n PIPTEST2 -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json -e parameters.json

	Ожидаемые выходные данные:

		info:    Executing command group create
		+ Getting resource group PIPTEST2
		+ Creating resource group PIPTEST2
		info:    Created resource group PIPTEST2
		+ Initializing template configurations and parameters
		+ Creating a deployment
		info:    Created template deployment "azuredeploy"
		data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/PIPTEST2
		data:    Name:                PIPTEST2
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK

<!---HONumber=AcomDC_0128_2016-->