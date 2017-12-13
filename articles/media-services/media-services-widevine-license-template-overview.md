---
title: "Обзор шаблона лицензии Widevine | Документация Майкрософт"
description: "В этом разделе содержится обзор шаблона лицензии Widevine, который используется для настройки лицензий Widevine."
author: juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: 0e6f1f05-7ed6-4ed6-82a0-0cc2182b075a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 68d519cd36d41728f57419cd6cecd2a79d65a4af
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2017
---
# <a name="widevine-license-template-overview"></a>Обзор шаблона лицензии Widevine
Службы мультимедиа Azure позволяют настраивать и запрашивать лицензии Widevine. Когда проигрыватель конечного пользователя пытается воспроизвести содержимое, защищенное с помощью Widevine, в службу доставки лицензий отправляется запрос на получение лицензии. Если служба лицензий утверждает запрос, она выдает лицензию, которая отправляется клиенту и может использоваться для расшифровки и воспроизведения указанного содержимого.

Запрос на лицензию Widevine форматируется как сообщение JSON.  

>[!NOTE]
> Вы можете создать пустое сообщение без значений (просто "{}"), и шаблон лицензии будет создан со всеми значениями по умолчанию. Значения по умолчанию подходят для большинства случаев. Например, для сценариев доставки лицензий на основе MS всегда используются значения по умолчанию. Если вам все-таки необходимо изменить значения параметров "provider" (поставщик) и "content_id" (идентификатор содержимого), то поставщик должен совпадать с учетными данными Google Widevine.

    {  
       “payload”:“<license challenge>”,
       “content_id”: “<content id>” 
       “provider”: ”<provider>”
       “allowed_track_types”:“<types>”,
       “content_key_specs”:[  
          {  
             “track_type”:“<track type 1>”
          },
          {  
             “track_type”:“<track type 2>”
          },
          …
       ],
       “policy_overrides”:{  
          “can_play”:<can play>,
          “can persist”:<can persist>,
          “can_renew”:<can renew>,
          “rental_duration_seconds”:<rental duration>,
          “playback_duration_seconds”:<playback duration>,
          “license_duration_seconds”:<license duration>,
          “renewal_recovery_duration_seconds”:<renewal recovery duration>,
          “renewal_server_url”:”<renewal server url>”,
          “renewal_delay_seconds”:<renewal delay>,
          “renewal_retry_interval_seconds”:<renewal retry interval>,
          “renew_with_usage”:<renew with usage>
       }
    }

## <a name="json-message"></a>Сообщение JSON
| Имя | Значение | Описание |
| --- | --- | --- |
| payload |строка в кодировке Base64 |Запрос на лицензию, отправленный клиентом. |
| content_id |строка в кодировке Base64 |Идентификатор, используемый для получения ИД ключей и ключей содержимого для каждого content_key_specs.track_type. |
| provider |string |Используется для поиска ключей и политик содержимого. Если для доставки лицензий Widevine используется доставка ключей MS, то этот параметр пропускается. |
| policy_name |string |Имя ранее зарегистрированной политики. Необязательно |
| allowed_track_types |enum |SD_ONLY или SD_HD. Контролирует, какие ключи содержимого должны быть включены в лицензию. |
| content_key_specs |массив структур JSON, см. раздел **Спецификации ключей содержимого** ниже |Более точный контроль возвращаемых ключей содержимого. Более подробные сведения см. ниже в подразделе "Спецификации ключей содержимого".  Можно указать только один allowed_track_types и content_key_specs. |
| use_policy_overrides_exclusively |логическое Значение true или false |Используйте атрибуты политики, указанные в параметре policy_overrides, и пропустите все сохраненные ранее политики. |
| policy_overrides |структура JSON, см. раздел **Переопределения политики** ниже |Параметры политики для этой лицензии.  В случае если этот ресурс имеет предопределенную политику, будут использоваться эти указанные значения. |
| session_init |структура JSON, см. раздел **Инициализация сеанса** ниже |Необязательные данные, передаваемые в лицензию. |
| parse_only |логическое Значение true или false |Запрос на лицензию проанализирован, но лицензия не выдана. Однако в ответе возвращаются значения, формирующие запрос на лицензию. |

## <a name="content-key-specs"></a>Спецификации ключей содержимого
Если имеющаяся политика существует, указывать какие-либо значения в спецификации ключа содержимого не требуется.  Существующая политика, связанная с этим содержимым, будет использоваться для определения выходной защиты, например HDCP и CGMS.  Если существующая политика не зарегистрирована на сервере лицензирования Widevine, поставщик содержимого может внедрить значения в запрос на лицензию.   

Каждый параметр content_key_specs должен быть указан для всех записей независимо от параметра use_policy_overrides_exclusively. 

| Имя | Значение | Описание |
| --- | --- | --- |
| content_key_specs. track_type |string |Имя типа записи. Если content_key_specs указан в запросе лицензии, убедитесь, что все типы записей указаны явным образом. Невыполнение этого требования приведет к сбою воспроизведения последних 10 секунд. |
| content_key_specs  <br/> security_level |uint32 |Определяет требования к надежности клиента для воспроизведения. <br/> 1. Требуется программное шифрование методом белого ящика. <br/> 2. Требуется шифрование ПО и скрытый декодер. <br/> 3. Материал ключа и операции шифрования должны быть выполнены в резервной доверенной среде выполнения оборудования. <br/> 4. Операции шифрования и расшифровки содержимого должны быть выполнены в резервной доверенной среде выполнения оборудования.  <br/> 5. Шифрование, расшифровка и обработка всех носителей (сжатых и несжатых) должны быть выполнены в резервной доверенной среде выполнения оборудования. |
| content_key_specs <br/> required_output_protection.hdc |строка — одна из: HDCP_NONE, HDCP_V1, HDCP_V2 |Указывает, требуется ли HDCP. |
| content_key_specs <br/>key |строка в кодировке  <br/>Base64 |Ключ содержимого для этой записи. Если указано, требуется track_type или key_id.  Этот параметр позволяет поставщикам содержимого внедрить ключ содержимого для этой записи вместо того, чтобы сервер лицензирования Widevine создал или нашел ключ. |
| content_key_specs.key_ID |Двоичные данные строки в кодировке Base64, 16 байт |Уникальный идентификатор ключа. |

## <a name="policy-overrides"></a>Переопределения политики
| Имя | Значение | Описание |
| --- | --- | --- |
| policy_overrides. can_play |логическое Значение true или false |Указывает, допускается ли воспроизведение содержимого. Значение по умолчанию — false. |
| policy_overrides. can_persist |логическое Значение true или false |Указывает, что лицензия может быть сохранена для долговременного хранения в целях автономного использования. Значение по умолчанию — false. |
| policy_overrides. can_renew |логическое значение: true или false |Указывает, что разрешено продление лицензии. Если указано значение "true", лицензию можно расширить с помощью периодического сигнала. Значение по умолчанию — false. |
| policy_overrides. license_duration_seconds |int64 |Указывает интервал времени для данной конкретной лицензии. Значение 0 указывает на неограниченное время. Значение по умолчанию — 0. |
| policy_overrides. rental_duration_seconds |int64 |Указывает интервал времени, в течение которого разрешено воспроизведение. Значение 0 указывает на неограниченное время. Значение по умолчанию — 0. |
| policy_overrides. playback_duration_seconds |int64 |Окно просмотра времени после начала воспроизведения в течение срока действия лицензии. Значение 0 указывает на неограниченное время. Значение по умолчанию — 0. |
| policy_overrides. renewal_server_url |string |Все запросы на продление для этой лицензии должны направляться на указанный URL-адрес. Это поле используется только в том случае, если can_renew имеет значение "true". |
| policy_overrides. renewal_delay_seconds |int64 |Количество секунд после license_start_time перед первой попыткой продления. Это поле используется только в том случае, если can_renew имеет значение "true". Значение по умолчанию — 0. |
| policy_overrides. renewal_retry_interval_seconds |int64 |Определяет задержку в секундах между последующими запросами на продление лицензии (в случае сбоя). Это поле используется только в том случае, если can_renew имеет значение "true". |
| policy_overrides. renewal_recovery_duration_seconds |int64 |Интервал времени, в течение которого разрешено продолжение воспроизведения во время попытки продления, если выполнение неудачно из-за внутренних проблем с сервером лицензирования. Значение 0 указывает на неограниченное время. Это поле используется только в том случае, если can_renew имеет значение "true". |
| policy_overrides. renew_with_usage |логическое значение: true или false |Указывает, что в начале использования лицензия должна быть отправлена для продления. Это поле используется только в том случае, если can_renew имеет значение "true". |

## <a name="session-initialization"></a>Инициализация сеанса
| Имя | Значение | Описание |
| --- | --- | --- |
| provider_session_token |строка в кодировке Base64 |Этот маркер сеанса передается обратно в лицензию и будет существовать в последующих продлениях.  Маркер сеанса не сохраняется вне сеансов. |
| provider_client_token |строка в кодировке Base64 |Маркер клиента для отправки обратно в ответе лицензии.  Если запрос лицензии содержит маркер клиента, это значение игнорируется. Маркер клиента будет сохраняться вне сеансов лицензии. |
| override_provider_client_token |логическое Значение true или false |Если задано значение "false" и запрос лицензии содержит маркер клиента, используйте маркер из запроса, даже если в этой структуре был указан маркер клиента.  Если задано значение "true", всегда используйте маркер, заданный в этой структуре. |

## <a name="configure-your-widevine-licenses-using-net-types"></a>Настройка лицензий Widevine с помощью типов .NET
Службы мультимедиа предоставляют API-интерфейсы .NET, которые позволяют настраивать лицензии Widevine. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>Классы, как определено в пакете SDK .NET для служб мультимедиа
Ниже приведены определения этих типов.

    public class WidevineMessage
    {
        public WidevineMessage();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public AllowedTrackTypes? allowed_track_types { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public ContentKeySpecs[] content_key_specs { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public object policy_overrides { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

### <a name="example"></a>Пример
В следующем примере показано, как использовать API-интерфейсы .NET для настройки простой лицензии Widevine.

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }


## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Дополнительные материалы
[Использование общего динамического шифрования PlayReady и (или) Widevine DRM](media-services-protect-with-playready-widevine.md)

