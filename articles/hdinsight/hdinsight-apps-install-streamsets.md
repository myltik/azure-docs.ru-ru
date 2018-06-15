---
title: Установка опубликованного приложения "Сборщик данных StreamSets" в Azure HDInsight | Документация Майкрософт
description: Установка и использование стороннего приложения Hadoop "Сборщик данных StreamSets".
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: e433de82576f8b943988881ed0b6673c0dccd77e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31401029"
---
# <a name="install-published-application---streamsets-data-collector"></a>Установка опубликованного приложения "Сборщик данных StreamSets"

В этой статье описывается, как установить и запустить опубликованное приложение Hadoop [Сборщик данных StreamSets](https://streamsets.com/) в Azure HDInsight. Обзор платформы приложений HDInsight и список доступных опубликованных приложений независимых поставщиков программного обеспечения приведены в статье [Установка сторонних приложений Hadoop в Azure HDInsight](hdinsight-apps-install-applications.md). Инструкции по установке собственного приложения см. в статье [Установка пользовательских приложений HDInsight](hdinsight-apps-install-custom-applications.md).

## <a name="about-streamsets-data-collector"></a>Сведения о сборщике данных StreamSets

Сборщик данных StreamSets развертывается в дополнение к приложению Azure HDInsight. Сборщик данных StreamSets предоставляет полнофункциональную интегрированную среду разработки (IDE), которая позволяет создавать, тестировать, развертывать любые конвейеры и управлять ими. Эти конвейеры могут объединять потоковые и пакетные данные, а также включают различные преобразования в поток. И все это без необходимости писать пользовательский код.

Сборщик данных StreamSets позволяет создавать потоки данных с помощью множества компонентов больших данных, таких как HDFS, Kafka, Solr, Hive, HBASE и Kudu. Так как сборщик данных StreamSets работает на пограничном сервере или в кластере Hadoop, вы можете отслеживать аномалии данных и операции потока данных в режиме реального времени. Этот мониторинг включает оповещение на основе пороговых значений, обнаружение аномалий и автоматическое исправление ошибок.

Сборщик данных StreamSets предназначен для логической изоляции каждого этапа в конвейере, поэтому вы можете отказаться от новых процессоров и соединителей и достигнуть нужного результата без кодирования и с минимальным временем простоя.

### <a name="streamsets-resource-links"></a>Ссылки на ресурсы StreamSets

* [Документация](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html)
* [Блог](https://streamsets.com/blog/)
* [Руководства](https://github.com/streamsets/tutorials)
* [Форум поддержки Developer](https://groups.google.com/a/streamsets.com/forum/#!forum/sdc-user)
* [Общедоступный канал Slack StreamSets](https://streamsetters.slack.com/)
* [Исходный код](https://github.com/streamsets)

## <a name="prerequisites"></a>предварительным требованиям

Для установки этого приложения на новый кластер HDInsight или на имеющийся кластер требуется следующая конфигурация:

* уровни кластера: "Стандартный" или "Премиум";
* версии кластера: 3.5 или выше.

## <a name="install-the-streamsets-data-collector-published-application"></a>Установка опубликованного приложения "Сборщик данных StreamSets"

Пошаговые инструкции по установке этого и других доступных приложений независимых поставщиков программного обеспечения см. в статье [Установка сторонних приложений Hadoop в Azure HDInsight](hdinsight-apps-install-applications.md).

## <a name="launch-streamsets-data-collector"></a>Запуск сборщика данных StreamSets

1. После установки вы можете запустить StreamSets из своего кластера на портале Azure. Для этого перейдите на панель **Параметры**, а затем выберите **Приложения** в категории **Общие**. На панели "Установленные приложения" перечислены все установленные приложения.

    ![Установленное приложение StreamSets](./media/hdinsight-apps-install-streamsets/streamsets.png)

2. При выборе StreamSets отображается ссылка на веб-страницу и путь конечной точки SSH. Щелкните ссылку на веб-страницу.

3. Чтобы войти, в диалоговом окне "Вход" введите следующие учетные данные: `admin` и `admin`.

4. На странице "Начало работы" щелкните **Create New Pipeline** (Создать конвейер).

    ![Создание конвейера](./media/hdinsight-apps-install-streamsets/get-started.png)

5. В окне "Новый конвейер" введите имя конвейера (Hello World), добавьте описание и выберите **Сохранить**.

6. Отобразится консоль сборщика данных. Свойства конвейера находятся на панели свойств.
 
    ![Консоль сборщика данных](./media/hdinsight-apps-install-streamsets/pipeline-canvas.png)

7. Теперь можно приступить к [руководству по StreamSets](https://streamsets.com/documentation/datacollector/latest/help/#Tutorial/Tutorial-title.html). В этом руководстве представлены пошаговые инструкции по созданию конвейера.

## <a name="next-steps"></a>Дополнительная информация

* [Документация по сборщику данных StreamSets](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html#concept_htw_ghg_jq).
* [Установка пользовательских приложений Hadoop в Azure HDInsight](hdinsight-apps-install-custom-applications.md) — узнайте, как развернуть в HDInsight неопубликованное приложение HDInsight.
* [Публикация приложений HDInsight в Azure Marketplace](hdinsight-apps-publish-applications.md)— узнайте, как опубликовать пользовательские приложения HDInsight в Azure Marketplace.
* [Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(Установка приложения HDInsight) — узнайте, как определить приложения HDInsight.
* [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md) — узнайте, как использовать действие скрипта для установки дополнительных приложений.
* [Использование пустых граничных узлов в кластерах Hadoop в HDInsight](hdinsight-apps-use-edge-node.md) — узнайте, как использовать пустой граничный узел для доступа к кластерам HDInsight, а также тестирования и размещения приложений HDInsight.
