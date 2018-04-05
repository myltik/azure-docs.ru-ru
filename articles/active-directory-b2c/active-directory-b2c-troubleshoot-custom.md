---
title: 'Azure AD B2C: устранение неполадок в настраиваемых политиках с помощью Application Insights | Документация Майкрософт'
description: Сведения о настройке Application Insights для отслеживания выполнения пользовательских политик
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/04/2017
ms.author: davidmu
ms.openlocfilehash: 3e27976b02ee75083b07ca2e4f0c06e19502d1f5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-collecting-logs"></a>Azure Active Directory B2C: сбор журналов

В этой статье приводятся действия по сбору журналов из Azure AD B2C, с помощью которых вы сможете диагностировать неполадки в пользовательских политиках.

>[!NOTE]
>Сейчас детально описанные здесь журналы действий предназначены **только** для упрощения разработки пользовательских политик. Не используйте режим разработки в рабочей среде.  В журналах регистрируются все утверждения, отправляемые и принимаемые поставщиками удостоверений во время разработки.  При использовании режима разработки в рабочей среде разработчик несет ответственность за персональные данные (личные сведения), собранные в его журнале App Insights.  Эти подробные журналы могут собираться, только когда политика помещается в **режим разработки**.


## <a name="use-application-insights"></a>Использование Application Insights

Служба Azure AD B2C поддерживает функцию отправки данных в Application Insights.  Application Insights позволяет диагностировать исключения и визуализировать проблемы производительности в приложениях.

### <a name="setup-application-insights"></a>Настройка Application Insights

1. Перейдите на [портал Azure](https://portal.azure.com). Убедитесь, что вы находитесь в клиенте с подпиской Azure (а не в клиенте Azure AD B2C).
1. На панели навигации слева щелкните **+ Создать**.
1. Найдите и выберите **Application Insights**, а затем щелкните **Создать**.
1. Заполните форму и щелкните **Создать**. Выберите **универсальный** **тип приложения**.
1. Откройте ресурс Application Insights после его создания.
1. Найдите пункт **Свойства** в меню слева и выберите его.
1. Скопируйте **ключ инструментирования** и сохраните его для использования в следующем разделе.

### <a name="set-up-the-custom-policy"></a>Настройка пользовательской политики

1. Откройте файл проверяющей стороны (например, SignUpOrSignin.xml).
1. Добавьте следующие атрибуты в элемент `<TrustFrameworkPolicy>`.

  ```XML
  DeploymentMode="Development"
  UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  ```

1. Если он еще не существует, добавьте дочерний узел `<UserJourneyBehaviors>` в узел `<RelyingParty>`. Он должен находиться сразу после `<DefaultUserJourney ReferenceId="UserJourney Id from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`.
2. Добавьте следующий узел в качестве дочернего узла элемента `<UserJourneyBehaviors>`. Обязательно замените `{Your Application Insights Key}` **ключом инструментирования**, полученным из Application Insights в предыдущем разделе.

  ```XML
  <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
  ```

  * Параметр `DeveloperMode="true"` сообщает Application Insights о том, что необходимо ускорить передачу данных телеметрии через конвейер обработки. Это удобно при разработке, но связано с ограничениями при больших объемах.
  * Параметр `ClientEnabled="true"` отправляет Application Insights клиентский скрипт для отслеживания представления страницы и ошибок на стороне клиента (необязательно).
  * Параметр `ServerEnabled="true"` отправляет существующие данные JSON UserJourneyRecorder как пользовательское событие в Application Insights.
Пример:

  ```XML
  <TrustFrameworkPolicy
    ...
    TenantId="fabrikamb2c.onmicrosoft.com"
    PolicyId="SignUpOrSignInWithAAD"
    DeploymentMode="Development"
    UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
  </TrustFrameworkPolicy>
  ```

3. Отправьте политику.

### <a name="see-the-logs-in-application-insights"></a>Просмотр журналов в Application Insights

>[!NOTE]
> Новые журналы отобразятся в Application Insights через некоторое время (менее 5 минут).

1. Откройте созданный ресурс Application Insights на [портале Azure](https://portal.azure.com).
1. В меню **Обзор** выберите пункт **Analytics**.
1. Откройте новую вкладку в Application Insights
1. Ниже приведен список запросов, которые можно использовать для просмотра журналов.

| Запрос | ОПИСАНИЕ |
|---------------------|--------------------|
traces | Просмотр всех журналов, созданных Azure AD B2C |
traces \| where timestamp > ago(1d) | Просмотр всех журналов, созданных Azure AD B2C за последний день

Записи могут быть длинными.  Выполните экспорт в CSV-файл, чтобы изучить их подробнее.

Дополнительные сведения об инструменте Analytics см. [здесь](https://docs.microsoft.com/azure/application-insights/app-insights-analytics).

>[!NOTE]
>Для разработчиков удостоверений сообщество разработало средство просмотра пути взаимодействия пользователя.  Это средство не поддерживается Майкрософт и предоставляется исключительно в том виде, в котором оно было создано.  Оно считывается из экземпляра Application Insights и обеспечивает хорошо структурированное представление событий пути взаимодействия пользователя.  Исходный код можно получить и развернуть в собственном решении.

Версия средства просмотра, которое считывает события из Application Insights, находится [здесь](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)

>[!NOTE]
>Сейчас детально описанные здесь журналы действий предназначены **только** для упрощения разработки пользовательских политик. Не используйте режим разработки в рабочей среде.  В журналах регистрируются все утверждения, отправляемые и принимаемые поставщиками удостоверений во время разработки.  При использовании режима разработки в рабочей среде разработчик несет ответственность за персональные данные (личные сведения), собранные в его журнале App Insights.  Эти подробные журналы могут собираться, только когда политика помещается в **режим разработки**.

[Репозиторий GitHub с примерами неподдерживаемых пользовательских политик и связанных с ними средств](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies)



## <a name="next-steps"></a>Дальнейшие действия

Изучите данные в Application Insights, чтобы понять, как работает платформа Identity Experience Framework, лежащая в основе B2C, чтобы предоставить собственные удостоверения.
