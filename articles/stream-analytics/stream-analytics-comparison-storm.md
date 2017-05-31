---
title: "Платформы аналитики: сравнение Apache Storm и Stream Analytics | Документация Майкрософт"
description: "Рекомендации по выбору облачной платформы аналитики на примере сравнения Apache Storm и Stream Analytics. Возможности и отличия."
keywords: "платформа аналитики, платформы аналитики, облачная платформа аналитики, сравнение storm"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: b9aac017-9866-4d0a-b98f-6f03881e9339
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/24/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5b5aa75756d2a7061d531d50a59a2c52dfb75cbe
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="help-choosing-a-streaming-analytics-platform-apache-storm-comparison-to-azure-stream-analytics"></a>Помощь в выборе платформы потоковой аналитики: сравнение Apache Storm и Azure Stream Analytics
Рекомендации по выбору облачной платформы аналитики на примере сравнения Apache Storm и Azure Stream Analytics Узнайте об основных преимуществах Stream Analytics по сравнению с Apache Storm, управляемой службой в Azure HDInsight, и выберите решение, максимально отвечающее вашим задачам.

Несмотря на то, что обе платформы аналитики предоставляют возможности решения типа PaaS, между ними существует несколько фундаментальных отличий. Возможности и ограничения этих служб перечислены ниже, чтобы вы могли выбрать решение, необходимое для достижения ваших целей.

## <a name="storm-comparison-to-stream-analytics-general-features"></a>Сравнение Storm и Stream Analytics: общие характеристики

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
Нет, служба Azure Stream Analytics принадлежит исключительно корпорации Майкрософт.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Да, служба Apache Storm является лицензированной технологией Apache.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Поддерживается корпорацией Майкрософт</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Да </p>
            </td>
            <td width="246" valign="top">
                <p>
Да </p>
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
Требования к оборудованию отсутствуют. Azure Stream Analytics является службой Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Требования к оборудованию отсутствуют. Apache Storm является службой Azure.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Единица верхнего уровня</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
При помощи Azure Stream Analytics клиенты могут развертывать и отслеживать задания потоковой передачи.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
С помощью Apache Storm в HDInsight клиенты могут развертывать и отслеживать целый кластер, на котором могут размещаться многочисленные задания Storm, а также другие рабочие нагрузки (включая пакетную службу).
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Цена</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Stream Analytics оценивается согласно объему обработанных данных и требуемому количеству единиц потоковой передачи (на каждый час выполнения задания).
                </p>
                <p>
                    <a href="http://azure.microsoft.com/pricing/details/stream-analytics/">Дополнительные сведения о ценах можно получить здесь.</a>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Для Apache Storm в HDInsight вы платите за продолжительность работы кластера, независимо от развернутых заданий.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/pricing/details/hdinsight/">Дополнительные сведения о ценах можно получить здесь.</a>
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="authoring-on-each-analytics-platform"></a>Работа в каждой платформе аналитики ##

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
Да, доступна легкая в использовании поддержка языка SQL
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Нет, пользователь должен написать код на языке Java C# или использовать API Trident.
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
Пользователь сам должен выполнить реализацию временных операторов.
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
Интерактивная разработка и отладка демонстрационных данных при помощи портала Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Для пользователей .NET разработка, отладка и отслеживание обеспечиваются инструментами Visual Studio, в то время как для Java и других языков разработчики могут использовать IDE по своему выбору.
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
Stream Analytics предлагает базовые состояния заданий и операционные журналы для отладки, но в настоящее время не обеспечивает гибкость содержания журналов и его количества,например, режим подробного протоколирования.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Для отладки доступны подробные журналы. Пользователь может вызывать журналы двумя способами: через Visual Studio или с помощью подключения к кластеру по протоколу удаленного рабочего стола.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Поддержка пользовательских функций</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
В настоящее время UDF не поддерживается.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
UDF можно написать на C#, Java или другом языке по вашему выбору.
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
Расширяемый код не поддерживается в Stream Analytics.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Да, есть возможность написать пользовательский код на C#, Java или другом поддерживаемом в Storm языке.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="data-sources-and-outputs"></a>Источники и выводы данных ##

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
                <p>Поддерживаемые источники входных данных: концентраторы событий Azure и большие двоичные объекты Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Для концентраторов событий, служебных шин, Kafka и т. д. доступны соединители. Неподдерживаемые соединители могут быть реализованы при помощи пользовательского кода.
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
Поддерживаемые форматы входных данных: Avro, JSON, CSV.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Все форматы могут быть реализованы через пользовательский код.
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
Задание потоковой передачи может направлять результаты обработки в различные среды. Поддерживаемые источники выходных данных: концентраторы событий Azure, хранилище больших двоичных объектов Azure, таблицы Azure, базы данных SQL Azure  и PowerBI.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Поддержка нескольких выходных данных в топологии. Все данные могут иметь настраиваемую логику для последующей обработки. По умолчанию Storm включает соединители для PowerBI, концентраторов событий Azure, хранилищ больших двоичных объектов Azure, Azure Cosmos DB, SQL и HBase. Неподдерживаемые соединители могут быть реализованы при помощи пользовательского кода.
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
Stream Analytics требует использования формата данных UTF-8.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Все форматы кодирования данных могут быть реализованы через пользовательский код.
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
                <p>
                    - <strong>Портал Azure</strong>
                </p>
                <p>
                    - <strong>Visual Studio</strong>
                </p>
                <p>
                    - <strong>PowerShell</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Развертывание реализуется через портал Azure, PowerShell и REST API.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Развертывание реализуется через портал Azure, PowerShell, Visual Studio и REST API.
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
Мониторинг реализуется через портал Azure и REST API.
                </p>
                <p>
Пользователь также может настроить оповещения Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Мониторинг реализуется через пользовательский интерфейс Storm и REST API.
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
Число единиц потоковой передачи для каждого задания. Каждая единица потоковой передачи обрабатывает до 1 МБ/с По умолчанию максимальное число единиц – 50. Увеличение лимита по звонку.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Количество узлов в кластере HDI Storm. Нет ограничений на количество узлов (верхний предел определяется вашей квотой Azure). Увеличение лимита по звонку.
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
Пользователи могут масштабировать число единиц потоковой передачи для увеличения скорости обработки данных или оптимизации затрат.
                </p>
                <p>
Возможность увеличения до 1 Гбит/с </p>
            </td>
            <td width="246" valign="top">
                <p>
Пользователь может масштабировать размер кластера в соответствии с потребностями.
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
                    <strong>Непрерывность бизнес-процессов благодаря службам высокой доступности с гарантированным соглашением об уровне обслуживания</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
SLA с временем бесперебойной работы на уровне 99,9% </p>
                <p>
Автоматическое восстановление после сбоев </p>
                <p>
Встроенная функция восстановления временных операторов с отслеживанием состояния.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
SLA с временем бесперебойной работы на уровне 99,9% кластера Storm. Apache Storm является отказоустойчивой платформой потоковой передачи, однако пользователь должен самостоятельно следить за непрерывностью выполнения своих заданий потоковой передачи.
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
                    <strong>Позднее прибытие и обработка событий не по порядку</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Встроенные настраиваемые политики для изменения порядка и  удаления событий или настройки времени продолжительности события.
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
Ссылочные данные доступны из больших двоичных объектов Azure с максимальным размером кэша просмотра в памяти – 100 МБ. Обновление ссылочных данных управляется службой.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Нет ограничений на размер данных. Соединители для HBase, Azure Cosmos DB, SQL Server и Azure. Неподдерживаемые соединители могут быть реализованы при помощи пользовательского кода.
                </p>
                <p>
Обновление ссылочных данных должно реализоваться через пользовательский код.
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
Путем настройки опубликованных моделей машинного обучения Azure в виде функций во время создания задания ASA <a href="http://blogs.msdn.com/b/streamanalytics/archive/2015/05/24/real-time-scoring-of-streaming-data-using-machine-learning-models.aspx">(закрытая предварительная версия)</a>.
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

