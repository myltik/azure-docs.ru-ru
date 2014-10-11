<properties linkid="manage-hdinsight-understand-nosql" urlDisplayName="Understand NoSQL technologies" pageTitle="Understanding NoSQL Technologies on Azure | Azure" metaKeywords="" description="Learn how NoSQL technologies on HDInsight can help you manage data not suited to relational databases, such as big data sets and JSON documents or graphs." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="storage,hdinsight" documentationCenter="" title="Data management: Understanding NoSQL technologies on Azure" authors="dchappell" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="dchappell"></tags>

# Управление данными: общие сведения о технологиях NoSQL в Azure

**Сводка:** Azure предлагает широкий диапазон вариантов для рабочих нагрузок NoSQL, однако выбор правильного варианта может быть непростым. В этом документе поясняются технологии NoSQL в Azure и выбор правильной службы для управления нереляционными данными, которые включают неструктурированные или особенно большие наборы данных, зачастую называемые "данные большого размера".

Реляционные технологии уже давно являются главным подходом для работы с данными. Тем не менее при работе с очень большими объемами данных различных типов масштабирование на нескольких серверах представляет собой сложную задачу. Кроме того, реляционные технологии — не лучший вариант для управления определенными видами данных, такими как документы JSON или графы. Это руководство содержит пошаговые инструкции по вариантам с разъяснениями относительно возможностей каждого из них и потенциальных причин его выбора.

**Содержание:**

-   Данные в Azure: общая картина
-   Реляционные технологии Azure: краткий обзор
-   Технологии Azure NoSQL: оперативные данные

    -   Хранилища пар "ключ-значение"
    -   Хранилища семейств столбцов
    -   Хранилища документов
    -   Базы данных графов
-   Технологии Azure NoSQL: аналитические данные

    -   Hadoop MapReduce
    -   HDInsight

**Автор:** Дэвид Чэппел (David Chappell), руководитель компании Chappell & Associates

**Опубликовано:** ноябрь 2013

**Версия:** 1.0

**Загрузить:** [Общие сведения о технологиях NoSQL в Azure (PDF-файл)][]

  [Общие сведения о технологиях NoSQL в Azure (PDF-файл)]: http://go.microsoft.com/fwlink/p/?LinkId=330292
