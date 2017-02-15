---
title: "Устранение неполадок клиента Docker в Windows с помощью Visual Studio | Документация Майкрософт"
description: "Устранение неполадок, которые возникают при использовании Visual Studio для создания и развертывания веб-приложений в Docker в Windows с помощью Visual Studio."
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: 01c10d04606e15082c8842ad87f617703a00c903
ms.openlocfilehash: cbb376dae88d39e965838de74d90158691b59f90


---

# <a name="troubleshooting-visual-studio-docker-development"></a>Устранение неполадок при разработке в Visual Studio Docker

При работе с инструментами Visual Studio для предварительной версии Docker могут возникать некоторые проблемы из-за природы предварительной версии.
Ниже приведены некоторые распространенные проблемы и способы их устранения.  

## <a name="visual-studio-2017-rc"></a>Visual Studio 2017 RC

### <a name="linux-containers"></a>**Контейнеры Linux**

###  <a name="build-errors-occur-when-debugging-a-net-core-web-or-console-application"></a>При отладке веб-приложения или консольного приложения .NET Core возникают ошибки сборки.  

Это может быть связано с отсутствием общего доступа к диску, на котором расположен проект с Docker for Windows.  Может появиться сообщение об ошибке следующего вида.

```
The "PrepareForLaunch" task failed unexpectedly.
Microsoft.DotNet.Docker.CommandLineClientException: Creating network "webapplication13628050196_default" with the default driver
Building webapplication1
Creating webapplication13628050196_webapplication1_1
ERROR: for webapplication1  Cannot create container for service webapplication1: C: drive is not shared. Please share it in Docker for Windows Settings
```
Чтобы устранить ошибку, щелкните правой кнопкой мыши значок Docker for Windows в области уведомлений и выберите "Settings..." (Параметры).  Перейдите на вкладку "Shared Drives" (Общие диски) и предоставьте общий доступ к диску, на котором находится проект.

### <a name="windows-containers"></a>**Контейнеры Windows**

Ниже описывается поиск и устранение неполадок при отладке веб-приложений и консольных приложений .NET Framework в контейнерах Windows.

### <a name="pre-requisites"></a>Предварительные требования

1. Visual Studio 2017 RC (или более поздняя версия) с установленными рабочими нагрузками .NET Core и Docker (предварительная версия).
2. Юбилейное обновление Windows 10 с последними обновлениями и исправлениями для Windows.
3. Docker For Windows (бета-версия): https://docs.docker.com/docker-for-windows/ (1.12.2-beta28 7813 или более поздняя версия).
4. Щелкнув значок Docker for Windows на панели задач, выберите "Switch to Windows containers" (Переключиться на контейнеры Windows).  После перезагрузки виртуальной машины убедитесь, что этот параметр остался включенным.

### <a name="clicking-docker-debug-project-results-in-the-error--client-version-122-is-too-old--minimum-supported-api-version-is-124-please-upgrade-your-client-to-a-newer-version"></a>Щелчок **Docker: отладить проект** вызывает ошибку: "client version 1.22 is too old.  Minimum supported API version is 1.24, please upgrade your client to a newer version" (Версия 1.22 устарела. Минимальная поддерживаемая версия API: 1.24. Обновите клиент до более новой версии).

Для версии 1.13-RC2-beta31 (9123) или более поздней версии Docker for Windows требуется Docker Compose версии 2.1.   Чтобы устранить эту проблему, замените все вхождения версии "2" в файлах docker-compose*.yml в проекте. Шаблоны по умолчанию, которые Visual Studio добавляет в проект, будут обновлены в будущих версиях инструментов. 

### <a name="console-output-does-not-appear-in-visual-studios-output-window-while-debugging-a-console-application"></a>Вывод консоли не отображается в окне вывода Visual Studio при отладке консольного приложения.

Это известная проблема отладчика Visual Studio (msvsmon.exe), который в настоящее время не предназначен для этого сценария.  Мы планируем заняться этой проблемой в будущем выпуске.  Для просмотра выходных данных консольного приложения в Visual Studio используйте "Docker: запустить проект", что эквивалентно операции "Запуск без отладки".

### <a name="debugging-web-applications-with-the-release-configuration-fails-with-403-forbidden-error"></a>При отладке веб-приложений с помощью конфигурации выпуска возникает ошибка "(403) Запрещено".

Чтобы обойти эту проблему, откройте файл web.release.config в решении и закомментируйте или удалите приведенные ниже строки.

```
<compilation xdt:Transform="RemoveAttributes(debug)" />
```

### <a name="when-switching-to-windows-containers-you-could-potentially-see-an-error-dialog-stating-error-response-from-daemon-io-timeout"></a>При переключении на контейнеры Windows может появится диалоговое окно со следующим сообщением об ошибке: "Error response from daemon: i/o timeout" (Ошибочный ответ управляющей программы: истекло время ожидания ввода-вывода).

За исправлением этой проблемы в Docker for Windows можно следить на странице https://github.com/docker/for-win/issues/178.


## <a name="visual-studio-2015"></a>Visual Studio 2015

### <a name="linux-containers"></a>**Контейнеры Linux**

### <a name="unable-to-validate-volume-mapping"></a>Не удалось проверить сопоставление тома
Сопоставление тома требуется для доступа к исходному коду и двоичным файлам приложения из папки приложения в контейнере.  Определенные сопоставления томов содержатся в файлах docker-compose.dev.debug.yml и docker-compose.dev.release.yml. Так как файлы изменяются на хост-компьютере, контейнеры отобразят эти изменения в той же структуре папок.

Чтобы включить сопоставление тома, в приложении Docker для Windows щелкните значок с изображением кита на панели задач, выберите пункт **Settings…** (Параметры…), а затем перейдите на вкладку **Shared Drives** (Общие диски).  Удостоверьтесь, что диск, где размещен проект, а также диск, на который указывает с переменная среды %USERPROFILE%, являются общими, а затем щелкните **Применить**.

Чтобы проверить, работает ли сопоставление тома, как только диски станут общими, выполните повторную сборку или нажмите клавишу F5 в Visual Studio либо выполните следующие действия из командной строки.

*В командной строке Windows*

> [!Note]
> В этом примере предполагается, что папка Users находится на диске C и что к этому диску предоставлен общий доступ.
> Выполните соответствующее обновление, если общий доступ предоставлен к другому диску.

```
docker run -it -v /c/Users/Public:/wormhole busybox
```

*В контейнере Linux*

```
/ # ls
```

Вы должны увидеть список каталогов из папки Users/Public.
Если файлы не отображаются и папка /c/Users/Public не пустая, значит сопоставление тома настроено неправильно.

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Измените каталог на wormhole, чтобы просмотреть содержимое каталога `/c/Users/Public` :

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

> [!Note]
> При работе с виртуальными машинами Linux в файловой системе контейнера учитывается регистр.

##<a name="build--prepareforbuild-task-failed-unexpectedly"></a>Сборка. Непредвиденная ошибка при выполнении задачи PrepareForBuild

Microsoft.DotNet.Docker.CommandLine.ClientException. Произошла ошибка при попытке подключения.

Убедитесь, что узел Docker по умолчанию работает. Откройте окно командой строки и выполните следующую команду:

```
docker info
```

Если команда вернет ошибку, попытайтесь запустить классическое приложение **Docker для Windows** .  В запущенном приложении в области уведомлений должен отображаться значок **кита**. Щелкните этот значок правой кнопкой мыши и выберите **Settings**(Параметры).  Перейдите на вкладку **Reset** (Сброс) и выберите **Restart Docker..** (Перезапустить Docker..).

##<a name="manually-upgrading-from-version-031-to-040"></a>Обновление версии 0.31 до версии 0.40 вручную


1. Создайте резервную копию проекта.
1. Удалите в проекте следующие файлы:

    ```
      Dockerfile
      Dockerfile.debug
      DockerTask.ps1
      docker-compose-yml
      docker-compose.debug.yml
      .dockerignore
      Properties\Docker.props
      Properties\Docker.targets
    ```

1. Закройте решение и удалите из XPROJ-файла следующие строки:

    ```
      <DockerToolsMinVersion>0.xx</DockerToolsMinVersion>
      <Import Project="Properties\Docker.props" />
      <Import Project="Properties\Docker.targets" />
    ```

1. Повторно откройте решение.
1. Удалите из файла Properties\launchSettings.json следующие строки:

    ```
      "Docker": {
        "executablePath": "%WINDIR%\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
        "commandLineArgs": "-ExecutionPolicy RemoteSigned .\\DockerTask.ps1 -Run -Environment $(Configuration) -Machine '$(DockerMachineName)'"
      }
    ```

1. Удалите в разделе publishOptions файла project.json следующие файлы, связанные с Docker:

    ```
    "publishOptions": {
      "include": [
        ...
        "docker-compose.yml",
        "docker-compose.debug.yml",
        "Dockerfile.debug",
        "Dockerfile",
        ".dockerignore"
      ]
    },
    ```

1. Удалите предыдущую версию инструментов Docker и установите версию 0.40, а затем снова щелкните **Add > Docker Support** (Добавить > Поддержка Docker) в контекстном меню для веб-приложения ASP.NET Core или консольного приложения. В результате в проект будут добавлены новые необходимые артефакты Docker.

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>При попытке **добавить поддержку Docker** или запустить отладку (F5) приложения ASP.NET Core в контейнере отображается сообщение об ошибке

Иногда после удаления и установки расширений кэш Visual Studio MEF (Managed Extensibility Framework) может быть поврежден. В таком случае могут возникать различные сообщения об ошибках при добавлении поддержки Docker и попытке запустить или выполнить отладку (F5) приложения ASP.NET Core. В качестве временного решения выполните следующие действия, чтобы удалить и повторно создать кэш MEF.

1. Закройте все экземпляры Visual Studio.
1. Откройте %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\.
1. Удалите следующие папки:
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Запустите Visual Studio
1. Повторите сценарий снова.



<!--HONumber=Dec16_HO2-->


