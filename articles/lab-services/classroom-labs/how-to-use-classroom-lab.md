---
title: Как войти в лабораторию для аудитории в Службах лабораторий Azure | Документация Майкрософт
description: Из этого руководства вы узнаете, как войти на виртуальные машины в лаборатории для аудитории, которую настроил для вас преподаватель.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 85da6989740446c980bcb9f2c89a6c31afe488fb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34651563"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Как войти в лабораторию для аудитории в Службах лабораторий Azure
В этой статье объясняется, как получить доступ к лаборатории для аудитории, подключиться к виртуальной машине в этой лаборатории и остановить работу виртуальной машины. 

## <a name="view-all-the-classroom-labs"></a>Просмотр всех лабораторий для аудитории

1. Перейдите по **URL-адресу регистрации**, который вам предоставил преподаватель или методист. 
2. Войдите в службу, используя учебную учетную запись, чтобы завершить процесс регистрации. 
3. После регистрации убедитесь, что вы видите виртуальные машины всех лабораторий, к которым у вас должен быть доступ. 

    ![Просмотр всех лабораторий](../media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>Подключение к виртуальной машине в лаборатории для аудитории

1. Выберите **Connect** (Подключиться) на плитке, которая представляет виртуальную машину в нужной лаборатории.

    ![Просмотр всех лабораторий](../media/how-to-use-classroom-lab/connect-button.png)
2. Подготовка виртуальной машины занимает несколько минут.

    ![Подготовка виртуальной машины](../media/how-to-use-classroom-lab/getting-virtual-machine-ready.png)
3. Сохраните RDP-файл на жесткий диск и откройте его. 
    
    ![Сохранение RDP-файла](../media/how-to-use-classroom-lab/save-rdp-file.png)
4. Чтобы войти на виртуальную машину, введите **имя пользователя** и **пароль**, которые вам предоставил преподаватель или методист. 

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>Остановка работы виртуальной машины в лаборатории для аудитории

Выберите **Stop** (Остановить) на плитке, которая представляет лабораторию для аудитории. Когда виртуальная машина будет остановлена, на этой плитке снова станет доступна кнопка **Start** (Запустить). 

## <a name="next-steps"></a>Дополнительная информация
Начальная информация о настройке лаборатории в Службах лабораторий Azure:

- [Руководство по настройке лаборатории для аудитории](how-to-manage-classroom-labs.md)
- [Настройка лаборатории](../tutorial-create-custom-lab.md)

 