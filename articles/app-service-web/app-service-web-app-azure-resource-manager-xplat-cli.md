---
title: "Кроссплатформенные средства командной строки на основе Azure Resource Manager для веб-приложений Azure | Документация Майкрософт"
description: "Узнайте, как использовать новые кроссплатформенные средства командной строки на основе Azure Resource Manager для управления веб-приложениями Azure."
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: d415b195-4262-416f-b59f-7e1aef200054
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: f595be46983bf07783b529de885d889c18fdb61a
ms.openlocfilehash: 6f75781af24d1ad4cb6460f0dfd528684aaad57f


---
# <a name="using-azure-resource-manager-based-xplat-cli-for-azure-app-service"></a>Использование кроссплатформенного интерфейса командной строки на основе Azure Resource Manager для службы приложений Azure
> [!div class="op_single_selector"]
> * [Интерфейс командной строки Azure](app-service-web-app-azure-resource-manager-xplat-cli.md)
> * [Azure PowerShell](app-service-web-app-azure-resource-manager-powershell.md)

Версия кроссплатформенного средства командной строки Microsoft Azure 0.10.5 включает новые команды, которые позволяют использовать команды PowerShell на основе Azure Resource Manager для управления службой приложений.

Дополнительные сведения об управлении группами ресурсов см. в статье об [управлении ресурсами и группами ресурсов Azure с помощью интерфейса командной строки Azure](../azure-resource-manager/xplat-cli-azure-resource-manager.md). 

> [!NOTE] 
> Попробуйте также [Azure CLI 2.0 (предварительная версия)](https://github.com/Azure/azure-cli). Это интерфейс командной строки нового поколения, написанный на языке Python, для модели развертывания с помощью Resource Manager.
>
>

## <a name="managing-app-service-plans"></a>Управление планами службы приложений
### <a name="create-an-app-service-plan"></a>Создание плана службы приложений
Чтобы создать план службы приложений, используйте команду **azure appserviceplan create**.

Ниже описаны его параметры.

* **--resource-group**: группа ресурсов, в которую входит новый план службы приложений.
* **--name**: имя плана службы приложений.
* **--location**: расположение плана службы.
* **--sku**: ценовая категория (SKU). Варианты: F1 (бесплатный); D1 (общедоступный); B1 (малый базовый), B2 (средний базовый) и B3 (крупный базовый); S1 (малый стандартный), S2 (средний стандартный) и S3 (крупный стандартный); P1 (малый премиум), P2 (средний премиум) и P3 (крупный премиум).
* **--instances**: число рабочих служб в плане службы приложений. Значение по умолчанию — 1.

Пример использования этого командлета:

    azure appserviceplan create --name ContosoAppServicePlan --location "South Central US" --resource-group ContosoAzureResourceGroup --sku P1 --instances 10

#### <a name="create-a-linux-app-service-plan"></a>Создание плана службы приложений Linux

Это можно сделать с помощью той же команды **azure appserviceplan create** с дополнительным параметром **--islinux true**. Обратите внимание на ограничения и регионы, описанные в статье [Вводные сведения о службе приложений на платформе Linux](app-service-linux-intro.md)

### <a name="list-existing-app-service-plans"></a>Получение списка существующих планов службы приложений
Чтобы получить список существующих планов службы приложений, используйте команду **azure appserviceplan list**.

Чтобы получить список всех планов службы приложений для определенной группы ресурсов, используйте такую команду:

    azure appserviceplan list --resource-group ContosoAzureResourceGroup

Чтобы получить определенный план службы приложений, используйте команду **azure appserviceplan show**.

    azure appserviceplan show --name ContosoAppServicePlan --resource-group southeastasia

### <a name="configure-an-existing-app-service-plan"></a>Настройка существующего плана службы приложений
Чтобы изменить параметры существующего плана службы приложений, используйте команду **azure appserviceplan config**. Вы можете изменить SKU и число рабочих ролей. 

    azure appserviceplan config --name ContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1 --instances 9

#### <a name="scaling-an-app-service-plan"></a>Масштабирование плана службы приложений
Чтобы масштабировать существующий план службы приложений, используйте команду:

    azure appserviceplan config --name ContosoAppServicePlan --resource-group ContosoAzureResourceGroup --instances 9

#### <a name="changing-the-sku-of-an-app-service-plan"></a>Изменение SKU для плана службы приложений
Чтобы изменить SKU для существующего плана службы приложений, используйте следующую команду:

    azure appserviceplan config --name ContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1


### <a name="delete-an-existing-app-service-plan"></a>Удаление существующего плана службы приложений
Чтобы удалить существующий план службы приложений, сначала необходимо переместить или удалить все назначенные приложения. Затем с помощью команды **azure webapp delete** можно удалить план службы приложений.

    azure appserviceplan delete --name ContosoAppServicePlan --resource-group southeastasia

## <a name="managing-app-service-apps"></a>Управление приложениями службы приложений
### <a name="create-a-web-app"></a>Создание веб-приложения
Чтобы создать веб-приложение, используйте команду **azure webapp create**.

Ниже описаны его параметры.

* **--name**: имя нового веб-приложения.
* **--plan**: имя плана службы приложений, в котором будет размещено веб-приложение.
* **--resource-group**: группа ресурсов, в которую входит этот план службы приложений.
* **--location**: расположение веб-приложения.

Пример использования этого командлета:

    azure webapp create --name ContosoWebApp --resource-group ContosoAzureResourceGroup --plan ContosoAppServicePlan --location "South Central US"

### <a name="delete-an-existing-app"></a>Удаление существующего приложения
Удалить существующее приложение можно с помощью команды **azure webapp delete**. Необходимо указать имя приложения и имя группы ресурсов.

    azure webapp delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="list-existing-apps"></a>Список существующих приложений
Чтобы получить список существующих приложений, используйте команду **azure webapp list**.

Чтобы получить список всех приложений в определенной группе ресурсов, используйте следующую команду:

    azure webapp list --resource-group ContosoAzureResourceGroup

Чтобы получить определенное приложение, используйте команду **azure webapp show**.

    azure webapp show --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="configure-an-existing-app"></a>Настройка существующего приложения
Чтобы изменить параметры и конфигурацию существующего приложения, используйте команду **azure webapp config set**.

Пример 1. Изменение PHP-версии приложения 

    azure webapp config set --name ContosoWebApp --resource-group ContosoAzureResourceGroup --phpversion 5.6

Пример 2. Добавление или изменение параметров приложения:

    webapp config appsettings set --name ContosoWebApp --resource-group ContosoAzureResourceGroup appsetting1=appsetting1value,appsetting2=appsetting2value

Чтобы узнать, какие еще конфигурации можно изменить, используйте команду **azure webapp config set -h**.

### <a name="change-the-state-of-an-existing-app"></a>Изменение состояния существующего приложения
#### <a name="restart-an-app"></a>Перезапуск приложения
Чтобы перезапустить приложение, укажите имя приложения и его группу ресурсов.

    azure webapp restart --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="stop-an-app"></a>Остановка приложения
Чтобы остановить приложение, укажите имя приложения и его группу ресурсов.

    azure webapp stop --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="start-an-app"></a>Запуск приложения
Чтобы запустить приложение, укажите имя приложения и его группу ресурсов.

    azure webapp start --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="manage-publishing-profiles-of-an-app"></a>Управление профилями публикации приложения
У каждого приложения есть профиль публикации, который может использоваться для публикации кода.

#### <a name="get-publishing-profile"></a>Получение профиля публикации
Чтобы получить профиль публикации для приложения, используйте следующую команду:

    azure webapp publishingprofile --name ContosoWebApp --resource-group ContosoAzureResourceGroup

Эта команда выводит в командной строке имя публикации профиля пользователя и пароль.

### <a name="manage-app-hostnames"></a>Управление именами узлов приложения
Чтобы управлять привязками имен узлов для приложения, используйте команду **azure webapp config hostnames**.  

#### <a name="list-hostname-bindings"></a>Список привязок имен узлов
Чтобы получить текущие привязки имен узлов для приложения, используйте следующую команду:

    azure webapp config hostnames list --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="add-hostname-bindings"></a>Добавление привязок имен узлов
Чтобы добавить привязки имен узлов в приложение, используйте следующую команду:

    azure webapp config hostnames add --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

#### <a name="delete-hostname-bindings"></a>Удаление привязок имен узлов
Чтобы удалить привязки имен узлов, используйте следующую команду:

    azure webapp config hostnames delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о поддержке интерфейса командной строки в Azure Resource Manager см. в статье об [управлении ресурсами и группами ресурсов Azure с помощью интерфейса командной строки Azure](../azure-resource-manager/xplat-cli-azure-resource-manager.md).
* Дополнительные сведения об управлении службой приложений с помощью PowerShell см. в статье [Управление веб-приложениями Azure с помощью PowerShell на основе Azure Resource Manager](app-service-web-app-azure-resource-manager-powershell.md).
* Сведения об использовании службы приложений Azure на платформе Linux см. в статье [Вводные сведения о службе приложений на платформе Linux](app-service-linux-intro.md).



<!--HONumber=Feb17_HO2-->


