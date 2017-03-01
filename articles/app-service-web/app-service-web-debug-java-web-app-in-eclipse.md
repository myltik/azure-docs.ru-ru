---
title: "Отладка в Eclipse веб-приложения Java, размещенного в Azure | Документация Майкрософт"
description: "В этом учебнике показано, как выполнить отладку веб-приложения Java, работающего в Azure, с помощью набора средств Azure для Eclipse."
services: app-service\web
documentationcenter: java
author: selvasingh
manager: erikre
editor: 
ms.assetid: 321d2d19-9ce0-4165-8555-b6b15e671393
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: asirveda;robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 8a4f22442d030dae602171555bafb3733d61ebd7
ms.lasthandoff: 02/16/2017


---
# <a name="debug-a-java-web-app-on-azure-in-eclipse"></a>Отладка в Eclipse веб-приложения Java, размещенного в Azure
В этом учебнике показано, как выполнить отладку веб-приложения Java, работающего в Azure, с помощью [набора средств Azure для Eclipse]. Для простоты в данном учебнике будет использован базовый пример Java Server Page (JSP), но действия будет те же, что и при отладке сервлета Java в Azure.

После завершения этого учебника приложение при отладке в Eclipse будет выглядеть следующим образом.

![][01]

## <a name="prerequisites"></a>Предварительные требования
* Пакет SDK для Java (JDK) 1.8 или более поздней версии.
* Интегрированная среда разработки Eclipse для разработчиков Java EE, версия Indigo или более поздняя. Скачать ее можно на веб-странице <http://www.eclipse.org/downloads/>.
* Дистрибутив веб-сервера или сервера приложений на основе Java, например Apache Tomcat или Jetty.
* Подписка на Azure, которую можно получить, перейдя по ссылке <https://azure.microsoft.com/en-us/free/> или <http://azure.microsoft.com/pricing/purchase-options/>.
* Набор средств Azure для Eclipse. Дополнительные сведения см. в статье [Установка набора средств Azure для Eclipse].
* Динамический веб-проект создан и развернут в службе приложений Azure. Пример см. в статье [Создание веб-приложения Hello World для Azure в Eclipse].

## <a name="to-debug-a-java-web-app-on-azure"></a>Отладка веб-приложения Java в Azure
Для выполнения указаний в этом разделе можно использовать имеющийся динамический веб-проект, который уже развернут как веб-приложение Java в Azure. Скачайте [пример динамического веб-проекта] и выполните действия, описанные в статье [Создание веб-приложения Hello World для Azure в Eclipse], чтобы развернуть его в Azure. 

1. Откройте Eclipse.
2. Настройте время ожидания для удаленной отладки.
   
   1. Щелкните меню **Windows** в Eclipse, затем щелкните **Параметры**.
   2. Разверните узел **Java** и щелкните **Отладка**.
   3. Задайте для параметров **Debugger timeout (ms)** (Время ожидания отладчика (мс)) и **Launch timeout (ms)** (Время ожидания запуска (мс)) значение `120000`.
      
       ![][02]
   4. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Параметры**.
3. В представлении обозревателя проектов Eclipse щелкните правой кнопкой мыши динамический веб-проект, развернутый в Azure. В контекстном меню выберите **Azure**, затем щелкните **Веб-приложение Azure**.
   
    ![][03]
4. Если вы впервые отлаживаете динамический веб-проект, то откроется диалоговое окно **Debug Configurations** (Конфигурации отладки). Вы можете принять значения по умолчанию, указанные с использованием набора средств на вкладке **Подключение**. На вкладке **Источник** щелкните **Добавить**, а затем **Java project** (Проект Java). Выберите **Dynamic Web Project** (Динамический веб-проект) и нажмите кнопку **ОК**. После этого щелкните **Отладка**.
   
    ![][04]
5. При появлении запроса **Enable remote debugging in the remote Web App now?** (Включить удаленную отладку в удаленном веб-приложении?) нажмите кнопку **ОК**.
6. При появлении запроса **Your web app is now ready for remote debugging** (Теперь веб-приложение готово к удаленной отладке) нажмите кнопку **ОК**.
   
    ![][05]
7. Когда появится диалоговое окно **Debug Configurations** (Конфигурации отладки), щелкните **Отладка**.
8. Откроется командная строка Windows или оболочка Unix и будет выполнена подготовка нужного подключения для отладки. Нужно дождаться успешного завершения подключения к удаленному веб-приложению Java, прежде чем продолжить. При использовании Windows командная строка будет выглядеть как на следующем рисунке.
   
    ![][06]
9. Вставьте точку останова в страницу JSP, затем откройте URL-адрес веб-приложения Java в браузере.
   
   1. Откройте **Azure Explorer** в Eclipse.
   2. Перейдите к разделу **Веб-приложения** и веб-приложению Java, отладку которого необходимо выполнить.
   3. Щелкните правой кнопкой мыши это веб-приложение и выберите пункт **Открыть в браузере**.
   4. Eclipse перейдет в режим отладки.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об использовании Azure с Java можно найти в [Центре разработчиков Java для Azure].

Дополнительные сведения о создании веб-приложений Azure см. в разделе [Обзор веб-приложений].

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[набора средств Azure для Eclipse]: ../azure-toolkit-for-eclipse.md
[Установка набора средств Azure для Eclipse]: ../azure-toolkit-for-eclipse-installation.md
[Создание веб-приложения Hello World для Azure в Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[пример динамического веб-проекта]: http://go.microsoft.com/fwlink/?LinkId=817337

[Центре разработчиков Java для Azure]: https://azure.microsoft.com/develop/java/
[Обзор веб-приложений]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-eclipse/01-debug-java-web-app-in-eclipse.png
[02]: ./media/app-service-web-debug-java-web-app-in-eclipse/02-configure-eclipse-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-eclipse/03-debug-as.png
[04]: ./media/app-service-web-debug-java-web-app-in-eclipse/04-debug-configurations.png
[05]: ./media/app-service-web-debug-java-web-app-in-eclipse/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-eclipse/06-windows-command-prompt-connection-successful-to-remote.png

