---
title: Событие создания пула пакетной службы Azure | Документы Майкрософт
description: Справочник по событию создания пула пакетной службы.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: danlep
ms.openlocfilehash: bf7dfc2600c3d94faeb8d03561f6f2b30a0ee2d2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30316993"
---
# <a name="pool-create-event"></a>Событие создания пула

 Это событие возникает при создании пула. Содержимое журнала предоставляет общие сведения о пуле. Обратите внимание, что если целевой размер пула больше 0 вычислительных узлов, сразу после этого события начинается событие начала изменения размера пула.

 В следующем примере приведен текст события создания для пула, сформированного с помощью свойства CloudServiceConfiguration.

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicated": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|Элемент|Тип|Заметки|
|-------------|----------|-----------|
|id|Строка|Идентификатор пула.|
|displayName|string|Отображаемое имя пула.|
|vmSize|Строка|Размер виртуальных машин в пуле. Все виртуальные машины в пуле имеют одинаковый размер. <br/><br/> Сведения о доступных размерах виртуальных машин для пулов облачных служб (пулы, созданные с помощью cloudServiceConfiguration), см. в статье [Размеры для облачных служб](http://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/). Пакетная служба поддерживает все размеры виртуальных машин для облачных служб, кроме `ExtraSmall`.<br/><br/> Сведения о доступных размерах виртуальных машин для пулов при использовании образов из магазина виртуальных машин (пулы, созданные с помощью virtualMachineConfiguration) см. в статье [Размеры виртуальных машин](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) (Linux) или [Размеры виртуальных машин](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows). Пакетная служба поддерживает все размеры виртуальных машин Azure, кроме `STANDARD_A0`. Для хранилища класса Premium также не поддерживаются размеры таких серий: `STANDARD_GS`, `STANDARD_DS` и `STANDARD_DSV2`.|
|[cloudServiceConfiguration](#bk_csconf)|Сложный тип|Конфигурация облачной службы для пула.|
|[virtualMachineConfiguration](#bk_vmconf)|Сложный тип|Конфигурация виртуальной машины для пула.|
|[networkConfiguration](#bk_netconf)|Сложный тип|Конфигурация сети для пула.|
|resizeTimeout|Время|Время ожидания при выделении вычислительных узлов для пула, указанное для последней операции изменения размера в этом пуле.  (Исходная регулировка размера при создании пула считается изменением размера.)|
|targetDedicated|Int32|Число вычислительных узлов, запрошенных для пула.|
|enableAutoScale|Bool|Указывает, корректируется ли размер пула автоматически с течением времени.|
|enableInterNodeCommunication|Bool|Указывает, настроен ли пул для прямой связи между узлами.|
|isAutoPool|Bool|Определяет, создан ли пул с помощью механизма AutoPool задания.|
|maxTasksPerNode|Int32|Максимальное число задач, которые могут быть запущены одновременно на одном вычислительном узле в пуле.|
|vmFillType|Строка|Определяет, каким образом пакетная служба распределяет задачи между вычислительными узлами в пуле. Допустимые значения: "Spread" (Распределение) или "Pack" (Упаковка).|

###  <a name="bk_csconf"></a> cloudServiceConfiguration

|Имя элемента|type|Заметки|
|------------------|----------|-----------|
|osFamily|Строка|Семейство гостевой ОС Azure для установки на виртуальных машинах в пуле.<br /><br /> Возможные значения:<br /><br /> **2** — семейство ОС 2, эквивалентное Windows Server 2008 R2 с пакетом обновления 1 (SP1).<br /><br /> **3** — семейство ОС 3, эквивалентное Windows Server 2012.<br /><br /> **4** — семейство ОС 4, эквивалентное Windows Server 2012 R2.<br /><br /> Дополнительные сведения см. в статье [Выпуски гостевой ОС Azure](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|
|targetOSVersion|Строка|Версия гостевой ОС Azure для установки на виртуальных машинах в пуле.<br /><br /> Значение по умолчанию — **\***, что означает последнюю версию операционной системы для заданного семейства.<br /><br /> Другие допустимые значения см. в статье [Выпуски гостевой ОС Azure](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|

###  <a name="bk_vmconf"></a> virtualMachineConfiguration

|Имя элемента|type|Заметки|
|------------------|----------|-----------|
|[imageReference](#bk_imgref)|Сложный тип|Указывает информацию об используемой платформе или образе Marketplace.|
|nodeAgentSKUId|Строка|Номер SKU для агента узла пакетной службы, подготовленного на вычислительном узле.|
|[windowsConfiguration](#bk_winconf)|Сложный тип|Указывает параметры операционной системы Windows на виртуальной машине. Это свойство не нужно задавать, если imageReference ссылается на образ операционной системы Linux.|

###  <a name="bk_imgref"></a> imageReference

|Имя элемента|type|Заметки|
|------------------|----------|-----------|
|publisher|Строка|Издатель образа.|
|offer|Строка|Предложение образа.|
|sku|Строка|Номер SKU образа.|
|версия|Строка|Версия образа.|

###  <a name="bk_winconf"></a> windowsConfiguration

|Имя элемента|type|Заметки|
|------------------|----------|-----------|
|enableAutomaticUpdates|Логическое|Указывает, включено ли для виртуальной машины автоматическое обновление. Если это свойство не задано, используется значение по умолчанию true.|

###  <a name="bk_netconf"></a> networkConfiguration

|Имя элемента|type|Заметки|
|------------------|--------------|----------|
|subnetId|Строка|Указывает идентификатор ресурса для подсети, в которой создаются вычислительные узлы пула.|
