<properties 
	pageTitle="Общие сведения о шаблоне лицензии Widevine" 
	description="В этом разделе содержится обзор шаблона лицензии Widevine, который используется для настройки лицензий Widevine." 
	authors="juliako" 
	manager="erikre" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2016"  
	ms.author="juliako"/>

#Общие сведения о шаблоне лицензии Widevine

##Обзор

Теперь службы мультимедиа Azure позволяют настраивать и запрашивать лицензии Widevine. Когда проигрыватель конечного пользователя пытается воспроизвести содержимое, защищенное с помощью Widevine, в службу доставки лицензий отправляется запрос на получение лицензии. Если служба лицензий утверждает запрос, она выдает лицензию, которая отправляется клиенту и может использоваться для расшифровки и воспроизведения указанного содержимого.

Запрос на лицензию Widevine форматируется как сообщение JSON.

Обратите внимание, что вы можете создать пустое сообщение без значений (просто "{}"), и шаблон лицензии будет создан со всеми значениями по умолчанию.

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

##Сообщение JSON

Имя | Значение | Описание
---|---|---
payload |строка в кодировке Base64 |Запрос на лицензию, отправленный клиентом. 
content\_id | строка в кодировке Base64|Идентификатор, используемый для получения ИД ключей и ключей содержимого для каждого content\_key\_specs.track\_type.
provider |string |Используется для поиска ключей и политик содержимого. обязательный параметр.
policy\_name | string |Имя ранее зарегистрированной политики. Необязательно
allowed\_track\_types | enum | SD\_ONLY или SD\_HD. Контролирует, какие ключи содержимого должны быть включены в лицензию.
content\_key\_specs | массив структур JSON, см. подраздел **Спецификации ключей содержимого** ниже | Более точный контроль возвращаемых ключей содержимого. Более подробные сведения см. ниже в подразделе "Спецификации ключей содержимого". Можно указать только один allowed\_track\_types и content\_key\_specs. 
use\_policy\_overrides\_exclusively | логическое значение: true или false | Используйте атрибуты политики, указанные в параметре policy\_overrides, и пропустите все сохраненные ранее политики.
policy\_overrides | структура JSON, см. подраздел **Переопределения политики** ниже | Параметры политики для этой лицензии. В случае если этот ресурс имеет предопределенную политику, будут использоваться эти указанные значения. 
session\_init | структура JSON, см. подраздел **Инициализация сеанса** ниже | Необязательные данные, передаваемые в лицензию.
parse\_only | логическое значение: true или false | Запрос на лицензию проанализирован, но лицензия не выдана. Однако в ответе возвращаются значения, формирующие запрос на лицензию.  

##Спецификации ключей содержимого 

Если имеющаяся политика существует, указывать какие-либо значения в спецификации ключа содержимого не требуется. Существующая политика, связанная с этим содержимым, будет использоваться для определения выходной защиты, например HDCP и CGMS. Если существующая политика не зарегистрирована на сервере лицензирования Widevine, поставщик содержимого может внедрить значения в запрос на лицензию.


Каждый параметр content\_key\_specs должен быть указан для всех записей независимо от параметра use\_policy\_overrides\_exclusively.


Имя | Значение | Описание
---|---|---
content\_key\_specs. track\_type | string | Имя типа записи. Если content\_key\_specs указан в запросе лицензии, убедитесь, что все типы записей указаны явным образом. Невыполнение этого требования приведет к сбою воспроизведения последних 10 секунд. 
content\_key\_specs <br/> security\_level | uint32 | Определяет требования к надежности клиента для воспроизведения. <br/> 1. Требуется программное шифрование методом белого ящика. <br/> 2. Требуется шифрование ПО и скрытый декодер. <br/> 3. Материал ключа и операции шифрования должны быть выполнены в резервной доверенной среде выполнения оборудования. <br/> 4. Операции шифрования и расшифровки содержимого должны быть выполнены в резервной доверенной среде выполнения оборудования. <br/> 5. Шифрование, расшифровка и обработка всех носителей (сжатых и несжатых) должна быть выполнена в резервной доверенной среде выполнения оборудования.  
content\_key\_specs <br/> required\_output\_protection.hdc | строка — одна из: HDCP\_NONE, HDCP\_V1, HDCP\_V2 | Указывает, требуется ли HDCP.
content\_key\_specs <br/>key | строка в кодировке <br/>Base64|Ключ содержимого для этой записи. Если указано, требуется track\_type или key\_id. Этот параметр позволяет поставщикам содержимого внедрить ключ содержимого для этой записи вместо того, чтобы сервер лицензирования Widevine создал или нашел ключ.
content\_key\_specs.key\_ID| Base64 encoded string binary, 16 bytes | Уникальный идентификатор ключа. 


##Переопределения политики 

Имя | Значение | Описание
---|---|---
policy\_overrides. can\_play | логическое значение: true или false | Указывает, допускается ли воспроизведение содержимого. Значение по умолчанию — false.
policy\_overrides. can\_persist | логическое значение: true или false |Указывает, что лицензия может быть сохранена для долговременного хранения в целях автономного использования. Значение по умолчанию — false.
policy\_overrides. can\_renew | логическое значение: true или false |Указывает, что разрешено продление лицензии. Если указано значение "true", лицензию можно расширить с помощью периодического сигнала. Значение по умолчанию — false. 
policy\_overrides. license\_duration\_seconds | int64 | Указывает интервал времени для данной конкретной лицензии. Значение 0 указывает на неограниченное время. Значение по умолчанию — 0. 
policy\_overrides. rental\_duration\_seconds | int64 | Указывает интервал времени, в течение которого разрешено воспроизведение. Значение 0 указывает на неограниченное время. Значение по умолчанию — 0. 
policy\_overrides. playback\_duration\_seconds | int64 | Окно просмотра времени после начала воспроизведения в течение срока действия лицензии. Значение 0 указывает на неограниченное время. Значение по умолчанию — 0. 
policy\_overrides. renewal\_server\_url |string | Все запросы на продление для этой лицензии должны направляться на указанный URL-адрес. Это поле используется только в том случае, если can\_renew имеет значение "true".
policy\_overrides. renewal\_delay\_seconds |int64 |Количество секунд после license\_start\_time перед первой попыткой продления. Это поле используется только в том случае, если can\_renew имеет значение "true". Значение по умолчанию — 0. 
policy\_overrides. renewal\_retry\_interval\_seconds | int64 | Определяет задержку в секундах между последующими запросами на продление лицензии (в случае сбоя). Это поле используется только в том случае, если can\_renew имеет значение "true". 
policy\_overrides. renewal\_recovery\_duration\_seconds | int64 | Интервал времени, в течение которого разрешено продолжение воспроизведения во время попытки продления, если выполнение неудачно из-за внутренних проблем с сервером лицензирования. Значение 0 указывает на неограниченное время. Это поле используется только в том случае, если can\_renew имеет значение "true".
policy\_overrides. renew\_with\_usage | логическое значение: true или false |Указывает, что в начале использования лицензия должна быть отправлена для продления. Это поле используется только в том случае, если can\_renew имеет значение "true". 

##Инициализация сеанса

Имя | Значение | Описание
---|---|---
provider\_session\_token | строка в кодировке Base64 |Этот маркер сеанса передается обратно в лицензию и будет существовать в последующих продлениях. Маркер сеанса не сохраняется вне сеансов. 
provider\_client\_token | строка в кодировке Base64 | Маркер клиента для отправки обратно в ответе лицензии. Если запрос лицензии содержит маркер клиента, это значение игнорируется. Маркер клиента будет сохраняться вне сеансов лицензии.
override\_provider\_client\_token | логическое значение: true или false |Если задано значение "false" и запрос лицензии содержит маркер клиента, используйте маркер из запроса, даже если в этой структуре был указан маркер клиента. Если задано значение "true", всегда используйте маркер, заданный в этой структуре.

##Настройка лицензий Widevine с помощью типов .NET

Службы мультимедиа предоставляют API-интерфейсы .NET, которые позволяют настраивать лицензии Widevine.

###Классы, как определено в пакете SDK .NET для служб мультимедиа

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

###Пример

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


##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##См. также

[Использование общего динамического шифрования PlayReady и (или) Widevine DRM](media-services-protect-with-drm.md)

<!---HONumber=AcomDC_0629_2016-->