<properties
	pageTitle="Новые возможности машинного обучения | Microsoft Azure"
	description="Новые возможности в Машинном обучении Azure."
	services="machine-learning"
	documentationCenter=""
	authors="vDonGlover"
	manager="raymondl"
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/15/2016"
	ms.author="v-donglo"/>

# Новые возможности Машинного обучения Azure

Обновления Машинного обучения Microsoft Azure за июль 2016 года обеспечивают приведенные ниже возможности.

* Веб-службы теперь управляются как ресурсы Azure, с помощью интерфейсов [Azure Resource Manager](../resource-group-overview.md), что обеспечивает следующие усовершенствования:
	* Добавлены новые [интерфейсы REST API](https://msdn.microsoft.com/library/azure/Dn950030.aspx) для развертывания веб-служб на основе Resource Manager и управления ими.
	* Доступен новый портал [веб-служб Машинного обучения Microsoft Azure](https://services.azureml.net/), который обеспечивает централизованное управление всеми аспектами веб-службы.
* Добавлена новая модель развертывания веб-служб в нескольких регионах по подписке, в которой применяются интерфейсы API на основе Resource Manager, использующие поставщик ресурсов Resource Manager для веб-служб.
* Добавлены новые [ценовые планы](https://azure.microsoft.com/pricing/details/machine-learning/) и возможности планирования управления, использующие новые функции поставщика ресурсов Resource Manager для выставления счетов.
	* Теперь можно [развернуть веб-службу в нескольких регионах](machine-learning-how-to-deploy-to-multiple-regions.md) без необходимости создавать новую подписку в каждом из них.
* Предоставляется [статистика использования](machine-learning-manage-new-webservice.md) веб-службы.
* Упрощено тестирование функции RRS (запрос-ответ) в Машинном обучении Azure с помощью образца данных.
* Добавлена новая страница тестирования BES, на которой можно использовать образец данных и журнал отправки заданий.

Кроме того, Студия машинного обучения была обновлена, чтобы можно было развертывать новую модель веб-служб или продолжить развертывание классической модели веб-служб.

<!---HONumber=AcomDC_0727_2016-->