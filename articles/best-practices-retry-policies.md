<properties
   pageTitle="Пакеты NuGet | Microsoft Azure"
   description="Руководство по пакетами NuGet реализации политики общих повторных попыток."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/09/2015"
   ms.author="masashin"/>

# Пакеты NuGet

<p class="lead">Поскольку все больше компонентов начинают взаимодействовать, правильная обработка временных ошибок становится все более важной. Обработка временных ошибок, выполняемая пакетом политик повторов NuGet, может помочь с реализацией повторных попыток в одном экземпляре.</p>

> Этот документ был основан на черновике в качестве пробной версии. Это не фактически проверенное руководство.

Кодекс шаблонов и рекомендаций `TransientFaultHandling` рекомендуется для работы с общей политикой повторных попыток.

```
Install-Package EnterpriseLibrary.WindowsAzure.TransientFaultHandling
```

## Конфигурация

Раздел содержит сведения о настройке компонента повторных попыток.

Параметр | Описание
-------------------- | ----------------------
MaximumExecutionTime | Максимальное время выполнения запроса, включая все потенциальные повторные попытки.
ServerTimeOut | Интервал времени ожидания сервера для запроса
RetryPolicy | Политика повтора. См. раздел "Политики" ниже.

```csharp
/// <summary>
/// An interface required for request option types.
/// </summary>
public interface IRequestOptions
{
    IRetryPolicy RetryPolicy { get; set; }

    TimeSpan? ServerTimeout { get; set; }

    TimeSpan? MaximumExecutionTime { get; set; }
}
```

Программный:

- поддержка параметров на клиентском компьютере.
- Включить переопределение в операциях, предоставляемых клиентом

Файл конфигурации.

```xml
<RetryPolicyConfiguration defaultRetryStrategy="Fixed Interval Retry Strategy">
    <linearInterval name="Fixed Interval Retry Strategy"
	retryInterval="00:00:01" maxRetryCount="10" />
    <exponentialBackoff name="Backoff Retry Strategy" minBackoff="00:00:01"
        maxBackoff="00:00:30" deltaBackoff="00:00:10" maxRetryCount="10"
        fastFirst="false"/>
</RetryPolicyConfiguration>
```

## Политики

### Экспоненциальная

Применяется для выбора экспоненциального интервала ожидания повторных попыток вызовов службы, чтобы избежать регулирования службы.

__Подход.__

Экспоненциально увеличивает интервал задержки между последующими попытками входа. Добавление случайного элемента (+/-20 %) в интервал задержки во избежание выполнения одновременного повтора всеми клиентами

__Конфигурация.__

Параметр | Описание
-------------------- | -------------------------------------------------------
maxAttempt | Количество повторных попыток.
deltaBackoff | Интервал отсрочки между повторными попытками. Величина, кратная timespan, будет использоваться для последующих попыток.
MinBackoff | Добавляется ко всем интервалам повтора, вычисленным на основе значения deltaBackoff.
FastFirst | Немедленная первая повторная попытка.
MaxBackoff | MaxBackoff используется в том случае, если расчетный интервал повторных попыток оказывается больше, чем MaxBackoff. Это значение не может быть изменено.

__Логика реализации.__

```csharp
if(!ExponentialRetry.FastFirst){
    Random r = new Random();
    double increment = (Math.Pow(2, currentRetryCount) - 1) * r.Next((int)(this.deltaBackoff.TotalMilliseconds * 0.8), (int)(this.deltaBackoff.TotalMilliseconds * 1.2));
    retryInterval = (increment < 0) ? ExponentialRetry.MaxBackoff :
    TimeSpan.FromMilliseconds(Math.Min(ExponentialRetry.MaxBackoff.TotalMilliseconds, ExponentialRetry.MinBackoff.TotalMilliseconds + increment));
} else {
    retryInterval = TimeSpan.Zero;
}
```

## Линейная

Применяется для выбора линейного интервала ожидания повторных попыток вызовов службы, чтобы избежать регулирования службы.

__Подход.__

Выполняет указанное число повторных попыток, используя указанный фиксированный интервал времени между повторными попытками. Добавление случайного элемента (+/-20 %) в интервал задержки во избежание выполнения одновременного повтора всеми клиентами.

__Конфигурация.__

Параметр | Описание
-------------------- | -------------------------------------------------------
maxAttempt | Количество повторных попыток.
deltaBackoff | Интервал отсрочки между повторными попытками.
FastFirst | Немедленная первая повторная попытка.

__Логика реализации.__

```csharp
if(!ExponentialRetry.FastFirst) {
    Random r = new Random();
    retryInterval = TimeSpan.FromMilliseconds(r.Next((int)(
    this.deltaBackoff.TotalMilliseconds * 0.8), (int)(this.deltaBackoff.TotalMilliseconds * 1.2)));
} else {
    retryInterval = TimeSpan.Zero;
}
```

## Адаптивная

Применяется для интервала ожидания повторных попыток вызовов службы на основе кода ошибки/метаданных, передаваемых службой в заголовке ответа.

__Подход.__

Выполняет указанное число повторных попыток, используя интервал задержки, вычисляемый на основании кода ошибки/метаданных, передаваемых службой в заголовке ответа.


__Конфигурация.__

Не настраивается.

__Логика реализации.__

На основе кода ошибки/метаданных, передаваемых службой в заголовке ответа.

__Автоматическое выключение.__

На основе [Автоматического выключения](http://msdn.microsoft.com/library/dn589784.aspx).

## Расширяемость

Открытый интерфейс, который можно реализовать для осуществления политики пользовательских повторов.

```csharp
public interface IRetryPolicy
{
    /// <summary>
    /// Generates a new retry policy for the current request attempt.
    /// </summary>
    IRetryPolicy CreateInstance();

    /// <summary>
    /// Determines whether the operation should be retried and the interval until the next retry.
    /// </summary>
    /// <param name="currentRetryCount">An integer specifying the number of retries for the given operation. A value of zero signifies this is the first error encountered.</param>
    /// <param name="statusCode">An integer containing the status code for the last operation.</param>
    /// <param name="retryInterval">A <see cref="TimeSpan"/> indicating the interval to wait until the next retry.</param>
    /// <returns><c>true</c> if the operation should be retried; otherwise, <c>false</c>.</returns>
    bool ShouldRetry(int currentRetryCount, int statusCode, out TimeSpan retryInterval);
}
```

## Телеметрия

Регистрирует повторные попытки как события ETW с помощью EventSource. Ниже перечислены поля, которые следует вносить в журнал для каждой попытки повтора.

Параметр | Описание
-------------------- | -------------------------------------------------------
requestId | ""
policyType | "RetryExponential"
операция | "Get:https://retry-guidance-tests.servicebus.windows.net/TestQueue/?api-version=2014-05"
operationStartTime | 05\.09.2014, 22:00:13
operationEndTime | 05\.09.2014, 22:00:14
Итерация | 0
iterationSleep | "00:00:00.1000000"
lastExceptionType | "Microsoft.ServiceBus.Messaging.MessagingCommunicationException"
exceptionMessage | "Невозможно разрешить удаленное имя: 'retry-guidance-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3,TimeStamp:9/5/2014 10:00:13 PM"

<!---HONumber=July15_HO5-->