---
title: Как использовать API Engagement в iOS
description: Пакет SDK для последней версии iOS - Как использовать API Engagement в iOS
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 1fb4509e-3804-46c1-949f-1cf727f91f9f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 189a3029449a3161da2a20f940b77a5bb63bd1ef
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-use-the-engagement-api-on-ios"></a>Как использовать API Engagement в iOS
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

Этот документ представляет собой дополнение к документу «Как интегрировать Mobile Engagement в iOS». В нем предоставлена подробная информация о том, как использовать API Engagement для сообщения статистики приложения.

Учтите, что если вам требуется, чтобы служба Engagement сообщала только о сеансах, действиях, сбоях и технической информации приложения, проще всего сделать так, чтобы все пользовательские объекты `UIViewController` наследовались из соответствующего класса `EngagementViewController`.

Если вы хотите, чтобы служба делала что-то еще, например сообщала о событиях, ошибках и заданиях приложения или сообщала о действиях приложения способом, отличным от реализованного в классах `EngagementViewController`, необходимо использовать API Engagement.

API Engagement предоставляется в классе `EngagementAgent` . Экземпляр этого класса можно получить, вызвав статический метод `[EngagementAgent shared]` (обратите внимание на то, объект `EngagementAgent` возвращается в виде singleton).

Перед выполнением вызовов API необходимо инициализировать объект `EngagementAgent`, вызвав метод `[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];`.

## <a name="engagement-concepts"></a>Основные понятия Engagement
В следующих подразделах дано более подробное объяснение [общих понятий Служб мобильного взаимодействия](mobile-engagement-concepts.md) для платформы iOS.

### <a name="session-and-activity"></a>`Session` и `Activity`
*Действие*, как правило, связано с одним экраном приложения, т. е. *действие* начинается при отображении экрана и завершается при его закрытии. Именно в таком случае пакет SDK для Engagement интегрируется с помощью класса `EngagementViewController`.

*Действиями* можно также управлять вручную с помощью API Engagement. Это позволяет разделить экран на несколько частей, чтобы получить дополнительную информацию об использовании этого экрана (например, информацию о частоте и продолжительности использования диалоговых окон на этом экране).

## <a name="reporting-activities"></a>Уведомление о действиях
### <a name="user-starts-a-new-activity"></a>Запуск нового действия пользователем
            [[EngagementAgent shared] startActivity:@"MyUserActivity" extras:nil];

При каждом изменении пользовательского действия необходимо вызывать `startActivity()` . При первом вызове этой функции начинается новый сеанс пользователя.

### <a name="user-ends-his-current-activity"></a>Завершение текущего действия пользователем
            [[EngagementAgent shared] endActivity];

> [!WARNING]
> **Никогда** не вызывайте эту функцию самостоятельно, если только вам не нужно разделить одно использование приложения на несколько сеансов. При вызове этой функции текущий сеанс будет немедленно завершен, поэтому при последующем вызове `startActivity()` будет начат новый сеанс. Пакет SDK вызывает эту функцию автоматически при закрытии приложения.
> 
> 

## <a name="reporting-events"></a>Уведомление о событиях
### <a name="session-events"></a>События сеанса
События сеанса обычно используются для уведомления о действиях, выполняемых пользователем во время сеанса.

**Пример без дополнительных данных:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:nil];
            ...
       }
       [...]
    }

**Пример с дополнительными данными:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        NSMutableDictionary* extras = [NSMutableDictionary dictionary];
        [extras setObject:[NSNumber numberWithInt:toInterfaceOrientation] forKey:@"to_orientation_id"];
        [extras setObject:[NSNumber numberWithDouble:duration] forKey:@"duration"];
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:extras];
            ...
       }
       [...]
    }

### <a name="standalone-events"></a>Изолированные события
В отличие от событий сеанса изолированные события можно использовать вне контекста сеанса.

**Пример.**

    [[EngagementAgent shared] sendEvent:@"received_notification" extras:nil];

## <a name="reporting-errors"></a>Уведомление об ошибках
### <a name="session-errors"></a>Ошибки сеанса
Ошибки сеанса обычно используются для уведомления об ошибках, влияющих на пользователя во время сеанса.

**Пример**

    /** The user has entered invalid data in a form */
    @implementation MyViewController {
      [...]
      -(void)onMyFormSubmitted:(MyForm*)form {
        [...]
        /* The user has entered an invalid email address */
        [[EngagementAgent shared] sendSessionError:@"sign_up_email" extras:nil]
        [...]
      }
      [...]
    }

### <a name="standalone-errors"></a>Изолированные ошибки
В отличие от ошибок сеанса изолированные ошибки можно использовать вне контекста сеанса.

**Пример.**

    [[EngagementAgent shared] sendError:@"something_failed" extras:nil];

## <a name="reporting-jobs"></a>Уведомление о заданиях
**Пример**

Предположим, что вам необходимо сообщить о продолжительности процесса входа в систему:

    [...]
    -(void)signIn
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      [... sign in ...]

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    }
    [...]

### <a name="report-errors-during-a-job"></a>Уведомление об ошибках при выполнении задания
Ошибки могут быть связаны с выполняемым заданием, а не с текущим сеансом пользователя.

**Пример.**

Предположим, что вам необходимо сообщить об ошибке во время входа в систему:

    [...]
    -(void)signin
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      BOOL success = NO;
      while (!success) {
        /* Try to sign in */
        NSError* error = nil;
        [self trySigin:&error];
        success = error == nil;

        /* If an error occured report it */
        if(!success)
        {
          [[EngagementAgent shared] sendJobError:@"sign_in_error"
                         jobName:@"sign_in"
                          extras:[NSDictionary dictionaryWithObject:[error localizedDescription] forKey:@"error"]];

          /* Retry after a moment */
          [NSThread sleepForTimeInterval:20];
        }
      }

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    };
    [...]

### <a name="events-during-a-job"></a>События при выполнении задания
События могут быть связаны с выполняемым заданием, а не с текущим сеансом пользователя.

**Пример.**

Предположим, что у нас есть социальная сеть, и мы используем задание, чтобы сообщить об общем времени подключения пользователя к серверу. Пользователь может получить сообщения от своих друзей. Это и есть событием задания.

    [...]
    - (void) signin
    {
      [...Sign in code...]
      [[EngagementAgent shared] startJob:@"connection" extras:nil];
    }
    [...]
    - (void) signout
    {
      [...Sign out code...]
      [[EngagementAgent shared] endJob:@"connection"];
    }
    [...]
    - (void) onMessageReceived
    {
      [...Notify user...]
      [[EngagementAgent shared] sendJobEvent:@"connection" jobName:@"message_received" extras:nil];
    }
    [...]

## <a name="extra-parameters"></a>Дополнительные параметры
Произвольные данные можно прикрепить к событиям, ошибкам, действиям и заданиям.

Эти данные могут быть структурированы. Они используют класс NSDictionary в iOS.

Обратите внимание, что дополнения могут содержать `arrays(NSArray, NSMutableArray)`, `numbers(NSNumber class)`, `strings(NSString, NSMutableString)`, `urls(NSURL)`, `data(NSData, NSMutableData)` или другие экземпляры `NSDictionary`.

> [!NOTE]
> Дополнительный параметр сериализуется в JSON-файле. Если требуется передать объекты, отличные от описанных выше, в классе следует реализовать следующий метод:
> 
> -(NSString*)JSONRepresentation;
> 
> Метод должен возвратить представление объекта в формате JSON.
> 
> 

### <a name="example"></a>Пример
    NSMutableDictionary* extras = [NSMutableDictionary dictionaryWithCapacity:2];
    [extras setObject:[NSNumber numberWithInt:123] forKey:@"video_id"];
    [extras setObject:@"http://foobar.com/blog" forKey:@"ref_click"];
    [[EngagementAgent shared] sendEvent:@"video_clicked" extras:extras];

### <a name="limits"></a>Ограничения
#### <a name="keys"></a>ключей
Каждый ключ в `NSDictionary` должен соответствовать следующему регулярному выражению:

`^[a-zA-Z][a-zA-Z_0-9]*`

Это означает, что ключ должен содержать не менее одной буквы, за которой следуют буквы, цифры или символы подчеркивания (\_).

#### <a name="size"></a>Размер
Длина дополнений ограничена **1024** знаками на один вызов (после кодирования в JSON-файле агентом Engagement).

В предыдущем примере длина JSON-файла, отправленного на сервер, составляет 58 знаков:

    {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

## <a name="reporting-application-information"></a>Уведомление о данных приложения
С помощью функции `sendAppInfo:` вы можете вручную сообщать о данных отслеживания (или любых других данных о приложении).

Обратите внимание, что такие данные можно отправлять поэтапно, так как для заданного устройства будет сохраняться только последнее значение заданного ключа.

Как и дополнения событий, класс `NSDictionary` используется для абстрагирования данных о приложении. Обратите внимание, что массивы или вложенные словари будут считаться неструктурированными строками (в которых используется сериализация JSON).

**Пример.**

    NSMutableDictionary* appInfo = [NSMutableDictionary dictionaryWithCapacity:2];
    [appInfo setObject:@"female" forKey:@"gender"];
    [appInfo setObject:@"1983-12-07" forKey:@"birthdate"]; // December 7th 1983
    [[EngagementAgent shared] sendAppInfo:appInfo];

### <a name="limits"></a>Ограничения
#### <a name="keys"></a>ключей
Каждый ключ в `NSDictionary` должен соответствовать следующему регулярному выражению:

`^[a-zA-Z][a-zA-Z_0-9]*`

Это означает, что ключ должен содержать не менее одной буквы, за которой следуют буквы, цифры или символы подчеркивания (\_).

#### <a name="size"></a>Размер
Данные приложения ограничены **1024** знаками на один вызов (после кодирования в JSON-файле агентом Engagement).

В предыдущем примере длина JSON-файла, отправленного на сервер, составляет 44 знака:

    {"birthdate":"1983-12-07","gender":"female"}
