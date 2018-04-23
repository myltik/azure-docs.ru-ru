---
title: Создание виртуальной машины с несколькими сетевыми картами с помощью шаблона Azure Resource Manager | Документация Майкрософт
description: Создание виртуальной машины с несколькими сетевыми картами с помощью шаблона Azure Resource Manager.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 486f7dd5-cf2f-434c-85d1-b3e85c427def
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 85bfa264c6cf2b0586816a47b3ab72f3aee8ec96
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-vm-with-multiple-nics-using-a-template"></a>Создание виртуальной машины с несколькими сетевыми интерфейсами с помощью шаблона
[!INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[!INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

> [!NOTE]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../resource-manager-deployment-model.md).  В этой статье описывается использование модели развертывания c помощью Resource Manager. Для большинства новых развертываний мы рекомендуем использовать эту модель вместо [классической](virtual-network-deploy-multinic-classic-ps.md).
> 

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

В приведенных ниже действиях используются следующие группы ресурсов: *IaaSStory* для веб-серверов и *IaaSStory-BackEnd* для серверов базы данных.

## <a name="prerequisites"></a>предварительным требованиям
Перед созданием серверов базы данных необходимо создать группу ресурсов *IaaSStory* со всеми ресурсами, необходимыми для этого сценария. Чтобы создать эти ресурсы, выполните следующие действия:

1. Перейдите к [странице шаблона](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. На странице шаблона справа от **Родительская группа ресурсов** щелкните **Deploy to Azure** (Развернуть в Azure).
3. При необходимости измените значения параметров, а затем следуйте инструкциям на портале предварительной версии Azure для развертывания группы ресурсов.

> [!IMPORTANT]
> Имена учетных записей хранения должны быть уникальными. Они не могут повторяться в Azure.
> 

## <a name="understand-the-deployment-template"></a>Сведения о шаблоне развертывания
Перед развертыванием шаблона, предоставляемого с этой документацией, убедитесь, что вы понимаете, что он делает. Следующие действия предоставляют хороший обзор шаблона.

1. Перейдите к [странице шаблона](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Щелкните по файлу **azuredeploy.json** , чтобы открыть файл шаблона.
3. Обратите внимание на параметр *osType* , показанный ниже. Этот параметр используется для выбора образа виртуальной машины сервера базы данных и нескольких параметров, связанных с операционной системой.

    ```json
    "osType": {
      "type": "string",
      "defaultValue": "Windows",
      "allowedValues": [
        "Windows",
        "Ubuntu"
      ],
      "metadata": {
      "description": "Type of OS to use for VMs: Windows or Ubuntu."
      }
    },
    ```

4. Прокрутите список переменных и проверьте определение переменных **dbVMSetting** , перечисленных ниже. Он получает один из элементов массива, содержащегося в переменной **dbVMSettings** . Если вы знакомы с терминологией разработки программного обеспечения, переменные **dbVMSettings** можно считать хэш-таблицей или словарем.

    ```json
    "dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"
    ```

5. Предположим, требуется развернуть виртуальные машины Windows с SQL в серверной части. Тогда значение **osType** должно быть равно *Windows*, и переменная **dbVMSetting** будет содержать элемент, показанный ниже, который представляет первое значение в переменной **dbVMSettings**.

    ```json
    "Windows": {
      "vmSize": "Standard_DS3",
      "publisher": "MicrosoftSQLServer",
      "offer": "SQL2014SP1-WS2012R2",
      "sku": "Standard",
      "version": "latest",
      "vmName": "DB",
      "osdisk": "osdiskdb",
      "datadisk": "datadiskdb",
      "nicName": "NICDB",
      "ipAddress": "192.168.2.",
      "extensionDeployment": "",
      "avsetName": "ASDB",
      "remotePort": 3389,
      "dbPort": 1433
    },
    ```

6. Обратите внимание, что **vmSize** содержит значение *Standard_DS3*. С несколькими сетевыми адаптерами разрешается использовать только определенные размеры виртуальных машин. Чтобы проверить, какие размеры виртуальных машин поддерживают несколько сетевых интерфейсов, ознакомьтесь со статьями о размерах виртуальных машин [под управлением Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) и [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

7. Прокрутите вниз до **ресурсов** и обратите внимание на первый элемент. Он описывает учетную запись хранения. Эта учетная запись хранения будет использоваться для дисков данных, используемых в каждой виртуальной машине баз данных. В этом сценарии у каждой виртуальной машины баз данных есть диск ОС, который размещен в обычном хранилище, и два диска данных, размещенных в хранилище SSD (хранилище класса Premium).

    ```json
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('prmStorageName')]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "Storage Account - Premium"
      },
      "properties": {
      "accountType": "[parameters('prmStorageType')]"
      }
    },
    ```

8. Прокрутите вниз до следующего ресурса, как показано ниже. Этот ресурс представляет сетевой адаптер, используемый для доступа к базе данных в каждой виртуальной машине баз данных. Обратите внимание на использование функции **копирования** в этом ресурсе. Этот шаблон позволяет развертывать столько виртуальных машин, сколько вы хотите, на основе параметра **dbCount** . Поэтому необходимо создать такое же количество сетевых адаптеров для доступа к базе данных, по одному для каждой виртуальной машины.

    ```json
    {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/networkInterfaces",
    "name": "[concat(variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
    "location": "[variables('location')]",
    "tags": {
      "displayName": "NetworkInterfaces - DB DA"
    },
    "copy": {
      "name": "dbniccount",
      "count": "[parameters('dbCount')]"
    },
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "privateIPAllocationMethod": "Static",
            "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(4))]",
            "subnet": {
              "id": "[variables('backEndSubnetRef')]"
            }
          }
         }
       ] 
     }
    },
    ```

9. Прокрутите вниз до следующего ресурса, как показано ниже. Этот ресурс представляет сетевой адаптер, используемый для управления в каждой виртуальной машине баз данных. Опять же, для каждой виртуальной машины баз данных необходимо иметь один из этих сетевых адаптеров. Обратите внимание на элемент **networkSecurityGroup** , связывающий сетевую группу безопасности, обеспечивающую доступ к RDP или SSH, только с этим сетевым адаптером.

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[concat(variables('dbVMSetting').nicName, '-RA-',copyindex(1))]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "NetworkInterfaces - DB RA"
    },
    "copy": {
      "name": "dbniccount",
      "count": "[parameters('dbCount')]"
    },
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "networkSecurityGroup": {
             "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('remoteAccessNSGName'))]"
             },
             "privateIPAllocationMethod": "Static",
             "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(54))]",
             "subnet": {
              "id": "[variables('backEndSubnetRef')]"
             }
           }
          }
        ]
      }
    },
```

10. Прокрутите вниз до следующего ресурса, как показано ниже. Этот ресурс представляет группу доступности, которая будет общей для всех виртуальных машин в базе данных. Таким образом, можно гарантировать, что как минимум одна виртуальная машина в наборе всегда будет запущена во время обслуживания.

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/availabilitySets",
      "name": "[variables('dbVMSetting').avsetName]",
      "location": "[variables('location')]",
      "tags": {
        "displayName": "AvailabilitySet - DB"
      }
    },
    ```

11. Прокрутите вниз до следующего ресурса. Этот ресурс представляет виртуальные машины баз данных, как показано в первых нескольких строках, приведенных ниже. Снова обратите внимание на функцию **копирования**, которая гарантирует, что на основе параметра **dbCount** создается несколько виртуальных машин. Также обратите внимание на коллекцию **dependsOn** . В ней два сетевых адаптера, которые необходимо создать до развертывания виртуальной машины, а также группа доступности и учетная запись хранения.

    ```json
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('dbVMSetting').vmName,copyindex(1))]",
    "location": "[variables('location')]",
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
      "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-RA-', copyindex(1))]",
      "[concat('Microsoft.Compute/availabilitySets/', variables('dbVMSetting').avsetName)]",
      "[concat('Microsoft.Storage/storageAccounts/', parameters('prmStorageName'))]"
    ],
    "tags": {
      "displayName": "VMs - DB"
    },
    "copy": {
      "name": "dbvmcount",
      "count": "[parameters('dbCount')]"
    },
    ```

12. Прокрутите вниз ресурс виртуальной машины до элемента **networkProfile** , как показано ниже. Обратите внимание, что для каждой виртуальной машины используются два сетевых адаптера. При создании нескольких сетевых интерфейсов для виртуальной машины необходимо задать для свойства **primary** одного из сетевых интерфейсов значение *true*, а для остальных *false*.

    ```json
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-DA-',copyindex(1)))]",
          "properties": { "primary": true }
        },
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-RA-',copyindex(1)))]",
          "properties": { "primary": false }
        }
      ]
    }
    ```

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Развертывание шаблона ARM с помощью кнопки развертывания

> [!IMPORTANT]
> Перед выполнением следующих действий убедитесь, что вы выполнили [предварительные требования](#Pre-requisites) .
> 

Образец шаблона, который находится в общедоступном репозитории, использует файл параметров, содержащий значения по умолчанию для создания описанного выше сценария. Чтобы развернуть этот шаблон, перейдите по [этой ссылке](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC), щелкните **Deploy to Azure** (Развернуть в Azure) справа от **Группа внутренних ресурсов (см. документацию)**, при необходимости замените значения параметров по умолчанию и следуйте инструкциям на портале.

На следующем рисунке показано содержимое новой группы ресурсов после развертывания.

![Внутренняя группа ресурсов](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## <a name="deploy-the-template-by-using-powershell"></a>Развертывание шаблона с помощью PowerShell
Чтобы развернуть шаблон, скачанный с помощью PowerShell, установите и настройте PowerShell, выполнив действия, описанные в [этой статье](/powershell/azure/overview), а затем сделайте следующее:

Запустите командлет **`New-AzureRmResourceGroup`** для создания группы ресурсов с помощью шаблона.

```powershell
New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
-TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'
```

Ожидаемые выходные данные:

    ResourceGroupName : IaaSStory-Backend
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                        Actions  NotActions
                        =======  ==========
                        *
        Resources         :
                        Name                 Type                                 Location
                        ===================  ===================================  ========
                        ASDB                 Microsoft.Compute/availabilitySets   westus  
                        DB1                  Microsoft.Compute/virtualMachines    westus  
                        DB2                  Microsoft.Compute/virtualMachines    westus  
                        NICDB-DA-1           Microsoft.Network/networkInterfaces  westus  
                        NICDB-DA-2           Microsoft.Network/networkInterfaces  westus  
                        NICDB-RA-1           Microsoft.Network/networkInterfaces  westus  
                        NICDB-RA-2           Microsoft.Network/networkInterfaces  westus  
                        wtestvnetstorageprm  Microsoft.Storage/storageAccounts    westus  
    ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Развертывание шаблона с помощью интерфейса командной строки Azure
Чтобы развернуть шаблон с помощью интерфейса командной строки Azure, выполните следующие действия.

1. Если вы еще не пользовались Azure CLI, ознакомьтесь со статьей [Установка и настройка CLI Azure](../cli-install-nodejs.md) и следуйте инструкциям вплоть до выбора учетной записи Azure и подписки.
2. Выполните команду **`azure config mode`** , чтобы переключиться в режим диспетчера ресурсов, как показано ниже.

    ```azurecli
    azure config mode arm
    ```

    Ожидаемые выходные данные должны выглядеть следующим образом:

        info:    New mode is arm

3. Откройте [файл параметров](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json), выберите его содержимое и сохраните его в файл на своем компьютере. В этом примере мы сохранили файл параметров в файл *parameters.json*.
4. Выполните командлет **`azure group deployment create`** , чтобы развернуть новую виртуальную сеть с помощью файлов шаблона и параметров, которые вы загрузили и изменили раньше. В списке, который откроется после выполнения команды, будут указаны используемые параметры.

    ```azurecli
    azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json
    ```

    Ожидаемые выходные данные:
   
        info:    Executing command group create
        + Getting resource group IaaSStory-Backend
        + Creating resource group IaaSStory-Backend
        info:    Created resource group IaaSStory-Backend
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
        data:    Name:                IaaSStory-Backend
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK

