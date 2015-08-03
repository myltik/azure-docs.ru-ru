<properties pageTitle="Развертывание ресурсов Azure с помощью шаблона" description="Узнайте, как использовать доступные в библиотеке управления ресурсами Azure клиенты для развертывания виртуальной машины, виртуальной сети и учетной записи хранения" services="virtual-machines,virtual-networks,storage" documentationCenter="" authors="davidmu1" manager="timlt" editor="tysonn" tags="azure-resource-manager/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na" 
	ms.topic="article"
	ms.date="06/15/2015"
	ms.author="davidmu"/>

# Развертывание ресурсов Azure с использованием библиотек .NET и шаблона

С помощью групп ресурсов и шаблонов вы можете управлять всеми ресурсами, которые поддерживают работу вашего приложения. В этом учебнике показано, как использовать некоторые из доступных клиентов в библиотеке управления ресурсами Azure и как создать шаблон для развертывания виртуальной машины, виртуальной сети и учетной записи хранения.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Для работы с этим учебником необходимы указанные ниже компоненты.

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Учетная запись хранения Azure.](../storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/ru-ru/download/details.aspx?id=34595) или [Windows Management Framework 4.0.](http://www.microsoft.com/ru-ru/download/details.aspx?id=40855)
- [Azure PowerShell](../powershell-install-configure.md)

На выполнение этих действий требуется примерно 30 минут.

## Шаг 1. Добавление приложения в Azure AD и настройка разрешений

Чтобы использовать Azure AD для аутентификации запросов к диспетчеру ресурсов Azure, необходимо добавить приложение в каталог по умолчанию. Чтобы добавить приложение, выполните указанные ниже действия.

1. Откройте командную строку Azure PowerShell и выполните следующую команду:

        Switch-AzureMode –Name AzureResourceManager

2. Настройте учетную запись Azure, которую вы собираетесь использовать для работы с данным учебником. Выполните следующую команду и при появлении соответствующего запроса введите учетные данные для вашей подписки:

	    Add-AzureAccount

3. В следующей команде замените слово {password} паролем, который вы будете использовать, а затем выполните ее, чтобы создать приложение:

	    New-AzureADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

4. Запишите значение ApplicationId, полученное в результате выполнения предыдущего шага. Оно потребуется при дальнейшей работе с данным учебником:

	![Создание приложения AD](./media/arm-template-deployment/azureapplicationid.png)

	>[AZURE.NOTE]Кроме того, идентификатор приложения отображается в поле идентификатора клиента приложения на портале управления.

5. Замените {application-id} только что записанным идентификатором, а затем создайте субъект-службу для приложения:

        New-AzureADServicePrincipal -ApplicationId {application-id}

6. Настройте разрешение на использование приложения:

	    New-AzureRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## Шаг 2. Создание проекта Visual Studio, файла шаблона и файла параметров

###Создание файла шаблона

С помощью шаблона диспетчера ресурсов Azure можно развертывать ресурсы Azure и управлять ими совокупно, используя описание ресурсов в формате JSON и связанные параметры развертывания. В Visual Studio выполните следующее:

1. Щелкните **Файл** > **Создать** > **Проект**.

2. В разделе **Шаблоны** > **Visual C#** выберите пункт **Консольное приложение**, введите имя и расположение проекта, а затем нажмите кнопку **OK**.

3. В обозревателе решений щелкните правой кнопкой мыши проект, затем выберите **Добавить** > **Новый элемент**.

4.	В окне «Добавление нового элемента» выберите **Текстовый файл**, введите имя *VirtualMachineTemplate.json* и нажмите кнопку **Добавить**.

5.	Откройте файл VirtualMachineTemplate.json и добавьте открывающую и закрывающую скобки, а также обязательные элементы schema и contentVersion:

        {
            "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
            "contentVersion": "1.0.0.0",
        }

6. [Параметры](https://msdn.microsoft.com/library/azure/dn835138.aspx#parameters) не всегда обязательны, но они упрощают управление шаблонами. Они описывают тип значения, значение по умолчанию (если требуется) и, возможно, допустимые значения параметра. В этом учебнике параметры, которые используются для создания виртуальной машины, учетной записи хранения и виртуальной сети, добавлены в шаблон.

    Добавьте элемент parameters и его дочерние элементы после элемента contentVersion:

		{
		  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "location": { "type": "String", "defaultValue" : "West US", "allowedValues": [ "West US", "East US" ] },
            "newStorageAccountName": { "type": "string" },
            "storageAccountType": { "type": "string", "defaultValue": "Standard_LRS", "allowedValues": [ "Standard_LRS", "Standard_GRS" ] },
            "publicIPAddressName": { "type": "string" },
            "publicIPAddressType" : { "type" : "string", "defaultValue" : "Dynamic", "allowedValues" : [ "Dynamic" ] },
            "vmStorageAccountContainerName": { "type": "string", "defaultValue": "vhds" },
            "vmName": { "type": "string" },
            "vmSize": { "type": "string", "defaultValue": "Standard_A0", "allowedValues" : [ "Standard_A0", "Standard_A1" ] },
            "vmSourceImageName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "virtualNetworkName":{ "type" : "string" },
            "addressPrefix": { "type" : "string", "defaultValue" : "10.0.0.0/16" },
            "subnet1Name": { "type" : "string", "defaultValue" : "Subnet-1" },
            "subnet2Name": { "type" : "string", "defaultValue" : "Subnet-2" },
            "subnet1Prefix" : { "type" : "string", "defaultValue" : "10.0.0.0/24" },
            "subnet2Prefix" : { "type" : "string", "defaultValue" : "10.0.1.0/24" },
            "dnsName" : { "type" : "string" },
            "subscriptionId": { "type" : "string" },
            "nicName": { "type" : "string" }
          },
        }

7.	[Переменные](https://msdn.microsoft.com/library/azure/dn835138.aspx#variables) в шаблоне можно использовать для указания значений, которые могут часто изменяться, или значений, которые должны быть созданы из сочетания значений параметров.

    Добавьте элемент variables после раздела parameters:

		{
		  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "location": { "type": "String", "defaultValue" : "West US", "allowedValues": [ "West US", "East US" ] },
            "newStorageAccountName": { "type": "string" },
            "storageAccountType": { "type": "string", "defaultValue": "Standard_LRS", "allowedValues": [ "Standard_LRS", "Standard_GRS" ] },
            "publicIPAddressName": { "type": "string" },
            "publicIPAddressType" : { "type" : "string", "defaultValue" : "Dynamic", "allowedValues" : [ "Dynamic" ] },
            "vmStorageAccountContainerName": { "type": "string", "defaultValue": "vhds" },
            "vmName": { "type": "string" },
            "vmSize": { "type": "string", "defaultValue": "Standard_A0", "allowedValues" : [ "Standard_A0", "Standard_A1" ] },
            "vmSourceImageName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "virtualNetworkName":{ "type" : "string" },
            "addressPrefix": { "type" : "string", "defaultValue" : "10.0.0.0/16" },
            "subnet1Name": { "type" : "string", "defaultValue" : "Subnet-1" },
            "subnet2Name": { "type" : "string", "defaultValue" : "Subnet-2" },
            "subnet1Prefix" : { "type" : "string", "defaultValue" : "10.0.0.0/24" },
            "subnet2Prefix" : { "type" : "string", "defaultValue" : "10.0.1.0/24" },
            "dnsName" : { "type" : "string" },
            "subscriptionId": { "type" : "string" },
            "nicName": { "type" : "string" }
          },
          "variables": {
            "sourceImageName" : "[concat('/',parameters('subscriptionId'),'/services/images/',parameters('vmSourceImageName'))]",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',parameters('subnet1Name'))]"
          },
        }

8.	Далее в шаблоне определяются [ресурсы](https://msdn.microsoft.com/library/azure/dn835138.aspx#resources), например виртуальная машина, виртуальная сеть и учетная запись хранения.

    Добавьте раздел resources после раздела variables:

		{
		  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "location": { "type": "String", "defaultValue" : "West US", "allowedValues": [ "West US", "East US" ] },
            "newStorageAccountName": { "type": "string" },
            "storageAccountType": { "type": "string", "defaultValue": "Standard_LRS", "allowedValues": [ "Standard_LRS", "Standard_GRS" ] },
            "publicIPAddressName": { "type": "string" },
            "publicIPAddressType" : { "type" : "string", "defaultValue" : "Dynamic", "allowedValues" : [ "Dynamic" ] },
            "vmStorageAccountContainerName": { "type": "string", "defaultValue": "vhds" },
            "vmName": { "type": "string" },
            "vmSize": { "type": "string", "defaultValue": "Standard_A0", "allowedValues" : [ "Standard_A0", "Standard_A1" ] },
            "vmSourceImageName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "virtualNetworkName":{ "type" : "string" },
            "addressPrefix": { "type" : "string", "defaultValue" : "10.0.0.0/16" },
            "subnet1Name": { "type" : "string", "defaultValue" : "Subnet-1" },
            "subnet2Name": { "type" : "string", "defaultValue" : "Subnet-2" },
            "subnet1Prefix" : { "type" : "string", "defaultValue" : "10.0.0.0/24" },
            "subnet2Prefix" : { "type" : "string", "defaultValue" : "10.0.1.0/24" },
            "dnsName" : { "type" : "string" },
            "subscriptionId": { "type" : "string" },
            "nicName": { "type" : "string" }
          },
          "variables": {
            "sourceImageName" : "[concat('/',parameters('subscriptionId'),'/services/images/',parameters('vmSourceImageName'))]",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',parameters('subnet1Name'))]"
          },
          "resources": [ {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('newStorageAccountName')]",
            "location": "[parameters('location')]",
            "properties": { "accountType": "[parameters('storageAccountType')]" }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
              "publicIPAllocationMethod": "[parameters('publicIPAddressType')]",
              "dnsSettings": { "domainNameLabel": "[parameters('dnsName')]" }
            }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
              "addressSpace": { "addressPrefixes": [ "[parameters('addressPrefix')]" ] },
              "subnets": [ {
                "name": "[parameters('subnet1Name')]",
                "properties": { "addressPrefix": "[parameters('subnet1Prefix')]" }
              },
              {
                "name": "[parameters('subnet2Name')]",
                "properties": { "addressPrefix": "[parameters('subnet2Prefix')]" }
              } ]
            }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[parameters('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
              "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
            ],
            "properties": {
              "ipConfigurations": [ {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": {
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddressName'))]"
                  },
                  "subnet": { "id": "[variables('subnet1Ref')]" }
                }
              } ]
            }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[parameters('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
              "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
            ],
            "properties": {
              "hardwareProfile": { "vmSize": "[parameters('vmSize')]" },
              "osProfile": {
                "computername": "[parameters('vmName')]",
                "adminUsername": "[parameters('adminUsername')]",
                "adminPassword": "[parameters('adminPassword')]",
                "windowsProfile": { "provisionVMAgent": "true" }
              },
              "storageProfile": {
                "sourceImage": { "id": "[variables('sourceImageName')]" },
                "destinationVhdsContainer" : "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmStorageAccountContainerName'),'/')]"
              },
              "networkProfile": {
                "networkInterfaces" : [ {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                } ]
              }
            }
          } ]
        }

9.	Сохраните созданный файл шаблона.

###Создание файла параметров

Чтобы задать значения для параметров ресурсов, которые были определены в шаблоне, вы создаете файл параметров, содержащий значения, и передаете его в диспетчер ресурсов с шаблоном. В Visual Studio выполните следующее:

1.	В обозревателе решений щелкните правой кнопкой мыши проект, затем выберите **Добавить** > **Новый элемент**.

2.	В окне «Добавление нового элемента» выберите **Текстовый файл**, введите имя *Parameters.json* и нажмите кнопку **Добавить**.

3.	Откройте файл parameters.json и добавьте следующее содержимое JSON:

        {
          "contentVersion": "1.0.0.0",
          "parameters": {
            "vmName": { "value": "mytestvm1" },
            "newStorageAccountName": { "value": "mytestsa1" },
            "storageAccountType": { "value": "Standard_LRS" },
            "publicIPaddressName": { "value": "mytestip1" },
            "location": { "value": "West US" },
            "vmStorageAccountContainerName": { "value": "vhds" },
            "vmSize": { "value": "Standard_A1" },
            "subscriptionId": { "value": "{subscription-id}" },
            "vmSourceImageName": { "value": "{source-image-name}" },
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" },
            "virtualNetworkName": { "value": "mytestvn1" },
            "dnsName": { "value": "mytestdns1" },
            "nicName": { "value": "mytestnic1" }
          }
        }

    >[AZURE.NOTE]Имена образов виртуальных жестких дисков в коллекции образов регулярно изменяются, поэтому для развертывания виртуальной машины необходимо получить текущее имя образа. В разделе [Управление образами с помощью Windows PowerShell](https://msdn.microsoft.com/library/azure/dn790330.aspx) прочитайте сведения о том, как сделать это, и замените текст {source-image-name} именем файла виртуального жесткого диска, который необходимо использовать. Имя может иметь следующий вид: a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201412.01-en.us-127GB.vhd. Замените текст {subscription-id} идентификатором своей подписки.


4.	Сохраните созданный файл параметров.

Диспетчер ресурсов Azure обращается к файлу шаблона и файлу параметров из учетной записи хранения Azure. Чтобы поместить файлы в хранилище, выполните следующее:

1.	Откройте обозреватель сервера и перейдите к контейнеру в своей учетной записи хранения, куда требуется поместить файл. В этом учебнике контейнер, где расположен шаблон, называется templates.

2.	В правом верхнем углу области контейнера templates щелкните значок отправки большого двоичного объекта, перейдите к файлу VirtualMachineTemplate.json, который вы создали, и щелкните **Открыть**.

3. Снова щелкните значок отправки большого двоичного объекта, перейдите к файлу Parameters.json, который был создан, и щелкните **Открыть**.

##Шаг 3. Установка библиотек

Самый простой способ установить библиотеки, необходимые для завершения работы с данным учебником, — это использовать пакеты NuGet. Вам потребуется установить библиотеку управления ресурсами Azure и библиотеку аутентификации Azure Active Directory. Чтобы добавить эти библиотеки в Visual Studio, выполните следующее:

1.	В обозревателе решений щелкните правой кнопкой мыши имя проекта и выберите пункт **Управление пакетами NuGet**.

2.	В поле поиска введите текст *Active Directory*, нажмите кнопку **Установить** для пакета библиотеки аутентификации Active Directory, а затем следуйте указаниям по установке пакета.

3.	В верхней части страницы выберите пункт **Включить предварительный выпуск**. В поле поиска введите текст *Azure Resource Management*, нажмите кнопку **Установить**, чтобы установить библиотеки управления ресурсами Azure для .NET, а затем следуйте указаниям по установке пакета.

Теперь вы готовы использовать библиотеки для создания приложения.

##Шаг 4. Создание учетных данных, используемых для аутентификации запросов

Теперь, когда создано приложение Azure Active Directory и установлена библиотека аутентификации, на основе информации о приложении можно создать учетные данные, которые будут использоваться для аутентификации запросов к диспетчеру ресурсов Azure. Выполните следующие действия.

1.	Откройте файл Program.cs для созданного вами проекта и добавьте в начало файла указанные ниже операторы using:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.Resources.Models;

2.	Добавьте в класс Program указанный ниже метод, чтобы получить токен, необходимый для создания учетных данных:

        private static string GetAuthorizationHeader()
        {
          ClientCredential cc = new ClientCredential("{application-id}", "{password}");
            var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
            var result = context.AcquireToken("https://management.azure.com/", cc);
          if (result == null)
          {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.AccessToken;

          return token;
        }

	Замените {application-id} записанным ранее идентификатором приложения, {password} — паролем, заданным вами для приложения AD, и {tenant-id} — идентификатором клиента для своей подписки. Чтобы узнать идентификатор клиента, выполните команду Get-AzureSubscription.

3.	Чтобы создать учетные данные, добавьте указанный ниже код в метод Main в файле Program.cs:

		var token = GetAuthorizationHeader();
		var credential = new TokenCloudCredentials("{subscription-id}", token);

4.	Сохраните файл Program.cs.


##Шаг 5. Добавление кода для развертывания шаблона

Ресурсы всегда развертываются из шаблона в группу ресурсов. Чтобы создать группу ресурсов, в которую будут развернуты ресурсы, используйте классы [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) и [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx).

1.	Чтобы создать группу ресурсов, добавьте указанный ниже метод в класс Program:

		public async static void CreateResourceGroup(TokenCloudCredentials credential)
		{
		  Console.WriteLine("Creating the resource group...");
		  var resourceGroup = new ResourceGroup { Location = "West US" };
		  using (var resourceManagementClient = new ResourceManagementClient(credential))
		  {
		    var rgResult = await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync("mytestrg1", resourceGroup);
		    Console.WriteLine(rgResult.StatusCode);
		  }
		}

2.	Для вызова только что добавленного метода добавьте указанный ниже код в метод Main:

		CreateResourceGroup(credential);
		Console.ReadLine();

3.	Добавьте следующий метод в класс Program для развертывания ресурсов в группу ресурсов с помощью шаблона, определенного вами:

		public async static void CreateTemplateDeployment(TokenCloudCredentials credential)
		{
		  Console.WriteLine("Creating the template deployment...");
		  var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
		  {
		    Mode = DeploymentMode.Incremental,
		    TemplateLink = new TemplateLink
		    {
		      Uri = new Uri("https://{storage-account-name}.blob.core.windows.net/templates/VirtualMachineTemplate.json")
			},
			ParametersLink = new ParametersLink
			{
			  Uri = new Uri("https://{storage-account-name}.blob.core.windows.net/templates/Parameters.json")
			}
		  };
		  using (var templateDeploymentClient = new ResourceManagementClient(credential))
		  {
		    var dpResult = await templateDeploymentClient.Deployments.CreateOrUpdateAsync("mytestrg1", "mytestdp1", deployment);
			Console.WriteLine(dpResult.StatusCode);
		  }
		}

	Замените {storage-account-name} именем учетной записи, в которую вы ранее поместили файлы.

4.	Для вызова только что добавленного метода добавьте указанный ниже код в метод Main:

		CreateTemplateDeployment(credential);
		Console.ReadLine();

##Шаг 6. Добавление кода для удаления ресурсов

Так как за использование ресурсов Azure взимается плата, рекомендуется всегда удалять ресурсы, которые больше не нужны. Не нужно отдельно удалять каждый ресурс из группы ресурсов. Можно удалить группу ресурсов, тогда все ее ресурсы будут автоматически удалены.

1.	Добавьте указанный ниже метод в класс Program, чтобы удалить группу ресурсов:

		public async static void DeleteResourceGroup(TokenCloudCredentials credential)
		{
		  using (var resourceGroupClient = new ResourceManagementClient(credential))
		  {
		    var rgResult = await resourceGroupClient.ResourceGroups.DeleteAsync("mytestrg1");
			Console.WriteLine(rgResult.StatusCode);
		  }
		}

2.	Для вызова только что добавленного метода добавьте указанный ниже код в метод Main:

		DeleteResourceGroup(credential);
		Console.ReadLine();

##Шаг 7. Запуск консольного приложения

1.	Чтобы запустить консольное приложение, нажмите кнопку **Запустить** в Visual Studio, а затем войдите в Azure AD с помощью того же имени пользователя и пароля, которые вы используете для подписки.

2.	Для создания каждого ресурса нажимайте клавишу **ВВОД** после возврата каждого кода состояния. После создания виртуальной машины выполните указанное ниже действие, прежде чем нажать клавишу ВВОД и удалить все ресурсы.

	На полное выполнение этого консольного приложения потребуется примерно 5 минут. Прежде чем нажать клавишу ВВОД и начать удаление ресурсов, потратьте несколько минут и проверьте на портале предварительной версии Azure, созданы ли эти ресурсы.

3. Чтобы отобразить состояние ресурсов, на портале предварительной версии Azure откройте раздел журналов аудита.

	![Создание приложения AD](./media/arm-template-deployment/crpportal.png)

<!---HONumber=July15_HO4-->