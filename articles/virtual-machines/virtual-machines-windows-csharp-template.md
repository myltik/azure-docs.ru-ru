<properties
	pageTitle="Развертывание ресурсов Azure с помощью шаблона | Microsoft Azure"
	description="Узнайте, как использовать некоторые из доступных клиентов в библиотеке управления ресурсами Azure для развертывания виртуальной машины, виртуальной сети и учетной записи хранения."
	services="virtual-machines-windows,virtual-networks,storage"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="davidmu"/>

# Развертывание ресурсов Azure с использованием библиотек .NET и шаблона

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.


С помощью групп ресурсов и шаблонов вы можете управлять всеми ресурсами, которые поддерживают работу вашего приложения. В этом учебнике показано, как использовать некоторые из доступных клиентов в библиотеке управления ресурсами Azure и как создать шаблон для развертывания виртуальной машины, виртуальной сети и учетной записи хранения.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Для работы с этим учебником необходимы указанные ниже компоненты.

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Учетная запись хранения Azure.](../storage/storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) или [Windows Management Framework 4.0.](http://www.microsoft.com/download/details.aspx?id=40855)

>[AZURE.NOTE] Создаваемая на этом этапе учетная запись хранения используется для хранения шаблона. Другая учетная запись хранения создается при развертывании шаблона, используемого для хранения диска для виртуальной машины. Создайте контейнер в шаблонах с именем этой учетной записи хранения.

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

На выполнение этих действий требуется примерно 30 минут.

## Шаг 1. Добавление приложения в Azure AD и настройка разрешений

Чтобы использовать Azure AD для аутентификации запросов к диспетчеру ресурсов Azure, необходимо добавить приложение в каталог по умолчанию. Чтобы добавить приложение, выполните указанные ниже действия.

1. Откройте командную строку Azure PowerShell и выполните следующую команду. При появлении соответствующего запроса введите учетные данные своей подписки.

			Login-AzureRmAccount

2. В следующей команде замените слово "{password}" паролем, который вы будете использовать, а затем выполните ее, чтобы создать приложение:

			New-AzureRmADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

	>[AZURE.NOTE] Запишите идентификатор приложения, который возвращается после создания приложения, так как он потребуется для следующего шага. Кроме того, идентификатор приложения отображается в поле идентификатора клиента приложения в разделе Active Directory портала Azure.

3. Замените {application-id} только что записанным идентификатором, а затем создайте субъект-службу для приложения:

			New-AzureRmADServicePrincipal -ApplicationId {application-id}

4. Настройте разрешение на использование приложения:

			New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## Шаг 2. Создание проекта Visual Studio, файла шаблона и файла параметров

### Создание файла шаблона

С помощью шаблона диспетчера ресурсов Azure можно развертывать ресурсы Azure и управлять ими совокупно, используя описание ресурсов в формате JSON и связанные параметры развертывания. Шаблон, создаваемый в этом руководстве, похож на шаблон, который можно найти в коллекции шаблонов. Дополнительные сведения см. в разделе [Развертывание простой виртуальной машины Windows в Западном регионе США](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).

В Visual Studio выполните следующее:

1. Щелкните **Файл** > **Создать** > **Проект**.

2. В разделе **Шаблоны** > **Visual C#** выберите пункт **Консольное приложение**, введите имя и расположение проекта, а затем нажмите кнопку **OK**.

3. В обозревателе решений щелкните правой кнопкой мыши проект, затем выберите **Добавить** > **Новый элемент**.

4. В окне «Добавление нового элемента» выберите **Текстовый файл**, введите имя *VirtualMachineTemplate.json* и нажмите кнопку **Добавить**.

5. Откройте файл VirtualMachineTemplate.json и добавьте открывающую и закрывающую скобки, а также обязательные элементы schema и contentVersion:

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
	}
	```

6. [Параметры](../resource-group-authoring-templates.md#parameters) не всегда обязательны, но они упрощают управление шаблонами. Они описывают тип значения, значение по умолчанию (если требуется) и, возможно, допустимые значения параметра. В этом учебнике параметры, которые используются для создания виртуальной машины, учетной записи хранения и виртуальной сети, добавлены в шаблон.

    Добавьте элемент parameters и его дочерние элементы после элемента contentVersion:

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUserName": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": [ "2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter" ]
			}
		},
 	}
	```

7. [Переменные](../resource-group-authoring-templates.md#variables) в шаблоне можно использовать для указания значений, которые могут часто изменяться, или значений, которые должны быть созданы из сочетания значений параметров.

    Добавьте элемент variables после раздела parameters:

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUsername": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": ["2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter"]
			}
		},
		"variables": {
			"location": "West US",
			"imagePublisher": "MicrosoftWindowsServer",
			"imageOffer": "WindowsServer",
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
	```

8. Далее в шаблоне определяются [ресурсы](../resource-group-authoring-templates.md#resources), например виртуальная машина, виртуальная сеть и учетная запись хранения.

    Добавьте раздел resources после раздела variables:

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUsername": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": [ "2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter"]
			}
		},
		"variables": {
			"location": "West US",
			"imagePublisher": "MicrosoftWindowsServer",
			"imageOffer": "WindowsServer",
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
		"resources": [
			{
  			"apiVersion": "2015-05-01-preview",
				"type": "Microsoft.Storage/storageAccounts",
				"name": "[parameters('newStorageAccountName')]",
				"location": "[variables('location')]",
				"properties": {
					"accountType": "[variables('storageAccountType')]"
				}
			},
			{
				"apiVersion": "2015-05-01-preview",
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
				"apiVersion": "2015-05-01-preview",
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
				"apiVersion": "2015-05-01-preview",
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
				"apiVersion": "2015-06-15",
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
							"sku": "[parameters('windowsOSVersion')]",
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
	```

9. Сохраните созданный файл шаблона.

### Создание файла параметров

Чтобы задать значения для параметров ресурсов, которые были определены в шаблоне, вы создаете файл параметров, содержащий значения, и передаете его в диспетчер ресурсов с шаблоном. В Visual Studio выполните следующее:

1. В обозревателе решений щелкните правой кнопкой мыши проект, затем выберите **Добавить** > **Новый элемент**.

2. В окне «Добавление нового элемента» выберите **Текстовый файл**, введите имя *Parameters.json* и нажмите кнопку **Добавить**.

3. Откройте файл parameters.json и добавьте следующее содержимое JSON:

	```
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
	```

    >[AZURE.NOTE] В этом учебнике создается виртуальная машина под управлением одной из версий операционной системы Windows Server. Дополнительные сведения о выборе других образов см. в статье [Просмотр и выбор образов виртуальных машин Linux в Azure с помощью интерфейса командной строки или оболочки PowerShell](virtual-machines-linux-cli-ps-findimage.md).


4. Сохраните созданный файл параметров.

### Передача файлов

Диспетчер ресурсов Azure обращается к файлу шаблона и файлу параметров из учетной записи хранения Azure. Чтобы поместить файлы в первое созданное вами хранилище, выполните следующие действия.

1. Откройте обозреватель сервера и перейдите к контейнеру в своей учетной записи хранения, куда требуется поместить файл. В этом учебнике контейнер, где расположен шаблон, называется templates.

2. В правом верхнем углу области контейнера templates щелкните значок отправки большого двоичного объекта, перейдите к файлу VirtualMachineTemplate.json, который вы создали, и щелкните **Открыть**.

3. Снова щелкните значок отправки большого двоичного объекта, перейдите к файлу Parameters.json, который был создан, и щелкните **Открыть**.

## Шаг 3. Установка библиотек

Самый простой способ установить библиотеки, необходимые для завершения работы с данным учебником, — это использовать пакеты NuGet. Вам потребуется установить библиотеку управления ресурсами Azure и библиотеку аутентификации Azure Active Directory. Чтобы добавить эти библиотеки в Visual Studio, выполните следующее:

1. В обозревателе решений щелкните правой кнопкой мыши имя проекта и выберите пункт **Управление пакетами NuGet**.

2. В поле поиска введите текст *Active Directory*, нажмите кнопку **Установить** для пакета библиотеки аутентификации Active Directory, а затем следуйте указаниям по установке пакета.

3. В верхней части страницы выберите пункт **Включить предварительный выпуск**. В поле поиска введите *Microsoft.Azure.Management.Resources* и нажмите кнопку **Установить**, чтобы установить библиотеки управления ресурсами Microsoft Azure, а затем следуйте указаниям по установке пакета.

Теперь вы готовы использовать библиотеки для создания приложения.

##Шаг 4. Создание учетных данных, используемых для аутентификации запросов

Теперь, когда создано приложение Azure Active Directory и установлена библиотека аутентификации, на основе информации о приложении можно создать учетные данные, которые будут использоваться для аутентификации запросов к диспетчеру ресурсов Azure. Выполните следующие действия.

1. Откройте файл Program.cs для созданного вами проекта и добавьте в начало файла указанные ниже операторы using:

	```
	using Microsoft.Azure;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Azure.Management.Resources;
	using Microsoft.Azure.Management.Resources.Models;
	using Microsoft.Rest;
	```

2.	Добавьте в класс Program указанный ниже метод, чтобы получить токен, необходимый для создания учетных данных:

	```
	private static string GetAuthorizationHeader()
	{
		ClientCredential cc = new ClientCredential("{application-id}", "{password}");
		var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
		var result = context.AcquireTokenAsync("https://management.azure.com/", cc);
		if (result == null)
		{
			throw new InvalidOperationException("Failed to obtain the JWT token");
		}

		string token = result.AccessToken;

		return token;
	}
	```

	Замените {application-id} записанным ранее идентификатором приложения, {password} — паролем, заданным вами для приложения AD, и {tenant-id} — идентификатором клиента для своей подписки. Чтобы узнать идентификатор клиента, выполните команду Get-AzureSubscription.

3. Чтобы создать учетные данные, добавьте указанный ниже код в метод Main в файле Program.cs:

	```
	var token = GetAuthorizationHeader();
	var credential = new TokenCredentials(token);
	```

4. Сохраните файл Program.cs.


## Шаг 5. Добавление кода для развертывания шаблона

Ресурсы всегда развертываются из шаблона в группу ресурсов. Чтобы создать группу ресурсов, в которую будут развернуты ресурсы, используйте классы [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) и [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx).

1. Чтобы создать группу ресурсов, добавьте указанный ниже метод в класс Program:

	```
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
		Console.WriteLine(rgResult.StatusCode.Properties.ProvisioningState);
	}
	```

2. Для вызова только что добавленного метода добавьте указанный ниже код в метод Main.

	```
	CreateResourceGroup(
		credential,
		"{group-name}",
		"{subscription-id}",
		"{location}");
		Console.ReadLine();
	```

	Замените {group-name} именем, которое будет использоваться для группы ресурсов. Замените {subscription-id} идентификатором вашей подписки (это значение можно получить, выполнив команду Get-AzureSubscription). Замените расположение регионом, в котором вы хотите создать ресурсы, например West US.

3. Добавьте следующий метод в класс Program для развертывания ресурсов в группу ресурсов с помощью шаблона, определенного вами:

	```
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
				Uri = "https://{storage-name}.blob.core.windows.net/templates/VirtualMachineTemplate.json"
			},
			ParametersLink = new ParametersLink
			{
				Uri = "https://{storage-name}.blob.core.windows.net/templates/Parameters.json"
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
	```

	Замените {storage-name} именем учетной записи, в которую вы ранее поместили файлы.

4. Для вызова только что добавленного метода добавьте указанный ниже код в метод Main.

	```
	CreateTemplateDeployment(
		credential,
		"{group-name}",
		"{storage-name}",
		"{deployment-name}",
		"{subscription-id}");
	Console.ReadLine();
	```

    Замените {group-name} именем созданной группы ресурсов. Замените {storage-name} именем учетной записи хранения, в которую вы ранее поместили файлы шаблонов. Замените {deployment-name} именем, которое вы хотите использовать для идентификации развертывания набора ресурсов. Замените {subscription-id} идентификатором вашей подписки (это значение можно получить, выполнив команду Get-AzureSubscription).

##Шаг 6. Добавление кода для удаления ресурсов

Так как за использование ресурсов Azure взимается плата, рекомендуется всегда удалять ресурсы, которые больше не нужны. Не нужно отдельно удалять каждый ресурс из группы ресурсов. Можно удалить группу ресурсов, тогда все ее ресурсы будут автоматически удалены.

1.	Добавьте указанный ниже метод в класс Program, чтобы удалить группу ресурсов:

	```
	public static void DeleteResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId)
	{
		Console.WriteLine("Deleting resource group...");
		var resourceGroupClient = new ResourceManagementClient(credential);
		resourceGroupClient.ResourceGroups.DeleteAsync(groupName);
	}
	```

2.	Для вызова только что добавленного метода добавьте указанный ниже код в метод Main.

	```
	DeleteResourceGroup(
		credential,
		"{group-name}",
		"{subscription-id}");
	Console.ReadLine();
	```
    Замените {group-name} именем созданной группы ресурсов. Замените {subscription-id} идентификатором вашей подписки (это значение можно получить, выполнив команду Get-AzureSubscription).

##Шаг 7. Запуск консольного приложения

1.	Чтобы запустить консольное приложение, нажмите кнопку **Запустить** в Visual Studio, а затем войдите в Azure AD с помощью того же имени пользователя и пароля, которые вы используете для подписки.

2.	Нажмите клавишу **ВВОД** после появления состояния "Принято".

	На полное выполнение этого консольного приложения потребуется примерно 5 минут. Прежде чем нажать клавишу "ВВОД" и начать удаление ресурсов, потратьте несколько минут и проверьте на портале Azure, созданы ли эти ресурсы.

3. Чтобы отобразить состояние ресурсов, на портале Azure откройте раздел журналов аудита.

	![Создание приложения AD](./media/virtual-machines-windows-csharp-template/crpportal.png)

<!---HONumber=AcomDC_0323_2016-->

