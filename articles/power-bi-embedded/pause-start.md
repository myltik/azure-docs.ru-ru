---
title: "Приостановка и запуск емкости Power BI Embedded на портале Azure | Документация Майкрософт"
description: "В этой статье содержится пошаговое руководство по приостановке и запуску емкости Power BI Embedded в Microsoft Azure."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: ab5d184fde2cbcb517b325624c8405954b4d0972
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="pause-and-start-your-power-bi-embedded-capacity-in-the-azure-portal"></a>Приостановка и запуск емкости Power BI Embedded на портале Azure

В этой статье содержится пошаговое руководство по приостановке и запуску емкости Power BI Embedded в Microsoft Azure. При этом подразумевается, что емкость Power BI Embedded уже создана. Если это не так, чтобы приступить к работе, ознакомьтесь с разделом [Создание емкости Power BI Embedded на портале Azure](create-capacity.md).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="pause-your-capacity"></a>Приостановка емкости

Приостановка емкости позволяет предотвратить выставление счетов за ее использование. Это очень удобно в том случае, если в течение определенного периода времени необходимость в ресурсах отсутствует. Чтобы приостановить емкость, выполните следующие действия.

> [!NOTE]
> Приостановка емкости может привести к тому, что содержимое станет недоступным в Power BI. Обязательно отмените назначение рабочих областей в емкость перед ее приостановкой во избежание прерывания их работы.

1. Войдите на [портал Azure](https://portal.azure.com/).

2. Выберите **Больше служб** > **Power BI Embedded**, чтобы просмотреть имеющиеся емкости.

    ![Раздел "Больше служб" на портале Azure](media/pause-start/azure-portal-more-services.png)

3. Выберите емкость, которую нужно приостановить.

    ![Список емкостей Power BI Embedded на портале Azure](media/pause-start/azure-portal-capacity-list.png)

4. Щелкните **Приостановить** в разделе сведений о емкости.

    ![Приостановка емкости](media/pause-start/azure-portal-pause-capacity.png)

5. Щелкните **Да**, чтобы подтвердить приостановку емкости.

    ![Подтверждение приостановки](media/pause-start/azure-portal-confirm-pause.png)

## <a name="start-your-capacity"></a>Запуск емкости

Возобновите использование емкости, запустив ее. При запуске емкость также возобновляется выставления счетов.

1. Войдите на [портал Azure](https://portal.azure.com/).

2. Выберите **Больше служб** > **Power BI Embedded**, чтобы просмотреть имеющиеся емкости.

    ![Раздел "Больше служб" на портале Azure](media/pause-start/azure-portal-more-services.png)

3. Выберите емкость, которую нужно запустить.

    ![Список емкостей Power BI Embedded на портале Azure](media/pause-start/azure-portal-capacity-list.png)

4. Щелкните **Запустить** в разделе сведений о емкости.

    ![Запуск емкости](media/pause-start/azure-portal-start-capacity.png)

5. Щелкните **Да**, чтобы подтвердить запуск емкости.

    ![Подтверждение запуска](media/pause-start/azure-portal-confirm-start.png)

Если этой емкости назначено какое-либо содержимое, оно станет доступно после ее запуска.

## <a name="next-steps"></a>Дальнейшие действия

Если необходимо масштабировать емкость, просчитайте раздел [Масштабирование емкости Power BI Embedded](scale-capacity.md).

Чтобы начать внедрение содержимого Power BI в приложение, прочитайте раздел [Внедрение панелей мониторинга, отчетов и плиток Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

У вас имеются и другие вопросы? [Попробуйте задать их в сообществе Power BI](http://community.powerbi.com/)