---
title: "Установка расширения устойчивых функций и примеров в Azure"
description: "Сведения о том, как установить расширение устойчивых функций для Функций Azure для разработки с помощью портала или Visual Studio."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 03326304e438f3b716c43d268f07d8e22cd15ea3
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2017
---
# <a name="install-the-durable-functions-extension-and-samples-azure-functions"></a>Установка расширения устойчивых функций и примеров (Функции Azure)

Расширение [устойчивых функций](durable-functions-overview.md) для Функций Azure предоставляется в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). В этой статье показано, как установить пакет и набор примеров для следующих сред разработки:

* Visual Studio 2017 (рекомендуется) 
* Портал Azure

## <a name="visual-studio-2017"></a>Visual Studio 2017

Сейчас Visual Studio обеспечивает максимальное удобство разработки приложений с использованием устойчивых функций.  Функции можно выполнять локально, а также публиковать в Azure. Можно начать с пустого проекта или набора примеров функций.

### <a name="prerequisites"></a>Предварительные требования

* Установите [последнюю версию Visual Studio](https://www.visualstudio.com/downloads/) (15.3 или выше). Включите рабочую нагрузку **разработки Azure** в параметрах установки.

### <a name="start-with-sample-functions"></a>Начало работы с примерами функций

1. Скачайте [ZIP-файл с примерами приложений для Visual Studio](https://azure.github.io/azure-functions-durable-extension/files/VSDFSampleApp.zip). Ссылку на NuGet добавлять не нужно, так как она уже содержится в примере проекта.
2. Установите и запустите [эмулятор хранилища Azure](https://docs.microsoft.com/azure/storage/storage-use-emulator) версии 5.2 или выше. Вместо этого можно также обновить файл *local.appsettings.json* строки подключения к реальному хранилищу Azure.
3. Откройте проект в Visual Studio 2017. 
4. Инструкции о том, как запустить пример, см. в руководстве по [созданию цепочек функций с примером последовательности приветствия](durable-functions-sequence.md). Пример можно выполнить локально или опубликовать в Azure.

### <a name="start-with-an-empty-project"></a>Начало работы с пустым проектом
 
Следуйте тем же инструкциям, что и в начале работы с примерами, но вместо скачивания *ZIP*-файла выполните следующие действия.

1. Создайте проект приложения-функции.
2. Добавьте следующую ссылку на пакет NuGet в файл с расширением *.csproj*:

   ```xml
   <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.DurableTask" Version="1.0.0-beta" />
   ```

## <a name="azure-portal"></a>Портал Azure

При желании для разработки с использованием устойчивых функций можно использовать портал Azure.

### <a name="create-an-orchestrator-function"></a>Создание функции оркестратора

1. Создайте приложение-функцию на сайте [functions.azure.com](https://functions.azure.com/signin).
2. Настройте в приложении-функции [использование среды выполнения версии 2.0](functions-versions.md).
3. Создайте функцию, установив для языка значение C#, а для сценария — "Все". Затем выберите шаблон **Durable Functions Orchestrator — C#** (Оркестратор устойчивых функций — C#).
4. В разделе **Расширения не установлены**, нажмите кнопку **Установить**, чтобы скачать расширение с сайта NuGet.org.

### <a name="copy-sample-code-to-the-function-app"></a>Копирование примера кода в приложение-функцию

1. Скачайте файл [DFSampleApp.zip](https://github.com/Azure/azure-functions-durable-extension/raw/master/docfx/files/DFSampleApp.zip).
2. Распакуйте файл примера в папку `D:\home\site\wwwroot` в приложении-функции, используя Kudu или FTP.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Запуск примера создания цепочки функций](durable-functions-sequence.md)
