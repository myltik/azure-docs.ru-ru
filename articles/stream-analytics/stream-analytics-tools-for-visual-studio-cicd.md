---
title: Непрерывная интеграция и разработка с помощью инструментов Stream Analytics
description: Из этой статьи вы узнаете, как использовать инструменты Visual Studio для Azure Stream Analytics для настройки процессов непрерывной интеграции и развертывания.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 9/27/2017
ms.openlocfilehash: e4e831c602255df66f4c86ffa17336f51d2b52f7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30906279"
---
# <a name="continuously-integrate-and-develop-with-stream-analytics-tools"></a>Непрерывная интеграция и разработка с помощью инструментов Stream Analytics
Из этой статьи вы узнаете, как использовать инструменты Azure Stream Analytics для Visual Studio для настройки процессов непрерывной интеграции и развертывания.

Чтобы получить поддержку MSBuild, используйте [инструменты Stream Analytics для Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) версии 2.3.0000.0 или выше.

Доступен пакет NuGet [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Он предоставляет MSBuild, локальное выполнение и средства развертывания, которые поддерживают процесс непрерывной интеграции и развертывания проектов Stream Analytics Visual Studio. 
> [!NOTE] 
Пакет NuGet может использоваться только с версией 2.3.0000.0 или более поздней версией средств Stream Analytics для Visual Studio. При наличии проектов, созданных в предыдущих версиях средств Visual Studio, откройте его с помощью версии 2.3.0000.0 или более поздней версии и сохраните. После этого будут включены новые возможности. 

Дополнительные сведения см. в статье [Использование инструментов Azure Stream Analytics для Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="msbuild"></a>MSBuild
Как и в стандартном интерфейсе Visual Studio MSBuild, для выполнения сборки проекта имеются две возможности. Можно щелкнуть правой кнопкой мыши проект и выбрать **Собрать**. Можно также использовать **MSBuild** в пакете NuGet из командной строки.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

При успешном выполнении сборки проекта Stream Analytics Visual Studio создаются два файла шаблона Azure Resource Manager в папке **bin/[Debug/Retail]/Deploy**: 

*  файл шаблона Resource Manager;

       [ProjectName].JobTemplate.json 

*  файл параметров Resource Manager.

       [ProjectName].JobTemplate.parameters.json   

Параметры по умолчанию в файле parameters.json на основе параметров в проекте Visual Studio. При необходимости развертывания в другой среде замените соответствующие параметры.

> [!NOTE] 
Для всех учетных данных значения по умолчанию имеют значение NULL. Их *необходимо* установить перед развертыванием в облаке.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Узнайте, как [развернуть файл шаблона Resource Manager с помощью Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Узнайте, как [использовать объект в качестве параметра в шаблоне Resource Manager](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).


## <a name="command-line-tool"></a>Программа командной строки

### <a name="build-the-project"></a>Сборка проекта
В пакете NuGet есть служебная программа командной строки, которая называется **SA.exe**. Она поддерживает сборку проекта и локальное тестирование на произвольном компьютере, которое можно использовать в процессе непрерывной интеграции и непрерывной доставки. 

Файлы развертывания по умолчанию помещаются в текущем каталоге. С помощью параметра -OutputPath можно указать выходной путь.

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Тестирование сценария локально

Если в проекте указаны локальные входные файлы Visual Studio, можно выполнить автоматическое тестирование сценария с помощью команды *localrun*. Выходной результат помещается в текущий каталог.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Создайте файл определения задания для использования с API PowerShell для Stream Analytics.

Команда *arm* принимает шаблон задания и файл параметров шаблона задания, созданный путем сборки в качестве входных данных. Затем объедините их в файл определения задания JSON, который может использоваться с API PowerShell для Stream Analytics.

```
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Пример:
```
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```


