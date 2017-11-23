---
title: "Хранилище данных SQL Azure. Использование службы \"Функции Azure\" для автоматизации управления уровнями вычислений в хранилище данных SQL | Документация Майкрософт"
description: "Инструкции по использованию службы \"Функции Azure\" для управления вычислениями в хранилище данных."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: barbkess
ms.assetid: 52DFC191-E094-4B04-893F-B64D5828A901
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: quickstart
ms.date: 11/06/2017
ms.author: elbutter
ms.openlocfilehash: e2138f26a78338406b466bdd2aa6e756e602726d
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2017
---
# <a name="use-azure-functions-to-automate-sql-dw-compute-levels"></a>Использование службы "Функции Azure" для автоматизации управления уровнями вычислений в хранилище данных SQL

В этом руководстве объясняется, как с помощью службы "Функции Azure" управлять уровнями вычислений в хранилище данных SQL Azure. Эти архитектуры рекомендуется использовать с уровнем производительности хранилища данных SQL, [оптимизированным для эластичности][Performance Tiers].

Чтобы использовать приложение-функцию Azure с хранилищем данных SQL, необходимо создать [учетную запись субъекта-службы](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal) с правами участника в той же подписке, в которой создан экземпляр хранилища данных. 

## <a name="deploy-timer-based-scaler-with-an-azure-resource-manager-template"></a>Развертывание средства масштабирования на основе таймера с помощью шаблона Azure Resource Manager

Для развертывания шаблона вам потребуются следующие сведения:

- имя группы ресурсов, в которой находится экземпляр хранилища данных SQL;
- имя логического сервера, на котором расположен экземпляр хранилища данных SQL;
- имя экземпляра хранилища данных SQL;
- идентификатор клиента (идентификатор каталога) Azure Active Directory;
- Идентификатор подписки 
- идентификатор приложения субъекта-службы;
- секретный ключ субъекта-службы.

Получив приведенные выше сведения, разверните этот шаблон:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

После развертывания шаблона должны появиться три новых ресурса: бесплатный план службы приложений Azure, план приложения-функции с оплатой по мере использования и учетная запись хранения, в которой будут обрабатываться очереди операций и журналов. Продолжайте изучать следующие разделы, чтобы узнать, как изменить развернутые функции в соответствии с потребностями.

### <a name="change-the-scale-up-or-scale-down-compute-level"></a>Изменение уровня вычислений при увеличении или уменьшении масштаба

1. Перейдите к службе приложения-функции. Если вы развернули шаблон со значениями по умолчанию, эта служба должна называться *DWOperations*. Когда приложение-функция откроется, вы увидите пять развернутых функций в службе приложения-функции. 

   ![Функции, развернутые с помощью шаблона](media/manage-compute-with-azure-functions/five-functions.png)

2. Выберите *DWScaleDownTrigger* или *DWScaleUpTrigger* в зависимости от того, что нужно изменить: время увеличения или уменьшения масштаба. В раскрывающемся списке выберите "Интегрировать".

   ![Выбор пункта "Интегрировать" для функции](media/manage-compute-with-azure-functions/select-integrate.png)

3. В текущий момент должно отображаться значение *%ScaleDownTime%* или *%ScaleUpTime%*. Эти значения указывают на то, что расписание основано на значениях, определенных в [параметрах приложения][Application Settings]. Сейчас вы можете проигнорировать это и изменить расписание на любое повременное расписание, выполнив следующие шаги.

4. В области расписания добавьте время, которое должно отражаться в выражении CRON, чтобы определить частоту увеличения масштаба хранилища данных SQL. 

  ![Изменение расписания функции](media/manage-compute-with-azure-functions/change-schedule.png)

  Значение `schedule` представляет собой [выражение CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) с шестью полями: 
  ```json
  {second} {minute} {hour} {day} {month} {day-of-week}
  ```

  Например, при значении *0 30 9 * * 1-5* триггер будет отображаться по рабочим дням в 9:30. Дополнительные сведения см. в [примерах расписаний][schedule examples] службы "Функции Azure".


### <a name="change-the-scale-up-or-scale-down-time"></a>Изменение времени увеличения или уменьшения масштаба

1. Перейдите к службе приложения-функции. Если вы развернули шаблон со значениями по умолчанию, эта служба должна называться *DWOperations*. Когда приложение-функция откроется, вы увидите пять развернутых функций в службе приложения-функции. 

2. Выберите *DWScaleDownTrigger* или *DWScaleUpTrigger* в зависимости от того, что нужно изменить: значение увеличения или уменьшения масштаба вычислений. После выбора функций в области должен отобразиться файл *index.js*.

   ![Изменение уровня вычислений триггера функции](media/manage-compute-with-azure-functions/index-js.png)

3. Замените значение параметра *ServiceLevelObjective* на значение необходимого уровня и нажмите кнопку "Сохранить". Это уровень вычислений, до которого будет масштабирован ваш экземпляр хранилища данных по расписанию, определенному в разделе интеграции.

### <a name="use-pause-or-resume-instead-of-scale"></a>Использование приостановки или возобновления работы вместо масштабирования 

Сейчас по умолчанию включены функции *DWScaleDownTrigger* и *DWScaleUpTrigger*. Если вместо функции масштабирования вам нужно использовать приостановку или возобновление работы, можете включить *DWPauseTrigger* или *DWResumeTrigger*.

1. Перейдите на панель "Функции".

   ![Панель "Функции"](media/manage-compute-with-azure-functions/functions-pane.png)



2. Передвиньте переключатели для соответствующих триггеров, которые нужно включить.

3. Перейдите на вкладки *интеграции* для соответствующих триггеров, чтобы изменить их расписание.

   [!NOTE]: The functional difference between the scaling triggers and the pause/resume triggers is the message that is sent to the queue. See [Add a new trigger function][Add a new trigger function] for more information.



### <a name="add-a-new-trigger-function"></a>Добавление новой функции триггера

Сейчас в шаблоне содержатся только две функции масштабирования. Это означает, что в течение дня можно увеличить и уменьшить масштаб только один раз. Для более детализированного управления, например для уменьшения масштаба несколько раз за день или изменения поведения масштабирования в выходные дни, нужно добавить другой триггер.

1. Создайте пустую функцию. Нажмите кнопку *+* возле пункта "Функции", чтобы открыть панель шаблона функции.

   ![Создание функции](media/manage-compute-with-azure-functions/create-new-function.png)

2. В поле "Язык" выберите *JavaScript*, а затем выберите *TimerTrigger*.

   ![Создание функции](media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Присвойте функции имя и настройте шаблон. На рисунке показано, как активировать функцию каждую субботу в полночь (поздним вечером в пятницу).

   ![Уменьшение масштаба в субботу](media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Скопируйте содержимое файла *index.js* из других функций триггера.

   ![Копирование содержимого файла index.js](media/manage-compute-with-azure-functions/index-js.png)

5. Задайте для переменной операции требуемое поведение следующим образом:

   ```javascript
   // Resume the data warehouse instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the data warehouse instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the data warehouse instance to DW600
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600"
   }
   ```


### <a name="complex-scheduling"></a>Создание сложного расписания

В этом разделе кратко объясняется, что требуется для более сложного расписания приостановки, возобновления работы и масштабирования.

#### <a name="example-1"></a>Пример 1

Ежедневное увеличение масштаба до значения DW600 в 8:00 и уменьшение масштаба до DW200 в 20:00.

| Функция  | Расписание     | Операция                                |
| :-------- | :----------- | :--------------------------------------- |
| Функция 1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW600"}` |
| Функция 2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

#### <a name="example-2"></a>Пример 2 

Ежедневное увеличение масштаба до значения DW1000 в 8:00, уменьшение масштаба до DW600 один раз в 16:00 и до DW200 в 22:00.

| Функция  | Расписание     | Операция                                |
| :-------- | :----------- | :--------------------------------------- |
| Функция 1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW1000"}` |
| Функция 2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Функция 3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

#### <a name="example-3"></a>Пример 3 

Увеличение масштаба до DW1000 в 8:00 и уменьшение масштаба до DW600 один раз в 16:00 в рабочие дни. Приостановка работы в пятницу в 23:00 и возобновление работы в понедельник в 7:00.

| Функция  | Расписание       | Операция                                |
| :-------- | :------------- | :--------------------------------------- |
| Функция 1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Функция 2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Функция 3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Функция 4 | 0 0 7 * * 0    | `var operation = {"operationType": "ResumeDw"}` |



## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о функциях Azure триггера с таймером см. [здесь](../azure-functions/functions-create-scheduled-function.md).

Просмотрите [репозиторий с примерами](https://github.com/Microsoft/sql-data-warehouse-samples) для хранилища данных SQL.



[schedule examples]: ../azure-functions/functions-bindings-timer.md#example

[Application Settings]: ../azure-functions/functions-how-to-use-azure-function-app-settings.md
[Add a new trigger function]: manage-compute-with-azure-functions.md#add-a-new-trigger-function

[Performance Tiers]: performance-tiers.md
