---
title: "Операции Synchronization Service Manager Azure AD Connect | Документация Майкрософт"
description: "Общие сведения о вкладке \"Operations\" (Операции) в Synchronization Service Manager для Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 7f50ce0d4842bde809664c392ebee5425a70e6f0
ms.contentlocale: ru-ru
ms.lasthandoff: 03/08/2017

---
# Использование вкладки "Operations" (Операции) в Synchronization Service Manager
<a id="using-the-sync-service-manager-operations-tab" class="xliff"></a>

![Диспетчер службы синхронизации](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

На вкладке "Operations" (Операции) отображаются результаты последних операций. Эта вкладка крайне важна для понимания и устранения неполадок.

## Пояснение информации, отображаемой на вкладке "Operations" (Операции)
<a id="understand-the-information-visible-in-the-operations-tab" class="xliff"></a>
В верхней части отображены все операции выполнения в хронологическом порядке. По умолчанию журнал операций содержит сведения за последние семь дней, но эту цифру можно изменить с помощью [планировщика](active-directory-aadconnectsync-feature-scheduler.md). Вы можете найти любую операцию выполнения, которая не была завершена успешно. Вы также можете изменить порядок сортировки, щелкая заголовки.

Столбец **Status** (Состояние) является самым важным, он показывает наиболее серьезную проблему при выполнении. Ниже приводится краткая сводка наиболее распространенных состояний в порядке приоритета для анализа (где * означает, что возможно несколько строк ошибки).

| Status | Комментарий |
| --- | --- |
| stopped-* |Не удалось завершить выполнение. Например, если удаленная система не работает и с ней не удается связаться. |
| stopped-error-limit |Обнаружено более 5000 ошибок. Выполнение было автоматически остановлено из-за большого количества ошибок. |
| completed-\*-errors |Выполнение завершено, но есть ошибки (меньше 5000), которые необходимо изучить. |
| completed-\*-warnings |Запуск завершен, но некоторые данные не находятся в ожидаемом состоянии. Если обнаружены ошибки, такое сообщение обычно является указателем. Пока вы не исправили ошибки, не следует рассматривать предупреждения. |
| Успешное завершение |Проблемы отсутствуют. |

При выборе строки в нижней части отображаются сведения об этой операции выполнения. Слева внизу может отображаться список **Step #**(Шаг №). Он отображается, только если у вас в лесу несколько доменов, где каждый домен представлен шагом. Имя домена можно найти в разделе **Partition**(Секция). В разделе **Synchronization Statistics**(Статистика синхронизации) можно найти дополнительные сведения о числе обработанных изменений. Щелкая ссылки, можно получить список измененных объектов. Если у вас есть объекты с ошибкой, они отображаются в разделе **Synchronization Errors**(Ошибки синхронизации).

Дополнительные сведения см. в разделе [Устранение неполадок синхронизации объекта с Azure AD](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md).

## Дальнейшие действия
<a id="next-steps" class="xliff"></a>
Узнайте больше о настройке [службы синхронизации Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).

