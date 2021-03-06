---
title: "Управление контейнерами томов StorSimple на устройстве StorSimple серии 8000 | Документация Майкрософт"
description: "В данной статье рассказывается, как на странице контейнеров томов службы диспетчера устройств StorSimple добавить, изменить или удалить контейнер томов."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/19/2017
ms.author: alkohli
ms.openlocfilehash: 0f8e00d6d07224f56625482f339e612e68914be2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>Использование службы диспетчера устройств StorSimple для управления контейнерами томов StorSimple

## <a name="overview"></a>Обзор
В данном учебнике рассказывается, как с помощью службы диспетчера устройств StorSimple создавать контейнеры томов StorSimple и управлять ими.

Контейнер томов в устройстве Microsoft Azure StorSimple содержит один или несколько томов, которые совместно используют учетную запись хранения, функцию шифрования и параметры потребления полосы пропускания. В устройстве может быть несколько контейнеров томов для всех его томов. 

У контейнера томов имеются указанные ниже атрибуты.

* **Тома** — многоуровневые или локально закрепленные тома StorSimple, содержащиеся в контейнере томов. 
* **Шифрование** — ключ шифрования, который можно задать для каждого контейнера томов. Этот ключ используется для шифрования данных, отправляемых с устройства StorSimple в облако. Для шифрования используется алгоритм AES-256 (оборонного класса), использующий вводимый пользователем ключ. Для защиты данных, размещаемых в облаке, рекомендуется всегда шифровать их.
* **Учетная запись хранения** — учетная запись хранения Azure, которая используется для хранения данных. Все тома, находящиеся в контейнере томов, совместно используют эту учетную запись хранения. При создании контейнера томов можно выбрать учетную запись хранения из существующего списка или создать новую, а затем указать учетные данные доступа для этой учетной записи.
* **Пропускная способность облака** — часть пропускной способности, потребляемая устройством при передаче данных из устройства в облако. При создании контейнера можно ограничить пропускную способность, указав значение от 1 до 1000 Мбит/с. Если необходимо, чтобы устройство потребляло всю доступную полосу пропускания, выберите для этого поля значение **Не ограничено**. Кроме того, можно создать и применить шаблон пропускной способности для распределения пропускной способности по расписанию.

Ниже рассказывается, как в колонке **Контейнеры томов** StorSimple выполнять указанные ниже стандартные операции.

* Добавление контейнера томов
* Изменение контейнера томов
* Удаление контейнера томов

## <a name="add-a-volume-container"></a>Добавление контейнера томов
Чтобы добавить контейнер томов, выполните указанные ниже действия.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Изменение контейнера томов
Чтобы изменить контейнер томов, выполните указанные ниже действия.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Удаление контейнера томов
Контейнер томов содержит тома. Чтобы удалить контейнер, необходимо сначала удалить из него все тома. Чтобы удалить контейнер томов, выполните указанные ниже действия.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Дополнительная информация
* Узнайте больше об [управлении томами StorSimple](storsimple-8000-manage-volumes-u2.md). 
* Узнайте больше об [использовании службы диспетчера устройств StorSimple для администрирования устройства StorSimple](storsimple-8000-manager-service-administration.md).

