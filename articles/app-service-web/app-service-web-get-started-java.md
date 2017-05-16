---
title: "Создание первого веб-приложения Java в Azure за пять минут | Документация Майкрософт"
description: "Узнайте, как просто запускать веб-приложения в службе приложений, развернув пример приложения Java."
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: hero-article
ms.date: 04/17/2017
ms.author: cephalin;robmcm
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 75e51ca45a899c6b6fa123346aa3c5860fd1600d
ms.contentlocale: ru-ru
ms.lasthandoff: 05/08/2017


---
# <a name="create-your-first-java-web-app-in-azure-in-five-minutes"></a>Создание первого веб-приложения Java в Azure за пять минут

[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

Это краткое руководство поможет вам развернуть первое веб-приложение Java в [службе приложений Azure](../app-service/app-service-value-prop-what-is.md) за считаные минуты. Поработав с этим руководством, вы получите простое веб-приложение на платформе Java, работающее в облаке.

![Переход к веб-приложению](./media/app-service-web-get-started-java/browse-web-app-1.png)

## <a name="before-you-begin"></a>Перед началом работы

В этом руководстве показано, как создать и развернуть веб-приложение Java в Azure, используя интегрированную среду разработки Eclipse для разработчиков Java EE. Если у вас еще не установлен Eclipse, его можно загрузить бесплатно по ссылке http://www.eclipse.org/.

Чтобы упростить процесс публикации веб-приложений Java в Azure, используйте инструкции, описанные в руководстве [Набор средств Azure для Eclipse](/azure/azure-toolkit-for-eclipse). Инструкции по установке набора средств Azure см. в статье [Установка набора средств Azure для Eclipse](/azure/azure-toolkit-for-eclipse-installation).

> [!NOTE]
>
> В этом руководстве можно также использовать [IntelliJ IDEA](https://www.jetbrains.com/idea/) от компании JetBrains. Некоторые действия для этой среды разработки могут отличаться, несмотря на то, что имеется [набор средств Azure для IntelliJ](/azure/azure-toolkit-for-intellij), который позволяет упростить процесс публикации в этой среде.
>

Для работы с этим руководством вам потребуется подписка Azure. Если у вас нет подписки Azure, вы можете активировать [преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или зарегистрироваться для получения [бесплатной учетной записи Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-dynamic-web-project-in-eclipse"></a>Создание динамических веб-проектов в Eclipse

В Eclipse откройте меню **File** (Файл), выберите пункт **New** (Создать) и щелкните **Dynamic Web Project** (Динамический веб-проект).

В диалоговом окне **New Dynamic Web Project** (Новый динамический веб-проект) присвойте проекту имя **MyFirstJavaOnAzureWebApp** и нажмите кнопку **Finish** (Готово).
   
![Диалоговое окно динамического веб-проекта](./media/app-service-web-get-started-java/new-dynamic-web-project-dialog-box.png)

> [!NOTE]
>
> Если у вас установлена локальная среда [Apache Tomcat](https://tomcat.apache.org/), укажите это в поле **Целевая среда выполнения**.
>

После создания динамического веб-проекта добавьте новую страницу JSP, развернув проект в обозревателе проектов, щелкните правой кнопкой мыши папку **WebContent**, а затем нажмите кнопку **Создать** и выберите **JSP File** (JSP-файл).

![Новое меню файла JSP](./media/app-service-web-get-started-java/new-jsp-file-menu.png)

В диалоговом окне New JSP File (Создание JSP-файла) укажите имя файла **index.jsp**, оставьте родительскую папку **MyFirstJavaOnAzureWebApp/WebContent** без изменений, а затем нажмите кнопку **Далее**.

![Диалоговое окно New JSP File (Создание JSP-файла)](./media/app-service-web-get-started-java/new-jsp-file-dialog-box-page-1.png)

На следующей странице диалогового окна New JSP File (Создание JSP-файла) укажите имя файла **index.jsp**, оставьте родительскую папку **MyFirstJavaOnAzureWebApp/WebContent** без изменений, а затем нажмите кнопку **Готово**.

![Диалоговое окно New JSP File (Создание JSP-файла)](./media/app-service-web-get-started-java/new-jsp-file-dialog-box-page-2.png)

Когда в Eclipse откроется новая страница, замените существующий раздел `<body></body>` следующим кодом:

```jsp
<body>
<h1><% out.println("Hello Azure!"); %></h1>
</body>
```

Сохраните изменения на странице.

## <a name="publish-your-web-app-to-azure"></a>Публикация веб-приложения в Azure

Чтобы развернуть веб-приложение в Azure, воспользуйтесь преимуществом некоторых функциональных возможностей, предоставляемых набором инструментов Azure для Eclipse.

Чтобы начать процесс публикации, используйте один из следующих методов:

* Щелкните правой кнопкой мыши проект в Eclipse **Обозреватель проектов**, щелкните **Azure**, а затем выберите **Publish as Azure Web App** (Опубликовать как веб-приложение Azure).

   ![Контекстное меню Publish as Azure Web App (Опубликовать как веб-приложение Azure)](./media/app-service-web-get-started-java/publish-as-azure-web-app-context-menu.png)

* Щелкните значок **Опубликовать** на панели инструментов Eclipse, а затем щелкните **Publish as Azure Web App** (Опубликовать как веб-приложение Azure).

   ![Раскрывающееся меню Publish as Azure Web App (Опубликовать как веб-приложение Azure)](./media/app-service-web-get-started-java/publish-as-azure-web-app-drop-down-menu.png)

Если вы еще не вошли в свою учетную запись Azure, появится запрос на вход: Для этого выполните следующие действия.

1. Существует два различных варианта для входа в учетную запись Azure. В этом руководстве выберите **интерактивный**.

   ![Диалоговое окно входа в Azure](./media/app-service-web-get-started-java/azure-signin-dialog-box.png)

1. Введите учетные данные Azure и нажмите кнопку **Войти**.

   ![Диалоговое окно входа в Azure](./media/app-service-web-get-started-java/azure-login-dialog-box.png)

1. Выберите подписку Azure и нажмите кнопку **Выбрать**.

   ![Диалоговое окно входа в Azure](./media/app-service-web-get-started-java/select-azure-subscriptions-dialog-box.png)

> [!NOTE]
>
> Подробные инструкции об **интерактивном** и **автоматическом** входе в систему см. в статье [Azure Sign In Instructions for the Azure Toolkit for Eclipse](https://go.microsoft.com/fwlink/?linkid=846174) (Инструкции для входа в Azure. Набор инструментов Azure для Eclipse).
>

После входа в учетную запись Azure отобразится диалоговое окно **Deploy Web App** (Развертывание веб-приложения). Если это первая публикация веб-приложения в Azure, служб приложений не будет в списке. Если это так или если вы хотите создать службу приложений, создайте ее на следующем этапе. Для этого нажмите кнопку **Создать**.

![Диалоговое окно развертывания веб-приложения](./media/app-service-web-get-started-java/deploy-web-app-dialog-box.png)

Когда отобразится диалоговое окно **Create App Service** (Создание службы приложений), необходимо ввести исходные данные:

* Уникальное имя, которое станет DNS-адресом веб-приложения. Например, **MyJavaWebApp** станет *myjavawebapp.azurewebsites.net*.

* Веб-контейнер, который будет использовать веб-приложение, например **последняя версия Tomcat 8.5**.

* Вашу подписку Azure.

   ![Диалоговое окно Create App Service (Создание службы приложений)](./media/app-service-web-get-started-java/create-app-service-dialog-box.png)

Если у вас есть существующие планы службы приложений или если вы хотите создать план службы, необходимо указать следующие сведения:

* Уникальное имя для нового плана службы. Это имя будет отображаться в будущем при публикации веб-приложений с помощью набора инструментов Azure, а также указано в списке на [портале Azure](https://portal.azure.com) при управлении вашей учетной записью.

* Географическое расположение, где будет создаваться план службы.

* Ценовую категорию плана службы.

   ![Создание плана службы приложений](./media/app-service-web-get-started-java/create-app-service-plan.png)

Щелкните вкладку **Группа ресурсов**. Если у вас нет существующей группы ресурсов или если вы хотите создать ее, необходимо указать уникальное имя новой группы ресурсов. В противном случае выберите существующую группу ресурсов в раскрывающемся меню.

![Создание плана службы приложений](./media/app-service-web-get-started-java/create-app-service-resource-group.png)

Наконец, щелкните вкладку **JDK**. Здесь представлено несколько вариантов, что позволяет разработчикам указывать сторонние или пользовательские наборы Java Developer Kit (JDK). В целях этого руководства выберите **По умолчанию**, а затем нажмите кнопку **Создать**.

![Создание плана службы приложений](./media/app-service-web-get-started-java/create-app-service-specify-jdk.png)

Набор средств Azure приступит к созданию службы приложений и отобразит диалоговое окно хода выполнения во время обработки.

![Индикатор выполнения процесса создания плана службы приложений](./media/app-service-web-get-started-java/create-app-service-progress-bar.png)

После создания службы приложений необходимо выбрать, нужно ли развертывать веб-приложение в корневом каталоге веб-сайта. Например, если у вас есть служба приложений в *wingtiptoys.azurewebsites.net* и вы не выполните развертывание в корневой каталог, тогда веб-приложение **MyFirstJavaOnAzureWebApp** будет развернуто в каталоге *wingtiptoys.azurewebsites.net/MyFirstJavaOnAzureWebApp*.

![Развертывание веб-приложения в корневой каталог](./media/app-service-web-get-started-java/deploy-web-app-to-root.png)

Выполнив предыдущие действия, щелкните **Развернуть** для публикации веб-приложения в Azure.

![Развертывание веб-приложения в Azure](./media/app-service-web-get-started-java/deploy-web-app-to-azure.png)

Поздравляем! Веб-приложение успешно развернуто в Azure. Теперь можно просмотреть веб-приложение на веб-сайте Azure:

![Переход к веб-приложению](./media/app-service-web-get-started-java/browse-web-app-1.png)

## <a name="updating-your-web-app"></a>Обновление веб-приложения

После успешной публикации веб-приложения в Azure его обновление существенно упростится. Далее представлен процесс публикации изменений в веб-приложении.

Сначала измените пример кода JSP из более ранней версии, чтобы заголовок заменился на сегодняшнюю дату:

```jsp
<%@ page
    language="java"
    contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1"
    import="java.text.SimpleDateFormat"
    import="java.util.Date" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<% SimpleDateFormat date = new SimpleDateFormat("yyyy/MM/dd"); %>
<title><% out.println(date.format(new Date())); %></title>
</head>
<body>
<h1><% out.println("Hello Azure!"); %></h1>
</body>
</html>
```

Сохранив изменения, щелкните проект правой кнопкой мыши в **обозревателе проектов** Eclipse, выберите **Azure**, а затем — **Publish as Azure Web App** (Опубликовать как веб-приложение Azure).

![Публикация обновленного веб-приложения](./media/app-service-web-get-started-java/publish-updated-web-app-context-menu.png)

Когда откроется диалоговое окно **Deploy Web App** (Развертывание веб-приложения), появятся предыдущие службы приложений. Чтобы обновить веб-приложение, выделите службу приложений и нажмите кнопку **Развернуть** для публикации изменений.

![Развертывание веб-приложения в Azure](./media/app-service-web-get-started-java/deploy-web-app-to-azure.png)

> [!NOTE]
>
> Во время развертывания веб-приложения в корневую папку службы приложений нужно устанавливать флажок **Deploy to root** (Развернуть в корень) каждый раз при публикации изменений.
>

После публикации изменений можно заметить, что заголовок страницы в браузере изменился на сегодняшнюю дату.

![Переход к веб-приложению](./media/app-service-web-get-started-java/browse-web-app-2.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить веб-приложение, используйте **обозреватель Azure**, входящий в состав набора средств Azure. Если представление **обозревателя Azure** еще не отображается в Eclipse, сделайте следующее:

1. Щелкните **Окно**, **Show View** (Показать представление), а затем выберите **Другие**.

   ![Меню Show View (Показать представление)](./media/app-service-web-get-started-java/show-azure-explorer-view-1.png)

2. Когда откроется диалоговое окно **Show View** (Показать представление), выберите **Azure Explorer** (Обозреватель Azure) и нажмите кнопку **ОК**.

   ![Диалоговое окно Show View (Показать представление)](./media/app-service-web-get-started-java/show-azure-explorer-view-2.png)

Чтобы удалить веб-приложение из обозревателя Azure, разверните узел **Веб-приложения**, затем щелкните веб-приложение правой кнопкой мыши и выберите **Удалить**.

![Удаление веб-приложения](./media/app-service-web-get-started-java/delete-web-app-context-menu.png)

При появлении запроса на удаление веб-приложения нажмите кнопку **ОК**.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о наборах средств Azure для Java IDE см. по следующим ссылкам:

* [Набор средств Azure для Eclipse (в этой статье)](../azure-toolkit-for-eclipse.md)
  * [Новые возможности набора средств Azure для Eclipse](../azure-toolkit-for-eclipse-whats-new.md)
  * [Установка набора средств Azure для Eclipse](../azure-toolkit-for-eclipse-installation.md)
  * [Sign In Instructions for the Azure Toolkit for Eclipse](https://go.microsoft.com/fwlink/?linkid=846174) (Инструкции по входу для набора средств Azure для Eclipse)
* [Набор средств Azure для IntelliJ](../azure-toolkit-for-intellij.md)
  * [Новые возможности набора средств Azure для IntelliJ](../azure-toolkit-for-intellij-whats-new.md)
  * [Установка набора средств Azure для IntelliJ](../azure-toolkit-for-intellij-installation.md)
  * [Sign In Instructions for the Azure Toolkit for IntelliJ](https://go.microsoft.com/fwlink/?linkid=846179) (Инструкции по входу для набора средств Azure для IntelliJ)

Дополнительные сведения об использовании Azure см. в [центре разработчиков Java для Azure](https://azure.microsoft.com/develop/java/) и на странице [инструментов Java для Visual Studio Team Services](https://java.visualstudio.com/).

