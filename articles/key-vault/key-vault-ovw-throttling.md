---
title: Руководство по регулированию хранилища ключей Azure
description: Регулирование Key Vault позволяет ограничить число одновременных вызовов для предотвращения чрезмерного использования ресурсов.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: ''
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 05/10/2018
ms.author: alleonar
ms.openlocfilehash: 59968f2bccbe2828ebe5fb33c57ed28d4f8509b6
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
---
# <a name="azure-key-vault-throttling-guidance"></a>Руководство по регулированию хранилища ключей Azure

Регулирование — это процесс, позволяющий ограничить число одновременных вызовов к службе Azure для предотвращения чрезмерного использования ресурсов. Хранилище ключей Azure (AKV) может обрабатывать большое число запросов. Если это число превышает приемлемое, для обеспечения оптимальной производительности и надежности службы AKV используется регулирование клиентских запросов.

Ограничения регулирования зависят от сценария. Например, при выполнении большого объема операций записи возможность регулирования будет более высокой по сравнению с ситуацией, когда выполняются только операции чтения.

## <a name="how-does-key-vault-handle-its-limits"></a>Как хранилище ключей обрабатывает свои ограничения?

Для предотвращения некорректного использования ресурсов и обеспечения качества обслуживания для всех клиентов хранилища ключей в самом хранилище существуют ограничения служб. При превышении порогового значения службы хранилище ключей ограничивает все последующие запросы от этого клиента на определенный период времени. В этом случае хранилище ключей возвращает код состояния HTTP 429 (слишком много запросов), и запросы завершаются сбоем. Кроме того, хранилище ключей подсчитывает невыполненные запросы, возвращающих код состояния 429, и сравнивает их с ограничениями регулирования. 

Если у вас есть реальный пример использования с более высокими ограничениями регулирования, пожалуйста, расскажите нам о нем.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Регулирование приложения в ответ на ограничения служб

Ниже приведены **рекомендации** по регулированию приложения.
- Сократите число операций на один запрос.
- Уменьшите частоту запросов.
- Избегайте немедленных повторных попыток. 
    - Все запросы учитываются, и их количество сравнивается с ограничениями использования.

При реализации обработки ошибок для приложения используйте код ошибки HTTP 429, чтобы определить необходимость регулирования на стороне клиента. Если запрос снова завершается сбоем с кодом ошибки HTTP 429, это означает, что вы по-прежнему не соблюдаете ограничения службы Azure. Продолжайте использовать рекомендуемый метод регулирования на стороне клиента и повторяйте выполнять запрос, пока он не будет успешно выполнен.

### <a name="recommended-client-side-throttling-method"></a>Рекомендуемый метод регулирования на стороне клиента

При возврате кода ошибки HTTP 429 начните регулирование клиента с помощью метода экспоненциального откладывания:

1. Подождите 1 с и повторите запрос
2. Если запрос по-прежнему не выполнен, подождите 2 с и повторите запрос
3. Если запрос по-прежнему не выполнен, подождите 4 с и повторите запрос
4. Если запрос по-прежнему не выполнен, подождите 8 с и повторите запрос
5. Если запрос по-прежнему не выполнен, подождите 16 с и повторите запрос

На этом этапе вы не должны получать коды ответа HTTP 429.

## <a name="see-also"></a>См. также

Более подробные сведения о регулировании для Microsoft Cloud см. в разделе [Шаблон регулирования](https://docs.microsoft.com/azure/architecture/patterns/throttling).

