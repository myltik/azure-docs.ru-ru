<properties
	pageTitle="Обзор набора Microsoft Azure IoT Suite | Microsoft Azure"
	description="Содержит обзор набора Azure IoT Suite, включая упаковку и предварительно настроенные решения."
	services=""
    suite="iot-suite"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="11/27/2015"
     ms.author="dobett"/>

# Общие сведения о наборе Azure IoT Suite

Службы Azure IoT предоставляют широкий спектр возможностей. Это службы корпоративного уровня, которые обеспечивают:

- сбор данных с устройств;
- анализ движения потоков данных;
- хранение больших наборов данных и создание запросов к ним;
- визуализацию данных, получаемых в реальном времени, и архивных данных;
- интеграцию с системами операционных отделов организации.

В Azure IoT Suite часто используемые службы Azure сочетаются с пользовательскими расширениями, образуя предварительно настроенные решения. Эти предварительно настроенные решения являются базовой реализацией стандартных шаблонов решений IoT, позволяющих сократить время разработки решений IoT. С помощью [пакетов средств разработки программного обеспечения IoT][lnk-sdks] вы можете легко вносить изменения в такие предварительно настроенные решения в соответствии со своими требованиями. Также их можно использовать в качестве примера при разработке новых решений.

Следующий видеоролик содержит обзорные сведения об Azure IoT Suite.

> [AZURE.VIDEO azurecon-2015-introducing-the-microsoft-azure-iot-suite]

## Службы Azure IoT в наборе Azure IoT Suite

Основой пакета Azure IoT Suite является служба [Центр IoT Azure][lnk-iot-hub]. Эта служба предоставляет возможности обмена сообщениями между устройствами и облаком, а также выступает в качестве шлюза для облака и других ключевых служб IoT Suite.

Модуль обработки событий [Azure Stream Analytics][lnk-asa] обеспечивает оперативный анализ данных. Пакет IoT Suite использует эту службу для обработки входящих данных телеметрии, агрегирования и обнаружения событий. Предварительно настроенные решения также используют Stream Analytics для обработки информационных сообщений, содержащих, например, метаданные или ответы на команды от устройств.

Возможности хранения данных предоставляются [службой хранилища Azure][lnk-azure-storage] и [службой Azure DocumentDB][lnk-document-db]. Для хранения данных телеметрии и их подготовки к анализу предварительно настроенные решения используют хранилище BLOB-объектов. Для управления метаданными устройств предварительно настроенные решения используют индексированное хранилище с функцией частично структурированных данных, доступной в DocumentDB. Это позволяет решениям управлять разнородными устройствами с различными требованиями к хранению содержимого.

Возможности визуализации данных предоставляются [веб-приложениями Azure][lnk-web-apps] и [Microsoft Power BI][lnk-power-bi]. Гибкость Power BI позволит вам быстро создавать собственные интерактивные панели мониторинга на основе данных из пакета IoT Suite.

Обзор архитектуры стандартного решения IoT см. в статье [Azure и IoT][iot-suite-what-is-azure-iot].

## Предварительно настроенные решения

Пакет IoT Suite содержит предварительно настроенные решения, которые позволяют быстро приступить к работе и изучить наиболее распространенные сценарии IoT. Вы можете развернуть предварительно настроенные решения в своей подписке Azure, а затем запустить готовое к использованию комплексное решение IoT.

## Дальнейшие действия

Дополнительные сведения о предварительно настроенных решениях из пакета IoT Suite см. в статье [Что такое предварительно настроенные решения IoT Azure?][lnk-what-are-preconfig]

Сведения о начале работы с одним из предварительно настроенных решений см. в руководстве [Начало работы с решениями IoT с предварительно заданными параметрами][lnk-preconfig-start].

Дополнительные сведения о службе центра IoT Azure см. в [документации по центру IoT][lnk-iot-hub].


[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
[lnk-preconfig-start]: iot-suite-getstarted-preconfigured-solutions/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/

<!---HONumber=AcomDC_0128_2016-->