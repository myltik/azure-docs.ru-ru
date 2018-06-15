---
title: Руководство. Управление вычислениями с помощью службы "Функции Azure" в хранилище данных Azure SQL | Документация Майкрософт
description: Инструкции по использованию службы "Функции Azure" для управления вычислениями в хранилище данных.
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/27/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 48428ef329de4719a25afd20c21ac102bba540a8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32188452"
---
# <a name="use-azure-functions-to-manage-compute-resources-in-azure-sql-data-warehouse"></a>Управление вычислительными ресурсами в хранилище данных Azure SQL с помощью службы "Функции Azure"

В этом руководстве используется служба "Функции Azure" для управления вычислительными ресурсами хранилища данных в хранилище данных Azure SQL.

Чтобы использовать приложение-функцию Azure с хранилищем данных SQL, необходимо создать [учетную запись субъекта-службы](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) с правами участника в той же подписке, в которой создан экземпляр хранилища данных. 

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Развертывание масштабирования по таймеру с помощью шаблона Azure Resource Manager

Чтобы развернуть шаблон, понадобится следующая информация:

- имя группы ресурсов, в которой находится экземпляр хранилища данных SQL;
- имя логического сервера, на котором расположен экземпляр хранилища данных SQL;
- имя экземпляра хранилища данных SQL;
- идентификатор клиента (идентификатор каталога) Azure Active Directory;
- Идентификатор подписки 
- идентификатор приложения субъекта-службы;
- секретный ключ субъекта-службы.

После получения предыдущей информации разверните этот шаблон:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

После развертывания шаблона, необходимо найти три новых ресурса: бесплатный план обслуживания приложений Azure, план приложения-функции на основе потребления и учетную запись хранения, которая управляет журналом и очередью операций. Продолжайте изучать следующие разделы, чтобы узнать, как изменить развернутые функции в соответствии с потребностями.

## <a name="change-the-compute-level"></a>Изменение уровня вычислений

1. Перейдите к службе приложения-функции. Если вы развернули шаблон со значениями по умолчанию, эта служба должна называться *DWOperations*. Когда приложение-функция откроется, вы увидите пять развернутых функций в службе приложения-функции. 

   ![Функции, развернутые с помощью шаблона](media/manage-compute-with-azure-functions/five-functions.png)

2. Выберите *DWScaleDownTrigger* или *DWScaleUpTrigger* в зависимости от того, что нужно изменить: время увеличения или уменьшения масштаба. В раскрывающемся меню выберите пункт "Интегрировать".

   ![Выбор пункта "Интегрировать" для функции](media/manage-compute-with-azure-functions/select-integrate.png)

3. В текущий момент должно отображаться значение *%ScaleDownTime%* или *%ScaleUpTime%*. Эти значения указывают на то, что расписание основано на значениях, определенных в [параметрах приложения][Application Settings]. Пока что это значение можно проигнорировать и изменить время в расписании по своему усмотрению, основываясь на следующих шагах.

4. В области расписания добавьте время, которое должно отражаться в выражении CRON, чтобы определить частоту увеличения масштаба хранилища данных SQL. 

  ![Изменение расписания функции](media/manage-compute-with-azure-functions/change-schedule.png)

  Значение `schedule` представляет собой [выражение CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) с шестью полями: 
  ```json
  {second} {minute} {hour} {day} {month} {day-of-week}
  ```

  Например, *«0 30 9 * * 1-5»* будет отражать триггер, активирующийся каждый будний день в 9:30. Дополнительные сведения см. в [примерах расписаний][schedule examples] службы "Функции Azure".


## <a name="change-the-time-of-the-scale-operation"></a>Изменение времени операции масштабирования

1. Перейдите к службе приложения-функции. Если вы развернули шаблон со значениями по умолчанию, эта служба должна называться *DWOperations*. Когда приложение-функция откроется, вы увидите пять развернутых функций в службе приложения-функции. 

2. Выберите *DWScaleDownTrigger* или *DWScaleUpTrigger* в зависимости от того, что нужно изменить: значение увеличения или уменьшения масштаба вычислений. После выбора функций в области должен отобразиться файл *index.js*.

   ![Изменение уровня вычислений триггера функции](media/manage-compute-with-azure-functions/index-js.png)

3. Замените значение параметра *ServiceLevelObjective* на значение необходимого уровня и нажмите кнопку "Сохранить". Это значение задает уровень вычислений, на основе которого будет масштабироваться ваш экземпляр хранилища данных в соответствии с расписанием, определенным в разделе «Интеграция».

## <a name="use-pause-or-resume-instead-of-scale"></a>Использование приостановки или возобновления работы вместо масштабирования 

Сейчас по умолчанию включены функции *DWScaleDownTrigger* и *DWScaleUpTrigger*. Если вместо функции масштабирования вам нужно использовать приостановку или возобновление работы, можете включить *DWPauseTrigger* или *DWResumeTrigger*.

1. Перейдите на панель "Функции".

   ![Панель "Функции"](media/manage-compute-with-azure-functions/functions-pane.png)



2. Передвиньте переключатели для соответствующих триггеров, которые нужно включить.

3. Перейдите на вкладки *интеграции* для соответствующих триггеров, чтобы изменить их расписание.

   > [!NOTE]
   > Функциональная разница между триггерами масштабирования и триггерами паузы или возобновления — это сообщение, отправленное в очередь. Дополнительные сведения см. в разделе [Добавление новой функции триггера][Add a new trigger function].


## <a name="add-a-new-trigger-function"></a>Добавление новой функции триггера

Сейчас в шаблоне содержатся только две функции масштабирования. С помощью этих функций в течение дня можно только один раз уменьшить масштаб и один раз увеличить его. Для более детального контроля, например для уменьшения масштаба несколько раз в день или настройки другого режима масштабирования на выходные дни, нужно добавить еще один триггер.

1. Создайте пустую функцию. Выберите *+* рядом с расположением службы "Функции", чтобы отобразить панель шаблонов функций.

   ![Создание функции](media/manage-compute-with-azure-functions/create-new-function.png)

2. В поле "Язык" выберите *JavaScript*, а затем выберите *TimerTrigger*.

   ![Создание функции](media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Присвойте функции имя и настройте шаблон. На рисунке показано, как активировать функцию каждую субботу в полночь (поздним вечером в пятницу).

   ![Уменьшение масштаба в субботу](media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Скопируйте содержимое файла *index.js* из других функций триггера.

   ![Копирование содержимого файла index.js](media/manage-compute-with-azure-functions/index-js.png)

5. Установите для переменной операции требуемое поведение следующим образом:

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


## <a name="complex-scheduling"></a>Создание сложного расписания

В этом разделе кратко показано, что необходимо для более комплексного планирования возможностей паузы, возобновления и масштабирования.

### <a name="example-1"></a>Пример 1

Ежедневное увеличение масштаба до значения DW600 в 8:00 и уменьшение масштаба до DW200 в 20:00.

| Функция  | Расписание     | Операция                                |
| :-------- | :----------- | :--------------------------------------- |
| Функция 1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW600"}` |
| Функция 2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-2"></a>Пример 2 

Ежедневное увеличение масштаба в 8:00 до DW1000, однократное уменьшение до DW600 в 16:00 и уменьшение масштаба в 22:00 до DW200.

| Функция  | Расписание     | Операция                                |
| :-------- | :----------- | :--------------------------------------- |
| Функция 1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW1000"}` |
| Функция 2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Функция 3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-3"></a>Пример 3 

Увеличение масштаба до DW1000 в 8:00 и уменьшение масштаба до DW600 один раз в 16:00 в рабочие дни. Приостановка работы в пятницу в 23:00 и возобновление работы в понедельник в 7:00.

| Функция  | Расписание       | Операция                                |
| :-------- | :------------- | :--------------------------------------- |
| Функция 1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Функция 2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Функция 3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Функция 4 | 0 0 7 * * 0    | `var operation = {"operationType": "ResumeDw"}` |



## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о функциях Azure триггера с таймером см. [здесь](../azure-functions/functions-create-scheduled-function.md).

Просмотрите [репозиторий с примерами](https://github.com/Microsoft/sql-data-warehouse-samples) для хранилища данных SQL.



[schedule examples]: ../azure-functions/functions-bindings-timer.md#example

[Application Settings]: ../azure-functions/functions-how-to-use-azure-function-app-settings.md
[Add a new trigger function]: manage-compute-with-azure-functions.md#add-a-new-trigger-function