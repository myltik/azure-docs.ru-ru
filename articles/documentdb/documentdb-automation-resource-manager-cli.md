---
title: "Служба автоматизации DocumentDB — Resource Manager — CLI | Документация Майкрософт"
description: "Разверните учетную запись базы данных DocumentDB с помощью шаблонов диспетчера ресурсов Azure или интерфейса командной строки. DocumentDB — это облачная база данных NoSQL для данных JSON."
services: documentdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: eae5eec6-0e27-442c-abfc-ef6b7fd3f8d2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/11/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 0782000e87bed0d881be5238c1b91f89a970682c
ms.openlocfilehash: 686b26f082951a71ad21f653c8086061afa56b59


---
# <a name="automate-documentdb-account-creation-using-azure-cli-and-azure-resource-manager-templates"></a>Автоматизация создания учетной записи DocumentDB с помощью Azure CLI и шаблонов Azure Resource Manager
> [!div class="op_single_selector"]
> * [Портал Azure](documentdb-create-account.md)
> * [Azure CLI и ARM](documentdb-automation-resource-manager-cli.md)
> * [Azure PowerShell](documentdb-manage-account-with-powershell.md)

В этой статье показано, как создать учетную запись Azure DocumentDB с помощью шаблонов Azure Resource Manager или напрямую с помощью интерфейса командной строки Azure. Сведения о создании учетной записи DocumentDB с помощью портала Azure см. в статье [Создание учетной записи базы данных DocumentDB](documentdb-create-account.md).

Учетные записи базы данных DocumentDB в настоящее время являются единственным ресурсом DocumentDB, который можно создать с помощью шаблонов Resource Manager и Azure CLI.

## <a name="getting-ready"></a>Подготовка
Чтобы использовать интерфейс командной строки Azure с группами ресурсов Azure, вам понадобится подходящая версия интерфейса командной строки Azure, а также учетная запись Azure. Если у вас нет интерфейса командной строки Azure, [установите его](../xplat-cli-install.md).

### <a name="update-your-azure-cli-version"></a>Обновление версии интерфейса командной строки Azure
В командной строке введите команду `azure --version`, чтобы узнать, установлена ли у вас версия 0.10.4 или более поздняя. На этом этапе вам может быть предложено принять участие в сборе данных Microsoft Azure CLI. Можно выбрать "y" (Да) или "n" (Нет), чтобы согласиться или отказаться от участия, соответственно.

    azure --version
    0.10.4 (node: 4.2.4)

Если у вас не установлена версия 0.10.4 или более поздняя, [установите Azure CLI](../xplat-cli-install.md) либо выполните обновление c помощью одного из встроенных установщиков или команды **npm** (введите `npm update -g azure-cli` для обновления или `npm install -g azure-cli` для установки).

### <a name="set-your-azure-account-and-subscription"></a>Настройка учетной записи и подписки Azure
Если у вас нет подписки Azure, но есть подписка Visual Studio, то вы можете [активировать преимущества подписчика Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). или подписаться на [бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/).

Чтобы использовать шаблоны управления ресурсами Azure, требуется рабочая или учебная учетная запись или учетная запись Майкрософт. Если у вас есть одна из этих учетных записей, введите следующую команду:

    azure login

В результате будут возвращены следующие выходные данные:

    info:    Executing command login
    |info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin.
    Enter the code E1A2B3C4D to authenticate.

> [!NOTE]
> Если у вас нет учетной записи Azure, появится сообщение об ошибке, указывающее, что вам необходима учетная запись другого типа. Сведения о том, как создать такую учетную запись из текущей учетной записи Azure, см. в статье [Создание рабочего или школьного удостоверения в Azure Active Directory](../virtual-machines/virtual-machines-windows-create-aad-work-id.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

Откройте адрес [https://aka.ms/devicelogin](https://aka.ms/devicelogin) в браузере и введите код, указанный в выходных данных команды.

![Снимок экрана, демонстрирующий вход устройства в систему для командной строки Microsoft Azure.](media/documentdb-automation-resource-manager-cli/azure-cli-login-code.png)

После введения кода выберите удостоверение, которое вы хотите использовать в браузер, и при необходимости укажите имя пользователя и пароль.

![Снимок экрана, показывающий, где можно выбрать удостоверение учетной записи Майкрософт, связанной с подпиской Azure, которую вы хотите использовать.](media/documentdb-automation-resource-manager-cli/identity-cli-login.png)

Если вход успешно выполнен, появится следующий экран подтверждения, после чего вы можете закрыть окно браузера.

![Снимок экрана, показывающий подтверждение входа в кроссплатформенный интерфейс командной строки Microsoft Azure.](media/documentdb-automation-resource-manager-cli/login-confirmation.png)

Кроме того, командная оболочка предоставляет следующие выходные данные:

    /info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK

Помимо описанного здесь метода интерактивного входа в систему, доступны дополнительные методы входа Azure CLI. Дополнительные сведения о других методах и об использовании нескольких подписок см. в статье [Connect to an Azure subscription from the Azure Command-Line Interface (Azure CLI)](../xplat-cli-connect.md) (Подключение к среде Azure с использованием интерфейса командной строки Azure (Azure CLI)).

### <a name="switch-to-the-azure-cli-resource-group-mode"></a>Переключение в режим группы ресурсов в интерфейсе командной строки Azure
По умолчанию интерфейс командной строки Azure запускается в режиме управления службой (режим**asm** ). Чтобы перейти в режим группы ресурсов, введите следующее.

    azure config mode arm

В результате будут возвращены такие выходные данные:

    info:    Executing command config mode
    info:    New mode is arm
    info:    config mode command OK

При необходимости вы можете переключиться на набор команд по умолчанию, введя `azure config mode asm`.

### <a name="create-or-retrieve-your-resource-group"></a>Создание или получение группы ресурсов
Чтобы создать учетную запись DocumentDB, сначала вам понадобится группа ресурсов. Если известно имя группы ресурсов, которую вы хотите использовать, перейдите к [шагу 2](#create-documentdb-account-cli).

Чтобы просмотреть список всех текущих групп ресурсов, выполните следующую команду и запомните имя группы ресурсов, которую вы хотите использовать:

    azure group list

Чтобы создать группу ресурсов, выполните следующую команду, укажите имя новой группы ресурсов для создания и регион, в котором необходимо создать группу ресурсов:

    azure group create <resourcegroupname> <resourcegrouplocation>

* `<resourcegroupname>` могут использоваться только буквы, цифры, точки, символы подчеркивания, символ «-» и скобки, и он не может заканчиваться точкой.
* В параметре `<resourcegrouplocation>` должен быть указан один из регионов, в которых служба DocumentDB является общедоступной. Текущий список регионов см. на [странице регионов Azure](https://azure.microsoft.com/regions/#services).

Пример входных данных:

    azure group create new_res_group westus

В результате будут возвращены следующие выходные данные:

    info:    Executing command group create
    + Getting resource group new_res_group
    + Creating resource group new_res_group
    info:    Created resource group new_res_group
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group
    data:    Name:                new_res_group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

При возникновении ошибок см. раздел [Устранение неполадок](#troubleshooting).

## <a name="understanding-resource-manager-templates-and-resource-groups"></a>Общая информация о шаблонах Resource Manager и группах ресурсов
Большинство приложений создаются с использованием различных ресурсов. Это может быть комбинация из одной или нескольких учетных записей DocumentDB и учетных записей хранения, виртуальной сети или сети доставки содержимого. API управления службами Azure по умолчанию и портал Azure представляют эти компоненты в рамках подхода «одна служба за другой». Такой подход предполагает отдельное развертывание каждой службы и управление ею (или использование других инструментов для выполнения этой задачи) вместо обработки единой логической единицы развертывания.

*Шаблоны Azure Resource Manager* позволяют развертывать различные ресурсы и управлять ими как единой логической единицей развертывания с помощью декларативного подхода. Вместо того чтобы прямо сообщать Azure о том, что необходимо развертывать, выполняя одну команду за другой, вы просто описываете все развертывание в JSON-файле, т. е. все ресурсы и соответствующие параметры конфигурации и развертывания, и сообщаете Azure о том, что эти ресурсы требуется развернуть как одну группу.

Дополнительные сведения о группах ресурсов Azure и их возможностях см. в статье [Обзор Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Если вас интересует разработка шаблонов, ознакомьтесь со статьей [Создание шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

## <a name="a-idquick-create-documentdb-accountatask-create-a-single-region-documentdb-account"></a><a id="quick-create-documentdb-account"></a>Задача: создание учетной записи DocumentDB с одним регионом
Используйте указания в этом разделе, чтобы создать учетную запись DocumentDB с одним регионом. Это можно сделать с помощью Azure CLI с шаблонами Resource Manager или без них.

### <a name="a-idcreate-single-documentdb-account-cli-arma-create-a-single-region-documentdb-account-using-azure-cli-without-resource-manager-templates"></a><a id="create-single-documentdb-account-cli-arm"></a> Создание учетной записи DocumentDB с одним регионом с помощью Azure CLI без шаблонов Resource Manager
Создайте учетную запись DocumentDB в новой или существующей группе ресурсов, введя следующую команду в командной строке:

> [!TIP]
> При выполнении этой команды в Azure PowerShell или Windows PowerShell вы получите ошибку о непредвиденном маркере. Вместо этого запустите эту команду в командной строке Windows.
>
>

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority>\"}"]}"

* `<resourcegroupname>` могут использоваться только буквы, цифры, точки, символы подчеркивания, символ «-» и скобки, и он не может заканчиваться точкой.
* `<resourcegrouplocation>` — регион текущей группы ресурсов.
* `<ip-range-filter>` — указывает набор IP-адресов или их диапазонов в нотации CIDR, которые добавляются в список разрешенных клиентских IP-адресов для указанной учетной записи базы данных. IP-адреса и их диапазоны должны быть разделены запятой без пробелов. Дополнительные сведения см. в статье [Поддержка брандмауэра DocumentDB](documentdb-firewall-support.md).
* `<databaseaccountname>` могут использоваться только строчные буквы, цифры и символ «-», а его длина должна составлять от 3 до 50 символов.
* В параметре `<databaseaccountlocation>` должен быть указан один из регионов, в которых служба DocumentDB является общедоступной. Текущий список регионов см. на [странице регионов Azure](https://azure.microsoft.com/regions/#services).

Пример входных данных:

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l westus -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"\",\"locations\":["{\"locationName\":\"westus\",\"failoverPriority\":\"0\"}"]}"

В результате будут возвращены следующие выходные данные после подготовки вашей новой учетной записи:

    info:    Executing command resource create
    + Getting resource samplecliacct
    + Creating resource samplecliacct
    info:    Resource samplecliacct is updated
    data:
    data:    Id:        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group/providers/Microsoft.DocumentDB/databaseAccounts/samplecliacct
    data:    Name:      samplecliacct
    data:    Type:      Microsoft.DocumentDB/databaseAccounts
    data:    Parent:
    data:    Location:  West US
    data:    Tags:
    data:
    info:    resource create command OK

При возникновении ошибок см. раздел [Устранение неполадок](#troubleshooting).

После возврата команды учетная запись несколько минут будет в состоянии **Создание**, потом состояние изменится на **В сети**. Это означает, что учетная запись готова к использованию. Проверить состояние учетной записи можно на [портале Azure](https://portal.azure.com) в колонке **Учетные записи DocumentDB**.

### <a name="a-idcreate-single-documentdb-account-cli-arma-create-a-single-region-documentdb-account-using-azure-cli-with-resource-manager-templates"></a><a id="create-single-documentdb-account-cli-arm"></a> Создание учетной записи DocumentDB с одним регионом с помощью Azure CLI с шаблонами Resource Manager
Инструкции в этом разделе позволяют создать учетную запись DocumentDB с помощью шаблона Azure Resource Manager и файла необязательных параметров, каждый из которых является JSON-файлом. Использование шаблона позволяет точно описать, что вы хотите, и повторить все без ошибок.

Создайте локальный файл шаблона со следующим содержимым: Имя файла azuredeploy.json.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            },
            "locationName1": {
                "type": "string"
            }
        },
        "variables": {},
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "databaseAccountOfferType": "Standard",
                    "ipRangeFilter": "",
                    "locations": [
                        {
                            "failoverPriority": 0,
                            "locationName": "[parameters('locationName1')]"
                        }
                    ]
                }
            }
        ]
    }

Для параметра failoverPriority нужно задать значение 0, так как это учетная запись с одним регионом. Такая настройка указывает, что этот регион будет использоваться в качестве [региона для записи для учетной записи DocumentDB][scaling-globally].
Вы можете ввести значение в командной строке или создать файл параметров для указания значения.

Чтобы создать файл параметров, скопируйте следующее содержимое в новый файл и назовите файл azuredeploy.parameters.json. Если планируется указать имя учетной записи базы данных в командной строке, вы можете продолжить работу без создания этого файла.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            },
            "locationName1": {
                "value": "westus"
            }
        }
    }

В файле azuredeploy.parameters.json измените поле значения `"samplearmacct"` на имя базы данных, которую вы хотите использовать, а затем сохраните файл. `"databaseAccountName"` могут использоваться только строчные буквы, цифры и символ «-», а его длина должна составлять от 3 до 50 символов. Измените поле значения `"locationName1"` на регион, в котором нужно создать учетную запись DocumentDB.

Чтобы создать учетную запись DocumentDB в группе ресурсов, выполните следующую команду и укажите путь к файлу шаблона, путь к файлу параметра или значение параметра, имя группы ресурсов, в которой будет проводиться развертывание, и имя развертывания (-n является необязательным).

Использование файла параметров:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

* `<PathToTemplate>` — путь к файлу azuredeploy.json, созданному на шаге 1. Если путь содержит пробелы, заключите этот параметр в двойные кавычки.
* `<PathToParameterFile>` — путь к файлу azuredeploy.parameters.json, созданному на шаге 1. Если путь содержит пробелы, заключите этот параметр в двойные кавычки.
* `<resourcegroupname>` — имя существующей группы ресурсов, в которую добавляется учетная запись базы данных DocumentDB.
* `<deploymentname>` — необязательное имя развертывания.

Пример входных данных:

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

ИЛИ, чтобы указать параметр имени учетной записи базы данных без файла параметров и получить значение, выполните следующую команду:

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

Вот пример, в котором показаны командная строка и запись для учетной записи базы данных с именем samplearmacct:

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

По мере подготовки учетной записи вы получите следующие сведения:

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    +
    +
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Running
    +
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Succeeded
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    CorrelationId      : 4a5d4049-c494-4053-bad4-cc804d454700
    data:    DeploymentParameters :
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    databaseAccountName  String  samplearmacct
    data:    locationName1        String  westus
    info:    group deployment create command OK

При возникновении ошибок см. раздел [Устранение неполадок](#troubleshooting).  

После возврата команды учетная запись несколько минут будет в состоянии **Создание**, потом состояние изменится на **В сети**. Это означает, что учетная запись готова к использованию. Проверить состояние учетной записи можно на [портале Azure](https://portal.azure.com) в колонке **Учетные записи DocumentDB**.

## <a name="a-idquick-create-documentdb-with-mongodb-api-accountatask-create-a-single-region-documentdb-with-support-for-mongodb-account"></a><a id="quick-create-documentdb-with-mongodb-api-account"></a>Задача: создание базы данных DocumentDB с одним регионом с поддержкой учетной записи MongoDB
Используйте инструкции, описанные в этом разделе, чтобы создать базу данных DocumentDB с одним регионом с поддержкой учетной записи MongoDB. Это можно сделать с помощью интерфейса командной строки Azure с шаблонами Resource Manager.

### <a name="a-idcreate-single-documentdb-with-mongodb-api-account-cli-arma-create-a-single-region-documentdb-with-support-for-mongodb-account-using-azure-cli-with-resource-manager-templates"></a><a id="create-single-documentdb-with-mongodb-api-account-cli-arm"></a> Создание базы данных DocumentDB с одним регионом с поддержкой учетной записи MongoDB с помощью интерфейса командной строки Azure с шаблонами Resource Manager
Инструкции, описанные в этом разделе, позволяют создать базу данных DocumentDB с поддержкой учетной записи MongoDB с помощью шаблона Azure Resource Manager и файла необязательных параметров, каждый из которых является JSON-файлом. Использование шаблона позволяет точно описать, что вы хотите, и повторить все без ошибок.

Создайте локальный файл шаблона со следующим содержимым: Имя файла azuredeploy.json.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            },
            "locationName1": {
                "type": "string"
            }
        },
        "variables": {},
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "kind": "MongoDB",
                "properties": {
                    "databaseAccountOfferType": "Standard",
                    "ipRangeFilter": "",
                    "locations": [
                        {
                            "failoverPriority": 0,
                            "locationName": "[parameters('locationName1')]"
                        }
                    ]
                }
            }
        ]
    }

Для свойства kind необходимо задать MongoDB, чтобы эта учетная запись поддерживала интерфейсы API MongoDB. Если для свойства kind не указано значение, по умолчанию будет использоваться собственная учетная запись DocumentDB.

Для параметра failoverPriority нужно задать значение 0, так как это учетная запись с одним регионом. Такая настройка указывает, что этот регион будет использоваться в качестве [региона для записи для учетной записи DocumentDB][scaling-globally].
Вы можете ввести значение в командной строке или создать файл параметров для указания значения.

Чтобы создать файл параметров, скопируйте следующее содержимое в новый файл и назовите файл azuredeploy.parameters.json. Если планируется указать имя учетной записи базы данных в командной строке, вы можете продолжить работу без создания этого файла.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            },
            "locationName1": {
                "value": "westus"
            }
        }
    }

В файле azuredeploy.parameters.json измените поле значения `"samplearmacct"` на имя базы данных, которую вы хотите использовать, а затем сохраните файл. `"databaseAccountName"` могут использоваться только строчные буквы, цифры и символ «-», а его длина должна составлять от 3 до 50 символов. Измените поле значения `"locationName1"` на регион, в котором нужно создать учетную запись DocumentDB.

Чтобы создать учетную запись DocumentDB в группе ресурсов, выполните следующую команду и укажите путь к файлу шаблона, путь к файлу параметра или значение параметра, имя группы ресурсов, в которой будет проводиться развертывание, и имя развертывания (-n является необязательным).

Использование файла параметров:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

* `<PathToTemplate>` — путь к файлу azuredeploy.json, созданному на шаге 1. Если путь содержит пробелы, заключите этот параметр в двойные кавычки.
* `<PathToParameterFile>` — путь к файлу azuredeploy.parameters.json, созданному на шаге 1. Если путь содержит пробелы, заключите этот параметр в двойные кавычки.
* `<resourcegroupname>` — имя существующей группы ресурсов, в которую добавляется учетная запись базы данных DocumentDB.
* `<deploymentname>` — необязательное имя развертывания.

Пример входных данных:

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

ИЛИ, чтобы указать параметр имени учетной записи базы данных без файла параметров и получить значение, выполните следующую команду:

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

Вот пример, в котором показаны командная строка и запись для учетной записи базы данных с именем samplearmacct:

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

По мере подготовки учетной записи вы получите следующие сведения:

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    +
    +
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Running
    +
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Succeeded
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    CorrelationId      : 4a5d4049-c494-4053-bad4-cc804d454700
    data:    DeploymentParameters :
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    databaseAccountName  String  samplearmacct
    data:    locationName1        String  westus
    info:    group deployment create command OK

При возникновении ошибок см. раздел [Устранение неполадок](#troubleshooting).  

После возврата команды учетная запись несколько минут будет в состоянии **Создание**, потом состояние изменится на **В сети**. Это означает, что учетная запись готова к использованию. Проверить состояние учетной записи можно на [портале Azure](https://portal.azure.com) в колонке **Учетные записи DocumentDB**.

## <a name="a-idcreate-multi-documentdb-accountatask-create-a-multi-region-documentdb-account"></a><a id="create-multi-documentdb-account"></a>Задача: создание учетной записи DocumentDB с несколькими регионами
DocumentDB обладает возможностью [глобального распределения данных][distribute-globally] между различными [регионами Azure](https://azure.microsoft.com/regions/#services). При создании учетной записи DocumentDB можно указать регионы, в которых служба должна быть доступна. Используйте указания в этом разделе, чтобы создать учетную запись DocumentDB с несколькими регионами. Это можно сделать с помощью Azure CLI с шаблонами Resource Manager или без них.

### <a name="a-idcreate-multi-documentdb-account-clia-create-a-multi-region-documentdb-account-using-azure-cli-without-resource-manager-templates"></a><a id="create-multi-documentdb-account-cli"></a> Создание учетной записи DocumentDB с несколькими регионами с помощью Azure CLI без шаблонов Resource Manager
Создайте учетную запись DocumentDB в новой или существующей группе ресурсов, введя следующую команду в командной строке:

> [!TIP]
> При выполнении этой команды в Azure PowerShell или Windows PowerShell вы получите ошибку о непредвиденном маркере. Вместо этого запустите эту команду в командной строке Windows.
>
>

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation1>\",\"failoverPriority\":\"<failoverPriority1>\"},{\"locationName\":\"<databaseaccountlocation2>\",\"failoverPriority\":\"<failoverPriority2>\"}"]}"

* `<resourcegroupname>` могут использоваться только буквы, цифры, точки, символы подчеркивания, символ «-» и скобки, и он не может заканчиваться точкой.
* `<resourcegrouplocation>` — регион текущей группы ресурсов.
* `<ip-range-filter>` — указывает набор IP-адресов или их диапазонов в нотации CIDR, которые добавляются в список разрешенных клиентских IP-адресов для указанной учетной записи базы данных. IP-адреса и их диапазоны должны быть разделены запятой без пробелов. Дополнительные сведения см. в статье [Поддержка брандмауэра DocumentDB](documentdb-firewall-support.md).
* `<databaseaccountname>` могут использоваться только строчные буквы, цифры и символ «-», а его длина должна составлять от 3 до 50 символов.
* В параметре `<databaseaccountlocation1>` и `<databaseaccountlocation2>` должны быть указаны регионы, в которых служба DocumentDB является общедоступной. Текущий список регионов см. на [странице регионов Azure](https://azure.microsoft.com/regions/#services).

Пример входных данных:

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l westus -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"\",\"locations\":["{\"locationName\":\"westus\",\"failoverPriority\":\"0\"},{\"locationName\":\"eastus\",\"failoverPriority\":\"1\"}"]}"

В результате будут возвращены следующие выходные данные после подготовки вашей новой учетной записи:

    info:    Executing command resource create
    + Getting resource samplecliacct
    + Creating resource samplecliacct
    info:    Resource samplecliacct is updated
    data:
    data:    Id:        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group/providers/Microsoft.DocumentDB/databaseAccounts/samplecliacct
    data:    Name:      samplecliacct
    data:    Type:      Microsoft.DocumentDB/databaseAccounts
    data:    Parent:
    data:    Location:  West US
    data:    Tags:
    data:
    info:    resource create command OK

При возникновении ошибок см. раздел [Устранение неполадок](#troubleshooting).

После возврата команды учетная запись несколько минут будет в состоянии **Создание**, потом состояние изменится на **В сети**. Это означает, что учетная запись готова к использованию. Проверить состояние учетной записи можно на [портале Azure](https://portal.azure.com) в колонке **Учетные записи DocumentDB**.

### <a name="a-idcreate-multi-documentdb-account-cli-arma-create-a-multi-region-documentdb-account-using-azure-cli-with-resource-manager-templates"></a><a id="create-multi-documentdb-account-cli-arm"></a> Создание учетной записи DocumentDB с несколькими регионами с помощью Azure CLI с шаблонами Resource Manager
Инструкции в этом разделе позволяют создать учетную запись DocumentDB с помощью шаблона Azure Resource Manager и файла необязательных параметров, каждый из которых является JSON-файлом. Использование шаблона позволяет точно описать, что вы хотите, и повторить все без ошибок.

Создайте локальный файл шаблона со следующим содержимым: Имя файла azuredeploy.json.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            },
            "locationName1": {
                "type": "string"
            },
            "locationName2": {
                "type": "string"
            }
        },
        "variables": {},
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "databaseAccountOfferType": "Standard",
                    "ipRangeFilter": "",
                    "locations": [
                        {
                            "failoverPriority": 0,
                            "locationName": "[parameters('locationName1')]"
                        },
                        {
                            "failoverPriority": 1,
                            "locationName": "[parameters('locationName2')]"
                        }
                    ]
                }
            }
        ]
    }

Для создания учетной записи DocumentDB с двумя регионами можно использовать предыдущий файл шаблона. Чтобы создать учетную запись с дополнительными регионами, добавьте ее в массив расположений, а также добавьте соответствующие параметры.

Для одного из регионов следует задать для параметра failoverPriority значение 0. Такая настройка указывает, что этот регион будет использоваться в качестве [региона для записи для учетной записи DocumentDB][scaling-globally]. Значения приоритетов отработки отказа должны быть уникальными среди расположений. Наибольшее значение приоритета отработки отказа не должно превышать общее число регионов. Вы можете ввести значение в командной строке или создать файл параметров для указания значения.

Чтобы создать файл параметров, скопируйте следующее содержимое в новый файл и назовите файл azuredeploy.parameters.json. Если планируется указать имя учетной записи базы данных в командной строке, вы можете продолжить работу без создания этого файла.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            },
            "locationName1": {
                "value": "westus"
            },
            "locationName2": {
                "value": "eastus"
            }
        }
    }

В файле azuredeploy.parameters.json измените поле значения `"samplearmacct"` на имя базы данных, которую вы хотите использовать, а затем сохраните файл. `"databaseAccountName"` могут использоваться только строчные буквы, цифры и символ «-», а его длина должна составлять от 3 до 50 символов. Измените поле значения `"locationName1"` и `"locationName2"` на регион, в котором нужно создать учетную запись DocumentDB.

Чтобы создать учетную запись DocumentDB в группе ресурсов, выполните следующую команду и укажите путь к файлу шаблона, путь к файлу параметра или значение параметра, имя группы ресурсов, в которой будет проводиться развертывание, и имя развертывания (-n является необязательным).

Использование файла параметров:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

* `<PathToTemplate>` — путь к файлу azuredeploy.json, созданному на шаге 1. Если путь содержит пробелы, заключите этот параметр в двойные кавычки.
* `<PathToParameterFile>` — путь к файлу azuredeploy.parameters.json, созданному на шаге 1. Если путь содержит пробелы, заключите этот параметр в двойные кавычки.
* `<resourcegroupname>` — имя существующей группы ресурсов, в которую добавляется учетная запись базы данных DocumentDB.
* `<deploymentname>` — необязательное имя развертывания.

Пример входных данных:

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

ИЛИ, чтобы указать параметр имени учетной записи базы данных без файла параметров и получить значение, выполните следующую команду:

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

Вот пример, в котором показаны командная строка и запись для учетной записи базы данных с именем samplearmacct:

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

По мере подготовки учетной записи вы получите следующие сведения:

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    +
    +
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Running
    +
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Succeeded
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    CorrelationId      : 4a5d4049-c494-4053-bad4-cc804d454700
    data:    DeploymentParameters :
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    databaseAccountName  String  samplearmacct
    data:    locationName1        String  westus
    data:    locationName2        String  eastus
    info:    group deployment create command OK

При возникновении ошибок см. раздел [Устранение неполадок](#troubleshooting).  

После возврата команды учетная запись несколько минут будет в состоянии **Создание**, потом состояние изменится на **В сети**. Это означает, что учетная запись готова к использованию. Проверить состояние учетной записи можно на [портале Azure](https://portal.azure.com) в колонке **Учетные записи DocumentDB**.

## <a name="troubleshooting"></a>Устранение неполадок
Существует несколько вариантов устранения ошибок типа `Deployment provisioning state was not successful` , возникающих при создании группы ресурсов или учетной записи базы данных.

> [!NOTE]
> Указание недопустимых символов в имени учетной записи базы данных или выбор региона, в котором отсутствует доступ к DocumentDB, приводит к ошибкам развертывания. В именах учетных записей могут использоваться только строчные буквы, цифры и символ «-», а длина имени должна составлять от 3 до 50 символов. Все допустимые расположения учетной записи базы данных перечислены на [странице регионов Azure](https://azure.microsoft.com/regions/#services).
>
>

* Если выходные данные содержат ошибку `Error information has been recorded to C:\Users\wendy\.azure\azure.err`, информацию об этой ошибке можно просмотреть в файле azure.err.
* Полезные сведения также можно найти в файле журнала группы ресурсов. Чтобы просмотреть файл журнала, выполните следующую команду:

        azure group log show <resourcegroupname> --last-deployment

    Пример входных данных:

        azure group log show new_res_group --last-deployment

    Дополнительные сведения см. в статье [Устранение неполадок при развертывании групп ресурсов в Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md).
* Кроме того, сведения об ошибках доступны на портале Azure, как показано на следующем снимке экрана. Чтобы перейти к информации об ошибке: нажмите "Группа ресурсов" на навигационной панели, выберите группу ресурсов, в которой есть ошибка, затем в области Essentials в колонке группы ресурсов щелкните дату последнего развертывания, выберите неудачное развертывание в колонке журналов развертываний и щелкните сведения об операции с красным восклицательным знаком в колонке развертывания. В колонке сведений об операции отображается сообщение о состоянии неудачного развертывания.

    ![Снимок экрана портала Azure, демонстрирующий, как перейти к сообщению об ошибке развертывания](media/documentdb-automation-resource-manager-cli/portal-troubleshooting-deploy.png)

## <a name="next-steps"></a>Дальнейшие действия
Следующий шаг после создания учетной записи DocumentDB — создание базы данных DocumentDB. Для создания базы данных можно использовать один из следующих вариантов:

* С помощью портала Azure, как описано в статье [Создание коллекции и базы данных DocumentDB на портале Azure](documentdb-create-collection.md).
* примеры для C# .NET в проекте [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) в репозитории [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) на портале GitHub;
* [Пакеты SDK для DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB содержит пакеты SDK для .NET, Java, Python, Node.js и JavaScript API.

Создав базу данных, в нее нужно [добавить хотя бы одну коллекцию](documentdb-create-collection.md), а затем в коллекцию [добавить документы](documentdb-view-json-document-explorer.md).

Добавив документы в коллекцию, [DocumentDB SQL](documentdb-sql-query.md) можно использовать для [выполнения запросов](documentdb-sql-query.md#executing-sql-queries) к документам с помощью [обозревателя запросов](documentdb-query-collections-query-explorer.md) на портале, [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) или одного из [пакетов SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

Дополнительные сведения о DocumentDB можно получить из следующих ресурсов:

* [Схема обучения для DocumentDB.](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
* [Модель ресурсов и понятия DocumentDB](documentdb-resources.md)

Дополнительные шаблоны см. на странице [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[distribute-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally
[scaling-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet



<!--HONumber=Jan17_HO2-->


