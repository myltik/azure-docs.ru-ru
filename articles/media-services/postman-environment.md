 ---
title: Импорт среды Postman для вызовов REST в службах мультимедиа Azure description: В этой статье приведено определение среды Postman для вызовов REST в службах мультимедиа Azure.
services: media-services documentationcenter: '' author: Juliako manager: cfowler editor: ''

ms.service: media-services ms.workload: media ms.tgt_pltfrm: na ms.devlang: na ms.topic: article ms.date: 01/04/2017 ms.author: juliako

---

# <a name="import-the-postman-environment"></a>Импорт среды Postman 

Эта статья содержит определение переменных среды **Postman**, которые используются в [коллекции Postman](postman-collection.md), содержащий сгруппированные HTTP-запросы для вызова REST API служб мультимедиа. Файлы среды и коллекции используются в учебнике[Configure Postman for Media Services REST API calls](media-rest-apis-with-postman.md) (Настройка Postman для вызовов REST API служб мультимедиа).

```
{
  "id": "2dbce3ce-74c2-2ceb-0461-c4c2323f5b09",
  "name": "AzureMedia",
  "values": [
    {
      "enabled": true,
      "key": "TenantID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AzureADSTSEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "RESTAPIEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientSecret",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AccessToken",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAssetId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAccessPolicyId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "UploadURL",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "MediaFileName",
      "value": "BigBuckBunny.mp4",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastChannelId",
      "value": "",
      "type": "text"
    }
  ],
  "_postman_variable_scope": "environment",
  "_postman_exported_using": "Postman/5.5.0"
}
```
