
<properties
	pageTitle="Автоматическое масштабирование вычислительных узлов в пуле пакетной службы Azure"
	description="Автоматическое масштабирование выполняется путем включения этой функции в пуле и связывания с пулом формулы. Формула используется для вычисления количества вычислительных узлов, которые необходимы для обработки приложения."
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
	ms.date="07/21/2015"
	ms.author="davidmu"/>

# Автоматическое масштабирование вычислительных узлов в пуле пакетной службы Azure

Автоматическое масштабирование вычислительных узлов в пуле пакетной службы Azure представляет собой динамическую настройку вычислительной мощности, используемой вашим приложением. Простота этой настройки сэкономит вам время и деньги. Для получения дополнительных сведений о вычислительных узлах и пулах см. статью [Технический обзор пакетной службы Azure](batch-technical-overview.md).

Автоматическое масштабирование происходит при включении этой функции в пуле, а также связывании формулы с пулом. Формула используется для определения количества вычислительных узлов, необходимого для обработки приложения. Автоматическое масштабирование задается во время создания пула. Также вы можете включить его позднее для существующего пула. Формула также может обновляться в пуле со включенной функцией автоматического масштабирования.

При включенном автоматическом масштабировании количество доступных вычислительных узлов корректируется каждые 15 минут на основе формулы. Формула опирается на выборки, которые собираются каждые 5 секунд. Между получением выборки и ее доступностью для формулы проходит 75 секунд. Эти временные факторы необходимо учитывать при использовании метода GetSample, описанного ниже.

Рекомендуется всегда оценивать формулу, прежде чем назначать ее пулу. Также важно отслеживать состояние выполнения автоматического масштабирования.

> [AZURE.NOTE]Каждая учетная запись пакетной службы Azure имеет ограничение на максимальное количество вычислительных узлов, которые могут использоваться для обработки. Поскольку система создает узлы в рамках этого ограничения, она может и не достичь целевого количества, заданного формулой.

## Определение формулы

Формула, которую вы определяете, используется для указания количества доступных вычислительных узлов в пуле для следующего интервала обработки. Формула представляет собой строку, которая не может превышать размер 8 КБ и которая может включать до 100 инструкций, разделенных точкой с запятой.

Инструкции в формуле указаны в виде выражений свободной формы. Эти выражения могут включать определяемые системой и пользователем переменные, постоянные значения, а также поддерживаемые операции с этими переменными или значениями.

	VAR = Function(System defined variables, user-defined variables);

Это позволяет объединять переменные для создания сложных формул.

	VAR₀ = Function₀(system-defined variables);
	VAR₁ = Function₁(system-defined variables, VAR₀);

### Переменные

В формуле можно использовать определяемые системой и пользователем переменные. Вы можете указывать значения этих определяемых системой переменных для управления вычислительными узлами в пуле.

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
    <td>$TVMDeallocationOption</td>
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

Доступные только для чтения значения определяемых системой переменных можно использовать, чтобы вносить изменения с учетом метрик вычислительных узлов в выборке.

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
    <td>$SampleTVMCount</td>
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

В формуле поддерживаются следующие типы:

- double
- doubleVec
- string
- Timestamp
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

Для перечисленных выше типов разрешены следующие операции.

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

Для определения формулы автоматического масштабирования доступны следующие предопределенные функции.

<table>
  <tr>
    <th>Функция</th>
    <th>Описание</th>
  </tr>
  <tr>
    <td>double avg(doubleVecList)</td>
    <td>Среднее значение для всех значений в doubleVecList.</td>
  </tr>
  <tr>
    <td>double len(doubleVecList)</td>
    <td>Длина вектора, созданного из doubleVecList.</td>
  <tr>
    <td>double lg(double)</td>
    <td>Логарифм по основанию&#160;2.</td>
  </tr>
  <tr>
    <td>doubleVec lg(doubleVecList)</td>
    <td>Покомпонентный логарифм с основанием&#160;2. Функция vec(double) должна явно передаваться для одного параметра типа double, иначе предполагается версия double lg(double).</td>
  </tr>
  <tr>
    <td>double ln(double)</td>
    <td>Натуральный логарифм.</td>
  </tr>
  <tr>
    <td>doubleVec ln(doubleVecList)</td>
    <td>Покомпонентный логарифм с основанием&#160;2. Функция vec(double) должна явно передаваться для одного параметра типа double, иначе предполагается версия double lg(double).</td>
  </tr>
  <tr>
    <td>double log(double)</td>
    <td>Логарифм по основанию&#160;10.</td>
  </tr>
  <tr>
    <td>doubleVec log(doubleVecList)</td>
    <td>Покомпонентный логарифм с основанием&#160;10. Функция vec(double) должна явно передаваться для одного параметра типа double, иначе предполагается версия double log(double).</td>
  </tr>
  <tr>
    <td>double max(doubleVecList)</td>
    <td>Максимальное значение в doubleVecList.</td>
  </tr>
  <tr>
    <td>double min(doubleVecList)</td>
    <td>Минимальное значение в doubleVecList.</td>
  </tr>
  <tr>
    <td>double norm(doubleVecList)</td>
    <td>2-норма вектора, созданного из doubleVecList.
  </tr>
  <tr>
    <td>double percentile(doubleVec v, double p)</td>
    <td>Элемент процентиля вектора&#160;v.</td>
  </tr>
  <tr>
    <td>double rand()</td>
    <td>Случайное значение от 0,0 до 1,0.</td>
  </tr>
  <tr>
    <td>double range(doubleVecList)</td>
    <td>Разница между минимальным и максимальным значением в doubleVecList.</td>
  </tr>
  <tr>
    <td>double std(doubleVecList)</td>
    <td>Стандартное отклонение выборки для значений в doubleVecList.</td>
  </tr>
  <tr>
    <td>stop()</td>
    <td>Остановка оценки выражений автоматического масштабирования.</td>
  </tr>
  <tr>
    <td>double sum(doubleVecList)</td>
    <td>Сумма всех компонентов doubleVecList.</td>
  </tr>
  <tr>
    <td>timestamp time(string dateTime="")</td>
    <td>Отметка времени для текущего времени, если параметры не переданы; отметка времени строки dateTime, если параметры переданы. Поддерживаемые форматы даты и времени: W3CDTF и RFC1123.</td>
  </tr>
  <tr>
    <td>double val(doubleVec v, double i)</td>
    <td>Значение элемента в расположении&#160;i в векторе&#160;v с начальным индексом&#160;0.</td>
  </tr>
  <tr>
    <td>doubleVec vec(doubleVecList)</td>
    <td>Явное создание одного типа doubleVec из doubleVecList.</td>
  </tr>
</table>

Некоторые функции, описанные в таблице, могут принимать список в качестве аргумента. Список значений, разделенных запятыми, — это любая комбинация типов double и doubleVec. Например:

	doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?

Значение doubleVecList преобразуется в один тип doubleVec перед оценкой. Например, если v = [1,2,3], то вызывающая функция avg(v) равна вызывающей функции avg(1,2,3), а вызывающая функция avg (v, 7) равная вызывающей функции avg(1,2,3,7).

### Пример данных

Определяемые системой переменные — это объекты, которые предоставляют методы для доступа к связанным данным. Например, следующее выражение иллюстрирует запрос для получения данных об использовании ЦП за последние пять минут.

	$CPUPercent.GetSample(TimeInterval_Minute*5)

Эти методы можно использовать для получения демонстрационных данных.

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
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime, (timestamp | timeinterval) endTime [, double samplePercent])</b>&#160;— задает интервал времени для сбора данных, т.&#160;е. начало и конец сбора.</p></li></ul></td>
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
    <p><b>doubleVec GetSamplePercent( (timestamp | timeinterval) startTime [, (timestamp | timeinterval) endTime] )</b>&#160;— поскольку метод GetSample не срабатывает, если возвращается меньший процент собранных данных, чем указано в параметре samplePercent, вы можете использовать методы GetSamplePercent для первой проверки, а затем выполнить другое действие, если журнал содержит недостаточное количество выборок, без прерывания оценки автоматического масштабирования.</p></td>
  </tr>
</table>

### Метрики

В формуле можно определить следующие метрики.

<table>
  <tr>
    <th>Metric (Метрика)</th>
    <th>Описание</th>
  </tr>
  <tr>
    <td>Ресурс</td>
    <td><p>Основывается на использовании ЦП, пропускной способности и памяти, а также на количестве вычислительных узлов. В формулах для управления вычислительными узлами в пуле используются следующие системные переменные, описанные выше:</p>
    <p><ul>
      <li>$TargetDedicated</li>
      <li>$TVMDeallocationOption</li>
    </ul></p>
    <p>Для внесения изменений с учетом метрик узла используются следующие системные переменные:</p>
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
    <p>В этом примере показана формула, которая используется для указания количества вычислительных узлов в пуле. А именно&#160;— до 110&#160;% от текущего целевого количества узлов, если минимальное среднее использование ЦП за последние 10&#160;минут превышает 70&#160;%:</p>
    <p><b>totalTVMs = (min($CPUPercent.GetSample(TimeInterval\_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;</b></p>
    <p>В этом примере показана формула, которая используется для указания количества вычислительных узлов в пуле. А именно&#160;— до 90&#160;% от текущего целевого количества узлов, если среднее использование ЦП за последние 60&#160;минут составляет менее 20&#160;%:</p>
    <p><b>totalTVMs = (avg($CPUPercent.GetSample(TimeInterval\_Minute*60)) &lt; 0.2) ? ($CurrentDedicated * 0.9) : totalTVMs;</b></p>
    <p>В этом примере задается целевое количество выделенных вычислительных узлов, не превышающее&#160;400:</p>
    <p><b>$TargetDedicated = min(400, totalTVMs);</b></p></td>
  </tr>
  <tr>
    <td>Задача</td>
    <td><p>С учетом состояния задач (активные, в ожидании и завершенные).</p>
    <p>Для внесения изменений с учетом метрик задач используются следующие системные переменные:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$SucceededTasks</li>
      <li>$FailedTasks</li>
      <li>$CurrentDedicated</li></ul></p>
    <p>В этом примере показана формула, которая определяет, были ли записаны 70&#160;% выборок за последние 15&#160;минут. В противном случае используется последняя выборка. Формула пытается увеличить количество вычислительных узлов, чтобы оно совпадало с количеством активных задач с максимальным значением&#160;3. Она задает количество узлов в одну четверть от количества активных задач, так как для свойства MaxTasksPerVM пула установлено значение&#160;4. Она также задает параметр освобождения для действия taskcompletion, гарантируя, что компьютер будет использоваться, пока задача не будет выполнена.</p>
    <p><b>$Samples = $ActiveTasks.GetSamplePercent(TimeInterval\_Minute * 15); $Tasks = $Samples &lt; 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval\_Minute * 15))); $Cores = $TargetDedicated * 4; $ExtraVMs = ($Tasks - $Cores) / 4; $TargetVMs = ($TargetDedicated+$ExtraVMs);$TargetDedicated = max(0,min($TargetVMs,3)); $TVMDeallocationOption = taskcompletion;</b></p></td>
  </tr>
</table>

## Оценка формулы автоматического масштабирования

Рекомендуется оценивать формулу, прежде чем использовать ее в приложении. Формула оценивается путем тестового выполнения для существующего пула. Для этого можно использовать один из следующих методов.

- [Метод IPoolManager.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/dn931617.aspx) или [метод IPoolManager.EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/dn931545.aspx) — для этих методов .NET требуется имя существующего пула и строка, которая содержит формулу автоматического масштабирования. Результаты вызова содержатся в экземпляре [класса AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx).
- [Оценка формулы автоматического масштабирования](https://msdn.microsoft.com/library/azure/dn820183.aspx) — в этой операции REST имя пула указывается в URI, а формула автоматического масштабирования указывается в элементе autoScaleFormula текста запроса. Ответ операции содержит все сведения об ошибках, которые могут быть связаны с формулой.

## Создание пула со включенным автоматическим масштабированием

Создайте пул, используя один из следующих способов.

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx) — этот командлет использует параметр AutoScaleFormula для определения формулы автоматического масштабирования.
- [Метод IPoolManager.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.ipoolmanager.createpool.aspx) — после вызова этого метода .NET для создания пула в пуле задается [свойство ICloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscaleenabled.aspx) и [свойство ICloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscaleformula.aspx) для включения автоматического масштабирования.
- [Добавление пула в учетную запись](https://msdn.microsoft.com/library/azure/dn820174.aspx) — в этом API REST используются элементы enableAutoScale и autoScaleFormula для настройки автоматического масштабирования для пула во время его создания.

> [AZURE.NOTE]Если вы настроили автоматическое масштабирование во время создания с помощью ресурсов, перечисленных выше, параметр targetDedicated не будет использоваться для пула.

## Включение автоматического масштабирования после создания пула

Если вы уже настроили пул с указанным количеством вычислительных узлов с помощью параметра targetDedicated, такой пул можно обновить позднее, настроив для него автоматическое масштабирование. Это можно сделать одним из следующих способов.

- [Метод IPoolManager.EnableAutoScale](https://msdn.microsoft.com/library/azure/dn931709.aspx) — для этого метода .NET требуется имя существующего пула и формула автоматического масштабирования.
- [Включение и отключение автоматического масштабирования](https://msdn.microsoft.com/library/azure/dn820173.aspx) — для этого API REST требуется имя существующего пула в URI и формула автоматического масштабирования в тексте запроса.

> [AZURE.NOTE]Значение, указанное для параметра targetDedicated во время создания пула, игнорируется во время оценки формулы автоматического масштабирования.

## Получение сведений о выполнении автоматического масштабирования

Следует периодически проверять результаты выполнения автоматического масштабирования. Это можно сделать одним из следующих способов.

- [Свойство ICloudPool.AutoScaleRun свойство](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscalerun.aspx) — при использовании библиотеки .NET это свойство пула предоставляет экземпляр [класса AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx), который предоставляет [свойство AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)[, свойство AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx) и [свойство AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx).
- [Получить сведения о пуле](https://msdn.microsoft.com/library/dn820165.aspx) — этот API REST возвращает сведения о пуле, содержащие результаты последнего выполнения автоматического масштабирования.

## Дальнейшие действия

1.	Вам может потребоваться получить доступ к вычислительным узлам, чтобы иметь возможность полностью оценить эффективность работы приложения. Чтобы воспользоваться преимуществами удаленного доступа, необходимо добавить учетную запись пользователя в вычислительный узел, к которому необходимо получить доступ, и извлечь RDP-файл из этого узла. Добавьте учетную запись пользователя одним из следующих способов.

	- [New-AzureBatchVMUser](https://msdn.microsoft.com/library/mt149846.aspx) — этот командлет принимает имя пула, имя вычислительного узла, имя учетной записи и пароль в качестве параметров.
	- [Метод IVM.CreateUser](https://msdn.microsoft.com/library/microsoft.azure.batch.ivm.createuser.aspx) — этот метод .NET создает экземпляр [интерфейса IUser](https://msdn.microsoft.com/library/microsoft.azure.batch.iuser.aspx), в котором для вычислительного узла можно задать имя пользователя и пароль.
	- [Добавление учетной записи пользователя в узел](https://msdn.microsoft.com/library/dn820137.aspx) — имя пула и вычислительного узла указаны в URI, а имя учетной записи и пароль передаются узлу в тексте запроса этого API REST.

		Извлечение RDP-файла.

	- [Метод IVM.GetRDPFile](https://msdn.microsoft.com/library/microsoft.azure.batch.ivm.getrdpfile.aspx) — для этого метода.NET требуется создать имя RDP-файла.
	- [Извлечение файла из узла с помощью протокола удаленного рабочего стола](https://msdn.microsoft.com/library/dn820120.aspx) — для этого API REST требуется имя пула и имя вычислительного узла. Ответ включает содержимое RDP-файла.
	- [Get-AzureBatchRDPFile](https://msdn.microsoft.com/library/mt149851.aspx) — этот командлет извлекает RDP-файл из указанного вычислительного узла и сохраняет его в указанной папке или потоке.
2.	Некоторые приложения создают большое количество данных, которые может быть трудно обработать. Одним из способов устранения этой проблемы является использование [эффективных списков запросов](batch-efficient-list-queries.md).

<!---HONumber=August15_HO6-->