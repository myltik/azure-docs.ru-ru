<properties
	pageTitle="Трассировка потока в приложении облачных служб с помощью системы диагностики Azure | Microsoft Azure"
	description="Добавление сообщений трассировки в приложения Azure для отладки, измерения производительности, мониторинга, анализа трафика и выполнения других задач."
	services="cloud-services"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="robb"/>



# Трассировка потока в приложении облачных служб с помощью системы диагностики Azure

Трассировка — это мониторинг выполнения запущенного приложения. Сведения об ошибках и выполнении приложения в журналы, текстовые файлы или на устройства для последующего анализа можно записывать с помощью классов [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx), [System.Diagnostics.Debug](https://msdn.microsoft.com/library/system.diagnostics.debug.aspx) и [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx). Дополнительные сведения о трассировке см. в разделе [Трассировка и инструментирование приложений](https://msdn.microsoft.com/library/zs6s4h68.aspx).


## Использование трассировочных операторов и переключателей

Чтобы реализовать трассировку в приложении облачных служб, добавьте класс [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) в конфигурацию приложения и вызовите метод System.Diagnostics.Trace или System.Diagnostics.Debug в коде приложения. Используйте файл конфигурации *app.config* для рабочих ролей и *web.config* для веб-ролей. Во время создания размещенной службы с помощью шаблона Visual Studio система диагностики Azure автоматически добавляется в проект. При этом класс DiagnosticMonitorTraceListener включается в соответствующий файл конфигурации для добавляемых ролей.

Дополнительную информацию о размещении трассировочных операторов см. в статье [Добавление трассировочных операторов в код приложения](https://msdn.microsoft.com/library/zd83saa2.aspx).

Процессом и масштабом трассировки можно управлять. Для этого добавьте в код [переключатели трассировки](https://msdn.microsoft.com/library/3at424ac.aspx). Так вы сможете отслеживать состояние приложения в рабочей среде. Это особенно важно в бизнес-приложениях, которые используют различные компоненты, выполняющиеся на нескольких компьютерах. Дополнительные сведения см. в статье [Настройка переключателей трассировки](https://msdn.microsoft.com/library/t06xyy08.aspx).

## Настройка прослушивателя трассировки в приложении Azure

Использование элементов Trace, Debug и TraceSource предполагает настройку прослушивателей для сбора и записи отправляемых сообщений. Прослушиватели собирают, хранят и перенаправляют сообщения трассировки. Они направляют выходные данные трассировки в соответствующий целевой объект, например журнал, окно или текстовый файл. Система диагностики Azure использует класс [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx).

Перед выполнением следующей процедуры необходимо инициализировать монитор диагностики Azure. Информацию о том, как это сделать, см. в статье [Включение диагностики в Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Обратите внимание, что при использовании шаблонов Visual Studio конфигурация прослушивателя добавляется автоматически.


### Добавление прослушивателя трассировки

1. Откройте файл web.config или app.config для своей роли.
2. Добавьте в файл следующий код. Измените атрибут Version, чтобы использовать номер версии сборки, которая указана в ссылках. Версия сборки не обязательно меняется с каждым выпуском пакета SDK для Azure, а только если она обновляется.

	```
	<system.diagnostics>
		<trace>
			<listeners>
				<add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
		          Microsoft.WindowsAzure.Diagnostics,
		          Version=2.8.0.0,
		          Culture=neutral,
		          PublicKeyToken=31bf3856ad364e35"
		          name="AzureDiagnostics">
			  	  <filter type="" />
				</add>
			</listeners>
		</trace>
	</system.diagnostics>
	```
	>[AZURE.IMPORTANT] Убедитесь, что у вас есть ссылка проекта на сборку Microsoft.WindowsAzure.Diagnostics. Обновите номер версии в приведенном выше коде XML в соответствии с версией сборки Microsoft.WindowsAzure.Diagnostics, используемой в ссылке.

3. Сохраните файл конфигурации.

Дополнительные сведения о прослушивателях см. в статье [Trace Listeners](https://msdn.microsoft.com/library/4y5y10s7.aspx).

Добавив прослушиватель, вы можете добавить в код трассировочные операторы.


### Добавление трассировочного оператора в код

1. Откройте исходный файл приложения. Например, файл <RoleName>.cs для рабочей роли или веб-роли.
2. Добавьте следующий оператор using, если он еще не добавлен:
	```
	    using System.Diagnostics;
	```
3. Добавьте трассировочные операторы, чтобы записывать сведения о состоянии приложения. Вывод оператора трассировки можно форматировать разными способами. Дополнительную информацию об этом см. в статье [How to: Add Trace Statements to Application Code](https://msdn.microsoft.com/library/zd83saa2.aspx).
4. Сохраните исходный файл.

<!---HONumber=AcomDC_0302_2016-->