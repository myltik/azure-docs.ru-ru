<properties 
   pageTitle="Руководство по устранению неполадок Azure Mobile Engagement — SDK" 
   description="Устранение неполадок интеграции пакета в Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="06/18/2015"
   ms.author="piyushjo"/>

# Руководство по поиску и устранению проблем с интеграцией при использовании SDK

Ниже представлены проблемы, которые могут возникнуть в связи со способом интеграции Azure Mobile Engagement в ваше приложение.

## Проблемы SDK обнаруживаются, когда в другой области приложения происходит сбой

### Проблема
- Сбой сбора данных в пользовательском интерфейсе (в разделах аналитики, мониторинга, сегментации или панели мониторинга).
- Сбои push-уведомлений (в приложении, вне приложения или и там и там).
- Сбои дополнительных функций (не работает отслеживание, определение географического расположения, или не передаются push-уведомления на определенной платформе).
- Сбои API (часто сбои API происходят незаметно и без сообщений об ошибке).
- Сбои службы (ни один раздел Azure Mobile Engagement не работает в приложении).

### Причины

- Большинство проблем, которые должны быть разрешены с помощью пакета SDK для Azure Mobile Engagement, будут обнаружены по сбою в приложении (например, по сбою сбора данных в пользовательском интерфейсе, push-уведомления, дополнительных функций, API, аварийному завершению работы приложения или обнаруженному простою в работе службы).  
- Если определенная функция Azure Mobile Engagement никогда прежде не работала в вашем приложении, необходимо завершить интеграцию. 
- Если определенная функция Azure Mobile Engagement работала, но больше не работает, возможно, необходимо обновить службу до последней версии с помощью пакета SDK для Azure Mobile Engagement. Помните, что для каждой платформы, которую поддерживает Azure Mobile Engagement, существует определенная версия пакета SDK для Azure Mobile Engagement (Android, iOS, Windows и Windows Phone).

#### Интеграция пакета SDK

- Azure Mobile Engagement неправильно интегрирована в пакет SDK (раздел аналитики).
- Функция рекламных кампаний неправильно интегрирована в пакет SDK (push-уведомления в приложениях и вне приложений).
- Истек срок действия сертификата, или используется неверная версия PROD или DEV (только iOS).
- Служба GCM или ADM неправильно интегрирована в пакет SDK (только в Android в отношении push-уведомлений конкретных служб).
- Функция отслеживания неправильно интегрирована в пакет SDK (отслеживания установки из магазина).
- Адаптирующееся расположение или GPS-расположение неправильно интегрировано в пакет SDK (отслеживание по GPS-расположению).


**См. также:**

- [Документация по пакету SDK – Руководства по интеграции][Link 5] 
- [Руководство по устранению неполадок – Push-уведомления][Link 23]

#### Обновление пакета SDK

- Необходимо обновить пакет SDK, чтобы устранить проблемы с предыдущими версиями пакета SDK (часто эти проблемы связаны с тем, что на устройстве установлена более поздняя версия ОС).
- Удалите с устройства все предыдущие версии приложения и переустановите приложение, используя его новую версию, снова зарегистрируйте идентификатор своего устройства с помощью пользовательского интерфейса Azure Mobile Engagement, чтобы подтвердить, что ваше устройство использует последнюю версию приложения.

**См. также:**

- [Документация к пакету SDK – примечания к выпуску](http://go.microsoft.com/fwlink/?LinkId= 525554) 
- [Документация по пакету SDK - руководства по обновлению](http://go.microsoft.com/fwlink/?LinkId= 525554)

#### Другие SDK

- Ошибки в файле манифеста приложения AndroidManifest.xml могут привести к сбою работы Azure Mobile Engagement (только в Android).
- Еще одна распространенная проблема с интеграцией пакета SDK и использованием API – неправильное использование ключа пакета SDK и ключа API.

**См. также:**

- [Основные понятия — глоссарий][Link 6]

## Более сложные проблемы кодирования

### Проблема
-  Если код конкретной платформы не связан с Azure Mobile Engagement напрямую, из-за этого могут возникнуть проблемы в iOS, Android и Windows Phone.

### Причины

- Многие более сложные проблемы кодирования в Azure Mobile Engagement возникают потому, что в определенной платформе есть неправильно написанный код, который не связан с Azure Mobile Engagement напрямую. В таком случае необходимо просмотреть документацию к платформе, для которой вы выполняете разработку, а также документацию к Azure Mobile Engagement (для Android, iOS, Web, Windows и Windows Phone).
- В результате неправильной настройки категорий ссылки в уведомлениях на другие расположения в приложении или вне приложения не работают (только в Android). 
- Если для параметра UIKit.framework не задать значение optional в коде iOS, отобразится ошибка "Символ не найден" или произойдет сбой работы устройств с iOS предыдущей версии (только в iOS).
- Если срок действия сертификата истек или вы неправильно используете версии сертификата — рабочую или разработки, возникают проблемы с push-уведомлениями (только в iOS).
- Есть некоторые ограничения, которые зависят от платформы и которыми нельзя управлять в Azure Mobile Engagement (например, касающиеся работы System Center при передаче push-уведомлений вне приложений в Android и iOS).
- Azure Mobile Engagement публикует полный список внутренних пакетов, которые она использует, для iOS и Android в качестве справочной информации. Учтите, что для некоторых функций Azure Mobile Engagement характерны особенности, зависящие от конкретной платформы (Android, iOS, Web, Windows и Windows Phone).

### См. также

 - [Руководство по устранению неполадок – Push-уведомления][Link 23] 
 - [Документация к пакету SDK – примечания к выпуску][Link 5]
 - [Документация к пакету SDK – руководства по обновлению][Link 5]

## Сбои приложений

### Проблема
- На устройствах конечных пользователей происходит сбой приложения.

### Причины

- Информацию о сбое можно просмотреть в *пользовательском интерфейсе раздела аналитики* или в *Analytics API*.
- Вы можете найти идентификатор устройства для своего тестового устройства и выполнить то действие, которое вызвало сбой приложения конечного пользователя, чтобы определить его причину.
- Известные проблемы с пакетом SDK для Azure Mobile Engagement, которые вызывают сбой приложений, иногда можно устранить путем обновления до последней версии пакета SDK. В случае сбоя просмотрите заметки о выпуске для своей платформы.

### См. также

- [Документация к пакету SDK – примечания к выпуску][Link 5]
- [Документация к пакету SDK – руководства по обновлению][Link 5]

## Сбои передачи в магазин приложений

### Проблема
- Ошибки, связанные с загрузкой последней версии приложения в магазин Apple, Google или Windows App.

### Причины

- Иногда магазины приложений блокируют приложения с некоторыми функциями (например, магазин Apple запрещает использование IDFV в приложениях магазина, а магазин GooglePlay запрещает совместное использование информации о приложениях между приложениями). 
- Если у вас возникли трудности с загрузкой приложения в магазин, ознакомьтесь с заметками о выпуске для своей платформы и проверьте текущую версию пакета SDK.

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 

<!---HONumber=July15_HO3-->