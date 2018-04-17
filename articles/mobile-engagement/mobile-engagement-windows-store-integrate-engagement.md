---
title: Интеграция пакета SDK Engagement для универсальных приложений для Windows
description: Интеграция Служб мобильного взаимодействия Azure с универсальными приложениями для Windows
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 71236b68-5ebd-44aa-8c82-c7ca8098ea05
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: dc075ffe6565b4ef60e77f74ba4f065629c99952
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-apps-engagement-sdk-integration"></a>Интеграция пакета SDK Engagement для универсальных приложений для Windows
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

> [!div class="op_single_selector"]
> * [Универсальная платформа Windows](mobile-engagement-windows-store-integrate-engagement.md) 
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
> * [iOS](mobile-engagement-ios-integrate-engagement.md) 
> * [Android](mobile-engagement-android-integrate-engagement.md) 
> 
> 

Здесь описана самая простая процедура активации функций раздела аналитики и мониторинга Engagement в универсальном приложении для Windows.

Достаточно выполнить следующие шаги, чтобы активировать отчеты по журналам, которые необходимы для вычисления всех статистических данных, касающихся пользователей, сеансов, действий, сбоев и технической информации. Отчеты по журналам, необходимые для вычисления других статистических данных (например, касающихся событий, ошибок и заданий), требуется активировать вручную с помощью API Служб мобильного взаимодействия (см. статью [Как использовать API Служб мобильного взаимодействия в iOS](mobile-engagement-windows-store-use-engagement-api.md)), так как эти статистические данные зависят от приложения.

## <a name="supported-versions"></a>Поддерживаемые версии
Пакет SDK для Служб мобильного взаимодействия для универсальных приложений для Windows можно интегрировать только в приложения среды выполнения Windows и универсальной платформы Windows, предназначенные для:

* Windows 8
* Windows 8.1
* Windows Phone 8.1
* Windows 10 (настольных компьютеров и мобильных устройств)

> [!NOTE]
> Если вы намерены использовать Windows Phone Silverlight, см. [процедуру интеграции Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md).
> 
> 

## <a name="install-the-mobile-engagement-universal-apps-sdk"></a>Установка пакета SDK Служб мобильного взаимодействия для универсальных приложений
### <a name="all-platforms"></a>Все платформы
Пакет SDK Служб мобильного взаимодействия для универсальных приложений для Windows доступен в виде пакета Nuget, который называется *MicrosoftAzure.MobileEngagement*. Вы можете установить его из диспетчера пакетов NuGet в Visual Studio.

### <a name="windows-8x-and-windows-phone-81"></a>Windows 8.x и Windows Phone 8.1
NuGet автоматически развертывает ресурсы пакета SDK в папку `Resources` , расположенную в корне проекта приложения.

### <a name="windows-10-universal-windows-platform-applications"></a>Универсальные приложения для Windows 10
Автоматическое развертывание ресурсов пакета SDK в приложениях UWP пока не поддерживается. Пока эта функция не будет реализована, ресурсы необходимо развертывать вручную:

1. Откройте обозреватель файлов.
2. Откройте следующую папку (**x.x.x** — это версия устанавливаемой службы Engagement): *%USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\\**x.x.x**\\content\win81*
3. Перетащите папку **Ресурсы** из обозревателя файлов в корень проекта в Visual Studio.
4. В Visual Studio выберите проект и активируйте значок **Показать все файлы** в верхней части **обозревателя решений**.
5. Некоторые файлы в проект не включены. Чтобы импортировать все файлы сразу, щелкните папку **Ресурсы** правой кнопкой мыши и выберите параметр **Исключить из проекта**. Чтобы включить всю папку в проект, еще раз щелкните папку **Ресурсы** правой кнопкой мыши и выберите параметр **Включить в проект**. Теперь все файлы из папки **Ресурсы** включены в проект.

## <a name="add-the-capabilities"></a>Добавление возможностей
Для надлежащей работы пакету SDK для Engagement требуются некоторые возможности пакета SDK для Windows.

Откройте файл `Package.appxmanifest` и убедитесь, что объявлены следующие возможности:

* `Internet (Client)`

## <a name="initialize-the-engagement-sdk"></a>Запуск пакета SDK для Engagement
### <a name="engagement-configuration"></a>Конфигурация Engagement
Конфигурация Engagement централизована в файле `Resources\EngagementConfiguration.xml` проекта.

Измените этот файл, чтобы указать:

* строку подключения приложения между тегами `<connectionString>` and `<\connectionString>`.

Если вместо этого вам необходимо указать ее во время выполнения, можно вызвать следующий метод до инициализации агента Engagement:

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

Теперь строка подключения для приложения отображается на портале Azure.

### <a name="engagement-initialization"></a>Инициализация Engagement
При создании проекта создается файл `App.xaml.cs` . Этот класс наследуется из `Application` и содержит множество важных методов. Он также будет использоваться для инициализации пакета SDK для Engagement.

Измените `App.xaml.cs`:

* Добавьте операторы `using` :
  
      using Microsoft.Azure.Engagement;
* Определите метод совместного использования инициализации Engagement для всех вызовов сразу:
  
      private void InitEngagement(IActivatedEventArgs e)
      {
        EngagementAgent.Instance.Init(e);
  
        // or
  
        EngagementAgent.Instance.Init(e, engagementConfiguration);
      }
* Вызовите `OnLaunched` в методе `InitEngagement`:
  
      protected override void OnLaunched(LaunchActivatedEventArgs e)
      {
        InitEngagement(e);
      }
* При запуске приложения с использованием настраиваемой схемы, другого приложения или командной строки вызывается метод `OnActivated`. При активации приложения необходимо также запустить пакет SDK для службы Engagement. Чтобы это сделать, переопределите метод `OnActivated` :
  
      protected override void OnActivated(IActivatedEventArgs args)
      {
        InitEngagement(args);
      }

> [!IMPORTANT]
> Настоятельно рекомендуем не добавлять инициализацию Engagement в другом расположении приложения.
> 
> 

## <a name="basic-reporting"></a>Упрощенные отчеты
### <a name="recommended-method-overload-your-page-classes"></a>Рекомендуемый метод: перегрузка классов `Page`
Чтобы активировать отчет по всем журналам, необходимым для Engagement при вычислении статистики пользователей, сеансов, действий, сбоев и технической информации, вы можете просто задать для подклассов `Page` наследование из классов `EngagementPage`.

Ниже приведен пример того, как сделать это для страницы приложения. То же самое можно сделать для всех страниц приложения.

#### <a name="c-source-file"></a>Исходный файл на C#
Измените файл страницы `.xaml.cs` :

* Добавьте операторы `using`:
  
      using Microsoft.Azure.Engagement;
* Замените `Page` на `EngagementPage`:

**Без Engagement:**

        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**С Engagement:**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [!IMPORTANT]
> Если страница переопределяет метод `OnNavigatedTo`, необходимо обязательно вызвать `base.OnNavigatedTo(e)`. В противном случае о действии не будет сообщено (`EngagementPage` вызывает `StartActivity` внутри метода `OnNavigatedTo`).
> 
> 

#### <a name="xaml-file"></a>XAML-файл
Измените файл страницы `.xaml` :

* Добавьте в объявления пространств имен:
  
      xmlns:engagement="using:Microsoft.Azure.Engagement"
* Замените `Page` на `engagement:EngagementPage`:

**Без Engagement:**

        <Page>
            <!-- layout -->
            ...
        </Page>

**С Engagement:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

#### <a name="override-the-default-behaviour"></a>Переопределение действия по умолчанию
По умолчанию имя класса страницы сообщается как имя действия без дополнительной информации. Если класс использует суффикс Page, Engagement также удалит его.

Если требуется переопределить поведение по умолчанию имени, просто добавьте в код следующее:

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
Если вам не удается перегрузить классы `Page` или вы не хотите этого делать, запустите действия, вызвав методы `EngagementAgent` напрямую.

Рекомендуем вызывать `StartActivity` внутри метода `OnNavigatedTo` своей страницы.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [!IMPORTANT]
> Убедитесь, что сеанс завершен правильно.
> 
> Пакет SDK для универсальных приложений для Windows автоматически вызывает метод `EndActivity` при закрытии приложения. Поэтому мы **настоятельно** рекомендуем вызывать метод `StartActivity` при каждом изменении активности пользователя и **никогда** не вызывать метод `EndActivity`. Этот метод отправляет на сервер Engagement сообщение о том, что текущий пользователь вышел из приложения, и это отражается во всех журналах приложений.
> 
> 

## <a name="advanced-reporting"></a>Расширенные отчеты
При желании можно также сообщать об определенных событиях, ошибках и заданиях приложения. Это можно сделать с помощью других методов в классе `EngagementAgent`. Engagement API позволяет использовать все дополнительные возможности Engagement.

Дополнительные сведения см. в статье [Как использовать API Служб мобильного взаимодействия для универсальных приложений для Windows](mobile-engagement-windows-store-use-engagement-api.md).

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
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Пакетный режим
По умолчанию служба Engagement ведет отчеты по журналам в режиме реального времени. Если приложение создает отчеты по журналам очень часто, лучше поместить журналы в буфер и создавать отчеты по всем журналам одновременно через регулярные промежутки времени (это называется «пакетный режим»).

Для этого вызовите следующий метод:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

Значение аргумента задается в **миллисекундах**. Если вам в любое время потребуется повторно активировать ведение журнала в реальном времени, просто вызовите метод без каких-либо параметров или со значением 0.

Пакетный режим немного продлевает время работы батареи, но влияет на Engagement Monitor: время выполнения всех сеансов и заданий будет округляться до порогового значения пакета (таким образом, сеансы и задания, время выполнения которых короче, чем пороговое значение пакета, могут не отображаться). Мы советуем использовать пороговое значение пакета не более чем 30 000 (30 с). Следует иметь в виду, что в сохраняемых журналах может быть не более 300 элементов. Если отправка занимает слишком много времени, некоторые журналы могут быть потеряны.

> [!WARNING]
> Пороговое значение для пакета нельзя задать на период меньше 1 с. При попытке задать значение менее одной секунды трассировка в пакете SDK отобразится с ошибкой, и будет автоматически восстановлено значения по умолчанию, т. е. ноль секунд. Это приведет к тому, что пакет SDK начнет создавать отчеты по журналам в режиме реального времени.
> 
> 

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview

