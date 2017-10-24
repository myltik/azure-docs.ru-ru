---
title: "Анализ вариантов использования Azure CDN | Документация Майкрософт"
description: "Клиент может включить анализ журналов для Azure CDN."
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: v-semcev
ms.openlocfilehash: af396e9f8847421d529c32956216cfc47294edb2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Анализ вариантов использования CDN Azure

Когда вы включите CDN для вашего приложения, вам, скорее всего, нужно будет отслеживать использование CDN, проверять работоспособность службы доставки и устранять возможные неполадки. Azure CDN позволяет делать это следующим образом: 

## <a name="verizon-core-reports"></a>С помощью базовых отчетов Verizon

Как пользователь с Azure CDN с профилем Verizon "Стандартный" или "Премиум" вы можете просматривать базовые отчеты Verizon на вспомогательном портале Verizon. На портале Verizon доступно множество графиков и представлений. Перейти на него можно с помощью параметра **Управление** на портале Azure. См. дополнительные сведения о [базовых отчетах Verizon](cdn-analyze-usage-patterns.md).

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Базовая аналитика с использованием журналов диагностики Azure

Базовая аналитика доступна для всех конечных точек CDN, относящихся к профилям CDN Verizon (уровни "Стандартный" и "Премиум") и Akamai (уровень "Стандартный"). Журналы диагностики Azure разрешают экспортировать базовую аналитику в хранилище Azure, концентраторы событий или службу Log Analytics в Operations Management Suite (OMS). Log Analytics OMS предлагает решение с настраиваемыми диаграммами. См. дополнительные сведения о [журналах диагностики Azure](cdn-azure-diagnostic-logs.md).

