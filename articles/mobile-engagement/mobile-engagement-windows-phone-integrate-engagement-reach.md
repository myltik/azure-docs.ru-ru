---
title: Интеграция пакета SDK Reach для Windows Phone Silverlight
description: Интеграция Служб мобильного взаимодействия Azure Reach с приложениями Windows Phone Silverlight
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d3516a6b-db9f-4cdb-a475-4148edf81af1
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 34e5ce414ebf72fbecef6c921e57128e2658c921
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="windows-phone-silverlight-reach-sdk-integration"></a>Интеграция пакета SDK Reach для Windows Phone Silverlight
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

Перед выполнением действий, описанных в этом руководстве, необходимо выполнить процедуру интеграции, изложенную в документе [Интеграция пакета SDK Engagement для Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) .

## <a name="embed-the-engagement-reach-sdk-into-your-windows-phone-silverlight-project"></a>Внедрение пакета SDK для Engagement Reach в проект Windows Phone Silverlight
Ничего добавлять не нужно. `EngagementReach` уже включены в проект.

> [!TIP]
> Вы можете изменить изображения, которые находятся в папке `Resources` проекта, в частности фирменный значок (по умолчанию используется значок Engagement).
> 
> 

## <a name="add-the-capabilities"></a>Добавление возможностей
Для пакета SDK для Engagement Reach нужны определенные дополнительные возможности.

Откройте файл `WMAppManifest.xml` и убедитесь, что объявлены следующие возможности:

* `ID_CAP_PUSH_NOTIFICATION`
* `ID_CAP_WEBBROWSERCOMPONENT`

Первая возможность используется службой MPNS для отображения всплывающих уведомлений. Вторая позволяет внедрить задачу браузера в пакет SDK.

Измените файл `WMAppManifest.xml` и добавьте следующее внутри тега `<Capabilities />`:

    <Capability Name="ID_CAP_PUSH_NOTIFICATION" />
    <Capability Name="ID_CAP_WEBBROWSERCOMPONENT" />

## <a name="enable-the-microsoft-push-notification-service"></a>Включение службы push-уведомлений Майкрософт
Чтобы использовать **службу push-уведомлений Майкрософт** (MPNS), в файле `WMAppManifest.xml` должен быть тег `<App />` с именем проекта в качестве атрибута `Publisher`.

## <a name="initialize-the-engagement-reach-sdk"></a>Запуск пакета SDK для Engagement Reach
### <a name="engagement-configuration"></a>Конфигурация Engagement
Конфигурация Engagement централизована в файле `Resources\EngagementConfiguration.xml` проекта.

Измените этот файл, чтобы задать конфигурацию Reach:

* *Необязательно.* Укажите, активированы ли системные push-уведомления (MPNS) между тегами `<enableNativePush>` и `</enableNativePush>` (по умолчанию `true`).
* *Необязательно.* Укажите имя канала push-уведомлений между тегами `<channelName>` и `</channelName>`. Задайте то имя, которое приложение может использовать сейчас, или не указывайте ничего.

Если вместо этого вам необходимо указать ее во время выполнения, можно вызвать следующий метод до инициализации агента Engagement:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    engagementConfiguration.Reach.EnableNativePush = true;                  
    /* [Optional] whether the native push (MPNS) is activated or not. */

    engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   
    /* [Optional] Provide the same channel name that your application may currently use. */

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

> [!TIP]
> Вы можете указать имя канала push-уведомлений MPNS своего приложения. По умолчанию Engagement создает имя на основе appId, так что его не нужно задавать самостоятельно, за исключением случаев, когда вы планируете использовать канал push-уведомлений вне Engagement.
> 
> 

### <a name="engagement-initialization"></a>Инициализация Engagement
Измените `App.xaml.cs`:

* Добавьте операторы `using`:
  
      using Microsoft.Azure.Engagement;
* Вставьте `EngagementReach.Instance.Init` сразу `EngagementAgent.Instance.Init` после в `Application_Launching`:
  
      private void Application_Launching(object sender, LaunchingEventArgs e)
      {
         EngagementAgent.Instance.Init();
         EngagementReach.Instance.Init();
      }
* Вставьте `EngagementReach.Instance.OnActivated` в метод `Application_Activated`:
  
      private void Application_Activated(object sender, ActivatedEventArgs e)
      {
         EngagementAgent.Instance.OnActivated(e);
         EngagementReach.Instance.OnActivated(e);
      }

> [!IMPORTANT]
> `EngagementReach.Instance.Init` выполняется в выделенном потоке. Вам не придется делать это самостоятельно.
> 
> 

## <a name="app-store-submission-considerations"></a>Замечания по отправке приложений в Магазин
Корпорация Майкрософт ввела ряд правил в отношении использования push-уведомлений.

Вот цитата из раздела 2.9 документации по [политикам в отношении приложений] Майкрософт:

1) Нужно попросить пользователя дать согласие на получение push-уведомлений. Поэтому добавьте в параметры возможность отключения push-уведомлений.

Объект EngagementReach предоставляет два метода управления явным согласием и отказом: `EnableNativePush()` и `DisableNativePush()`. Вы можете, например, создать в настройках параметр, позволяющий включить или отключить MPNS.

Вы также можете отключить MPNS с помощью конфигурации Engagement \<windows-phone-sdk-reach-configuration\>.

> 2.9.1. Сначала приложение должно показать описание предоставляемых уведомлений и **получить явное разрешение (согласие) пользователя**. В нем также **должен быть предусмотрен механизм, с помощью которого пользователь мог бы отказаться от получения push-уведомлений**. Все уведомления, предоставляемые с помощью службы push-уведомлений Майкрософт, должны соответствовать описанию, предоставленному пользователю, и всем применимым [политикам в отношении приложений][Content Policies] и [дополнительным требованиям к приложениям определенных типов].
> 
> 

2) Не следует использовать слишком много push-уведомлений. Управлять уведомлениями будет Engagement.

> 2.9.2) Приложение и предусмотренные в нем механизмы использования службы push-уведомлений Майкрософт не должны слишком интенсивно использовать ресурсы или полосу пропускания сети службы push-уведомлений Майкрософт или создавать любую другую недопустимую нагрузку на устройство Windows Phone, другие устройства Майкрософт или другие службы вследствие отправки чрезмерного (по разумному усмотрению Майкрософт) количества push-уведомлений. Кроме того, они не должны конфликтовать с сетями или серверами Майкрософт или сторонних поставщиков, подключенными к службам push-уведомлений Майкрософт, либо нарушать их работу.
> 
> 

3) Не следует полагаться на MPNS, если необходимо отправить критически важную информацию. Engagement использует MPNS, поэтому это правило также действует для кампаний, созданных во внешних компонентах Engagement.

> 2.9.3) Службу push-уведомлений Майкрософт нельзя использовать для отправки критически важных уведомлений или уведомлений, от которых зависит жизнь или смерть, в том числе критически важных уведомлений, связанных с медицинскими устройствами или состоянием здоровья. КОРПОРАЦИЯ МАЙКРОСОФТ НЕ ДАЕТ НИКАКИХ ГАРАНТИЙ ТОГО, ЧТО ИСПОЛЬЗОВАНИЕ СЛУЖБЫ PUSH-УВЕДОМЛЕНИЙ МАЙКРОСОФТ И ДОСТАВКА УВЕДОМЛЕНИЙ ЭТОЙ СЛУЖБЫ БУДЕТ ОСУЩЕСТВЛЯТЬСЯ БЕСПЕРЕБОЙНО, БЕЗОШИБОЧНО И В РЕЖИМЕ РЕАЛЬНОГО ВРЕМЕНИ.
> 
> 

**Если вы не будете следовать этим рекомендациям, мы не гарантируем, что приложения пройдут процесс проверки.**

## <a name="handle-data-push-optional"></a>Обработка отправленных данных (необязательно)
Если вы хотите, чтобы приложения могли получать отправленные данные рекламных кампаний, необходимо реализовать два события класса EngagementReach:

    EngagementReach.Instance.DataPushStringReceived += (body) =>
    {
       Debug.WriteLine("String data push message received: " + body);
       return true;
    };

    EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
    {
       Debug.WriteLine("Base64 data push message received: " + encodedBody);
       // Do something useful with decodedBody like updating an image view
       return true;
    };

Как можно видеть, обратный вызов каждого метода возвращает логическое значение. После отправки данных Engagement отправляет отзыв серверной части. Если обратный вызов возвращает значение false, будет отправлен отзыв `exit` . В противном случае отправляется `action`. Если для событий не задан обратный вызов, Engagement будет возвращен отзыв `drop` .

> [!WARNING]
> Engagement не может получать несколько отзывов для отправленных данных. Не забывайте, что если вы планируете установить для события несколько обработчиков, отзывы будут соответствовать последнему из них. Во избежание путаницы с отзывами на внешних компонентах советуем, чтобы в этом случае возвращаемое значение было всегда одинаковым.
> 
> 

## <a name="customize-ui-optional"></a>Настройка пользовательского интерфейса (необязательно)
### <a name="first-step"></a>Первый шаг
Мы позаботились о том, чтобы вы могли настраивать функциональность пользовательского интерфейса по своему усмотрению.

Для этого необходимо создать подкласс класса `EngagementReachHandler` .

**Пример кода:**

    using Microsoft.Azure.Engagement;

    namespace Example
    {
       internal class ExampleReachHandler : EngagementReachHandler
       {
          // Override EngagementReachHandler methods depending on your needs
       }
    }

Затем задайте содержимое поля `EngagementReach.Instance.Handler`, используя настраиваемый объект в классе `App.xaml.cs` метода `Application_Launching`.

**Пример кода:**

    private void Application_Launching(object sender, LaunchingEventArgs e)
    {
       // your app initialization 
       EngagementReach.Instance.Handler = new ExampleReachHandler();
       // Engagement Agent and Reach initialization
    }

> [!NOTE]
> По умолчанию Engagement использует собственную реализацию `EngagementReachHandler`. Ее не обязательно создавать отдельно, но даже если вы ее создадите, вам не придется переопределять каждый метод. Поведение по умолчанию — выбор базового объекта Engagement.
> 
> 

### <a name="layouts"></a>Макеты
По умолчанию рекламная кампания будет использовать внедренные ресурсы библиотеки DLL для отображения уведомлений и страниц.

Тем не менее, вы можете применять собственные ресурсы, чтобы использовать в оформлении компонентов свою фирменную символику.

Вы можете переопределить методы `EngagementReachHandler` в подклассе, чтобы в Engagement использовались ваши макеты:

**Пример кода:**

    // In your subclass of EngagementReachHandler

    public override string GetTextViewAnnouncementUri()
    {
       // return the path of your own xaml
    }

    public override string GetWebViewAnnouncementUri()
    {
       // return the path of your own xaml
    }

    public override string GetPollUri()
    {
       // return the path of your own xaml
    }

    public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
    {
       // return a new instance of your own notification
    }

> [!TIP]
> Метод `CreateNotification` может возвращать значение NULL. Уведомления не будут отображаться и рекламная кампания будет удалена.
> 
> 

Чтобы упростить реализацию макета, мы также предоставляем свой код на языке XAML, который может служить основой для вашего кода. Они находятся в архиве пакета SDK для Engagement (/src/reach/).

> [!WARNING]
> Мы предоставляем те же источники, которые используем сами. Поэтому если вы хотите изменить их напрямую, не забудьте изменить пространство имен и имя.
> 
> 

### <a name="notification-position"></a>Расположение уведомлений
По умолчанию уведомление отображается в приложении снизу слева. Это можно изменить, переопределив метод `GetNotificationPosition` объекта `EngagementReachHandler`.

    // In your subclass of EngagementReachHandler

    public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
    {
       // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
    }

На данный момент вы можете выбрать расположение `BOTTOM` (по умолчанию) или `TOP`.

### <a name="launch-message"></a>Запуск сообщения
Если щелкнуть системное уведомление (всплывающее уведомление), Engagement запустит приложение, загрузит содержимое push-сообщений и отобразит страницу для соответствующей кампании.

Между запуском приложения и отображением страницы есть определенная задержка (в зависимости от скорости сети).

Чтобы уведомить пользователя о загрузке, необходимо предоставить визуальную информацию, например индикатор выполнения. Engagement не может обработать ее самостоятельно, но предоставляет несколько обработчиков, которыми вы можете воспользоваться.

Чтобы реализовать обратный вызов, сделайте вот что:

    /* The application has launched and the content is loading.
     * You should display an indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };

    /* The application has finished loading the content and the page
     * is about to be displayed.
     * You should hide the indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };

    /* The content has been loaded, but an error has occurred.
     * You can provide an information to the user.
     * You should hide the indicator here.
     */
    EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

Вы можете задать обратный вызов в методе `Application_Launching` файла `App.xaml.cs` (предпочтительно перед вызовом `EngagementReach.Instance.Init()`).

> [!TIP]
> Каждый обработчик вызывается потоком пользовательского интерфейса. При использовании MessageBox или других компонентов, связанных с пользовательским интерфейсом, об этом можно не беспокоится.
> 
> 

[политикам в отношении приложений]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Content Policies]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[дополнительным требованиям к приложениям определенных типов]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx

