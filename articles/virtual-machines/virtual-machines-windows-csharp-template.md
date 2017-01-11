---
title: "Развертывание виртуальной машины с помощью C# и шаблона Resource Manager | Документация Майкрософт"
description: "Узнайте, как использовать C# и шаблон Resource Manager для развертывания виртуальной машины Azure."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 5d3bcc3c1434b16279778573ccf3034f9ac28a4d
ms.openlocfilehash: aeea0c65a3332197efcd823e29c8f0c4fe0426b3


---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Развертывание виртуальной машины Azure с помощью C# и шаблона Resource Manager
С помощью групп ресурсов и шаблонов вы можете управлять всеми ресурсами, которые поддерживают работу вашего приложения. В этой статье показано, как использовать Visual Studio и C# для настройки проверки подлинности, создания шаблона и последующего развертывания ресурсов Azure с его помощью.

Сначала необходимо выполнить следующие задачи.

* Установите [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
* Проверьте, установлен ли компонент [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) или [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).
* Получите [маркер аутентификации](../resource-group-authenticate-service-principal.md)
* Создайте группу ресурсов с помощью [Azure PowerShell](../resource-group-template-deploy.md), [Azure CLI](../resource-group-template-deploy-cli.md) или [портала Azure](../resource-group-template-deploy-portal.md).

На выполнение этих действий требуется примерно 30 минут.

## <a name="step-1-create-the-visual-studio-project-the-template-file-and-the-parameters-file"></a>Шаг 1. Создание проекта Visual Studio, файла шаблона и файла параметров
### <a name="create-the-template-file"></a>Создание файла шаблона
Шаблон Azure Resource Manager Azure позволяет развертывать ресурсы Azure и управлять ими. Шаблон — это JSON-описание ресурсов и связанных параметров развертывания.

В Visual Studio выполните описанные ниже действия.

1. Последовательно выберите пункты **Файл** > **Создать** > **Проект**.
2. В разделе **Шаблоны** > **Visual C#** выберите пункт **Консольное приложение**, введите имя и расположение проекта, а затем нажмите кнопку **OK**.
3. В обозревателе решений щелкните правой кнопкой мыши проект, затем выберите **Добавить** > **Новый элемент**.
4. Щелкните "Интернет", выберите пункт "JSON-файл", введите имя *VirtualMachineTemplate.json* и нажмите кнопку **Добавить**.
5. В скобках файла VirtualMachineTemplate.json добавьте обязательные элементы schema и contentVersion:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
        }
6. [Параметры](../azure-resource-manager/resource-group-authoring-templates.md#parameters) требуются не всегда, но при развертывании шаблона с их помощью можно передавать значения. Добавьте элемент parameters и его дочерние элементы после элемента contentVersion:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }
7. [Переменные](../azure-resource-manager/resource-group-authoring-templates.md#variables) в шаблоне можно использовать для указания значений, которые могут часто изменяться, или значений, которые должны быть созданы из сочетания значений параметров. Добавьте элемент variables после раздела parameters:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
          },
        }
8. [ресурсы](../azure-resource-manager/resource-group-authoring-templates.md#resources) , например виртуальная машина, виртуальная сеть и учетная запись хранения. Добавьте раздел resources после раздела variables:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "mystorage1",
              "apiVersion": "2015-06-15",
              "location": "[resourceGroup().location]",
              "properties": { "accountType": "Standard_LRS" }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "myip1",
              "location": "[resourceGroup().location]",
              "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": { "domainNameLabel": "mydns1" }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "myvnet1",
              "location": "[resourceGroup().location]",
              "properties": {
                "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
                "subnets": [ {
                  "name": "mysn1",
                  "properties": { "addressPrefix": "10.0.0.0/24" }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "mync1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/publicIPAddresses/myip1",
                "Microsoft.Network/virtualNetworks/myvn1"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
                    },
                    "subnet": { "id": "[variables('subnetRef')]" }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "myvm1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/networkInterfaces/mync1",
                "Microsoft.Storage/storageAccounts/mystorage1"
              ],
              "properties": {
                "hardwareProfile": { "vmSize": "Standard_A1" },
                "osProfile": {
                  "computerName": "myvm1",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "myosdisk1",
                    "vhd": {
                      "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
                  } ]
                }
              }
            } ]
          }
9. Сохраните созданный файл шаблона.

### <a name="create-the-parameters-file"></a>Создание файла параметров
Чтобы задать значения для параметров ресурсов, которые были определены в шаблоне, создайте файл параметров, содержащий значения, используемые при развертывании шаблона. В Visual Studio выполните описанные ниже действия.

1. В обозревателе решений щелкните правой кнопкой мыши проект, затем выберите **Добавить** > **Новый элемент**.
2. Щелкните "Интернет", выберите пункт "JSON-файл", введите имя *Parameters.json* и нажмите кнопку **Добавить**.
3. Откройте файл parameters.json и добавьте следующее содержимое JSON:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }

   > [!NOTE]
   > В этой статье создается виртуальная машина под управлением одной из версий операционной системы Windows Server. Дополнительные сведения о выборе других образов см. в статье [Просмотр и выбор образов виртуальных машин Windows в Azure с помощью оболочки PowerShell или интерфейса командной строки](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
   >
   >
4. Сохраните созданный файл параметров.

## <a name="step-2-install-the-libraries"></a>Шаг 2. Установка библиотек
Самый простой способ установить библиотеки, необходимые для завершения работы с данным учебником, — это использовать пакеты NuGet. Вам потребуется установить библиотеку управления ресурсами Azure и библиотеку проверку подлинности Azure Active Directory. Чтобы добавить эти библиотеки в Visual Studio, выполните следующие действия.

1. В обозревателе решений щелкните правой кнопкой мыши имя проекта и выберите пункт **Управление пакетами NuGet**, затем щелкните "Обзор".
2. В поле поиска введите текст *Active Directory* , нажмите кнопку **Установить** для пакета библиотеки аутентификации Active Directory, а затем следуйте указаниям по установке пакета.
3. В верхней части страницы выберите пункт **Включить предварительный выпуск**. В поле поиска введите *Microsoft.Azure.Management.ResourceManager* и нажмите кнопку **Установить** , чтобы установить библиотеки управления ресурсами Microsoft Azure, а затем следуйте указаниям по установке пакета.

Теперь вы готовы использовать библиотеки для создания приложения.

## <a name="step-3-create-the-credentials-that-are-used-to-authenticate-requests"></a>Действие 3. Создание учетных данных, используемых для проверки подлинности запросов
Создано приложение Azure Active Directory и установлена библиотека проверки подлинности. Теперь на основе сведений о приложении можно создать учетные данные, которые будут использоваться для проверки подлинности запросов к Azure Resource Manager.

1. Откройте файл Program.cs для созданного проекта и добавьте в начало файла следующие операторы using:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Rest;
        using System.IO;
2. Добавьте в класс Program этот метод, чтобы получить токен, необходимый для создания учетных данных:

     private static async Task<AuthenticationResult> GetAccessTokenAsync()   {

       var cc = new ClientCredential("{client-id}", "{client-secret}");
       var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
       var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
       if (token == null)
       {
         throw new InvalidOperationException("Could not get the token.");
       }
       return token;
     }

   Замените {client-id} идентификатором приложения Azure Active Directory, {client-secret} — ключом доступа приложения AD, а {tenant-id} — кодом клиента своей подписки. Чтобы узнать идентификатор клиента, выполните команду Get-AzureRmSubscription. Ключ доступа можно узнать на портале Azure.
3. Чтобы создать учетные данные, добавьте этот код в метод Main в файле Program.cs.

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);
4. Сохраните файл Program.cs.

## <a name="step-4-deploy-the-template"></a>Шаг 4. Развертывание шаблона
На этом этапе используется ранее созданная группа ресурсов. Можно также создать группу ресурсов с помощью классов [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) и [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx).

1. Добавьте в метод Main класса Program переменные для указания имен ранее созданных ресурсов, имени расположения и идентификатора подписки.

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var deploymentName = "deployment name";

    Замените значение groupName именем группы ресурсов. Замените значение deploymentName именем, которое вы хотите использовать для развертывания. Чтобы узнать идентификатор подписки, выполните команду Get-AzureRmSubscription.
2. Добавьте следующий метод в класс Program для развертывания ресурсов в группу с помощью шаблона, который вы определили:

        public static async Task<DeploymentExtended> CreateTemplateDeploymentAsync(
          TokenCredentials credential,
          string groupName,
          string deploymentName,
          string subscriptionId)
        {
          Console.WriteLine("Creating the template deployment...");
          var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            Template = File.ReadAllText("..\\..\\VirtualMachineTemplate.json"),
            Parameters = File.ReadAllText("..\\..\\Parameters.json")
          };
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await resourceManagementClient.Deployments.CreateOrUpdateAsync(
            groupName,
            deploymentName,
            deployment);
        }

    Чтобы развернуть шаблон из учетной записи хранения, замените свойство Template свойством TemplateLink.
3. Для вызова только что добавленного метода добавьте этот код в метод Main.

        var dpResult = CreateTemplateDeploymentAsync(
          credential,
          groupName,
          deploymentName,
          subscriptionId);
        Console.WriteLine(dpResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

## <a name="step-5-delete-the-resources"></a>Шаг 5. Удаление ресурсов
Так как за использование ресурсов Azure взимается плата, рекомендуется всегда удалять ресурсы, которые больше не нужны. Не нужно отдельно удалять каждый ресурс из группы ресурсов. Удалите группу ресурсов, и все ее ресурсы будут автоматически удалены.

1. Чтобы удалить группу ресурсов, добавьте этот метод в класс Program:

     public static async void DeleteResourceGroupAsync(

       TokenCredentials credential,
       string groupName,
       string subscriptionId)
     {

       Console.WriteLine("Deleting resource group...");
       var resourceManagementClient = new ResourceManagementClient(credential)
         { SubscriptionId = subscriptionId };
       await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
     }
2. Для вызова только что добавленного метода добавьте этот код в метод Main.

     DeleteResourceGroupAsync(

       credential,
       groupName,
       subscriptionId);
     Console.ReadLine();

## <a name="step-6-run-the-console-application"></a>Действие 6. Запуск консольного приложения
1. Чтобы запустить консольное приложение, нажмите кнопку **Запустить** в Visual Studio, а затем войдите в Azure AD с помощью учетных данных, которые вы используете для подписки.
2. После появления состояния "Принято" нажмите клавишу **ВВОД** .

   На полное выполнение этого консольного приложения потребуется примерно 5 минут. Прежде чем нажать клавишу "ВВОД" и начать удаление ресурсов, потратьте несколько минут и проверьте на портале Azure, созданы ли эти ресурсы.
3. Чтобы отобразить состояние ресурсов, на портале Azure откройте раздел журналов аудита.

    ![Поиск журналов аудита на портале Azure](./media/virtual-machines-windows-csharp-template/crpportal.png)

## <a name="next-steps"></a>Дальнейшие действия
* При наличии проблем с развертыванием ознакомьтесь с информацией об [устранении неполадок развертываний групп ресурсов с помощью портала Azure](../resource-manager-troubleshoot-deployments-portal.md).
* Узнайте, как управлять созданной виртуальной машиной, прочитав статью [Управление виртуальными машинами Azure с помощью Azure Resource Manager и PowerShell](virtual-machines-windows-csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



<!--HONumber=Dec16_HO1-->


