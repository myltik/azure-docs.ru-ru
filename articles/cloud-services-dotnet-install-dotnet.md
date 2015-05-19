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
   ms.date="04/23/2015"
   ms.author="saurabh"/>

# Установка .NET для роли облачной службы 

В этой статье описывается, как установить платформу .NET Framework для веб-роли и рабочей роли облачной службы. С помощью приведенных ниже действий можно установить версию платформы .NET Framework, которая по умолчанию не установлена в гостевой ОС Azure. Последние сведения о выпусках гостевой ОС см. в статье [Таблица совместимости версий гостевых ОС и Azure SDK](cloud-services-guestos-update-matrix.md).

Процесс установки .NET для веб-роли и рабочей роли предполагает добавление пакета установщика .NET в облачный проект и запуск установщика вместе с другими начальными задачами роли.

## Добавление установщика .NET в проект
1. Загрузите [веб-установщик .NET 4.5.2](http://www.microsoft.com/ru-ru/download/details.aspx?id=42643).
2. Для веб-роли:
  1. В **обозревателе решений** в папке **Роли** облачного проекта щелкните правой кнопкой мыши роль и выберите **Добавить \> Новая папка**. Создайте папку с именем *bin*.
  2. Щелкните папку **bin** правой кнопкой мыши и выберите **Добавить \> Существующий элемент**. Выберите установщик .NET и добавьте его в папку bin.
3. Для рабочей роли:
  1. Щелкните роль правой кнопкой мыши и выберите **Добавить \> Существующий элемент**. Выберите установщик .NET и добавьте его в роль. 

Файлы, добавленные таким образом в папку содержимого роли, будут автоматически добавлены в пакет облачной службы и развернуты в нужном расположении на виртуальной машине. Повторите эту процедуру для всех веб-ролей и рабочих ролей в облачной службе, чтобы у всех ролей была копия установщика.

![Роль с файлами установщика][1]

## Определение начальных задач для ролей
С помощью начальных задач вы можете выполнять различные операции еще до запуска роли. Установка .NET Framework как начальной задачи позволит завершить установку платформы до запуска всех приложений. Дополнительные сведения о начальных задачах см. в статье [Выполнение задач запуска в Azure](https://msdn.microsoft.com/library/azure/hh180155.aspx).

1. В файл *ServiceDefinition.csdef* в узле *WebRole* или *WorkerRole* добавьте для всех ролей следующий код:
	
	```xml
	 <LocalResources>
	    <LocalStorage name="InstallLogs" sizeInMB="5" cleanOnRoleRecycle="false" />
	 </LocalResources>
	 <Startup>
	    <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
	        <Environment>
	        <Variable name="PathToInstallLogs">
	        <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='InstallLogs']/@path" />
	        </Variable>
	        </Environment>
	    </Task>
	 </Startup>
	```

	Он выполняет консольную команду *install.cmd* с правами администратора, что позволит установить платформу .NET Framework. Этот код также создает локальное хранилище с именем *InstallLogs*, в котором будут храниться все журналы, созданные сценарием установки. Дополнительные сведения см. в статье [Использование локального хранилища для сохранения файлов во время запуска](https://msdn.microsoft.com/library/azure/hh974419.aspx).

2. Создайте файл install.cmd с таким содержимым:

	```
	REM install.cmd to install .NET Framework
	set timehour=%time:~0,2%
	set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
	set startuptasklog=%PathToInstallLogs%\startuptasklog-%timestamp%.txt
	set netfxinstallerlog = %PathToInstallLogs%\NetFXInstallerLog-%timestamp%
	echo Logfile generated at: %startuptasklog% >> %startuptasklog%
	echo Checking if .NET 4.5.2 is installed >> %startuptasklog%
	reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release | Find "0x5cbf5"
	if %ERRORLEVEL%== 0 goto end
	echo Installing .NET 4.5.2. Log: %netfxinstallerlog% >> %startuptasklog%
	start /wait %~dp0NDP452-KB2901954-Web.exe /q /serialdownload /log %netfxinstallerlog%
	:end
	echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
	```

	Сценарий установки проверяет реестр компьютера на предмет установки .NET 4.5.2. Если платформа .NET 4.5.2 не установлена, запускается веб-установщик .NET. Сценарий записывает все действия в файл *startuptasklog-\(текущие\_тада\_и\_время\).txt*, который хранится в локальном хранилище *InstallLogs*. Если возникнут какие-то неполадки, сведения в этом файле помогут устранить их.

	> [AZURE.NOTE]Для создания этого файла используйте простой текстовый редактор, например Блокнот. Если создать файл в Visual Studio и затем изменить его расширение на CMD, в файле может остаться метка порядка байтов UTF-8 и выполнения первой строки сценария приведет к ошибке. Если вы все же решили создать файл в Visual Studio, оставьте в первой строке отметку REM \(комментарий\), чтобы она игнорировалась при выполнении.
      
3. Добавьте файл install.cmd во все роли. Для этого щелкните каждую роль правой кнопкой мыши и выберите **Добавить \> Существующий элемент**. Теперь у каждой роли будет файл установщика .NET и файл install.cmd.
	
	![Роль со всеми файлами][2]

## Настройка системы диагностики для передачи журналов начальных задач в хранилище BLOB-объектов \(необязательная процедура\)
При необходимости вы можете настроить в системе диагностики Azure передачу все файлов журналов, созданных сценарием запуска или установщиком .NET, в хранилище BLOB-объектов. Это позволит просматривать журналы, загружая их из хранилища больших двоичных объектов, а не на удаленном рабочем столе, на котором хранится роль.

Чтобы настроить систему диагностики, откройте файл *diagnostics.wadcfgx* и добавьте под узлом *<Directories>* следующий код:

```xml 
<DataSources>
    <DirectoryConfiguration containerName="netfx-install">
    <LocalResource name="InstallLogs" relativePath="."/>
    </DirectoryConfiguration>
</DataSources>
```

Это позволит системе диагностики Azure передавать все файлы из ресурса *InstallLogs* в учетную запись хранилища диагностических данных в контейнере BLOB-объектов *netfx-install*.

## Развертывание службы 
При развертывании службы запускаются начальные задачи и устанавливается платформа .NET Framework \(если она еще не установлена\). Ваши роли будут в состоянии занятости во время установки платформы и могут даже перезагрузиться, если это потребуется для установки платформы.


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


<!--HONumber=52-->
