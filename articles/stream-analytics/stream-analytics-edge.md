---
title: Azure Stream Analytics в IoT Edge (предварительная версия)
description: Создание заданий Edge в Azure Stream Analytics и их развертывание на устройствах, запущенных в Azure IoT Edge.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/16/2017
ms.openlocfilehash: 9a9608825cf041007c000729becb34e9a3063f92
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-stream-analytics-on-iot-edge-preview"></a>Azure Stream Analytics в IoT Edge (предварительная версия)

> [!IMPORTANT]
> Эта функция доступна в режиме предварительной версии. Мы не рекомендуем использовать ее в рабочей среде.
 
Azure Stream Analytics (ASA) в IoT Edge помогает разработчикам развертывать решения аналитики ближе к устройствам Интернета вещей практически в реальном времени, что позволяет максимально эффективно использовать данные, полученные от устройств. Предприятия, которым требуется минимальная задержка, отказоустойчивость, эффективное использование пропускной способности и соответствие требованиям, теперь могут развертывать логику управления вблизи от промышленных объектов и дополнять выполняющийся в облаке анализ больших данных.  
Azure Stream Analytics в IoT Edge работает на платформе [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). Когда задание создано в ASA, вы можете развертывать задания ASA и управлять ими с помощью Центра Интернета вещей.
Эта функция предоставляется в предварительной версии. Если у вас возникли вопросы или вы хотите оставить отзыв, воспользуйтесь этим [опросом](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u), чтобы связаться с группой разработчиков. 

## <a name="scenarios"></a>Сценарии
![Общая схема](media/stream-analytics-edge/ASAedge_highlevel.png)

* **Инструменты контроля и управления с низкой задержкой.** Например, производственные системы безопасности должны реагировать на операционные данные со сверхнизкой задержкой. Используя ASA в IoT Edge, вы можете анализировать данные датчиков практически в реальном времени, а также при обнаружении аномалий отдавать команды на остановку машины или активацию предупреждений.
*   **Ограниченные возможности подключения к облаку.** Критически важные системы, такие как оборудование для удаленного интеллектуального анализа данных, подключенные резервуары или оборудование для детализации данных внешней разработки, должны анализировать данные и реагировать на них даже в том случае, если подключение к облаку периодически прерывается. С ASA логика потоковой передачи выполняется независимо от сетевого подключения, и вы можете выбирать, какие именно данные необходимо отправить в облако для дальнейшей обработки и хранения.
* **Ограниченная пропускная способность.** Объем данных, создаваемых с помощью реактивных двигателей или подключенных автомобилей, может быть настолько большим, что данные необходимо отфильтровать или предварительно обработать, прежде чем отправить их в облако. Используя ASA, можно отфильтровать или статистически обработать данные, которые необходимо передать в облако.
* **Соответствие.** Для обеспечения соответствия нормативным требованиям может потребоваться локально анонимизировать или статистически обработать некоторые данные перед их отправкой в облако.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Задания Edge в службе Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>Что такое задание "Edge"?

Задания ASA Edge выполняются как модули в [среде выполнения Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). Они состоят из двух частей:
1.  Облачная часть, которая отвечает за определение задания: пользователи определяют входы и выходы, запросы и другие параметры (неупорядоченные события и т. д.) в облаке.
2.  ASA в модуле IoT Edge, который работает локально. Она содержит механизм обработки сложных событий ASA и получает определение задания из облака. 

ASA развертывает задания Edge на устройства с помощью Центра Интернета вещей. Дополнительные сведения о развертывании IoT Edge [см. здесь](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Задание Edge](media/stream-analytics-edge/ASAedge_job.png)


### <a name="installation-instructions"></a>Инструкции по установке
В следующей таблице приведены основные действия. Дополнительные сведения см. в следующих разделах.
|      |Шаг   | Место     | Заметки   |
| ---   | ---   | ---       |  ---      |
| 1   | **Создание задания Edge ASA**   | Портал Azure      |  Создайте новое задание, выберите **Edge** в качестве **среды размещения**. <br> Создание этих заданий и управление ими осуществляется из облака, а выполняются они на ваших устройствах IoT Edge.     |
| 2   | **Создание контейнера хранилища**   | Портал Azure       | Контейнеры хранилища используются для сохранения определения задания, доступ к которому можно получить с устройств IoT. <br>  Любой имеющийся контейнер хранилища можно использовать повторно.     |
| 3   | **Настройка среды IoT Edge на ваших устройствах**   | Устройства      | Инструкции для [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) или [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4.   | **Развертывание ASA на ваших устройствах IoT Edge**   | Портал Azure      |  Определение задания ASA экспортируется в контейнер хранилища, созданный ранее.       |
Вы можете выполнить инструкции из [этого пошагового руководства](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics), чтобы развернуть свое первое задание ASA в IoT Edge. Следующее видео поможет вам понять процесс выполнения задания Stream Analytics на устройстве IoT Edge:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]



#### <a name="create-an-asa-edge-job"></a>Создание задания Edge ASA
> [!Note]
> Это руководство посвящено созданию задания ASA с помощью портала Azure. Кроме того, [чтобы создать задание ASA Edge, вы можете использовать подключаемый модуль Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs).

1. На портале Azure создайте "задание Stream Analytics". [Прямая ссылка для создания задания ASA](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. В окне создания выберите **Edge** в качестве **среды размещения** (см. следующий рисунок). ![Создание задания](media/stream-analytics-edge/ASAEdge_create.png)
3. Определение задания
    1. **Определите входные потоки**. Определите один или несколько входных потоков для своего задания.
    2. Определите эталонные данные (необязательно).
    3. **Определите потоки вывода**. Определите один или несколько потоков вывода для своего задания. 
    4. **Определите запрос**. Определите запрос ASA в облаке с использованием встроенного редактора. Компилятор автоматически проверяет синтаксис, включенный для ASA Edge. Запрос также можно протестировать путем передачи образцов данных. 
4. Указание необязательных параметров
    1. **Упорядочивание событий**. Неупорядоченную политику можно настроить на портале. Документация доступна [здесь](https://msdn.microsoft.com/library/azure/mt674682.aspx?f=255&MSPPError=-2147217396).
    2. **Языковой стандарт**. Задайте формат интернализации.


#### <a name="create-a-storage-container"></a>Создание контейнера хранилища
Контейнер хранилища необходим для экспорта скомпилированных запросов и конфигурации задания ASA. Он используется для настройки образа ASA Docker с определенным запросом. 
1. Следуйте [этим инструкциям](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) по созданию учетной записи хранения на портале Azure. Вы можете сохранить все параметры по умолчанию, чтобы использовать эту учетную запись с ASA.
2. В созданной учетной записи хранения создайте контейнер хранилища BLOB-объектов:
    1. Щелкните "Большие двоичные объекты", а затем — "+ Контейнер". 
    2. Введите имя и сохраните контейнер как "Частный".


> [!Note]
> При создании развертывания ASA экспортирует определение задания в контейнер хранилища. Это определение задания не изменяется на протяжении развертывания. Как следствие, если вы хотите обновить задание, выполняющееся в Edge, необходимо изменить задание в ASA, а затем создать развертывание в Центре Интернета вещей.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Настройка среды IoT Edge на ваших устройствах
Задания Edge можно развернуть на устройствах, работающих с Azure IoT Edge.
Для этого необходимо сделать следующее:
- создайте Центр Интернета вещей;
- установите среду выполнения IoT Edge и Docker на устройствах Edge;
- задайте свои устройства как "Устройства IoT Edge" в Центре Интернета вещей.

Эти действия описаны в документации по IoT Edge для [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) и [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Развертывание ASA на ваших устройствах IoT Edge
##### <a name="add-asa-to-your-deployment"></a>Добавление развертывания ASA
- На портале Azure откройте Центр Интернета вещей, перейдите в обозреватель IoT Edge и откройте колонку вашего устройства.
- Щелкните **Set modules** (Задать модули), а затем — **Import Azure Service IoT Edge Module** (Импорт модуля IoT Edge службы Azure).
- Выберите подписку и созданное задание ASA Edge. Затем выберите свою учетную запись хранения. Щелкните Сохранить.
![Добавление модуля ASA в развертывание](media/stream-analytics-edge/set_module.png)


> [!Note]
> При выполнении этого шага ASA запрашивает доступ к выбранному контейнеру хранилища, а затем создает папку с именем "EdgeJobs". Для каждого развертывания в папке "EdgeJobs" создается новая вложенная папка.
> Чтобы развернуть задание на устройствах Edge, ASA создает подписанный URL-адрес (SAS) для файла определения задания. Ключ SAS безопасно передается на устройства IoT Edge с помощью двойника устройства. Срок действия этого ключа составляет три года после даты создания.


Дополнительные сведения о развертываниях IoT Edge см. на [этой странице](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Настройка маршрутов
IoT Edge предоставляет способ декларативно маршрутизировать сообщения между модулями, а также между модулями и Центром Интернета вещей. Полный синтаксис описан [здесь](https://docs.microsoft.com/azure/iot-edge/module-composition).
Имена входов и выходов, сформированных в задании ASA, можно использовать в качестве конечных точек для маршрутизации.  

###### <a name="example"></a>Пример
```
{
"routes": {                                              
    "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
    "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream", 
    "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")" 
}
}   

```
В этом примере показаны маршруты для сценария, описанного на рисунке ниже. Он содержит задание Edge с именем **ASA**, ввод с именем **temperature** и вывод с именем **alert**.
![Пример маршрутизации](media/stream-analytics-edge/RoutingExample.png)

В этом примере определяются следующие маршруты:
- Каждое сообщение из **tempSensor** отправляется в модуль с именем **ASA** и ввод с именем **temperature**.
- Все выходные данные модуля **ASA** отправляются в Центр Интернета вещей, связанный с этим устройством ($upstream).
- Все выходные данные модуля **ASA** отправляются в конечную точку **control** **tempSensor**.


## <a name="technical-information"></a>Технические сведения
### <a name="current-limitations-for-edge-jobs-compared-to-cloud-jobs"></a>Текущие ограничения заданий Edge в сравнении с облачными заданиями
Целью является обеспечение соответствия между заданиями Edge и облачными заданиями. Большинство функций языка запросов SQL уже поддерживается.
Тем не менее для заданий Edge пока не поддерживаются следующие компоненты:
* Определяемые пользователем функции (UDF) и определяемые пользователем статистические функции (UDA).
* Функции машинного обучения Azure.
* Использование более 14 статических выражений за один шаг.
* Формат AVRO для входных и выходных данных. На данный момент поддерживаются только форматы CSV и JSON.
* Следующие операторы SQL:
    * AnomalyDetection;
    * геопространственные операторы:
        * CreatePoint;
        * CreatePolygon;
        * CreateLineString;
        * ST_DISTANCE;
        * ST_WITHIN;
        * ST_OVERLAPS;
        * ST_INTERSECTS.
    * PARTITION BY;
    * GetMetadataPropertyValue.


### <a name="runtime-and-hardware-requirements"></a>Требования к оборудованию и среде выполнения
Для запуска ASA в IoT Edge вам потребуются устройства, которые могут работать в [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

В ASA и Azure IoT Edge используются контейнеры **Docker** для предоставления переносимого решения, которое выполняется на нескольких ОС узла (Windows, Linux).

ASA в IoT Edge доступна в качестве образов Windows и Linux, работающих под управлением архитектуры x86–64 или ARM. 


### <a name="input-and-output"></a>Входные и выходные данные
#### <a name="input-and-output-streams"></a>Входные потоки и потоки вывода
Задания ASA Edge могут получать входные и выходные данные из других модулей, запущенных на устройствах IoT Edge. Чтобы подключиться к конкретным модулям и из них, можно задать конфигурацию маршрутизации во время развертывания. Дополнительные сведения см. в [документации по композиции модуля IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition).

Для входных и выходных данных поддерживаются форматы CSV и JSON.

Для каждого входного потока и потока вывода, создаваемого в задании ASA, в развернутом модуле создается соответствующая конечная точка. Эти конечные точки можно использовать в маршрутах развертывания.



##### <a name="reference-data"></a>Ссылочные данные
Эталонные данные (которые также называются таблицами подстановки) — это ограниченный набор данных, являющийся по своей сути статическим или медленно изменяющимся. Он используется для уточняющего запроса или для согласования с потоком данных. Для использования ссылочных данных в задании Azure Stream Analytics обычно используется [соединение ссылочных данных](https://msdn.microsoft.com/library/azure/dn949258.aspx) в запросе. Дополнительные сведения см. в [документации ASA по эталонным данным](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data).

Чтобы использовать эталонные данные для ASA в IoT Edge, необходимо сделать следующее: 
1. Создайте входные данные для своего задания.
2. Выберите значение **Эталонные данные** для поля **Тип источника**.
3. Задайте путь к файлу. Путь к файлу должен быть **абсолютным** путем к файлу на устройстве. ![Создание эталонных данных](media/stream-analytics-edge/ReferenceData.png)
4. Включите **Shared Drives** (Общие диски) в конфигурации Docker и убедитесь, что диск включен, перед началом развертывания.

Кроме того, можно ознакомиться с документацией по Docker для Windows [здесь](https://docs.docker.com/docker-for-windows/#shared-drives).

> [!Note]
> В данный момент поддерживаются только локальные эталонные данные.




## <a name="license-and-third-party-notices"></a>Уведомления о лицензии и сторонних производителях
* [Лицензия на Azure Stream Analytics в IoT Edge (предварительная версия)](https://go.microsoft.com/fwlink/?linkid=862827) 
* [Уведомление сторонних производителей для Azure Stream Analytics в IoT Edge (предварительная версия)](https://go.microsoft.com/fwlink/?linkid=862828)

## <a name="get-help"></a>Получение справки
За дополнительной помощью обращайтесь на [форум Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Дополнительная информация

* [Что такое Azure IoT Edge (предварительная версия)](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [Deploy Azure Stream Analytics as an IoT Edge module - preview](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) (Развертывание Azure Stream Analytics в качестве модуля IoT Edge (предварительная версия))
* [Отправьте отзыв команде с помощью этого опроса](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
* [Разработка заданий Edge Stream Analytics с помощью средств Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
