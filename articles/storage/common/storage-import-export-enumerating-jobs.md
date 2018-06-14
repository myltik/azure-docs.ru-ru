---
title: Вывод списка всех заданий импорта и экспорта Azure | Документация Майкрософт
description: Узнайте, как вывести список всех заданий службы импорта и экспорта Azure в подписке.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: f2e619be-1bbd-4a54-9472-9e2f70a83b64
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 1977bfc0e516088310f45ecdd960287eeed2c2d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23059389"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Перечисление заданий в службе импорта и экспорта Azure
Чтобы перечислить все задания в подписке, вызовите операцию [List Jobs](/rest/api/storageimportexport/jobs#Jobs_List). `List Jobs` возвращает список заданий, а также перечисленные ниже атрибуты:

-   тип задания (импорт или экспорт);

-   текущее состояние задания;

-   связанная с задание учетная запись хранения.

## <a name="next-steps"></a>Дополнительная информация

* [Использование REST API службы импорта и экспорта Azure](storage-import-export-using-the-rest-api.md)
