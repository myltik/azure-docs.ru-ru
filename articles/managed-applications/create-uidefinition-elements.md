---
title: Создание определений элементов пользовательского интерфейса для портала Azure | Документация Майкрософт
description: Сведения об элементах, используемых при создании определений пользовательского интерфейса для портала Azure.
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
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: d6f96d4aa66839518023b4d567caf1ff839a29fb
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="createuidefinition-elements"></a>Элементы CreateUiDefinition
В этой статье описывается схема и свойства всех поддерживаемых элементов CreateUiDefinition. Для большинства элементов схема выглядит следующим образом:

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "my value",
  "toolTip": "Provide a descriptive name.",
  "constraints": {},
  "options": {},
  "visible": true
}
```

| Свойство | Обязательно | ОПИСАНИЕ |
| -------- | -------- | ----------- |
| name | Yes | Внутренний идентификатор для ссылки на конкретный экземпляр элемента. Чаще всего имя элемента используется в `outputs`, где значения выходных данных указанных элементов сопоставляются с параметрами шаблона. Его также можно использовать для привязки выходного значения элемента к `defaultValue` другого элемента. |
| Тип | Yes | Элемент управления пользовательского интерфейса для обработки элемента. Список поддерживаемых типов см. в разделе [Элементы](#elements). |
| label | Yes | Отображаемый текст элемента. Некоторые типы элементов содержат несколько меток, поэтому значение может быть объектом, содержащим несколько строк. |
| defaultValue | Нет  | Значение элемента по умолчанию. Некоторые типы элементов поддерживают сложные значения по умолчанию, поэтому значение может быть объектом. |
| toolTip | Нет  | Текст для отображения в подсказке элемента. Аналогично `label` некоторые элементы поддерживают несколько строк с подсказками. С помощью синтаксиса Markdown можно внедрить встроенные ссылки.
| constraints | Нет  | Одно или несколько свойств, используемых для настройки поведения проверки элемента. Поддерживаемые свойства для constraints зависят от типа элемента. Некоторые типы элементов не поддерживают настройку поведения проверки и поэтому не содержат свойство constraints. |
| options | Нет  | Дополнительные свойства, позволяющие настроить поведение элемента. Аналогично `constraints` поддерживаемые свойства зависят от типа элемента. |
| visible | Нет  | Указывает, отображается ли элемент. Если задано значение `true`, элемент и применимые дочерние элементы будут отображены. По умолчанию используется значение `true`. Используйте [логические функции](create-uidefinition-functions.md#logical-functions), чтобы динамически управлять значением свойства.

## <a name="elements"></a>Элементы

Документация для каждого элемента содержит примеры пользовательского интерфейса, схемы, примечания о поведении элемента (обычно в отношении проверки и поддерживаемой настройки) и пример выходных данных.

- [Элемент пользовательского интерфейса Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Элемент пользовательского интерфейса Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Элемент пользовательского интерфейса Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Элемент пользовательского интерфейса Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Элемент пользовательского интерфейса Microsoft.Common.Section](microsoft-common-section.md)
- [Элемент пользовательского интерфейса Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Элемент пользовательского интерфейса Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Элемент пользовательского интерфейса Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Элемент пользовательского интерфейса Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Элемент пользовательского интерфейса Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Элемент пользовательского интерфейса Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Элемент пользовательского интерфейса Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Элемент пользовательского интерфейса Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Дополнительная информация
Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
