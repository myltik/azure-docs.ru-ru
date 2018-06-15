---
title: Общие сведения о входных данных Azure Stream Analytics
description: В этой статье описываются концепции входных данных в задании Azure Stream Analytics, сравнение потоковых и справочных входных данных.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/25/2018
ms.openlocfilehash: 926821e2ba9912ae0140f11c9fe9a2d504609a1e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32186068"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Общие сведения о входных данных Azure Stream Analytics

Задания Azure Stream Analytics используют несколько видов входных данных. Все входные данные определяют подключение к имеющемуся источнику данных. Stream Analytics принимает входящие данные от нескольких типов источников событий, включая концентраторы событий, Центр Интернета вещей и хранилище BLOB-объектов. Входные данные подаются в имени потокового SQL-запроса, которое записывается для каждого задания. В запросе вы можете объединить несколько входных данных для смешивания данных или сравнения потоковых данных с помощью уточняющего запроса ссылочных данных и передачи результатов в выходные данные. 

Stream Analytics полностью интегрируется с тремя типами ресурсов, используемых в качестве входных данных.
- [Концентраторы событий Azure](https://azure.microsoft.com/services/event-hubs/)
- [Центр Интернета вещей Azure](https://azure.microsoft.com/services/iot-hub/) 
- [хранилище BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/) 

Эти ресурсы входных данных могут существовать в той же подписке Azure, что и задание Stream Analytics, или быть из другой подписки.

Для создания, изменения и проверки входных данных задания Stream Analytics можно использовать [портал Azure](stream-analytics-quick-create-portal.md#configure-input-to-the-job), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/New-AzureRmStreamAnalyticsInput), [.Net API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input) и [Visual Studio](stream-analytics-tools-for-visual-studio.md).

## <a name="stream-and-reference-inputs"></a>Потоковые и справочные входные данные
Данные, отправляемые в источник данных, принимаются заданием Stream Analytics и обрабатываются в режиме реального времени. Входные данные делятся на два типа: входные потоковые данные и входные справочные данные.

### <a name="data-stream-input"></a>Входные потоковые данные
Поток данных — это несвязанная последовательность событий в динамике по времени. Задания Stream Analytics должны включать хотя бы один входной поток данных. В качестве источников входных потоковых данных могут выступать хранилища больших двоичных объектов, концентраторы событий и центры Интернета вещей. Концентраторы событий используются для сбора потоков событий из нескольких устройств и служб. Это могут быть ленты новостей социальных сетей, сведения о торговле акциями или данные датчиков. Центры Интернета вещей оптимизированы для сбора данных с подключенных устройств в сценариях Интернета вещей.  Хранилище больших двоичных объектов может использоваться в качестве источника входных данных при сборе массовых данных в виде потока, например файлов журналов.  

Дополнительные сведения о входных потоковых данных см. в статье [Подключение данных: узнайте о потоках входных данных из событий в Stream Analytics](stream-analytics-define-inputs.md).

### <a name="reference-data-input"></a>Входные справочные данные
Stream Analytics также поддерживает входные данные, называемые *ссылочными данными*. Справочные данные являются полностью статическими и изменяются крайне редко. Они обычно используются для осуществления корреляции и поисков. Например, можно соединить входные потоковые данные со ссылочными данными так же, как вы бы выполнили соединение SQL для поиска статических значений. Хранилище больших двоичных объектов Azure — единственный поддерживаемый источник входных эталонных данных. Максимальный размер больших двоичных объектов в источнике ссылочных данных — 100 МБ.

Дополнительные сведения о входных справочных данных см. в статье [Использование эталонных данных для уточняющих запросов в Stream Analytics](stream-analytics-use-reference-data.md).

Эта статья представляет собой шаг [схемы обучения Stream Analytics](/documentation/learning-paths/stream-analytics/).

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Краткое руководство по созданию задания Stream Analytics с помощью портала Azure](stream-analytics-quick-create-portal.md)