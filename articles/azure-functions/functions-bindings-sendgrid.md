---
title: "Привязки SendGrid для Функций Azure | Документация Майкрософт"
description: "Справочник по привязкам SendGrid для Функций Azure."
services: functions
documentationcenter: na
author: rachelappel
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: rachelap
ms.openlocfilehash: 4cdafbe05e29d8b483c6b0e1daf41a36583d7b5e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-sendgrid-bindings"></a>Привязки SendGrid для Функций Azure

В этой статье объясняется, как настроить и использовать привязки SendGrid в Функциях Azure. С помощью SendGrid можно использовать Функции Azure для программной отправки настраиваемой электронной почты.

Эта статья содержит справочные сведения для разработчиков Функций Azure. Если вы новичок в функциях Azure, начните со следующих ресурсов:

[Создание первой функции Azure](functions-create-first-azure-function.md). 
Справочники разработчика по [C#](functions-reference-csharp.md), [F#](functions-reference-fsharp.md) или [Node](functions-reference-node.md).

## <a name="functionjson-for-sendgrid-bindings"></a>Файл function.json для привязок SendGrid

Функции Azure предоставляют привязку для вывода для SendGrid. Привязка для вывода SendGrid дает возможность программно создавать и отправлять электронные сообщения. 

Привязка SendGrid поддерживает перечисленные ниже свойства.

|Свойство  |Описание  |
|---------|---------|
|**name**| Обязательное. Имя переменной, из которой в коде функции можно получить запрос или текст запроса. Это значение равно ```$return``` при наличии только одного возвращаемого значения. |
|**type**| Обязательное. Необходимо задать значение `sendGrid`.|
|**direction**| Обязательное. Необходимо задать значение `out`.|
|**apiKey**| Обязательное. Необходимо задать имя ключа API, сохраненного в параметрах приложения-функции. |
|**to**| Электронный адрес получателя. |
|**from**| Электронный адрес отправителя. |
|**subject**| Тема электронного сообщения. |
|**text**| Содержимое электронного сообщения. |

Ниже приведен пример файла **function.json**.

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

> [!NOTE]
> Функции Azure хранят сведения о подключении и ключи API в качестве параметров приложения, чтобы они не возвращались в репозиторий системы управления версиями. Это обеспечивает защиту конфиденциальной информации.
>
>

## <a name="c-example-of-the-sendgrid-output-binding"></a>Пример привязки для вывода SendGrid на C#

```csharp
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static Mail Run(TraceWriter log, string input, out Mail message)
{
     message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    personalization.AddTo(new Email("recipient@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

## <a name="node-example-of-the-sendgrid-output-binding"></a>Пример привязки для вывода SendGrid на Node

```javascript
module.exports = function (context, input) {    
    var message = {
         "personalizations": [ { "to": [ { "email": "sample@sample.com" } ] } ],
        from: { email: "sender@contoso.com" },        
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};

```

## <a name="next-steps"></a>Дальнейшие действия
Сведения о других привязках и триггерах для Функций Azure доступны в следующих разделах: 
- [Справочник разработчика по триггерам и привязкам в Функциях Azure](functions-triggers-bindings.md)

- [Рекомендации по Функциям Azure](functions-best-practices.md). Некоторые рекомендации по созданию функций Azure.

- [Руководство для разработчиков по Функциям Azure](functions-reference.md). Справочник программиста по созданию функций, а также определению триггеров и привязок.
