---
title: "Автоматизация управления Azure DocumentDB. Управление регионами | Документация Майкрософт"
description: "Сведения об управлении регионами в учетной записи базы данных DocumentDB с помощью Azure CLI 1.0 и Azure Resource Manager. DocumentDB — это облачная база данных NoSQL для данных JSON."
services: documentdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 7f765c17-8549-4108-9475-46394fc3a218
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: dimakwan
translationtype: Human Translation
ms.sourcegitcommit: 655f501f920e3169450831f501f7183ae46a4a60
ms.openlocfilehash: 70614f7d97466fb7e8a2f325d744f5e1632640a6
ms.lasthandoff: 02/27/2017


---
# <a name="automate-documentdb-account-region-management-using-azure-cli-10-and-azure-resource-manager-templates"></a>Автоматизация управления регионами учетной записи DocumentDB с помощью Azure CLI 1.0 и шаблонов Azure Resource Manager

В этой статье описывается, как добавить или удалить регион в учетной записи Azure DocumentDB с помощью шаблонов Azure Resource Manager и команд Azure CLI 1.0. Управление регионами также можно осуществлять на [портале Azure](documentdb-portal-global-replication.md). Обратите внимание, что команды в этом руководстве не позволяют изменить приоритет разных регионов при отработке отказа. Можно добавлять или удалять только регионы для чтения. Регионы для записи (приоритет отработки отказа — 0) в учетной записи базы данных нельзя добавлять или удалять.

Учетные записи базы данных DocumentDB в настоящее время являются единственным ресурсом DocumentDB, который можно создать или изменить с помощью [шаблонов Resource Manager и Azure CLI 1.0](documentdb-automation-resource-manager-cli.md).

## <a name="getting-ready"></a>Подготовка

Чтобы использовать Azure CLI 1.0 с группами ресурсов Azure, вам понадобится подходящая версия Azure CLI, а также учетная запись Azure. Если у вас нет Azure CLI 1.0, [установите его](../xplat-cli-install.md).

### <a name="update-your-azure-cli-10-version"></a>Обновление версии Azure CLI 1.0

В командной строке введите команду `azure --version`, чтобы узнать, установлена ли у вас версия 0.10.4 или более поздняя. На этом этапе вам может быть предложено принять участие в сборе данных Microsoft Azure CLI 1.0. Вы можете выбрать "y" (Да) или "n" (Нет), чтобы согласиться или отказаться от участия, соответственно.

    azure --version
    0.10.4 (node: 4.2.4)

Если у вас не установлена версия 0.10.4 или более поздняя, [установите Azure CLI 1.0](../xplat-cli-install.md) либо выполните обновление c помощью одного из встроенных установщиков или команды **npm** (введите `npm update -g azure-cli` для обновления или `npm install -g azure-cli` для установки).

### <a name="set-your-azure-account-and-subscription"></a>Настройка учетной записи и подписки Azure

Если у вас нет подписки Azure, но есть подписка Visual Studio, то вы можете [активировать преимущества подписчика Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). или подписаться на [бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/).

Чтобы использовать шаблоны управления ресурсами Azure, требуется рабочая или учебная учетная запись или учетная запись Майкрософт. Если у вас есть одна из этих учетных записей, введите следующую команду.

    azure login

В результате будут возвращены следующие выходные данные: 

    info:    Executing command login
    |info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin. 
    Enter the code E1A2B3C4D to authenticate.

> [!NOTE]
> Если у вас нет учетной записи Azure, появится сообщение об ошибке, указывающее, что вам необходима учетная запись другого типа. Сведения о том, как создать такую учетную запись из текущей учетной записи Azure, см. в статье [Создание рабочего или школьного удостоверения в Azure Active Directory](../virtual-machines/virtual-machines-windows-create-aad-work-id.md).

Откройте адрес [https://aka.ms/devicelogin](https://aka.ms/devicelogin) в браузере и введите код, указанный в выходных данных команды.

![Снимок экрана, на котором показан экран входа устройства для Microsoft Azure CLI 1.0](media/documentdb-automation-resource-manager-cli/azure-cli-login-code.png)

После введения кода выберите удостоверение, которое вы хотите использовать в браузер, и при необходимости укажите имя пользователя и пароль.

![Снимок экрана, показывающий, где можно выбрать удостоверение учетной записи Майкрософт, связанной с подпиской Azure, которую вы хотите использовать.](media/documentdb-automation-resource-manager-cli/identity-cli-login.png)

Если вход успешно выполнен, появится следующий экран подтверждения, после чего вы можете закрыть окно браузера.

![Снимок экрана, показывающий подтверждение входа в кроссплатформенный интерфейс командной строки Microsoft Azure.](media/documentdb-automation-resource-manager-cli/login-confirmation.png)

Кроме того, командная оболочка предоставляет следующие выходные данные.

    /info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK

Помимо описанного здесь метода интерактивного входа доступны дополнительные методы входа Azure CLI 1.0. Дополнительные сведения о других методах и об использовании нескольких подписок см. в статье [Войдите в Azure из командной строки Azure](../xplat-cli-connect.md).

### <a name="switch-to-azure-cli-10-resource-group-mode"></a>Переключение в режим группы ресурсов в Azure CLI 1.0

По умолчанию Azure CLI 1.0 запускается в режиме управления службой (режим **asm**). Чтобы перейти в режим группы ресурсов, введите следующее.

    azure config mode arm

В результате будут возвращены такие выходные данные:

    info:    Executing command config mode
    info:    New mode is arm
    info:    config mode command OK

При необходимости вы можете переключиться на набор команд по умолчанию, введя `azure config mode asm`.

### <a name="create-or-retrieve-your-resource-group"></a>Создание или получение группы ресурсов

Чтобы создать учетную запись DocumentDB, сначала необходима группа ресурсов. Если известно имя группы ресурсов, которую вы хотите использовать, перейдите к [шагу 2](#create-documentdb-account-cli). 

Чтобы просмотреть список всех текущих групп ресурсов, выполните следующую команду и запомните имя группы ресурсов, которую вы хотите использовать: 

    azure group list

Чтобы создать новую группу ресурсов, выполните следующую команду, укажите имя новой группы ресурсов для создания и регион, в котором необходимо создать группу ресурсов: 

    azure group create <resourcegroupname> <resourcegrouplocation>

 - `<resourcegroupname>` могут использоваться только буквы, цифры, точки, символы подчеркивания, символ «-» и скобки, и он не может заканчиваться точкой. 
 - В параметре `<resourcegrouplocation>` должен быть указан один из регионов, в которых служба DocumentDB является общедоступной. Текущий список регионов см. на [странице регионов Azure](https://azure.microsoft.com/regions/#services).

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

## <a name="understanding-azure-resource-manager-templates-and-resource-groups"></a>Общая информация о шаблонах Azure Resource Manager и группах ресурсов

Большинство приложений создаются с использованием различных ресурсов. Это может быть комбинация из одной или нескольких учетных записей DocumentDB и учетных записей хранения, виртуальной сети или сети доставки содержимого. API управления службами Azure по умолчанию и портал Azure представляют эти компоненты в рамках подхода «одна служба за другой». Такой подход предполагает отдельное развертывание каждой службы и управление ею (или использование других инструментов для выполнения этой задачи) вместо обработки единой логической единицы развертывания.

*Шаблоны Azure Resource Manager* позволяют развертывать различные ресурсы и управлять ими как единой логической единицей развертывания с помощью декларативного подхода. Вместо того чтобы прямо сообщать Azure о том, что необходимо развертывать, выполняя одну команду за другой, вы просто описываете все развертывание в JSON-файле, т. е. все ресурсы и соответствующие параметры конфигурации и развертывания, и сообщаете Azure о том, что эти ресурсы требуется развернуть как одну группу.

Дополнительные сведения о группах ресурсов Azure и их возможностях см. в статье [Обзор Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Если вас интересует разработка шаблонов, ознакомьтесь со статьей [Создание шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).


## <a id="add-region-documentdb-account"></a>Задача. Добавление региона к учетной записи DocumentDB

DocumentDB обладает возможностью [глобального распределения данных][distribute-globally] между различными [регионами Azure](https://azure.microsoft.com/regions/#services). В этом разделе описывается, как добавить регион для чтения в существующую учетную запись DocumentDB с помощью Azure CLI 1.0 и шаблонов Resource Manager. Вы можете сделать это с помощью Azure CLI 1.0 с шаблонами Resource Manager или без них.

### <a id="add-region-documentdb-account-cli"></a> Добавление региона к учетной записи DocumentDB с помощью Azure CLI 1.0 без шаблонов Resource Manager

Добавьте регион к существующей учетной записи DocumentDB в новой или существующей группе ресурсов, введя приведенную ниже команду в командной строке. Обратите внимание, что значение параметра locations должно отражать текущую конфигурацию регионов учетной записи DocumentDB, за исключением нового добавляемого региона. В приведенном ниже примере показана команда для добавления второго региона к учетной записи.

Подберите значения приоритетов отработки отказа для существующей конфигурации. Для одного из регионов следует задать для параметра failoverPriority значение 0. Такая настройка указывает, что этот регион будет использоваться в качестве [региона для записи для учетной записи DocumentDB][scaling-globally]. Значения приоритетов отработки отказа должны быть уникальными среди расположений. Наибольшее значение приоритета отработки отказа не должно превышать общее число регионов. Новый регион будет регионом для чтения и должен иметь значение приоритета отработки отказа больше 0.

> [!TIP]
> При выполнении этой команды в Azure PowerShell или Windows PowerShell вы получите ошибку о непредвиденном токене. Вместо этого запустите эту команду в командной строке Windows.

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority1>\"},{\"locationName\":\"<newdatabaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority2>\"}"]}"

 - `<resourcegroupname>` могут использоваться только буквы, цифры, точки, символы подчеркивания, символ «-» и скобки, и он не может заканчиваться точкой.
 - `<resourcegrouplocation>` — регион текущей группы ресурсов.
 - `<ip-range-filter>` — указывает набор IP-адресов или их диапазонов в нотации CIDR, которые добавляются в список разрешенных клиентских IP-адресов для указанной учетной записи базы данных. IP-адреса и их диапазоны должны быть разделены запятой без пробелов. Дополнительные сведения см. в статье [Поддержка брандмауэра DocumentDB](documentdb-firewall-support.md).
 - `<databaseaccountname>` могут использоваться только строчные буквы, цифры и символ «-», а его длина должна составлять от 3 до 50 символов.
 - В параметре `<databaseaccountlocation>` должен быть указан один из регионов, в которых служба DocumentDB является общедоступной. Текущий список регионов см. на [странице регионов Azure](https://azure.microsoft.com/regions/#services).
 - В параметре `<newdatabaseaccountlocation>` нужно указать новый регион, который требуется добавить. Это должен быть один из регионов, в которых служба DocumentDB является общедоступной. Текущий список регионов см. на [странице регионов Azure](https://azure.microsoft.com/regions/#services).


Пример входных данных для добавления региона "восточная часть США" в качестве региона для чтения в учетной записи DocumentDB: 

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

### <a id="add-region-documentdb-account-cli-arm"></a> Добавление региона к учетной записи DocumentDB с помощью Azure CLI 1.0 с шаблонами Resource Manager

В этом разделе показано, как добавить регион к существующей учетной записи DocumentDB с помощью шаблона Azure Resource Manager и файла необязательных параметров, каждый из которых является JSON-файлом. Использование шаблона позволяет точно описать, что вы хотите, и повторить все без ошибок.

Подберите значения приоритетов отработки отказа для существующей конфигурации. Для одного из регионов следует задать для параметра failoverPriority значение 0. Такая настройка указывает, что этот регион будет использоваться в качестве [региона для записи для учетной записи DocumentDB][scaling-globally]. Значения приоритетов отработки отказа должны быть уникальными среди расположений. Наибольшее значение приоритета отработки отказа не должно превышать общее число регионов. Новый регион будет регионом для чтения и должен иметь значение приоритета отработки отказа больше 0.

Создайте локальный файл шаблона, аналогичный приведенному ниже, который соответствует текущей конфигурации регионов DocumentDB. Массив locations должен содержать все существующие регионы в учетной записи базы данных наряду с новым добавляемым регионом. Имя файла azuredeploy.json.

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
            },
            "newLocationName": {
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
                        },
                        {
                            "failoverPriority": 2,
                            "locationName": "[parameters('newLocationName')]"
                        }
                    ]
                }
            }
        ]
    }

В файле шаблона, указанном выше, новый регион добавляется к учетной записи DocumentDB, которая уже содержит два региона.

Вы можете ввести значения параметров в командной строке или создать файл параметров для указания значений.

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
            },
            "newLocationName": {
                "value": "northeurope"
            }
        }
    }

В файле azuredeploy.parameters.json укажите для поля "value" параметра `"databaseAccountName"` на имя базы данных, которую вы хотите использовать, а затем сохраните файл. `"databaseAccountName"` могут использоваться только строчные буквы, цифры и символ «-», а его длина должна составлять от 3 до 50 символов. В поля "value" параметров `"locationName1"` и `"locationName2"` введите регионы, где находится учетная запись DocumentDB. В поле "value" параметра `"newLocationName"` введите регион, которой необходимо добавить.

Чтобы создать учетную запись DocumentDB в группе ресурсов, выполните следующую команду и укажите путь к файлу шаблона, путь к файлу параметра или значение параметра, имя группы ресурсов, в которой будет проводиться развертывание, и имя развертывания (-n является необязательным). 

Использование файла параметров:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

 - `<PathToTemplate>` — путь к файлу azuredeploy.json, созданному на шаге 1. Если путь содержит пробелы, заключите этот параметр в двойные кавычки.
 - `<PathToParameterFile>` — путь к файлу azuredeploy.parameters.json, созданному на шаге 1. Если путь содержит пробелы, заключите этот параметр в двойные кавычки.
 - `<resourcegroupname>` — имя существующей группы ресурсов, в которую добавляется учетная запись базы данных DocumentDB. 
 - `<deploymentname>` — необязательное имя развертывания.

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
    data:    locationName1        String  westus
    data:    locationName2        String  eastus
    data:    newLocationName      String  eastus
    data:    databaseAccountName  String  samplearmacct
    info:    group deployment create command OK

При возникновении ошибок см. раздел [Устранение неполадок](#troubleshooting).  

После возврата команды учетная запись несколько минут будет в состоянии **Создание**, потом состояние изменится на **В сети**. Это означает, что учетная запись готова к использованию. Проверить состояние учетной записи можно на [портале Azure](https://portal.azure.com) в колонке **Учетные записи DocumentDB**.

## <a id="remove-region-documentdb-account"></a>Задача. Удаление региона из учетной записи DocumentDB

DocumentDB обладает возможностью [глобального распределения данных][distribute-globally] между различными [регионами Azure](https://azure.microsoft.com/regions/#services). В этом разделе описывается, как удалить регион из существующей учетной записи DocumentDB с помощью Azure CLI 1.0 и шаблонов Resource Manager. Вы можете сделать это с помощью Azure CLI 1.0 с шаблонами Resource Manager или без них.

### <a id="remove-region-documentdb-account-cli"></a> Удаление региона из учетной записи DocumentDB с помощью Azure CLI 1.0 без шаблонов Resource Manager

Чтобы удалить регион из существующей учетной записи DocumentDB, вы можете выполнить следующую команду с помощью Azure CLI 1.0. Массив locations должен содержать только те регионы, которые останутся после удаления региона. **Пропущенные расположения будут удалены из учетной записи DocumentDB**. Введите приведенную ниже команду в окне командной строки.

Для одного из регионов следует задать для параметра failoverPriority значение 0. Такая настройка указывает, что этот регион будет использоваться в качестве [региона для записи для учетной записи DocumentDB][scaling-globally]. Значения приоритетов отработки отказа должны быть уникальными среди расположений. Наибольшее значение приоритета отработки отказа не должно превышать общее число регионов. 

> [!TIP]
> При выполнении этой команды в Azure PowerShell или Windows PowerShell вы получите ошибку о непредвиденном токене. Вместо этого запустите эту команду в командной строке Windows.

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority>\"}"]}"

 - `<resourcegroupname>` могут использоваться только буквы, цифры, точки, символы подчеркивания, символ «-» и скобки, и он не может заканчиваться точкой.
 - `<resourcegrouplocation>` — регион текущей группы ресурсов.
 - `<ip-range-filter>` — указывает набор IP-адресов или их диапазонов в нотации CIDR, которые добавляются в список разрешенных клиентских IP-адресов для указанной учетной записи базы данных. IP-адреса и их диапазоны должны быть разделены запятой без пробелов. Дополнительные сведения см. в статье [Поддержка брандмауэра DocumentDB](documentdb-firewall-support.md).
 - `<databaseaccountname>` могут использоваться только строчные буквы, цифры и символ «-», а его длина должна составлять от 3 до 50 символов.
 - В параметре `<databaseaccountlocation>` должен быть указан один из регионов, в которых служба DocumentDB является общедоступной. Текущий список регионов см. на [странице регионов Azure](https://azure.microsoft.com/regions/#services).

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

После возврата команды учетная запись несколько минут будет в состоянии **Обновление**, потом состояние изменится на **В сети**. Это означает, что учетная запись готова к использованию. Проверить состояние учетной записи можно на [портале Azure](https://portal.azure.com) в колонке **Учетные записи DocumentDB**.

### <a id="remove-region-documentdb-account-cli-arm"></a> Удаление региона из учетной записи DocumentDB с помощью Azure CLI 1.0 и шаблонов Resource Manager

В этом разделе описано, как удалить регион из существующей учетной записи DocumentDB с помощью шаблона Azure Resource Manager и файла необязательных параметров, каждый из которых является JSON-файлом. Использование шаблона позволяет точно описать, что вы хотите, и повторить все без ошибок.

Для одного из регионов следует задать для параметра failoverPriority значение 0. Такая настройка указывает, что этот регион будет использоваться в качестве [региона для записи для учетной записи DocumentDB][scaling-globally]. Значения приоритетов отработки отказа должны быть уникальными среди расположений. Наибольшее значение приоритета отработки отказа не должно превышать общее число регионов. 

Создайте локальный файл шаблона, аналогичный приведенному ниже, который соответствует текущей конфигурации регионов DocumentDB. Массив locations должен содержать только те регионы, которые останутся после удаления региона. **Пропущенные расположения будут удалены из учетной записи DocumentDB**. Имя файла azuredeploy.json.

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

Вы можете ввести значения параметров в командной строке или создать файл параметров для указания значений.

Чтобы создать файл параметров, скопируйте следующее содержимое в новый файл и назовите файл azuredeploy.parameters.json. Если планируется указать имя учетной записи базы данных в командной строке, вы можете продолжить работу без создания этого файла. Не забудьте добавить необходимые параметры, определенные в шаблоне Azure Resource Manager.

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

В файле azuredeploy.parameters.json укажите для поля "value" параметра `"databaseAccountName"` на имя базы данных, которую вы хотите использовать, а затем сохраните файл. `"databaseAccountName"` могут использоваться только строчные буквы, цифры и символ «-», а его длина должна составлять от 3 до 50 символов. В поле "value" параметра `"locationName1"` укажите регионы, где будет находиться учетная запись DocumentDB после удаления региона.

Чтобы создать учетную запись DocumentDB в группе ресурсов, выполните следующую команду и укажите путь к файлу шаблона, путь к файлу параметра или значение параметра, имя группы ресурсов, в которой будет проводиться развертывание, и имя развертывания (-n является необязательным). 

Использование файла параметров:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

 - `<PathToTemplate>` — путь к файлу azuredeploy.json, созданному на шаге 1. Если путь содержит пробелы, заключите этот параметр в двойные кавычки.
 - `<PathToParameterFile>` — путь к файлу azuredeploy.parameters.json, созданному на шаге 1. Если путь содержит пробелы, заключите этот параметр в двойные кавычки.
 - `<resourcegroupname>` — имя существующей группы ресурсов, в которую добавляется учетная запись базы данных DocumentDB. 
 - `<deploymentname>` — необязательное имя развертывания.

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

После возврата команды учетная запись несколько минут будет в состоянии **Обновление**, потом состояние изменится на **В сети**. Это означает, что учетная запись готова к использованию. Проверить состояние учетной записи можно на [портале Azure](https://portal.azure.com) в колонке **Учетные записи DocumentDB**.

## <a name="troubleshooting"></a>Устранение неполадок

Существует несколько вариантов устранения ошибок типа `Deployment provisioning state was not successful` , возникающих при создании группы ресурсов или учетной записи базы данных. 

> [!NOTE]
> Указание недопустимых символов в имени учетной записи базы данных или выбор региона, в котором отсутствует доступ к DocumentDB, приводит к ошибкам развертывания. В именах учетных записей могут использоваться только строчные буквы, цифры и символ «-», а длина имени должна составлять от 3 до 50 символов. Все допустимые расположения учетной записи базы данных перечислены на [странице регионов Azure](https://azure.microsoft.com/regions/#services).

- Если выходные данные содержат ошибку `Error information has been recorded to C:\Users\wendy\.azure\azure.err`, информацию об этой ошибке можно просмотреть в файле azure.err.

- Полезные сведения также можно найти в файле журнала группы ресурсов. Чтобы просмотреть файл журнала, выполните следующую команду:

        azure group log show <resourcegroupname> --last-deployment

    Пример ввода:       azure group log show new_res_group --last-deployment

    Дополнительные сведения см. в статье [Устранение неполадок при развертывании групп ресурсов в Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md).

- Кроме того, сведения об ошибках доступны на портале Azure, как показано на следующем снимке экрана. Чтобы перейти к информации об ошибке: нажмите "Группа ресурсов" на навигационной панели, выберите группу ресурсов, в которой есть ошибка, затем в области Essentials в колонке группы ресурсов щелкните дату последнего развертывания, выберите неудачное развертывание в колонке журналов развертываний и щелкните сведения об операции с красным восклицательным знаком в колонке развертывания. В колонке сведений об операции отображается сообщение о состоянии неудачного развертывания. 

    ![Снимок экрана портала Azure, демонстрирующий, как перейти к сообщению об ошибке развертывания](media/documentdb-automation-resource-manager-cli/portal-troubleshooting-deploy.png) 

## <a name="next-steps"></a>Дальнейшие действия

Следующий шаг после создания учетной записи DocumentDB — создание базы данных DocumentDB. Для создания базы данных можно использовать один из следующих вариантов:

- С помощью портала Azure, как описано в статье [Создание коллекции и базы данных DocumentDB на портале Azure](documentdb-create-collection.md).
- примеры для C# .NET в проекте [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) в репозитории [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) на портале GitHub;
- [Пакеты SDK для DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB содержит пакеты SDK для .NET, Java, Python, Node.js и JavaScript API. 

Создав базу данных, в нее нужно [добавить хотя бы одну коллекцию](documentdb-create-collection.md), а затем в коллекцию [добавить документы](documentdb-view-json-document-explorer.md). 

Добавив документы в коллекцию, [DocumentDB SQL](documentdb-sql-query.md) можно использовать для [выполнения запросов](documentdb-sql-query.md#ExecutingSqlQueries) к документам с помощью [обозревателя запросов](documentdb-query-collections-query-explorer.md) на портале, [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) или одного из [пакетов SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

Дополнительные сведения о DocumentDB можно получить из следующих ресурсов:

-    [Схема обучения для DocumentDB.](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-    [Модель ресурсов и понятия DocumentDB](documentdb-resources.md)

Дополнительные шаблоны см. на странице [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[distribute-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally
[scaling-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet

