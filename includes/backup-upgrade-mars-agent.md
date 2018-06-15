---
title: Обновление агента службы Azure Backup
description: Здесь объясняется, почему необходимо обновить агент службы Azure Backup и откуда можно скачать обновление.
services: backup
cloud: ''
suite: ''
author: markgalioto
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 3/16/2018
ms.author: markgal
ms.openlocfilehash: 4142f88c3ccb376acbdd2af07c5cfdf8fd275780
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/17/2018
ms.locfileid: "29973305"
---
## <a name="upgrade-the-mars-agent"></a>Обновление агента MARS
Версии агента службы восстановления Microsoft Azure (MARS) ниже 2.0.9083.0 зависят от службы контроля доступа Azure (ACS). В 2018 г. [служба контроля доступа Azure (ACS) будет объявлена устаревшей](../articles/active-directory/develop/active-directory-acs-migration.md). Начиная с 19 марта 2018 г. использование любых версий агента MARS ниже 2.0.9083.0 будет приводить к сбою резервного копирования. Чтобы избежать сбоев резервного копирования или устранить их, [установите последнюю версию агента MARS](https://go.microsoft.com/fwlink/?linkid=229525). Для идентификации серверов, для которых требуется обновление агента MARS, следуйте инструкциям [по обновлению агентов Azure Backup из блога службы Backup](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). Агент MARS используется для резервного копирования в Azure следующих типов данных:
- Файлы 
- данные состояния системы;
- резервное копирование System Center DPM в Azure;
- Azure Backup Server (MABS)
