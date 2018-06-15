---
title: Масштабирование конечных точек потоковой передачи с помощью портала Azure | Документация Майкрософт
description: В этом учебнике пошагово описано, как масштабировать конечные точки потоковой передачи с помощью портала Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: juliako
ms.openlocfilehash: f2a14f2622d78a4222a8518172eb1ce8ed9e6637
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33785643"
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Масштабирование конечных точек потоковой передачи с помощью портала Azure
## <a name="overview"></a>Обзор

> [!NOTE]
> Для работы с этим учебником требуется учетная запись Azure. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Конечные точки уровня **Премиум** подходят для выполнения более сложных задач благодаря выделенной пропускной способности и возможности масштабирования. Клиенты, имеющие конечную точку потоковой передачи уровня **Премиум**, по умолчанию получают одну единицу потоковой передачи. Конечную точку потоковой передачи можно масштабировать, добавляя единицы потоковой передачи. Каждая единица потоковой передачи предоставляет дополнительную пропускную способность для приложения. Дополнительные сведения о типах конечных точек потоковой передачи и конфигурации сети CDN см. в статье [Управление конечными точками потоковой передачи с помощью портала Azure](media-services-streaming-endpoints-overview.md).
 
В этом разделе показано, как масштабировать конечную точку потоковой передачи.

Дополнительные сведения о ценах на службы мультимедиа см. [здесь](http://go.microsoft.com/fwlink/?LinkId=275107).

## <a name="scale-streaming-endpoints"></a>Масштабирование конечных точек потоковой передачи

Чтобы изменить число единиц потоковой передачи, сделайте следующее.

1. Выберите учетную запись служб мультимедиа Azure на [портале Azure](https://portal.azure.com/).
2. В окне **Параметры** щелкните**Конечные точки потоковой передачи**.
3. Щелкните конечную точку потоковой передачи, которую требуется масштабировать. 

    > [!NOTE] 
    > Масштабировать можно только конечные точки потоковой передачи уровня **Премиум**.

4. Переместите ползунок, чтобы указать нужное число единиц потоковой передачи.

    ![конечная точка потоковой передачи](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>Дополнительная информация
Просмотрите схемы обучения работе со службами мультимедиа.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

