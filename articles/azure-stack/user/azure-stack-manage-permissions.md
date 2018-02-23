---
title: "Управление доступом к ресурсам каждого пользователя в Azure Stack | Документация Майкрософт"
description: "Узнайте, как администратор служб или клиент может управлять доступом на основе ролей."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: dfec5648ff383fd98965c1918cdab6472bb3c17f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/22/2018
---
# <a name="manage-role-based-access-control"></a>Управление доступом на основе ролей

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Пользователь в Azure Stack может быть читателем, владельцем или участником каждого экземпляра подписки, группы ресурсов или службы. Например, пользователь A может иметь разрешения на чтение подписки 1, но иметь права владельца виртуальной машины 7.

* Читатель: пользователь может все просматривать, но не может вносить изменения.
* Участник: пользователь может управлять всем, кроме доступа к ресурсам.
* Владелец: пользователь может управлять всем, включая доступ к ресурсам.

## <a name="set-access-permissions-for-a-user"></a>Настройка прав доступа для пользователя
1. Выполните вход с помощью с учетной записью с разрешениями владельца ресурса, которым вы хотите управлять.
2. В колонке ресурса щелкните значок **Доступ** ![](media/azure-stack-manage-permissions/image1.png).
3. В колонке **Пользователи** щелкните **Роли**.
4. В колонке **Роли** выберите **Добавить**, чтобы добавить разрешения для пользователя.

## <a name="next-steps"></a>Дополнительная информация


