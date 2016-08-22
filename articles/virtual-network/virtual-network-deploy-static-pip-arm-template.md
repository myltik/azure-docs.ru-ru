<properties
   pageTitle="Развертывание виртуальных машин с общедоступным статическим IP-адресом с помощью шаблона в диспетчере ресурсов | Microsoft Azure"
   description="Узнайте, как развернуть виртуальную машину со статическим общедоступным IP-адресом с помощью шаблона в диспетчере ресурсов"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
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
   ms.date="04/27/2016"
   ms.author="jdial" />

# Развертывание виртуальной машины со статическим общедоступным IP-адресом с помощью шаблона

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] Классическая модель развертывания.

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

Обратите внимание на свойство **publicIPAllocationMethod**, которому присвоено значение *Static*. Это свойство может иметь значение либо *Dynamic* (значение по умолчанию), либо *Static*. Установка значения Static гарантирует, что назначенный общедоступный IP-адрес никогда не изменится.

В разделе ниже показана связь общедоступного IP-адреса с сетевым интерфейсом.

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

Наконец, приведенный выше сетевой интерфейс указан в свойстве **networkProfile** создаваемой виртуальной машины.

      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('webVMSetting').nicName)]"
          }
        ]
      }

## Развертывание шаблона с помощью кнопки развертывания

Образец шаблона, который находится в общедоступном репозитории, использует файл параметров, содержащий значения по умолчанию для создания описанного выше сценария. Для развертывания этого шаблона с помощью кнопки развертывания щелкните **Развертывание в Azure** в файле Readme.md шаблона [виртуальной машины со статическим PIP-адресом](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/03-Static-public-IP). При необходимости замените значения параметров по умолчанию и введите значения для параметров без значений. Следуйте инструкциям на портале, чтобы создать виртуальную машину с общедоступным статическим IP-адресом.

## Развертывание шаблона с помощью PowerShell

Чтобы развернуть шаблон, загруженный с помощью PowerShell, выполните описанные ниже действия.

1. Если вы ранее не использовали Azure PowerShell, см. статью [Установка и настройка Azure PowerShell](../powershell-install-configure.md) и следуйте инструкциям в шагах с 1 по 3.

2. При необходимости выполните командлет **New-AzureRMResourceGroup** в консоли PowerShell, чтобы создать новую группу ресурсов. Если вы уже создали группу ресурсов, перейдите к шагу 3.

		New-AzureRmResourceGroup -Name PIPTEST -Location westus

	Ожидаемые выходные данные:

		ResourceGroupName : PIPTEST
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
		Timestamp         : <Deployment date> <Deployment time>
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

1. Если вы ранее не работали с командной строкой Azure, выполните действия, описанные в статье [Установка и настройка интерфейса командной строки Azure](../xplat-cli-install.md), а затем процедуру подключения CLI к подписке в разделе "Использование имени входа Azure для интерактивной проверки подлинности" статьи [Подключение к среде Azure с использованием интерфейса командной строки Azure (Azure CLI)](../xplat-cli-connect.md).
2. Выполните команду **azure config mode**, чтобы переключиться в режим диспетчера ресурсов, как показано ниже.

		azure config mode arm

	Вот результат, ожидаемый для указанной выше команды:

		info:    New mode is arm

3. Откройте [файл параметров](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json), выберите его содержимое и сохраните его в файл на своем компьютере. Для этого примера параметры сохраняются в файл с именем *parameters.json*. Измените значения параметров в файле по мере необходимости (как минимум, рекомендуется заменить значение параметра adminPassword уникальным сложным паролем).

4. Выполните командлет **azure group deployment create**, чтобы развернуть новую виртуальную сеть с помощью файлов шаблона и параметров, которые вы скачали и изменили раньше. В приведенной ниже команде замените <path> значением пути к папке, в которой сохранен файл.

		azure group create -n PIPTEST2 -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json -e <path>\parameters.json

	Ожидаемый результат (перечисляются используемые значения параметров):

		info:    Executing command group create
		+ Getting resource group PIPTEST2
		+ Creating resource group PIPTEST2
		info:    Created resource group PIPTEST2
		+ Initializing template configurations and parameters
		+ Creating a deployment
		info:    Created template deployment "azuredeploy"
		data:    Id:                  /subscriptions/<Subscription ID>/resourceGroups/PIPTEST2
		data:    Name:                PIPTEST2
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK

<!---HONumber=AcomDC_0810_2016-->