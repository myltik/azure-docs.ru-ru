<properties
	pageTitle="Модуль PowerShell для машинного обучения Microsoft Azure | Microsoft Azure"
	description="Модуль PowerShell для машинного обучения Azure доступен в режиме общедоступной предварительной версии. Используйте PowerShell для создания рабочих областей, экспериментов, веб-служб, а также управления этими компонентами."
	keywords="эксперимент,линейная регрессия,алгоритмы машинного обучения,руководство по машинному обучению,приемы прогнозного моделирования,эксперимент по обработке и анализу данных"
	services="machine-learning"
	documentationCenter=""
	authors="hning86"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/05/2016"
	ms.author="garye;haining"/>

# Модуль PowerShell для машинного обучения Microsoft Azure

Модуль PowerShell для машинного обучения Azure — это мощное средство, которое позволяет использовать Windows PowerShell для управления рабочими областями, экспериментами, наборами данных, веб-службами и пр.

На странице [https://aka.ms/amlps](https://aka.ms/amlps) вы можете просмотреть документацию и скачать модуль, в том числе полный исходный код.

## Что такое модуль PowerShell для машинного обучения?

Модуль PowerShell для машинного обучения — это библиотека DLL на основе .NET, которая позволяет с помощью Windows PowerShell управлять рабочими областями, экспериментами, наборами данных, веб-службами и конечными точками веб-службы машинного обучения Azure. Вместе с модулем также можно скачать полный исходный код, который включает четко разделенный [уровень API C#](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs). Это означает, что на эту библиотеку DLL можно ссылаться из собственного проекта .NET, а также управлять машинным обучением Azure с помощью кода .NET. Кроме того библиотека DLL зависит от базовых интерфейсов REST API, которые можно использовать непосредственно из клиента, с которым вы работаете.

## Что можно сделать с помощью модуля PowerShell?

Ниже описаны некоторые задачи, которые можно выполнить с помощью этого модуля PowerShell. Ознакомьтесь с [полной документацией](https://aka.ms/amlps) по этим и многим другим функциям.

- Подготовка новой рабочей области с помощью сертификата управления ([New-AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace)).
- Экспорт и импорт файла JSON, представляющего граф эксперимента ([Export-AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) и [Import-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph)).
- Запуск эксперимента ([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment)).
- Создание веб-службы из прогнозного эксперимента ([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice)).
- Создание конечной точки в опубликованной веб-службе ([Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint)).
- Вызов конечной точки веб-службы BES и (или) RRS ([Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) и [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint)).

Ниже представлен пример использования PowerShell для запуска существующего эксперимента:

		#Find the first Experiment named “xyz”
		$exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
		#Run the Experiment
		Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Более подробное описание использования модуля PowerShell для автоматизации часто запрашиваемых задач см. в статье, посвященной [созданию разных конечных точек веб-служб и моделей машинного обучения с помощью PowerShell в рамках одного эксперимента](machine-learning-create-models-and-endpoints-with-powershell.md).

## Как начать работу?

Чтобы начать работу с модулем PowerShell для машинного обучения, скачайте [пакет выпуска](https://github.com/hning86/azuremlps/releases) из GitHub и следуйте [инструкциям по установке](https://github.com/hning86/azuremlps/blob/master/README.md). Разблокируйте загруженную и распакованную библиотеку DLL, а затем импортируйте ее в среду PowerShell. Для большинства командлетов нужно указать идентификатор рабочей области, маркер проверки подлинности рабочей области, а также регион Azure, в котором эта рабочая область находится. Проще всего это сделать с помощью стандартного файла config.json, особенности работы с которым подробно описаны в инструкциях по установке. Кроме того, вы также можете клонировать дерево Git, а также изменить или компилировать код локально, используя Visual Studio.

## Дальнейшие действия

Мы работаем над улучшением модуля PowerShell; он будет дополнен в течение этого периода предварительной версии. Обратите внимание на блог, посвященный [Cortana Analytics и машинному обучению](https://blogs.technet.microsoft.com/machinelearning/). В этом блоге публикуются новости и дополнительные сведения.

<!---HONumber=AcomDC_0914_2016-->