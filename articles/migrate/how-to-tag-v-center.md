---
title: "Группирование виртуальных машин с помощью тегов VMware vCenter | Документация Майкрософт"
description: "В этой статье описывается, как создать группу, прежде чем выполнять оценку с помощью службы \"Миграция Azure\"."
services: migration-planner
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5c279804-aa30-4946-a222-6b77c7aac508
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: db33e31cdef143aa70809442457e447446a1681a
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2017
---
# <a name="group-vms-with-vcenter-tagging"></a>Группирование виртуальных машин с помощью тегов vCenter

В этой статье описывается, как создать группу компьютеров для оценки с помощью службы [Миграция Azure](migrate-overview.md) путем добавления тегов в VMware vCenter. Требуемая категория тега указывается при создании группы. 

## <a name="set-up-tagging"></a>Настройка тегов

Во время развертывания службы "Миграция Azure" локальная виртуальная машина службы "Миграция Azure" обнаруживает компьютеры, работающие на узлах ESXi под управлением сервера vCenter Server. Необходимо настроить теги vCenter до начала процесса обнаружения.

1. В веб-клиенте VMware vSphere перейдите к серверу vCenter Server.
2. Щелкните **Теги**, чтобы просмотреть все текущие теги.
3. Чтобы добавить тег к виртуальной машине, выберите **Связанные объекты** > **Виртуальные машины**, затем выберите виртуальную машину для добавления тега.
4. Выберите **Сводка** > **Теги** и щелкните **Назначить**. 
5. Щелкните **Новый тег** и укажите имя и описание тега.
6. Чтобы создать категорию тега, выберите **Новая категория** из раскрывающегося списка.
7. Укажите имя, описание и кратность категории. Нажмите кнопку **ОК**.

    ![Теги виртуальной машины](./media/how-to-tag-v-center/vm-tag.png)

## <a name="use-tagging-to-create-groups"></a>Использование тегов для создания групп

1. Настройте обнаружение локальных компьютеров, как описано в [руководстве по оценке виртуальных машин VMware](tutorial-assessment-vmware.md#run-the-collector-to-discover-vms).
2. Из раскрывающегося списка **Tag category for grouping** (Категория тега для группирования) выберите категорию тега vCenter, на котором будет основана группа оценки. Служба "Миграция Azure" автоматически создает группу для выбранной категории.

    

## <a name="next-steps"></a>Дальнейшие действия

[Настройка оценки виртуальных машин VMware](tutorial-assessment-vmware.md).
