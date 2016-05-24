<properties
   	pageTitle="Публикация приложений HDInsight | Microsoft Azure"
   	description="Узнайте, как создавать и публиковать приложения HDInsight."
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="05/16/2016"
   	ms.author="jgao"/>

# Публикация приложений HDInsight в Azure Marketplace

Пользователи могут устанавливать приложения HDInsight в кластере HDInsight под управлением Linux. Разработчиками этих приложений могут быть корпорация Майкрософт, независимые поставщики программного обеспечения или вы сами. Из этой статьи вы узнаете, как опубликовать приложение HDInsight в Azure Marketplace. Общие сведения о публикации в Azure Marketplace см. в статье [Как опубликовать предложение в Microsoft Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).

В приложениях HDInsight применяется модель *с использованием собственной лицензии (BYOL)*. В рамках этой модели поставщик приложения отвечает за лицензирование продукта для пользователей, а пользователи платят только за создаваемые ими ресурсы Azure, включая кластеры HDInsight, а также виртуальные машины и узлы. Сейчас счета за само приложение не выставляются в Azure.

Другие связанные статьи о приложениях HDInsight:

- [Install custom HDInsight application](hdinsight-apps-install-custom-applications.md) (Установка пользовательских приложений HDInsight) — узнайте, как устанавливать и тестировать пользовательские приложения HDInsight.

 
## Предварительные требования

Чтобы отправить пользовательское приложение в магазин, необходимо сначала создать и протестировать его. Ознакомьтесь со следующими статьями:

- [Install custom HDInsight application](hdinsight-apps-install-custom-applications.md) (Установка пользовательских приложений HDInsight) — узнайте, как устанавливать и тестировать пользовательские приложения HDInsight.

Также необходимо зарегистрировать учетную запись разработчика. См. статьи [Как опубликовать предложение в Microsoft Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) и [Создание учетной записи разработчика Майкрософт](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).

## Определение приложения

Публикация приложений в Azure Marketplace осуществляется в два этапа. Сначала необходимо определить файл **createUiDef.json**, чтобы указать кластеры, совместимые с приложением; затем можно опубликовать шаблон с помощью портала Azure. Ниже приведен пример файла createUiDef.json.

	{
		"handler": "Microsoft.HDInsight",
		"version": "0.0.1-preview",
		"clusterFilters": {
			"types": ["Hadoop", "HBase", "Storm", "Spark"],
			"tiers": ["Standard", "Premium"],
			"versions": ["3.4"]
		}
	}


|Поле | Описание | Возможные значения|
|-------|---------------|----------------|
|types |Типы кластеров, совместимые с приложением. |Hadoop, HBase, Storm, Spark (или любое их сочетание).|
|tiers |Уровни кластеров, совместимые с приложением. |"Стандартный", "Премиум" (или оба).|
|versions|	Версии кластеров HDInsight, совместимые с приложением. |3\.4|

## Пакет приложения

Создайте ZIP-файл, который содержит все необходимые файлы для установки приложений HDInsight. Этот ZIP-файл вам понадобится при [публикации приложения](#publish-application).

- [createUiDefinition.json](#define-application).
- mainTemplate.json. См. пример из статьи [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Установка пользовательских приложений HDInsight).
- Все необходимые скрипты.

> [AZURE.NOTE] Файлы приложения (включая имеющиеся файлы веб-приложения) могут быть расположены в любой общедоступной конечной точке.

## Публикация приложения

Чтобы опубликовать приложение HDInsight:

1. Войдите на [портал публикации Azure](https://publish.windowsazure.com/).
2. Щелкните **Шаблоны решений**, чтобы создать шаблон решения.
3. Щелкните **Create Dev Center account and join the Azure program** (Создать учетную запись центра разработчиков и присоединиться к программе Azure), чтобы зарегистрировать свою компанию, если это еще не сделано. См. статью [Создание учетной записи разработчика Майкрософт](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).
4. Щелкните **Define some Topologies to get Started** (Определить некоторые топологии и начать работу). Шаблон решения служит родительским элементом для всех своих топологий. В одном шаблоне предложений или решения можно определить сразу несколько топологий. Когда предложение переходит к стадии промежуточного развертывания, вместе с ним отправляются все его топологии. 
5. Добавьте новую версию.
6. Отправьте ZIP-файл, подготовленный на этапе работы с [пакетом приложения](#package-application).  
7. Щелкните **Request Certification** (Запросить сертификацию). Команда сертификации Майкрософт проверит файлы и выполнит сертификацию топологии.

## Дальнейшие действия

- [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Установка пользовательских приложений HDInsight) — узнайте, как развернуть в HDInsight приложение HDInsight с отмененной публикацией.
- [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md) — узнайте, как использовать действие сценария для установки дополнительных приложений.
- [Создание кластеров Hadoop под управлением Linux в HDInsight с помощью шаблонов ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md) — узнайте, как вызывать шаблоны ARM для создания кластеров HDInsight.

<!---HONumber=AcomDC_0518_2016-->