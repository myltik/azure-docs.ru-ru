---
title: Создание кластеров Hadoop с помощью шаблонов Azure HDInsight | Документация Майкрософт
description: Узнайте о создании кластеров для HDInsight с помощью шаблонов Resource Manager.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00a80dea-011f-44f0-92a4-25d09db9d996
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: ae5085eb52210844b364113a436fa033da9daac7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202359"
---
# <a name="create-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Создание кластеров Hadoop в HDInsight с помощью шаблонов Resource Manager
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

В этой статье вы изучите несколько способов создания кластеров Azure HDInsight с помощью шаблонов Azure Resource Manager. Узнайте подробнее [о развертывании приложения с помощью шаблона диспетчера ресурсов Azure](../azure-resource-manager/resource-group-template-deploy.md). Сведения о других инструментах и функциях создания кластеров можно получить, воспользовавшись селектором вкладок в верхней части этой страницы или ознакомившись с разделом [Способы создания кластера](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

## <a name="prerequisites"></a>предварительным требованиям
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Для выполнения инструкций из этой статьи понадобится следующее:

* [Подписка Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell и (или) Azure CLI.

### <a name="resource-manager-templates"></a>Шаблоны диспетчера ресурсов
Шаблон Resource Manager упрощает создание ресурсов для приложений, приведенных ниже, с помощью отдельной скоординированной операции:
* кластеры HDInsight и зависимые ресурсы (например, учетная запись хранения по умолчанию);
* другие ресурсы (например, база данных SQL Azure для использования Apache Sqoop).

В шаблоне определяются ресурсы, необходимые для приложения. Можно также указать параметры развертывания в качестве входных значений для различных сред. Шаблон состоит из кода JSON и выражений, на основе которых можно создавать значения для развертывания.

Примеры шаблонов HDInsight можно найти в [коллекции шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight). Используйте кроссплатформенный редактор [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) с [расширением Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) или текстовый редактор, чтобы сохранить шаблон в файл на своей рабочей станции. 

Дополнительные сведения о шаблонах Resource Manager см. в перечисленных ниже статьях.

* [Шаблоны диспетчера ресурсов Azure](../azure-resource-manager/resource-group-authoring-templates.md)
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../azure-resource-manager/resource-group-template-deploy.md)

## <a name="generate-templates"></a>Создание шаблонов

Resource Manager позволяет экспортировать шаблон Resource Manager из имеющихся ресурсов в подписке, используя различные инструменты. Используя созданный шаблон, можно изучить синтаксис шаблонов или при необходимости автоматизировать повторное развертывание решения.

- Портал Azure: см. статью [Экспорт шаблона Azure Resource Manager из существующих ресурсов](../azure-resource-manager/resource-manager-export-template.md).
- Azure PowerShell: см. статью [Экспорт шаблона Azure Resource Manager с помощью PowerShell](../azure-resource-manager/resource-manager-export-template-powershell.md).
- Azure CLI: см. статью [Экспорт шаблонов Azure Resource Manager с помощью Azure CLI](../azure-resource-manager/resource-manager-export-template-cli.md).


## <a name="deploy-using-the-portal"></a>Развертывание с помощью портала

Шаблон Resource Manager можно развернуть с помощью портала Azure. Дополнительные сведения см. в разделе [Развертывание ресурсов с помощью настраиваемого шаблона](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Развертывание с помощью PowerShell

Шаблон Resource Manager можно развернуть с помощью Azure PowerShell. Дополнительные сведения см. в статьях [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) и [Развертывание частного шаблона Resource Manager с использованием токена SAS и Azure PowerShell](../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="deploy-using-cli"></a>Развертывание с помощью интерфейса командной строки

Шаблон Resource Manager можно развернуть с помощью Azure CLI. Дополнительные сведения см. в статьях [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) и [Развертывание частного шаблона Resource Manager с использованием токена SAS и Azure CLI](../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Развертывание с помощью REST API
Шаблон Resource Manager можно развернуть с помощью REST API. Дополнительные сведения см. в статье [Развертывание ресурсов с использованием шаблонов и REST API Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Развертывание с помощью Visual Studio
 С помощью Visual Studio можно создать проект группы ресурсов и развернуть его в Azure, используя пользовательский интерфейс. Выберите тип ресурсов, добавляемых в проект. Эти ресурсы автоматически добавляются в шаблон Resource Manager. Проект также предоставляет сценарий PowerShell для развертывания шаблона.

Обзорные сведения об использовании групп ресурсов в Visual Studio см. в статье [Создание и развертывание групп ресурсов Azure с помощью Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="troubleshoot"></a>Устранение неполадок

Если при создании кластеров HDInsight возникли проблемы, см. раздел [Создание кластеров](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Дополнительная информация
В этой статье вы ознакомились с несколькими способами создания кластера HDInsight. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* Дополнительные шаблоны, связанные с HDInsight, см. в статье [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Пример развертывания ресурсов с помощью клиентской библиотеки .NET см. в статье [Развертывание виртуальной машины Azure с помощью C# и шаблона Resource Manager](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Подробный пример развертывания приложения см. в статье [Предсказуемые подготовка и развертывание микрослужб в Azure](../app-service/app-service-deploy-complex-application-predictably.md).
* Инструкции по развертыванию своего решения в различных средах см. в статье [Среды разработки и тестирования в Microsoft Azure](../solution-dev-test-environments.md).
* Дополнительную информацию о разделах в шаблоне Azure Resource Manager см. в статье [Создание шаблонов диспетчера ресурсов Azure](../azure-resource-manager/resource-group-authoring-templates.md).
* Список функций, которые можно использовать в шаблоне Azure Resource Manager, см. в статье [Функции шаблонов диспетчера ресурсов Azure](../azure-resource-manager/resource-group-template-functions.md).
