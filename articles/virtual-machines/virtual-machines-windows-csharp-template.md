<properties
	pageTitle="Развертывание виртуальной машины с помощью C# и шаблона Resource Manager | Microsoft Azure"
	description="Узнайте, как использовать C# и шаблон Resource Manager для развертывания виртуальной машины Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="davidmu"/>

# Развертывание виртуальной машины Azure с помощью C# и шаблона Resource Manager

С помощью групп ресурсов и шаблонов вы можете управлять всеми ресурсами, которые поддерживают работу вашего приложения. В этой статье демонстрируется настройка проверки подлинности и хранилища с использованием Azure PowerShell, а также последующие сборка и развертывание шаблона при помощи C# для создания ресурсов Azure.

Для работы с этим учебником необходимы указанные ниже компоненты.

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) или [Windows Management Framework 4.0.](http://www.microsoft.com/download/details.aspx?id=40855)
- [Токен проверки подлинности](../resource-group-authenticate-service-principal.md)

На выполнение этих действий требуется примерно 30 минут.

## Шаг 1. Установка Azure PowerShell

Сведения о том, как установить последнюю версию Azure PowerShell, выбрать нужную подписку и войти в учетную запись Azure, см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).
    
## Шаг 2. Создание группы ресурсов для хранения шаблона

Все ресурсы должны развертываться в группе ресурсов. Дополнительные сведения см. в статье [Общие сведения об Azure Resource Manager](../resource-group-overview.md).

1. Получите список доступных расположений, где можно создавать ресурсы.

	    Get-AzureRmLocation | sort Location | Select Location
        
2. Замените значение **$locName** расположением из списка, например **centralus**. Создайте переменную.

        $locName = "location name"
        
3. Замените значение **$rgName** именем новой группы ресурсов. Создайте переменную и группу ресурсов.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    Вы увидите нечто вроде этого:
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1
    
## Шаг 3. Создание учетной записи хранения и контейнера шаблонов

Учетная запись хранения необходима для хранения шаблона, который вы собираетесь создать и развернуть.

1. Замените значение $stName именем учетной записи хранения (только строчные буквы и цифры). Проверьте имя на уникальность.

        $stName = "storage account name"
        Get-AzureRmStorageAccountNameAvailability $stName

    Если команда возвращает значение **True**, предложенное имя является уникальным.
    
2. Теперь выполните эту команду для создания учетной записи хранения.
    
        New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -SkuName "Standard_LRS" -Kind "Storage" -Location $locName
        
3. Замените значение {blob-storage-endpoint} значением конечной точки хранилища BLOB-объектов в вашей учетной записи. Замените свойство {storage-account-name} именем вашей учетной записи хранения. Замените значение {primary-storage-key} значением первичного ключа доступа. Выполните эти команды для создания контейнера, в котором будут храниться файлы. Значения конечной точки и ключа можно получить на портале Azure.

        $ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint={blob-storage-endpoint};AccountName={storage-account-name};AccountKey={primary-storage-key}"
        $ctx = New-AzureStorageContext -ConnnectionString $ConnectionString
        New-AzureStorageContainer -Name "templates" -Permission Blob -Context $ctx

## Шаг 3. Создание проекта Visual Studio, файла шаблона и файла параметров

### Создание файла шаблона

С помощью шаблона диспетчера ресурсов Azure можно развертывать ресурсы Azure и управлять ими совокупно, используя описание ресурсов в формате JSON и связанные параметры развертывания. Шаблон, создаваемый в этом руководстве, похож на шаблон, который можно найти в коллекции шаблонов. Дополнительные сведения см. в статье [Deploy a simple Windows VM in West US](https://azure.microsoft.com/documentation/templates/101-vm-simple-windows/) (Развертывание простой виртуальной машины Windows в западной части США).

В Visual Studio выполните описанные ниже действия.

1. Последовательно выберите пункты **Файл**, **Создать** и **Проект**.

2. В разделе **Шаблоны** > **Visual C#** выберите пункт **Консольное приложение**, введите имя и расположение проекта, а затем нажмите кнопку **OK**.

3. В обозревателе решений щелкните правой кнопкой мыши проект, затем выберите **Добавить** > **Новый элемент**.

4. Щелкните элемент "Сеть", выберите пункт "Файл JSON", введите имя *VirtualMachineTemplate.json* и нажмите кнопку **Добавить**.

5. В скобках файла VirtualMachineTemplate.json добавьте обязательные элементы schema и contentVersion:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
        }

6. [Параметры](../resource-group-authoring-templates.md#parameters) не всегда обязательны, но они упрощают управление шаблонами. Они описывают тип значения, значение по умолчанию (если требуется) и, возможно, допустимые значения параметра. В этом руководстве параметры, которые вы используете для создания виртуальной машины, учетной записи хранения и виртуальной сети, добавлены в шаблон. Добавьте элемент parameters и его дочерние элементы после элемента contentVersion:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "dnsNameForPublicIP": { "type": "string" }
          },
        }

7. [Переменные](../resource-group-authoring-templates.md#variables) в шаблоне можно использовать для указания значений, которые могут часто изменяться, или значений, которые должны быть созданы из сочетания значений параметров. Добавьте элемент variables после раздела parameters:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "type": "string" },
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "dnsNameForPublicIP": { "type": "string" },
          },
          "variables": {
            "location": "West US",
            "imagePublisher": "MicrosoftWindowsServer",
            "imageOffer": "WindowsServer",
            "windowsOSVersion": "2012-R2-Datacenter",
            "OSDiskName": "osdiskforwindowssimple",
            "nicName": "myVMnic1",
            "addressPrefix": "10.0.0.0/16",
            "subnetName": "Subnet",
            "subnetPrefix": "10.0.0.0/24",
            "storageAccountType": "Standard_LRS",
            "publicIPAddressName": "myPublicIP",
            "publicIPAddressType": "Dynamic",
            "vmStorageAccountContainerName": "vhds",
            "vmName": "MyWindowsVM",
            "vmSize": "Standard_A2",
            "virtualNetworkName": "MyVNET",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
            "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]"  
          },
        }

8. Далее в шаблоне определяются [ресурсы](../resource-group-authoring-templates.md#resources), например виртуальная машина, виртуальная сеть и учетная запись хранения. Добавьте раздел resources после раздела variables:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "type": "string" },
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "dnsNameForPublicIP": { "type": "string" }
          },
          "variables": {
            "location": "West US",
            "imagePublisher": "MicrosoftWindowsServer",
            "imageOffer": "WindowsServer",
            "OSDiskName": "osdiskforwindowssimple",
            "windowsOSVersion": "2012-R2-Datacenter",
            "nicName": "myVMnic1",
            "addressPrefix": "10.0.0.0/16",
            "subnetName": "Subnet",
            "subnetPrefix": "10.0.0.0/24",
            "storageAccountType": "Standard_LRS",
            "publicIPAddressName": "myPublicIP",
            "publicIPAddressType": "Dynamic",
            "vmStorageAccountContainerName": "vhds",
            "vmName": "MyWindowsVM",
            "vmSize": "Standard_A2",
            "virtualNetworkName": "MyVNET",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
          },
          "resources": [
            {
              "apiVersion": "2015-06-15",
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[parameters('newStorageAccountName')]",
              "location": "[variables('location')]",
              "properties": {
                "accountType": "[variables('storageAccountType')]"
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "[variables('publicIPAddressName')]",
              "location": "[variables('location')]",
              "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                  "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "[variables('virtualNetworkName')]",
              "location": "[variables('location')]",
              "properties": {
                "addressSpace": {
                  "addressPrefixes": [ "[variables('addressPrefix')]" ]
                },
                "subnets": [ {
                  "name": "[variables('subnetName')]",
                  "properties": {
                    "addressPrefix": "[variables('subnetPrefix')]"
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "[variables('nicName')]",
              "location": "[variables('location')]",
              "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))]"
                    },
                    "subnet": {
                      "id": "[variables('subnetRef')]"
                    }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "[variables('vmName')]",
              "location": "[variables('location')]",
              "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
                "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
              ],
              "properties": {
                "hardwareProfile": {
                  "vmSize": "[variables('vmSize')]"
                },
                "osProfile": {
                  "computerName": "[variables('vmName')]",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]",
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "[variables('imagePublisher')]",
                    "offer": "[variables('imageOffer')]",
                    "sku": "[variables('windowsOSVersion')]",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "osdisk",
                    "vhd": {
                      "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                  } ]
                }
              }
            } ]
          }
      
9. Сохраните созданный файл шаблона.

### Создание файла параметров

Чтобы задать значения для параметров ресурсов, которые были определены в шаблоне, вы создаете файл параметров, содержащий значения, и передаете его в диспетчер ресурсов с шаблоном. В Visual Studio выполните описанные ниже действия.

1. В обозревателе решений щелкните правой кнопкой мыши проект, затем выберите **Добавить** > **Новый элемент**.

2. Щелкните элемент "Сеть", выберите пункт "Файл JSON", введите имя *Parameters.json* и нажмите кнопку **Добавить**.

3. Откройте файл parameters.json и добавьте следующее содержимое JSON:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "value": "mytestsa1" },
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" },
            "dnsNameForPublicIP": { "value": "mytestdns1" }
          }
        }

    >[AZURE.NOTE] В этой статье создается виртуальная машина под управлением одной из версий операционной системы Windows Server. Дополнительные сведения о выборе других образов см. в статье [Просмотр и выбор образов виртуальных машин Linux в Azure с помощью интерфейса командной строки или оболочки PowerShell](virtual-machines-linux-cli-ps-findimage.md).

4. Сохраните созданный файл параметров.

### Отправка файлов и установка разрешения на их использование 

Диспетчер ресурсов Azure обращается к файлу шаблона и файлу параметров из учетной записи хранения Azure. Чтобы поместить файлы в первое созданное вами хранилище, выполните описанные ниже действия.

1. Откройте обозреватель облака и перейдите в созданный ранее контейнер шаблонов в учетной записи хранения.

2. В правом верхнем углу окна контейнера шаблона щелкните значок отправки большого двоичного объекта, перейдите к файлу VirtualMachineTemplate.json, который вы создали, и щелкните **Открыть**.

3. Снова щелкните значок отправки большого двоичного объекта, перейдите к файлу Parameters.json, который был создан, и щелкните **Открыть**.

## Шаг 4. Установка библиотек

Самый простой способ установить библиотеки, необходимые для завершения работы с данным учебником, — это использовать пакеты NuGet. Вам потребуется установить библиотеку управления ресурсами Azure и библиотеку аутентификации Azure Active Directory. Чтобы добавить эти библиотеки в Visual Studio, выполните описанные ниже действия.

1. В обозревателе решений щелкните правой кнопкой мыши имя проекта и выберите пункт **Управление пакетами NuGet**.

2. В поле поиска введите текст *Active Directory*, нажмите кнопку **Установить** для пакета библиотеки аутентификации Active Directory, а затем следуйте указаниям по установке пакета.

4. В верхней части страницы выберите пункт **Включить предварительный выпуск**. В поле поиска введите *Microsoft.Azure.ResourceManager* и нажмите кнопку **Установить**, чтобы установить библиотеки управления ресурсами Microsoft Azure, а затем следуйте указаниям по установке пакета.

Теперь вы готовы использовать библиотеки для создания приложения.

##Шаг 5. Создание учетных данных, используемых для проверки подлинности запросов

Теперь, когда создано приложение Azure Active Directory и установлена библиотека проверки подлинности, можно на основе сведений о приложении создать учетные данные, которые будут использоваться для проверки подлинности запросов к Azure Resource Manager. Процедура

1. Откройте файл Program.cs для созданного проекта и добавьте в начало файла следующие операторы using:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.Resources.Models;
        using Microsoft.Rest;

2.	Добавьте в класс Program этот метод, чтобы получить токен, необходимый для создания учетных данных:

        private static string GetAuthorizationHeader()
        {
          ClientCredential cc = new ClientCredential("{application-id}", "{password}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var result = context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (result == null)
          {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.Result.AccessToken;

          return token;
        }

    Замените {application-id} записанным ранее идентификатором приложения, {password} — паролем, заданным вами для приложения AD, и {tenant-id} — идентификатором клиента для своей подписки. Чтобы узнать идентификатор клиента, выполните команду Get-AzureRmSubscription.

3. Чтобы создать учетные данные, добавьте этот код в метод Main в файле Program.cs:

        var token = GetAuthorizationHeader();
        var credential = new TokenCredentials(token);

4. Сохраните файл Program.cs.

## Шаг 6. Добавление кода для развертывания шаблона

На этом этапе, чтобы создать группу, в которую будут развернуты ресурсы, используйте классы [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) и [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx).

1. Добавьте переменные в метод Main класса Program, чтобы указать имена, которые вы хотите использовать для ресурсов, расположение ресурсов (например, "Центральная часть США"), данные учетной записи администратора и идентификатор вашей подписки:

        var groupName = "resource group name";
        var storageName = "storage account name";
        var vmName = "virtual machine name";  
        var deploymentName = "deployment name";
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
        var location = "location name";
        var subscriptionId = "subsciption id";

    Замените все значения переменных именами и идентификаторами, которые вы хотите использовать. Чтобы узнать идентификатор подписки, выполните команду Get-AzureRmSubscription. Значение переменной storageName — имя учетной записи хранилища хранения шаблона.
    
2. Чтобы создать группу ресурсов, добавьте этот метод в класс Program:

        public static void CreateResourceGroup(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location)
        {
          Console.WriteLine("Creating the resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential);
          resourceManagementClient.SubscriptionId = subscriptionId;
          var resourceGroup = new ResourceGroup {
            Location = location
          };
          var rgResult = resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, resourceGroup);
          Console.WriteLine(rgResult.Properties.ProvisioningState);
        }

2. Для вызова только что добавленного метода добавьте этот код в метод Main:

        CreateResourceGroup(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.ReadLine();

3. Добавьте следующий метод в класс Program для развертывания ресурсов в группу с помощью шаблона, который вы определили:

        public static void CreateTemplateDeployment(
          TokenCredentials credential,
          string groupName,
          string storageName,
          string deploymentName,
          string subscriptionId)
        {
          Console.WriteLine("Creating the template deployment...");
          var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = "https://" + storageName + ".blob.core.windows.net/templates/VirtualMachineTemplate.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = "https://" + storageName + ".blob.core.windows.net/templates/Parameters.json"
            }
          };
          var resourceManagementClient = new ResourceManagementClient(credential);
          resourceManagementClient.SubscriptionId = subscriptionId;
          var dpResult = resourceManagementClient.Deployments.CreateOrUpdate(
            groupName,
            deploymentName,
            deployment);
          Console.WriteLine(dpResult.Properties.ProvisioningState);
        }

4. Для вызова только что добавленного метода добавьте этот код в метод Main:

        CreateTemplateDeployment(
          credential,
          groupName",
          storageName,
          deploymentName,
          subscriptionId);
        Console.ReadLine();

##Шаг 7. Добавление кода для удаления ресурсов

Так как за использование ресурсов Azure взимается плата, рекомендуется всегда удалять ресурсы, которые больше не нужны. Не нужно отдельно удалять каждый ресурс из группы ресурсов. Просто удалите группу ресурсов, и все ее ресурсы будут автоматически удалены.

1.	Чтобы удалить группу ресурсов, добавьте этот метод в класс Program:

        public static void DeleteResourceGroup(
          TokenCredentials credential,
          string groupName)
        {
          Console.WriteLine("Deleting resource group...");
          var resourceGroupClient = new ResourceManagementClient(credential);
          resourceGroupClient.ResourceGroups.DeleteAsync(groupName);
        }

2.	Для вызова только что добавленного метода добавьте этот код в метод Main:

        DeleteResourceGroup(
          credential,
          groupName);
        Console.ReadLine();

##Шаг 8. Запуск консольного приложения

1.	Чтобы запустить консольное приложение, нажмите кнопку **Запустить** в Visual Studio, а затем войдите в Azure AD с помощью того же имени пользователя и пароля, которые вы используете для подписки.

2.	После появления состояния "Принято" нажмите клавишу **Ввод**.

	На полное выполнение этого консольного приложения потребуется примерно 5 минут. Прежде чем нажать клавишу "ВВОД" и начать удаление ресурсов, потратьте несколько минут и проверьте на портале Azure, созданы ли эти ресурсы.

3. Чтобы отобразить состояние ресурсов, на портале Azure откройте раздел журналов аудита.

	![Поиск журналов аудита на портале Azure](./media/virtual-machines-windows-csharp-template/crpportal.png)

## Дальнейшие действия

- При возникновении проблем с развертыванием ознакомьтесь со статьей [Устранение неполадок развертываний групп ресурсов с помощью портала Azure](../resource-manager-troubleshoot-deployments-portal.md).
- Узнайте, как управлять созданной виртуальной машиной, прочитав статью [Управление виртуальными машинами с помощью диспетчера ресурсов Azure и PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0622_2016-->