<properties
	pageTitle="Установка набора средств Azure для IntelliJ | Microsoft Azure"
	description="Узнайте, как установить набор средств Azure для IntelliJ IDEA."
	services=""
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="multiple"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="06/07/2016" 
	ms.author="robmcm"/>

# Установка набора средств Azure для IntelliJ

В набор средств Azure для IntelliJ входят шаблоны и функциональные возможности для простого создания, разработки, тестирования и развертывания приложений Azure с помощью среды разработки IntelliJ IDEA. Набор средств Azure для IntelliJ — это проект с открытым кодом, исходный код которого доступен по лицензии MIT на сайте проекта в GitHub по следующему URL-адресу:

<https://github.com/microsoft/azure-tools-for-java>

Набор средств Azure для IntelliJ можно установить двумя способами — из диалогового окна "Параметры" и их меню "Настройка" на начальном экране; оба способа установки будут продемонстрированы ниже.

[AZURE.INCLUDE [azure-toolkit-for-IntelliJ-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## Установка набора средств Azure для IntelliJ из диалогового окна параметров

1. Запустите IntelliJ IDEA.

1. Когда откроется IntelliJ IDEA, щелкните **Файл** и выберите **Параметры**.

    ![Откройте диалоговое окно параметров IntelliJ IDEA][01a]

1. В диалоговом окне "Параметры" нажмите кнопку **Подключаемые модули**, а затем **Обзор репозиториев**.

    ![Диалоговое окно параметров IntelliJ IDEA][02a]

1. В диалоговом окне **Обзор репозиториев** введите "Azure" в поле поиска. Выделите **Набор средств Azure для IntelliJ** и нажмите кнопку **Установить**.

    ![Поиск набора средств Azure для IntelliJ][03]

    Ход установки IntelliJ IDEA отобразится в диалоговом окне.

    ![Ход установки][04]

1. После завершения установки нажмите кнопку **Перезапуск IntelliJ IDEA**.

    ![Перезапустите IntelliJ IDEA][05]

1. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно параметров.

    ![Закройте диалоговое окно параметров IntelliJ IDEA][06]

1. При появлении запроса на перезапуск или приостановку IntelliJ IDEA нажмите кнопку **Перезагрузить**.

    ![Перезапустите IntelliJ IDEA][07]

## Установка набора средств Azure для IntelliJ с начального экрана

1. Запустите IntelliJ IDEA.

1. Когда откроется начальный экран IntelliJ IDEA, нажмите кнопку **Настройка** и выберите **Подключаемые модули**.

    ![Подключаемые модули IntelliJ IDEA][01b]

1. В диалоговом окне **Подключаемые модули** нажмите кнопку **Обзор репозиториев**.

    ![Обзор репозиториев подключаемых модулей IntelliJ IDEA][02b]

1. В диалоговом окне **Обзор репозиториев** введите "Azure" в поле поиска. Выделите **Набор средств Azure для IntelliJ** и нажмите кнопку **Установить**.

    ![Поиск набора средств Azure для IntelliJ][03]

    Ход установки IntelliJ IDEA отобразится в диалоговом окне.

    ![Ход установки][04]

1. После завершения установки нажмите кнопку **Перезапуск IntelliJ IDEA**.

    ![Перезапустите IntelliJ IDEA][05]

1. При появлении запроса на перезапуск или приостановку IntelliJ IDEA нажмите кнопку **Перезагрузить**.

    ![Перезапустите IntelliJ IDEA][07]

## См. также

Дополнительные сведения о наборах средств Azure для Java IDE см. по следующим ссылкам:

- [Набор средств Azure для Eclipse]
  - [Установка набора средств Azure для Eclipse]
  - [Создание веб-приложения Hello World для Azure в Eclipse]
  - [Новые возможности набора средств Azure для Eclipse]
- [Набор средств Azure для IntelliJ]
  - *Установка набора средств Azure для IntelliJ (в этой статье)*
  - [Создание веб-приложения Hello World для Azure в IntelliJ]

Дополнительные сведения об использовании Azure с Java см. в [центре разработчиков Java для Azure].

<!-- URL List -->

[Набор средств Azure для Eclipse]: ./azure-toolkit-for-eclipse.md
[Набор средств Azure для IntelliJ]: ./azure-toolkit-for-intellij.md
[Создание веб-приложения Hello World для Azure в Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Создание веб-приложения Hello World для Azure в IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Установка набора средств Azure для Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Новые возможности набора средств Azure для Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md

[центре разработчиков Java для Azure]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[01a]: ./media/azure-toolkit-for-intellij-installation/01-intellij-file-settings.png
[01b]: ./media/azure-toolkit-for-intellij-installation/01-intellij-configure-dropdown.png
[02a]: ./media/azure-toolkit-for-intellij-installation/02-intellij-settings-dialog.png
[02b]: ./media/azure-toolkit-for-intellij-installation/02-intellij-plugins-dialog.png
[03]: ./media/azure-toolkit-for-intellij-installation/03-intellij-browse-repositories.png
[04]: ./media/azure-toolkit-for-intellij-installation/04-install-progress.png
[05]: ./media/azure-toolkit-for-intellij-installation/05-restart-intellij.png
[06]: ./media/azure-toolkit-for-intellij-installation/06-intellij-settings-dialog.png
[07]: ./media/azure-toolkit-for-intellij-installation/07-restart-intellij.png

<!---HONumber=AcomDC_0608_2016-->