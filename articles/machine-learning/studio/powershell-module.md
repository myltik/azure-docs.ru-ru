---
title: Модуль PowerShell для машинного обучения Документация Майкрософт | Документация Майкрософт
description: Модуль PowerShell для машинного обучения Azure доступен в режиме общедоступной предварительной версии. Используйте PowerShell для создания рабочих областей, экспериментов, веб-служб, а также управления этими компонентами.
keywords: эксперимент,линейная регрессия,алгоритмы машинного обучения,руководство по машинному обучению,приемы прогнозного моделирования,эксперимент по обработке и анализу данных
services: machine-learning
documentationcenter: ''
author: hning86
ms.author: haining
manager: mwinkle
editor: cgronlun
ms.assetid: a9001cc2-3aa0-47e1-b175-1f76408ba1d1
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.openlocfilehash: 6ecd2d9a1519cd89058385ad1e40aee9b3fc9082
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835701"
---
# <a name="powershell-module-for-microsoft-azure-machine-learning"></a>Модуль PowerShell для машинного обучения Microsoft Azure
Модуль PowerShell для машинного обучения Azure — это мощный инструмент, который позволяет использовать Windows PowerShell для управления рабочими областями, экспериментами, наборами данных, классическими веб-службами и пр.

На странице [https://aka.ms/amlps](https://aka.ms/amlps) вы можете просмотреть документацию и скачать модуль, в том числе полный исходный код. 

> [!NOTE]
> В настоящее время модуль PowerShell для машинного обучения Azure находится в предварительной версии. Мы работаем над улучшением модуля. Он будет дополнен в течение этого периода предварительной версии. Обратите внимание на блог, посвященный [Cortana Intelligence и машинному обучению](https://blogs.technet.microsoft.com/machinelearning/). В этом блоге публикуются новости и дополнительные сведения.

## <a name="what-is-the-machine-learning-powershell-module"></a>Что такое модуль PowerShell для машинного обучения?
Модуль PowerShell для машинного обучения — это библиотека DLL на основе .NET, которая позволяет с помощью Windows PowerShell управлять рабочими областями, экспериментами, наборами данных, классическими веб-службами и конечными точками классических веб-служб машинного обучения Azure. 

Вместе с модулем также можно скачать полный исходный код, который включает четко разделенный [уровень API C#](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs). На эту библиотеку DLL можно ссылаться из собственного проекта .NET, а также управлять машинным обучением Azure с помощью кода .NET. Кроме того библиотека DLL зависит от базовых интерфейсов REST API, которые можно использовать непосредственно из клиента, с которым вы работаете.

## <a name="what-can-i-do-with-the-powershell-module"></a>Что можно сделать с помощью модуля PowerShell?
Ниже описаны некоторые задачи, которые можно выполнить с помощью этого модуля PowerShell. Ознакомьтесь с [полной документацией](https://aka.ms/amlps) по этим и многим другим функциям.

* Подготовка новой рабочей области с помощью сертификата управления ([New-AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace)).
* Экспорт и импорт файла JSON, представляющего граф эксперимента ([Export-AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) и [Import-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph)).
* Запуск эксперимента ([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment)).
* Создание веб-службы из прогнозного эксперимента ([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice)).
* Создание конечной точки в опубликованной веб-службе ([Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint)).
* Вызов конечной точки веб-службы BES и (или) RRS ([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) и [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint)).

Ниже представлен пример использования PowerShell для запуска существующего эксперимента:

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Подробное описание использования модуля PowerShell для автоматизации часто запрашиваемых задач см. в статье, посвященной [созданию разных конечных точек веб-служб и моделей машинного обучения с помощью PowerShell в рамках одного эксперимента](create-models-and-endpoints-with-powershell.md).

## <a name="how-do-i-get-started"></a>Как начать работу?
Чтобы начать работу с модулем PowerShell для машинного обучения, скачайте [пакет выпуска](https://github.com/hning86/azuremlps/releases) из GitHub и следуйте [инструкциям по установке](https://github.com/hning86/azuremlps/blob/master/README.md). В инструкциях показано, как разблокировать скачанную и распакованную библиотеку DLL, а затем импортировать ее в среду PowerShell. Для большинства командлетов нужно указать идентификатор рабочей области, маркер проверки подлинности рабочей области, а также регион Azure, в котором эта рабочая область находится. Чтобы задать значения, проще всего использовать файл config.json по умолчанию. В инструкциях также указано, как настроить этот файл. 

Кроме того, вы также можете клонировать дерево Git, а также изменить или компилировать код локально, используя Visual Studio.

## <a name="next-steps"></a>Дополнительная информация
Полный комплект документации по модулю PowerShell можно найти на странице [https://aka.ms/amlps](https://aka.ms/amlps). 

Развернутый пример использования модуля в реалистичном сценарии см. в подробном сценарии использования в статье [Создание множества моделей машинного обучения и конечных точек веб-службы из одного эксперимента с помощью PowerShell](create-models-and-endpoints-with-powershell.md).
