<properties 
	pageTitle="Интеграция пакета iOS SDK для Azure Mobile Engagement" 
	description="Последние обновления и процедуры пакета iOS SDK для Azure Mobile Engagement"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />


<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-integrate-engagement/" title="Windows Store">Windows Магазин</a><a href="/documentation/articles/mobile-engagement-windows-phone-integrate-engagement/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-integrate-engagement/" title="iOS" class="current">iOS</a><a href="/documentation/articles/mobile-engagement-android-integrate-engagement/" title="Android" >Android</a></div>


#Интеграция службы Engagement в iOS

> [AZURE.IMPORTANT] Требуется пакет SDK iOS4 и выше для Engagement: цель развертывания приложения должна быть не ниже iOS 4.

Эта процедура описывает самый простой способ активации функции аналитики и мониторинга службы Engagement в приложении iOS.

Следующих действий достаточно для активации отчета журналов, который необходим при вычислении всех статистических данных о пользователях, сеансах, действиях сбоях и технических проблемах. Отчет журналов, необходимый для вычисления других статистических данных таких как события, ошибки и задания, необходимо делать вручную с помощью API Engagement (см. ios-sdk-engagement-advanced), поскольку эта статистика зависит от приложения.

##Внедрение пакета SDK для Engagement в проект iOS

Добавьте пакет SDk для Engagement в проект iOS: в Xcode 4 щелкните правой кнопкой мыши проект, выберите **"Добавить файлы к..."** и выберите папку `EngagementSDK`.

Служба Engagement требует дополнительные среды для работы: в обозревателе проектов откройте панель проекта и выберите правильную цель. Затем откройте вкладку **"Этапы построения"** и в меню **"Связать двоичные объекты с библиотеками"** добавьте следующие среды:

> -   `AdSupport.framework` - укажите связь как `необязательную`
> -   `SystemConfiguration.framework`
> -   `CoreTelephony.framework`
> -   `CFNetwork.framework`
> -   `CoreLocation.framework`
> -   `libxml2.dylib`

> [AZURE.NOTE] Среду AdSupport можно удалить. Службе Engagement необходима эта среда для сбора IDFA. Однако сбор IDFA можно отключить \<ios-sdk-engagement-idfa\>, чтобы обеспечить соответствие с новой политикой Apple в отношении этого идентификатора.

##Инициализация пакета SDK для Engagement

Необходимо изменить делегат приложения:

-   В верхнюю часть файла реализации импортируйте агент Engagement:

			[...]
			#import "EngagementAgent.h"

-   Инициализация службы Engagement в методе '**applicationDidFinishLaunching:**' или '**application:didFinishLaunchingWithOptions:**':

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
			  [...]
			}

##Базовые отчеты

### Рекомендуется использовать метод перегрузки классов `UIViewController`

Для активации отчетов всех журналов, которые требуются службе Engagement для сравнения статистических данных пользователей, действий, сбоев и технических проблем, достаточно просто сделать все подклассы `UIViewController` наследуемыми из классов `EngagementViewController` (то же правило для `UITableViewController` -\> `EngagementTableViewController`).

**Без Engagement :**

			#import <UIKit/UIKit.h>
			
			@interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
			  UITextField* myTextField1;
			  UITextField* myTextField2;
			}
			
			@property (nonatomic, retain) IBOutlet UITextField* myTextField1;
			@property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**С Engagement :**

			#import <UIKit/UIKit.h>
			#import "EngagementViewController.h"
			
			@interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
			  UITextField* myTextField1;
			  UITextField* myTextField2;
			}
			
			@property (nonatomic, retain) IBOutlet UITextField* myTextField1;
			@property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### Альтернативный способ: вызов `startActivity()` вручную

Если невозможно или нежелательно перегружать классы `UIViewController`, вместо этого можно запускать и останавливать действия с помощью прямого вызова методов `EngagementAgent`.

> [AZURE.IMPORTANT] Пакет iOS SDK автоматически вызывает метод `endActivity()` при закрытии приложения. Таким образом, *НАСТОЯТЕЛЬНО* рекомендуется вызывать метод `startActivity` при каждом изменении действия пользователя и *НИКОГДА* не вызывать метод `endActivity`, поскольку вызов этого метода приводит к завершению текущего сеанса.

##Отчеты о расположении

Условия предоставления услуг Apple не позволяет приложениям использовать отслеживание расположения только для целей статистики. Таким образом, рекомендуется включить отчеты о расположении только в том случае, если приложение также использует расположение отслеживания по другой причине.

Начиная с версии iOS 8, необходимо предоставить описание того, как приложение использует службы расположения, задав строку для ключа [NSLocationWhenInUseUsageDescription] или [NSLocationAlwaysUsageDescription] в файле Info.plist приложения. Если необходимо создать отчет о расположении в фоновом режиме с помощью службы Engagement, добавьте ключ NSLocationAlwaysUsageDescription. Во всех остальных случаях добавьте ключ NSLocationWhenInUseUsageDescription.

### Отчеты о расположении отложенной области

Отчеты о расположении отложенной области позволяют включать в отчеты страну, область и населенный пункт, связанные с устройствами. Этот тип отчетов о расположении использует только сетевые расположения (на основе идентификатора ячейки или Wi-Fi). Отчеты об области устройства выполняются максимум один раз за сеанс. GPS никогда не используется, и в результате этот тип отчета о расположении оказывает исключительно небольшое (если не сказать вообще не оказывает) воздействие на батарею.

Области в отчетах используются для вычисления географических статистических данных о пользователях, сеансах, событиях и ошибках. Они также могут использоваться в качестве критерия для рекламных компаний Reach. Последнюю зафиксированную область в отчете для устройства можно извлечь благодаря [API устройства].

Чтобы включить отчет о расположении отложенной области, добавьте следующую строку после инициализации агента Engagement:

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [[EngagementAgent shared] setLazyAreaLocationReport:YES];
			  [...]
			}

### Отчет о расположении в реальном времени

Отчет о расположении в реальном времени позволяет включать в отчет широту и долготу, связанные с устройствами. По умолчанию такой тип отчета о расположении использует только сетевые расположения (на основе идентификатора ячейки или Wi-Fi). Этот отчет активен только тогда, когда приложение выполняется в фоновом режиме (т.е во время сеанса).

Расположения в реальном времени *НЕ* используются для вычисления статистических данных. Единственное их назначение - дать возможность использовать критерий
географического разграничения \<Reach-Audience-geofencing\> в реальном времени в рекламных кампаниях Reach.

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

При необходимости получения отчетов о событиях, ошибках и заданиях, имеющих отношение к приложению, следует использовать API службы Engagement при помощи методов класса `EngagementAgent`. Объект этого класса можно получить с помощью вызова статического метода`[EngagementAgent shared]`.

API службы Engagement позволяет использовать все расширенные возможности этой службы и подробно описывается в разделе "Использование
API службы Engagement на iOS (а также в технической документации класса `EngagementAgent`).

##Отключение сбора IDFA

По умолчанию служба Engagement будет использовать [IDFA] для уникальной идентификации пользователя. Но, если рекламу не используется где-либо еще в приложении, вы можете получить отказ в результате процесса проверки Магазина приложений. Сбор IDFA можно отключить, добавив макроопределение препроцессора `ENGAGEMENT_DISABLE_IDFA` в pch-файл (или в `параметры построения` приложения). Это обеспечит отсутствие ссылок на `ASIdentifierManager`, `advertisingIdentifier` или `isAdvertisingTrackingEnabled` в сборке приложения.

Интеграция в файл **prefix.pch**:

			#define ENGAGEMENT_DISABLE_IDFA
			...

Можно проверить выключение сбора IDFA в приложении, проверив журналы тестирования службы Engagement. См. документацию о тесте интеграции\<ios-sdk-engagement-test-idfa\> для получения дальнейшей информации.

##Выключение отчетов журналов

### Использование вызова метода

Если необходимо, чтобы служба Engagement перестала отправлять журналы, можно вызвать:

			[[EngagementAgent shared] setEnabled:NO];

Этот вызов является постоянным: он использует `NSUserDefaults` для хранения информации

Можно снова включить отчеты журналов, вызвав ту же функцию со значением `ДА`.

### Интеграция в пакет параметров

Вместо вызова данной функции можно также интегрировать данный параметр прямо в существующий файл `Settings.bundle`. Строка `engagement_agent_enabled` должна использоваться как идентификатор параметра и должна быть связана с переключателем(`PSToggleSwitchSpecifier`).

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
[API устройства]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]:http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]:https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier

<!--HONumber=47-->
