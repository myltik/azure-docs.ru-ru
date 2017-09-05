---
title: "Общие сведения о создании определения пользовательского интерфейса для управляемых приложений Azure | Документация Майкрософт"
description: "Сведения о создании определений пользовательского интерфейса для управляемых приложений Azure"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 176b891538f85c5638a2321561c3d8bd377d245b
ms.contentlocale: ru-ru
ms.lasthandoff: 05/11/2017

---
# <a name="getting-started-with-createuidefinition"></a>Начало работы с CreateUiDefinition
В этом документе приводится обзор ключевых понятий определения CreateUiDefinition, которое используется на портале Azure, чтобы сгенерировать пользовательский интерфейс для создания управляемого приложения.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json",
   "handler": "Microsoft.Compute.MultiVm",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { }
   }
}
```

CreateUiDefinition всегда содержит три свойства: 

* handler;
* версия
* parameters

Для управляемых приложений handler должен всегда иметь значение `Microsoft.Compute.MultiVm`, а последняя поддерживаемая версия — `0.1.2-preview`.

Схема свойства parameters зависит от сочетания определенных значений handler и version. Для управляемых приложений поддерживаются свойства `basics`, `steps` и `outputs`. Свойства basics и steps содержат _элементы_, например текстовые поля и раскрывающиеся списки, которые отображаются на портале Azure. Свойство output используется для сопоставления значений определенных элементов с параметрами шаблона развертывания Azure Resource Manager.

Советуем также использовать параметр `$schema`, но это необязательно. При его использовании значение параметра `version` должно совпадать с версией в URI `$schema`.

## <a name="basics"></a>Основы
Шаг basics всегда является первым шагом мастера, который создается, когда портал Azure анализирует CreateUiDefinition. Помимо отображения элементов, указанных в `basics`, портал вводит элементы, чтобы пользователи выбрали подписку, группу ресурсов и местоположение для развертывания. Как правило, на этом шаге должны выполняться элементы, запрашивающие параметры для всего развертывания, такие как имя кластера или учетные данные администратора.

Если поведение элемента зависит от подписки пользователя, группы ресурсов или местоположения, тогда он не может использоваться на шаге basics. Например, **Microsoft.Compute.SizeSelector** определяет список доступных размеров в зависимости от подписки пользователя и местоположения. Таким образом, **Microsoft.Compute.SizeSelector** может использоваться только в steps. Как правило, только элементы в пространстве имен **Microsoft.Common** могут использоваться в basics. Хотя некоторые элементы в других пространствах имен (например, **Microsoft.Compute.Credentials**), которые не зависят от контекста пользователя, по-прежнему разрешены.

## <a name="steps"></a>Действия
Свойство steps может содержать как ноль, так и несколько дополнительных шагов для отображения после basics, каждый из которых содержит один или несколько элементов. Вы можете добавить действия для каждой роли или уровня развертываемого приложения. Например, действие для входных данных главных узлов и действие для рабочих узлов в кластере.

## <a name="outputs"></a>outputs
Портал Azure использует свойство `outputs` для сопоставления элементов `basics` и `steps` с параметрами шаблона развертывания Azure Resource Manager. Ключами являются имена параметров шаблона, а значениями — свойства выходных объектов из элементов, на которые даются ссылки.

## <a name="functions"></a>Функции
Аналогично [функциям шаблонов](resource-group-template-functions.md) в Azure Resource Manager (и синтаксис, и функции) CreateUiDefinition предоставляет функции для работы с входными и выходными данными элементов, а также условные выражения.

## <a name="next-steps"></a>Дальнейшие действия
CreateUiDefinition имеет простую схему. Заложенные в нем возможности раскрываются с помощью всех поддерживаемых элементов и функций, которые подробно описаны в следующих документах:

- [CreateUiDefinition elements](managed-application-createuidefinition-elements.md) (Элементы CreateUiDefinition)
- [Функции](managed-application-createuidefinition-functions.md)

Текущая схема JSON для CreateUiDefinition доступна здесь: https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json. 

Более поздние версии будут доступны там же. Замените часть `0.1.2-preview` URL-адреса и значение `version` идентификатором версии, которую вы собираетесь использовать. Сейчас поддерживаются идентификаторы версий `0.0.1-preview`, `0.1.0-preview`, `0.1.1-preview` и `0.1.2-preview`.
