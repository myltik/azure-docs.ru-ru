---
title: Развертывание ресурсов Azure с помощью портала Azure | Документация Майкрософт
description: Узнайте, как использовать портал Azure для развертывания ресурсов и управления ими.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2017
ms.author: tomfitz
ms.openlocfilehash: c2232e1ce92eb6198b22bad22b8c3e10bab6bf12
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Развертывание ресурсов с использованием шаблонов Resource Manager и портала Azure

В этой статье показано, как использовать [портал Azure](https://portal.azure.com) и [Azure Resource Manager](resource-group-overview.md) для развертывания ресурсов Azure. Сведения об управлении ресурсами см. в статье [Управление ресурсами Azure через портал](resource-group-portal.md).

## <a name="create-resource-group"></a>Создать группу ресурсов

1. Чтобы создать пустую группу ресурсов, выберите **Группы ресурсов**.

   ![Выбор групп ресурсов](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. В разделе "Группы ресурсов" выберите **Добавить**.

   ![Добавление группы ресурсов](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Укажите имя группы, ее расположение и при необходимости выберите подписку. Вам нужно указать расположение группы, так как она хранит метаданные ресурсов. Для соответствия требованиям вам, возможно, нужно указать, где хранятся эти метаданные. Обычно мы рекомендуем указывать расположение, в котором будет храниться большинство ресурсов. Используя одно и то же расположение, можно упростить шаблон.

   ![Настройка значений группы](./media/resource-group-template-deploy-portal/set-group-properties.png)

   После завершения задания свойств выберите **Создать**.

1. Чтобы просмотреть новую группу ресурсов, выберите **Обновить**.

   ![Обновление групп ресурсов](./media/resource-group-template-deploy-portal/refresh-resource-groups.png)

## <a name="deploy-resources-from-marketplace"></a>Развертывание ресурсов из Marketplace

Создав группу ресурсов, вы можете развернуть в ней ресурсы из Marketplace. Marketplace предоставляет предварительно определенные решения для распространенных сценариев использования.

1. Чтобы начать развертывание, выберите **Создать ресурс**.

   ![Новый ресурс](./media/resource-group-template-deploy-portal/new-resources.png)

1. Найдите тип ресурса, который нужно развернуть.

   ![Выбор типа ресурсов](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. Если конкретное решение, которое вы хотите развернуть, отсутствует, его можно поискать в Marketplace. Например, чтобы найти решение Wordpress, начните вводить **Wordpress** и выберите нужный вариант.

   ![Поиск в Marketplace](./media/resource-group-template-deploy-portal/search-resource.png)

1. В зависимости от выбранного типа ресурса вам потребуется задать набор соответствующих свойств, прежде чем начинать развертывание. Для всех типов необходимо выбрать целевую группу ресурсов. Ниже показано создание веб-приложения и его развертывание в уже созданной группе ресурсов.

   ![Создать группу ресурсов](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Кроме того, можно создать новую группу ресурсов непосредственно при развертывании ресурсов. Щелкните **Создать** и укажите имя группы ресурсов.

   ![Создание группы ресурсов](./media/resource-group-template-deploy-portal/select-new-group.png)

1. Начнется развертывание. Это может занять несколько минут. По окончании развертывания появится уведомление.

   ![Просмотр уведомления](./media/resource-group-template-deploy-portal/view-notification.png)

1. После развертывания ресурсов можно добавить дополнительные ресурсы в группу ресурсов, выбрав **Добавить**.

   ![Добавление ресурса](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Развертывание ресурсов с помощью настраиваемого шаблона

Если вы хотите выполнить развертывание без использования шаблонов из Marketplace, создайте настраиваемый шаблон, определяющий инфраструктуру для вашего решения. Дополнительные сведения о создании шаблонов см. в статье [Описание структуры и синтаксиса шаблонов Azure Resource Manager](resource-group-authoring-templates.md).

1. Чтобы развернуть настроенный шаблон на портале, выберите **Создать ресурс** и начните поиск по словам **Развертывание шаблона**, пока не появится соответствующий пункт.

   ![Поиск развертывания шаблона](./media/resource-group-template-deploy-portal/search-template.png)

1. Нажмите кнопку **Создать**.

   ![Выбор кнопки "Создать"](./media/resource-group-template-deploy-portal/show-template-option.png)

1. Отобразится несколько вариантов создания шаблона. Выберите **Создать собственный шаблон в редакторе**.

   ![Параметры представления](./media/resource-group-template-deploy-portal/see-options.png)

1. У вас есть пустой шаблон, который можно настроить.

   ![Создание шаблона](./media/resource-group-template-deploy-portal/blank-template.png)

1. Синтаксис JSON можно изменить вручную или выбрать готовый шаблон из [коллекции шаблонов быстрого запуска](https://azure.microsoft.com/resources/templates/). Однако в этой статье используется параметр **Добавить ресурс**.

   ![Изменить шаблон](./media/resource-group-template-deploy-portal/select-add-resource.png)

1. Выберите **Учетная запись хранения** и укажите имя. По завершении ввода значений выберите **ОК**.

   ![Выбор учетной записи хранения](./media/resource-group-template-deploy-portal/add-storage-account.png)

1. Редактор автоматически добавляет JSON в качестве типа ресурса. Обратите внимание, что он включает параметр, определяющий тип учетной записи хранения. Щелкните **Сохранить**.

   ![Отображение шаблона](./media/resource-group-template-deploy-portal/show-json.png)

1. Теперь у вас есть возможность развернуть ресурсы, заданные в шаблоне. Чтобы развернуть их, согласитесь с условиями и выберите **Приобрести**.

   ![Развертывание шаблона](./media/resource-group-template-deploy-portal/provide-custom-template-values.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Развертывание ресурсов с помощью шаблона, сохраненного в учетной записи

Портал позволяет сохранить шаблон в свою учетную запись Azure для последующего повторного развертывания. Дополнительные сведения о работе с сохраненными шаблонами см. в статье [Начало работы с частными шаблонами на портале Azure](../marketplace-consumer/mytemplates-getstarted.md).

1. Чтобы найти сохраненные шаблоны, выберите **Другие службы**.

   ![Другие службы](./media/resource-group-template-deploy-portal/more-services.png)

1. Найдите **шаблоны** и выберите соответствующий вариант.

   ![Поиск шаблонов](./media/resource-group-template-deploy-portal/find-templates.png)

1. Из списка шаблонов, сохраненных в вашей учетной записи, выберите тот, с которым вы хотите работать.

   ![Сохраненные шаблоны](./media/resource-group-template-deploy-portal/saved-templates.png)

1. Нажмите кнопку **Развернуть** , чтобы повторно развернуть этот сохраненный шаблон.

   ![Развертывание сохраненного шаблона](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>Дополнительная информация
* Сведения о просмотре журналов аудита см. в статье [Операции аудита с помощью Resource Manager](resource-group-audit.md).
* Сведения об устранении неполадок развертывания см. в статье [Просмотр операций развертывания с помощью Azure Resource Manager](resource-manager-deployment-operations.md).
* Чтобы извлечь шаблон из развернутой службы или группы ресурсов, ознакомьтесь со статьей [Экспорт шаблона Azure Resource Manager из существующих ресурсов](resource-manager-export-template.md).
* Инструкции по использованию Resource Manager для эффективного управления подписками в организациях см. в статье [Корпоративный каркас Azure: рекомендуемая система управления подписками](resource-manager-subscription-governance.md).
