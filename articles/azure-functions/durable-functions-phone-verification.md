---
title: Участие пользователя и время ожидания в устойчивых функциях — Azure
description: Сведения о том, как обрабатывать участие пользователей и время ожидания в расширении устойчивых функций для Функций Azure.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 071a9ffb8305a30b0fedeaa49c4a95d91fbce6c1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30168407"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Участие пользователя в устойчивых функциях. Пример проверки номера телефона

В этом примере показано, как создать оркестрацию [устойчивых функций](durable-functions-overview.md), которая включает в себя участие пользователя. Каждый раз, когда человек участвует в автоматизированном процессе, процесс должен отправлять уведомления пользователю и получать ответы асинхронно. Он также должен допускать возможность, что пользователь недоступен. (Именно в этой части время ожидания становится важным.)

В этом примере реализуется система проверки номера телефона на основе SMS. Эти типы потоков часто используются при проверке номера телефона клиента или для многофакторной проверки подлинности. Это очень эффективный пример, так как полная реализация выполняется с помощью нескольких небольших функций. Внешнее хранилище данных, например база данных, не требуется.

## <a name="prerequisites"></a>предварительным требованиям

* [Установите устойчивые функции](durable-functions-install.md).
* Ознакомьтесь с пошаговым руководством по примеру [последовательности Hello](durable-functions-sequence.md).

## <a name="scenario-overview"></a>Обзор сценария

Проверка номера телефона используется для подтверждения того, что пользователи вашего приложения не рассылают нежелательную почту и являются теми, за кого себя выдают. Многофакторная проверка подлинности часто применяется для защиты учетных записей пользователей от злоумышленников. Проблема реализации собственной проверки номера телефона состоит в том, что требуется взаимодействие с пользователем с **отслеживанием состояния**. Пользователю обычно предоставляется определенный код (например, 4-значное число), на который он должен отреагировать **в течение приемлемого промежутка времени**.

Обычно в решении "Функции Azure" не отслеживается состояние (как и в других облачных конечных точках на других платформах), поэтому эти типы взаимодействия требуют явного управления состоянием во внешней базе данных или другом постоянном хранилище. Кроме того, взаимодействие должно быть разбито на несколько функций, которые можно согласовать вместе. Например, необходима хотя бы одна функция для выбора кода, его сохранения и отправки на телефон пользователя. Кроме того, вам также требуется другая функция, чтобы получить ответ от пользователя и каким-то образом обратно сопоставить его с исходным вызовом функции, чтобы выполнить проверку кода. Время ожидания также является важным аспектом обеспечения безопасности. Ситуация может очень быстро усложниться.

Сложность этого сценария значительно снижается, когда вы используете устойчивые функции. Как будет показано в этом примере, с помощью функции оркестратора можно без проблем управлять взаимодействием с отслеживанием состояния без использования любых внешних хранилищ данных. Так как функции оркестратора *устойчивые*, эти интерактивные потоки также очень надежные.

## <a name="configuring-twilio-integration"></a>Настройка интеграции Twilio

[!INCLUDE [functions-twilio-integration](../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>Функции

В этой статье описаны следующие функции, используемые в примере приложения:

* **E4_SmsPhoneVerification**
* **E4_SendSmsChallenge**

В следующих разделах рассматривается конфигурация и код, которые используются для написания скриптов на языке C#. Код для разработки с помощью Visual Studio представлен в конце этой статьи.
 
## <a name="the-sms-verification-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Оркестрация проверки с помощью SMS (пример кода Visual Studio Code и портала Azure) 

Функция **E4_SmsPhoneVerification** использует стандартный файл *function.json* для функций оркестратора.

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/function.json)]

Ниже приведен код, реализующий функцию.

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/run.csx)]

После запуска функция оркестратора выполняет следующие задачи:

1. Получает номер телефона, на который она будет *отправлять* SMS-уведомление.
2. Вызывает **E4_SendSmsChallenge** для отправки SMS-сообщения пользователю и возвращает обратно ожидаемый 4-значный код запроса.
3. Создает устойчивый таймер, который отсчитывает 90 секунд от текущего времени.
4. Пока выполняется таймер, ожидает событие **SmsChallengeResponse** от пользователя.

Пользователи получают SMS-сообщение с 4-значным кодом. У них есть 90 секунд, чтобы отправить этот код обратно в экземпляр функции оркестратора для завершения проверки. Если пользователи отправляют неверный код, у них есть еще три попытки (в пределах тех же 90 секунд).

> [!NOTE]
> Сперва это может показаться неочевидным, но эта функция оркестратора полностью детерминированная. Это связано с тем, что свойство `CurrentUtcDateTime` используется для подсчета времени истечения срока, настроенного для таймера. Это свойство возвращает то же значение при каждом воспроизведении в коде оркестратора. Важно, чтобы в результате каждого повторяющегося вызова `Task.WhenAny` был получен один и тот же `winner`.

> [!WARNING]
> Если таймеры вам больше не нужны, как показано в примере выше, когда принимается ответ на запрос, [отключите их с помощью CancellationTokenSource](durable-functions-timers.md).

## <a name="send-the-sms-message"></a>Отправка SMS-сообщения

Функция **E4_SendSmsChallenge** использует привязку Twilio для отправки SMS-сообщения с 4-значным кодом пользователю. Файл *function.json* выглядит следующим образом:

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/function.json)]

Ниже приведен код, который создает 4-значный код запроса и отправляет SMS-сообщение:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/run.csx)]

Функция **E4_SendSmsChallenge** вызывается только один раз, даже когда процесс завершается сбоем или воспроизводится повторно. Это удобно, так как пользователь не должен получать несколько SMS-сообщений. Возвращаемое значение `challengeCode` автоматически сохраняется, поэтому функция оркестратора всегда знает правильный код.

## <a name="run-the-sample"></a>Запуск примера

С помощью функций, активируемых по HTTP (перечисленных в примере), можно запустить оркестрацию, отправив следующий запрос HTTP POST:

```
POST http://{host}/orchestrators/E4_SmsPhoneVerification
Content-Length: 14
Content-Type: application/json

"+1425XXXXXXX"
```
```
HTTP/1.1 202 Accepted
Content-Length: 695
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

Функция оркестратора получает указанный номер телефона и немедленно отправляет на него SMS-сообщение со случайно сгенерированным 4-значным кодом проверки, например *2168*. Функция ожидает ответ в течение 90 секунд.

Чтобы отправить код, вы можете использовать `RaiseEventAsync` внутри другой функции или вызвать веб-перехватчик HTTP POST **sendEventUrl**, указанный в ответе 202 выше, заменив `{eventName}` именем события `SmsChallengeResponse`:

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Если вы отправите это до истечения времени ожидания, оркестрация будет завершена, а для поля `output` будет задано значение `true`, указывающее, что проверка прошла успешно.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```
```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Если время ожидания истекло или вы ввели неправильный код 4 раза, можно запросить состояние и просмотреть выходные данные функции оркестрации `false`, где указано, что проверка номера телефона завершилась сбоем.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="visual-studio-sample-code"></a>Пример кода для Visual Studio

Пример описанной оркестрации, реализованной в одном файле C# в проекте Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs)]

## <a name="next-steps"></a>Дополнительная информация

В этом примере показаны некоторые расширенные возможности устойчивых функций, в частности `WaitForExternalEvent` и `CreateTimer`. Вы узнали, как их можно использовать совместно с `Task.WaitAny`, чтобы реализовать надежную систему времени ожидания, которая часто полезна для взаимодействия с реальными людьми. Дополнительные сведения об использовании устойчивых функций см. в серии статей, где подробно рассматриваются определенные темы.

> [!div class="nextstepaction"]
> [Перейти к первой статье из серии](durable-functions-bindings.md)
