<properties
	pageTitle="Триггеры с таймерами в функциях Azure | Microsoft Azure"
	description="Узнайте, как использовать триггеры с таймерами в функциях Azure."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="функции azure, функции, обработка событий, динамические вычисления, независимая архитектура"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="05/16/2016"
	ms.author="chrande"/>

# Триггеры с таймерами в функциях Azure

В этой статье описывается настройка триггеров с таймерами в функциях Azure. Триггеры с таймерами вызывают функции по расписанию, однократно или в повторяющемся режиме.

[AZURE.INCLUDE [общие сведения](../../includes/functions-bindings-intro.md)]

## Файл function.JSON для триггера с таймером

Файл *function.json* содержит выражение schedule и параметр, который указывает, следует ли активировать функцию немедленно.

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "runOnStartup": true,
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Триггер таймера автоматически масштабирует несколько экземпляров. Во всех экземплярах будет выполняться только один экземпляр определенной функции таймера.

## Формат выражения schedule

Выражение schedule может быть [выражением CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression), содержащим 6 полей: {second}, {minute}, {hour}, {day}, {month} и {day of the week}. Во многих документах по выражениям CRON в сети отсутствует поле {second}. Поэтому если скопировать текст выражения в одном из них, понадобится добавить дополнительное поле.

Выражение schedule также может иметь формат *чч:мм:сс*. Таким образом можно указать задержку между вызовами функции.

Ниже приведены примеры выражений schedule.

Активация через каждые 5 минут:

```json
"schedule": "0 */5 * * * *",
"runOnStartup": false,
```

Немедленная активация, а затем через каждые два часа:

```json
"schedule": "0 0 */2 * * *",
"runOnStartup": true,
```

Активация через каждые 15 секунд:

```json
"schedule": "00:00:15",
"runOnStartup": false,
```

## Пример кода C# для триггера таймера

В этом примере кода C# при каждой активации функции записывается один журнал.

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");    
}
```

## Дальнейшие действия

[AZURE.INCLUDE [дальнейшие действия](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0525_2016-->