---
title: Интеграция пакета SDK Engagement для Windows Phone Silverlight
description: Интеграция Служб мобильного взаимодействия Azure с приложениями Windows Phone Silverlight
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 447fea8d-f4e3-4ad4-8ec0-8e3cf1ad3ab0
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: f041f19ceff366a34b1761bfdca12242f4bdf9b8
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="windows-phone-silverlight-engagement-sdk-integration"></a>Интеграция пакета SDK Engagement для Windows Phone Silverlight
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md) 
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
> * [iOS](mobile-engagement-ios-integrate-engagement.md) 
> * [Android](mobile-engagement-android-integrate-engagement.md) 
> 
> 

Здесь описана самая простая процедура активации функций раздела аналитики и мониторинга Служб мобильного взаимодействия Azure в приложении Windows Phone Silverlight.

Достаточно выполнить следующие шаги, чтобы активировать отчеты по журналам, которые необходимы для вычисления всех статистических данных, касающихся пользователей, сеансов, действий, сбоев и технической информации. Отчеты по журналам, необходимые для вычисления других статистических данных (например, касающихся событий, ошибок и заданий), требуется активировать вручную с помощью API Служб мобильного взаимодействия (см. статью [Как использовать API для расширенного добавления тегов Служб мобильного взаимодействия в приложении Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md) ниже), так как эти статистические данные зависят от приложения.

## <a name="supported-versions"></a>Поддерживаемые версии
Пакет SDK для Служб мобильного взаимодействия для Windows Silverlight можно интегрировать только в приложения, предназначенные для:

* Windows Phone 8.0
* Windows Phone 8.1 Silverlight

> [!NOTE]
> Если вы ориентируетесь на Windows Phone 8.1 (не Silverlight), см. статью [Интеграция пакета SDK Engagement для универсальных приложений для Windows](mobile-engagement-windows-store-integrate-engagement.md).
> 
> 

## <a name="install-the-mobile-engagement-silverlight-sdk"></a>Установка пакета SDK для Служб мобильного взаимодействия для Silverlight
Пакет SDK Служб мобильного взаимодействия для Windows Silverlight доступен в виде пакета Nuget, который называется *MicrosoftAzure.MobileEngagement*. Вы можете установить его из диспетчера пакетов NuGet в Visual Studio. 

## <a name="add-the-capabilities"></a>Добавление возможностей
Для надлежащей работы пакету SDK для Engagement требуются некоторые возможности пакета SDK для Windows Phone Silverlight.

Откройте файл `WMAppManifest.xml` и убедитесь, что следующие возможности объявлены на панели `Capabilities`:

* `ID_CAP_NETWORKING`
* `ID_CAP_IDENTITY_DEVICE`

## <a name="initialize-the-engagement-sdk"></a>Запуск пакета SDK для Engagement
### <a name="engagement-configuration"></a>Конфигурация Engagement
Конфигурация Engagement централизована в файле `Resources\EngagementConfiguration.xml` проекта.

Измените файл, чтобы указать:

* строку подключения приложения между тегами `<connectionString>` and `<\connectionString>`.

Если вместо этого вам необходимо указать ее во время выполнения, можно вызвать следующий метод до инициализации агента Engagement:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

Теперь строка подключения для приложения отображается на портале Azure.

### <a name="engagement-initialization"></a>Инициализация Engagement
При создании проекта создается файл `App.xaml.cs` . Этот класс наследуется из `Application` и содержит множество важных методов. Он также будет использоваться для инициализации пакета SDK для Engagement.

Измените `App.xaml.cs`:

* Добавьте операторы `using`:
  
      using Microsoft.Azure.Engagement;
* Вставьте `EngagementAgent.Instance.Init` в метод `Application_Launching`:
  
      private void Application_Launching(object sender, LaunchingEventArgs e)
      {
        EngagementAgent.Instance.Init();
      }
* Вставьте `EngagementAgent.Instance.OnActivated` в метод `Application_Activated`:
  
      private void Application_Activated(object sender, ActivatedEventArgs e)
      {
         EngagementAgent.Instance.OnActivated(e);
      }

> [!WARNING]
> Настоятельно рекомендуем не добавлять инициализацию Engagement в другом расположении приложения. Однако учтите, что метод `EngagementAgent.Instance.Init` выполняется в выделенном потоке, а не в потоке пользовательского интерфейса.
> 
> 

## <a name="basic-reporting"></a>Упрощенные отчеты
### <a name="recommended-method--overload-your-phoneapplicationpage-classes"></a>Рекомендуемый метод: перегрузка классов `PhoneApplicationPage`
Чтобы активировать отчет по всем журналам, необходимым для Engagement при вычислении статистики пользователей, сеансов, действий, сбоев и технической информации, вы можете просто задать для подклассов `PhoneApplicationPage` наследование из классов `EngagementPage`.

Ниже приведен пример того, как сделать это для страницы приложения. То же самое можно сделать для всех страниц приложения.

#### <a name="c-source-file"></a>Исходный файл на C#
Измените файл страницы `.xaml.cs` :

* Добавьте операторы `using`:
  
      using Microsoft.Azure.Engagement;
* Замените `PhoneApplicationPage` на `EngagementPage`:

**Без Engagement:**

        namespace Example
        {
          public partial class ExamplePage : PhoneApplicationPage
          {
            [...]
          }
        }

**С Engagement:**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [!WARNING]
> Если страница наследуется из метода `OnNavigatedTo`, убедитесь в вызове `base.OnNavigatedTo(e)`. В противном случае служба не сообщит о действии. Более того, `EngagementPage` вызывает `StartActivity` в методе `OnNavigatedTo`.
> 
> 

#### <a name="xaml-file"></a>XAML-файл
Измените файл страницы `.xaml` :

* Добавьте в объявления пространств имен:
  
      xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
* Замените `phone:PhoneApplicationPage` на `engagement:EngagementPage`:

**Без Engagement:**

        <phone:PhoneApplicationPage>
            <!-- layout -->
        </phone:PhoneApplicationPage>

**С Engagement:**

        <engagement:EngagementPage 
            xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">

            <!-- layout -->
        </engagement:EngagementPage >

#### <a name="override-the-default-behavior"></a>Переопределение действия по умолчанию
По умолчанию имя класса страницы сообщается как имя действия без дополнительной информации. Если класс использует суффикс Page, Engagement также удалит его.

Если требуется переопределить поведение по умолчанию для имени, просто добавьте в код следующую строку:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
           /* your code */
           return "new name";
        }

Чтобы сообщить дополнительную информацию о действии, добавьте в код следующую строку:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
           /* your code */
           return extra;
        }

Эти методы вызываются из метода `OnNavigatedTo` вашей страницы.

### <a name="alternate-method-call-startactivity-manually"></a>Альтернативный метод: вызов `StartActivity()` вручную
Если вам не удается перегрузить классы `PhoneApplicationPage` или вы не хотите этого делать, запустите действия, вызвав методы `EngagementAgent` напрямую.

Советуем вызывать `StartActivity` в методе `OnNavigatedTo` класса PhoneApplicationPage.

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
           base.OnNavigatedTo(e);
           EngagementAgent.Instance.StartActivity("MyPage");
        }

> [!IMPORTANT]
> Убедитесь, что сеанс завершен правильно.
> 
> Пакет SDK автоматически вызывает метод `EndActivity` при закрытии приложения. Поэтому мы **настоятельно** рекомендуем вызывать метод `StartActivity` при каждом изменении действия пользователя и **никогда** не вызывать метод `EndActivity`. Этот метод отправляет на сервер Engagement сообщение о том, что текущий пользователь вышел из приложения, и это отражается во всех журналах приложений.
> 
> 

## <a name="advanced-reporting"></a>Расширенные отчеты
При желании можно также сообщать об определенных событиях, ошибках и заданиях приложения. Это можно сделать с помощью других методов в классе `EngagementAgent`. Engagement API позволяет использовать все дополнительные возможности Engagement.

Дополнительные сведения см. в статье [Как использовать API Служб мобильного взаимодействия в Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md).

## <a name="advanced-configuration"></a>Расширенная конфигурация
### <a name="disable-automatic-crash-reporting"></a>Отключение автоматического создания отчетов о сбоях
Вы можете отключить функцию автоматического создания отчетов о сбоях в Engagement. После этого при возникновении необработанного исключения Engagement не будет предпринимать никаких действий.

> [!WARNING]
> Если вы планируете отключить эту функцию, учтите, что при возникновении необработанного сбоя в приложении Engagement не будет отправлять уведомление о сбое **И** не закроет сеанс и задания.
> 
> 

Чтобы отключить автоматическое создание отчетов о сбоях, просто настройте конфигурацию в зависимости от того, как она была объявлена:

#### <a name="from-engagementconfigurationxml-file"></a>Из файла `EngagementConfiguration.xml`
Задайте для параметра уведомления о сбоях значение `false` между тегами `<reportCrash>` и `</reportCrash>`.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>Из объекта `EngagementConfiguration` во время выполнения
Задайте для параметра уведомления о сбоях значение false с помощью объекта EngagementConfiguration.

        /* Engagement configuration. */

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        /\* Disable Engagement crash reporting. \*/ engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Пакетный режим
По умолчанию служба Engagement ведет отчеты по журналам в режиме реального времени. Если приложение создает отчеты по журналам очень часто, лучше поместить журналы в буфер и создавать отчеты по всем журналам одновременно через регулярные промежутки времени (это называется «пакетный режим»).

Для этого вызовите следующий метод:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

Значение аргумента задается в **миллисекундах**. Если вам в любое время потребуется повторно активировать ведение журнала в реальном времени, просто вызовите метод без каких-либо параметров или со значением 0.

Пакетный режим немного продлевает время работы батареи, но влияет на Engagement Monitor: время выполнения всех сеансов и заданий будет округляться до порогового значения пакета (таким образом, сеансы и задания, время выполнения которых короче, чем пороговое значение пакета, могут не отображаться). Мы советуем использовать пороговое значение пакета не более чем 30 000 (30 с). Следует иметь в виду, что в сохраняемых журналах может быть не более 300 элементов. Если отправка занимает слишком много времени, некоторые журналы могут быть потеряны.

> [!WARNING]
> Для порогового значения пакета нельзя настроить период менее одной секунды. При попытке задать значение менее одной секунды трассировка в пакете SDK отобразится с ошибкой, и будет автоматически восстановлено значения по умолчанию, т. е. ноль секунд. Это приведет к тому, что пакет SDK начнет создавать отчеты по журналам в режиме реального времени.
> 
> 

