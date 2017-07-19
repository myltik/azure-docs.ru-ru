---
title: "Включение и отключение мониторинга виртуальной машины Azure"
description: "В этой статье описывается включение или отключение мониторинга виртуальной машины Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: kmouss
manager: timlt
editor: 
ms.assetid: 6ce366d2-bd4c-4fef-a8f5-a3ae2374abcc
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/08/2016
ms.author: kmouss
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 9b2fe579113d6ca6bfd27d82eb9d4657657d44ff
ms.contentlocale: ru-ru
ms.lasthandoff: 05/25/2017

---
# <a name="enable-or-disable-azure-vm-monitoring"></a>Включение и отключение мониторинга виртуальной машины Azure

В этом разделе описывается, как включить или отключить мониторинг виртуальных машин, работающих в Azure. Мониторинг можно включить или отключить с помощью портала или интерфейса командной строки Azure для Mac, Linux и Windows (Azure CLI).

> [!IMPORTANT]
> В этом документе описывается версия 2.3 диагностического расширения для Linux, которая признана нерекомендуемой. Версия 2.3 будет поддерживаться до 30 июня 2018 г.
>
> Вместо нее можно включить диагностическое расширение для Linux версии 3.0. Дополнительные сведения см. в [документации](./diagnostic-extension.md).

## <a name="enable--disable-monitoring-through-the-azure-portal"></a>Включение и отключение мониторинга через портал Azure

Вы можете включить мониторинг виртуальной машины Azure, который предоставляет данные о вашем экземпляре с интервалом в 1 минуту (применяются изменения хранилища). В результате на диаграммах портала или с помощью API становятся доступными подробные диагностические данные для виртуальной машины. По умолчанию портал Azure обеспечивает мониторинг ограниченного набора метрик на основе узла. Включить мониторинг метрик в виртуальной машине можно, когда виртуальная машина работает или остановлена.

* Откройте портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).
* В левой панели навигации щелкните "Виртуальные машины".
* В списке виртуальных машин выберите запущенный или остановленный экземпляр. Откроется колонка "Виртуальная машина".
* Щелкните "Все параметры".
* Щелкните "Диагностика".
* Измените состояние с "Вкл" на "Выкл". В этой колонке также можно выбрать уровень детализации мониторинга для виртуальной машины.

![Включение и отключение мониторинга через портал Azure.][1]

## <a name="enable--disable-monitoring-with-azure-cli"></a>Включение и отключение мониторинга с помощью интерфейса командной строки Azure

Чтобы включить мониторинг для виртуальной машины Azure, выполните следующие действия.

* Создайте файл (например, с именем PrivateConfig.json):

```json
{
        "storageAccountName":"the storage account to receive data",
        "storageAccountKey":"the key of the account"
}
```

* Включение расширения через Azure CLI.

```azurecli
azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.3 --private-config-path PrivateConfig.json
```

Дополнительные сведения см. в статье [Использование диагностического расширения Linux для мониторинга данных о состоянии и производительности виртуальных машин под управлением Linux](classic/diagnostic-extension-v2.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

<!--Image references-->
[1]: ./media/vm-monitoring/portal-enable-disable.png

