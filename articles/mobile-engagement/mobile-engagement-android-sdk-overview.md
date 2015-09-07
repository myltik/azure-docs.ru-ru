<properties 
	pageTitle="Интеграция пакета Android SDK для Azure Mobile Engagement"
	description="Последние обновления и процедуры пакета Android SDK для Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="08/10/2015"
	ms.author="piyushjo"/>


#Пакет Android SDK для Azure Mobile Engagement

Начните здесь, чтобы получить все сведения об интеграции службы Azure Mobile Engagement в приложение Android. Если вы хотите потренироваться, обязательно пройдите наш [15-минутный учебник](mobile-engagement-android-get-started.md).

Щелкните, чтобы просмотреть [содержимое пакета SDK](mobile-engagement-android-sdk-content.md).

##Процедуры по интеграции
1. Начните с этого раздела: [Интеграция службы Mobile Engagement в приложение Android](mobile-engagement-android-integrate-engagement.md)

2. Сведения об уведомлениях: [Интеграция модуля Reach (уведомления) в приложение Android](mobile-engagement-android-integrate-engagement-reach.md)
	1. Google Cloud Messaging (GCM): [Интеграция GCM с помощью службы Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
	2. Amazon Device Messaging (ADM): [Интеграция ADM с помощью службы Mobile Engagement](mobile-engagement-android-adm-integrate.md)

3. Реализация плана добавления тегов: [Использование расширенного API добавления тегов службы Mobile Engagement в приложении Android](mobile-engagement-android-use-engagement-api.md)


##Заметки о выпуске

##4\.1.0 (25.08.2015)

- Обработка новой модели разрешений для Android M.
- Возможность настройки характеристик расположения в среде выполнения, а не с помощью `AndroidManifest.xml`.
- Исправление ошибки разрешений: если используется `ACCESS_FINE_LOCATION`, `ACCESS_COARSE_LOCATION` больше не требуется.
- Улучшение стабильности.

Информацию о предыдущих версиях см. в [полных заметках о выпуске](mobile-engagement-android-release-notes.md).

##Процедуры обновления

Если вы уже интегрировали более старую версию нашего пакета SDK в свое приложение, обратитесь к разделу [Процедуры обновления](mobile-engagement-android-upgrade-procedure.md).

<!---HONumber=August15_HO9-->