---
title: "Замена корпуса устройства StorSimple серии 8000 | Документация Майкрософт"
description: "Эта статья описывает снятие и установку основного корпуса или корпуса EBOD устройства StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 537659ed-4c46-49c1-b1e4-186262f2542d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 5295c5dd039b1d4746ebaaf90372932e4c3e7c26
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Замена корпуса устройства StorSimple
## <a name="overview"></a>Обзор
В этом учебнике объясняется, как снять и заменить корпус устройства StorSimple серии 8000. Модель StorSimple 8100 — устройство в отдельном корпусе (один корпус), тогда как модель 8600 состоит из двух корпусов (два корпуса). В модели 8600 неисправность может возникнуть в двух корпусах: в основном корпусе и в корпусе EBOD.

В каждом из этих случаев Майкрософт предоставляет на замену пустой корпус. В комплект поставки не входят блоки питания и охлаждения (БПО), модули контроллера, твердотельные дисковые накопители (SSD), жесткие диски (HDD) и модули EBOD.

> [!IMPORTANT]
> Перед снятием и заменой корпуса ознакомьтесь со сведениями о безопасности в разделе [Замена компонентов оборудования StorSimple](storsimple-hardware-component-replacement.md).
> 
> 

## <a name="remove-the-chassis"></a>Снятие корпуса
Выполните следующие действия для снятия корпуса устройства StorSimple.

#### <a name="to-remove-a-chassis"></a>Снятие корпуса
1. Убедитесь, что устройство StorSimple выключено и отключено от всех источников питания.
2. Отключите все сетевые кабели и кабели SAS, если они используются.
3. Извлеките устройство из стойки.
4. Извлеките каждый из дисков и запишите номера слотов, из которых они извлекаются. Дополнительные сведения см. в разделе [Снять жесткий диск](storsimple-disk-drive-replacement.md#remove-the-disk-drive).
5. Для корпуса EBOD (при неисправности корпуса) снимите модули контроллеров EBOD. Дополнительные сведения см. в разделе [Снятие контроллера EBOD](storsimple-ebod-controller-replacement.md#remove-an-ebod-controller). 
   
    Для основного корпуса (при неисправности корпуса) снимите модули контроллеров и запишите номера слотов, в которых они находились. Дополнительные сведения см. в разделе [Снятие контроллера](storsimple-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Установка корпуса
Чтобы установить корпус устройства StorSimple, выполните следующие действия.

#### <a name="to-install-a-chassis"></a>Установка корпуса
1. Установите корпус в стойку. Дополнительные сведения см. в разделах [Установка устройства StorSimple 8100 в стойку](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) или [Установка устройства StorSimple 8600 в стойку](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. После установки корпуса в стойку установите модули контроллера так же, как они были установлены ранее.
3. Установите диски в те же слоты, в которые они были установлены ранее.
   
   > [!NOTE]
   > Рекомендуется сначала устанавливать твердотельные жесткие диски, а затем жесткие диски.
   > 
   > 
4. После установки корпуса в стойку и установки компонентов подключите устройство к соответствующим источникам питания и включите устройство. Дополнительные сведения см. в разделах [Подключение кабельного хозяйства к устройству StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) или [Подключение кабельного хозяйства к устройству StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Дальнейшие действия
Узнайте подробнее о [замене компонентов оборудования StorSimple](storsimple-hardware-component-replacement.md).

