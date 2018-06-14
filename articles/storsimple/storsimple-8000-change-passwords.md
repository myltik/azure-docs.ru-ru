---
title: Изменение паролей StorSimple | Документация Майкрософт
description: В этой статье показано, как использовать службу диспетчера устройств StorSimple для изменения паролей Snapshot Manager и администратора устройств StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 7762f8499c67672f0a2ffed99e98baea4c940fa0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23108172"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>Изменение паролей StorSimple с помощью службы диспетчера устройств StorSimple

## <a name="overview"></a>Обзор
В разделе **Параметры устройства** на портале Azure указаны все параметры, которые можно настроить на устройстве StorSimple, контролируемом службой диспетчера устройств StorSimple. В этом руководстве показано, как использовать элемент **Безопасность** в разделе **Параметры устройства**, чтобы изменить в StorSimple пароль администратора устройства или пароль Snapshot Manager.

## <a name="change-the-device-administrator-password"></a>Изменение пароля администратора устройства
При использовании интерфейса Windows PowerShell для доступа к устройству StorSimple вам потребуется ввести пароль администратора устройства. После регистрации первого устройства StorSimple в службе пароль по умолчанию для этого интерфейса — *Password1*. Для обеспечения безопасности ваших данных необходимо изменить этот пароль в конце регистрации. Закончить регистрацию, не изменив пароль, невозможно. Дополнительные сведения см. в разделе [Шаг 3. Настройка и регистрация устройства средствами Windows PowerShell для StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Пароль, который устанавливается через интерфейс Windows PowerShell во время регистрации, можно изменить позднее на портале Azure. Чтобы изменить пароль администратора устройства, выполните следующие действия.

#### <a name="to-change-the-device-administrator-password"></a>Изменение пароля администратора устройства
1. В службе диспетчера устройств StorSimple выберите **Устройства**.

2. В табличном списке устройств выберите и щелкните устройство, для которого требуется изменить пароль.

    ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. В колонке **Параметры** выберите **Параметры устройства > Безопасность**.

    ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. В колонке **Параметры безопасности** щелкните **Пароль**, чтобы изменить пароль администратора устройства.

    ![](./media/storsimple-8000-change-passwords/changepwd3.png)

5. В колонке **Пароль** введите пароль администратора длиной от 8 до 15 символов. Пароль должен содержать не менее 3 букв в верхнем и нижнем регистре, цифр и специальных символов.

6. Подтвердите пароль.

    ![](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Щелкните **Сохранить** и при появлении запроса на подтверждение щелкните **Да**.

    ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Теперь пароль администратора устройства изменен. Этот новый пароль можно использовать для доступа к интерфейсу Windows PowerShell.

## <a name="set-the-storsimple-snapshot-manager-password"></a>Настройка пароля Snapshot Manager для StorSimple
Программное обеспечение диспетчера моментальных снимков StorSimple находится на узле Windows и позволяет администраторам управлять созданием резервных копий для устройства StorSimple в виде локальных и облачных моментальных снимков.

При настройке устройства в диспетчере моментальных снимков StorSimple вас попросят указать IP-адрес и пароль устройства для проверки подлинности устройства для хранения данных.

Вы можете задать или изменить пароль Snapshot Manager для StorSimple через портал Azure. Чтобы задать или изменить пароль Snapshot Manager для StorSimple, выполните следующие действия.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>Настройка пароля Snapshot Manager для StorSimple
1. В службе диспетчера устройств StorSimple выберите **Устройства**.

2. В табличном списке устройств выберите и щелкните устройство StorSimple, для которого требуется изменить пароль Snapshot Manager.

     ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. В колонке **Параметры** выберите **Параметры устройства > Безопасность**.

     ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. В колонке **Параметры безопасности** щелкните **Пароль**, чтобы задать или изменить пароль Snapshot Manager для StorSimple.

     ![](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. В колонке **Пароль** введите пароль длиной 14 или 15 символов. Убедитесь в том, что пароль состоит не менее чем из 3 букв в верхнем и нижнем регистре, цифр и специальных символов.

6. Подтвердите пароль.

     ![](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Щелкните **Сохранить** и при появлении запроса на подтверждение щелкните **Да**.

     ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Теперь пароль диспетчера моментальных снимков StorSimple должен быть обновлен.

## <a name="next-steps"></a>Дополнительная информация
* Узнайте больше о [безопасности StorSimple](storsimple-8000-security.md).
* Узнайте больше об [изменении конфигурации устройства](storsimple-8000-modify-device-config.md).
* Узнайте больше об [использовании службы диспетчера устройств StorSimple для администрирования устройства StorSimple](storsimple-8000-manager-service-administration.md).

