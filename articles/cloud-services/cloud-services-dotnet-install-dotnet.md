<properties
   pageTitle="Установка .NET для роли облачной службы"
   description="В этой статье описывается, как вручную установить платформу .NET Framework для веб-роли и рабочей роли облачной службы."
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/11/2015"
   ms.author="saurabh"/>

# Установка .NET для роли облачной службы 

В этой статье описывается, как установить платформу .NET Framework для веб-роли и рабочей роли облачной службы. Вы можете использовать эти шаги для установки .NET framework 4.5.2 или NET 4.6 в 4 семействе версий гостевой ОС Azure. Последние сведения о выпусках гостевой ОС см. в статье [Таблица совместимости версий гостевых ОС и Azure SDK](cloud-services-guestos-update-matrix.md).

Процесс установки .NET для веб-роли и рабочей роли предполагает добавление пакета установщика .NET в облачный проект и запуск установщика вместе с другими начальными задачами роли.

## Добавление установщика .NET в проект
1. Загрузите веб-установщик для .NET framework, который хотите установить
	- [Веб-установщик .NET 4.5.2](http://go.microsoft.com/fwlink/p/?LinkId=397703)
	- [Веб-установщик .NET 4.6](http://go.microsoft.com/fwlink/?LinkId=528259)
	- [Веб-установщик .NET 4.6.1](http://go.microsoft.com/fwlink/?LinkId=671729)
2. Для веб-роли:
  1. В **обозревателе решений** в папке **Роли** облачного проекта щелкните правой кнопкой мыши роль и выберите **Добавить > Новая папка**. Создайте папку с именем *bin*.
  2. Щелкните папку **bin** правой кнопкой мыши и выберите **Добавить > Существующий элемент**. Выберите установщик .NET и добавьте его в папку bin.
3. Для рабочей роли:
  1. Щелкните роль правой кнопкой мыши и выберите **Добавить > Существующий элемент**. Выберите установщик .NET и добавьте его в роль. 

Файлы, добавленные таким образом в папку содержимого роли, будут автоматически добавлены в пакет облачной службы и развернуты в нужном расположении на виртуальной машине. Повторите эту процедуру для всех веб-ролей и рабочих ролей в облачной службе, чтобы у всех ролей была копия установщика.

![Роль с файлами установщика][1]

## Определение начальных задач для ролей
С помощью начальных задач вы можете выполнять различные операции еще до запуска роли. Установка .NET Framework как начальной задачи позволит завершить установку платформы до запуска всех приложений. Дополнительные сведения о начальных задачах см. в статье [Выполнение задач запуска в Azure](https://msdn.microsoft.com/library/azure/hh180155.aspx).

1. В файл *ServiceDefinition.csdef* в узле **WebRole** или **WorkerRole** добавьте для всех ролей следующий код:
	
	```xml
	 <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
	```

	Он выполняет консольную команду *install.cmd* с правами администратора, что позволит установить платформу .NET Framework. Конфигурация также создает LocalStorage с именем *NETFXInstall*. Сценарий запуска в качестве временной папки задаст этот локальный ресурс хранилища, чтобы установщик .NET Framework был скачан и установлен из данного ресурса. Важно задать размер этого ресурса не менее 1024 МБ, чтобы обеспечить правильную установку платформы. Дополнительные сведения см. в статье [Использование локального хранилища для сохранения файлов во время запуска](https://msdn.microsoft.com/library/azure/hh974419.aspx).

2. Создайте файл **install.cmd** и добавьте его во все роли. Для этого щелкните каждую роль правой кнопкой мыши и выберите **Добавить > Существующий элемент**. Теперь у каждой роли будет файл установщика .NET и файл install.cmd.
	
	![Роль со всеми файлами][2]

	> [AZURE.NOTE]Для создания этого файла используйте простой текстовый редактор, например Блокнот. Если создать файл в Visual Studio и затем изменить его расширение на CMD, в файле может остаться метка порядка байтов UTF-8 и выполнения первой строки сценария приведет к ошибке. Если вы все же решили создать файл в Visual Studio, оставьте в первой строке отметку REM (комментарий), чтобы она игнорировалась при выполнении.

3. Добавьте следующий сценарий в файл **install.cmd**.

	```
	REM Set the value of netfx to install appropriate .NET Framework. 
	REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
	REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
	REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
	set netfx="NDP46"
		
	
	REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
	set TMP=%PathToNETFXInstall%
	set TEMP=%PathToNETFXInstall%
	
		
	REM ***** Setup .NET filenames and registry keys *****
	if %netfx%=="NDP461" goto NDP461
	if %netfx%=="NDP46" goto NDP46
	    set netfxinstallfile="NDP452-KB2901954-Web.exe"
	    set netfxregkey="0x5cbf5"
	    goto logtimestamp
		
	:NDP46
	set netfxinstallfile="NDP46-KB3045560-Web.exe"
	set netfxregkey="0x60051"
	goto logtimestamp
		
	:NDP461
	set netfxinstallfile="NDP461-KB3102438-Web.exe"
	set netfxregkey="0x6041f"
		
	:logtimestamp
	REM ***** Setup LogFile with timestamp *****
	set timehour=%time:~0,2%
	set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
	md "%PathToNETFXInstall%\log"
	set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
	set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
	
	echo Logfile generated at: %startuptasklog% >> %startuptasklog%
	echo TMP set to: %TMP% >> %startuptasklog%
	echo TEMP set to: %TEMP% >> %startuptasklog%
	
	REM ***** Check if .NET is installed *****
	echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
	reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release | Find %netfxregkey%
	if %ERRORLEVEL%== 0 goto end
		
	REM ***** Installing .NET *****
	echo Installing .NET: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% >> %startuptasklog%
	start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% >> %startuptasklog% 2>>&1
		
	:end
	echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%

	```
	
	> [AZURE.IMPORTANT]Измените в сценарии значение переменной *netfx* в соответствии с версией платформы, которую вы хотите установить. Для установки .NET 4.5.2 переменной *netfx* следует присвоить значение *NDP452*, для установки .NET 4.6 переменной *netfx* следует присвоить значение *NDP46*, а для установки .NET 4.6.1 переменной *netfx* следует присвоить значение *NDP461*.
		
	Сценарий установки путем поиска по реестру проверяет, установлена ли на компьютере выбранная версия .NET framework. Если требуемая версия .NET не установлена, запускается веб-установщик .NET. Сценарий записывает все действия в файл *startuptasklog-(текущие\_тада\_и\_время).txt*, который хранится в локальном хранилище *InstallLogs*. Если возникнут какие-то неполадки, сведения в этом файле помогут устранить их.
 
      

## Настройка системы диагностики для передачи журналов начальных задач в хранилище BLOB-объектов (необязательная процедура)
При необходимости вы можете настроить в системе диагностики Azure передачу все файлов журналов, созданных сценарием запуска или установщиком .NET, в хранилище BLOB-объектов. Это позволит просматривать журналы, загружая их из хранилища больших двоичных объектов, а не на удаленном рабочем столе, на котором хранится роль.

Чтобы настроить систему диагностики, откройте файл *diagnostics.wadcfgx* и добавьте в узел **Directories** следующий код.

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Это позволит системе диагностики Azure передавать все файлы из каталога *log* в ресурсе *InstallLogs* в учетную запись хранения диагностических данных в контейнере больших двоичных объектов *netfx-install*.

## Развертывание службы 
При развертывании службы запускаются начальные задачи и устанавливается платформа .NET Framework (если она еще не установлена). Ваши роли будут в состоянии занятости во время установки платформы и могут даже перезагрузиться, если это потребуется для установки платформы.


## Дополнительные ресурсы

- [Установка платформы .NET Framework][]
- [Практическое руководство. Определение установленных версий платформы .NET Framework][]
- [Устранение неполадок с установкой .NET Framework][]

[Практическое руководство. Определение установленных версий платформы .NET Framework]: https://msdn.microsoft.com/library/hh925568.aspx
[Установка платформы .NET Framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[Устранение неполадок с установкой .NET Framework]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png

 

<!---HONumber=AcomDC_1217_2015-->