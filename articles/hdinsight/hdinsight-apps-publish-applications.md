---
title: "Публикация приложений HDInsight | Документация Майкрософт"
description: "Узнайте, как создавать и публиковать приложения HDInsight."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 14aef891-7a37-4cf1-8f7d-ca923565c783
ms.service: hdinsight
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/18/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 35a6c06bc4850f3fcfc6221d62998465f3b38251


---
# <a name="publish-hdinsight-applications-into-the-azure-marketplace"></a>Публикация приложений HDInsight в Azure Marketplace
Пользователи могут устанавливать приложения HDInsight в кластере HDInsight под управлением Linux. Разработчиками этих приложений могут быть корпорация Майкрософт, независимые поставщики программного обеспечения или вы сами. Из этой статьи вы узнаете, как опубликовать приложение HDInsight в Azure Marketplace.  Общие сведения о публикации в Azure Marketplace см. в статье [Как опубликовать предложение и управлять им в Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).

В приложениях HDInsight применяется модель *с использованием собственной лицензии (BYOL)*. В рамках этой модели поставщик приложения отвечает за лицензирование продукта для пользователей, а пользователи платят только за создаваемые ими ресурсы Azure, включая кластеры HDInsight, а также виртуальные машины и узлы. Сейчас счета за само приложение не выставляются в Azure.

Другие связанные статьи о приложениях HDInsight:

* [Установка приложений HDInsight](hdinsight-apps-install-applications.md)— узнайте, как устанавливать в кластер приложения HDInsight.
* [Установка пользовательских приложений HDInsight](hdinsight-apps-install-custom-applications.md)— узнайте, как устанавливать и тестировать пользовательские приложения HDInsight.

## <a name="prerequisites"></a>Предварительные требования
Чтобы отправить пользовательское приложение в магазин, необходимо сначала создать и протестировать его. Ознакомьтесь со следующими статьями:

* [Установка пользовательских приложений HDInsight](hdinsight-apps-install-custom-applications.md)— узнайте, как устанавливать и тестировать пользовательские приложения HDInsight.

Также необходимо зарегистрировать учетную запись разработчика. Дополнительные сведения см. в статье [Как опубликовать предложение и управлять им в Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) и [Создание учетной записи разработчика Майкрософт](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).

## <a name="define-application"></a>Определение приложения
Публикация приложений в Azure Marketplace осуществляется в два этапа.  Сначала необходимо определить файл **createUiDef.json** , чтобы указать кластеры, совместимые с приложением, а затем можно опубликовать шаблон с помощью портала Azure. Ниже приведен пример файла createUiDef.json.

    {
        "handler": "Microsoft.HDInsight",
        "version": "0.0.1-preview",
        "clusterFilters": {
            "types": ["Hadoop", "HBase", "Storm", "Spark"],
            "tiers": ["Standard", "Premium"],
            "versions": ["3.4"]
        }
    }


| Поле | Описание | Возможные значения |
| --- | --- | --- |
| types |Типы кластеров, совместимые с приложением. |Hadoop, HBase, Storm, Spark (или любое их сочетание). |
| tiers |Уровни кластеров, совместимые с приложением. |"Стандартный", "Премиум" (или оба). |
| versions |Версии кластеров HDInsight, совместимые с приложением. |3.4 |

## <a name="package-application"></a>Пакет приложения
Создайте ZIP-файл, который содержит все необходимые файлы для установки приложений HDInsight. Этот ZIP-файл вам понадобится при [публикации приложения](#publish-application).

* [createUiDefinition.json](#define-application).
* mainTemplate.json. См. пример из статьи [Установка пользовательских приложений HDInsight](hdinsight-apps-install-custom-applications.md).
  
  > [!IMPORTANT]
  > Имя скрипта установки приложения должно быть уникальным для определенного кластера и иметь следующий формат: Кроме того, любые действия скриптов установки и удаления должны быть идемпотентными. Это значит, что скрипты можно вызывать повторно с тем же результатом.
  > 
  > name": "[concat('hue-install-v0','-' ,uniquestring(‘applicationName’)]"
  > 
  > Обратите внимание, что имя скрипта состоит из трех частей:
  > 
  > 1. Префикс имени, который состоит из имени приложения или имени, которое связано с приложением.
  > 2. Дефис (-) для удобства чтения.
  > 3. Уникальная строковая функция с именем приложения в качестве параметра.
  > 
  > Например, в сохраненном списке действий скрипта приведенное выше имя будет выглядеть так: hue-install-v0-4wkahss55hlas. Пример полезных данных JSON см. здесь: [https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 
  > 
* Все необходимые скрипты.

> [!NOTE]
> Файлы приложения (включая имеющиеся файлы веб-приложения) могут быть расположены в любой общедоступной конечной точке.
> 
> 

## <a name="publish-application"></a>публикации приложения
Чтобы опубликовать приложение HDInsight:

1. Войдите на [портал публикации Azure](https://publish.windowsazure.com/).
2. Щелкните **Шаблоны решений** слева, чтобы создать шаблон решения.
3. Введите заголовок и нажмите кнопку **Создать шаблон решения**.
4. Щелкните **Create Dev Center account and join the Azure program** (Создать учетную запись центра разработчиков и присоединиться к программе Azure), чтобы зарегистрировать свою компанию, если вы еще этого не сделали.  См. статью [Создание учетной записи разработчика Майкрософт](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).
5. Щелкните **Define some Topologies to get Started**(Определить некоторые топологии и начать работу). Шаблон решения служит родительским элементом для всех своих топологий. В одном шаблоне предложений или решения можно определить сразу несколько топологий. Когда предложение переходит к стадии промежуточного развертывания, вместе с ним отправляются все его топологии. 
6. Введите имя топологии, а затем щелкните знак "плюс".
7. Введите новую версию, а затем щелкните знак "плюс".
8. Отправьте ZIP-файл, подготовленный на этапе работы с [пакетом приложения](#package-application).  
9. Щелкните **Request Certification**(Запросить сертификацию). Команда сертификации Майкрософт проверит файлы и выполнит сертификацию топологии.

## <a name="next-steps"></a>Дальнейшие действия
* [Установка приложений HDInsight](hdinsight-apps-install-applications.md)— узнайте, как устанавливать в кластер приложения HDInsight.
* [Установка пользовательских приложений HDInsight](hdinsight-apps-install-custom-applications.md)— узнайте, как развернуть в HDInsight приложение HDInsight с отмененной публикацией.
* [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md)— узнайте, как использовать действие скрипта для установки дополнительных приложений.
* [Создание кластеров Hadoop под управлением Linux в HDInsight с помощью шаблонов ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md)— узнайте, как вызывать шаблоны Resource Manager для создания кластеров HDInsight.
* [Использование пустых граничных узлов в HDInsight](hdinsight-apps-use-edge-node.md)— узнайте, как использовать пустой граничный узел для доступа к кластеру HDInsight, а также тестирования и размещения приложений HDInsight.




<!--HONumber=Dec16_HO2-->


