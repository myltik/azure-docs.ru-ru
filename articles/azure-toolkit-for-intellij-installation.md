---
title: "Установка набора средств Azure для IntelliJ | Документация Майкрософт"
description: "Узнайте, как установить набор средств Azure для IntelliJ IDEA."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: c6817c7b-f28c-4c06-8216-41c7a8117de3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: bf11a8580500f78c4a96a02953f221501eeffe6c
ms.lasthandoff: 04/22/2017


---
# <a name="installing-the-azure-toolkit-for-intellij"></a>Установка набора средств Azure для IntelliJ
В набор средств Azure для IntelliJ входят шаблоны и функциональные возможности для простого создания, разработки, тестирования и развертывания приложений Azure с помощью среды разработки IntelliJ IDEA. Набор средств Azure для IntelliJ — это проект с открытым кодом, исходный код которого доступен по лицензии MIT на сайте проекта в GitHub по следующему URL-адресу:

<https://github.com/microsoft/azure-tools-for-java>

Набор средств Azure для IntelliJ можно установить двумя способами — из диалогового окна "Параметры" и их меню "Настройка" на начальном экране; оба способа установки будут продемонстрированы ниже.

[!INCLUDE [azure-toolkit-for-IntelliJ-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="to-install-the-azure-toolkit-for-intellij-from-the-settings-dialog-box"></a>Установка набора средств Azure для IntelliJ из диалогового окна параметров
1. Запустите IntelliJ IDEA.
2. Когда откроется IntelliJ IDEA, щелкните **Файл** и выберите **Параметры**.
   
    ![Откройте диалоговое окно параметров IntelliJ IDEA][01a]
3. В диалоговом окне "Параметры" нажмите кнопку **Подключаемые модули**, а затем **Browse repositories** (Обзор репозиториев).
   
    ![Диалоговое окно параметров IntelliJ IDEA][02a]
4. В диалоговом окне **Обзор репозиториев** введите "Azure" в поле поиска. Выделите **Azure Toolkit for IntelliJ** (Набор средств Azure для IntelliJ) и нажмите кнопку **Установить**.
   
    ![Поиск набора средств Azure для IntelliJ][03]
   
    Ход установки IntelliJ IDEA отобразится в диалоговом окне.
   
    ![Ход установки][04]
5. После завершения установки нажмите кнопку **Перезапуск IntelliJ IDEA**.
   
    ![Перезапуск IntelliJ IDEA][05]
6. Нажмите кнопку **ОК** , чтобы закрыть диалоговое окно параметров.
   
    ![Закройте диалоговое окно параметров IntelliJ IDEA][06]
7. При появлении запроса на перезапуск или приостановку IntelliJ IDEA нажмите кнопку **Перезагрузить**.
   
    ![Перезапуск IntelliJ IDEA][07]

## <a name="to-install-the-azure-toolkit-for-intellij-from-the-start-screen"></a>Установка набора средств Azure для IntelliJ с начального экрана
1. Запустите IntelliJ IDEA.
2. Когда откроется начальный экран IntelliJ IDEA, нажмите кнопку **Настройка** и выберите **Подключаемые модули**.
   
    ![Подключаемые модули IntelliJ IDEA][01b]
3. В диалоговом окне **Подключаемые модули** нажмите кнопку **Browse repositories** (Обзор репозиториев).
   
    ![Обзор репозиториев подключаемых модулей IntelliJ IDEA][02b]
4. В диалоговом окне **Обзор репозиториев** введите "Azure" в поле поиска. Выделите **Azure Toolkit for IntelliJ** (Набор средств Azure для IntelliJ) и нажмите кнопку **Установить**.
   
    ![Поиск набора средств Azure для IntelliJ][03]
   
    Ход установки IntelliJ IDEA отобразится в диалоговом окне.
   
    ![Ход установки][04]
5. После завершения установки нажмите кнопку **Перезапуск IntelliJ IDEA**.
   
    ![Перезапуск IntelliJ IDEA][05]
6. При появлении запроса на перезапуск или приостановку IntelliJ IDEA нажмите кнопку **Перезагрузить**.
   
    ![Перезапуск IntelliJ IDEA][07]

## <a name="see-also"></a>См. также
Дополнительные сведения о наборах средств Azure для Java IDE см. по следующим ссылкам:

* [Набор средств Azure для Eclipse]
  * [Новые возможности набора средств Azure для Eclipse]
  * [Установка набора средств Azure для Eclipse]
  * [Инструкции по входу для набора средств Azure для Eclipse]
  * [Создание веб-приложения Hello World для Azure в Eclipse]
* [Набор средств Azure для IntelliJ]
  * [Новые возможности набора средств Azure для IntelliJ]
  * *Установка набора средств Azure для IntelliJ (в этой статье)*
  * [Инструкции по входу для набора средств Azure для IntelliJ]
  * [Создание веб-приложения Hello World для Azure в IntelliJ]

Дополнительные сведения об использовании Azure с Java можно найти в [Центре разработчиков Java для Azure].

<!-- URL List -->

[Набор средств Azure для Eclipse]: ./azure-toolkit-for-eclipse.md
[Набор средств Azure для IntelliJ]: ./azure-toolkit-for-intellij.md
[Создание веб-приложения Hello World для Azure в Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Создание веб-приложения Hello World для Azure в IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Установка набора средств Azure для Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Installing the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Инструкции по входу для набора средств Azure для Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Инструкции по входу для набора средств Azure для IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Новые возможности набора средств Azure для Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Новые возможности набора средств Azure для IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Центре разработчиков Java для Azure]: https://azure.microsoft.com/develop/java/

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

