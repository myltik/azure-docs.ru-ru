---
title: "Защита API с помощью службы управления API Azure | Документация Майкрософт"
description: "Узнайте, как защитить API с помощью политик квот и регулирования (ограничения скорости)."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 450dc368-d005-401d-ae64-3e1a2229b12f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 30ec6f45da114b6c7bc081f8a2df46f037de61fd
ms.openlocfilehash: 73c9675490f95f68450716cd67e58df9c84daef8


---
# <a name="protect-your-api-with-rate-limits-using-azure-api-management"></a>Защита API путем ограничения скорости с помощью управления API Azure
В этом руководстве показано, как просто защитить серверный API, настроив политики ограничения скорости и политики квот с помощью управления API Azure.

В ходе работы с этим руководством вы создадите продукт "Бесплатная пробная версия", в котором есть API. Разработчики смогут вызывать этот API до 10 раз в минуту и до 200 раз в неделю, используя политики [Limit call rate per subscription](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) (Ограничение скорости вызова по подписке) и [Set usage quota per subscription](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota) (Установка квоты использования по подписке). Затем вы опубликуете API и протестируете политику ограничения скорости.

Более сложные сценарии регулирования, в которых используются политики [rate-limit-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) и [quota-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey), описаны в статье [Расширенное регулирование запросов с помощью управления API](api-management-sample-flexible-throttling.md).

## <a name="create-product"> </a>Создание продукта
На этом шаге создается бесплатная пробная версия продукта, которая не требует утверждения подписки.

> [!NOTE]
> Если вы уже настроили продукт и хотите использовать его в этом руководстве, можно сразу перейти к разделу [Настройка ограничений скорости вызова и политик квот][Configure call rate limit and quota policies] и выполнить указания руководства с этого места, используя вместо бесплатной пробной версии продукта свой продукт.
> 
> 

Чтобы начать работу, щелкните **Publisher portal** (Портал издателя) на портале Azure для службы управления API.

![Портал издателя][api-management-management-console]

> Если экземпляр службы управления API еще не создан, выполните инструкции из раздела [Создание экземпляра управления API][Create an API Management service instance] в руководстве [Начало работы со службой управления Azure API][Manage your first API in Azure API Management].
> 
> 

В меню **Управление API** слева щелкните **Продукты**, чтобы открыть страницу **Продукты**.

![Добавление продукта][api-management-add-product]

Щелкните **Добавить продукт**, чтобы открыть диалоговое окно **Add new product** (Добавление нового продукта).

![Добавление нового продукта][api-management-new-product-window]

В поле **Заголовок** введите **Бесплатная пробная версия**.

В текстовом поле **Описание** введите:  **Подписчики смогут выполнять до 10 вызовов в минуту и до 200 вызовов в неделю, после чего доступ будет запрещен.**

Продукты в службе управления API могут быть защищенными или открытыми. Доступ к защищенным продуктам предоставляется по подписке. Открытые продукты могут использоваться без подписки. Обязательно установите флажок **Require subscription** (Требуется подписка), чтобы создать защищенный продукт, требующий подписки. Это значение по умолчанию.

Установите флажок **Require subscription approval**(Требуется утверждение администратора), чтобы администратор просматривал и утверждал попытки подписки на продукт. Если не установить этот флажок, попытки подписки будут утверждаться автоматически. В этом примере подписки утверждаются автоматически, поэтому не устанавливайте этот флажок.

Чтобы разрешить для учетных записей разработчика многократную подписку на новый продукт, установите флажок **Allow multiple simultaneous subscriptions** (Разрешить несколько одновременных подписок). В этом руководстве не используются несколько одновременных подписок, поэтому не устанавливайте этот флажок.

После ввода значений щелкните **Сохранить** для создания продукта.

![Продукт создан][api-management-product-added]

По умолчанию новые продукты могут видеть пользователи группы **Администраторы** . Мы добавим группу **Разработчики** . Щелкните **Бесплатная пробная версия** и выберите вкладку **Видимость**.

> В службе управления API группы используются для управления видимостью продуктов для разработчиков. Продукты предоставляют видимость группам, и разработчики могут просматривать и подписываться на продукты, видимые для групп, к которым они принадлежат. Дополнительные сведения см. в статье [Как создавать и использовать группы для управления учетными записями разработчика в службе управления Azure API][How to create and use groups in Azure API Management].
> 
> 

![Добавление группы разработчиков][api-management-add-developers-group]

Установите флажок **Разработчики** и нажмите кнопку **Сохранить**.

## <a name="add-api"> </a>Добавление API к продукту
На этом шаге руководства Echo API добавится для нового продукта Free Trial.

> Каждый экземпляр службы API Management поставляется предварительно настроенным с Echo API, с которым можно экспериментировать при изучении API Management. Дополнительные сведения см. в статье [Начало работы со службой управления Azure API][Manage your first API in Azure API Management].
> 
> 

В меню **Управление API** слева щелкните **Продукты** и выберите **Бесплатная пробная версия** для настройки продукта.

![Настройка продукта][api-management-configure-product]

Щелкните **Добавить API к продукту**.

![Добавить API к продукту][api-management-add-api]

Выберите **Echo API**, а затем нажмите кнопку **Сохранить**.

![Добавление Echo API][api-management-add-echo-api]

## <a name="policies"> </a>Настройка ограничений скорости вызова и политик квот
Ограничения скорости и квоты настраиваются в редакторе политик. В меню **Управление API** слева щелкните **Политики**. В списке **Продукт** выберите **Бесплатная пробная версия**.

![Политика продукта][api-management-product-policy]

Нажмите кнопку **Добавить политику** , чтобы импортировать шаблон политики и начать создание ограничения скорости и политики квот.

![добавление политики;][api-management-add-policy]

Чтобы вставить политики, наведите курсор на раздел **inbound** или **outbound** в шаблоне политики. Ограничение скорости и политики квот – это входящие политики, поэтому поместите курсор в раздел входящих.

![Редактор политики][api-management-policy-editor-inbound]

В этом руководстве добавляются две политики: [Limit call rate per subscription](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) (Ограничение скорости вызова по подписке) и [Set usage quota per subscription](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota) (Установка квоты использования по подписке).

![Правила политики][api-management-limit-policies]

Поместив курсор в элемент политики **inbound**, щелкните стрелку рядом с политикой **Limit call rate per subscription** (Ограничение скорости вызова по подписке), чтобы вставить шаблон политики.

```xml
<rate-limit calls="number" renewal-period="seconds">
<api name="name" calls="number">
<operation name="name" calls="number" />
</api>
</rate-limit>
```

**Ограничение скорости вызова по подписке** можно использовать на уровне продукта, а также на уровне API и на уровне имени отдельной операции. В этом руководстве используются только политики уровня продукта, поэтому удалите элементы **api** и **operation** из элемента **rate-limit**, чтобы остался только внешний элемент **rate-limit**, как показано в следующем примере.

```xml
<rate-limit calls="number" renewal-period="seconds">
</rate-limit>
```

В бесплатной версии продукта "Бесплатная пробная версия" максимальная разрешенная скорость вызова — 10 вызовов в минуту, поэтому введите **10** в качестве значения атрибута **calls** и **60** в качестве значения атрибута **renewal-period**.

```xml
<rate-limit calls="10" renewal-period="60">
</rate-limit>
```

Чтобы настроить политику **Set usage quota per subscription** (Установка квоты использования по подписке), поместите курсор под только что добавленным элементом **rate-limit** в элементе **inbound** и щелкните стрелку слева от политики **Set usage quota per subscription** (Установка квоты использования по подписке).

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
<api name="name" calls="number" bandwidth="kilobytes">
<operation name="name" calls="number" bandwidth="kilobytes" />
</api>
</quota>
```

Так как эта политика также предназначена для уровня продукта, удалите элементы **api** и **operation**, как показано в следующем примере.

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
</quota>
```

Квоты могут основываться на количестве вызовов за интервал, пропускной способности или обоих факторах. В этом руководстве нет регулировки по пропускной способности, поэтому удалите атрибут **bandwidth** .

```xml
<quota calls="number" renewal-period="seconds">
</quota>
```

Для продукта Free Trial квота составит 200 вызовов в неделю. Укажите **200** в качестве значения атрибута **calls** и **604800** в качестве значения атрибута **renewal-period**.

```xml
<quota calls="200" renewal-period="604800">
</quota>
```

> Интервалы политики указываются в секундах. Для вычисления интервала в неделю можно умножить число дней (7) на число часов в дне (24), на число минут в часе (60) и на число секунд в минуте (60): 7 * 24 * 60 * 60 = 604 800.
> 
> 

После окончания настройки политика должна соответствовать следующему примеру.

```xml
<policies>
    <inbound>
        <rate-limit calls="10" renewal-period="60">
        </rate-limit>
        <quota calls="200" renewal-period="604800">
        </quota>
        <base />

</inbound>
<outbound>

    <base />

    </outbound>
</policies>
```

После настройки желаемой политики нажмите кнопку **Сохранить**.

![Сохранение политики][api-management-policy-save]

## <a name="publish-product"> </a> Публикация продукта
Теперь, когда API добавлены и политики настроены, продукт можно опубликовать, чтобы его могли использовать разработчики. В меню **Управление API** слева щелкните **Продукты** и выберите **Бесплатная пробная версия** для настройки продукта.

![Настройка продукта][api-management-configure-product]

Щелкните **Опубликовать**, а затем — **Yes, publish it** (Да, опубликовать) для подтверждения.

![Публикация продукта][api-management-publish-product]

## <a name="subscribe-account"> </a>Подписка учетной записи разработчика на продукт
Теперь продукт опубликован и доступен для подписки и использования разработчиками.

> Администраторы экземпляра API Management автоматически подписываются на каждый продукт. На этом шаге руководства будет проведена подписка одной из учетных записей, не принадлежащей администратору, на бесплатную пробную версию продукта. Если учетная запись разработчика является частью роли "Администраторы", можно выполнить этот шаг, даже если подписка уже оформлена.
> 
> 

В меню **Управление API** слева щелкните **Пользователи** и выберите имя учетной записи разработчика. В этом примере используется учетная запись **Clayton Gragg** .

![Настройка разработчика][api-management-configure-developer]

Нажмите кнопку **Добавить подписку**.

![Добавить подписку][api-management-add-subscription-menu]

Выберите **Бесплатная пробная версия**, а затем нажмите кнопку **Подписаться**.

![Добавить подписку][api-management-add-subscription]

> [!NOTE]
> В этом руководстве для бесплатной пробной версии продукта не включаются несколько одновременных подписок. Если бы они включались, вам было бы предложено назвать подписку, как показано в следующем примере.
> 
> 

![Добавить подписку][api-management-add-subscription-multiple]

После нажатия кнопки **Подписаться** продукт появится в списке **Подписки**.

![Подписка добавлена][api-management-subscription-added]

## <a name="test-rate-limit"> </a>Вызов операции и тестирование ограничения скорости
Теперь, когда продукт Free Trial настроен и опубликован, можно вызвать некоторые операции и протестировать политику ограничения скорости.
Перейдите на портал разработчика, щелкнув **Портал разработчика** в меню вверху справа.

![Портал разработчика][api-management-developer-portal-menu]

Щелкните **API** в верхнем меню, а затем выберите **Echo API**.

![Портал разработчика][api-management-developer-portal-api-menu]

Щелкните **GET Resource** (Ресурс GET), а затем нажмите кнопку **Попробовать**.

![Открытие консоли][api-management-open-console]

Сохраните значения по умолчанию и выберите ключ подписки для бесплатной пробной версии продукта.

![Ключ подписки][api-management-select-key]

> [!NOTE]
> Если у вас несколько подписок, убедитесь, что выбран ключ для **бесплатной пробной версии**, иначе политики, настроенные на предыдущем шаге, не смогут вступить в силу.
> 
> 

Щелкните **Отправить**, а затем просмотрите ответ. Обратите внимание, что для **состояния ответа** отображается значение **200 OK**.

![Результаты операции][api-management-http-get-results]

Щелкайте **Отправить** со скоростью, превышающей ограничение скорости политики в 10 вызовов в минуту. Как только ограничение скорости политики будет превышено, возвращается ответ **429 – Слишком много запросов** .

![Результаты операции][api-management-http-get-429]

**Содержимое ответа** указывает оставшийся интервал, после которого повторы будут успешными.

При действующей политике ограничения скорости в 10 вызовов в минуту дальнейшие вызовы будут неудачными, пока не пройдет 60 секунд после первых 10 успешных вызовов продукта, после которых ограничение скорости было превышено. В этом примере оставшийся интервал составляет 54 секунды.

## <a name="next-steps"> </a>Дальнейшие действия
* Демонстрацию настройки ограничений скорости и квот см. в следующем видео.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]
> 
> 

[api-management-management-console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
[api-management-new-product-window]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
[api-management-product-added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
[api-management-add-policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
[api-management-policy-editor-inbound]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
[api-management-limit-policies]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
[api-management-policy-save]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
[api-management-configure-product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
[api-management-add-api]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
[api-management-add-echo-api]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
[api-management-developer-portal-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
[api-management-publish-product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
[api-management-configure-developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
[api-management-add-subscription-menu]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
[api-management-add-subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
[api-management-developer-portal-api-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
[api-management-open-console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-product-with-rules/api-management-http-get.png
[api-management-http-get-results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
[api-management-http-get-429]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
[api-management-product-policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
[api-management-add-developers-group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
[api-management-select-key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
[api-management-subscription-added]: ./media/api-management-howto-product-with-rules/api-management-subscription-added.png
[api-management-add-subscription-multiple]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-multiple.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Manage your first API in Azure API Management]: api-management-get-started.md
[How to create and use groups in Azure API Management]: api-management-howto-create-groups.md
[View subscribers to a product]: api-management-howto-add-products.md#view-subscribers
[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps

[Create a product]: #create-product
[Configure call rate limit and quota policies]: #policies
[Add an API to the product]: #add-api
[Publish the product]: #publish-product
[Subscribe a developer account to the product]: #subscribe-account
[Call an operation and test the rate limit]: #test-rate-limit

[Limit call rate]: https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate
[Set usage quota]: https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota



<!--HONumber=Dec16_HO3-->


