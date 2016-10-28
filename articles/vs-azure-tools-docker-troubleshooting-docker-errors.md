<properties
   pageTitle="Устранение неполадок клиента Docker в Windows с помощью Visual Studio | Microsoft Azure"
   description="Устранение неполадок, которые возникают при использовании Visual Studio для создания и развертывания веб-приложений в Docker в Windows с помощью Visual Studio."
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="allclark" />

# Устранение неполадок при разработке в Visual Studio Docker

При работе с инструментами Visual Studio для предварительной версии Docker могут возникать некоторые проблемы из-за природы предварительной версии. Ниже приведены некоторые распространенные проблемы и способы их устранения.


## Не удалось проверить сопоставление тома
Сопоставление тома требуется для доступа к исходному коду и двоичным файлам приложения из папки приложения в контейнере. Определенные сопоставления томов содержатся в файлах docker-compose.dev.debug.yml и docker-compose.dev.release.yml. Так как файлы изменяются на хост-компьютере, контейнеры отобразят эти изменения в той же структуре папок.

Чтобы включить сопоставление тома, в приложении Docker для Windows щелкните значок с изображением кита на панели задач, выберите пункт **Settings...** (Параметры...), а затем перейдите на вкладку **Shared Drives** (Общие диски). Удостоверьтесь, что диск, где размещен проект, а также диск с переменной среды %USERPROFILE% являются общими, а затем щелкните **Применить**.

Чтобы проверить, работает ли сопоставление тома, как только диски станут общими, выполните повторную сборку или нажмите клавишу F5 в Visual Studio либо выполните следующие действия из командной строки:

*В командной строке Windows*

*[Примечание. Предполагается, что папка Users находится на диске C и что к этому диску предоставлен общий доступ. Выполните соответствующее обновление, если общий доступ предоставлен к другому диску]*
```
docker run -it -v /c/Users/Public:/wormhole busybox
```

*В контейнере Linux*

```
/ # ls
```

Вы должны увидеть список каталогов из папки Users/Public. Если файлы не отображаются и папка /c/Users/Public не пустая, значит сопоставление тома настроено неправильно.

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Измените каталог на wormhole, чтобы просмотреть содержимое каталога `/c/Users/Public`:

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

**Примечание**. *При работе с виртуальными машинами Linux в файловой системе контейнера учитывается регистр.*

##Сборка. Непредвиденная ошибка при выполнении задачи PrepareForBuild

Microsoft.DotNet.Docker.CommandLine.ClientException. Произошла ошибка при попытке подключения.

Убедитесь, что узел Docker по умолчанию работает. Откройте окно командой строки и выполните следующую команду:

```
docker info
```

Если команда вернет ошибку, попытайтесь запустить классическое приложение **Docker для Windows**. В запущенном приложении на панели задач должен отображаться значок **с изображением кита**. Щелкните этот значок правой кнопкой мыши и выберите **Settings** (Параметры). Перейдите на вкладку **Reset** (Сброс) и выберите **Restart Docker..** (Перезапустить Docker..).

##Обновление версии 0.31 до версии 0.40 вручную


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
1. Удалите из файла Properties\\launchSettings.json следующие строки:

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

1. Удалите предыдущую версию инструментов Docker и установите версию 0.40, а затем снова щелкните **Add -> Docker Support** (Добавить -> Поддержка Docker) в контекстном меню для веб-приложения ASP.Net Core или консольного приложения. В результате в проект будут добавлены новые необходимые артефакты Docker.

## При попытке **добавить поддержку Docker** или запустить отладку (F5) приложения ASP.NET Core в контейнере отображается сообщение об ошибке

Иногда после удаления и установки расширений кэш Visual Studio MEF (Managed Extensibility Framework) может быть поврежден. В таком случае могут возникать различные сообщения об ошибках при добавлении поддержки Docker и попытке запустить или выполнить отладку (F5) приложения ASP.NET Core. В качестве временного решения выполните следующие действия, чтобы удалить и повторно создать кэш MEF.

1. Закройте все экземпляры Visual Studio.
1. Откройте %USERPROFILE%\\AppData\\Local\\Microsoft\\VisualStudio\\14.0\\.
1. Удалите следующие папки:
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Запустите Visual Studio
1. Повторите сценарий снова.

<!---HONumber=AcomDC_0921_2016-->