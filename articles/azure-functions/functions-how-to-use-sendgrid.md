---
title: "Использование SendGrid в Функциях Azure | Документация Майкрософт"
description: "Здесь описывается, как использовать SendGrid в Функциях Azure"
services: functions
documentationcenter: na
author: rachelappel
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/31/2017
ms.author: rachelap
ms.translationtype: Human Translation
ms.sourcegitcommit: b95fcfa1ed4ea4cf1051f4920b9c05bb40a19cab
ms.openlocfilehash: 05c9f4e4a4351219da68af8b702c25f21d7d4d02
ms.contentlocale: ru-ru
ms.lasthandoff: 02/22/2017

---
# <a name="how-to-use-sendgrid-in-azure-functions"></a>Использование SendGrid в Функциях Azure

## <a name="sendgrid-overview"></a>Общие сведения о SendGrid

Функции Azure поддерживают выходные привязки SendGrid, позволяя вашим функциям отправлять сообщения электронной почты с помощью нескольких строк кода и учетной записи SendGrid.

Чтобы использовать API SendGrid в функции Azure, необходимо иметь [учетную запись SendGrid](http://SendGrid.com). А также необходимо иметь ключ API SendGrid. Чтобы создать ключ API, войдите в учетную запись SendGrid и выберите **Параметры**, а затем — **Ключ API**. Сохраните этот ключ, так как он потребуется на последующем шаге.

Теперь вы готовы создать приложение-функцию Azure.

## <a name="create-an-azure-function-app"></a>Создание приложения-функции Azure 

Приложения-функции Azure являются контейнерами для одной или нескольких функций Azure. Функции Azure являются именно функциями. Каждая функция Azure связана с одним триггером, который является событием, инициирующим запуск функции.
Каждая функция может содержать любое количество входных или выходных привязок. Привязки — это службы, которые можно использовать в функции. SendGrid — это выходная привязка, которую можно использовать для отправки электронной почты. 

1. Войдите на портал Azure и [создайте приложение-функцию Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) или откройте существующее приложение-функцию. 
2. Создайте функцию Azure. Для простоты выберите ручной триггер и C#. 

 ![Создание функции Azure](./media/functions-how-to-use-sendgrid/functions-new-function-manual-trigger-page.png)

## <a name="configure-sendgrid-for-use-in-an-azure-function-app"></a>Настройка SendGrid для использования в приложении-функции Azure

Чтобы использовать ключ API SendGrid в функции, необходимо сохранить его как параметр приложения. Поле ApiKey является не фактическим ключом API SendGrid, а определяемым вами параметром приложения, который представляет фактический ключ API. Такое хранение ключа способствует обеспечению безопасности, так как ключ отделен от кода и файлов, которые могут быть возвращены в систему управления версиями.

- Создайте ключ **AppSettings** в **параметрах** приложения-функции.

 ![Создание функции Azure](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-api-key-app-settings.png)

## <a name="configure-sendgrid-output-bindings"></a>Настройка выходных привязок SendGrid

Привязка SendGrid доступна как выходная привязка функции Azure. Чтобы создать выходную привязку SendGrid, выполните следующие действия:

1. Перейдите на вкладку **Интегрировать** функции на портале Azure.
2. Чтобы создать выходную привязку SendGrid, щелкните **Новое выходное значение**.
3. Заполните свойства **Ключ SendGrid API** и **Имя параметра сообщения**. При желании вы можете ввести сейчас и другие свойства или закодировать их. Приведенные здесь параметры можно использовать как значения по умолчанию.

 ![Настройка выходных привязок SendGrid](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-output-bindings.png)

При добавлении в функцию привязки в папке функции создается файл с именем **function.json**. Этот файл содержит все те же сведения, которые можно видеть на вкладке **Интегрировать** функции Azure, но в формате JSON. Если задать значения полей **Ключ SendGrid API**, **Имя параметра сообщения** и **Адрес отправителя**, то в файле **function.json** будут созданы следующие записи: 

```json
{
  "bindings": [    
    {
      "type": "sendGrid",
      "name": "message",
      "apiKey": "SendGridKey",
      "direction": "out",
      "from": "azure@contoso.com"
    }
  ],
  "disabled": false
}
```

При желании вы можете изменить этот файл самостоятельно.

Теперь, когда созданы и настроены приложение-функция и функция, можно написать код для отправки электронной почты.

## <a name="write-code-that-creates-and-sends-email"></a>Написание кода, который создает и отправляет электронную почту

API SendGrid содержит все команды, необходимые для создания и отправки электронной почты.  

- Замените код в функции следующим кодом:

```cs
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static void Run(TraceWriter log, string input, out Mail message)
{
    message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    // change to email of recipient
    personalization.AddTo(new Email("MoreEmailPlease@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

Обратите внимание, что в первой строке содержится директива ```#r```, которая ссылается на сборку SendGrid. Затем можно использовать инструкцию ```using```, чтобы упростить доступ к объектам в этом пространстве имен. Из интерфейса API SendGrid создайте в коде экземпляры объектов ```Mail```, ```Personalization``` и ```Content```, из которых состоит сообщение электронной почты. Когда вы возвращаете сообщение, SendGrid доставляет его. 

Подпись функции также содержит дополнительный выходной параметр типа ```Mail``` с именем ```message```. Как входные, так и выходные привязки выражаются в коде как параметры функции. 

2. Протестируйте код, щелкнув **Тест** и введя сообщение в поле **Текст запроса**. Затем нажмите кнопку **Запуск**.

 ![Тестирование кода](./media/functions-how-to-use-sendgrid/functions-develop-test-sendgrid.png)

3. Проверьте электронную почту, чтобы убедиться в отправке сообщения SendGrid. Оно должно быть доставлено на адрес, указанный в коде на шаге 1, и содержать сообщение, введенное в поле **Текст запроса**.

## <a name="next-steps"></a>Дальнейшие действия
В этой статье показано, как использовать службу SendGrid для создания и отправки электронной почты. Дополнительные сведения об использовании Функций Azure в приложениях см. в следующих статьях: 

- [Рекомендации по Функциям Azure](functions-best-practices.md). Некоторые рекомендации по созданию функций Azure.

- [Руководство для разработчиков по Функциям Azure](functions-reference.md). Справочник программиста по созданию функций, а также определению триггеров и привязок.

- [Тестирование функций Azure](functions-test-a-function.md). Описание различных средств и методов тестирования функций.
