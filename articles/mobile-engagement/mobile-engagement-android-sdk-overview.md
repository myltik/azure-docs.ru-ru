<properties
	pageTitle="Интеграция пакета Android SDK с Azure Mobile Engagement"
	description="Описывает, как интегрировать пакет SDK для Azure Mobile Engagement в приложения Android."
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="08/10/2016"
	ms.author="piyushjo;ricksal" />

# Интеграция пакета Android SDK с Azure Mobile Engagement

> [AZURE.SELECTOR]
- [Универсальная платформа Windows](mobile-engagement-windows-store-sdk-overview.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
- [iOS](mobile-engagement-ios-sdk-overview.md)
- [Android](mobile-engagement-android-sdk-overview.md)

В этом документе описываются все параметры интеграции и конфигурации в пакете Android SDK для Azure Mobile Engagement.

## Предварительные требования

[AZURE.INCLUDE [Предварительные требования](../../includes/mobile-engagement-android-prereqs.md)]

## Дополнительные функции

### Функции отчетов

Можно добавить такие функции:

1. [дополнительные параметры отчетов](mobile-engagement-android-advanced-reporting.md);
2. [параметры отчетов о местонахождении](mobile-engagement-android-location-reporting.md);
3. [дополнительные параметры конфигурации](mobile-engagement-android-advanced-configuration.md).

### Уведомления:
[Интеграция модуля Reach (уведомления) в приложение Android](mobile-engagement-android-integrate-engagement-reach.md)

1. Google Cloud Messaging (GCM): [Интеграция GCM с помощью службы Mobile Engagement](mobile-engagement-android-gcm-integrate.md)

2. Amazon Device Messaging (ADM): [Интеграция ADM с помощью службы Mobile Engagement](mobile-engagement-android-adm-integrate.md)

### Реализация плана добавления тегов:
[Использование расширенного API добавления тегов службы Mobile Engagement в приложении Android](mobile-engagement-android-use-engagement-api.md)

## Заметки о выпуске

### 4\.2.3 (10.08.2016)

 - Больше не блокируется сеть Wi-Fi.
 - Устранена взаимоблокировка при вызове getDeviceId перед инициализацией (ошибка появилась в версии 4.2.0).

Информацию о всех версиях см. в [полной версии заметок о выпуске](mobile-engagement-android-release-notes.md).

## Процедуры обновления

Если вы уже интегрировали более старую версию нашего пакета SDK в свое приложение, обратитесь к разделу [Процедуры обновления](mobile-engagement-android-upgrade-procedure.md).

<!---HONumber=AcomDC_0810_2016-->