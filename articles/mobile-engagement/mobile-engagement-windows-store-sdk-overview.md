<properties 
	pageTitle="Общие сведения о пакете SDK универсальных приложений для Windows" 
	description="Общие сведения о пакете SDK универсальных приложений для Windows для Azure Mobile Engagement" 									
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="piyushjo" />

#Общие сведения о пакете SDK для универсальных приложений для Windows для Azure Mobile Engagement

Начните с этой статьи, чтобы получить подробную информацию об интеграции Azure Mobile Engagement в универсальное приложение для Windows. Если вы хотите потренироваться, обязательно пройдите наш [15-минутный учебник](mobile-engagement-windows-store-dotnet-get-started.md).

Щелкните, чтобы просмотреть [содержимое пакета SDK](mobile-engagement-windows-store-sdk-content.md).

##Процедуры по интеграции

1. Начните с этого раздела: [Как интегрировать Mobile Engagement в универсальное приложение для Windows](mobile-engagement-windows-store-integrate-engagement.md)

2. Сведения об уведомлениях: [Как интегрировать Reach (Notifications) в универсальное приложение для Windows](mobile-engagement-windows-store-integrate-engagement-reach.md)

3. Реализация плана добавления тегов: [Как использовать API для расширенного добавления тегов Mobile Engagement в универсальном приложении для Windows](mobile-engagement-windows-store-use-engagement-api.md)

##Заметки о выпуске

###3\.3.1 (18.02.2016)

-   Устранение конфликтов между HTML-содержимым веб-объявления и HTML-страницей пакета SDK.
-   Улучшение стабильности.

Информацию о предыдущих версиях см. в [полных заметках о выпуске](mobile-engagement-windows-store-release-notes.md).

##Процедуры обновления

Если вы уже интегрировали в приложение старую версию службы Engagement, при обновлении пакета SDK необходимо учитывать следующее.

Если вы пропустили несколько версий пакета SDK, вам понадобиться выполнить ряд процедур. См. полную версию статьи [Процедуры обновления](mobile-engagement-windows-store-upgrade-procedure.md). Например, при миграции с версии 0.10.1 в версию 0.11.0 необходимо сначала выполнить процедуру миграции «с 0.9.0 в 0.10.1», а затем процедуру миграции «с 0.10.1 в 0.11.0».

###С 3.2.0 в 3.3.0

#### Ресурсы
Этот шаг относится только к настраиваемым ресурсам. Если вы настроили ресурсы, предоставляемые в SDK (HTML-код, изображения, наложения), необходимо создать их резервную копию перед обновлением ресурсов и повторным применением к ним настроек.

### Обновление предыдущих версий

См. статью [Процедуры обновления](mobile-engagement-windows-store-upgrade-procedure/).

<!---HONumber=AcomDC_0302_2016-->