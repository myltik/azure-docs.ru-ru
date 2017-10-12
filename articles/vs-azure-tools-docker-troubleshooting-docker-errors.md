---
title: "Устранение неполадок клиента Docker в Windows с помощью Visual Studio | Документация Майкрософт"
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
ms.openlocfilehash: 89fa04a1107b6abb49aefd68066443717ac9b731
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-visual-studio-docker-development"></a>Устранение неполадок при разработке Docker в Visual Studio

При работе с инструментами Visual Studio для предварительной версии Docker могут возникать некоторые проблемы из-за особенностей предварительной версии.
Далее приведены некоторые распространенные проблемы и способы их устранения.  

## <a name="visual-studio-2017-rc"></a>Visual Studio 2017 RC

### <a name="linux-containers"></a>**Контейнеры Linux**

####  <a name="build-errors-occur-when-debugging-a-net-core-web-or-console-application"></a>При отладке веб-приложения или консольного приложения .NET Core возникают ошибки сборки.  

Это может быть связано с отсутствием общего доступа к диску, на котором расположен проект с Docker для Windows.  Может появляться сообщение об ошибке следующего вида.

```
The "PrepareForLaunch" task failed unexpectedly.
Microsoft.DotNet.Docker.CommandLineClientException: Creating network "webapplication13628050196_default" with the default driver
Building webapplication1
Creating webapplication13628050196_webapplication1_1
ERROR: for webapplication1  Cannot create container for service webapplication1: C: drive is not shared. Please share it in Docker for Windows Settings
```
Действия для устранения этой проблемы.

1. Щелкните правой кнопкой мыши **Docker for Windows** (Docker для Windows) в области уведомлений, а затем выберите **Параметры**.  
2. Перейдите на вкладку **Shared Drives** (Общие диски) и предоставьте доступ к диску, на котором находится проект.

### <a name="windows-containers"></a>**Контейнеры Windows**

Ниже описываются особенности отладки веб-приложений и консольных приложений .NET Framework в контейнерах Windows.

#### <a name="prerequisites"></a>Предварительные требования

1. Должна быть установлена Visual Studio 2017 RC (или более поздняя версия) с .NET Core и Docker (предварительная версия).
2. Юбилейное обновление Windows 10 с последними обновлениями и исправлениями для Windows. В частности, должно быть установлено обновление [KB3194798](https://support.microsoft.com/en-us/help/3194798/cumulative-update-for-windows-10-version-1607-and-windows-server-2016-october-11,-2016). 
3. Должен быть установлен компонент [Docker для Windows](https://docs.docker.com/docker-for-windows/) (сборка 1.13.0 или более поздней версии).
4. Должен быть выбран параметр **Switch to Windows containers** (Переключение на контейнеры Windows). В области уведомлений щелкните **Docker for Windows** (Docker для Windows) и выберите **Switch to Windows containers** (Переключение на контейнеры Windows). После перезагрузки виртуальной машины убедитесь, что этот параметр остался включенным.

#### <a name="console-output-does-not-appear-in-visual-studios-output-window-while-debugging-a-console-application"></a>Вывод консоли не отображается в окне вывода Visual Studio при отладке консольного приложения.

Это известная проблема отладчика Visual Studio (msvsmon.exe), который сейчас не предназначен для такого использования. Поддержка этого сценария, возможно, будет добавлена в будущем выпуске. Для просмотра выходных данных консольного приложения в Visual Studio используйте **Docker: Start Project** (Docker: запустить проект), что эквивалентно операции **Start without Debugging** (Запуск без отладки).

#### <a name="debugging-web-applications-with-the-release-configuration-fails-with-403-forbidden-error"></a>При отладке веб-приложений в конфигурации выпуска возникает ошибка "(403) Запрещено".

Чтобы решить эту проблему, откройте файл web.release.config в решении и закомментируйте или удалите приведенные ниже строки.

```
<compilation xdt:Transform="RemoveAttributes(debug)" />
```

## <a name="visual-studio-2015"></a>Visual Studio 2015

### <a name="linux-containers"></a>**Контейнеры Linux**

#### <a name="unable-to-validate-volume-mapping"></a>Не удалось проверить сопоставление тома
Сопоставление тома требуется для доступа к исходному коду и двоичным файлам приложения из папки приложения в контейнере.  Определенные сопоставления томов содержатся в файлах docker-compose.dev.debug.yml и docker-compose.dev.release.yml. Так как файлы изменяются на хост-компьютере, контейнеры отобразят эти изменения в той же структуре папок.

Вот как включить сопоставление тома.

1. Щелкните значок **кита** в области уведомлений и выберите **Settings** (Параметры).
2. Выберите **Shared Drives** (Общие диски).
3. Выберите диск, на котором размещен ваш проект, и диск, на котором находится профиль пользователя (%USERPROFILE%).
4. Нажмите кнопку **Применить**.

Чтобы проверить, работает ли сопоставление тома, выполните повторную сборку или нажмите клавишу F5 в Visual Studio. Или же выполните следующие команды из командной строки.

> [!NOTE]
> В этом примере предполагается, что папка Users находится на диске C и что к этому диску предоставлен общий доступ.
> Измените настройки, если общий доступ предоставлен к другому диску.

```
docker run -it -v /c/Users/Public:/wormhole busybox
```

Выполните следующий код в контейнере Linux.

```
/ # ls
```

Вы должны увидеть список каталогов из папки Users/Public. Если файлы не отображаются и папка /c/Users/Public не пустая, значит сопоставление тома настроено неправильно.

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Перейдите в каталог wormhole, чтобы просмотреть содержимое каталога `/c/Users/Public`:

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

> [!NOTE]
> При работе с виртуальными машинами Linux в файловой системе контейнера учитывается регистр.

## <a name="build-prepareforbuild-task-failed-unexpectedly"></a>Сборка. Непредвиденная ошибка при выполнении задачи PrepareForBuild

Microsoft.DotNet.Docker.CommandLine.ClientException. Произошла ошибка при попытке подключения.

Убедитесь, что запущен узел Docker по умолчанию. Откройте окно командой строки и выполните следующую команду:

```
docker info
```

Если команда вернет ошибку, попытайтесь запустить классическое приложение **Docker для Windows** . При запущенном приложении в области уведомлений должен отображаться значок **кита**. Щелкните значок **кита** правой кнопкой мыши и откройте **Settings** (Параметры). Щелкните **Reset** (Сброс) и перезапустите Docker.

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>В контейнере отображается сообщение об ошибке при попытке добавить поддержку Docker или запустить отладку (F5) для приложения ASP.NET Core

Иногда после удаления и установки расширений может повреждаться кэш Visual Studio MEF (Managed Extensibility Framework). В таком случае могут возникать разные сообщения об ошибках при добавлении поддержки Docker и (или) попытке запустить или выполнить отладку (F5) для приложения ASP.NET Core. В качестве временного решения удалите и повторно создайте кэш MEF, используя следующие действия.

1. Закройте все экземпляры Visual Studio.
1. Откройте каталог %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\.
1. Удалите следующие папки:
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Откройте Visual Studio.
1. Запустите сценарий снова.
