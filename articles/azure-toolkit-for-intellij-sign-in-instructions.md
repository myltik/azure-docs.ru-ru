---
title: "Инструкции по входу для набора средств Azure для IntelliJ | Документация Майкрософт"
description: "Сведения о входе в Microsoft Azure с помощью набора средств Azure для IntelliJ."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 4e2ed072bdaea0a71fef042c0c72b7656a42bbe8
ms.contentlocale: ru-ru
ms.lasthandoff: 05/15/2017


---

# <a name="sign-in-instructions-for-the-azure-toolkit-for-intellij"></a>Инструкции по входу для набора средств Azure для IntelliJ

Набор средств Azure для IntelliJ предоставляет два метода для входа в систему с помощью учетной записи Azure:

  * **Интерактивный.** Учетные данные Azure необходимо вводить при каждом входе в учетную запись.
  * **Автоматический.** Вы создаете файл учетных данных, который можно использовать для автоматического входа в учетную запись Azure.

В разделах ниже описано использование каждого из методов.

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="sign-in-to-your-azure-account-interactively"></a>Интерактивный вход в учетную запись Azure

Чтобы войти в Azure, вручную введя учетные данные Azure, сделайте следующее:

1. Откройте проект с помощью IntelliJ IDEA.

2. Выберите **Tools** (Средства), наведите указатель мыши на пункт **Azure**, а затем щелкните **Azure Sign In** (Вход в Azure).

   ![Команда Azure Sign In (Вход в Azure) в IntelliJ][I01]

3. В окне **Azure Sign In** (Вход в Azure) выберите **Interactive** (Интерактивный) и щелкните **Sign in** (Войти).

   ![Окно Azure Sign In (Вход в Azure) с выбранным значением Interactive (Интерактивный)][I02]

4. При отображении диалогового окна **Azure Log In** (Вход в Azure) введите учетные данные и щелкните **Sign in** (Войти).

   ![Диалоговое окно входа Azure][I03]

5. В диалоговом окне **Select Subscriptions** (Выбор подписок) выберите нужные подписки и нажмите кнопку **ОК**.

   ![Диалоговое окно выбора подписок][I04]

## <a name="sign-out-of-your-azure-account-after-you-have-signed-in-interactively"></a>Выход из учетной записи Azure после интерактивного входа

После настройки учетной записи с помощью действий, описанных в предыдущем разделе, вы будете автоматически выходить из своей учетной записи Azure при каждом перезапуске IntelliJ IDEA. Но если вы хотите выйти из учетной записи Azure, *не перезапуская* IntelliJ IDEA, сделайте следующее:

1. В IntelliJ IDEA в меню **Tools** (Средства) наведите указатель мыши на пункт **Azure**, а затем щелкните **Azure Sign Out** (Выход из Azure).

   ![Команда Azure Sign Out (Выход из Azure) в IntelliJ][L01]

2. В диалоговом окне подтверждения **выхода из Azure** нажмите кнопку **Да**.

   ![Диалоговое окно подтверждения выхода из Azure][L02]

## <a name="sign-in-to-your-azure-account-automatically"></a>Автоматический вход в учетную запись Azure

В этом разделе показано, как создать файл учетных данных, содержащий данные субъекта-службы. После выполнения этого процесса Eclipse использует файл учетных данных для автоматического входа в Azure при каждом открытии проекта.

1. Откройте проект с помощью IntelliJ IDEA.

2. В меню **Tools** (Средства) наведите указатель мыши на пункт **Azure**, а затем щелкните **Azure Sign In** (Вход в Azure).

   ![Команда Azure Sign In (Вход в Azure) в IntelliJ][A01]

3. В диалоговом окне **Azure Sign In** (Вход в Azure) выберите **Automated** (Автоматически) и щелкните **New** (Создать).

   ![Окно Azure Sign In (Вход в Azure) с выбранным значением Automated (Автоматически)][A02]

4. В диалоговом окне **входа в Azure** введите учетные данные и щелкните **Sign in** (Войти).

   ![Диалоговое окно входа Azure][A03]

5. В диалоговом окне **Create authentication files** (Создание файлов проверки подлинности) выберите нужные подписки, конечный каталог и нажмите кнопку **Start** (Начать).

   ![Окно Create authentication files (Создание файлов проверки подлинности)][A04]

6. В диалоговом окне **Service Principal Creation Status** (Состояние создания субъекта-службы) после успешного создания файлов нажмите кнопку **ОК**.

   ![Диалоговое окно Service Principal Creation Status (Состояние создания субъекта-службы)][A05]

7. В окне **Azure Sign In** (Вход в Azure) щелкните **Sign in** (Войти).

   ![Диалоговое окно входа в Azure][A06]

8. В диалоговом окне **Select Subscriptions** (Выбор подписок) выберите нужные подписки и нажмите кнопку **ОК**.

   ![Диалоговое окно выбора подписок][A07]

## <a name="sign-out-of-your-azure-account-after-you-have-signed-in-automatically"></a>Выход из учетной записи Azure после автоматического входа

После настройки учетной записи с помощью действий, описанных в предыдущем разделе, набор средств Azure автоматически входит в учетную запись Azure при каждом перезапуске IntelliJ IDEA. Чтобы выйти из учетной записи Azure и предотвратить автоматический вход набора средств Azure, сделайте следующее:

1. В IntelliJ IDEA в меню **Tools** (Средства) наведите указатель мыши на пункт **Azure**, а затем щелкните **Azure Sign Out** (Выход из Azure).

   ![Команда Azure Sign Out (Выход из Azure) в IntelliJ][L01]

2. В диалоговом окне подтверждения **выхода из Azure** нажмите кнопку **Да**.

   ![Диалоговое окно подтверждения выхода из Azure][L03]

## <a name="sign-in-to-your-azure-account-automatically-by-using-an-existing-credentials-file"></a>Автоматический вход в учетную запись Azure с помощью имеющегося файла учетных данных

Если вы выходите из учетной записи Azure во время использования IntelliJ IDEA, вам потребуется использовать созданный файл учетных данных для автоматического входа в учетную запись Azure. Чтобы настроить набор средств Azure для Eclipse на использование имеющегося файла учетных данных, сделайте следующее:

1. Откройте проект с помощью IntelliJ IDEA.

2. В меню **Tools** (Средства) наведите указатель мыши на пункт **Azure**, а затем щелкните **Azure Sign In** (Вход в Azure).

   ![Команда Azure Sign In (Вход в Azure) в IntelliJ][A01]

3. В диалоговом окне **Azure Sign In** (Вход в Azure) выберите **Automated** (Автоматически) и щелкните **Browse** (Обзор).

   ![Окно Azure Sign In (Вход в Azure) с выбранным значением Automated (Автоматически)][A02]

4. В диалоговом окне **Select Authentication File** (Выбор файла проверки подлинности) выберите созданный ранее файл учетных данных ранее и нажмите кнопку **Select** (Выбрать).

   ![Диалоговое окно Select Authentication File (Выбор файла проверки подлинности)][A08]

5. В окне **Azure Sign In** (Вход в Azure) щелкните **Sign in** (Войти).

   ![Окно Azure Sign In (Вход в Azure) с выбранным значением Automated (Автоматически)][A06]

6. В диалоговом окне **Select Subscriptions** (Выбор подписок) выберите нужные подписки и нажмите кнопку **ОК**.

   ![Диалоговое окно выбора подписок][A07]

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о наборах средств Azure для Java IDE см. по следующим ссылкам:

* [Набор средств Azure для Eclipse]
  * [Новые возможности набора средств Azure для Eclipse]
  * [Установка набора средств Azure для Eclipse]
  * [Инструкции по входу для набора средств Azure для Eclipse]
  * [Создание веб-приложения Azure (цен. категория "Базовый") с помощью Eclipse]
* [Набор средств Azure для IntelliJ]
  * [Новые возможности набора средств Azure для IntelliJ]
  * [Установка набора средств Azure для IntelliJ]
  * *Инструкции по входу для набора средств Azure для IntelliJ (эта статья)*
  * [Создание веб-приложения Azure (цен. категория "Базовый") в IntelliJ]

Дополнительные сведения об использовании Azure см. в [центре разработчиков Java для Azure] и на странице [инструментов Java для Visual Studio Team Services].

<!-- URL List -->

[Набор средств Azure для Eclipse]: ./azure-toolkit-for-eclipse.md
[Набор средств Azure для IntelliJ]: ./azure-toolkit-for-intellij.md
[Создание веб-приложения Hello World для Azure в Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Создание веб-приложения Azure (цен. категория "Базовый") в IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Установка набора средств Azure для Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Установка набора средств Azure для IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Инструкции по входу для набора средств Azure для Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Sign-in instructions for the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Новые возможности набора средств Azure для Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Новые возможности набора средств Azure для IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[центре разработчиков Java для Azure]: https://azure.microsoft.com/develop/java/
[инструментов Java для Visual Studio Team Services]: https://java.visualstudio.com/ (Инструменты Java для Visual Studio Team Services)

<!-- IMG List -->

[I01]: ./media/azure-toolkit-for-intellij-sign-in-instructions/I01.png
[I02]: ./media/azure-toolkit-for-intellij-sign-in-instructions/I02.png
[I03]: ./media/azure-toolkit-for-intellij-sign-in-instructions/I03.png
[I04]: ./media/azure-toolkit-for-intellij-sign-in-instructions/I04.png

[A01]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A01.png
[A02]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A02.png
[A03]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A03.png
[A04]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A04.png
[A05]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A05.png
[A06]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A06.png
[A07]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A07.png
[A08]: ./media/azure-toolkit-for-intellij-sign-in-instructions/A08.png

[L01]: ./media/azure-toolkit-for-intellij-sign-in-instructions/L01.png
[L02]: ./media/azure-toolkit-for-intellij-sign-in-instructions/L02.png
[L03]: ./media/azure-toolkit-for-intellij-sign-in-instructions/L03.png

