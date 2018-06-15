---
title: включение файла
description: включение файла
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 3009948c72b0cad6ba603a3ccbe35db8d93fe2c7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664736"
---
## <a name="storage"></a>Служба хранилища

|  |  |
|---------|---------|
| [Допустимые номера SKU для учетных записей хранения и виртуальных машин](../articles/azure-policy/scripts/allowed-skus-storage.md) | Требует, чтобы учетные записи хранения и виртуальные машины использовали утвержденные номера SKU. Для обеспечения утвержденных номеров SKU используются встроенные политики. Вы можете указать массив утвержденных номеров SKU виртуальных машин и учетных записей хранения. |
| [Допустимые SKU учетной записи хранения](../articles/azure-policy/scripts/allowed-stor-acct-skus.md) | Требует, чтобы учетные записи хранения использовали допустимые номера SKU. Нужно указать массив утвержденных номеров SKU. |
| [Запрет на распределение по холодным уровням доступа для учетных записей хранения](../articles/azure-policy/scripts/deny-cool-access-tiering.md) | Запрещает распределение по холодным уровням доступа для учетных записей хранения больших двоичных объектов.  |
| [Использование HTTPS-трафика для учетной записи хранения](../articles/azure-policy/scripts/ensure-https-stor-acct.md) | Требует, чтобы учетные записи хранения использовали HTTPS-трафик.  |
| [Обеспечение шифрования файлов для хранилища](../articles/azure-policy/scripts/ensure-store-file-enc.md) | Требует, чтобы для учетных записей хранения было включено шифрование файлов.  |
| [Требование шифрования учетной записи хранения](../articles/azure-policy/scripts/req-store-acct-enc.md) | Требует, чтобы учетная запись хранения использовала шифрование больших двоичных объектов.  |