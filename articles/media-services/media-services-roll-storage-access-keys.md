---
title: Обновление служб мультимедиа после оборота ключей доступа к хранилищу | Microsoft Docs
description: В этой статье содержится руководство по обновлению служб мультимедиа после оборота ключей доступа к хранилищу.
services: media-services
documentationcenter: ''
author: Juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: milangada;cenkdin;juliako

---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Обновление служб мультимедиа после оборота ключей доступа к хранилищу
При создании новой учетной записи служб мультимедиа Azure пользователю предлагается выбрать учетную запись хранилища Azure, которое используется для хранения мультимедийного содержимого. Обратите внимание, что к учетной записи служб мультимедиа можно добавить [несколько учетных записей хранения](meda-services-managing-multiple-storage-accounts.md) .

При создании новой учетной записи хранения служба Azure создает два 512-разрядных ключа доступа к хранилищу, используемые для проверки подлинности во время доступа к вашей учетной записи хранения. Для безопасности подключений к хранилищу рекомендуется периодически повторно создавать и менять ключи доступа к хранилищу. Два ключа доступа (основной и дополнительный) дают возможность поддерживать подключение к учетной записи хранения с помощью одного ключа доступа в то время, как выполняется повторное создание второго ключа доступа. Этот процесс называется "оборот ключей доступа".

Службы мультимедиа зависят от предоставленного ключа к хранилищу данных. В частности, от ключа доступа к хранилищу зависят указатели, используемые для потоковой передачи или скачивания ресурсов-контейнеров. При создании учетная запись AMS по умолчанию зависит от ключа доступа к основному хранилищу, но пользователь может обновить ключ хранилища в AMS. Необходимо указать службам мультимедиа, какой ключ использовать, выполнив шаги, описанные в данном разделе. Кроме того, при обороте ключей доступа к хранилищу необходимо обновить указатели, чтобы в процессе потоковой передачи не было прерывания (это также описано в разделе).

> [!NOTE]
> Если используется несколько учетных записей хранения, необходимо выполнить эту процедуру для каждой из них.
> 
> Перед выполнением описанных в этом разделе действий с рабочей учетной записью попробуйте выполнить их на подготовительной учетной записи.
> 
> 

## <a name="step-1:-regenerate-secondary-storage-access-key"></a>Шаг 1. Повторное создание дополнительного ключа доступа к хранилищу
Начните с повторного создания дополнительно ключа хранилища. По умолчанию дополнительный ключ не используется службами мультимедиа.  Информацию о последовательной смене ключей к хранилищу данных см. в [статье о просмотре, копировании и повторном создании ключей доступа к хранилищу](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

## <a name="<a-id="step2"></a>step-2:-update-media-services-to-use-the-new-secondary-storage-key"></a><a id="step2"></a>Шаг 2. Обновление служб мультимедиа для использования нового дополнительного ключа к хранилищу данных
Обновите службы мультимедиа для использования дополнительного ключа доступа к хранилищу. Для синхронизации повторно созданного ключа хранилища со службами мультимедиа можно использовать один из следующих двух способов.

* Используйте портал Azure. Чтобы найти значения имени и ключа, перейдите на портал Azure и выберите свою учетную запись. Справа появится окно "Параметры". В окне "Параметры" выберите элемент "Ключи". В зависимости от того, какой ключ хранилища необходимо синхронизировать со службами мультимедиа, выберите кнопку синхронизации основного или дополнительного ключа. В данном случае используйте дополнительный ключ.
* Используйте API REST управления служб мультимедиа.

В следующем примере кода показано, как создать запрос https://endpoint/*subscriptionId*/services/mediaservices/Accounts/*accountName*/StorageAccounts/*storageAccountName*/Key для синхронизации указанного ключа к хранилищу данных со службами мультимедиа. В данном случае используется значение дополнительного ключа хранилища. Дополнительные сведения см. в статье [How to: Use Media Services Management REST API](http://msdn.microsoft.com/library/azure/dn167656.aspx) (Практическое руководство по использованию REST API для управления службами мультимедиа).

    public void UpdateMediaServicesWithStorageAccountKey(string mediaServicesAccount, string storageAccountName, string storageAccountKey)
    {
        var clientCert = GetCertificate(CertThumbprint);

        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/StorageAccounts/{3}/Key",
        Endpoint, SubscriptionId, mediaServicesAccount, storageAccountName));
        request.Method = "PUT";
        request.ContentType = "application/json; charset=utf-8";
        request.Headers.Add("x-ms-version", "2011-10-01");
        request.Headers.Add("Accept-Encoding: gzip, deflate");
        request.ClientCertificates.Add(clientCert);


        using (var streamWriter = new StreamWriter(request.GetRequestStream()))
        {
            streamWriter.Write("\"");
            streamWriter.Write(storageAccountKey);
            streamWriter.Write("\"");
            streamWriter.Flush();
        }

        using (var response = (HttpWebResponse)request.GetResponse())
        {
            string jsonResponse;
            Stream receiveStream = response.GetResponseStream();
            Encoding encode = Encoding.GetEncoding("utf-8");
            if (receiveStream != null)
            {
                var readStream = new StreamReader(receiveStream, encode);
                jsonResponse = readStream.ReadToEnd();
            }
        }
    }

После этого шага обновите существующие указатели (у которых есть зависимость от предыдущего ключа к хранилищу данных), как показано далее.

> [!NOTE]
> Подождите 30 минут перед выполнением любых действий со службами мультимедиа (например, создание новых указателей), чтобы предотвратить проблемы с ожидающими заданиями.
> 
> 

## <a name="step-3:-update-locators"></a>Шаг 3. Обновление указателей
> [!NOTE]
> При последовательной смене ключей доступа к хранилищу необходимо обновить существующие указатели, чтобы в процессе потоковой передачи не было прерывания.
> 
> 

Подождите по крайней мере 30 минут после синхронизации нового ключа к хранилищу данных с AMS. После этого можно будет повторно создать указатели OnDemand, чтобы они приняли зависимость с новым ключом к хранилищу данных и сохранили существующий URL-адрес.

Обратите внимание, что при обновлении (или повторном создании) указателя SAS URL-адрес всегда меняется.

> [!NOTE]
> Чтобы наверняка сохранить существующие URL-адреса указателей OnDemand, надо удалить существующий указатель и создать новый с таким же идентификатором.
> 
> 

В примере .NET ниже показано, как повторно создать указатель с тем же идентификатором.

private static ILocator RecreateLocator(CloudMediaContext context, ILocator locator) { // Save properties of existing locator.
var asset = locator.Asset; var accessPolicy = locator.AccessPolicy; var locatorId = locator.Id; var startDate = locator.StartTime; var locatorType = locator.Type; var locatorName = locator.Name;

// Delete old locator.
locator.Delete();

if (locator.ExpirationDateTime <= DateTime.UtcNow) { throw new Exception(String.Format( "Cannot recreate locator Id={0} because its locator expiration time is in the past", locator.Id)); }

        // Create new locator using saved properties.
        var newLocator = context.Locators.CreateLocator(
            locatorId,
            locatorType,
            asset,
            accessPolicy,
            startDate,
            locatorName);



        return newLocator;
    }


## <a name="step-5:-regenerate-primary-storage-access-key"></a>Шаг 5. Повторное создание ключа доступа к хранилищу
Создайте основной ключ доступа к хранилищу. Информацию о последовательной смене ключей к хранилищу данных см. в [статье о просмотре, копировании и повторном создании ключей доступа к хранилищу](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

## <a name="step-6:-update-media-services-to-use-the-new-primary-storage-key"></a>Шаг 6. Обновление служб мультимедиа для использования нового основного ключа хранилища
Используйте процедуру [шага 2](media-services-roll-storage-access-keys.md#step2), только в этот раз с учетной записью служб мультимедиа синхронизируйте новый ключ доступа к основному хранилищу.

> [!NOTE]
> Подождите 30 минут перед выполнением любых действий со службами мультимедиа (например, создание новых указателей), чтобы предотвратить проблемы с ожидающими заданиями.
> 
> 

## <a name="step-7:-update-locators"></a>Шаг 7. Обновление указателей
Через 30 минут можно будет повторно создать указатели OnDemand, чтобы они приняли зависимость с новым первичным ключом к хранилищу данных и обслуживали существующий URL-адрес.

Используйте ту же процедуру, что описана на [шаге 3](media-services-roll-storage-access-keys.md#step-3-update-locators).

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Благодарности
Мы выражаем признательность тем, кто помог нам в составлении этого документа, — это Сенк Динджилоглу (Cenk Dingiloglu), Милан Гада (Milan Gada) и Сева Титов.

<!--HONumber=Oct16_HO2-->


