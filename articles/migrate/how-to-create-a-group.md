---
title: "Группирование компьютеров для оценки с помощью службы \"Миграция Azure\" | Документация Майкрософт"
description: "В этой статье описывается, как группировать компьютеры, прежде чем выполнять оценку с помощью службы \"Миграция Azure\"."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5c279804-aa30-4946-a222-6b77c7aac508
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: 966e0aebf56ab049e898d1787bfdfbb2ef23635e
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2017
---
# <a name="group-machines-for-assessment"></a>Группирование компьютеров для оценки

В этой статье описывается, как создать группу компьютеров для оценки с помощью службы [Миграция Azure](migrate-overview.md). Служба "Миграция Azure" оценивает компьютеры в группе, чтобы проверить, подходят ли они для миграции в Azure, и предоставляет оценку размера и затрат, необходимых для выполнения компьютера в Azure.


## <a name="create-a-group"></a>Создание группы

1. На **панели мониторинга** проекта службы "Миграция Azure" щелкните  **Группы** > **+Group** (+ Группа) и укажите имя группы.
2. Добавьте один или несколько компьютеров в группу, а затем нажмите кнопку  **Создать**. 
3. При необходимости для группы можно выбрать выполнение новой оценки. 

    ![Создание группы](./media/how-to-create-a-group/create-group.png)

После создания группы ее можно изменить, выбрав группу на странице **Группы**, а затем добавив или удалив компьютеры.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как использовать [сопоставление зависимостей компьютеров](how-to-create-group-machine-dependencies.md), чтобы создать более точные группы.
- [Узнайте больше](concepts-assessment-calculation.md) о вычислении оценок.
