---
title: "Способы использования инструментов Stream Analytics Visual Studio для настройки процессов непрерывной интеграции и развертывания | Документация Майкрософт"
description: "Руководство по использованию средств Stream Analytics Visual Studio для настройки процессов непрерывной интеграции и развертывания."
keywords: visual studio, NuGet, DevOps, CI/CD
documentationcenter: 
services: stream-analytics
author: su-jie
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/27/2017
ms.author: sujie
ms.openlocfilehash: 947266dc94babab21556da26d8cc8c917cf81c12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="use-stream-analytics-visual-studio-tools-to-set-up-the-continuous-integration-and-deployment-process"></a>Использование средств Stream Analytics Visual Studio для настройки процессов непрерывной интеграции и развертывания
В этом руководстве показано, как использовать средства Stream Analytics Visual Studio для настройки процессов непрерывной интеграции и развертывания.

Последняя версия (2.3.0000.0 или более поздней версии) [средств Stream Analytics для Visual Studio](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio) добавляет поддержку **MSBuild**. 

Также имеется недавно выпущенный пакет NuGet [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Он предоставляет MSBuild, локальное выполнение и средства развертывания, которые поддерживают процесс непрерывной интеграции и развертывания проектов Stream Analytics Visual Studio. 
> [!NOTE] 
Пакет NuGet может использоваться только с версией 2.3.0000.0 или более поздней версией средства Stream Analytics для Visual Studio. При наличии проектов, созданных в предыдущих версиях средств Visual Studio, откройте его с помощью средства с версией 2.3.0000.0 или более поздней и сохраните. После этого новые возможности станут доступны. 

[Узнайте, как использовать средства Stream Analytics для Visual Studio](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio)

## <a name="msbuild"></a>MSBuild
Как и в стандартном интерфейсе Visual Studio MSBuild, для создания проекта можно щелкнуть правой кнопкой мыши по проекту, затем выбрать **Построение** или использовать **MSBuild** в пакете NuGet из командной строки.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

При успешном построении проекта Stream Analytics Visual Studio создается два файла шаблона Azure Resource Manager в папке **bin/[Debug/Retail]/Deploy**: 

Файл шаблона Azure Resource Manager
*       [Имя_проекта].JobTemplate.json 

Файл параметров Azure Resource Manager.
*       [Имя_проекта].JobTemplate.parameters.json   

Параметры по умолчанию в файле parameters.json на основе параметров в проекте Visual Studio. При необходимости развертывания в другой среде, замените соответствующие параметры. 
> [!NOTE] 
Для всех учетных данных значения по умолчанию имеют значение нуль. Их **НЕОБХОДИМО** установить перед развертыванием в облаке.
```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Узнайте как [развертывать ресурсы с использованием шаблонов Resource Manager и Azure PowerShell](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy) и [использовать объекты в качестве параметров в шаблоне Azure Resource Manager](https://docs.microsoft.com/en-us/azure/architecture/building-blocks/extending-templates/objects-as-parameters).


## <a name="command-line-tool"></a>Программа командной строки

### <a name="build-the-project"></a>Сборка проекта
В пакете NuGet есть программа командной строки, которая называется **SA.exe**. Она поддерживает сборку проекта и локальное тестирование на произвольном компьютере, которое можно использовать в процессе непрерывной интеграции и непрерывной поставки. 

Файлы развертывания по умолчанию помещаются в текущем каталоге. С помощью параметра -OutputPath можно указать выходной путь.

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Тестирование сценария локально

Если в проекте указаны локальные входные файлы Visual Studio, можно выполнить автоматическое тестирование сценария с помощью команды *localrun*. Выходной результат помещается в текущем каталоге.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-job-definition-file-to-use-with-stream-analytics-power-shell"></a>Создайте файл определения задания для использования с Stream Analytics Power Shell.

Команда *arm* принимает шаблон задания и файл параметров шаблона задания, созданный путем сборки в качестве входных данных. Затем объедините их в файл определения задания JSON, который может использоваться с API Stream Analytics PowerShell.

```
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```

```
Example
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```


