<properties 
	pageTitle="Отладка в Eclipse веб-приложения Java, размещенного в Azure | Microsoft Azure" 
	description="В этом учебнике показано, как выполнить отладку веб-приложения Java, работающего в Azure, с помощью набора средств Azure для Eclipse." 
	services="app-service\web" 
	documentationCenter="java" 
	authors="selvasingh" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="09/20/2016" 
	ms.author="asirveda;robmcm"/>

# Отладка в Eclipse веб-приложения Java, размещенного в Azure

В этом учебнике показано, как выполнить отладку веб-приложения Java, работающего в Azure, с помощью [набора средств Azure для Eclipse]. Для простоты в данном учебнике будет использован базовый пример Java Server Page (JSP), но действия будет те же, что и при отладке сервлета Java в Azure.

После завершения этого учебника приложение при отладке в Eclipse будет выглядеть следующим образом.

![][01]
 
## Предварительные требования

* Пакет SDK для Java (JDK) 1.8 или более поздней версии.
* Интегрированная среда разработки Eclipse для разработчиков Java EE, версия Indigo или более поздняя. Среду также можно загрузить с веб-страницы <http://www.eclipse.org/downloads/>.
* Дистрибутив веб-сервера или сервера приложений на основе Java, например Apache Tomcat или Jetty.
* Подписка Azure, которую можно получить на веб-сайте <https://azure.microsoft.com/free/> или <http://azure.microsoft.com/pricing/purchase-options/>.
* Набор средств Azure для Eclipse. Дополнительные сведения см. в статье [Установка набора средств Azure для Eclipse].
* Динамический веб-проект создан и развернут в службе приложений Azure. Пример см. в разделе [Создание веб-приложения Hello World для Azure в Eclipse].

## Отладка веб-приложения Java в Azure

Для выполнения указаний в этом разделе можно использовать существующий динамический веб-проект, который уже развернут как веб-приложение Java в Azure. Скачайте [пример динамического веб-проекта] и выполните действия, описанные в разделе [Создание веб-приложения Hello World для Azure в Eclipse], чтобы развернуть его в Azure.

1. Откройте Eclipse.

1. Настройте время ожидания для удаленной отладки.

    1. Щелкните меню **Windows** в Eclipse, затем щелкните **Параметры**.
    1. Разверните узел **Java** и щелкните **Отладка**.
    1. Задайте для параметров **Debugger timeout (ms)** (Время ожидания отладчика (мс)) и **Launch timeout (ms)** (Время ожидания запуска (мс)) значение `120000`.

        ![][02]

    1. Нажмите кнопку **OK**, чтобы закрыть диалоговое окно **Параметры**.

1. В представлении обозревателя проектов Eclipse щелкните правой кнопкой мыши динамический веб-проект, который вы развернули в Azure. В контекстном меню выберите **Azure**, затем щелкните **Веб-приложение Azure**.

    ![][03]

1. Если вы впервые отлаживаете динамический веб-проект, то откроется диалоговое окно **Debug Configurations** (Конфигурации отладки). Можно принять значения по умолчанию, которые указаны набором средств на вкладке **Подключение**. На вкладке **Источник** щелкните **Добавить**, затем **Java project** (Проект Java). Выберите **Dynamic Web Project** (Динамический веб-проект) и нажмите кнопку **ОК**. После этого щелкните **Отладка**.

    ![][04]

1. При появлении запроса **Enable remote debugging in the remote Web App now?** (Включить удаленную отладку в удаленном веб-приложении?) нажмите кнопку **ОК**.

1. При появлении запроса **Your web app is now ready for remote debugging** (Теперь веб-приложение готово к удаленной отладке) нажмите кнопку **ОК**.

    ![][05]

1. Когда появится диалоговое окно **Debug Configurations** (Конфигурации отладки), щелкните **Отладка**.

1. Откроется командная строка Windows или оболочка Unix и будет выполнена подготовка нужного подключения для отладки. Нужно дождаться успешного завершения подключения к удаленному веб-приложению Java, прежде чем продолжить. При использовании Windows командная строка будет выглядеть как на следующем рисунке.

    ![][06]

1. Вставьте точку останова в страницу JSP, затем откройте URL-адрес веб-приложения Java в браузере.

    1. Откройте **Azure Explorer** в Eclipse.
    1. Перейдите к разделу **Веб-приложения** и веб-приложению Java, отладку которого необходимо выполнить.
    1. Щелкните правой кнопкой мыши это веб-приложение и выберите пункт **Открыть в браузере**.
    1. Eclipse перейдет в режим отладки.

## Дальнейшие действия

Дополнительные сведения об использовании Azure с Java см. в [центре разработчиков Java для Azure].

Дополнительные сведения о создании веб-приложений Azure см. в разделе [Обзор веб-приложений].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[набора средств Azure для Eclipse]: ../azure-toolkit-for-eclipse.md
[Установка набора средств Azure для Eclipse]: ../azure-toolkit-for-eclipse-installation.md
[Создание веб-приложения Hello World для Azure в Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[пример динамического веб-проекта]: http://go.microsoft.com/fwlink/?LinkId=817337

[центре разработчиков Java для Azure]: https://azure.microsoft.com/develop/java/
[Обзор веб-приложений]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-eclipse/01-debug-java-web-app-in-eclipse.png
[02]: ./media/app-service-web-debug-java-web-app-in-eclipse/02-configure-eclipse-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-eclipse/03-debug-as.png
[04]: ./media/app-service-web-debug-java-web-app-in-eclipse/04-debug-configurations.png
[05]: ./media/app-service-web-debug-java-web-app-in-eclipse/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-eclipse/06-windows-command-prompt-connection-successful-to-remote.png

<!---HONumber=AcomDC_0921_2016-->