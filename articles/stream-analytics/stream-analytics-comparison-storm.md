---
title: 'Платформы аналитики: сравнение Apache Storm и Stream Analytics | Документация Майкрософт'
description: Рекомендации по выбору облачной платформы аналитики на примере сравнения Apache Storm и Stream Analytics. Возможности и отличия.
keywords: платформа аналитики, платформы аналитики, облачная платформа аналитики, сравнение storm
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: b9aac017-9866-4d0a-b98f-6f03881e9339
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/27/2017
ms.author: jeanb
ms.openlocfilehash: 48599c362c5db50ebec3acd0aaa4359439edb00b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="choosing-a-streaming-analytics-platform-comparing-apache-storm-and-azure-stream-analytics"></a>Выбор платформы потоковой аналитики: сравнение Apache Storm и Azure Stream Analytics
Azure предоставляет несколько решений для анализа потоковой передачи данных: [Azure Streaming Analytics](https://docs.microsoft.com/azure/stream-analytics/) и [Apache Storm на Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-storm/). Обе эти платформы аналитики предоставляют преимущества решения PaaS. Но платформы имеют ряд существенных отличий в своих возможностях, а также в настройке и управлении. 

Эта статья содержит сравнение функций, которое поможет вам выбрать между Apache Storm и Azure Stream Analytics в качестве облачной платформы аналитики. 

## <a name="general-features"></a>Общие компоненты

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm в HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Открытый исходный код</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Нет. Служба Azure Stream Analytics принадлежит исключительно корпорации Майкрософт.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Да. Служба Apache Storm является лицензированной технологией Apache.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Служба технической поддержки Майкрософт</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Yes </p>
            </td>
            <td width="246" valign="top">
                <p>
Yes </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Требования к оборудованию</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Отсутствует. Azure Stream Analytics является службой Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Отсутствует. Apache Storm является службой Azure.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Единицы верхнего уровня</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Пользователи развертывают и отслеживают задания потоковой передачи.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Пользователи развертывают и отслеживают весь кластер, который может содержать несколько заданий Storm, а также другие рабочие нагрузки (включая пакетную службу).
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Цены</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Оценивается согласно объему обработанных данных и требуемому количеству единиц потоковой передачи на каждый час выполнения задания. 
                </p>
                    <p>Дополнительные сведения см. на странице <a href="http://azure.microsoft.com/pricing/details/stream-analytics/">цен на Stream Analytics</a>.</p>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Вы платите за продолжительность работы кластера, независимо от развернутых заданий.
                </p>
                <p>
Дополнительные сведения см. на странице <a href="http://azure.microsoft.com/pricing/details/hdinsight/">цен на HDInsight</a>.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="authoring"></a>Разработка

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm в HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Возможности: SQL DSL</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Да. Stream Analytics предоставляет язык на основе SQL для создания преобразований.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Нет. Пользователи пишут код на Java или C# или используют API Trident.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Возможности: временные операторы</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
По умолчанию поддерживаются агрегаты данных на основе периодов времени и временные соединения.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Пользователю необходимо самому выполнить реализацию временных операторов.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Интерфейс разработки</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Пользователи могут создавать, отлаживать и отслеживать задания через портал Azure, используя пример данных на основе потока в реальном времени.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Пользователи, использующие .NET, могут разрабатывать, отлаживать и отслеживать через Visual Studio. Пользователи, использующие Java или другие языки, могут применять интегрированную среду разработки по своему выбору.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Поддержка отладки</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Отладку можно выполнить с помощью базовых журналов состояний заданий и операций. Сейчас в Stream Analytics пользователи не могут указывать содержимое или его объем для журналов (например, режим подробного протоколирования).
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Доступны подробные журналы. Пользователи могут получить доступ к журналам в Visual Studio или войти в кластер и получить доступ к журналам напрямую.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Расширяемый пользовательский код</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Частичная поддержка с помощью определяемых пользователем функций JavaScript. Дополнительные сведения см. в статье <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-javascript-user-defined-functions">Определяемые пользователем функции JavaScript в Azure Stream Analytics</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Да. Пользователи могут писать код на C#, Java или любом другом языке, поддерживаемом Storm.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="data-sources-inputs-and-outputs"></a>Источники входных и выходных данных ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm в HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                 <strong>Источники входных данных</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>Концентраторы событий Azure и хранилище BLOB-объектов Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Соединители доступны для концентраторов событий Azure, служебной шины Azure, Kafka и многих других компонентов. Пользователи могут создавать дополнительные соединители с помощью пользовательского кода.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Форматы входных данных</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Avro, JSON, CSV </p>
            </td>
            <td width="246" valign="top">
                <p>
Пользователи могут реализовать любой формат с помощью пользовательского кода.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Выходные данные</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Задание потоковой передачи может иметь несколько выходов. Поддерживаемые источники выходных данных: концентраторы событий Azure, хранилище BLOB-объектов Azure, хранилище таблиц Azure, базы данных SQL Azure и Power BI.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Storm поддерживает множество типов выходных данных в топологии, каждый из которых может иметь настраиваемую логику для последующей обработки. Storm включает соединители для Power BI, концентраторы событий Azure, хранилище BLOB-объектов Azure, Azure Cosmos DB, SQL и HBase. Пользователи могут создавать дополнительные соединители с помощью пользовательского кода.    
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Форматы кодирования данных</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Данные должны быть отформатированы с использованием UTF-8.
                </p>
            </td>   
            <td width="246" valign="top">
                <p>
Пользователи могут реализовать любой формат кодирования данных с помощью пользовательского кода.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="management-and-operations"></a>Управление и использование ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm в HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Модель развертывания задания</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Портал Azure, PowerShell, API REST
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Портал Azure, PowerShell, Visual Studio и API REST.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Мониторинг (статистика, счетчики, и т. д.)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Мониторинг реализуется c помощью портала Azure и API REST. Пользователи также могут настраивать оповещения Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Мониторинг реализуется с помощью пользовательского интерфейса Storm и API REST.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Масштабируемость</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Масштабируемость определяется числом единиц потоковой передачи для каждого задания. Каждая единица потоковой передачи обрабатывает до 1 МБ/с, максимальное число единиц — 50. Дополнительные сведения см. в статье <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-scale-jobs">Масштабирование заданий Azure Stream Analytics для повышения пропускной способности базы данных</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Масштабируемость определяется количеством узлов в кластере HDInsight Storm. Верхний предел числа узлов определяется квотой Azure пользователя.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Ограничения обработки данных</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Пользователи могут увеличить объем обрабатываемых данных и оптимизировать затраты, увеличивая или уменьшая число единиц потоковой передачи. Верхний предел — 1 ГБ/с.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Пользователи могут масштабировать размер кластера.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Остановка и возобновление</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Возможность остановки и возобновления работы с последнего места остановки.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Возможность остановки и возобновления работы с последнего места остановки в зависимости от значения предела.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Обновление услуг и платформы</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Автоматическое исправление без простоев.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Автоматическое исправление без простоев.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Непрерывность бизнес-процессов благодаря службам высокой доступности с гарантированными соглашениями об уровне обслуживания</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <ul>
                <li>SLA с временем бесперебойной работы на уровне 99,9%</li>
                <li>Автоматическое восстановление после сбоев</li>
                <li>Встроенная функция восстановления временных операторов с отслеживанием состояния.</li>
                </ul>
            </td>
            <td width="246" valign="top">
                <p>
SLA с временем бесперебойной работы на уровне 99,9% кластера Storm. 
                </p>
                <p>
Apache Storm — это отказоустойчивая платформа потоковой передачи. Однако пользователь несет ответственность за обеспечение непрерывного выполнения заданий потоковой передачи.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="advanced-features"></a>Дополнительные функции ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm в HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Интервал и обработка событий не по порядку</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Встроенные настраиваемые политики могут изменять порядок событий, удалять события или настраивать время продолжительности события.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Пользователь должен реализовать логику для обработки этого сценария.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Ссылочные данные</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Ссылочные данные доступны из хранилища BLOB-объектов Azure с максимальным размером 100 МБ кэша в памяти. Ссылочные данные обновляются службой.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Нет ограничений на размер данных. Соединители доступны для HBase, Azure Cosmos DB, SQL Server и Azure. Пользователи могут создавать дополнительные соединители с помощью пользовательского кода. Ссылочные данные должны обновляться с помощью пользовательского кода.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Интеграция с машинным обучением</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Опубликованные модели Машинного обучения Azure можно настроить в качестве функций во время создания задания. Дополнительные сведения см. в статье <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-scale-with-machine-learning-functions">Масштабирование заданий Stream Analytics с помощью функций машинного обучения Azure</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Доступно с помощью компонентов «сито»(bolts) в Storm.
                </p>
            </td>
        </tr>
    </tbody>
</table>
