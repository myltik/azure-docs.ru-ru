---
title: "Инструкции по входу для набора средств Azure для IntelliJ | Документы Майкрософт"
description: "Узнайте, как войти в Microsoft Azure с помощью набора средств Azure для IntelliJ."
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
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: d1be0432a54ed300bc135345b660b90f3efb3b39
ms.lasthandoff: 04/22/2017


---

# <a name="azure-sign-in-instructions-for-the-azure-toolkit-for-intellij"></a>Инструкции по входу для набора средств Azure для IntelliJ

Набор средств Azure для IntelliJ предоставляет два метода для входа в систему с помощью учетной записи Azure:

  * **Интерактивно** — при использовании этого метода потребуется вводить учетные данные Azure при каждом входе в учетную запись Azure.
  * **Автоматически** — при использовании этого метода создается файл учетных данных, содержащий данные субъекта-службы, после чего этот файл можно использовать для автоматического входа в учетную запись Azure.

В следующих разделах описано использование каждого из методов.

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="signing-into-your-azure-account-interactively"></a>Интерактивный вход в учетную запись Azure

Ниже показано, как войти в Azure, вручную введя учетные данные Azure.

1. Откройте проект с помощью IntelliJ IDEA.

1. Выберите **Сервис**, **Azure** и затем **Azure Sign In** (Вход в Azure).

   ![Меню IntelliJ для входа в систему Azure][I01]

1. При отображении диалогового окна **Вход в Azure** выберите **Интерактивно** и щелкните **Войти**.

   ![Диалоговое окно входа][I02]

1. При отображении диалогового окна **Вход в Azure** введите учетные данные и щелкните **Войти**.

   ![Диалоговое окно входа в Azure][I03]

1. При отображении диалогового окна **Выбор подписок** выберите нужные подписки и нажмите кнопку **ОК**.

   ![Диалоговое окно выбора подписок][I04]

## <a name="signing-out-of-your-azure-account-when-you-signed-in-interactively"></a>Выход из учетной записи Azure после интерактивного входа

После настройки, описанной в предыдущем разделе, вы будете автоматически выходить из своей учетной записи Azure при каждом перезапуске IntelliJ IDEA. Однако если вы хотите выйти из учетной записи Azure без перезапуска IntelliJ IDEA, выполните указанные ниже действия.

1. В IntelliJ IDEA выберите **Сервис**, **Azure** и затем **Azure Sign Out** (Выход из Azure).

   ![Меню IntelliJ для выхода из системы Azure][L01]

1. При отображении диалогового окна **Выход из Azure** нажмите кнопку **Да**.

   ![Диалоговое окно выхода][L02]

## <a name="signing-into-your-azure-account-automatically-and-creating-a-credentials-file-to-use-in-the-future"></a>Автоматический вход в учетную запись Azure и создание файла учетных данных для использования в будущем

Описанные ниже действия помогут вам создать файл учетных данных, содержащий данные субъекта-службы. После выполнения этих шагов Eclipse использует файл учетных данных для автоматического входа в Azure при каждом открытии проекта.

1. Откройте проект с помощью IntelliJ IDEA.

1. Выберите **Сервис**, **Azure** и затем **Azure Sign In** (Вход в Azure).

   ![Меню IntelliJ для входа в систему Azure][A01]

1. При отображении диалогового окна **Вход в Azure** выберите **Автоматически** и щелкните **Создать**.

   ![Диалоговое окно входа][A02]

1. При отображении диалогового окна **Вход в Azure** введите учетные данные и щелкните **Войти**.

   ![Диалоговое окно входа в Azure][A03]

1. При отображении диалогового окна **Create authentication files** (Создание файлов проверки подлинности) выберите нужные подписки, конечный каталог и нажмите кнопку **Начать**.

   ![Диалоговое окно входа в Azure][A04]

1. Отображается диалоговое окно **Service Principal Creatation Status** (Состояние создания субъекта-службы). После успешного создания файлов нажмите кнопку **ОК**.

   ![Диалоговое окно состояния создания субъекта-службы][A05]

1. При отображении диалогового окна **Вход в Azure** нажмите кнопку **Войти**.

   ![Диалоговое окно входа в Azure][A06]

1. При отображении диалогового окна **Выбор подписок** выберите нужные подписки и нажмите кнопку **ОК**.

   ![Диалоговое окно выбора подписок][A07]

## <a name="signing-out-of-your-azure-account-when-you-signed-in-automatically"></a>Выход из учетной записи Azure после автоматического входа

После настройки, описанной в предыдущем разделе, набор средств Azure будет автоматически выполнять выход из вашей учетной записи Azure при каждом перезапуске IntelliJ IDEA. Чтобы выйти из учетной записи Azure и предотвратить автоматический вход набора средств Azure, сделайте следующее.

1. В IntelliJ IDEA выберите **Сервис**, **Azure** и затем **Azure Sign Out** (Выход из Azure).

   ![Меню IntelliJ для выхода из системы Azure][L01]

1. При отображении диалогового окна **Выход из Azure** нажмите кнопку **Да**.

   ![Диалоговое окно выхода][L03]

## <a name="signing-into-your-azure-account-automatically-using-a-credentials-file-which-you-have-already-created"></a>Автоматический вход в учетную запись Azure с помощью созданного файла учетных данных

Если вы выходите из Azure во время использования IntelliJ IDEA, потребуется перенастроить набор средств Azure для Eclipse, чтобы использовать созданный файл учетных данных для автоматического входа в учетную запись Azure. Указанные ниже шаги помогут настроить набор средств Azure для использования существующего файла учетных данных.

1. Откройте проект с помощью IntelliJ IDEA.

1. Выберите **Сервис**, **Azure** и затем **Azure Sign In** (Вход в Azure).

   ![Меню IntelliJ для входа в систему Azure][A01]

1. При отображении диалогового окна **Вход в Azure** выберите **Автоматически** и щелкните **Обзор**.

   ![Диалоговое окно входа][A02]

1. Когда откроется диалоговое окно **Select Authentication File** (Выбор файла проверки подлинности), выберите созданный ранее файл учетных данных ранее и нажмите кнопку **Выбрать**.

   ![Диалоговое окно входа][A08]

1. При отображении диалогового окна **Вход в Azure** нажмите кнопку **Войти**.

   ![Диалоговое окно входа в Azure][A06]

1. При отображении диалогового окна **Выбор подписок** выберите нужные подписки и нажмите кнопку **ОК**.

   ![Диалоговое окно выбора подписок][A07]

## <a name="see-also"></a>См. также
Дополнительные сведения о наборах средств Azure для Java IDE см. по следующим ссылкам:

* [Набор средств Azure для Eclipse]
  * [Новые возможности набора средств Azure для Eclipse]
  * [Установка набора средств Azure для Eclipse]
  * [Инструкции по входу для набора средств Azure для Eclipse]
  * [Создание веб-приложения Hello World для Azure в Eclipse]
* [Набор средств Azure для IntelliJ]
  * [Новые возможности набора средств Azure для IntelliJ]
  * [Установка набора средств Azure для IntelliJ]
  * *Инструкции по входу для набора средств Azure для IntelliJ (эта статья)*
  * [Создание веб-приложения Hello World для Azure в IntelliJ]

Дополнительные сведения об использовании Azure см. в [центре разработчиков Java для Azure] и на странице [инструментов Java для Visual Studio Team Services].

<!-- URL List -->

[Набор средств Azure для Eclipse]: ./azure-toolkit-for-eclipse.md
[Набор средств Azure для IntelliJ]: ./azure-toolkit-for-intellij.md
[Создание веб-приложения Hello World для Azure в Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Создание веб-приложения Hello World для Azure в IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Установка набора средств Azure для Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Установка набора средств Azure для IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Инструкции по входу для набора средств Azure для Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Sign In Instructions for the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
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

