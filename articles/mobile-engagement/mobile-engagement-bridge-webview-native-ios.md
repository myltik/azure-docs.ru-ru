---
title: Создание моста между iOS WebView и собственным пакетом SDK для iOS для Служб мобильного взаимодействия
description: Описывает, как создать мост между WebView с Javascript и собственным пакетом SDK iOS для Служб мобильного взаимодействия
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: e1d6ff6f-cd67-4131-96eb-c3d6318de752
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 07b2b8be80c090ce533f31cc28910f3ce7b91f73
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="bridge-ios-webview-with-native-mobile-engagement-ios-sdk"></a>Создание моста между iOS WebView и собственным пакетом SDK для iOS для Служб мобильного взаимодействия
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

> [!div class="op_single_selector"]
> * [Мост Android](mobile-engagement-bridge-webview-native-android.md)
> * [Мост iOS](mobile-engagement-bridge-webview-native-ios.md)
> 
> 

Некоторые мобильные приложения представляют собой гибридные приложения. В этом случае само приложение разрабатывается на основе собственного языка Objective-C iOS, но некоторые или даже все окна отображаются с помощью iOS WebView. Тем не менее, пакет SDK iOS для Служб мобильного взаимодействия можно использовать в таких приложениях, и в этом руководстве описывается, как это сделать. 

Сделать это можно двумя способами (оба способа не документированы):

* Первый способ, который описан по следующей [ссылке](http://stackoverflow.com/questions/9826792/how-to-invoke-objective-c-method-from-javascript-and-send-back-data-to-javascrip), включает регистрацию `UIWebViewDelegate` в вашем веб-представлении и перехват и немедленную отмену изменения расположения в JavaScript. 
* Второй способ основан на следующем [сеансе WWDC 2013](https://developer.apple.com/videos/play/wwdc2013/615). Это более "чистый" подход по сравнению с первым, и именно им мы будем пользоваться в этом руководстве. Обратите внимание, что этот подход работает только на iOS7 и более поздних версиях. 

Для моста iOS выполните следующие действия:

1. Во-первых, полностью изучите наш [Учебник "Приступая к работе"](mobile-engagement-ios-get-started.md) для интеграции пакета SDK iOS для Служб мобильного взаимодействия в свое гибридное приложение. При необходимости также можно включить тестовое ведение журнала, чтобы видеть методы пакета SDK во время из вызова из веб-представления. 
   
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
           ....
             [EngagementAgent setTestLogEnabled:YES];
           ....
        }
2. Теперь убедитесь, что в гибридном приложении есть окно с веб-представлением. Его можно добавить в `Main.storyboard` приложения. 
3. Свяжите это веб-представление с вашим контроллером **ViewController**, щелкнув и перетащив веб-представления из сцены контроллера представления в окно изменения `ViewController.h` и поместив его непосредственно под строкой `@interface`. 
4. После этого откроется диалоговое окно с запросом имени. Укажите имя **webView**. Файл `ViewController.h` должен выглядеть примерно так:
   
        #import <UIKit/UIKit.h>
        #import "EngagementViewController.h"
   
        @interface ViewController : EngagementViewController
        @property (strong, nonatomic) IBOutlet UIWebView *webView;
   
        @end
5. Мы обновим файл `ViewController.m` позже, но сначала создадим файл моста. Этот файл создает обертку для некоторых часто используемых методов пакета SDK iOS для Служб мобильного взаимодействия. Создайте новый заголовочный файл **EngagementJsExports.h**, который использует механизм `JSExport`, описанный в вышеупомянутом [сеансе](https://developer.apple.com/videos/play/wwdc2013/615), для предоставления доступа к собственным методам iOS. 
   
        #import <Foundation/Foundation.h>
        #import <JavaScriptCore/JavascriptCore.h>
   
        @protocol EngagementJsExports <JSExport>
   
        + (void) sendEngagementEvent:(NSString*) name :(NSString*)extras;
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras;
        + (void) endEngagementJob:(NSString*) name;
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras;
        + (void) sendEngagementAppInfo:(NSString*) appInfo;
   
        @end
   
        @interface EngagementJs : NSObject <EngagementJsExports>
   
        @end
6. Затем мы создадим вторую часть файла моста. Создайте файл с именем **EngagementJsExports.m**, который будет содержать реализацию, создающую сами оболочки путем вызова методов пакета SDK iOS для Служб мобильного взаимодействия. Также обратите внимание, что мы разбираем `extras`, передаваемый из кода JavaScript веб-представления, и помещаем его в объект `NSMutableDictionary`, передаваемый с помощью вызовов метода пакета SDK Engagement.  
   
        #import <UIKit/UIKit.h>
        #import "EngagementAgent.h"
        #import "EngagementJsExports.h"
   
        @implementation EngagementJs
   
        +(void) sendEngagementEvent:(NSString*)name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendEvent:name extras:extrasInput];
        }
   
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] startJob:name extras:extrasInput];
        }
   
        + (void) endEngagementJob:(NSString*) name {
           [[EngagementAgent shared] endJob:name];
        }
   
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendError:name extras:extrasInput];
        }
   
        + (void) sendEngagementAppInfo:(NSString*) appInfo {
           NSMutableDictionary* appInfoInput = [self ParseExtras:appInfo];
           [[EngagementAgent shared] sendAppInfo:appInfoInput];
        }
   
        + (NSMutableDictionary*) ParseExtras:(NSString*) input {
           NSData *data = [input dataUsingEncoding:NSUTF8StringEncoding];
           NSError* error = nil;
           NSMutableDictionary* extras = [NSJSONSerialization JSONObjectWithData:data options:0 error:&error];
   
           return extras;
        }
   
        @end
7. Теперь вернемся к файлу **ViewController.m** и добавим в него следующий код: 
   
        #import <JavaScriptCore/JavaScriptCore.h>
        #import "ViewController.h"
        #import "EngagementJsExports.h"
   
        @interface ViewController ()
   
        @end
   
        @implementation ViewController
   
        - (void)viewDidLoad
        {
           self.webView.delegate = self;
           [super viewDidLoad];
           [self loadWebView];
        }
   
        - (void)loadWebView {
           NSBundle* mainBundle = [NSBundle mainBundle];
           NSURL* htmlPage = [mainBundle URLForResource:@"LocalPage" withExtension:@"html"];
   
           NSURLRequest* urlReq = [NSURLRequest requestWithURL:htmlPage];
           [self.webView loadRequest:urlReq];
        }
   
        - (void)webViewDidFinishLoad:(UIWebView*)wv
        {
           JSContext* context = [wv valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
   
           context[@"EngagementJs"] = [EngagementJs class];
        }
   
        - (void)webView:(UIWebView*)wv didFailLoadWithError:(NSError*)error
        {
           NSLog(@"Error for WEBVIEW: %@", [error description]);
        }
   
        - (void)didReceiveMemoryWarning {
           [super didReceiveMemoryWarning];
           // Dispose of any resources that can be recreated.
        }
   
        @end
8. Обратите внимание на следующие моменты, касающиеся файла **ViewController.m** :
   
   * В методе `loadWebView` мы загружаем локальный HTML-файл **LocalPage.html** , код которого будет рассмотрен далее. 
   * В методе `webViewDidFinishLoad` мы получаем `JsContext` и связываем с ним наш класс-оболочку. Это позволит вызывать наши методы SDK оболочки с помощью дескриптора **EngagementJs** из веб-представления. 
9. Создайте файл с именем **LocalPage.html** , содержащий следующий код:
   
        <!doctype html>
        <html>
           <head>
               <style type='text/css'>
                   html { font-family:Helvetica; color:#222; }
                   h1 { color:steelblue; font-size:22px; margin-top:16px; }
                   h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
               </style>
   
               <script type="text/javascript">
   
               window.onerror = function(err)
               {
                   alert('window.onerror: ' + err);
               }
   
               function send(inputId)
               {
                   var input = document.getElementById(inputId);
                   if(input)
                   {
                       var value = input.value;
                       // Example of how extras info can be passed with the Engagement logs
                       var extras = '{"CustomerId":"MS290011"}';
                   }
   
                   if(value && value.length > 0)
                   {
                       switch(inputId)
                       {
                           case "event":
                           EngagementJs.sendEngagementEvent(value, extras);
                           break;
   
                           case "job":
                           EngagementJs.startEngagementJob(value, extras);
                           window.setTimeout(
                           function(){
                               EngagementJs.endEngagementJob(value);
                           }, 10000 );
                           break;
   
                           case "error":
                           EngagementJs.sendEngagementError(value, extras);
                           break;
   
                           case "appInfo":
                           var appInfo = '{"customer_name":"' + value + '"}';
                           EngagementJs.sendEngagementAppInfo(appInfo);
                           break;
                       }
                   }
               }
               </script>
   
           </head>
           <body>
               <h1>Bridge Tester</h1>
   
               <div id='engagement'>
   
                   <br/>
                   <h2>Event</h2>
                   <input type="text" id="event" size="35">
                   <button onclick="send('event')">Send</button>
   
                   <br/>
                   <h2>Job</h2>
                   <input type="text" id="job" size="35">
                   <button onclick="send('job')">Send</button>
   
                   <br/>
                   <h2>Error</h2>
                   <input type="text" id="error" size="35">
                   <button onclick="send('error')">Send</button
   
                   <br/>
                   <h2>AppInfo</h2>
                   <input type="text" id="appInfo" size="35">
                   <button onclick="send('appInfo')">Send</button>
   
               </div>
           </body>
        </html>
10. Обратите внимание на следующие моменты, касающиеся файла HTML выше:
    
    * Он содержит набор полей ввода, в которые можно ввести данные для использования в качестве имен для событий, заданий, ошибок и информации о приложении. При нажатии на кнопку рядом с ним выполняется вызов Javascript, который в конечном итоге вызывает методы из файла моста, чтобы передать этот вызов пакету SDK iOS в Службах мобильного взаимодействия. 
    * Мы добавляем теги для указания дополнительной статической информации для событий, заданий и даже ошибок, чтобы показать, как это можно сделать. Эти дополнительные сведения отправляются в виде строки JSON, которая, если заглянуть в файл `EngagementJsExports.m` , анализируется и передается вместе с отправкой событий, заданий и ошибок. 
    * Задание Служб мобильного взаимодействия запускается под именем, указанным в поле ввода, работает в течение 10 секунд и завершается. 
    * Информация о приложении Служб мобильного взаимодействия или тег передаются с "customer_name" в виде статического ключа и значения, введенных в поле ввода в качестве значения тега. 
11. Запустите приложение и вы увидите следующее. Теперь укажите какое-нибудь имя для тестового события (например такое, как указано ниже) и щелкните **Отправить** рядом с ним. 
    
     ![][1]
12. Если теперь перейти на вкладку **Мониторинг** своего приложения и раскрыть категорию **События -> Сведения**, вы увидите, что это событие отображается вместе со статическими сведениями о приложении, которые мы отправляем. 
    
    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-ios/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-ios/event-output.png
