<properties 
	pageTitle="Приступая к работе с мобильной аналитикой | Центр мобильных разработок" 
	description="Начало работы с мобильной аналитикой." 
	documentationCenter="ios" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="mahender"/>

# Приступая к работе с мобильной аналитикой (Capptain)

<div class="dev-center-tutorial-selector sublanding">
<a href="/documentation/articles/mobile-services-ios-get-started-mobile-analytics" title="iOS" class="current">iOS</a>
</div>

В этом учебнике описывается добавление возможностей мобильной аналитики в приложение с помощью [Capptain]. С помощью мобильной аналитики можно определить, как пользователи взаимодействуют с приложением и какие разделы используются чаще всего. Чтобы приступить к сбору этих данных, необходимо добавить в приложение возможность работы с пакетом SDK Capptain.


>[AZURE.NOTE]Принадлежащий Майкрософт сайт Capptain.com предоставляет заказчикам мобильных служб Azure уровня Standard бесплатные возможности аналитики мобильных приложений, охватывающие до 100 000 активных пользователей в месяц. Чтобы воспользоваться этим предложением, свяжитесь с нами по адресу mobileservices@microsoft.com для получения дальнейших инструкций. В этом учебнике описываются функции и возможности Capptain.com и приводятся инструкции по их использованию.


В этом учебнике рассматриваются следующие основные действия:

1. [Запуск пакета SDK Capptain]
2. [Перегрузка UIViewController]

Для работы с данным учебником требуется следующее:

* Учетная запись [Capptain]
* Приложение типа [Мобильные службы уровня Standard]

## <a name="initialize"></a>Запуск пакета SDK Capptain

1. Откройте страницу **Сведения о приложении** вашего приложения, зарегистрированного в Capptain. Перейдите на вкладку SDK и загрузите пакет.

2. В XCode добавьте пакет SDK Capptain в ваш проект. Для этого щелкните правой кнопкой мыши на проекте и выберите "Добавить файлы в...". Выберите папку CapptainSDK.

3. Выберите проект. На вкладке **Этапы построения** выберите **Связать двоичные файлы с библиотеками** и добавьте следующие среды:
    * AdSupport.framework — укажите связь как необязательную (Optional)
    * SystemConfiguration.framework
    * CoreTelephony.framework
    * CFNetwork.framework
    * CoreLocation.framework
    * libxml2.dylib

    >[AZURE.NOTE]Среду AdSupport можно удалить. В Capptain она используется для сбора IDFA. Однако сбор IDFA можно отключить для соблюдения политики Apple в отношении данного ИД.

4. В файле реализации делегирования приложения импортируйте агент Capptain.


        #import "CapptainAgent.h"


5. В `applicationDidFinishLaunching:` или `application:didFinishLaunchingWithOptions:` запустите агент Capptain Agent, передав `registerapp:identifiedby:` с идентификатором вашего приложения и ключом SDK.

         - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
          [...]
          [CapptainAgent registerApp:@"YOUR_APPID" identifiedBy:@"YOUR_SDK_KEY"];
          [...]
        }

## <a name="instrument"></a>Перегрузка UIViewController

1. Найдите все дочерние элементы `UIViewController` в вашем проекте и убедитесь, что каждый из них наследуется от `CapptainViewController`.

        #import <UIKit/UIKit.h>
        #import "CapptainViewController.h"

        // formerly @interface Tab1ViewController : UIViewController<UITextFieldDelegate>
        @interface Tab1ViewController : CapptainViewController<UITextFieldDelegate> {
          UITextField* myTextField1;
          UITextField* myTextField2;
        }

        @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
        @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

2. Найдите все дочерние элементы `UITableViewController` в вашем проекте и убедитесь, что каждый из них наследуется от `CapptainTableViewController`.

    Теперь ваше приложение настроено для отправки данных аналитики в Capptain.

## Дальнейшие действия
Дополнительные сведения о возможностях Capptain для вашего приложения см. на веб-сайте [http://www.capptain.com](http://www.capptain.com)

<!-- Anchors. -->
[Запуск пакета SDK Capptain]: #initialize
[Перегрузка UIViewController]: #instrument


<!-- URLs. -->
[Capptain]: http://www.capptain.com
[Мобильные службы уровня Standard]: /pricing/details/mobile-services/
 

<!---HONumber=July15_HO3-->