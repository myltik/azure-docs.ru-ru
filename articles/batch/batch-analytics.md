---
title: Пакетная аналитика Azure | Документы Майкрософт
description: Справочник по пакетной аналитике Azure.
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
ms.openlocfilehash: 4c81acb282b24bbd899227c4dcc449fed4ba6c7d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30312683"
---
# <a name="batch-analytics"></a>Пакетная аналитика
Статьи по пакетной аналитике содержат справочные сведения о событиях и оповещениях, доступных для ресурсов пакетной службы.

Статья [Ведение журналов диагностики пакетной службы Azure](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) содержит дополнительные сведения о включении и использовании журналов диагностики пакетной службы.

## <a name="diagnostic-logs"></a>Журналы диагностики

За время существования некоторых пакетных ресурсов пакетная служба Azure выдает приведенные ниже события журнала диагностики.

**События журнала службы**
* [Создание пула](batch-pool-create-event.md)
* [Начало удаления пула](batch-pool-delete-start-event.md)
* [Завершение удаления пула](batch-pool-delete-complete-event.md)
* [Начало изменения размера пула](batch-pool-resize-start-event.md)
* [Завершение изменения размера пула](batch-pool-resize-complete-event.md)
* [Начало выполнения задачи](batch-task-start-event.md)
* [Завершение выполнения задачи](batch-task-complete-event.md)
* [Сбой выполнения задачи](batch-task-fail-event.md)