<properties
 pageTitle="Руководство разработчика. Язык запросов | Microsoft Azure"
 description="Руководство разработчика Центра Интернета вещей Azure. Описание языка запросов, используемого для получения сведений о двойниках, методах и заданиях из Центра Интернета вещей"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="elioda"/>


# <a name="reference-query-language-for-twins-and-jobs"></a>Справочник по языку запросов для двойников и заданий

## <a name="overview"></a>Обзор

Центр Интернета вещей предоставляет мощный язык на основе SQL для получения сведений о [двойниках устройств][lnk-twins] и [заданиях][lnk-jobs]. В этой статье представлены:

* общие сведения об основных возможностях языка запросов Центра Интернета вещей;
* подробное описание языка.

## <a name="getting-started-with-twin-queries"></a>Начало работы с запросами двойников

[Двойники устройств][lnk-twins] могут содержать произвольные объекты JSON в качестве тегов и свойств. Центр Интернета вещей позволяет выполнять запросы к двойникам устройств как к одному документу JSON, содержащему все сведения об устройствах.
Например, предположим, что двойники Центра Интернета вещей имеют следующую структуру:

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                "location": {                                                      
                    "region": "US",                                                  
                    "plant": "Redmond43"                                             
                }                                                                  
            },                                                                   
            "properties": {                                                      
                "desired": {                                                       
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300                                          
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                     
                    },                                                               
                    "$version": 4                                                    
                },                                                                 
                "reported": {                                                      
                    "connectivity": {                                                
                        "type": "cellular"                            
                    },                                                               
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300,                                         
                        "status": "Success"                                            
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                
                    },                                                               
                    "$version": 7                                                    
                }                                                                  
            }                                                                    
        }

Центр Интернета вещей предоставляет двойники устройства как коллекцию документов с именем **devices**.
Следующий запрос получает весь набор двойников устройства:

        SELECT * FROM devices

> [AZURE.NOTE] [Пакеты SDK Центра Интернета вещей][lnk-hub-sdks] поддерживают разбивку на страницы объемных результатов.

Центр Интернета вещей позволяет получить двойники, отфильтрованные по произвольным условиям. Например,

        SELECT * FROM devices
        WHERE tags.location.region = 'US'

извлекает двойники, для тега **location.region** которых задано значение **US**.
Кроме того, поддерживаются логические операторы и арифметические сравнения. Например,

        SELECT * FROM devices
        WHERE tags.location.region = 'US'
            AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60

извлекает все двойники, расположенные в США, для которых настроена отправка телеметрии реже, чем раз в минуту. Для удобства можно также использовать константы массива с операторами **IN** и **NIN** (не входит). Например,

        SELECT * FROM devices
        WHERE property.reported.connectivity IN ['wired', 'wifi']

извлекает все двойники, сообщившие о подключении по Wi-Fi или проводной сети. Полное описание возможностей фильтрации см. в разделе [Предложение WHERE][lnk-query-where].

Кроме того, поддерживаются группирование и агрегаты. Например,

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

возвращает количество устройств в каждом состоянии конфигурации телеметрии.

        [
            {
                "numberOfDevices": 3,
                "status": "Success"
            },
            {
                "numberOfDevices": 2,
                "status": "Pending"
            },
            {
                "numberOfDevices": 1,
                "status": "Error"
            }
        ]

В приведенном выше примере показана ситуация, когда три устройства сообщили об успешной конфигурации, два все еще применяют конфигурацию и одно сообщило об ошибке.

### <a name="c-example"></a>Пример C#

Функция обработки запросов предоставляется в [пакете SDK для службы C#][lnk-hub-sdks] в классе **RegistryManager**.
Ниже приведен пример простого запроса:

        var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
        while (query.HasMoreResults)
        {
            var page = await query.GetNextAsTwinAsync();
            foreach (var twin in page) 
            {
                // do work on twin object
            }
        }

Обратите внимание, как создается экземпляр объекта **query** с размером страницы (до 1000), а затем можно получить несколько страниц, вызвав метод **GetNextAsTwinAsync** несколько раз.
Важно отметить, что объект query предоставляет несколько вариантов **Next\*** в зависимости от варианта десериализации, требуемого для запроса, то есть объекты двойника или задания, или простой JSON, которые будут применяться при использовании проекций.

### <a name="node-example"></a>Пример узла

Функция обработки запросов предоставляется в [пакете SDK для службы узла][lnk-hub-sdks] в объекте **Registry**.
Ниже приведен пример простого запроса:

        var query = registry.createQuery('SELECT * FROM devices', 100);
        var onResults = function(err, results) {
            if (err) {
                console.error('Failed to fetch the results: ' + err.message);
            } else {
                // Do something with the results
                results.forEach(function(twin) {
                    console.log(twin.deviceId);
                });

                if (query.hasMoreResults) {
                    query.nextAsTwin(onResults);
                }
            }
        };
        query.nextAsTwin(onResults);

Обратите внимание, как создается экземпляр объекта **query** с размером страницы (до 1000), а затем можно получить несколько страниц, вызвав метод **nextAsTwin** несколько раз.
Важно отметить, что объект query предоставляет несколько вариантов **next\*** в зависимости от варианта десериализации, требуемого для запроса, то есть объекты двойника или задания, или простой JSON, которые будут применяться при использовании проекций.

### <a name="limitations"></a>Ограничения

В настоящее время проекции поддерживаются только при использовании агрегатов, т. е. запросы без агрегатов могут использовать только `SELECT *`. Кроме того, агрегаты поддерживаются только в сочетании с группированием.

## <a name="getting-started-with-jobs-queries"></a>Начало работы с запросами заданий

[Задания][lnk-jobs] позволяют выполнять операции с наборами устройств. Каждый двойник устройства содержит сведения о заданиях, в которых он участвует, в коллекции с именем **jobs**.
Логически получается следующее:

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                ...                                                              
            },                                                                   
            "properties": {                                                      
                ...                                                                 
            },
            "jobs": [
                { 
                    "deviceId": "myDeviceId",
                    "jobId": "myJobId",    
                    "jobType": "scheduleTwinUpdate",            
                    "status": "completed",                    
                    "startTimeUtc": "2016-09-29T18:18:52.7418462",
                    "endTimeUtc": "2016-09-29T18:20:52.7418462",
                    "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
                    "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
                    "outcome": {
                        "deviceMethodResponse": null   
                    }                                         
                },
                ...
            ]                                                             
        }

В настоящее время к этой коллекции можно выполнить запрос как к **devices.jobs** на языке запросов Центра Интернета вещей.

Например, чтобы получить все задания (выполненные и запланированные), влияющие на одно устройство, можно использовать следующий запрос:

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'

Обратите внимание, как этот запрос предоставляет сведения о состоянии конкретного устройства (и, возможно, ответ на прямой метод) в каждом возвращенном задании.
Все свойства объектов в коллекции **devices.jobs** можно также отфильтровать с помощью произвольных логических условий.
Например, следующий запрос:

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'
            AND devices.jobs.jobType = 'scheduleTwinUpdate'
            AND devices.jobs.status = 'completed'
            AND devices.jobs.createdTimeUtc > '2016-09-01'

получает список всех завершенных заданий обновления двойников для устройства **myDeviceId**, созданных после сентября 2016 года.

Кроме того, можно получить результаты выполнения одного задания для каждого устройства.

        SELECT * FROM devices.jobs
        WHERE devices.jobs.jobId = 'myJobId'

### <a name="limitations"></a>Ограничения
В настоящее время запросы к **devices.jobs** не поддерживают следующие элементы:

* проекции, т. е. можно использовать только `SELECT *`;
* условия, касающиеся двойника устройства, и свойства задания, как показано выше;
* агрегаты, например count, avg, group by.

## <a name="basics-of-an-iot-hub-query"></a>Основные сведения о запросе Центра Интернета вещей

Каждый запрос Центра Интернета вещей состоит из предложений SELECT и FROM, а также необязательных предложений WHERE и GROUP BY. Каждый запрос выполняется для коллекции документов JSON, например двойников устройств. Предложение FROM указывает коллекцию документов, по которой будет выполняться итерация (**devices** или **devices.jobs**). Затем применяется фильтр в предложении WHERE. В случае с агрегатами результаты этого шага группируются, как указано в предложении GROUP BY, и для каждой группы создается строка, как указано в предложении SELECT.

        SELECT <select_list>
        FROM <from_specification>
        [WHERE <filter_condition>]
        [GROUP BY <group_specification>]

## <a name="from-clause"></a>Предложение FROM

Предложение **FROM <из_спецификации>** может предоставить только два значения: **FROM devices** для запроса двойников устройства или **FROM devices.jobs** для запроса сведений о задании для каждого устройства.

## <a name="where-clause"></a>Предложение WHERE

Предложение **WHERE <условие_фильтрации>** является необязательным. Оно определяет условия, которым должны соответствовать документы JSON в коллекции FROM, чтобы быть включенными в результат. Любой документ JSON должен при вычислении указанных условий возвращать значение true, чтобы быть включенным в результат.

Допустимые условия описаны в разделе [Выражения и условия][lnk-query-expressions].

## <a name="select-clause"></a>Предложение SELECT

Предложение SELECT (**SELECT <список_для_выбора>**) является обязательным. Оно указывает значения, которые будут получены из запроса. Он задает значения JSON, которые нужно использовать для создания объектов JSON. На этапе проекции для каждого элемента отфильтрованного (и при необходимости сгруппированного) подмножества коллекции FROM создается объект JSON, собранный из значений, которые указаны в предложении SELECT.

Ниже указана грамматика предложения SELECT:

        SELECT [TOP <max number>] <projection list>

        <projection_list> ::=
            '*'
            | <projection_element> AS alias [, <projection_element> AS alias]+

        <projection_element> :==
            attribute_name
            | <projection_element> '.' attribute_name
            | <aggregate>

        <aggregate> :==
            count(<projection_element>) | count()
            | avg(<projection_element>) | avg()
            | sum(<projection_element>) | sum()
            | min(<projection_element>) | min()
            | max(<projection_element>) | max()

где **attribute_name** относится к любому свойству документа JSON в коллекции FROM. Некоторые примеры предложения SELECT можно найти в статье [Начало работы с запросами двойников][lnk-query-getstarted].

В настоящее время предложения для осуществления выбора, отличные от **SELECT \***, поддерживаются только в статистических запросах к двойникам.

## <a name="group-by-clause"></a>Предложение GROUP BY

Предложение **GROUP BY <спецификация_группирования>** является необязательным и выполняется после фильтра, указанного в предложении WHERE, и перед проекцией, указанной в предложении SELECT. Оно группирует документы на основе значения атрибута. Эти группы используются для создания статистических значений, как указано в предложении SELECT.

Ниже представлен пример запроса с использованием предложения GROUP BY:

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

Далее указан формальный синтаксис предложения GROUP BY:

        GROUP BY <group_by_element>
        <group_by_element> :==
            attribute_name
            | < group_by_element > '.' attribute_name

где **attribute_name** относится к любому свойству документа JSON в коллекции FROM. 

В настоящее время предложение GROUP BY поддерживается только при запросе двойников.

## <a name="expressions-and-conditions"></a>Выражения и условия

В общем *выражение*:

* возвращает экземпляр типа JSON (т. е. логическое значение, число, строка, массив или объект);
* определяется обработкой данных, поступающих из документа JSON устройства, и констант с помощью встроенных операторов и функций.

*Условие* — это выражение, возвращающее логическое значение, т. е. все константы, значения которых отличаются от логического **true**, считаются такими, которые имеют значение **false** (включая **null**, **undefined**, любой экземпляр объекта или массива, любую строку, а также логическое значение **false**).

Выражения имеют следующий синтаксис:

        <expression> ::=
            <constant> |
            attribute_name |
            unary_operator <expression> |
            <expression> binary_operator <expression> |
            <create_array_expression> |
            '(' <expression> ')'

        <constant> ::=
            <undefined_constant>
            | <null_constant> 
            | <number_constant> 
            | <string_constant> 
            | <array_constant> 

        <undefined_constant> ::= undefined
        <null_constant> ::= null
        <number_constant> ::= decimal_literal | hexadecimal_literal
        <string_constant> ::= string_literal
        <array_constant> ::= '[' <constant> [, <constant>]+ ']'

Описание:

| Знак | Определение |
| -------------- | -----------------|
| attribute_name | Любое свойство документа JSON в коллекции FROM. |
| unary_operator | Унарный оператор, как описано в разделе "Операторы". |
| binary_operator | Бинарный оператор, как описано в разделе "Операторы". |
| decimal_literal | Число с плавающей запятой в десятичном представлении. |
| hexadecimal_literal | Число, представленное строкой 0x, за которой следует строка с шестнадцатеричными цифрами. |
| string_literal | Строковые литералы — это строки Юникода, представленные в виде последовательности из нуля или более знаков Юникода или escape-последовательностей. Строковые литералы заключаются в одинарные кавычки (апостроф — ') или двойные кавычки (кавычки — "). В знаках Юникода, определяемых 4 шестнадцатеричными цифрами, разрешено использовать escape-символы `\'`, `\"`, `\\` и `\uXXXX`. |

### <a name="operators"></a>Операторы

Поддерживаются следующие операторы:

| Семейство | Операторы |
| -------------- | -----------------|
| Арифметические | +,-,*,/,% |
| Логические | AND, OR, NOT |
| Сравнение |  =, !=, <, >, <=, >=, <> |

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как выполнять запросы в своих приложениях с помощью [пакетов SDK для Центра Интернета вещей][lnk-hub-sdks].

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#getting-started-with-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md


<!---HONumber=Oct16_HO2-->


