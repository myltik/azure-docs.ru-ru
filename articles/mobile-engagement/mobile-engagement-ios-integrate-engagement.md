<properties
	pageTitle="Службы Мобильного Взаимодействия Azure: интеграция пакета SDK для iOS | Microsoft Azure"
	description="Последние обновления и указания для пакета SDK для iOS для Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="piyushjo" />

#Интеграция службы Engagement в iOS

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Эта процедура описывает самый простой способ активации функции аналитики и мониторинга службы Engagement в приложении iOS.

Пакет SDK Engagement требует использования версии iOS 6 или выше и Xcode 8 (целевое устройство для развертывания приложения должно быть с версией не ниже iOS 6).

> [AZURE.NOTE]
Если вам крайне важно оставить версию XCode 7, то можете воспользоваться [пакетом SDK Engagement для iOS версии 3.2.4](https://aka.ms/r6oouh). В предыдущей версии выявлена ошибка в модуле обработки рекламных кампаний при выполнении на устройствах iOS 10. Дополнительные сведения см. в статье [Как интегрировать рекламные кампании Engagement в iOS](mobile-engagement-ios-integrate-engagement-reach.md). Если вы решили использовать пакет SDK версии 3.2.4, то просто пропустите импорт платформы `UserNotifications.framework`, описанный на следующем шаге.

Достаточно выполнить следующие шаги, чтобы активировать отчеты по журналам, которые необходимы для вычисления всех статистических данных, касающихся пользователей, сеансов, действий, сбоев и технической информации. Отчеты по журналам, необходимые для вычисления других статистических данных (например, касающихся событий, ошибок и заданий), требуется создавать вручную с помощью API Engagement (см. статью [Как использовать API для расширенного добавления тегов Mobile Engagement в приложении iOS](mobile-engagement-ios-use-engagement-api.md)), так как эти статистические данные зависят от приложения.

##Внедрение пакета SDK для Engagement в проект iOS

- Скачайте пакет SDK для iOS [отсюда](http://aka.ms/qk2rnj).

- Добавьте пакет SDK для Engagement в проект iOS: в Xcode щелкните правой кнопкой мыши проект, выберите элемент **Add files to...** (Добавить файлы в...) и укажите папку `EngagementSDK`.

- Служба Engagement требует дополнительные среды для работы: в обозревателе проектов откройте панель проекта и выберите правильную цель. Затем откройте вкладку **Этапы сборки** и в меню **Связать двоичные объекты с библиотеками** добавьте следующие инфраструктуры:

	-   `UserNotifications.framework`: задайте для связи значение `Optional`
	-   `AdSupport.framework`: задайте для связи значение `Optional`
	-   `SystemConfiguration.framework`
	-   `CoreTelephony.framework`
	-   `CFNetwork.framework`
	-   `CoreLocation.framework`
	-   `libxml2.dylib`

> [AZURE.NOTE] Среду AdSupport можно удалить. Службе Engagement необходима эта среда для сбора IDFA. Однако сбор IDFA можно отключить (<ios-sdk-engagement-idfa>), чтобы обеспечить соответствие новой политике Apple в отношении этого идентификатора.

##Запуск пакета SDK для Engagement

Необходимо изменить делегат приложения:

-   В верхнюю часть файла реализации импортируйте агент Engagement:

		[...]
		#import "EngagementAgent.h"

-   Инициализация Engagement в методе **applicationDidFinishLaunching:** или **application:didFinishLaunchingWithOptions:**:

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
		  [...]
		  [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
		  [...]
		}

##Упрощенные отчеты

### Рекомендуемый метод: перегрузка классов `UIViewController`

Для активации отчетов всех журналов, которые требуются службе Engagement для вычисления статистических данных пользователей, действий, сбоев и технических проблем, достаточно сделать все подклассы `UIViewController` наследуемыми из классов `EngagementViewController` (то же правило действует для `UITableViewController` -> `EngagementTableViewController`).

**Без Engagement:**

	#import <UIKit/UIKit.h>

	@interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
	  UITextField* myTextField1;
	  UITextField* myTextField2;
	}

	@property (nonatomic, retain) IBOutlet UITextField* myTextField1;
	@property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**С Engagement:**

	#import <UIKit/UIKit.h>
	#import "EngagementViewController.h"

	@interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
	  UITextField* myTextField1;
	  UITextField* myTextField2;
	}

	@property (nonatomic, retain) IBOutlet UITextField* myTextField1;
	@property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### Альтернативный метод: вызов `startActivity()` вручную

Если вам не удается перегрузить классы `UIViewController` или вы не хотите этого делать, запустите действия, вызвав методы `EngagementAgent` напрямую.

> [AZURE.IMPORTANT] Пакет SDK для iOS автоматически вызывает метод `endActivity()` при закрытии приложения. Поэтому мы *НАСТОЯТЕЛЬНО* рекомендуем вызывать метод `startActivity` при каждом изменении действия пользователя и *НИКОГДА* не вызывать метод `endActivity`, так как это приводит к завершению текущего сеанса.

##Отчеты о расположении

Условия предоставления услуг Apple не позволяет приложениям использовать отслеживание расположения только для целей статистики. Таким образом, рекомендуется включить отчеты о расположении только в том случае, если приложение также использует расположение отслеживания по другой причине.

Начиная с версии iOS 8, необходимо предоставлять описание того, как приложение использует службы расположений, задавая строку для ключа [NSLocationWhenInUseUsageDescription] или [NSLocationAlwaysUsageDescription] в файле Info.plist приложения. Если необходимо создать отчет о расположении в фоновом режиме с помощью службы Engagement, добавьте ключ NSLocationAlwaysUsageDescription. Во всех остальных случаях добавьте ключ NSLocationWhenInUseUsageDescription.

### Отчеты о расположении отложенной области

Отчеты о расположении отложенной области позволяют включать в отчеты страну, область и населенный пункт, связанные с устройствами. Этот тип отчетов о расположении использует только сетевые расположения (на основе идентификатора ячейки или Wi-Fi). Отчеты об области устройства выполняются максимум один раз за сеанс. GPS никогда не используется, и в результате этот тип отчета о расположении оказывает исключительно небольшое (если не сказать вообще не оказывает) воздействие на батарею.

Области в отчетах используются для вычисления географических статистических данных о пользователях, сеансах, событиях и ошибках. Они также могут использоваться в качестве критерия для рекламных компаний Reach. Последнюю зафиксированную в отчете область для устройства можно получить благодаря [API устройств].

Чтобы включить отчет о расположении отложенной области, добавьте следующую строку после инициализации агента Engagement:

	- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
	{
	  [...]
	  [[EngagementAgent shared] setLazyAreaLocationReport:YES];
	  [...]
	}

### Отчет о расположении в реальном времени

Отчет о расположении в реальном времени позволяет включать в отчет широту и долготу, связанные с устройствами. По умолчанию такой тип отчета о расположении использует только сетевые расположения (на основе идентификатора ячейки или Wi-Fi). Этот отчет активен только тогда, когда приложение выполняется в фоновом режиме (т.е во время сеанса).

Расположения в реальном времени *НЕ* используются для вычисления статистических данных. Единственное их назначение — дать возможность использовать критерий геозоны реального времени <Reach-Audience-geofencing> в рекламных кампаниях.

Чтобы включить отчет о расположении в реальном времени, добавьте следующую строку после инициализации агента Engagement:

	[[EngagementAgent shared] setRealtimeLocationReport:YES];

#### Отчеты на базе GPS

По умолчанию отчеты о расположении в реальном времени используют только сетевые расположения. Чтобы включить использование расположений на базе GPS (которые значительно точнее), добавьте:

	[[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### Отчет в фоновом режиме

По умолчанию отчеты о расположении в реальном времени активны только тогда, когда приложения выполняются в фоновом режиме (т.е. во время сеанса). Чтобы включить отчеты в фоновом режиме, добавьте:

	[[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [AZURE.NOTE] Когда приложение выполняется в фоновом режиме, в отчете показываются только расположения на основе сети, даже если включен GPS.

Реализация этой функции будет вызывать [startMonitoringSignificantLocationChanges] при переходе приложения в фоновый режим. Имейте в виду, что при этом автоматически выполняется повторный запуск приложения в фоновом режиме при поступлении нового события расположения.

##Расширенные отчеты

Если вы хотите получать отчеты о событиях, ошибках и заданиях, относящихся к конкретному приложению, следует использовать API Engagement при помощи методов класса `EngagementAgent`. Объект этого класса можно получить с помощью вызова статического метода `[EngagementAgent shared]`.

API службы Engagement позволяет использовать все расширенные возможности Engagement и подробно описывается в разделе «Как использовать API Engagement в iOS» (а также в технической документации по классу `EngagementAgent`).

##Отключение сбора IDFA

По умолчанию служба Engagement будет использовать [IDFA] для уникальной идентификации пользователя. Но, если рекламу не используется где-либо еще в приложении, вы можете получить отказ в результате процесса проверки Магазина приложений. Сбор IDFA можно отключить, добавив макроопределение препроцессора `ENGAGEMENT_DISABLE_IDFA` в PCH-файл (или в `Build Settings` приложения). Это обеспечит отсутствие ссылок на `ASIdentifierManager`, `advertisingIdentifier` или `isAdvertisingTrackingEnabled` в сборке приложения.

Интеграция в файл **prefix.pch**:

	#define ENGAGEMENT_DISABLE_IDFA
	...

Можно проверить выключение сбора IDFA в приложении, проверив журналы тестирования службы Engagement. См. документацию о тесте интеграции (<ios-sdk-engagement-test-idfa>) для получения дальнейшей информации.

##Выключение отчетов журналов

### Использование вызова метода

Если необходимо, чтобы служба Engagement перестала отправлять журналы, можно вызвать:

	[[EngagementAgent shared] setEnabled:NO];

Этот вызов является постоянным: он использует `NSUserDefaults` для хранения информации.

Вы можете снова включить отчеты журналов, вызвав ту же функцию со значением `YES`.

### Интеграция в пакет параметров

Вместо вызова данной функции вы также можете интегрировать данный параметр напрямую в существующий файл `Settings.bundle`. Строка `engagement_agent_enabled` должна использоваться как идентификатор параметра и быть связана с переключателем (`PSToggleSwitchSpecifier`).

В следующем примере `Settings.bundle` показана реализация:

	<dict>
	    <key>PreferenceSpecifiers</key>
	    <array>
	        <dict>
	            <key>DefaultValue</key>
	            <true/>
	            <key>Key</key>
	            <string>engagement_agent_enabled</string>
	            <key>Title</key>
	            <string>Log reporting enabled</string>
	            <key>Type</key>
	            <string>PSToggleSwitchSpecifier</string>
	        </dict>
	    </array>
	    <key>StringsTable</key>
	    <string>Root</string>
	</dict>

<!-- URLs. -->
[API устройств]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]: https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]: https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]: http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]: https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier

<!---HONumber=AcomDC_0921_2016-->