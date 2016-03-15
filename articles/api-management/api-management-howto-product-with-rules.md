<properties
	pageTitle="Защита API с помощью управления API Azure| Microsoft Azure"
	description="Узнайте, как защитить API с помощью политик квот и регулирования (ограничения скорости)."
	services="api-management"
	documentationCenter=""
	authors="steved0x"
	manager="erikre"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/27/2016"
	ms.author="sdanie"/>

# Защита API путем ограничения скорости с помощью управления API Azure

В этом руководстве показано, как просто защитить серверный API, настроив политики ограничения скорости и политики квот с помощью управления API Azure.

В ходе работы над руководством вы создадите продукт "Бесплатная пробная версия", в котором есть API. Разработчики смогут вызывать этот API до 10 раз в минуту и до 200 раз в неделю, используя политики [ограничения скорости вызова по подписке](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) и [задания квоты использования по подписке](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota). Затем вы опубликуете API и протестируете политику ограничения скорости.

Более сложные сценарии регулирования, в которых используются политики [rate-limit-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) и [quota-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey), описаны в статье [Расширенное регулирование запросов с помощью управления API](api-management-sample-flexible-throttling.md).

## <a name="create-product"> </a>Создание продукта

На этом шаге создается бесплатный пробный продукт, который не требует утверждения подписки.

>[AZURE.NOTE] Если вы уже настроили продукт и хотите использовать его в этом учебнике, можно сразу перейти к разделу [Настройка политик ограничения скорости вызова и политик квот][] и выполнить указания учебника с этого места, используя вместо бесплатной пробной версии продукта свой продукт.

Для начала выберите на классическом портале Azure службу управления API и нажмите кнопку **Управление**. Будет открыт портал издателя службы управления API.

![Портал издателя][api-management-management-console]

>Если экземпляр службы управления API еще не создан, см. раздел [Создание экземпляра службы управления API][] в руководстве [Начинаем работу со службой управления API][].

Щелкните **Продукты** в меню **Управление API** слева для вывода страницы **Продукты**.

![Добавление продукта][api-management-add-product]

Щелкните **Добавить продукт** для отображения диалогового окна **Добавление нового продукта**.

![Добавление нового продукта][api-management-new-product-window]

В поле **Заголовок** введите **Бесплатная пробная версия**.

В текстовом поле **Описание** введите 
**Подписчики смогут выполнять до 10 вызовов в минуту и до 200 вызовов в неделю, после чего доступ будет запрещен**.

Продукты в службе управления API могут быть защищенными или открытыми. Доступ к защищенным продуктам предоставляется по подписке. Открытые продукты могут использоваться без подписки. Обязательно установите флажок **Требуется подписка**, чтобы создать защищенный продукт, требующий подписки. Это значение по умолчанию.

Для просмотра и утверждения администратором попыток подписки на продукт установите флажок **Требуется утверждение администратором**. Если флажок не установлен, попытки подписки получат автоматическое утверждение. В этом примере подписки утверждаются автоматически, поэтому не устанавливайте этот флажок.

Чтобы разрешить для учетных записей разработчика многократную подписку на новый продукт, установите флажок **Разрешить несколько одновременных подписок**. В этом учебнике не используется несколько одновременных подписок, поэтому не устанавливайте этот флажок.

После ввода значений щелкните **Сохранить** для создания продукта.

![Продукт создан][api-management-product-added]

По умолчанию новые продукты могут видеть пользователи группы **Администраторы**. Мы добавим группу **Разработчики**. Щелкните **Бесплатная пробная версия** и выберите вкладку **Видимость**.

>В службе управления API группы используются для управления видимостью продуктов для разработчиков. Продукты предоставляют видимость группам, и разработчики могут просматривать и подписываться на продукты, видимые для групп, к которым они принадлежат. Для получения дополнительной информации см. раздел [Как создать и использовать группы в службе управления Azure API][].

![Добавление группы разработчиков][api-management-add-developers-group]

Установите флажок **Разработчики** и нажмите кнопку **Сохранить**.

## <a name="add-api"> </a>Добавление API к продукту

На этом шаге руководства Echo API добавится для нового продукта Free Trial.

>Каждый экземпляр службы API Management поставляется предварительно настроенным с Echo API, с которым можно экспериментировать при изучении API Management. Дополнительные сведения см. в разделе [Начало работы с Azure API Management][].

Щелкните **Продукты** в меню **Управление API** слева и выберите **Бесплатная пробная версия** для настройки продукта.

![Настройка продукта][api-management-configure-product]

Щелкните **Добавить API к продукту**.

![Добавление API к продукту][api-management-add-api]

Выберите **Echo API**, а затем нажмите кнопку **Сохранить**.

![Добавление Echo API][api-management-add-echo-api]

## <a name="policies"> </a>Настройка ограничений скорости вызова и политик квот

Ограничения скорости и квоты настраиваются в редакторе политик. Щелкните элемент **Политики** в меню **Управление API** слева. В списке **Продукт** выберите **Бесплатная пробная версия**.

![Политика продукта][api-management-product-policy]

Нажмите кнопку **Добавить политику**, чтобы импортировать шаблон политики и начать создание ограничения скорости и политики квот.

![Добавление политики][api-management-add-policy]

Для вставки политик наведите курсор на раздел **inbound** или **outbound** в шаблоне политики. Ограничение скорости и политики квот – это входящие политики, поэтому поместите курсор в раздел входящих.

![Редактор политики][api-management-policy-editor-inbound]

В этом руководстве добавляются две политики, [Ограничить скорость вызова][] и [Задать квоту использования][].

![Правила политики][api-management-limit-policies]

Поместив курсор в элемент политики **входящие**, щелкните стрелку рядом с политикой **Ограничить скорость вызова** для вставки шаблона политики.

	<rate-limit calls="number" renewal-period="seconds">
	<api name="name" calls="number">
	<operation name="name" calls="number" />
	</api>
	</rate-limit>

Политика **Ограничить скорость вызова** может использоваться на уровне продукта и может также быть использована на уровнях API и индивидуального имени операции. В этом учебнике используются только политики уровня продукта, поэтому удалите элементы **api** и **operation** из элемента **rate-limit**, чтобы остался только внешний элемент **rate-limit**, как показано в следующем примере.

	<rate-limit calls="number" renewal-period="seconds">
	</rate-limit>

В бесплатной версии продукта Free Trial максимальная разрешенная скорость вызова — 10 вызовов в минуту, поэтому введите **10** в качестве атрибута **вызовов** и **60** в качестве атрибута **renewal-period**.

	<rate-limit calls="10" renewal-period="60">
	</rate-limit>

Для настройки политики **Задать квоту использования** установите курсор непосредственно внизу только что добавленного элемента **rate-limit** в элементе **inbound** и щелкните стрелку слева от политики **Задать квоту использования**.

	<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
	<api name="name" calls="number" bandwidth="kilobytes">
	<operation name="name" calls="number" bandwidth="kilobytes" />
	</api>
	</quota>

Поскольку эта политика также предназначена для уровня продукта, удалите элементы **api** и **operation**, как показано в следующем примере.

	<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
	</quota>

Квоты могут основываться на количестве вызовов за интервал, пропускной способности или обоих факторах. В этом руководстве нет регулировки по пропускной способности, поэтому удалите атрибут **bandwidth**.

	<quota calls="number" renewal-period="seconds">
	</quota>

Для продукта Free Trial квота составит 200 вызовов в неделю. Укажите **200** как значение атрибута **вызовов** и **604800** как значение для атрибута **renewal-period**.

	<quota calls="200" renewal-period="604800">
	</quota>

>Интервалы политики указываются в секундах. Для вычисления интервала в неделю можно умножить число дней (7) на число часов в дне (24), на число минут в часе (60) и на число секунд в минуте (60): 7 * 24 * 60 * 60 = 604 800.

После окончания настройки политика должна соответствовать следующему примеру.

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

После настройки желаемой политики нажмите кнопку **Сохранить**.

![Сохранение политики][api-management-policy-save]

## <a name="publish-product"> </a> Публикация продукта

Теперь, когда API добавлены и политики настроены, продукт можно опубликовать, чтобы его могли использовать разработчики. Щелкните **Продукты** в меню **Управление API** слева и выберите **Бесплатная пробная версия** для настройки продукта.

![Настройка продукта][api-management-configure-product]

Щелкните **Опубликовать** и затем щелкните **Да, опубликовать** для подтверждения.

![Публикация продукта][api-management-publish-product]

## <a name="subscribe-account"> </a>Подписка учетной записи разработчика на продукт

Теперь продукт опубликован и доступен для подписки и использования разработчиками.

>Администраторы экземпляра API Management автоматически подписываются на каждый продукт. На этом шаге руководства будет проведена подписка одной из учетных записей, не принадлежащей администратору, на бесплатную пробную версию продукта. Если учетная запись разработчика является частью роли "Администраторы", можно выполнить этот шаг, даже если подписка уже оформлена.

Щелкните пункт **Пользователи** в меню **Управление API** слева и выберите имя учетной записи разработчика. В этом примере используется учетная запись **Clayton Gragg**.

![Настройка разработчика][api-management-configure-developer]

Нажмите кнопку **Добавить подписку**.

![Добавление подписки][api-management-add-subscription-menu]

Выберите **Бесплатная пробная версия**, а затем нажмите кнопку **Подписаться**.

![Добавление подписки][api-management-add-subscription]

>[AZURE.NOTE] В этом учебнике для бесплатной пробной версии продукта не включается несколько одновременных подписок. Если бы они включались, вам было бы предложено назвать подписку, как показано в следующем примере.

![Добавление подписки][api-management-add-subscription-multiple]

После нажатия кнопки **Подписаться** продукт появится в списке **Подписки** для пользователя.

![Подписка добавлена][api-management-subscription-added]

## <a name="test-rate-limit"> </a>Вызов операции и тестирование ограничения скорости

Теперь, когда продукт Free Trial настроен и опубликован, можно вызвать некоторые операции и протестировать политику ограничения скорости. Перейдите на портал разработчика, щелкнув **Портал разработчика** в меню вверху справа.

![Портал разработчика][api-management-developer-portal-menu]

Щелкните **API** в верхнем меню, а затем **Echo API**.

![Портал разработчика][api-management-developer-portal-api-menu]

Щелкните **Ресурс GET**, а затем — **Попробовать**.

![Открытие консоли][api-management-open-console]

Сохраните значения по умолчанию и выберите ключ подписки для бесплатной пробной версии продукта.

![Ключ подписки][api-management-select-key]

>[AZURE.NOTE] Если у вас несколько подписок, убедитесь, что выбран ключ для **бесплатной пробной версии**, иначе политики, настроенные на предыдущем шаге, не смогут вступить в силу.

Щелкните **Отправить**, а затем просмотрите ответ. Обратите внимание, что **Состояние ответа** – **200 OK**.

![Результаты операции][api-management-http-get-results]

Щелкайте **Отправить** на скорости, превышающей ограничение скорости политики в 10 вызовов в минуту. Как только ограничение скорости политики будет превышено, возвращается ответ **429 – Слишком много запросов**.

![Результаты операции][api-management-http-get-429]

**Содержимое ответа** указывает оставшийся интервал, после которого повторы будут успешными.

При действующей политике ограничения скорости в 10 вызовов в минуту дальнейшие вызовы будут неудачными, пока не пройдет 60 секунд после первых 10 успешных вызовов продукта, после которых ограничение скорости было превышено. В этом примере оставшийся интервал составляет 54 секунды.

## <a name="next-steps"> </a>Дальнейшие действия

-	Просмотрите другие разделы в руководстве [Приступая к работе с расширенными параметрами API][].
-	Демонстрацию настройки ограничений скорости и квот см. в следующем видео.

> [AZURE.VIDEO rate-limits-and-quotas]


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
[Get started with Azure API Management]: api-management-get-started.md
[Как создать и использовать группы в службе управления Azure API]: api-management-howto-create-groups.md
[View subscribers to a product]: api-management-howto-add-products.md#view-subscribers
[Начало работы с Azure API Management]: api-management-get-started.md
[Начинаем работу со службой управления API]: api-management-get-started.md
[Создание экземпляра службы управления API]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps

[Create a product]: #create-product
[Настройка политик ограничения скорости вызова и политик квот]: #policies
[Add an API to the product]: #add-api
[Publish the product]: #publish-product
[Subscribe a developer account to the product]: #subscribe-account
[Call an operation and test the rate limit]: #test-rate-limit
[Приступая к работе с расширенными параметрами API]: api-management-get-started-advanced.md

[Ограничить скорость вызова]: https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate
[Задать квоту использования]: https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota

<!---HONumber=AcomDC_0309_2016-->