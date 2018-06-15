---
title: Примеры шаблонов политики | Документация Майкрософт
description: Примеры шаблонов службы "Политика Azure" для виртуальной сети.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 05/02/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 2b8766a5353b015030872176e9032034afb7cb9d
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32779589"
---
# <a name="azure-policy-sample-templates-for-virtual-network"></a>Примеры шаблонов службы "Политика Azure" для виртуальной сети

В следующей таблице представлены ссылки на шаблоны службы [Политика Azure](../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Эти примеры можно найти в [репозитории примеров службы "Политика Azure"](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Сеть**||
| [Конкретная группа безопасности сети на каждой сетевой карте](../azure-policy/scripts/nsg-on-nic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Требует, чтобы определенная группа безопасности сети использовалась для каждого виртуального сетевого интерфейса. Вы можете указать идентификатор требуемой группы безопасности сети. |
| [Конкретная группа безопасности сети в каждой подсети](../azure-policy/scripts/nsg-on-subnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Требует, чтобы определенная группа безопасности сети использовалась для каждой виртуальной подсети. Вы можете указать идентификатор требуемой группы безопасности сети. |
| [Без таблицы маршрутов](../azure-policy/scripts/no-user-def-route-table.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  |Предотвращает развертывание виртуальных сетей с использованием таблицы маршрутов. |
| [Использование утвержденной подсети для сетевых интерфейсов виртуальной машины](../azure-policy/scripts/use-approved-subnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Требует, чтобы сетевые интерфейсы использовали утвержденную подсеть. Нужно указать идентификатор утвержденной подсети. |
| [Использование утвержденной виртуальной сети для сетевых интерфейсов виртуальной машины](../azure-policy/scripts/use-approved-vnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Требует, чтобы сетевые интерфейсы использовали утвержденную виртуальную сеть. Вы можете указать идентификатор утвержденной виртуальной сети. |
|**Мониторинг**||
| [Аудит параметров диагностики](../azure-policy/scripts/audit-diag-setting.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Проверяет, включены ли параметры диагностики для определенных типов ресурсов. Вы можете указать массив типов ресурсов, чтобы проверить, включены ли параметры диагностики. |
|**Соглашение об именовании и тексте**||
| [Разрешение на использование нескольких шаблонов имен](../azure-policy/scripts/allow-multiple-name-patterns.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Разрешение на применение одного из нескольких шаблонов имен для ресурсов. |
| [Требовать шаблон схожести](../azure-policy/scripts/enforce-like-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Убедитесь, что имена ресурсов *соответствуют* условию схожести с шаблоном. |
| [Требовать шаблон совпадения](../azure-policy/scripts/enforce-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Убедитесь, что имена ресурсов совпадают с определенным шаблоном именования. |
| [Требовать шаблон совпадения тегов](../azure-policy/scripts/enforce-tag-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Убедитесь, что значение тега соответствует текстовому шаблону. |
|**Теги**||
| [Инициатива политики тегов для выставления счетов](../azure-policy/scripts/billing-tags-policy-init.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Требует указать значения тега для обозначения места возникновения затрат и названия продукта. Используются встроенные политики для применения и принудительного назначения требуемых тегов. Нужно указать необходимые значения для тегов.  |
| [Принудительное применение тега и его значения в группе ресурсов](../azure-policy/scripts/enforce-tag-rg.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Требует указать тег и значение в группе ресурсов. Вы можете указать имя и значение требуемого тега.  |
| [Принудительное применение тега и его значения](../azure-policy/scripts/enforce-tag-val.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Требует имя и значение указанного тега. Вы можете указать имя и значение тега для принудительного применения.  |
| [Применение тега и значения по умолчанию](../azure-policy/scripts/apply-tag-def-val.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Добавляет имя и значение указанного тега (если этот тег не предоставлен). Вы можете указать имя и значение применяемого тега.  |
|**Общие сведения**||
| [Допустимые расположения](../azure-policy/scripts/allowed-locs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Требует, чтобы все ресурсы развертывались в допустимых расположениях. Нужно указать массив утвержденных расположений.  |
| [Допустимые типы ресурсов](../azure-policy/scripts/allowed-res-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Обеспечивает развертывание только допустимых типов ресурсов. Вы можете указать массив допустимых типов ресурсов.  |
| [Недопустимые типы ресурсов](../azure-policy/scripts/not-allowed-res-type.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Запрещает развертывание указанных типов ресурсов. Нужно указать массив типов ресурсов, которые нужно блокировать.  |