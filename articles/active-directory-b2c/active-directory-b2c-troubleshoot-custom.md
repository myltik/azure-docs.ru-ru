---
title: "Azure Active Directory B2C: устранение неполадок в пользовательских политиках | Документация Майкрософт"
description: "Инструкции по устранению неполадок в настраиваемых политиках Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: saeeda
manager: krassk
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: saeeda
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 2fa67038f2a214c1569fc65fd9f1beba394cb790
ms.contentlocale: ru-ru
ms.lasthandoff: 04/27/2017

---

# <a name="azure-active-directory-b2c-collecting-logs"></a>Azure Active Directory B2C: сбор журналов

В этой статье приводятся действия по сбору журналов из Azure AD B2C, с помощью которых вы сможете диагностировать неполадки в пользовательских политиках.

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
  UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  ```

1. Если он еще не существует, добавьте дочерний узел `<UserJourneyBehaviors>` в узел `<RelyingParty>`.
2. Добавьте следующий узел в качестве дочернего узла элемента `<UserJourneyBehaviors>`. Обязательно замените `{Your Application Insights Key}` **ключом инструментирования**, полученным в предыдущем разделе.

  ```XML
  <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
  ```

  * Параметр `DeveloperMode="true"` сообщает Application Insights о том, что необходимо ускорить передачу данных телеметрии через конвейер обработки. Это удобно при разработке, но связано с ограничениями при больших объемах.
  * Параметр `ClientEnabled="true"` отправляет Application Insights клиентский скрипт для отслеживания представления страницы и ошибок на стороне клиента (необязательно).
  * Параметр `ServerEnabled="true"` отправляет существующие данные JSON UserJourneyRecorder как пользовательское событие в Application Insights.
  Окончательная версия XML-данных будет выглядеть следующим образом:

  ```XML
  <TrustFrameworkPolicy
    ...
    TenantId="fabrikamb2c.onmicrosoft.com"
    PolicyId="SignUpOrSignInWithAAD"
    UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  >
    ...
    <RelyingParty>
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
  </TrustFrameworkPolicy>
  ```

3. Отправьте политику.

### <a name="see-the-logs"></a>Просмотр журналов

>[!NOTE]
> Новые журналы отобразятся в Application Insights через короткое время (менее 5 минут).

1. Откройте созданный ресурс Application Insights на [портале Azure](https://portal.azure.com).
1. В меню **Обзор** выберите пункт **Analytics**.
1. Откройте новую вкладку в Application Insights
1. Ниже приведен список запросов, которые можно использовать для просмотра журналов.

| Запрос | Описание |
|---------------------|--------------------|
traces | Просмотр всех журналов, созданных Azure AD B2C |
traces \| where timestamp > ago(1d) | Просмотр всех журналов, созданных Azure AD B2C за последний день

Дополнительные сведения об инструменте Analytics см. [здесь](https://docs.microsoft.com/azure/application-insights/app-insights-analytics).





