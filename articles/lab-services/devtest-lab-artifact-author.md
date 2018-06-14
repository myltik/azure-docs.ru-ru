---
title: Создание пользовательских артефактов для виртуальной машины DevTest Labs | Документация Майкрософт
description: Узнайте, как создавать собственные артефакты, которые можно использовать с помощью решения Azure DevTest Labs.
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 32dcdc61-ec23-4a01-b731-78c029ea5316
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 268b9af7835c51d78812b35aff5aaac585961b01
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33781943"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Создание пользовательских артефактов для виртуальной машины DevTest Labs

Просмотрите следующее видео, чтобы ознакомиться с действиями, описанными в этой статье:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
> 
> 

## <a name="overview"></a>Обзор
Вы можете использовать *артефакты* для развертывания и настройки приложения после подготовки виртуальной машины. Артефакт состоит из файла определения артефакта и других файлов скриптов, которые хранятся в папке в репозитории Git. Файлы определения артефактов состоят из JSON и выражений, позволяющих указать, какие компоненты должны быть установлены на виртуальной машине. Например, можно определить имя артефакта, выполняемую команду и параметры, которые станут доступны после запуска команды. В файле определения артефакта можно ссылаться на другие файлы скриптов, используя их имена.

## <a name="artifact-definition-file-format"></a>Формат файла определения артефакта
В следующем примере показаны разделы, составляющие базовую структуру файла определения:

    {
      "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2016-11-28/dtlArtifacts.json",
      "title": "",
      "description": "",
      "iconUri": "",
      "targetOsType": "",
      "parameters": {
        "<parameterName>": {
          "type": "",
          "displayName": "",
          "description": ""
        }
      },
      "runCommand": {
        "commandToExecute": ""
      }
    }

| Имя элемента | Обязательный? | ОПИСАНИЕ |
| --- | --- | --- |
| $schema |Нет  |Расположение файла схемы JSON, позволяющего проверять подлинность файла определения. |
| title |Yes |Имя артефакта отображается в лаборатории. |
| description |Yes |Описание артефакта отображается в лаборатории. |
| iconUri |Нет  |URI значка отображается в лаборатории. |
| targetOsType |Yes |Операционная система виртуальной машины, на которую устанавливается артефакт. Поддерживаемые параметры: Windows и Linux. |
| parameters |Нет  |Значения, предоставляемые, когда на компьютере выполняется команда установки артефакта. Это поможет вам настроить артефакт. |
| runCommand |Yes |Команда установки артефакта, выполняемая на виртуальной машине. |

### <a name="artifact-parameters"></a>Параметры артефакта
В разделе параметров файла определения указываются значения, которые пользователь может вводить при установке артефакта. Это значения можно использовать в команде установки артефакта.

Параметры определяются с помощью следующей структуры.

    "parameters": {
        "<parameterName>": {
          "type": "<type-of-parameter-value>",
          "displayName": "<display-name-of-parameter>",
          "description": "<description-of-parameter>"
        }
      }

| Имя элемента | Обязательный? | ОПИСАНИЕ |
| --- | --- | --- |
| Тип |Yes |Тип значения параметра. Ниже приведен список допустимых типов. |
| displayName |Yes |Имя параметра, отображаемое для пользователя в лаборатории. | |
| description |Yes |Описание параметра, отображаемого в лаборатории. |

Допустимые типы:

* string (любая допустимая строка JSON);
* int (любое допустимое целое число JSON);
* bool (любое допустимое логическое значение JSON);
* array (любой допустимый массив JSON).

## <a name="artifact-expressions-and-functions"></a>Выражения и функции артефактов
Для ограничения команды установки артефактов можно использовать выражения и функции.
Выражения заключаются в квадратные скобки ([ и ]) и вычисляются при установке артефакта. Выражения могут встречаться в любом месте строкового значения JSON и всегда возвращают другое значение JSON. Если нужно использовать строковый литерал, который начинается с квадратной скобки ([), необходимо указать две квадратные скобки ([[).
Как правило, для составления значения используются выражения с функциями. Как и в языке JavaScript, вызовы функций форматируются в виде **functionName(arg1, arg2, arg3)**.

Ниже приведен список распространенных функций.

* **parameters(parameterName)**  — возвращает значение параметра, предоставляемого при выполнении команды артефакта.
* **concat(arg1, arg2, arg3,….. )** — объединяет несколько строковых значений. Эта функция может принимать различные аргументы.

В следующем примере показано, как составить значение, используя выражения и функции:

    runCommand": {
         "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>Создание пользовательского артефакта

1. Установите редактор JSON — он требуется для работы с файлами определения артефактов. Мы советуем использовать [Visual Studio Code](https://code.visualstudio.com/), доступный для Windows, Linux и OS X.
2. Получите пример файла определения artifactfile.json. Изучите артефакты, созданные командой DevTest Labs, в [репозитории GitHub](https://github.com/Azure/azure-devtestlab). Он содержит богатую библиотеку артефактов, которая поможет вам создавать собственные артефакты. Загрузите файл определения артефакта и внесите в него изменения, чтобы создать свои собственные артефакты.
3. Используйте IntelliSense — он позволяет просматривать элементы, которые можно использовать для создания файла определения артефакта. Здесь же можно увидеть различные варианты значений каждого элемента. Например, при редактировании элемента **targetOsType** IntelliSense предлагает два варианта — для Windows и для Linux.
4. Сохраните артефакт в [репозитории Git](devtest-lab-add-artifact-repo.md).
   
   1. Создайте для каждого артефакта отдельный каталог. Имя каталога должно совпадать с именем артефакта.
   2. Сохраните файл определения артефакта (artifactfile.json) в созданный каталог.
   3. Сохранить скрипты, на которые ссылается команда установки артефакта.
      
      Вот как может выглядеть папка артефакта:
      
      ![Пример папки артефакта](./media/devtest-lab-artifact-author/git-repo.png)
5. Добавьте репозиторий артефактов в лабораторию. Дополнительные сведения см. в статье [Добавление репозитория Git для хранения пользовательских артефактов и шаблонов Azure Resource Manager](devtest-lab-add-artifact-repo.md).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-articles"></a>Связанные статьи
* [Диагностика сбоев артефактов на виртуальной машине Azure DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Join a VM to existing AD Domain using ARM template in Azure Dev Test Lab](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs) (Присоединение виртуальной машины к имеющемуся домену AD с помощью шаблона ARM в Azure DevTest Labs)

## <a name="next-steps"></a>Дополнительная информация
* Узнайте, как [добавить репозиторий артефактов Git в лабораторию](devtest-lab-add-artifact-repo.md).

