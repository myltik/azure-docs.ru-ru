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
ms.date: 03/01/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 738ff9882cffc428f571ab6aea96c0927d2ce443
ms.lasthandoff: 03/31/2017


---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Развертывание виртуальной машины Azure с помощью C# и шаблона Resource Manager
В этой статье описывается развертывание шаблона Azure Resource Manager с помощью C#. Этот [шаблон](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json) позволяет выполнить развертывание одной виртуальной машины под управлением Windows Server в новой виртуальной сети с одной подсетью.

Подробное описание ресурса виртуальной машины см. в статье [Виртуальные машины в шаблоне Azure Resource Manager](template-description.md). Дополнительные сведения обо всех ресурсах в шаблоне см. в статье [Пошаговое руководство по созданию шаблона Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

На выполнение этих действий требуется примерно 10 минут.

## <a name="step-1-create-a-visual-studio-project"></a>Шаг 1. Создание проекта Visual Studio

На этом шаге нужно убедиться, что экземпляр Visual Studio установлен, и создать консольное приложение, используемое для развертывания шаблона.

1. Если вы этого еще не сделали, установите [Visual Studio](https://www.visualstudio.com/).
2. В Visual Studio выберите **Файл** > **Создать** > **Проект**.
3. В разделе **Шаблоны** > **Visual C#** выберите пункт **Консольное приложение**, введите имя и расположение проекта, а затем нажмите кнопку **OK**.

## <a name="step-2-install-libraries"></a>Шаг 2. Установка библиотек

Самый простой способ установить библиотеки, необходимые для выполнения этих инструкций, — это использовать пакеты NuGet. Требуется установить библиотеку управления ресурсами Azure и библиотеку аутентификации Azure Active Directory для создания ресурсов. Чтобы добавить эти библиотеки в Visual Studio, выполните следующие действия.

1. В обозревателе решений щелкните правой кнопкой мыши имя проекта и выберите пункт **Управление пакетами NuGet**, затем щелкните **Обзор**.
2. В поле поиска введите *Microsoft.IdentityModel.Clients.ActiveDirectory*, щелкните **Установить** и следуйте инструкциям по установке пакета.
3. В верхней части страницы выберите пункт **Включить предварительный выпуск**. В поле поиска введите *Microsoft.Azure.Management.ResourceManager*, щелкните **Установить** и следуйте инструкциям по установке пакета.

Теперь вы готовы использовать библиотеки для создания приложения.

## <a name="step-3-create-credentials-used-to-authenticate-requests"></a>Шаг 3. Создание учетных данных, используемых для аутентификации запросов

Прежде чем выполнить этот шаг, убедитесь в наличии доступа к [субъекту-службе Active Directory](../../resource-group-authenticate-service-principal.md). Субъект-служба предоставляет маркер для аутентификации запросов к Azure Resource Manager.

1. Откройте файл Program.cs для созданного проекта и добавьте в начало файла следующие операторы using:

    ```
    using Microsoft.Azure;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Rest;
    using System.IO;
    ```

2. Добавьте в класс Program этот метод, чтобы получить токен, необходимый для создания учетных данных:

    ```
    private static async Task<AuthenticationResult> GetAccessTokenAsync()
    {
      var cc = new ClientCredential("{client-id}", "{client-secret}");
      var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
      var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
      if (token == null)
      {
        throw new InvalidOperationException("Could not get the token.");
      } 
      return token;
    }
    ```

    Замените следующие значения.
    
    - Замените *{client-id}* идентификатором приложения Azure Active Directory. Этот идентификатор можно найти в колонке "Свойства" приложения AD. Чтобы найти приложение AD на портале Azure, в меню ресурса щелкните **Azure Active Directory** и выберите **Регистрация приложения**.
    - Замените *{client-secret}* ключом доступа приложения AD. Этот идентификатор можно найти в колонке "Свойства" приложения AD.
    - Замените *{tenant-id}* идентификатором клиента своей подписки. Идентификатор клиента можно найти в колонке "Свойства" для Azure Active Directory на портале Azure. Он называется *идентификатором каталога*.

3. Для вызова только что добавленного метода добавьте этот код в метод Main.

    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

4. Сохраните файл Program.cs.

## <a name="step-4-create-a-resource-group"></a>Шаг 4. Создание группы ресурсов

Хотя группу ресурсов можно создать на основе шаблона, используемый шаблон из коллекции ее не создает. На этом шаге вы добавите код для создания группы ресурсов.

1. Чтобы задать значения для приложения, добавьте переменные в метод Main класса Program.

    ```
    var groupName = "myResourceGroup";
    var subscriptionId = "subsciptionId";
    var deploymentName = "deploymentName;
    var location = "location";
    ```

    Замените следующие значения.
    
    - Замените *myResourceGroup* именем создаваемой группы ресурсов.
    - Замените *subscriptionId* идентификатором подписки. Идентификатор подписки можно найти в колонке "Подписки" на портале Azure.
    - Замените *deploymentName* именем развертывания.
    - Замените *location* [регионом Azure](https://azure.microsoft.com/regions/), в котором необходимо создать ресурсы.

2. Чтобы создать группу ресурсов, добавьте приведенный ниже метод в класс Program.

    ```
    public static async Task<ResourceGroup> CreateResourceGroupAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location)
    {
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };

      Console.WriteLine("Creating the resource group...");
      var resourceGroup = new ResourceGroup { Location = location };
      return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(
        groupName, 
        resourceGroup);
    }
    ```

3. Для вызова только что добавленного метода добавьте этот код в метод Main.

    ```
    var rgResult = CreateResourceGroupAsync(
      credential,
      groupName,
      subscriptionId,
      location);
    Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
    Console.ReadLine();
    ```

## <a name="step-5-create-a-parameters-file"></a>Шаг 5. Создание файла параметров

Чтобы задать значения для параметров ресурсов, определенных в шаблоне, создайте файл параметров, содержащий эти значения. Файл параметров используется при развертывании шаблона. Используемый шаблон из коллекции ожидает значения параметров *adminUserName*, *adminPassword* и *dnsLabelPrefix*.

В Visual Studio выполните описанные ниже действия.

1. В обозревателе решений щелкните правой кнопкой мыши проект, затем выберите **Добавить** > **Новый элемент**.
2. Щелкните **Интернет**, выберите **JSON-файл**, введите имя *Parameters.json* и нажмите кнопку **Добавить**.
3. Откройте файл parameters.json и добавьте в него следующее содержимое JSON.

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUserName": { "value": "mytestacct1" },
        "adminPassword": { "value": "mytestpass1" },
        "dnsLabelPrefix": { "value": "mydns1" }
      }
    }
    ```

    Замените значения параметров значениями для своей среды.

4. Сохраните файл Parameters.json.

## <a name="step-6-deploy-a-template"></a>Шаг 6. Развертывание шаблона

В этом примере вы развертываете шаблон из коллекции шаблонов Azure и передаете в него значения параметров из созданного вами локального файла. 

1. Чтобы развернуть шаблон, добавьте в класс Program следующий метод.

    ```
    public static async Task<DeploymentExtended> CreateTemplateDeploymentAsync(
      TokenCredentials credential,
      string groupName,
      string deploymentName,
      string subscriptionId)
    {
    
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };

      Console.WriteLine("Creating the template deployment...");
      var deployment = new Deployment();
      deployment.Properties = new DeploymentProperties
        {
          Mode = DeploymentMode.Incremental,
          TemplateLink = new TemplateLink("https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json"),
          Parameters = File.ReadAllText("..\\..\\Parameters.json")
        };
      
      return await resourceManagementClient.Deployments.CreateOrUpdateAsync(
        groupName,
        deploymentName,
        deployment
      );
    }
    ```

    Можно также развернуть шаблон из локальной папки, использовав свойство Template вместо свойства TemplateLink.

2. Для вызова только что добавленного метода добавьте этот код в метод Main.

    ```
    var dpResult = CreateTemplateDeploymentAsync(
      credential,
      groupName,
      deploymentName,
      subscriptionId);
    Console.WriteLine(dpResult.Result.Properties.ProvisioningState);
    Console.ReadLine();
    ```

## <a name="step-7-delete-the-resources"></a>Шаг 7. Удаление ресурсов

Так как за использование ресурсов Azure взимается плата, рекомендуется всегда удалять ресурсы, которые больше не нужны. Не нужно отдельно удалять каждый ресурс из группы ресурсов. Удалите группу ресурсов, и все ее ресурсы будут автоматически удалены.

1. Чтобы удалить группу ресурсов, добавьте этот метод в класс Program:

   ```
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
   ```

2. Для вызова только что добавленного метода добавьте этот код в метод Main.

   ```
   DeleteResourceGroupAsync(
     credential,
     groupName,
     subscriptionId);
   Console.ReadLine();
   ```

## <a name="step-8-run-the-console-application"></a>Шаг 8. Запуск консольного приложения

На полное выполнение этого консольного приложения потребуется примерно 5 минут. 

1. Чтобы запустить консольное приложение, нажмите кнопку **Запустить** в Visual Studio, а затем войдите в Azure AD с помощью учетных данных, которые вы используете для подписки.

2. После появления состояния **Успешно** нажмите клавишу *ВВОД*. 

    Вы должны увидеть текст **1 Успешно** в разделе "Развертывания" в колонке "Обзор" для группы ресурсов на портале Azure.

3. Прежде чем нажать клавишу **ВВОД** и начать удаление ресурсов, потратьте несколько минут и проверьте на портале Azure, созданы ли эти ресурсы. Щелкните состояние развертывания, чтобы просмотреть сведения о развертывании.

## <a name="next-steps"></a>Дальнейшие действия
* При наличии проблем с развертыванием ознакомьтесь с информацией об [устранении распространенных ошибок при развертывании Azure с помощью Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
* Узнайте, как развернуть виртуальную машину и ее вспомогательные ресурсы, ознакомившись с разделом [Развертывание ресурсов Azure с помощью языка C#](csharp.md).
* Узнайте, как управлять созданной виртуальной машиной, ознакомившись с разделом [Управление виртуальными машинами Azure с помощью Azure Resource Manager и языка C#](csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

