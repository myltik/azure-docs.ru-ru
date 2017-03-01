---
title: "Отладка в IntelliJ веб-приложения Java, размещенного в Azure | Документация Майкрософт"
description: "В этом учебнике показано, как выполнить отладку веб-приложения Java, работающего в Azure, с помощью набора средств Azure для IntelliJ."
services: app-service\web
documentationcenter: java
author: selvasingh
manager: erikre
editor: 
ms.assetid: 78148648-5a89-4b7d-98f1-9cf8f38dfe8d
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: asirveda;robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 50db8f7d1d514d076af453a34ca3a147e357e905
ms.lasthandoff: 02/11/2017


---
# <a name="debug-a-java-web-app-on-azure-in-intellij"></a>Отладка в IntelliJ веб-приложения Java, размещенного в Azure
В этом учебнике показано, как выполнить отладку веб-приложения Java, работающего в Azure, с помощью [набора средств Azure для IntelliJ]. Для простоты в данном учебнике будет использован базовый пример Java Server Page (JSP), но действия будет те же, что и при отладке сервлета Java в Azure.

После завершения этого учебника приложение при отладке в IntelliJ будет выглядеть следующим образом.

![][01]

## <a name="prerequisites"></a>Предварительные требования
* Пакет SDK для Java (JDK) 1.8 или более поздней версии.
* IntelliJ IDEA Ultimate Edition. Его можно скачать по адресу <https://www.jetbrains.com/idea/download/index.html>.
* Дистрибутив веб-сервера или сервера приложений на основе Java, например Apache Tomcat или Jetty.
* Подписка на Azure, которую можно получить, перейдя по ссылке <https://azure.microsoft.com/en-us/free/> или <http://azure.microsoft.com/pricing/purchase-options/>.
* Набор средств Azure для IntelliJ. Дополнительные сведения см. в статье [Установка набора средств Azure для IntelliJ].
* Динамический веб-проект создан и развернут в службе приложений Azure. Пример см. в статье [Создание веб-приложения Hello World для Azure в IntelliJ].

## <a name="to-debug-a-java-web-app-on-azure"></a>Отладка веб-приложения Java в Azure
Для выполнения указаний в этом разделе можно использовать имеющийся динамический веб-проект, который уже развернут как веб-приложение Java в Azure. Скачайте [пример динамического веб-проекта] и выполните действия, описанные в статье [Создание веб-приложения Hello World для Azure в IntelliJ], чтобы развернуть его в Azure. 

1. Откройте в IntelliJ проект веб-приложения Java, который был развернут в Azure.
2. Щелкните меню **Запуск**, а затем щелкните **Edit Configurations...** (Изменить конфигурации...).
   
    ![][02]
3. Когда откроется диалоговое окно **Run/Debug Configurations** (Конфигурации выполнения и отладки), выполните следующее. 
   
   1. Выберите **Веб-приложение Azure**.
   2. Щелкните **+** для добавления новой конфигурации.
   3. Укажите **имя** конфигурации.
   4. Примите остальные значения по умолчанию, предлагаемые набором средств Azure, и нажмите кнопку **ОК**.
      
       ![][03]
4. Выберите только что созданную конфигурацию отладки веб-приложений Azure в правой верхней части меню и щелкните **Отладка**
   
    ![][04]
5. При появлении запроса **Enable remote debugging in the remote Web App now?** (Включить удаленную отладку в удаленном веб-приложении?) нажмите кнопку **ОК**.
6. При появлении запроса **Your web app is now ready for remote debugging** (Теперь веб-приложение готово к удаленной отладке) нажмите кнопку **ОК**.
   
    ![][05]
7. Выберите только что созданную конфигурацию отладки веб-приложений Azure в правой верхней части меню и щелкните **Отладка**.
8. Откроется командная строка Windows или оболочка Unix и будет выполнена подготовка нужного подключения для отладки. Нужно дождаться успешного завершения подключения к удаленному веб-приложению Java, прежде чем продолжить. При использовании Windows командная строка будет выглядеть как на следующем рисунке.
   
    ![][06]
9. Вставьте точку останова в страницу JSP, затем откройте URL-адрес веб-приложения Java в браузере.
   
   1. Откройте **Azure Explorer** в IntelliJ.
   2. Перейдите к разделу **Веб-приложения** и веб-приложению Java, отладку которого необходимо выполнить.
   3. Щелкните правой кнопкой мыши это веб-приложение и выберите пункт **Открыть в браузере**.
   4. IntelliJ перейдет в режим отладки.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об использовании Azure с Java можно найти в [Центре разработчиков Java для Azure].

Дополнительные сведения о создании веб-приложений Azure см. в разделе [Обзор веб-приложений].

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[набора средств Azure для IntelliJ]: ../azure-toolkit-for-intellij.md
[Установка набора средств Azure для IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Создание веб-приложения Hello World для Azure в IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[пример динамического веб-проекта]: http://go.microsoft.com/fwlink/?LinkId=817337

[Центре разработчиков Java для Azure]: https://azure.microsoft.com/develop/java/
[Обзор веб-приложений]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-intellij/01-debug-java-web-app-in-intellij.png
[02]: ./media/app-service-web-debug-java-web-app-in-intellij/02-configure-intellij-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-intellij/03-debug-configuration.png
[04]: ./media/app-service-web-debug-java-web-app-in-intellij/04-select-debug.png
[05]: ./media/app-service-web-debug-java-web-app-in-intellij/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-intellij/06-windows-command-prompt-connection-successful-to-remote.png

