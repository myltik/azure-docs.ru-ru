---
title: "Добавление Azure Active Directory с помощью подключенных служб в Visual Studio | Документация Майкрософт"
description: "Добавление Azure Active Directory с помощью диалогового окна \"Добавление подключенных служб\" в Visual Studio"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.service: active-directory
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: kraigb
ms.openlocfilehash: a767c93fb271f3aa33d9556c69c511bcac7cb0d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Добавление Azure Active Directory с помощью подключенных служб в Visual Studio
Azure Active Directory (Azure AD) позволяет обеспечить единый вход для веб-приложений ASP.NET MVC или проверку подлинности Active Directory в службах веб-API. Благодаря проверке подлинности Azure Active Directory пользователи смогут подключаться к вашим веб-приложениям, используя свои учетные записи Azure Active Directory. В число преимуществ проверки подлинности Azure Active Directory с веб-API входит усиленная защита данных при использовании API из веб-приложения. С Azure AD вам не придется управлять отдельной системой проверки подлинности с отдельным управлением пользователями и учетными записями.

## <a name="prerequisites"></a>предварительным требованиям
- Учетная запись Azure. Если у вас ее нет, [зарегистрируйтесь для работы с бесплатной пробной версией](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) или [активируйте преимущества для подписчиков Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Подключение к Azure Active Directory с помощью диалогового окна подключенных служб
1. В Visual Studio создайте или откройте проект ASP.NET MVC или проект веб-API ASP.NET.

1. В обозревателе решений щелкните правой кнопкой мыши узел **Подключенные службы** и выберите в контекстном меню пункт **Добавить подключенную службу**.

1. На странице **Подключенные службы** выберите пункт **Проверка подлинности через Azure Active Directory**.
   
    ![Страница "Подключенные службы"](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. На странице **Введение** мастера **настройки проверки подлинности Azure AD** нажмите кнопку **Далее**.
   
    ![Страница "Введение"](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1. На странице **Единый вход** мастера **настройки проверки подлинности Azure AD** выберите домен в раскрывающемся списке **Домен**. Список доменов включает все домены, доступные для учетных записей, которые указаны в диалоговом окне параметров учетной записи. Если вы не нашли искомый домен, введите имя домена, например `mydomain.onmicrosoft.com`, вручную. Вы можете создать новое приложение Azure Active Directory или использовать параметры уже существующего приложения Azure Active Directory. По завершении нажмите кнопку **Далее**.
   
    ![Страница "Единый вход"](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)

1. На странице **Доступ к каталогу** мастера **настройки проверки подлинности Azure AD** установите флажок **Чтение данных каталога**. 
   
    ![Страница "Доступ к каталогу"](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. Нажмите кнопку **Готово**, чтобы добавить код конфигурации и ссылки, необходимые для включения проверки подлинности Azure AD в вашем проекте. Домен Active Directory отобразится на [портале Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. В Visual Studio появится статья [Что произошло](#how-your-project-is-modified), в которой показано, как проект был изменен. Если вы хотите проверить, все ли сработало, откройте один из измененных файлов конфигурации и убедитесь, что в нем присутствуют все параметры, упомянутые в статье. 

## <a name="how-your-project-is-modified"></a>Какие изменения произойдут в проекте
При запуске мастера Visual Studio добавляет в проект Azure Active Directory и соответствующие ссылки. В файлы конфигурации и файлы кода в проекте вносятся изменения, обеспечивающие поддержку Azure AD. Конкретные изменения, производимые Visual Studio, зависят от типа вашего проекта. Подробные сведения о том, как изменяются проекты ASP.NET MVC, см. в статье [Начало работы с Azure Active Directory и подключенными службами Visual Studio (проекты MVC)](http://go.microsoft.com/fwlink/p/?LinkID=513809). Эти же сведения о проектах веб-API см. в статье [Начало работы с Azure Active Directory и подключенными службами Visual Studio (проекты WebApi)](http://go.microsoft.com/fwlink/p/?LinkId=513810).

## <a name="next-steps"></a>Дополнительная информация
* [Форум MSDN по Azure Active Directory](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)
* [Документация по Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Introduction to Windows Azure Active Directory](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx) (Общие сведения о Microsoft Azure Active Directory)

