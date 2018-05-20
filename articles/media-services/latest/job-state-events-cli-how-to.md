---
title: Маршрутизация событий Служб мультимедиа Azure в пользовательскую конечную веб-точку | Документация Майкрософт
description: Подпишитесь на событие изменения состояния Служб мультимедиа с помощью службы "Сетка событий Azure".
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 2780438e715b6f6cb04d820c02d09f14e14b480f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="route-azure-media-services-events-to-a-custom-web-endpoint-using-cli"></a>Маршрутизация событий Служб мультимедиа Azure в пользовательскую конечную веб-точку с помощью CLI

"Сетка событий Azure" — это служба обработки событий для облака. В этой статье с помощью интерфейса командной строки Azure вы создадите подписку на события изменения состояния задания Служб мультимедиа Azure и активируете событие, чтобы просмотреть результат. 

Как правило, события можно отправлять в конечную точку, реагирующую на событие, такую как веб-перехватчик или функция Azure. В этом руководстве описывается процесс создания и настройки веб-перехватчика.

Выполнив действия, описанные в этой статье, вы увидите, что данные событий отправлены в конечную точку.

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите на [портал Azure](http://portal.azure.com) и запустите **CloudShell**, чтобы выполнить команды CLI, приведенные на следующих шагах.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Если вы решили установить и использовать CLI локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

Запишите значения, которые вы использовали в качестве имени учетной записи, имени хранилища и имени ресурса Служб мультимедиа.

## <a name="enable-event-grid-resource-provider"></a>Включение поставщика ресурсов службы "Сетка событий"

Первое, что нужно сделать — убедиться, что поставщик ресурсов службы "Сетка событий" включен в вашей подписке. 

На портале **Azure** сделайте следующее:

1. Перейдите к подпискам.
2. Выберите свою подписку.
3. В разделе параметров выберите "Поставщики ресурсов".
4. Найдите EventGrid.
5. Убедитесь, что Сетка событий зарегистрирована. Если это не так, нажмите кнопку **Зарегистрировать**.  

## <a name="create-a-generic-azure-function-webhook"></a>Создание универсального веб-перехватчика функции Azure 

### <a name="create-a-message-endpoint"></a>Создание конечной точки сообщения

Прежде чем оформлять подписку на статью Сетки событий, создайте конечную точку, которая собирает сообщения, чтобы можно было их просматривать.

Создайте функцию, которая активируется с помощью универсального веб-перехватчика, как описано в [этой статье](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function). В рамках этого руководства используется код **C#**.

После создания веб-перехватчика скопируйте URL-адрес, щелкнув ссылку *Получить URL-адрес функции* в верхней части окна на портале **Azure**. Последняя часть URL-адреса не понадобится (*&clientID=default*).

![Создание веб-перехватчика](./media/job-state-events-cli-how-to/generic_webhook_files.png)

### <a name="validate-the-webhook"></a>Проверка веб-перехватчика

Когда вы регистрируете собственную конечную точку веб-перехватчика с помощью службы "Сетка событий", отправляется запрос POST с кодом простой проверки, чтобы подтвердить владение конечной точкой. В ответ приложение должно вернуть код проверки. Служба "Сетка событий" не доставляет события в конечные точки веб-перехватчика, которые не прошли проверку. Дополнительные сведения см. в разделе [Сетка событий: безопасность и проверка подлинности](https://docs.microsoft.com/azure/event-grid/security-authentication). Этот раздел определяет две части, которые необходимо определить для успешного выполнения проверки.

#### <a name="update-the-source-code"></a>Обновление исходного кода

После создания веб-перехватчика в браузере откроется файл **run.csx**. Замените код по умолчанию приведенным ниже. 

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"Webhook was triggered!");

    string jsonContent = await req.Content.ReadAsStringAsync();
    string eventGridValidation = 
        req.Headers.FirstOrDefault( x => x.Key == "Aeg-Event-Type" ).Value?.FirstOrDefault();

    dynamic eventData = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"event: {eventData}");

    if (eventGridValidation != String.Empty)
    {
        if (eventData[0].data.validationCode !=String.Empty && eventData[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent")
        {
            return req.CreateResponse(HttpStatusCode.OK, new 
            {
                validationResponse = eventData[0].data.validationCode
            });
        }
    }
    
    log.Info(jsonContent);

    return req.CreateResponse(HttpStatusCode.OK);
}
```

#### <a name="update-test-request-body"></a>Обновите текст запроса для проверки

В правой части окна на портале **Azure** отображаются две вкладки: **Просмотреть файлы** и **Test** (Проверка). Откройте вкладку **Тест**. В поле **Текст запроса** вставьте следующий код JSON. Вы можете вставить его как есть без изменения значений.

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2017-08-06T22:09:30.740323Z"
}
]
```

Щелкните **Сохранить и запустить** в верхней части окна.

![Тело запроса](./media/job-state-events-cli-how-to/generic_webhook_test.png)

## <a name="register-for-the-event-grid-subscription"></a>Регистрация на подписку службы "Сетка событий" 

Подпишитесь на статью, чтобы определить в службе "Сетка событий", какие события вам необходимо отслеживать. В следующем примере создается подписка на созданную учетную запись Служб мультимедиа и передается URL-адрес из веб-перехватчика функций Azure в качестве конечной точки для уведомления о событии. 

Замените `<event_subscription_name>` уникальным именем подписки на события. Для `<resource_group_name>` и `<ams_account_name>` используйте созданные ранее значения.  Для `<endpoint_URL>` вставьте URL-адрес конечной точки. Удалите из URL-адреса *&clientID=default*. Указав конечную точку при подписке, служба "Сетка событий Azure" обрабатывает маршрутизацию событий для этой конечной точки. 

```cli
amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $amsResourceId \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

Значение идентификатора ресурса учетной записи Служб мультимедиа выглядит примерно так:

/subscriptions/81212121-2f4f-4b5d-a3dc-ba0015515f7b/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amstestaccount

## <a name="test-the-events"></a>Проверка событий

Выполните задание кодирования. Например, как описано в кратком руководстве по [потоковой передаче видеофайлов](stream-files-dotnet-quickstart.md).

Кроме того, вы активировали событие, а служба "Сетка событий" отправила сообщение в конечную точку, настроенную вами при подписке. Перейдите к веб-перехватчику, созданному ранее. Щелкните **Мониторинг** и **Refresh** (Обновить). Отобразятся события изменения состояния задания: "В очереди", "Запланировано", "Идет обработка", "Завершено", "Ошибка", Canceled (Отменено), "Отмена".  Дополнительные сведения см. в статье [Схемы службы "Сетка событий Azure" для событий Служб мультимедиа](media-services-event-schemas.md).

Например: 

```json
[{
  "topic": "/subscriptions/<subscription id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job id>",
  "eventType": "Microsoft.Media.JobStateChange",
  "eventTime": "2018-04-20T21:17:26.2534881",
  "id": "<id>",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

![Проверка событий](./media/job-state-events-cli-how-to/test_events.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете продолжать работу с этой учетной записью хранения и подпиской на события, не очищайте ресурсы, созданные при работе с этой статьей. Если вы не планируете продолжать работу, удалите все созданные ресурсы в этой статье.

Замените `<resource_group_name>` именем группы ресурсов, созданной ранее.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Дополнительная информация

[Реагирование на события Служб мультимедиа Azure](reacting-to-media-services-events.md)
