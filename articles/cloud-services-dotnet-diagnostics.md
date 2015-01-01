<properties urlDisplayName="Diagnostics" pageTitle="Как использовать диагностику (.NET) - руководство по компонентам Azure" metaKeywords="Azure diagnostics monitoring,logs crash dumps C#" description="Learn how to use diagnostic data in Azure for debugging, measuring performance, monitoring, traffic analysis, and more." metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Enabling Diagnostics in Azure" authors="raynew" solutions="" manager="johndaw" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/23/2014" ms.author="raynew" />



# Включение диагностики в облачных службах и виртуальных машинах Azure

Система диагностики Azure 1.2 позволяет собирать диагностические данные рабочей роли, веб-роли или виртуальной машины, запущенной в Azure. В этом руководстве описывается, как использовать систему диагностики Azure 1.2. Подробнее о создании стратегии ведения журналов и трассировки и об использовании средств диагностики и других методик для устранения неполадок см. в разделе [Советы и рекомендации по устранению неполадок при разработке приложений Azure][].

## Оглавление

-   [Обзор][]
-   [Как включить диагностику в рабочей роли][]
-   [Как включить диагностику в виртуальной машине][]
-   [Пример файла конфигурации и схемы][]
-   [Устранение неполадок][]
-   [Часто задаваемые вопросы][]
-   [Сравнение систем диагностики Azure версии 1.0 и 1.2][]
-   [Дополнительные ресурсы][]

<h2><a name="overview"></a>Overview</h2>

Система диагностики Azure 1.2 - это расширение Azure, которое позволяет собирать диагностические данные телеметрии рабочей роли, веб-роли или виртуальной машины, запущенной в Azure. Данные телеметрии сохраняются в учетной записи хранения Azure. Их можно использовать для отладки и устранения неполадок, измерения производительности, мониторинга использования ресурсов, анализа трафика и планирования емкости, а также для аудита. 

Если вы уже использовали систему диагностики версии 1.0, то отметите три значительных отличия по сравнению с версией 1.2:

1.	Систему диагностики 1.2 можно разворачивать на виртуальных машинах, в дополнение к облачным службам.
2.	Система диагностики 1.0 входит в состав пакета SDK для Azure, и она разворачивается при развертывании облачной службы. Система диагностики 1.2 - это расширение, которое разворачивается отдельно.
3.	С помощью системы диагностики 1.2 можно собирать данные трассировки событий Windows и событий .NET EventSource.

Более подробное сравнение см. в разделе [Сравнение систем диагностики Azure версии 1.0 и 1.2][] в конце этой статьи.

Система диагностики Azure может собирать телеметрию следующих типов:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>Источник данных</strong></td>
			<td><strong>Описание</strong></td>
	</tr>
	<tr>
		<td>Журналы IIS</td>
		<td>Информация о веб-сайтах IIS.</td>            
	</tr>
	<tr>
		<td>Журналы инфраструктуры системы диагностики Azure</td>
		<td>Информация о самой системе диагностики.</td>            
	</tr>
	<tr>
		<td>Журналы неудачно завершенных запросов IIS	</td>
		<td>Информация о неудачно завершенных запросах, отправленных на сайт или в приложение IIS.</td>            
	</tr>
	<tr>
		<td>Журналы событий Windows</td>
		<td>Информация, отправляемая системой ведения журналов событий Windows.</td>            
	</tr>
	<tr>
		<td>Счетчики производительности</td>
		<td>Системные и пользовательские счетчики производительности.</td>            
	</tr>
	<tr>
		<td>Аварийные дампы</td>
		<td>Информация о состоянии процесса в случае сбоя приложения.</td>            
	</tr>
	<tr>
		<td>Пользовательские журналы ошибок</td>
		<td>Журналы, созданные вашим приложением или службой.</td>            
	</tr>
	<tr>
		<td>.NET EventSource</td>
		<td>События, созданные вашим кодом с использованием класса <a href="http://msdn.microsoft.com/ru-ru/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx">EventSource .NET</a>.</td>            
	</tr>
	<tr>
		<td>Трассировка событий Windows на основе манифестов</td>
		<td>События трассировки событий Windows, созданные каким-либо процессом.</td>            
	</tr>
		
</tbody>
</table>

<h2><a name="worker-role"></a>Как включить диагностику в рабочей роли</h2>

В этом пошаговом руководстве описывается, как реализовать рабочую роль Azure, которая передает данные телеметрии с помощью класса EventSource .NET. Система диагностики Azure используется для сбора данных телеметрии и хранения их в учетной записи хранения Azure. При создании рабочей роли Visual Studio автоматически включает систему диагностики 1.0 как часть решения. В следующих указаниях описывается процесс создания рабочей роли, отключение системы диагностики 1.0 в решении и развертывание системы диагностики 1.2 в рабочей роли.

<h3>Предварительные требования</h3>
В данной статье предполагается, что у вас есть подписка Azure и вы используете Visual Studio 2013 с пакетом SDK для Azure. Если у вас нет подписки Azure, можно зарегистрироваться для получения [бесплатной пробной версии][]. Следует обязательно [установить и настроить Azure PowerShell версии 0.8.7 или более поздней][].

<h3>Шаг 1. Создание рабочей роли</h3>
1.	Запустите **Visual Studio 2013**.
2.	Создайте новый проект **облачной службы Microsoft Azure** из шаблона **Cloud**, предназначенного для .NET Framework 4.5.  Назовите проект "WadExample".
3.	Выберите пункт **Рабочая роль**.
4.	В **обозревателе решений** дважды щелкните файл свойств **WorkerRole1**.
5.	На вкладке **Настройка** снимите флажок **Включить диагностику**, чтобы отключить систему диагностики 1.0.
6.	Создайте свое решение, чтобы проверить, что в нем нет ошибок.

<h3>Шаг 2. Инструментирование кода</h3>
Замените содержимое файла WorkerRole.cs следующим кодом. Класс SampleEventSourceWriter, унаследованный из [класса EventSource][], реализует четыре метода ведения журнала: **SendEnums**, **MessageMethod**, **SetOther** и **HighFreq**. Первый параметр для метода **WriteEvent** определяет идентификатор соответствующего события. Метод Run реализует бесконечный цикл, который вызывает каждый из методов ведения журнала, реализованных в классе **SampleEventSourceWriter**, каждые 10 секунд.

	using Microsoft.WindowsAzure.ServiceRuntime;
	using System;
	using System.Diagnostics;
	using System.Diagnostics.Tracing;
	using System.Net;
	using System.Threading;

	namespace WorkerRole1
	{
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections 
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
	}


<h3>Шаг 3. Развертывание рабочей роли</h3>
1.	Разверните свою рабочую роль в Azure из Visual Studio, выбрав проект **WadExample**, а затем выбрав **Опубликовать** в меню **Сборка**.
2.	Выберите свою подписку.
3.	В диалоговом окне **Параметры публикации Microsoft Azure** выберите **<Создать...>**.
4.	В диалоговом окне **Создание облачной службы и учетной записи хранения** введите **Имя** (например, "WadExample") и выберите регион или территориальную группу.
5.	Выберите для параметра **Среда** значение **Промежуточная**.
6.	Измените любые другие **Параметры** по необходимости и щелкните **Опубликовать**.
7.	После завершения развертывания проверьте на портале Azure, что облачная служба находится в состоянии **Выполняется**.

<h3>Шаг 4. Создание файла конфигурации системы диагностики и установка расширения</h3>
1.	Скачайте общедоступное определение схемы файла конфигурации, выполнив следующую команду PowerShell:
2.	
		(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

2.	Добавьте XML-файл в свой проект **WorkerRole1**, щелкнув правой кнопкой мыши проект **WorkerRole1** и выбрав **Добавить** -> **Новый элемент...** -> **элементы Visual C#** -> **Данные** -> **XML-файл**. Назовите файл "WadExample.xml".

	![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)

3.	Свяжите файл WadConfig.xsd с файлом конфигурации. Убедитесь, что окно редактора WadExample.xml активно. Нажмите клавишу **F4**, чтобы открыть окно **Свойства**. Щелкните свойство **Schemas** в окне **Свойства**. Щелкните **...** в свойстве **Schemas**. Нажмите кнопку **Добавить...**, перейдите к расположению, где сохранен XSD-файл, и выберите файл WadConfig.xsd. Нажмите кнопку **ОК**.
4.	Замените содержимое файла настройки WadExample.xml приведенным кодом XML и сохраните файл. Этот файл конфигурации определяет пару счетчиков производительности: один - для использование ЦП, и один - для использования памяти. Затем конфигурация определяет четыре события, соответствующие методам в классе SampleEventSourceWriter.

		<?xml version="1.0" encoding="utf-8"?>
		<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  			<WadCfg>
    			<DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      			<PerformanceCounters scheduledTransferPeriod="PT1M">
        			<PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        			<PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      				</PerformanceCounters>
      				<EtwProviders>
        				<EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          					<Event id="1" eventDestination="EnumsTable"/>
          					<Event id="2" eventDestination="MessageTable"/>
          					<Event id="3" eventDestination="SetOtherTable"/>
          					<Event id="4" eventDestination="HighFreqTable"/>
          					<DefaultEvents eventDestination="DefaultTable" />
        				</EtwEventSourceProviderConfiguration>
      				</EtwProviders>
    			</DiagnosticMonitorConfiguration>
  			</WadCfg>
		</PublicConfig>

<h3>Шаг 5. Установка системы диагностики в рабочей роли</h3>
Командлеты PowerShell для управления системой диагностики в веб-роли или рабочей роли: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension и Remove-AzureServiceDiagnosticsExtension.

1.	Откройте Microsoft Azure PowerShell.
2.	Выполните сценарий для установки системы диагностики в своей рабочей роли (замените StorageAccountKey ключом для своей учетной записи хранения wadexample):

		$storage_name = "wadexample"
		$key = "<StorageAccountKey>"
		$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
		$service_name="wadexample"
		$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key 
		Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging


<h3>Шаг 6. Просмотр данных телеметрии</h3>
В **обозревателе сервера** Visual Studio перейдите к учетной записи хранения wadexample. После того, как облачная служба работает в течение примерно 5 минут, должны отобразиться таблицы **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** и **WADSetOtherTable**. Дважды щелкните одну из таблиц, чтобы просмотреть собранную телеметрию.
	![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

<h2><a name="virtual-machine"></a>Как включить диагностику в виртуальной машине</h2>

Мы рассмотрим, как удаленно установить систему диагностики на виртуальной машине Azure с компьютера разработчика. Также вы узнаете, как реализовать приложение, которое выполняется на виртуальной машине Azure и отправляет данные телеметрии через класс [EventSource][] .NET. Система диагностики Azure используется для сбора телеметрии и хранения ее в учетной записи хранения Azure.

<h3>Предварительные требования</h3>
В этом пошаговом учебнике предполагается, что у вас есть подписка Azure и вы используете Visual Studio 2013 с пакетом SDK для Azure. Если у вас нет подписки Azure, можно зарегистрироваться для получения [бесплатной пробной версии][]. Следует обязательно [установить и настроить Azure PowerShell версии 0.8.7 или более поздней][].

<h3>Шаг 1. Создание виртуальной машины</h3>
1.	На компьютере разработчика запустите Visual Studio 2013.
2.	В **обозревателе сервера** Visual Studio правой кнопкой мыши щелкните **Microsoft Azure**, затем выберите **Виртуальная машина** -> **Создание виртуальной машины**.
3.	Выберите подписку Azure в диалоговом окне **Выберите подписку** и щелкните **Далее**.
4.	В диалоговом окне **Выбор образа виртуальной машины** выберите **Windows Server 2012 R2 Datacenter** и щелкните **Далее**.
5.	В **базовых параметрах виртуальной машины** укажите для виртуальной машины имя "wadexample". Укажите имя пользователя и пароль администратора и щелкните **Далее**.
6.	В диалоговом окне **Параметры облачной службы** создайте новую облачную службу с именем "wadexampleVM". Создайте новую учетную запись хранения с именем "wadexample" и щелкните **Далее**.
7.	Щелкните **Создать**.

<h3>Шаг 2. Создание приложения</h3>
1.	На компьютере разработчика запустите Visual Studio 2013.
2.	Создайте новое приложение консоли Visual C#, предназначенное для платформы .NET Framework 4.5. Назовите проект "WadExampleVM".
	![CloudServices_diag_new_project](./media/cloud-services-dotnet-diagnostics/NewProject.png)
3.	Замените содержимое файла Program.cs на код, приведенный ниже. Класс **SampleEventSourceWriter** реализует четыре метода ведения журнала: **SendEnums**, **MessageMethod**, **SetOther** и **HighFreq**. Первый параметр для метода WriteEvent определяет идентификатор соответствующего события. Метод Run реализует бесконечный цикл, который вызывает каждый из методов ведения журнала, реализованных в классе **SampleEventSourceWriter**, каждые 10 секунд.

		using System;
		using System.Diagnostics;
		using System.Diagnostics.Tracing;
		using System.Threading;

		namespace WadExampleVM
		{
    	sealed class SampleEventSourceWriter : EventSource
    	{
        	public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        	public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        	public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        	public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        	public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    	}

    	enum MyColor
    	{
        	Red,
        	Blue,
        	Green
    	}

    	[Flags]
    	enum MyFlags
    	{
        	Flag1 = 1,
        	Flag2 = 2,
        	Flag3 = 4
    	}

    	class Program
    	{
        static void Main(string[] args)
        {
            Trace.TraceInformation("My application entry point called");
            
            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }

        }
    	}
		}


4.	Сохраните файл и в меню **Сборка** выберите **Собрать решение**, чтобы выполнить сборку кода.


<h3>Шаг 3. Развертывание приложения</h3>
1.	В **обозревателе решений** щелкните правой кнопкой мыши проект **WadExampleVM** и выберите **Открыть папку** в обозревателе файлов.
2.	Перейдите к папке *bin\Debug* и скопируйте все файлы (WadExampleVM.*)
3.	В **обозревателе сервера** щелкните правой кнопкой мыши виртуальную машину и выберите **Подключиться с помощью удаленного рабочего стола**.
4.	После подключения к виртуальной машине создайте папку с именем WadExampleVM и вставьте в нее свои файлы приложения.
5.	Запустите приложение WadExampleVM.exe. Должно отобразиться пустое окно консоли.

<h3>Шаг 4. Создание конфигурации системы диагностики и установка расширения</h3>
1.	Скачайте общедоступное определение схемы файла конфигурации на свой компьютер для разработки, выполнив следующую команду PowerShell:

		(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

2.	Откройте новый XML-файл в Visual Studio либо в уже открытом проекте, либо в экземпляре Visual Studio без открытых проектов. В Visual Studio выберите **Добавить** -> **Новый элемент...** -> **элементы Visual C#** -> **Данные** -> **XML-файл**. Назовите файл "WadExample.xml".
3.	Свяжите файл WadConfig.xsd с файлом конфигурации. Убедитесь, что окно редактора WadExample.xml активно. Нажмите клавишу **F4**, чтобы открыть окно **Свойства**. Щелкните свойство **Schemas** в окне **Свойства**. Щелкните **...** в свойстве **Schemas**. Нажмите кнопку **Добавить...**, перейдите к расположению, где сохранен XSD-файл, и выберите файл WadConfig.xsd. Нажмите кнопку **ОК**.
4.	Замените содержимое файла настройки WadExample.xml приведенным кодом XML и сохраните файл. Этот файл конфигурации определяет пару счетчиков производительности: один - для использование ЦП, и один - для использования памяти. Затем конфигурация определяет четыре события, соответствующие методам в классе SampleEventSourceWriter.

		<?xml version="1.0" encoding="utf-8"?>
		<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  			<WadCfg>
    			<DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      			<PerformanceCounters scheduledTransferPeriod="PT1M">
        			<PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        			<PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      				</PerformanceCounters>
      				<EtwProviders>
        				<EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          					<Event id="1" eventDestination="EnumsTable"/>
          					<Event id="2" eventDestination="MessageTable"/>
          					<Event id="3" eventDestination="SetOtherTable"/>
          					<Event id="4" eventDestination="HighFreqTable"/>
          					<DefaultEvents eventDestination="DefaultTable" />
        				</EtwEventSourceProviderConfiguration>
      				</EtwProviders>
    			</DiagnosticMonitorConfiguration>
  			</WadCfg>
		</PublicConfig>


<h3>Шаг 5. Удаленная установка системы диагностики на виртуальной машине Azure </h3>
Для управления системой диагностики на виртуальной машине используются следующие командлеты PowerShell: Set-AzureVMDiagnosticsExtension, Get-AzureVMDiagnosticsExtension и Remove-AzureVMDiagnosticsExtension.

1.	На компьютере разработчика откройте Microsoft Azure PowerShell.
2.	Выполните сценарий для удаленной установки системы диагностики в своей виртуальной машине (замените *StorageAccountKey* ключом для учетной записи хранения wadexamplevm):

		$storage_name = "wadexamplevm"
		$key = "<StorageAccountKey>"
		$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"
		$service_name="wadexamplevm"
		$vm_name="WadExample"
		$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key 
		$VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
		$VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
		$VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM


<h3>Шаг 6. Просмотр данных телеметрии</h3>
В **обозревателе сервера** Visual Studio перейдите к учетной записи хранения wadexample. После того, как виртуальная машина работает в течение примерно 5 минут, должны отобразиться таблицы **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** и **WADSetOtherTable**. Дважды щелкните одну из таблиц, чтобы просмотреть собранную телеметрию.
	![CloudServices_diag_wadexamplevm_tables](./media/cloud-services-dotnet-diagnostics/WadExampleVMTables.png)

<h2><a name="configuration-file-schema"></a>Схема файла конфигурации</h2>

Файл конфигурации системы диагностики определяет значения, которые используются для инициализации параметров конфигурации диагностики, когда запускается монитор диагностики. Пример файла конфигурации и подробная документация его схемы расположена здесь: [Azure Diagnostics 1.2 Configuration Schema][] (Схема конфигурации системы диагностики Azure 1.2).

<h2><a name="troubleshooting"></a>Устранение неполадок</h2>

<h3>Система диагностики Azure не запускается</h3>
Система диагностики состоит из двух компонентов: подключаемого модуля гостевого агента и агента мониторинга. Файлы журнала подключаемого модуля гостевого агента расположены в файле: 

*%SystemDrive%\ WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<DiagnosticsVersion>*\CommandExecution.log

Подключаемый модуль возвращает следующие коды ошибок:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>Код выхода</strong></td>
			<td><strong>Описание</strong></td>
	</tr>
    <tr>
		<td>0</td>
		<td>Успешно.</td>            
	</tr>
    <tr>
		<td>-1</td>
        <td>Общая ошибка.</td>		            
	</tr>
    <tr>
		<td>-2</td>
        <td><p>Не удалось загрузить RCF-файл.</p>
<p>Это внутренняя ошибка, которая может возникнуть, только если подключаемый модуль гостевого агента вызван вручную, неправильно и на виртуальной машине.</p></td>		            
	</tr>
    <tr>
		<td>-3</td>
        <td><p>Не удалось загрузить файл конфигурации системы диагностики.</p>
<p>Решение. Такой результат возникает, если файл конфигурации не прошел проверку схемы. Следует предоставить файл конфигурации, соответствующий схеме.</p></td>		            
	</tr>
    <tr>
		<td>-4</td>
        <td><p>Другой экземпляр системы диагностики агента мониторинга уже использует локальный каталог ресурсов.</p>
<p>Решение. Укажите другое значение для <strong>LocalResourceDirectory</strong>.</p></td>		            
	</tr>
    <tr>
		<td>-6</td>
        <td><p>Средство запуска подключаемого модуля гостевого агента попыталось запустить систему диагностики c использованием недопустимой командной строки.</p>
<p>Это внутренняя ошибка, которая может возникнуть, только если подключаемый модуль гостевого агента вызван вручную, неправильно и на виртуальной машине.</p></td>		            
	</tr>
    <tr>
		<td>-10</td>
        <td>Подключаемый модуль системы диагностики был завершен с необработанным исключением.</td>		            
	</tr>
    <tr>
		<td>-11</td>
        <td><p>Гостевому агенту не удалось создать процесс, ответственный за запуск и мониторинг агента мониторинга.</p>

<p>Решение. Убедитесь, что для запуска новых процессов достаточно системных ресурсов.</p></td>		            
	</tr>
    <tr>
		<td>-101</td>
        <td><p>Недопустимые аргументы при вызове подключаемого модуля системы диагностики.</p>

<p>Это внутренняя ошибка, которая может возникнуть, только если подключаемый модуль гостевого агента вызван вручную, неправильно и на виртуальной машине.</p></td>		            
	</tr>
    <tr>
		<td>-102</td>
        <td><p>Процесс подключаемого модуля не может инициализироваться.</p> 

<p>Решение. Убедитесь, что для запуска новых процессов достаточно системных ресурсов.</p></td>		            
	</tr>
    <tr>
		<td>-103</td>
        <td><p>Процесс подключаемого модуля не может инициализироваться. А именно: не удается создать объект средства ведения журнала.</p>

<p>Решение. Убедитесь, что для запуска новых процессов достаточно системных ресурсов.</p></td>		            
	</tr>
    <tr>
		<td>-104</td>
        <td><p>Не удалось загрузить RCF-файл, предоставленный гостевым агентом.</p>

<p>Это внутренняя ошибка, которая может возникнуть, только если подключаемый модуль гостевого агента вызван вручную, неправильно и на виртуальной машине.</p></td>		            
	</tr>
    <tr>
		<td>-105</td>
        <td><p>Подключаемый модуль системы диагностики не может открыть файл конфигурации системы диагностики.</p>

<p>Это внутренняя ошибка, которая может возникать, только если подключаемый модуль системы диагностики вызван вручную, неправильно и на виртуальной машине.</p></td>		            
	</tr>
    <tr>
		<td>-106</td>
        <td><p>Не удалось прочитать файл конфигурации системы диагностики.</p>

<p>Решение. Такой результат возникает, если файл конфигурации не прошел проверку схемы. Решением будет предоставить файл конфигурации, соответствующий схеме. Вы можете найти XML-файл, переданный в расширение системы диагностики, в папке <i>%SystemDrive%\WindowsAzure\Config</i> на виртуальной машине. Откройте соответствующий XML-файл и выполните поиск <strong>Microsoft.Azure.Diagnostics</strong>, а затем поиск поля <strong>xmlCfg</strong> . Данные имеют кодировку base64, поэтому вам необходимо будет <a href="http://www.bing.com/search?q=base64+decoder">их декодировать,</a> чтобы увидеть XML-файл, который был загружен системой диагностики.</p></td>		            
	</tr>
    <tr>
		<td>-107</td>
        <td><p>Недопустимая передача каталога ресурсов в агент мониторинга.</p>

<p>Это внутренняя ошибка, которая может возникать, только если агент мониторинга вызван вручную, неправильно и на виртуальной машине.</p></td>		            
	</tr>
    <tr>
		<td>-108	</td>
        <td><p>Не удалось преобразовать файл конфигурации системы диагностики в файл конфигурации агента мониторинга.</p>

<p>Это внутренняя ошибка, которая может возникать, только если подключаемый модуль системы диагностики вызван с помощью недопустимого файла конфигурации.</p></td>		            
	</tr>
    <tr>
		<td>-110</td>
        <td><p>Общая ошибка конфигурации системы диагностики.</p>

<p>Это внутренняя ошибка, которая может возникать, только если подключаемый модуль системы диагностики вызван с помощью недопустимого файла конфигурации.</p></td>		            
	</tr>
    <tr>
		<td>-111</td>
        <td><p>Не удалось запустить агент мониторинга.</p>

<p>Решение. Убедитесь, что системных ресурсов достаточно.</p></td>		            
	</tr>
    <tr>
		<td>-112</td>
        <td>Общая ошибка</td>		            
	</tr>    
</tbody>
</table>

<h3>Журнал данных диагностики не записывается в службу хранилища</h3>
Наиболее распространенная причина недостающих данных событий - это некорректно определенная информация об учетной записи хранения. 

Решение. Исправьте свой файл конфигурации системы диагностики или переустановите систему диагностики.
До того, как данные событий передаются в вашу учетную запись хранения, они сохраняются в папке. См. выше дополнительную информацию о **LocalResourceDirectory**.

Если в этой папке нет файлов, агент мониторинга не сможет запуститься. Обычно причина этого - недопустимый файл конфигурации, что должно быть записано в файл CommandExecution.log. Если агент мониторинга успешно собирает данные событий, то вы увидите TSF-файлы для каждого события, определенного в файле конфигурации.

Агент мониторинга регистрирует любые обнаруженные ошибки в файле MaEventTable.tsf. Чтобы проверить содержимое этого файла, выполните следующую команду:

		%SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.[IaaS | PaaS]Diagnostics\1.2.0.0\Monitor\x64\table2csv maeventtable.tsf

Этот инструмент создает файл maeventtable.csv, который можно открыть, чтобы просмотреть журналы на предмет сбоев.


<h2><a name="faq"></a>Часто задаваемые вопросы</h2>
Ниже приводятся ответы на часто задаваемые вопросы.

**В.** Как обновить мое решение Visual Studio с системы диагностики Azure 1.0 до системы диагностики Azure 1.1?

**О.** Обновление решения Visual Studio с системы диагностики 1.0 до 1.1 (или более поздней версии) - это ручной процесс:
- Отключите диагностику в своем решении Visual Studio, чтобы система диагностики 1.0 не развертывалась с вашей ролью.
- Если в коде используется прослушиватель трассировки, следует изменить код для использования .NET EventSource. Система диагностики 1.1 и более поздние версии не поддерживают прослушиватель трассировки.
- Измените процесс развертывания, чтобы устанавливалось расширение системы диагностики 1.1

**В.** Если у меня уже установлено расширение системы диагностики 1.1 в моей роли или виртуальной машине, как мне обновиться до системы диагностики 1.2?

**О.** Если при установке системы диагностики 1.1 вы указали "-Version "1.*"", при следующем перезапуске роли или при перезагрузке виртуальной машины она будет автоматически обновлена до последней версии, которая соответствует регулярному выражению "1.*" Если при установке системы диагностики 1.1 вы указали "-Version "1.1"", вы можете обновить ее до более новой версии, повторно выполнив командлет Set- и указав версию, которую хотите установить.

**В.** Как именуются таблицы?

**О.** Таблицы именуются следующим образом:

		if (String.IsNullOrEmpty(eventDestination)) {
		    if (e == "DefaultEvents")
		        tableName = "WADDefault" + MD5(provider);
		    else
		        tableName = "WADEvent" + MD5(provider) + eventId;
		}
		else
		    tableName = "WAD" + eventDestination;

Пример:

		<EtwEventSourceProviderConfiguration provider="prov1">
		  <Event id="1" />
		  <Event id="2" eventDestination="dest1" />
		  <DefaultEvents />
		</EtwEventSourceProviderConfiguration>
		<EtwEventSourceProviderConfiguration provider="prov2">
		  <DefaultEvents eventDestination="dest2" />
		</EtwEventSourceProviderConfiguration>

Это создает четыре таблицы:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>Событие</strong></td>
			<td><strong>Имя таблицы</strong></td>			
	</tr>
	<tr>
			<td>provider="prov1" &lt;Event id="1" /&gt;</td>
			<td>WADEvent+MD5("prov1")+"1"</td>			
	</tr>
	<tr>
			<td>provider="prov1" &lt;Event id="2" eventDestination="dest1" /&gt;</td>
			<td>WADdest1</td>			
	</tr>
	<tr>
			<td>provider="prov1" &lt;DefaultEvents /&gt;</td>
			<td>WADDefault+MD5("prov1")</td>			
	</tr>
	<tr>
			<td>provider="prov2" &lt;DefaultEvents eventDestination="dest2" /&gt;</td>
			<td>WADdest2</td>			
	</tr>
	

</table>
</tbody>

<h2><a name="comparing"></a>Сравнение систем диагностики Azure версии 1.0 и 1.2</h2>

В следующей таблице сравниваются функции, которые поддерживаются системами диагностики Azure версий 1.0 и 1.1 или 1.2:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>Поддерживаемые типы ролей</strong></td>
			<td><strong>Система диагностики 1.0</strong></td>
			<td><strong>Система диагностики 1.1 или 1.2</strong></td>
	</tr>

	<tr>
			<td>Веб-роль</td>
			<td>Да</td>
			<td>Да</td>
	</tr>
	<tr>
			<td>Рабочая роль</td>
			<td>Да</td>
			<td>Да</td>
	</tr>
	<tr>
			<td>IaaS</td>
			<td>Нет</td>
			<td>Да</td>
	</tr>
</tbody>
</table>

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>Конфигурация и развертывание</strong></td>
			<td><strong>Система диагностики 1.0</strong></td>
			<td><strong>Система диагностики 1.1 или 1.2</strong></td>
	</tr>

	<tr>
			<td>Интеграция с Visual Studio: интегрирована в средства разработки веб-ролей и рабочих ролей Azure.</td>
			<td>Да</td>
			<td>Нет</td>
	</tr>
	<tr>
			<td>Сценарии PowerShell: сценарии для управления установкой и настройкой системы диагностики в роли.</td>
			<td>Да</td>
			<td>Да</td>
	</tr>
	
</tbody>
</table>

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>Источник данных</strong></td>
			<td><strong>Коллекция по умолчанию</strong></td>
			<td><strong>Формат</strong></td>
			<td><strong>Описание</strong></td>
			<td><strong>Система диагностики 1.0</strong></td>
			<td><strong>Система диагностики 1.1 или 1.2</strong></td>
	</tr>
	<tr>
			<td>Журналы System.Diagnostics.Trace</td>
			<td>Да</td>
			<td>Таблица</td>
			<td>Записывает в журнал сообщения трассировки, отправленные из кода в прослушиватель трассировки (его необходимо добавить к файлу web.config или app.config). Данные журнала переносятся с указанным интервалом scheduledTransferPeriod в таблицу службы хранилища WADLogsTable.</td>
			<td>Да</td>
			<td>Нет (используется EventSource)</td>
	</tr>
	<tr>
			<td>Журналы IIS</td>
			<td>Да</td>
			<td>BLOB-объект</td>
			<td>Ведение журналов информации про узлы IIS. Данные журнала переносятся с указанным интервалом scheduledTransferPeriod в указанный вами контейнер.</td>
			<td>Да</td>
			<td>Да</td>
	</tr>
	<tr>
			<td>Журналы инфраструктуры системы диагностики Azure</td>
			<td>Да</td>
			<td>Таблица</td>
			<td>Ведение журнала информации об инфраструктуре диагностики, а также модулях RemoteAccess и RemoteForwarder. Данные журнала переносятся с указанным интервалом scheduledTransferPeriod в таблицу службы хранилища WADDiagnosticInfrastructureLogsTable.</td>
			<td>Да</td>
			<td>Да</td>
	</tr>
	<tr>
			<td>Журналы неудачно завершенных запросов IIS</td>
			<td>Нет</td>
			<td>BLOB-объект</td>
			<td>Ведение журнала информации о неудачно завершенных запросах, отправленных на сайт или в приложение IIS. Вам также необходимо включить, задав параметры трассировки в разделе system.WebServer файла Web.config. Данные журнала переносятся с указанным интервалом scheduledTransferPeriod в указанный вами контейнер.</td>
			<td>Да</td>
			<td>Yes</td>
	</tr>
	<tr>
			<td>Журналы событий Windows</td>
			<td>Нет</td>
			<td>Таблица</td>
			<td>Ведение журнала информации о том, насколько хорошо работает операционная система, приложение или драйвер. Счетчики производительности должны быть указаны явным образом. Когда они добавлены, данные о производительности переносятся с указанным интервалом scheduledTransferPeriod в таблицу службы хранилища WADPerformanceCountersTable.</td>
			<td>Да</td>
			<td>Да</td>
	</tr>
	<tr>
			<td>Счетчики производительности</td>
			<td>Нет</td>
			<td>Таблица</td>
			<td>Ведение журнала информации о том, насколько хорошо работает операционная система, приложение или драйвер. Счетчики производительности должны быть указаны явным образом. Когда они добавлены, данные о производительности переносятся с указанным интервалом scheduledTransferPeriod в таблицу службы хранилища WADPerformanceCountersTable.</td>
			<td>Да</td>
			<td>Да</td>
	</tr>
	<tr>
			<td>Аварийные дампы</td>
			<td>Нет</td>
			<td>BLOB-объект</td>
			<td>Ведение журнала информации о состоянии операционной системы в случае системного сбоя. Аварийные мини-дампы собираются локально. Полные дампы можно включить отдельно. Данные журнала переносятся с указанным интервалом scheduledTransferPeriod в указанный вами контейнер. Поскольку ASP.NET обрабатывает большинство исключений, обычно это полезно только для рабочей роли или виртуальной машины.</td>
			<td>Да</td>
			<td>Да</td>
	</tr>
	<tr>
			<td>Пользовательские журналы ошибок</td>
			<td>Нет</td>
			<td>BLOB-объект</td>
			<td>С помощью локальных ресурсов службы хранилища можно вести журнал пользовательских данных и немедленно переносить их в указанный контейнер.</td>
			<td>Да</td>
			<td>Да</td>
	</tr>
	<tr>
			<td>EventSource</td>
			<td>Нет</td>
			<td>Таблица</td>
			<td>Ведет журнал событий, созданных вашим кодом с использованием класса EventSource .NET.</td>
			<td>Нет</td>
			<td>Да</td>
	</tr>
	<tr>
			<td>Трассировка событий Windows на основе манифестов</td>
			<td>Нет</td>
			<td>Таблица</td>
			<td>События трассировки событий Windows, созданные каким-либо процессом.</td>
			<td>Нет</td>
			<td>Да</td>
	</tr>
</tbody>
</table>

<h2><a name="additional"></a>Дополнительные ресурсы</h2>

- [Советы и рекомендации по устранению неполадок при разработке приложений Azure][]
- [Сбор данных журнала с помощью системы диагностики Azure][]
- [Отладка приложения Azure][]
- [Настройка системы диагностики Azure][]

  

[Обзор]: #overview
[Как включить диагностику в рабочей роли]: #worker-role
[Как включить диагностику в виртуальной машине]: #virtual-machine
[Пример файла конфигурации и схемы]: #configuration-file-schema
[Устранение неполадок]: #troubleshooting
[Часто задаваемые вопросы]: #faq
[Сравнение систем диагностики Azure версии 1.0 и 1.2]: #comparing
[Дополнительные ресурсы]: #additional
[Класс EventSource]: http://msdn.microsoft.com/ru-ru/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx
  
[Настройка системы диагностики Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn186185.aspx
[Отладка приложения Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee405479.aspx   
[Сбор данных журнала с помощью системы диагностики Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433048.aspx
[Советы и рекомендации по устранению неполадок при разработке приложений Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh771389.aspx
[Бесплатная пробная версия]: http://azure.microsoft.com/ru-ru/pricing/free-trial/
[Установка и настройка Azure PowerShell версии 0.8.7 или более поздней]: http://azure.microsoft.com/ru-ru/documentation/articles/install-configure-powershell/
[Azure Diagnostics 1.2 Configuration Schema] (Схема конфигурации системы диагностики Azure 1.2): http://msdn.microsoft.com/ru-ru/library/azure/dn782207.aspx
[Set-AzureServiceDiagnosticsExtension]: http://msdn.microsoft.com/ru-ru/library/dn495270.aspx
[Get-AzureServiceDiagnosticsExtension]: http://msdn.microsoft.com/ru-ru/library/dn495145.aspx
[Remove-AzureServiceDiagnosticsExtension]: http://msdn.microsoft.com/ru-ru/library/dn495168.aspx

<!--HONumber=35_1-->
