<properties 
	pageTitle="Отладка в IntelliJ веб-приложения Java, размещенного в Azure | Microsoft Azure" 
	description="В этом учебнике показано, как выполнить отладку веб-приложения Java, работающего в Azure, с помощью набора средств Azure для IntelliJ." 
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

# Отладка в IntelliJ веб-приложения Java, размещенного в Azure

В этом учебнике показано, как выполнить отладку веб-приложения Java, работающего в Azure, с помощью [набора средств Azure для IntelliJ]. Для простоты в данном учебнике будет использован базовый пример Java Server Page (JSP), но действия будет те же, что и при отладке сервлета Java в Azure.

После завершения этого учебника приложение при отладке в IntelliJ будет выглядеть следующим образом.

![][01]
 
## Предварительные требования

* Пакет SDK для Java (JDK) 1.8 или более поздней версии.
* IntelliJ IDEA Ultimate Edition. Среду также можно загрузить с веб-страницы <https://www.jetbrains.com/idea/download/index.html>.
* Дистрибутив веб-сервера или сервера приложений на основе Java, например Apache Tomcat или Jetty.
* Подписка Azure, которую можно получить на веб-сайте <https://azure.microsoft.com/free/> или <http://azure.microsoft.com/pricing/purchase-options/>.
* Набор средств Azure для IntelliJ. Дополнительные сведения см. в статье [Установка набора средств Azure для IntelliJ].
* Динамический веб-проект создан и развернут в службе приложений Azure. Пример см. в разделе [Создание веб-приложения Hello World для Azure в IntelliJ].

## Отладка веб-приложения Java в Azure

Для выполнения указаний в этом разделе можно использовать существующий динамический веб-проект, который уже развернут как веб-приложение Java в Azure. Скачайте [пример динамического веб-проекта] и выполните действия, описанные в разделе [Создание веб-приложения Hello World для Azure в IntelliJ], чтобы развернуть его в Azure.

1. Откройте в IntelliJ проект веб-приложения Java, который был развернут в Azure.

1. Щелкните меню **Запуск**, затем щелкните **Изменить конфигурации**.

    ![][02]

1. Когда откроется диалоговое окно **Run/Debug Configurations** (Конфигурации выполнения и отладки), выполните следующее.

    1. Выберите **Веб-приложение Azure**.
    1. Щелкните **+** для добавления новой конфигурации.
    1. Укажите **имя** конфигурации.
    1. Примите остальные значения по умолчанию, предлагаемые набором средств Azure, и нажмите кнопку **ОК**.

        ![][03]

1. Выберите только что созданную конфигурацию отладки веб-приложений Azure в правой верхней части меню и щелкните **Отладка**.

    ![][04]

1. При появлении запроса **Enable remote debugging in the remote Web App now?** (Включить удаленную отладку в удаленном веб-приложении?) нажмите кнопку **ОК**.

1. При появлении запроса **Your web app is now ready for remote debugging** (Теперь веб-приложение готово к удаленной отладке) нажмите кнопку **ОК**.

    ![][05]

1. Выберите только что созданную конфигурацию отладки веб-приложений Azure в правой верхней части меню и щелкните **Отладка**.

1. Откроется командная строка Windows или оболочка Unix и будет выполнена подготовка нужного подключения для отладки. Нужно дождаться успешного завершения подключения к удаленному веб-приложению Java, прежде чем продолжить. При использовании Windows командная строка будет выглядеть как на следующем рисунке.

    ![][06]

1. Вставьте точку останова в страницу JSP, затем откройте URL-адрес веб-приложения Java в браузере.

    1. Откройте **Azure Explorer** в IntelliJ.
    1. Перейдите к разделу **Веб-приложения** и веб-приложению Java, отладку которого необходимо выполнить.
    1. Щелкните правой кнопкой мыши это веб-приложение и выберите пункт **Открыть в браузере**.
    1. IntelliJ перейдет в режим отладки.

## Дальнейшие действия

Дополнительные сведения об использовании Azure с Java см. в [центре разработчиков Java для Azure].

Дополнительные сведения о создании веб-приложений Azure см. в разделе [Обзор веб-приложений].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[набора средств Azure для IntelliJ]: ../azure-toolkit-for-intellij.md
[Установка набора средств Azure для IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Создание веб-приложения Hello World для Azure в IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[пример динамического веб-проекта]: http://go.microsoft.com/fwlink/?LinkId=817337

[центре разработчиков Java для Azure]: https://azure.microsoft.com/develop/java/
[Обзор веб-приложений]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-intellij/01-debug-java-web-app-in-intellij.png
[02]: ./media/app-service-web-debug-java-web-app-in-intellij/02-configure-intellij-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-intellij/03-debug-configuration.png
[04]: ./media/app-service-web-debug-java-web-app-in-intellij/04-select-debug.png
[05]: ./media/app-service-web-debug-java-web-app-in-intellij/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-intellij/06-windows-command-prompt-connection-successful-to-remote.png

<!---HONumber=AcomDC_0921_2016-->