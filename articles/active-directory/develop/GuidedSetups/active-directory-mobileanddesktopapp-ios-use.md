---
title: "Приступая к работе с Azure AD версии 2 для iOS. Использование | Документы Майкрософт"
description: "В этой статье описано, как приложения iOS (Swift) могут вызывать API, которому необходимы маркеры доступа, с помощью конечной точки Azure Active Directory версии 2."
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.openlocfilehash: 2ac1117a31a101705539a1f75520ce8de43809a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Использование библиотеки проверки подлинности Майкрософт для получения маркера для API Microsoft Graph

Откройте файл `ViewController.swift` и замените код на следующий:

```swift
import UIKit
import MSAL

class ViewController: UIViewController, UITextFieldDelegate, URLSessionDelegate {
    
    let kClientID = "Your_Application_Id_Here"
    let kAuthority = "https://login.microsoftonline.com/common/v2.0"

    let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
    let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
    
    var accessToken = String()
    var applicationContext = MSALPublicClientApplication.init()

    @IBOutlet weak var loggingText: UITextView!
    @IBOutlet weak var signoutButton: UIButton!

     // This button will invoke the call to the Microsoft Graph API. It uses the
     // built in Swift libraries to create a connection.
    
    @IBAction func callGraphButton(_ sender: UIButton) {
        
        
        do {
            
            // We check to see if we have a current logged in user. If we don't, then we need to sign someone in.
            // We throw an interactionRequired so that we trigger the interactive signin.
            
            if  try self.applicationContext.users().isEmpty {
                throw NSError.init(domain: "MSALErrorDomain", code: MSALErrorCode.interactionRequired.rawValue, userInfo: nil)
            } else {
            
            // Acquire a token for an existing user silently
            
            try self.applicationContext.acquireTokenSilent(forScopes: self.kScopes, user: applicationContext.users().first) { (result, error) in
    
                    if error == nil {
                        self.accessToken = (result?.accessToken)!
                        self.loggingText.text = "Refreshing token silently)"
                        self.loggingText.text = "Refreshed Access token is \(self.accessToken)"
                        
                        self.signoutButton.isEnabled = true;
                        self.getContentWithToken()
    
                    } else {
                        self.loggingText.text = "Could not acquire token silently: \(error ?? "No error information" as! Error)"
    
                    }
                }
            }
        }  catch let error as NSError {
            
            // interactionRequired means we need to ask the user to sign-in. This usually happens
            // when the user's Refresh Token is expired or if the user has changed their password
            // among other possible reasons.
            
            if error.code == MSALErrorCode.interactionRequired.rawValue {
                
                self.applicationContext.acquireToken(forScopes: self.kScopes) { (result, error) in
                        if error == nil {
                            self.accessToken = (result?.accessToken)!
                            self.loggingText.text = "Access token is \(self.accessToken)"
                            self.signoutButton.isEnabled = true;
                            self.getContentWithToken()
                            
                        } else  {
                            self.loggingText.text = "Could not acquire token: \(error ?? "No error information" as! Error)"
                        }
                }
                
            }
            
        } catch {
            
            // This is the catch all error.
            
            self.loggingText.text = "Unable to acquire token. Got error: \(error)"
            
        }
    }

    override func viewDidLoad() {
        super.viewDidLoad()
        
        do {
             // Initialize a MSALPublicClientApplication with a given clientID and authority
            self.applicationContext = try MSALPublicClientApplication.init(clientId: kClientID, authority: kAuthority)
        } catch {
            self.loggingText.text = "Unable to create Application Context. Error: \(error)"
        }
    }


    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    
    override func viewWillAppear(_ animated: Bool) {
        
        if self.accessToken.isEmpty {
            signoutButton.isEnabled = false; 
        }
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Дополнительные сведения
#### <a name="getting-a-user-token-interactively"></a>Интерактивное получение маркера пользователя
При вызове метода `acquireToken` откроется окно браузера, в котором пользователю предлагается выполнить вход. Приложениям обычно требуется, чтобы пользователь выполнял вход интерактивно при первом доступе к защищенному ресурсу или при сбое автоматической операции для получения маркера (например, срок действия пароля пользователя истек).

#### <a name="getting-a-user-token-silently"></a>Автоматическое получение маркера пользователя
Метод `acquireTokenSilent` обрабатывает получение и обновление маркера без участия пользователя. После первого выполнения метода `acquireToken` обычно используется метод `acquireTokenSilent`, чтобы получить маркеры для доступа к защищенным ресурсам для последующих вызовов (например, автоматическое выполнение запросов или обновлений маркеров).

В конечном счете в `acquireTokenSilent` произойдет сбой, например в случае выхода пользователя из системы или смены пароля на другом устройстве. Когда MSAL обнаруживает, что эту проблему можно решить, запросив интерактивное действие, возникает исключение `MSALErrorCode.interactionRequired`. Приложение может обработать это исключение двумя способами:

1.  Вызвать `acquireToken` немедленно, в результате чего пользователю будет предложено выполнить вход. Этот шаблон обычно используется в интерактивных приложениях, где пользователю недоступно автономное содержимое. В примере приложения, созданного в ходе интерактивной настройки, используется следующий шаблон: вы увидите его в действии при первом запуске приложения. Так как приложение еще не использовалось, `applicationContext.users().first` будет содержать значение null и будет выдано исключение ` MSALErrorCode.interactionRequired `. Код в примере обработает исключение, вызвав `acquireToken`, в результате чего пользователю будет предложено войти.

2.  Приложения также могут визуально уведомить пользователя, что требуется интерактивный вход, чтобы пользователь мог выбрать подходящее время для входа или приложение могло повторить метод `acquireTokenSilent` ​​позднее. Обычно это применимо для тех случаев, когда пользователь может использовать другие функции приложения, на которые это не влияет, например, когда в приложении есть автономное содержимое. В этом случае пользователь может решить, когда он хочет войти, чтобы получить доступ к защищенному ресурсу или обновить устаревшие данные, или ваше приложение может решить повторить `acquireTokenSilent` при восстановлении сети ​​после временной недоступности.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Вызов API Microsoft Graph с помощью полученного маркера

Добавьте указанный ниже новый метод в файл `ViewController.swift`. Этот метод используется для выполнения запроса `GET` к API Microsoft Graph с использованием *Заголовка авторизации HTTP*:

```swift
func getContentWithToken() {
    
    let sessionConfig = URLSessionConfiguration.default
    
    // Specify the Graph API endpoint
    let url = URL(string: kGraphURI)
    var request = URLRequest(url: url!)
    
    // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
    request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
    let urlSession = URLSession(configuration: sessionConfig, delegate: self, delegateQueue: OperationQueue.main)
    
    urlSession.dataTask(with: request) { data, response, error in
        let result = try? JSONSerialization.jsonObject(with: data!, options: [])
                    if result != nil {
                
                self.loggingText.text = result.debugDescription
            }
        }.resume()
}
```

<!--start-collapse-->
### <a name="making-a-rest-call-against-a-protected-api"></a>Вызов REST через защищенный API

В этом примере приложения метод `getContentWithToken()` выполняет HTTP-запрос `GET` к защищенному ресурсу, требующему маркер, а затем возвращает содержимое вызывающему объекту. Этот метод добавляет полученный маркер в *заголовок авторизации HTTP*. В этом примере ресурс — это конечная точка *me* API Microsoft Graph, которая отображает сведения о профиле пользователя.
<!--end-collapse-->

## <a name="set-up-sign-out"></a>Настройка выхода

Добавьте следующий метод в файл `ViewController.swift` для выхода пользователя:

```swift 
@IBAction func signoutButton(_ sender: UIButton) {

    do {
        
        // Removes all tokens from the cache for this application for the provided user
        // first parameter:   The user to remove from the cache
        
        try self.applicationContext.remove(self.applicationContext.users().first)
        self.signoutButton.isEnabled = false;
        
    } catch let error {
        self.loggingText.text = "Received error signing user out: \(error)"
    }
}
```
<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>Дополнительные сведения о выходе

Метод `signoutButton` удаляет пользователя из кэша пользователей MSAL. Фактически это заставляет MSAL забыть о текущем пользователе, поэтому будущие запросы на получение маркеров будут успешными только в том случае, если будут выполняться автоматически.

Несмотря на то, что приложение в этом примере поддерживает одного пользователя, MSAL поддерживает сценарии, в которых несколько пользователей могут войти в систему одновременно, например для приложения электронной почты, в котором у пользователя есть несколько учетных записей.
<!--end-collapse-->

## <a name="register-the-callback"></a>Регистрация обратного вызова

После проверки подлинности пользователь перенаправляет пользователя обратно в приложение. Чтобы зарегистрировать обратный вызов, выполните указанные ниже действия:

1.  Откройте `AppDelegate.swift` и импортируйте MSAL:

```swift
import MSAL
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Добавьте следующий метод в класс <code>AppDelegate</code> для обработки обратных вызовов:
</li>
</ol>

```swift
// @brief Handles inbound URLs. Checks if the URL matches the redirect URI for a pending AppAuth
// authorization request and if so, will look for the code in the response.

func application(_ application: UIApplication, open url: URL, sourceApplication: String?, annotation: Any) -> Bool {
    
    print("Received callback!")
    
    MSALPublicClientApplication.handleMSALResponse(url)
    
    return true
}
```

