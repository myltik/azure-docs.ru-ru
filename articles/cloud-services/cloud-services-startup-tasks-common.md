<properties 
pageTitle="Стандартные задачи запуска в облачной службе | Microsoft Azure" 
description="Содержит некоторые примеры стандартных задач запуска, которые можно выполнить в веб-роли или рабочей роли облачной службы." 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="03/25/2016" 
ms.author="adegeo"/>

# Стандартные задачи запуска в облачной службе

В этой статье приведены некоторые примеры стандартных задач запуска, которые можно выполнить в облачной службе. С помощью задач запуска вы можете выполнять различные операции перед запуском роли. Это может быть установка компонента, регистрация компонентов COM, установка разделов реестра или запуск длительного процесса.

[Эта статья](cloud-services-startup-tasks.md) поможет понять, как работают задачи запуска, и, в частности, как создавать записи, которые определяют задачу запуска.

Многие из приведенных здесь задач используют

>[AZURE.NOTE] Задачи запуска неприменимы к виртуальным машинам, они подходят только для веб-ролей и рабочих ролей облачной службы.


## Определение переменных среды до запуска роли

Переменные среды для всей роли можно определить, добавив элемент [Runtime] в определение роли в файле определения службы.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Runtime>
            <Environment>
                <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
            </Environment>
        </Runtime>
    </WebRole>
</ServiceDefinition>
```

Если требуется определить переменные среды для конкретной задачи, не используемые совместно другими задачами, можно использовать элемент [Environment] внутри элемента [Task].

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WebRole>
</ServiceDefinition>
```

В переменных также может использоваться [допустимое значение XPath Azure](cloud-services-role-config-xpath.md) для ссылки на что-либо, относящееся к развертыванию. Вместо использования атрибута `value` определите дочерний элемент [RoleInstanceValue].

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## Настройка запуска IIS с помощью AppCmd.exe

С помощью программы командной строки [AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) можно управлять параметрами IIS при запуске в Azure. *AppCmd.exe* предоставляет удобный доступ из командной строки к параметрам конфигурации, используемым в задачах запуска в Azure. С помощью *AppCmd.exe* можно добавить, изменить или удалить параметры веб-сайта для приложений и сайтов.

Однако следует знать о некоторых особенностях использования *AppCmd.exe* в качестве задачи запуска:

- Задачи запуска могут несколько раз выполняться между перезагрузками. Это может произойти, например, при перезапуске роли.
- Некоторые действия *AppCmd.exe* могут порождать ошибки, если они выполняются несколько раз. Попытка дважды добавить раздел в *Web.config* приведет к ошибке.
- Задачи запуска завершаются ошибкой, если они возвращают ненулевой код выхода или **errorlevel**. Это может произойти, если *AppCmd.exe* выдает ошибку.

По вышеупомянутым причинам разумно часто проверять **errorlevel** после вызова *AppCmd.exe*. Это легко сделать, если создать программу-оболочку вызова *AppCmd.exe* в *CMD*-файле. При обнаружении известного ответа **errorlevel** его можно пропустить, в противном случае — вернуть. Это показано в приведенном ниже примере.

Значения errorlevel, возвращаемые *AppCmd.exe*, перечислены в файле winerror.h. Кроме того, их можно просмотреть на сайте [MSDN](https://msdn.microsoft.com/library/windows/desktop/ms681382.aspx).

### Пример

Этот пример добавляет раздел сжатия и запись сжатия для JSON в файл *Web.config* с обработкой ошибок и ведением журнала.

Здесь показаны соответствующие разделы файла [ServiceDefinition.csdef], которые включают в себя присвоение атрибуту [executionContext](https://msdn.microsoft.com/library/azure/gg557552.aspx#Task) значения `elevated`. Так *AppCmd.exe* предоставляются достаточные разрешения для изменения параметров в файле *Web.config*:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Пакетный файл Startup.cmd использует AppCmd.exe для добавления раздела сжатия и записи сжатия для JSON в файл *Web.config*. Ожидаемый **errorlevel** 183 получает значение 0 с помощью программы командной строки VERIFY.EXE. Непредвиденные значения errorlevel заносятся в файл StartupErrorLog.txt.

    REM   *** Add a compression section to the Web.config file. ***
    %windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
    
    REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
    REM   batch file were executed twice. This can occur and must be accounted for in a Azure startup
    REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
    REM   command will safely set the ERRORLEVEL to zero.
    IF %ERRORLEVEL% EQU 183 DO VERIFY > NUL
    
    REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
    IF %ERRORLEVEL% NEQ 0 (
        ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
        GOTO ErrorExit
    )
    
    REM   *** Add compression for json. ***
    %windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
    IF %ERRORLEVEL% EQU 183 VERIFY > NUL
    IF %ERRORLEVEL% NEQ 0 (
        ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
        GOTO ErrorExit
    )
    
    REM   *** Exit batch file. ***
    EXIT /b 0
    
    REM   *** Log error and exit ***
    :ErrorExit
    REM   Report the date, time, and ERRORLEVEL of the error.
    DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
    TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
    EXIT %ERRORLEVEL%


## Добавление правил брандмауэра

Фактически в Azure есть два брандмауэра. Первый брандмауэр управляет подключениями между виртуальной машиной и внешним миром. Для этого используется элемент [EndPoints] в файле [ServiceDefinition.csdef].

Второй брандмауэр управляет подключениями между виртуальной машиной и процессами в этой виртуальной машине. Для этого используется программа командной строки `netsh advfirewall firewall`. И именно этому посвящена данная статья.

Azure создает правила брандмауэра для процессов, запущенных в ваших ролях. Например, при запуске службы или программы Azure автоматически создает необходимые правила брандмауэра, чтобы служба могла взаимодействовать с Интернетом. Тем не менее, если создать службу, которая запускается процессом извне роли (например, службу COM+ или программу, запускаемую планировщиком Windows), необходимо будет вручную создать правило брандмауэра, чтобы разрешить доступ к этой службе. Эти правила брандмауэра можно создавать с помощью задачи запуска.

У задачи запуска, которая создает правило брандмауэра, должен быть [executionContext][Task] со значением **elevated**. Добавьте следующую задачу запуска в файл [ServiceDefinition.csdef].

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Чтобы добавить правило брандмауэра, необходимо использовать в пакетном файле запуска соответствующие команды `netsh advfirewall firewall`. В этом примере задаче запуска требуется обеспечить безопасность и шифрование данных для TCP-порта 80.

    REM   Add a firewall rule in a startup task.
    
    REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
    netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1
    
    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%


## Блокирование определенного IP-адреса

Можно ограничить доступ веб-роли Azure набором указанных IP-адресов, изменив файл **web.config** служб IIS и создав командный файл, который разблокирует раздел **ipSecurity** файла **ApplicationHost.config**.

Сначала создайте командный файл, который запускается при запуске роли и разблокирует раздел **ipSecurity** файла **ApplicationHost.config**. Создайте новую папку **startup** на корневом уровне веб-роли, а в этой папке создайте пакетный файл **startup.cmd**. Задайте для этого файла свойства **Всегда копировать**, чтобы обеспечить его развертывание.

Добавьте следующую задачу запуска в файл [ServiceDefinition.csdef].

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Добавьте эту команду в файл **startup.cmd**:

    %windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity

В результате пакетный файл **startup.cmd** будет запускаться каждый раз при инициализации веб-роли, что разблокирует необходимый раздел **ipSecurity**.

Наконец, измените [раздел system.webServer](http://www.iis.net/configreference/system.webserver/security/ipsecurity#005) в файле **web.config** веб-роли, чтобы добавить список IP-адресов, к которым предоставлен доступ, как показано в следующем примере:

В этом примере конфигурации доступ к серверу **разрешен** всем IP-адресам, за исключением двух определенных.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are granted access-->
    <ipSecurity>
        <!--The following IP addresses are denied access-->
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

В этом примере конфигурации доступ к серверу **запрещен** всем IP-адресам, за исключением двух определенных.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity allowUnlisted="false">
        <!--The following IP addresses are granted access-->
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## Создание задачи запуска PowerShell

Сценарии Windows PowerShell нельзя вызывать непосредственно из файла [ServiceDefinition.csdef], но их можно вызывать из пакетного файла запуска.

PowerShell по умолчанию не запускает неподписанный сценарий. Пока не подписали сценарии, необходимо настроить Windows PowerShell для запуска неподписанных сценариев. Для запуска неподписанных сценариев параметру **ExecutionPolicy** необходимо присвоить значение **Unrestricted**. Используемый параметр **ExecutionPolicy** зависит от версии Windows PowerShell.

    REM   Run an unsigned PowerShell script and log the output
    PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
        
    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%


Если вы используете гостевую ОС, на которой выполняется PowerShell 2.0 или 1.0, можно принудительно выполнить версию 2, и если она недоступна, использовать версию 1.

    REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
    PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

    REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
    IF %ERRORLEVEL% EQU -393216 (

       PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
       PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
    )

    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%

## Создание файлов в локальном хранилище из задачи запуска

Локальный ресурс хранилища можно использовать для хранения файлов, созданных задачей запуска, которые будут использоваться приложением позже.

Для создания локального ресурса хранилища добавьте раздел [LocalResources] в файл [ServiceDefinition.csdef], затем добавьте дочерний элемент [LocalStorage]. Присвойте локальному ресурсу хранилища уникальное имя и задайте соответствующий размер для задачи запуска.

Чтобы использовать локальный ресурс хранилища в задаче запуска, необходимо создать переменную среды для ссылки на расположение локального ресурса хранилища. Затем задача запуска и приложение смогут выполнять чтение и запись файлов в локальный ресурс хранилища.

Соответствующие разделы файла **ServiceDefinition.csdef** показаны ниже:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        
        <LocalResources>
          <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
        </LocalResources>
        
        ...
        
        <Runtime>
            <Environment>
                <Variable name="PathToStartupStorage">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
                </Variable>
            </Environment>
        </Runtime>
        
        ...
        
        <Startup>
          <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Например, этот пакетный файл **Startup.cmd** использует переменную среды **PathToStartupStorage**, чтобы создать файл **MyTest.txt** в локальном хранилище.

    REM   Create a simple text file.

    ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
    ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
    ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
    ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

    REM   Exit the batch file with ERRORLEVEL 0.

    EXIT /b 0

Обратиться к локальном хранилищу можно из пакета SDK для Azure с помощью метода [GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx). Затем можно использовать стандартные операции с файлом для чтения и записи содержимого локального ресурса хранилища.

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTest.txt"));
```


## Различия между выполнением в эмуляторе и облаке

С помощью задачи запуска можно выполнять другие действия, когда она выполняется в облаке, по сравнению с выполнением в эмуляторе вычислений. Например, можно понадобиться использовать новую копию данных SQL только при выполнении в эмуляторе. Или вы можете захотеть каким-либо образом оптимизировать производительность для облака, что не требуется при выполнении в эмуляторе.

Эта возможность выполнять различные действия в эмуляторе вычислений и облаке можно получить, создав переменную среды в файле [ServiceDefinition.csdef], а затем проверив эту переменную в задаче запуска.

Чтобы создать переменную среды, добавьте элемент [Variable]/[RoleInstanceValue] и создайте значение XPath `/RoleEnvironment/Deployment/@emulated`. Значением переменной среды **%ComputeEmulatorRunning%** будет `"true"` при выполнении в эмуляторе вычислений и `"false"` при выполнении в облаке.


```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">

        ...
        
        <Runtime>
            <Environment>
                <Variable name="ComputeEmulatorRunning">
                    <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
                </Variable>
            </Environment>
        </Runtime>

    </WebRole>
</ServiceDefinition>
```

Теперь любая задача запуска может использовать переменную среды **%ComputeEmulatorRunning%** для выполнения разных действий в зависимости от того, где выполняется роль —в облаке или эмуляторе. Вот CMD-файл сценария оболочки для проверки этой переменной среды.

    REM   Check if this task is running on the compute emulator.

    IF "%ComputeEmulatorRunning%" == "true" (
        REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
        
    ) ELSE (
        REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
        
    )


## Обнаружение запуска задачи

Роль может перезапускаться без перезагрузки, вызывая повторный запуск задачи запуска. Существует флаг, который указывает, что задача уже была запущена на виртуальной машине размещения. Возможно, для некоторых задач не важно, сколько раз они выполняются. Однако возможны ситуации, когда нужно предотвратить выполнение задачи более одного раза.

Самый простой способ определить, что задача уже была запущена, —создавать файл в папке **%TEMP%**, если задача выполнена успешно, и искать его при запуске задачи. Ниже приведен пример CMD-файла сценария оболочки, который это делает.

    REM   If Task1_Success.txt exists, then Application 1 is already installed.
    IF EXIST "%RoleRoot%\Task1_Success.txt" (
      ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
      GOTO Finish
    )

    REM   Run your real exe task
    ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

    IF %ERRORLEVEL% EQU 0 (
      REM   The application installed without error. Create a file to indicate that the task
      REM   does not need to be run again.

      ECHO This line will create a file to indicate that Application 1 installed correctly. > "%RoleRoot%\Task1_Success.txt"
      
    ) ELSE (
      REM   An error occurred. Log the error and exit with the error code.

      DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
      TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
      ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1

      EXIT %ERRORLEVEL%
    )

    :Finish

    REM   Exit normally.
    EXIT /B 0


## Рекомендации по задачам
Ниже приведены некоторые рекомендации, которые необходимо выполнять при настройке задачи для рабочей роли или веб-роли.

### Всегда ведите журнал операций запуска

В Visual Studio нет отладчика для пошагового выполнения пакетных файлов, поэтому рекомендуется получить столько данных о выполнении пакетных файлов, сколько возможно. Ведение журнала выходных данных пакетных файлов, **stdout** и **stderr**, может предоставить вам важную информацию при попытке отладить и исправить пакетные файлы. Чтобы записывать **stdout** и **stderr** в файл StartupLog.txt в каталоге, указанном переменной среды **%TEMP%**, добавьте текст `>>  "%TEMP%\\StartupLog.txt" 2>&1` в конец конкретных строк, которые вы хотите добавлять в журнал. Например, для выполнения setup.exe в каталоге **%PathToApp1Install%**:

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

Если необходимо вести журнал всех выходных данных задачи запуска без добавления `>> "%TEMP%\StartupLog.txt" 2>&1` в конец каждой строки, необходимы два пакетных файла запуска. Первый пакетный файл будет вызывать второй пакетный файл с перенаправлением для записи в журнал всех действий второго пакетного файла. Это необходимо для правильного перенаправления.

Ниже показано, как перенаправить все выходные данные из пакетного файла запуска. В этом примере файл ServerDefinition.csdef создает задачу запуска, которая называется Startup1.cmd. Startup1.cmd вызывает Startup2.cmd, перенаправляя все выходные данные в %TEMP%\\StartupLog.txt.

ServiceDefinition.cmd:

```xml
<Startup>
    <Task commandLine="Startup1.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

Startup1.cmd:

    REM   Startup1.cmd calls the main startup batch file, Startup2.cmd, redirecting all output
    REM   to the StartupLog.txt log file.

    REM   Log the startup date and time.
    ECHO Startup1.cmd: >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO Current date and time: >> "%TEMP%\StartupLog.txt" 2>&1
    DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
    TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO Starting up Startup2.cmd. >> "%TEMP%\StartupLog.txt" 2>&1

    REM   Call the Startup2.cmd batch file, redirecting all output to the StartupLog.txt log file.
    START /B /WAIT Startup2.cmd >> "%TEMP%\StartupLog.txt" 2>&1

    REM   Log the completion of Startup1.cmd.
    ECHO Returned to Startup1.cmd. >> "%TEMP%\StartupLog.txt" 2>&1

    IF ERRORLEVEL EQU 0 (
       REM   No errors occurred. Exit Startup1.cmd normally.
       EXIT /B 0
    ) ELSE (
       REM   Log the error.
       ECHO An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
       EXIT %ERRORLEVEL%
    )

Startup2.cmd:

    REM   This is the batch file where the startup steps should be performed. Because of the
    REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
    REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.

    REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
    REM   calling batch file.
    EXIT /B %ERRORLEVEL%

### Правильная настройка executionContext для задач запуска

Задайте соответствующие привилегии для задачи запуска. Иногда задачи запуска должны выполняться с повышенными привилегиями, даже несмотря на то, что роль запускается с обычными привилегиями.

Атрибут [ExecutionContext][Task] задает уровень привилегий задачи запуска. Использование `executionContext="limited"` означает, что у задачи запуска будет тот же уровень привилегий, что и у роли. Использование `executionContext="elevated"` означает, что у задачи запуска будут привилегии администратора, разрешающие задаче запуска выполнять операции администрирования, без предоставления привилегий администратора вашей роли.

Примером задачи запуска, которой требуются повышенные привилегии, может послужить задача запуска, которая использует **AppCmd.exe** для настройки IIS. **AppCmd.exe** требуется `executionContext="elevated"`.

### Использование соответствующего taskType

Атрибут [TaskType][Task] определяет способ выполнения задачи запуска. Он имеет три значения: **simple**, **background** и **foreground**. Фоновые задачи (background) и задачи переднего плана (foreground) запускаются асинхронно, а затем по одной синхронно выполняются простые задачи (simple).

С помощью **простых** задач запуска можно задать порядок выполнения задач, упорядочив их список в файле ServiceDefinition.csdef. Если **простая** задача завершится с ненулевым кодом выхода, то процедура запуска остановится и роль не запустится.

Разница между **фоновыми** задачами запуска и задачами запуска **переднего плана** состоит в том, что задачи **переднего плана** оставляют роль запущенной до своего завершения. Это также означает, что если задача **переднего плана** перестает отвечать на запросы иди дает сбой, роль не перезапустится, пока задача **переднего плана** не будет принудительно закрыта. По этой причине рекомендуется использовать **фоновые** задачи для асинхронных задач запуска, если только не требуется использовать возможности задачи **переднего плана**.

### Завершайте пакетные файлы командой EXIT /B 0

Роль запустится, только если **errorlevel** каждой простой задачи запуска равен нулю. Не все программы корректно задают **errorlevel** (код завершения), поэтому пакетный файл должен заканчиваться `EXIT /B 0`, если все выполнено правильно.

Отсутствие `EXIT /B 0` в конце пакетного файла запуска — распространенная причина, по которой роли не запускаются.

### Ожидайте многократного выполнения задач запуска

Не все перезапуски роли включают в себя перезагрузку, но все они вызывают выполнение всех задач запуска. Это означает, что задачи запуска должны иметь возможность многократно выполняться между перезагрузками без каких-либо проблем. Этот вопрос рассмотрен [выше](#detect-that-your-task-has-already-run).

### Используйте локальное хранилище для хранения файлов, которые должны использоваться в роли

Если вы хотите во время выполнения задачи запуска скопировать или создать файл, который затем будет доступен вашей роли, этот файл необходимо поместить в локальное хранилище. См. [раздел](#create-files-in-local-storage-from-a-startup-task) выше.

## Дальнейшие действия

Ознакомьтесь с [моделью и пакетом облачной службы](cloud-services-model-and-package.md).

Узнайте, как работают [задачи](cloud-services-startup-tasks.md).

[Создайте и разверните](cloud-services-how-to-create-deploy-portal.md) свой пакет облачной службы.


[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Task]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Environment]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Endpoints]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[LocalStorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[LocalResources]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue

<!---HONumber=AcomDC_0330_2016-->