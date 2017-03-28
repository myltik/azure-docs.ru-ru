---
title: "Развертывание ресурсов Azure с помощью портала Azure | Документация Майкрософт"
description: "Узнайте, как использовать портал Azure для развертывания ресурсов и управления ими."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 2c98a4aa-8d9f-4a0a-b764-214dbe8ed009
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e4851e872349fa6483e1f1a340d0968e845a3518
ms.openlocfilehash: e74845bfa06bb9a53420260dfb45e72d711f4373
ms.lasthandoff: 01/14/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Развертывание ресурсов с использованием шаблонов Resource Manager и портала Azure
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [Интерфейс командной строки Azure](resource-group-template-deploy-cli.md)
> * [Портал](resource-group-template-deploy-portal.md)
> * [ИНТЕРФЕЙС REST API](resource-group-template-deploy-rest.md)
> 
> 

В этой статье показано, как использовать [портал Azure](https://portal.azure.com) и [Azure Resource Manager](resource-group-overview.md) для развертывания ресурсов Azure. Сведения об управлении ресурсами см. в статье [Управление ресурсами Azure через портал](resource-group-portal.md).

В настоящее время не все службы поддерживают текущую версию портала или диспетчер ресурсов. Для некоторых служб необходимо использовать [классический портал](https://manage.windowsazure.com). Состояние каждой службы можно просмотреть в [таблице доступности портала Azure](https://azure.microsoft.com/features/azure-portal/availability/).

## <a name="create-resource-group"></a>Создать группу ресурсов
1. Чтобы создать пустую группу ресурсов, выберите **Создать** > **Управление** > **Группа ресурсов**.
   
    ![создание группы ресурсов](./media/resource-group-template-deploy-portal/create-empty-group.png)
2. Укажите имя группы, ее расположение и при необходимости выберите подписку. Вам нужно указать расположение группы, так как она хранит метаданные ресурсов. Для соответствия требованиям вам, возможно, нужно указать, где хранятся эти метаданные. Обычно мы рекомендуем указывать расположение, в котором будет храниться большинство ресурсов. Используя одно и то же расположение, можно упростить шаблон.
   
    ![настройка значений группы](./media/resource-group-template-deploy-portal/set-group-properties.png)

## <a name="deploy-resources-from-marketplace"></a>Развертывание ресурсов из Marketplace
Создав группу ресурсов, вы можете развернуть в ней ресурсы из Marketplace. Marketplace предоставляет предварительно определенные решения для распространенных сценариев использования.

1. Чтобы начать развертывание, щелкните **Создать** и выберите тип ресурса, который хотите развернуть. Затем найдите определенную версию ресурса, которую требуется развернуть.
   
    ![развертывание ресурсов](./media/resource-group-template-deploy-portal/deploy-resource.png)
2. Если конкретное решение, которое вы хотите развернуть, отсутствует, его можно поискать в Marketplace.
   
    ![поиск в Marketplace](./media/resource-group-template-deploy-portal/search-resource.png)
3. В зависимости от выбранного типа ресурса вам потребуется задать набор соответствующих свойств, прежде чем начинать развертывание. Здесь эти свойства не показаны, так как они варьируются в зависимости от типа ресурса. Для всех типов необходимо выбрать целевую группу ресурсов. Ниже показано создание веб-приложения и его развертывание в уже созданной группе ресурсов.
   
    ![Создать группу ресурсов](./media/resource-group-template-deploy-portal/select-existing-group.png)
   
    Кроме того, можно создать новую группу ресурсов непосредственно при развертывании ресурсов. Щелкните **Создать** и укажите имя группы ресурсов.
   
    ![создание новой группы ресурсов](./media/resource-group-template-deploy-portal/select-new-group.png)
4. Начнется развертывание. Это может занять несколько минут. По окончании развертывания появится уведомление.
   
    ![просмотр уведомления](./media/resource-group-template-deploy-portal/view-notification.png)
5. Развернув ресурсы, вы можете добавлять в группу другие ресурсы с помощью команды **Добавить** в колонке этой группы ресурсов.
   
    ![Добавить ресурсы](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Развертывание ресурсов с помощью настраиваемого шаблона
Если вы хотите выполнить развертывание без использования шаблонов из Marketplace, создайте настраиваемый шаблон, определяющий инфраструктуру для вашего решения. Сведения о создании шаблонов см. в статье [Создание шаблонов диспетчера ресурсов Azure](resource-group-authoring-templates.md).

1. Чтобы развернуть настроенный шаблон на портале, выберите **Создать** и начните поиск по словам **Развертывание шаблона**, пока не появится соответствующий пункт.
   
    ![поиск развертывания шаблона](./media/resource-group-template-deploy-portal/search-template.png)
2. В списке доступных ресурсов выберите **Развертывание шаблона** .
   
    ![выберите "развертывание шаблона"](./media/resource-group-template-deploy-portal/select-template.png)
3. После запуска развертывания шаблона откройте пустой шаблон, доступный для настройки.
   
    ![создание шаблона](./media/resource-group-template-deploy-portal/show-custom-template.png)
   
    В редакторе добавьте данные в формате JSON, определяющие ресурсы, которые требуется развернуть. По завершении нажмите кнопку **Сохранить**. Рекомендации по созданию данных в формате JSON см. в [пошаговом руководстве по созданию шаблона Resource Manager](resource-manager-template-walkthrough.md).
   
    ![изменение шаблона](./media/resource-group-template-deploy-portal/edit-template.png)
4. Кроме того, можно выбрать готовый шаблон на странице [шаблонов быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/). Эти шаблоны предоставлены сообществом. Они охватывают множество распространенных сценариев, а кто-то мог добавить шаблон, который подходит и вам. Выполните поиск, чтобы найти шаблон, соответствующий вашему сценарию.
   
    ![выбор шаблона быстрого запуска](./media/resource-group-template-deploy-portal/select-quickstart-template.png)
   
    Выбранный шаблон можно просмотреть в редакторе.
5. Указав все остальные значения, нажмите кнопку **Создать** , чтобы развернуть шаблон. 
   
    ![развертывание шаблона](./media/resource-group-template-deploy-portal/create-custom-deploy.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Развертывание ресурсов с помощью шаблона, сохраненного в учетной записи
Портал позволяет сохранить шаблон в свою учетную запись Azure для последующего повторного развертывания. Дополнительные сведения о работе с сохраненными шаблонами см. в статье [Начало работы с частными шаблонами на портале Azure](../marketplace-consumer/mytemplates-getstarted.md).

1. Чтобы найти сохраненные шаблоны, выберите **Обзор** > **Шаблоны**.
   
    ![просмотр шаблонов](./media/resource-group-template-deploy-portal/browse-templates.png)
2. Из списка шаблонов, сохраненных в вашей учетной записи, выберите тот, с которым вы хотите работать.
   
    ![сохраненные шаблоны](./media/resource-group-template-deploy-portal/saved-templates.png)
3. Нажмите кнопку **Развернуть** , чтобы повторно развернуть этот сохраненный шаблон.
   
    ![развертывание сохраненного шаблона](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>Дальнейшие действия
* Сведения о просмотре журналов аудита см. в статье [Операции аудита с помощью Resource Manager](resource-group-audit.md).
* Сведения об устранении неполадок развертывания см. в статье [Просмотр операций развертывания с помощью Azure Resource Manager](resource-manager-deployment-operations.md).
* Чтобы извлечь шаблон из развернутой службы или группы ресурсов, ознакомьтесь со статьей [Экспорт шаблона Azure Resource Manager из существующих ресурсов](resource-manager-export-template.md).
* Руководство по использованию Resource Manager для эффективного управления подписками в организациях см [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Шаблон Azure для организаций. Рекомендуемая система управления подпиской).
* Сведения об автоматизации развертывания см. в статье [Automating application deployments to Azure Virtual Machines](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Автоматизация развертывания приложений на виртуальных машинах Azure), которая входит в цикл из четырех частей. Этот цикл охватывает такие аспекты, как архитектура приложения, доступ и безопасность, доступность и масштабирование, а также развертывание приложения.


