---
title: "Заметки о выпуске Stream Analytics | Документация Майкрософт"
description: "Заметки о выпуске Stream Analytics"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 5e59f893-cd2c-43fb-9eca-c146ce637203
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 2fe28e7b52af579038ec145c028a6d2796d6cc6e
ms.openlocfilehash: f47b719f7b1a78264e054ee15edacc32ca35aaf7
ms.lasthandoff: 02/01/2017


---
# <a name="stream-analytics-release-notes"></a>Заметки о выпуске Stream Analytics
## <a name="notes-for-02012017-release-of-stream-analytics"></a>Заметки о выпуске Stream Analytics от 01.02.2017
Этот выпуск содержит следующее обновление.

| Название | Описание |
| --- | --- |
| Введение в определяемые пользователем функции JavaScript |Теперь доступны [определяемые пользователем функции Java](stream-analytics-javascript-user-defined-functions.md) (UDF), повышающие гибкость при создании запросов. |
| Введение в инструменты для Visual Studio и Stream Analytics |Теперь доступны [инструменты для Visual Studio](stream-analytics-tools-for-visual-studio.md), используемые для отладки и расширяющие ваши возможности. |
| Обзор ведения журнала диагностики |Теперь доступно [ведение журнала диагностики](stream-analytics-job-diagnostic-logs.md), что расширяет возможности по устранению неполадок. |
| Введение в геопространственные функции |Выпущена общедоступная версия [геопространственных функций](http://msdn.microsoft.com/library/mt778980(Azure.100).aspx). |

## <a name="notes-for-04152016-release-of-stream-analytics"></a>Заметки о выпуске Stream Analytics 15.04.2016
Этот выпуск содержит следующее обновление.

| Название | Описание |
| --- | --- |
| Широкая доступность выходных данных Power BI |Сейчас [выходные данные Power BI](stream-analytics-power-bi-dashboard.md) уже являются общедоступными. 90-дневный срок действия авторизации для Power BI был удален. Дополнительные сведения о сценариях, в которых необходимо продлить авторизацию, см. в разделе [Продление авторизации](stream-analytics-power-bi-dashboard.md#renew-authorization) в статье о создании панели мониторинга Power BI. |

## <a name="notes-for-03032016-release-of-stream-analytics"></a>Заметки о выпуске Stream Analytics за 03.03.2016
Этот выпуск содержит следующее обновление.

| Название | Описание |
| --- | --- |
| Новые элементы языка запросов Stream Analytics |В MSDN добавлены следующие страницы для SAQL: [для GetType](https://msdn.microsoft.com/library/azure/mt643890.aspx "страница MSDN о GetType"), [для TRY_CAST](https://msdn.microsoft.com/library/azure/mt643735.aspx "страница MSDN о TRY_CAST") и [для REGEXMATCH](https://msdn.microsoft.com/library/azure/mt643891.aspx "страница MSDN о REGEXMATCH"). |

## <a name="notes-for-12102015-release-of-stream-analytics"></a>Заметки о выпуске Stream Analytics от 10.12.2015
Этот выпуск содержит следующее обновление.

| Название | Описание |
| --- | --- |
| Обновление версии API REST |API REST обновлен до версии от 01.10.2015. Дополнительную информацию можно найти на сайте MSDN, в разделах [Справочник по REST API для управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx) и [Интеграция машинного обучения в Stream Analytics](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md). |
| Интеграция машинного обучения Azure |В этом выпуске обеспечена поддержка определяемых пользователем функций машинного обучения Azure. Дополнительные сведения см. в [учебнике](stream-analytics-machine-learning-integration-tutorial.md), а также в [объявлении в блоге](http://blogs.technet.com/b/machinelearning/archive/2015/12/10/apply-azure-ml-as-a-function-in-azure-stream-analytics.aspx). |

## <a name="notes-for-11122015-release-of-stream-analytics"></a>Заметки о выпуске Stream Analytics от 12.11.2015
Этот выпуск содержит следующее обновление.

| Название | Описание |
| --- | --- |
| Новое поведение оператора SELECT |Оператор SELECT в службе Stream Analytics был расширен и теперь позволяет использовать * как метод доступа к свойству вложенной записи. Дополнительные сведения см. по адресу [http://msdn.microsoft.com/library/mt622759.aspx](http://msdn.microsoft.com/library/mt622759.aspx "Сложные типы данных"). |

## <a name="notes-for-10222015-release-of-stream-analytics"></a>Заметки о выпуске Stream Analytics от 22.10.15
Этот выпуск содержит следующие обновления.

| Название | Описание |
| --- | --- |
| Дополнительные возможности языка запросов |Stream Analytics расширяет язык запросов, добавляя следующие функции: [ABS](https://msdn.microsoft.com/library/azure/mt574054.aspx), [CEILING](https://msdn.microsoft.com/library/azure/mt605286.aspx), [EXP](https://msdn.microsoft.com/library/azure/mt605289.aspx), [FLOOR](https://msdn.microsoft.com/library/azure/mt605240.aspx), [POWER](https://msdn.microsoft.com/library/azure/mt605287.aspx), [SIGN](https://msdn.microsoft.com/library/azure/mt605290.aspx), [SQUARE](https://msdn.microsoft.com/library/azure/mt605288.aspx) и [SQRT](https://msdn.microsoft.com/library/azure/mt605238.aspx). |
| Сняты ограничения статистических функций |В этом выпуске сняты ограничения для 15 статистических функций запросов. Теперь нет ограничений на количество статистических выражений в запросе. |
| Добавлена функция GROUP BY System.Timestamp |Функция [GROUP BY](https://msdn.microsoft.com/library/azure/dn835023.aspx) теперь поддерживает параметры window_type и [System.Timestamp](https://msdn.microsoft.com/library/azure/mt598501.aspx). |
| Добавлена функция OFFSET для «переворачивающегося» и «прыгающего» окон |По умолчанию [переворачивающиеся](https://msdn.microsoft.com/library/azure/dn835055.aspx) и [прыгающие](https://msdn.microsoft.com/library/azure/dn835041.aspx) окна настраиваются по нулевому времени (01.01.0001 12:00:00 по Гринвичу). Новый (необязательный) параметр offsetsize позволяет задавать собственное смещение (или выравнивание). |

## <a name="notes-for-09292015-release-of-stream-analytics"></a>Заметки о выпуске Stream Analytics от 29.09.2015
Этот выпуск содержит следующие обновления.

| Название | Описание |
| --- | --- |
| Общедоступная предварительная версия Azure IoT |Служба Stream Analytics включена в общедоступную предварительную версию пакета Azure IoT. |
| Интеграция с порталом Azure |Помимо постоянного присутствия на портале управления Azure, служба Stream Analytics теперь интегрирована в [портал Azure](https://azure.microsoft.com/overview/preview-portal/). Обратите внимание, что функции Stream Analytics на портале предварительной версии в настоящий момент являются подмножеством функций портала управления Azure, в котором отсутствует возможность тестирования запросов в браузере, настройки выходных данных Power BI и перехода ко входным и выходным ресурсам в доступных вам подписках, а также создания новых. |
| Поддержка выходных данных DocumentDB |Теперь задания Stream Analytics могут отправлять выходные данные в [DocumentDB](https://azure.microsoft.com/services/documentdb/). |
| Поддержка ввода из центра IoT |Теперь задания Stream Analytics могут принимать данные из центров IoT. |
| TIMESTAMP BY для разнородных событий |Если один поток данных содержит события нескольких типов с временными метками в разных полях, то вы можете использовать команду [TIMESTAMP BY](http://msdn.microsoft.com/library/mt573293.aspx) с выражениями, чтобы указывать разные поля временных меток для каждого случая. |

## <a name="notes-for-09102015-release-of-stream-analytics"></a>Заметки о выпуске Stream Analytics 10.09.2015
Этот выпуск содержит следующие обновления.

| Название | Описание |
| --- | --- |
| Поддержка групп PowerBI |Чтобы разрешить совместное использование данных с другими пользователями Power BI, теперь задания Stream Analytics могут выполнять запись в [группы Power BI](stream-analytics-define-outputs.md#power-bi) внутри вашей учетной записи Power BI. |

## <a name="notes-for-08202015-release-of-stream-analytics"></a>Заметки о выпуске Stream Analytics от 20.08.2015
Этот выпуск содержит следующие обновления.

| Название | Описание |
| --- | --- |
| Добавлена функция LAST |Функция [LAST](http://msdn.microsoft.com/library/mt421186.aspx) теперь доступна для заданий Stream Analytics и позволяет извлекать самое последнее событие в потоке за заданный интервал времени. |
| Новые функции массивов |Теперь доступны функции массивов [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx), [GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx) и [GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx). |
| Новые функции записи |Теперь доступны функции записей [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx) и [GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx). |

## <a name="notes-for-07302015-release-of-stream-analytics"></a>Заметки о выпуске Stream Analytics от 30.07.2015
Этот выпуск содержит следующие обновления.

| Название | Описание |
| --- | --- |
| ИД организации Power BI отключен от идентификатора Azure |Эта функция позволяет получать [выходные данные Power BI](stream-analytics-power-bi-dashboard.md) для задания ASA с любого типа учетной записи Azure (Live ID или идентификатора организации). Кроме того, можно иметь один ИД организации для вашей учетной записи Azure и использовать другой для авторизации выходных данных Power BI. |
| Поддержка выходных данных очередей служебной шины |Вывод [очередей служебной шины](stream-analytics-define-outputs.md#service-bus-queues) теперь доступен в заданиях Stream Analytics. |
| Поддержка выходных данных разделов служебной шины |Вывод [разделов служебной шины](stream-analytics-define-outputs.md#service-bus-topics) теперь доступен в заданиях Stream Analytics. |

## <a name="notes-for-07092015-release-of-stream-analytics"></a>Заметки о выпуске Stream Analytics 09.07.2015
Этот выпуск содержит следующие обновления.

| Название | Описание |
| --- | --- |
| Секционирование выходных данных пользовательского большого двоичного объекта |Теперь возможно указывать частоту написания выходных данных больших двоичных объектов, а также структуру и формат структуры пути папки выходных данных для выходных данных хранилища больших двоичных объектов. |

## <a name="notes-for-05032015-release-of-stream-analytics"></a>Заметки о выпуске Stream Analytics от 03.05.2015
Этот выпуск содержит следующие обновления.

| Название | Описание |
| --- | --- |
| Увеличено максимальное значение неупорядоченного диапазона отклонений |Максимальный размер неупорядоченного диапазона отклонений теперь составляет 59:59 (MM:СС) |
| Формат выходных данных JSON: разделенные строки или массив |При выводе в большие двоичные объекты или концентратор событий теперь существует возможность получать в результате либо массив объектов JSON, либо разделенные новыми строками объекты JSON. |

## <a name="notes-for-04162015-release-of-stream-analytics"></a>Заметки о выпуске Stream Analytics 16.04.2015
| Название | Описание |
| --- | --- |
| Задержка в конфигурации учетной записи хранилища Azure |При первом запуске задания в Stream Analytics вам будет предложено создать новую учетную запись хранения или ввести данные уже существующей учетной записи для мониторинга заданий в Stream Analytics в соответствующем регионе. Из-за задержек в мониторинге конфигурации при создании еще одного задания в Stream Analytics в том же регионе в пределах 30 минут с момента создания первого отобразится запрос на ввод данных другой учетной записи хранения, а уже настроенная запись хранения не будет отображаться в раскрывающемся меню мониторинга учетных записей хранения. Чтобы не создавать ненужные учетные записи хранения, подождите 30 минут после создания предыдущего задания в том или ином регионе и только после этого запускайте новое задание в том же регионе. |
| Обновление задания |В настоящее время Stream Analytics не поддерживает изменение определений и конфигураций текущего задания в реальном времени. Для изменения ввода, вывода, запроса, масштаба или конфигурации текущего задания следует выполнение этого задания остановить. |
| Типы данных, выводимые из источника входных данных |Если инструкция CREATE TABLE не используется, тип входных данных определяется по входному формату: например, значения всех полей из CSV-файла будут строковыми. Чтобы избежать ошибки несоответствия типов, поля должны быть явно преобразованы в правильный тип с помощью функции CAST. |
| Отсутствующие поля выводятся как значения NULL |Если поле, на которое ссылается запрос, отсутствует в источнике входных данных, в выходном событии появятся значения NULL. |
| Инструкция WITH должна предшествовать инструкции SELECT |В запросе инструкции SELECT должны следовать за вложенными запросами, определенными с помощью операторов WITH. |
| Проблема нехватки памяти |Задание Streaming Analytics с большим допуском для событий отключений и/или сложных запросов с большим числом состояний может привести к нехватке памяти для этого задания, из-за чего оно перезапустится. Операции запуска и остановки будут записаны в операционном журнале задания. Чтобы избежать такого варианта, следует масштабировать запрос по нескольким разделам. В новой версии данное ограничение будет снято за счет снижения производительности при выполнении задания, а не путем его перезапуска. |
| Большие входные данные большого двоичного объекта без отметки времени для полезных данных могут вызвать проблемы нехватки памяти |Использование больших файлов из хранилища больших двоичных объектов может приводить к сбою задания Stream Analytics, если поле timestamp не задано с помощью инструкции TIMESTAMP BY. Чтобы избежать этой проблемы, не превышайте предельный размер большого двоичного объекта в 10 МБ. |
| Ограничение по объему событий базы данных SQL |При использовании базы данных SQL в качестве места назначения выходных данных большой объем выходных данных может привести к тому, что задание Stream Analytics превысит время ожидания. Для решения этой проблемы следует либо снизить объем выходных данных с помощью статистических выражений или операторов фильтрации, либо выбрать хранилище больших двоичных объектов или концентратор событий в качестве целевого объекта выходных данных. |
| Наборы данных PowerBI могут содержать только одну таблицу |PowerBI не поддерживает несколько таблиц в заданном наборе данных. |

## <a name="get-help"></a>Получение справки
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Дальнейшие действия
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

