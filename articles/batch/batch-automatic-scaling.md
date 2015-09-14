
<properties
	pageTitle="Автоматическое масштабирование вычислительных узлов в пуле пакетной службы Azure | Microsoft Azure"
	description="Включение автоматического масштабирования в облачном пуле для динамического изменения количества вычислительных узлов в пуле."
	services="batch"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="batch"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="08/26/2015"
	ms.author="davidmu"/>

# Автоматическое масштабирование вычислительных узлов в пуле пакетной службы Azure

Автоматическое масштабирование вычислительных узлов в пуле пакетной службы Azure представляет собой динамическую настройку вычислительной мощности, используемой вашим приложением. Простота этой настройки сэкономит вам время и деньги. Для получения дополнительных сведений о вычислительных узлах и пулах см. статью [Технический обзор пакетной службы Azure](batch-technical-overview.md).

Автоматическое масштабирование происходит при включении этой функции в пуле, а также при связывании формулы с пулом. Формула используется для определения количества вычислительных узлов, необходимого для обработки приложения. Воздействуя на выборки, которые собираются периодически, количество доступных вычислительных узлов в пуле корректируется каждые 15 минут на основе связанной формулы.

Автоматическое масштабирование задается во время создания пула. Также вы можете включить его позже для существующего пула. Формула также может обновляться в пуле с включенной ранее функцией автоматического масштабирования. Рекомендуется всегда оценивать формулу, прежде чем назначать ее пулу. Также важно отслеживать состояние выполнения автоматического масштабирования. Ниже мы рассмотрим каждую из этих тем.

> [AZURE.NOTE]Каждая учетная запись пакетной службы Azure имеет ограничение на максимальное количество вычислительных узлов, которые могут использоваться для обработки. Система создает узлы только в рамках этого ограничения, поэтому она может и не достичь целевого количества, заданного формулой.

## Автоматическое масштабирование вычислительных ресурсов

Формулы масштабирования, которые вы определяете, указывают количество доступных вычислительных узлов в пуле для следующего интервала обработки. Формула автоматического масштабирования — это просто строковое значение, которое назначается в пуле элементу [autoScaleFormula](https://msdn.microsoft.com/library/azure/dn820173.aspx) в тексте запроса (REST API) или свойству [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) (.NET API). Строка формулы не может превышать размер 8 КБ и может включать до 100 инструкций, разделенных точкой с запятой, а также разрывы строк и комментарии.

Инструкции в формуле указаны в виде выражений свободной формы. Эти выражения могут включать определяемые системой и пользователем переменные, постоянные значения, а также поддерживаемые операции с этими переменными или постоянными значениями.

	VAR = Expression(system-defined variables, user-defined variables);

Сложные формулы создаются с помощью нескольких инструкций и переменных:

	VAR₀ = Expression₀(system-defined variables);
	VAR₁ = Expression₁(system-defined variables, VAR₀);

> [AZURE.NOTE]Формула автоматического масштабирования состоит из переменных, типов, операций и функций [REST API пакетной службы](https://msdn.microsoft.com/library/azure/dn820158.aspx). Они используются в строках формулы даже при работе с библиотекой [.NET пакетной службы](https://msdn.microsoft.com/library/azure/mt348682.aspx).

### Переменные

В формуле можно использовать переменные, определяемые как системой, так и пользователем.

*Получите* и *установите* значение этих **определяемых системой переменных**, чтобы управлять количеством вычислительных узлов в пуле.

<table>
  <tr>
    <th>Переменная</th>
    <th>Описание</th>
  </tr>
  <tr>
    <td>$TargetDedicated</td>
    <td>Целевое количество выделенных пулу вычислительных узлов. Значение можно изменить на основе фактического потребления ресурсов при выполнении задач.</td>
  </tr>
  <tr>
    <td>$NodeDeallocationOption</td>
    <td>Действие, выполняемое после удаления вычислительных узлов из пула. Возможные значения:
      <br/>
      <ul>
        <li><p><b>requeue</b>&#160;— немедленное завершение задач с перемещением в очередь заданий, чтобы запланировать их выполнение заново.</p></li>
        <li><p><b>terminate</b>&#160;— немедленное завершение задач с удалением из очереди заданий.</p></li>
        <li><p><b>taskcompletion</b>&#160;— ожидание завершения выполнение текущих задач с удалением узла из пула.</p></li>
        <li><p><b>retaineddata</b>&#160;— ожидание очистки всех сохраненные в узле данных локальных задач перед удалением узла из пула.</p></li>
      </ul></td>
   </tr>
</table>

*Получите* значения этих **определяемых системой переменных**, чтобы вносить изменения с учетом метрик вычислительных узлов в выборках. Эти переменные доступны только для чтения.

<table>
  <tr>
    <th>Переменная</th>
    <th>Описание</th>
  </tr>
  <tr>
    <td>$CPUPercent</td>
    <td>Средний объем использования ЦП (%).</td>
  </tr>
  <tr>
    <td>$WallClockSeconds</td>
    <td>Затраченное время в секундах.</td>
  </tr>
  <tr>
    <td>$MemoryBytes</td>
    <td>Среднее количество используемых мегабайт.</td>
  <tr>
    <td>$DiskBytes</td>
    <td>Среднее количество гигабайт, используемых на локальных дисках.</td>
  </tr>
  <tr>
    <td>$DiskReadBytes</td>
    <td>Количество считанных байт.</td>
  </tr>
  <tr>
    <td>$DiskWriteBytes</td>
    <td>Количество записанных байт.</td>
  </tr>
  <tr>
    <td>$DiskReadOps</td>
    <td>Количество операций чтения с диска.</td>
  </tr>
  <tr>
    <td>$DiskWriteOps</td>
    <td>Количество операций записи на диск.</td>
  </tr>
  <tr>
    <td>$NetworkInBytes</td>
    <td>Количество входящих байт.</td>
  </tr>
  <tr>
    <td>$NetworkOutBytes</td>
    <td>Количество исходящих байт.</td>
  </tr>
  <tr>
    <td>$SampleNodeCount</td>
    <td>Количество вычислительных узлов.</td>
  </tr>
  <tr>
    <td>$ActiveTasks</td>
    <td>Количество задач в активном состоянии.</td>
  </tr>
  <tr>
    <td>$RunningTasks</td>
    <td>Количество задач в состоянии выполнения.</td>
  </tr>
  <tr>
    <td>$SucceededTasks</td>
    <td>Количество успешно выполненных задач.</td>
  </tr>
  <tr>
    <td>$FailedTasks</td>
    <td>Количество задач, которые не удалось выполнить.</td>
  </tr>
  <tr>
    <td>$CurrentDedicated</td>
    <td>Текущее количество выделенных вычислительных узлов.</td>
  </tr>
</table>

### Типы

В формуле поддерживаются следующие **типы**:

- double
- doubleVec
- строка
- timestamp. timestamp является комплексной структурой, которая содержит следующие элементы:
	- year
	- month (1-12)
	- day (1-31)
	- weekday (в формате числа, например 1 — понедельник);
	- hour (в 24-часовом формате, например 13 соответствует 13:00);
	- minute (00–59)
	- second (00–59)
- timeInterval
	- TimeInterval\_Zero
	- TimeInterval\_100ns
	- TimeInterval\_Microsecond
	- TimeInterval\_Millisecond
	- TimeInterval\_Second
	- TimeInterval\_Minute
	- TimeInterval\_Hour
	- TimeInterval\_Day
	- TimeInterval\_Week
	- TimeInterval\_Year

### Операции

Для перечисленных выше типов разрешены следующие **операции**.

<table>
  <tr>
    <th>Операция</th>
    <th>Допустимые операторы</th>
  </tr>
  <tr>
    <td>double &lt;operator> double => double</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>double &lt;operator> timeinterval => timeinterval</td>
    <td>*</td>
  </tr>
  <tr>
    <td>doubleVec &lt;operator> double => doubleVec</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>doubleVec &lt;operator> doubleVec => doubleVec</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operator> double => timeinterval</td>
    <td>*, /</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operator> timeinterval => timeinterval</td>
    <td>+, -</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operator> timestamp => timestamp</td>
    <td>+</td>
  </tr>
  <tr>
    <td>timestamp &lt;operator> timeinterval => timestamp</td>
    <td>+</td>
  </tr>
  <tr>
    <td>timestamp &lt;operator> timestamp => timeinterval</td>
    <td>-</td>
  </tr>
  <tr>
    <td>&lt;operator>double => double</td>
    <td>-, !</td>
  </tr>
  <tr>
    <td>&lt;operator>timeinterval => timeinterval</td>
    <td>-</td>
  </tr>
  <tr>
    <td>double &lt;operator> double => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>string &lt;operator> string => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>timestamp &lt;operator> timestamp => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operator> timeinterval => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>double &lt;operator> double => double</td>
    <td>&amp;&amp;, ||</td>
  </tr>
  <tr>
    <td>проверка только типа double (ненулевые значения означают «Истина»; ноль означает «Ложь»)</td>
    <td>? :</td>
  </tr>
</table>

### Функции

Для определения формулы автоматического масштабирования доступны следующие предопределенные **функции**.

<table>
  <tr>
    <th>Функция</th>
    <th>Описание</th>
  </tr>
  <tr>
    <td>double <b>avg</b>(doubleVecList)</td>
    <td>Среднее значение для всех значений в doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>len</b>(doubleVecList)</td>
    <td>Длина вектора, созданного из doubleVecList.</td>
  <tr>
    <td>double <b>lg</b>(double)</td>
    <td>Логарифм по основанию&#160;2.</td>
  </tr>
  <tr>
    <td>doubleVec <b>lg</b>(doubleVecList)</td>
    <td>Покомпонентный логарифм с основанием&#160;2. Функция vec(double) должна явно передаваться для одного параметра типа double, иначе предполагается версия double lg(double).</td>
  </tr>
  <tr>
    <td>double <b>ln</b>(double)</td>
    <td>Натуральный логарифм.</td>
  </tr>
  <tr>
    <td>doubleVec <b>ln</b>(doubleVecList)</td>
    <td>Покомпонентный логарифм с основанием&#160;2. Функция vec(double) должна явно передаваться для одного параметра типа double, иначе предполагается версия double lg(double).</td>
  </tr>
  <tr>
    <td>double <b>log</b>(double)</td>
    <td>Логарифм по основанию&#160;10.</td>
  </tr>
  <tr>
    <td>doubleVec <b>log</b>(doubleVecList)</td>
    <td>Покомпонентный логарифм с основанием&#160;10. Функция vec(double) должна явно передаваться для одного параметра типа double, иначе предполагается версия double log(double).</td>
  </tr>
  <tr>
    <td>double <b>max</b>(doubleVecList)</td>
    <td>Максимальное значение в doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>min</b>(doubleVecList)</td>
    <td>Минимальное значение в doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>norm</b>(doubleVecList)</td>
    <td>2-норма вектора, созданного из doubleVecList.
  </tr>
  <tr>
    <td>double <b>percentile</b>(doubleVec v, double p)</td>
    <td>Элемент процентиля вектора&#160;v.</td>
  </tr>
  <tr>
    <td>double <b>rand</b>()</td>
    <td>Случайное значение от 0,0 до 1,0.</td>
  </tr>
  <tr>
    <td>double <b>range</b>(doubleVecList)</td>
    <td>Разница между минимальным и максимальным значением в doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>std</b>(doubleVecList)</td>
    <td>Стандартное отклонение выборки для значений в doubleVecList.</td>
  </tr>
  <tr>
    <td><b>stop</b>()</td>
    <td>Остановка оценки выражений автоматического масштабирования.</td>
  </tr>
  <tr>
    <td>double <b>sum</b>(doubleVecList)</td>
    <td>Сумма всех компонентов doubleVecList.</td>
  </tr>
  <tr>
    <td>timestamp <b>time</b>(string dateTime="")</td>
    <td>Отметка времени для текущего времени, если параметры не переданы; отметка времени строки dateTime, если параметры переданы. Поддерживаемые форматы даты и времени: W3CDTF и RFC1123.</td>
  </tr>
  <tr>
    <td>double <b>val</b>(doubleVec v, double i)</td>
    <td>Значение элемента в расположении&#160;i в векторе&#160;v с начальным индексом&#160;0.</td>
  </tr>
</table>

Некоторые функции, описанные в таблице выше, могут принимать список в качестве аргумента. Список значений, разделенных запятыми, — это любая комбинация типов *double* и *doubleVec*. Например:

	doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?

Значение *doubleVecList* преобразуется в один тип *doubleVec* перед оценкой. Например, если v = [1,2,3], то вызывающая функция avg(v) равна вызывающей функции avg(1,2,3), а вызывающая функция avg (v, 7) равная вызывающей функции avg(1,2,3,7).

### Получение образца данных

Определяемые системой переменные, описанные выше, — это объекты, которые предоставляют методы для доступа к связанным данным. Например, следующее выражение иллюстрирует запрос для получения данных об использовании ЦП за последние пять минут.

	$CPUPercent.GetSample(TimeInterval_Minute*5)

Эти методы можно использовать для получения образцов данных.

<table>
  <tr>
    <th>Метод</th>
    <th>Описание</th>
  </tr>
  <tr>
    <td>Count()</td>
    <td>Возвращает общее количество выборок в журнале метрик.</td>
  </tr>
  <tr>
    <td>GetSample()</td>
    <td><p>Возвращает вектор образцов данных. Например:</p>
        <ul>
          <li><p><b>doubleVec GetSample (double count)</b>&#160;— указывает количество выборок, которые требуются из последней выборки.</p>
				  <p>Выборка имеет ценность для данных метрики в течение 5&#160;секунд. GetSample(1) возвращает последнюю доступную выборку, но ее нельзя будет использовать для такой метрики, как $CPUPercent, поскольку невозможно узнать момент получения выборки. Эта выборка может быть последней или, если в системе есть проблемы, гораздо более старой. Лучше использовать интервал времени, как показано ниже.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime [, double samplePercent])</b>&#160;— указывает интервал времени для сбора данных и при необходимости указывает долю выборок (%) в соответствии с требуемым диапазоном.</p>
          <p>Метод $CPUPercent.GetSample(TimeInterval\_Minute*10) должен вернуть 200&#160;выборок, если все они присутствовали в журнале CPUPercent в течение последних 10&#160;минут. Если последняя минута журнала по-прежнему отсутствует, возвращаются только 180&#160;выборок.</p>
					<p>Метод $CPUPercent.GetSample(TimeInterval\_Minute*10, 80) выполняется успешно, а метод $CPUPercent.GetSample(TimeInterval_Minute*10,95) не выполняется.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime, (timestamp | timeinterval) endTime [, double samplePercent])</b>&#160;— задает интервал времени для сбора данных, т.&#160;е. начало и конец сбора.</p></li></ul>
		  <p>Обратите внимание, что между сбором образца и моментом, когда он будет доступен для формулы, есть задержка; это необходимо учитывать при использовании метода GetSample; см. раздел GetSamplePercent ниже.</td>
  </tr>
  <tr>
    <td>GetSamplePeriod()</td>
    <td>Возвращает в исторический набор данных выборки период, за который эти выборки были получены.</td>
  </tr>
  <tr>
    <td>HistoryBeginTime()</td>
    <td>Возвращает метрике отметку времени самой старой доступной выборки данных.</td>
  </tr>
  <tr>
    <td>GetSamplePercent()</td>
    <td><p>Возвращает долю выборок (%), содержащихся в настоящее время в журнале для заданного интервала времени. Например:</p>
    <p><b>doubleVec GetSamplePercent( (timestamp | timeinterval) startTime [, (timestamp | timeinterval) endTime] )</b>
	<p>Поскольку метод GetSample не срабатывает, если возвращается меньший процент собранных данных, чем указано в параметре samplePercent, вы можете использовать метод GetSamplePercent для первой проверки, а затем выполнить другое действие, если журнал содержит недостаточное количество выборок, без прерывания оценки автоматического масштабирования.</p></td>
  </tr>
</table>

### Метрики

**Метрики** ресурсов и задач можно использовать при определении формулы, а также для управления вычислительными узлами в пуле.

<table>
  <tr>
    <th>Metric (Метрика)</th>
    <th>Описание</th>
  </tr>
  <tr>
    <td>Ресурс</td>
    <td><p>Метрики ресурсов основываются на показателях загрузки ЦП, использования пропускной способности и памяти, а также на количестве вычислительных узлов. В формулах для управления вычислительными узлами в пуле используются следующие определяемые системой переменные (описанные выше в разделе **«Переменные»**):</p>
    <p><ul>
      <li>$TargetDedicated</li>
      <li>$NodeDeallocationOption</li>
    </ul></p>
    <p>Для внесения изменений с учетом метрик ресурсов узла используются следующие определяемые системой переменные:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td>Задача</td>
    <td><p>С учетом состояния задач (активные, в ожидании и завершенные).</p>
    <p>Для внесения изменений с учетом метрик задач используются следующие определяемые системой переменные:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$SucceededTasks</li>
      <li>$FailedTasks</li>
      <li>$CurrentDedicated</li></ul></p></td>
  </tr>
</table>

## Построение формулы автомасштабирования

Формула автомасштабирования создается путем формирования инструкций с помощью указанных выше компонентов и объединения этих инструкций в полную формулу. Например, в следующем примере мы создадим формулу, сначала определив требования к ней. Она должна:

1. Увеличивать целевое количество вычислительных узлов в пуле при высокой загрузке ЦП.
2. Уменьшать целевое количество вычислительных узлов в пуле при низкой загрузке ЦП.
3. Всегда ограничивать максимальное количество узлов до 400.

Для *увеличения* количества узлов во время высокой загрузки ЦП определим инструкцию, которая заполняет определяемую пользователем переменную ($TotalNodes) значением, составляющим 110 % от текущего целевого числа узлов, если минимальное среднее использование ЦП за последние 10 минут превышало 70 %:

	$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;

Следующая инструкция задает той же переменной 90 % от текущего целевого числа узлов, если среднее использование ЦП за последние 60 минут было *ниже* 20 %, снижая целевое число узлов во время низкой загрузки ЦП. Обратите внимание, что эта инструкция также ссылается на определяемую пользователем переменную *$TotalNodes* в инструкции выше.

	$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;

Теперь ограничим целевое число выделенных вычислительных узлов **максимальным** количеством, равным 400.

	$TargetDedicated = min(400, $TotalNodes)

Вот полная формула:

	$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;
	$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;
	$TargetDedicated = min(400, $TotalNodes)

## Создание пула со включенным автоматическим масштабированием

Чтобы включить автоматическое масштабирование при создании пула, воспользуйтесь одним из следующих способов.

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx) — этот командлет Azure PowerShell использует параметр AutoScaleFormula для определения формулы автоматического масштабирования.
- [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx) — когда этот метод .NET вызывается для создания пула, в пуле задаются свойства [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) и [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) для включения автоматического масштабирования.
- [Добавление пула в учетную запись](https://msdn.microsoft.com/library/azure/dn820174.aspx) — в этом запросе REST API используются элементы enableAutoScale и autoScaleFormula для настройки автоматического масштабирования пула во время его создания.

> [AZURE.NOTE]Если вы настроили автоматическое масштабирование во время создания пула с помощью одного из перечисленных выше методов, параметр *targetDedicated* не будет (и не должен будет) указываться во время создания пула. Также обратите внимание, что если вы хотите вручную изменить размер пула с включенным автомасштабированием (например, с помощью [BatchClient.PoolOperations.ResizePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx)), то сначала необходимо отключить автоматическое масштабирование в пуле, а затем изменить его размер.

В следующем фрагменте кода показано создание объекта [CloudPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx), для которого включено автомасштабирование, с помощью библиотеки [.NET пакетной службы](https://msdn.microsoft.com/library/azure/mt348682.aspx). Ее формула задает целевое количество узлов: 5 по понедельникам и 1 в остальные дни недели. В приведенном фрагменте myBatchClient — это правильно инициализированный экземпляр [BatchClient](http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx):

		CloudPool pool myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
		pool.AutoScaleEnabled = true;
		pool.AutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";
		pool.Commit();

## Включение автоматического масштабирования после создания пула

Если вы уже настроили пул с указанным количеством вычислительных узлов с помощью параметра *targetDedicated*, такой пул можно обновить позднее, настроив для него автоматическое масштабирование. Это можно сделать одним из следующих способов.

- [BatchClient.PoolOperations.EnableAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx) — для этого метода .NET требуется идентификатор существующего пула и формула автоматического масштабирования для применения к пулу.
- [Включение автоматического масштабирования в пуле](https://msdn.microsoft.com/library/azure/dn820173.aspx) — для этого запроса REST API требуется имя существующего пула в универсальном коде ресурса (URI) и формула автоматического масштабирования в тексте запроса.

> [AZURE.NOTE]Если значение для параметра *targetDedicated* было указанно во время создания пула, оно игнорируется во время оценки формулы автоматического масштабирования.

Этот фрагмент кода демонстрирует включение автомасштабирования для существующего пула с помощью библиотеки [.NET пакетной службы](https://msdn.microsoft.com/library/azure/mt348682.aspx). Обратите внимание, что при включении и обновлении формулы для существующего пула используется один и тот же метод. Таким образом, этот метод будет *обновлять* формулу в указанном пуле, если включено автоматическое масштабирование. В этом фрагменте предполагается, что myBatchClient — неправильно инициализированный экземпляр [BatchClient](http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx), а mypool — идентификатор существующего пула [CloudPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx).

		 // Define the autoscaling formula. In this snippet, the  formula sets the target number of nodes to 5 on
		 // Mondays, and 1 on every other day of the week
		 string myAutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";

		 // Update the existing pool's autoscaling formula by calling the BatchClient.PoolOperations.EnableAutoScale
		 // method, passing in both the pool's ID and the new formula.
		 myBatchClient.PoolOperations.EnableAutoScale("mypool", myAutoScaleFormula);

## Оценка формулы автоматического масштабирования

Рекомендуется оценивать формулу, прежде чем использовать ее в приложении. Формула оценивается путем тестового выполнения для существующего пула. Это можно сделать, используя следующее:

- [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) или [BatchClient.PoolOperations.EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx) — для этих методов .NET требуется идентификатор существующего пула и строка, которая содержит формулу автоматического масштабирования. Результаты вызова содержатся в экземпляре класса [AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx).
- [Оценка формулы автоматического масштабирования](https://msdn.microsoft.com/library/azure/dn820183.aspx) — в этом запросе REST API идентификатор пула указывается в универсальном коде ресурса (URI), а формула автоматического масштабирования — в элементе *autoScaleFormula* текста запроса. Ответ операции содержит все сведения об ошибках, которые могут быть связаны с формулой.

> [AZURE.NOTE]Чтобы оценить формулу автомасштабирования, необходимо сначала включить автоматическое масштабирование в пуле, используя допустимую формулу.

В этом фрагменте кода, использующем библиотеку [.NET пакетной службы](https://msdn.microsoft.com/library/azure/mt348682.aspx), мы оценим формулу, прежде чем применять ее в пуле [CloudPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx).

		// First obtain a reference to the existing pool
		CloudPool pool = myBatchClient.PoolOperations.GetPool("mypool");

		// We must ensure that autoscaling is enabled on the pool prior to evaluating a formula
		if (pool.AutoScaleEnabled.HasValue && pool.AutoScaleEnabled.Value)
		{
			// The formula to evaluate - adjusts target number of nodes based on day of week and time of day
			string myFormula = @"
				$CurTime=time();
				$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
				$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
				$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
				$TargetDedicated=$IsWorkingWeekdayHour?20:10;
			";

			// Perform the autoscale formula evaluation. Note that this does not actually apply the formula to
			// the pool.
			AutoScaleEvaluation eval = client.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

			if (eval.AutoScaleRun.Error == null)
			{
				// Evaluation success - print the results of the AutoScaleRun. This will display the values of each
				// variable as evaluated by the the autoscaling formula.
				Console.WriteLine("AutoScaleRun.Results: " + eval.AutoScaleRun.Results);

				// Apply the formula to the pool since it evaluated successfully
				client.PoolOperations.EnableAutoScale(pool.Id, myFormula);
			}
			else
			{
				// Evaluation failed, output the message associated with the error
				Console.WriteLine("AutoScaleRun.Error.Message: " + eval.AutoScaleRun.Error.Message);
			}
		}

В случае успешной оценки формулы в этом фрагменте будет получен результат, подобный приведенному ниже:

		AutoScaleRun.Results: $TargetDedicated = 10;$NodeDeallocationOption = requeue;$CurTime = 2015 - 08 - 25T20: 08:42.271Z;$IsWeekday = 1;$IsWorkingWeekdayHour = 0;$WorkHours = 0

## Получение сведений о выполнении автоматического масштабирования

Чтобы убедиться, что формула работает так, как ожидается, следует периодически проверять результаты выполнения автоматического масштабирования. Это можно сделать одним из следующих способов.

- [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx) — при использовании библиотеки .NET это свойство пула предоставляет экземпляр класса [AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx), который содержит следующие свойства последнего выполнения автоматического масштабирования:
  - [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx);
  - [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx);
  - [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx).
- [Получить сведения о пуле](https://msdn.microsoft.com/library/dn820165.aspx) — этот запрос API REST возвращает сведения о пуле, содержащие результаты последнего выполнения автоматического масштабирования.

## Примеры формул

Рассмотрим примеры, показывающие лишь несколько способов использования формул для автоматического масштабирования вычислительных ресурсов в пуле.

### Пример 1

Возможно, вы решили изменить размер пула в зависимости от дня недели и времени дня, чтобы количество узлов в пуле увеличивалось или уменьшалось соответствующим образом:

		$CurTime=time();
		$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
		$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
		$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
		$TargetDedicated=$IsWorkingWeekdayHour?20:10;

Эта формула сначала получает значение текущего времени. В рабочие дни (1–5) и часы (8:00–18:00) целевой размер пула равен 20 узлам. В противном случае для пула устанавливается целевой размер 10 узлов.

### Пример 2

В этом примере размер пула настраивается в зависимости от количества задач в очереди. Обратите внимание, что в строках формул допускаются комментарии и разрывы строк.

	    // Get pending tasks for the past 15 minutes.
	    $Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
	    // If we have less than 70% data points, we use the last sample point, otherwise we use the maximum of
		// last sample point and the history average.
	    $Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
	    // If number of pending tasks is not 0, set targetVM to pending tasks, otherwise half of current dedicated.
	    $TargetVMs = $Tasks > 0? $Tasks:max(0, $TargetDedicated/2);
	    // The pool size is capped at 20, if target VM value is more than that, set it to 20. This value
		// should be adjusted according to your use case.
	    $TargetDedicated = max(0,min($TargetVMs,20));
	    // Set node deallocation mode - keep nodes active only until tasks finish
	    $NodeDeallocationOption = taskcompletion;

### Пример 3

Другой пример — размер пула изменяется в зависимости от количества задач. Эта формула также учитывает значение [MaxTasksPerComputeNode](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx), которое было задано для пула. Это особенно полезно в ситуациях, где требуется параллельное выполнение задач на вычислительных узлах.

		// Determine whether 70% of the samples have been recorded in the past 15 minutes; if not, use last sample
		$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
		$Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
		// Set the number of nodes to add to one-fourth the number of active tasks (the MaxTasksPerComputeNode
		// property on this pool is set to 4, adjust this number for your use case)
		$Cores = $TargetDedicated * 4;
		$ExtraVMs = ($Tasks - $Cores) / 4;
		$TargetVMs = ($TargetDedicated+$ExtraVMs);
		// Attempt to grow the number of compute nodes to match the number of active tasks, with a maximum of 3
		$TargetDedicated = max(0,min($TargetVMs,3));
		// Keep the nodes active until the tasks finish
		$NodeDeallocationOption = taskcompletion;

## Дальнейшие действия

1. Чтобы в полной мере оценить эффективность работы приложения, вам может потребоваться доступ к вычислительным узлам. Чтобы воспользоваться преимуществами удаленного доступа, необходимо добавить учетную запись пользователя в узел, к которому необходимо получить доступ, и получить RDP-файл для этого узла.
    - Добавьте учетную запись пользователя одним из следующих способов.
        * [New-AzureBatchVMUser](https://msdn.microsoft.com/library/mt149846.aspx) — этот командлет PowerShell принимает имя пула, имя вычислительного узла, имя учетной записи и пароль в качестве параметров.
        * [BatchClient.PoolOperations.CreateComputeNodeUser](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createcomputenodeuser.aspx) — этот метод .NET создает экземпляр класса [ComputeNodeUser](https://msdn.microsoft.com/library/microsoft.azure.batch.computenodeuser.aspx), на котором можно задать имя учетной записи и пароль для вычислительного узла. Затем, чтобы создать пользователя на этом узле, в экземпляре вызывается метод [ComputeNodeUser.Commit](https://msdn.microsoft.com/library/microsoft.azure.batch.computenodeuser.commit.aspx).
        * [Добавление учетной записи пользователя в узел](https://msdn.microsoft.com/library/dn820137.aspx) — имя пула и вычислительного узла указаны в универсальном коде ресурса (URI), а имя учетной записи и пароль передаются узлу в тексте этого запроса REST API.
    - Извлечение RDP-файла.
        * [BatchClient.PoolOperations.GetRDPFile](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getrdpfile.aspx) — в этом методе .NET требуется идентификатор пула, идентификатор узла и имя создаваемого RDP-файла.
        * [Извлечение файла из узла с помощью протокола удаленного рабочего стола](https://msdn.microsoft.com/library/dn820120.aspx) — для этого запроса REST API требуется имя пула и имя вычислительного узла. Ответ включает содержимое RDP-файла.
        * [Get-AzureBatchRDPFile](https://msdn.microsoft.com/library/mt149851.aspx) — этот командлет PowerShell извлекает RDP-файл из указанного вычислительного узла и сохраняет его в указанной папке или потоке.
2.	Некоторые приложения создают большое количество данных, которые может быть трудно обработать. Одним из способов устранения этой проблемы является использование [эффективных запросов списков](batch-efficient-list-queries.md).

<!---HONumber=September15_HO1-->