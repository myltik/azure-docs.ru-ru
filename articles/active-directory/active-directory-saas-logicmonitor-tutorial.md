---
title: "Учебник. Интеграция Azure Active Directory с LogicMonitor | Документация Майкрософт"
description: "Узнайте, как использовать LogicMonitor вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 496156c3-0e22-4492-b36f-2c29c055e087
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d47cb9ae48034c73ccb20bd1872a6b50eebb27d7
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Руководство. Интеграция Azure Active Directory с LogicMonitor
Цель данного учебника — показать интеграцию Azure и LogicMonitor.  

Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент LogicMonitor

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для LogicMonitor
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-logicmonitor-tutorial/IC790045.png "Сценарий")

## <a name="enable-the-application-integration-for-logicmonitor"></a>Включение интеграции приложений для LogicMonitor
В этом разделе показано, как включить интеграцию приложений для LogicMonitor.

**Чтобы включить интеграцию с приложением LogicMonitor, выполните следующее.**

1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-logicmonitor-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-logicmonitor-tutorial/IC700994.png "Приложения")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавление приложения](./media/active-directory-saas-logicmonitor-tutorial/IC749321.png "Добавление приложения")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавление приложения из коллекции](./media/active-directory-saas-logicmonitor-tutorial/IC749322.png "Добавление приложения из коллекции")
6. В **поле поиска** введите **logicmonitor**.
   
   ![Коллекция приложений](./media/active-directory-saas-logicmonitor-tutorial/IC790046.png "Коллекция приложений")
7. В области результатов выберите **LogicMonitor** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![LogicMonitor](./media/active-directory-saas-logicmonitor-tutorial/IC790047.png "LogicMonitor")
   
## <a name="configure-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в LogicMonitor со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

**Чтобы настроить единый вход, выполните следующие действия:**

1. На странице интеграции с приложением **LogicMonitor** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-logicmonitor-tutorial/IC790048.png "Настройка единого входа")
2. На странице **Как пользователи должны входить в LogicMonitor?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-logicmonitor-tutorial/IC790049.png "Настройка единого входа")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес для входа** введите URL-адрес, используемый для входа в приложение LogicMonitor \(например, *http://компания.logicmonitor.com*\), и нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-logicmonitor-tutorial/IC790050.png "Настройка URL-адреса приложения")
4. На странице **Настройка единого входа в LogicMonitor** нажмите кнопку **Скачать метаданные**, а затем сохраните эти данные на своем компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-logicmonitor-tutorial/IC790051.png "Настройка единого входа")
5. Выполните вход на веб-сайт компании **LogicMonitor** в качестве администратора.
6. В верхнем меню нажмите пункт **Параметры**.
   
   ![Параметры](./media/active-directory-saas-logicmonitor-tutorial/IC790052.png "Параметры")
7. В расположенной слева области навигации нажмите **Единый вход**
   
   ![Единый вход](./media/active-directory-saas-logicmonitor-tutorial/IC790053.png "Единый вход")
8. В разделе **Параметры единого входа** выполните следующие действия.
   
   ![Single Sign-On Settings](./media/active-directory-saas-logicmonitor-tutorial/IC790054.png "Single Sign-On Settings") (Параметры единого входа)
   
   1. Выберите пункт **Включить единый вход**.
   2. Выберите для параметра **Default Role Assignment** (Назначение ролей по умолчанию) значение **readonly** (только для чтения).
   3. Откройте скачанный файл метаданных в Блокноте и вставьте содержимое этого файла в текстовое поле **Метаданные поставщика удостоверений** .
   4. Нажмите кнопку **Сохранить изменения**.
9. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-logicmonitor-tutorial/IC790055.png "Настройка единого входа")
   
## <a name="configure-user-provisioning"></a>Настроить подготовку учетных записей пользователей

Чтобы пользователи AAD могли войти систему, они должны быть подготовлены для приложения LogicMonitor с использованием их имен пользователей Azure Active Directory.

**Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.**

1. Выполните вход на веб-сайт компании LogicMonitor в качестве администратора.
2. В меню вверху щелкните **Settings** (Параметры), а затем выберите **Roles and Users** (Роли и пользователи).
   
   ![Roles and Users](./media/active-directory-saas-logicmonitor-tutorial/IC790056.png "Roles and Users") (Роли и пользователи)
3. Щелкните **Добавить**.
4. В разделе **Добавить учетную запись** выполните следующие действия.
   
   ![Add an account](./media/active-directory-saas-logicmonitor-tutorial/IC790057.png "Add an account") (Добавление учетной записи)
   
   1. В соответствующие текстовые поля введите значения **Username** (Имя пользователя), **Email** (Адрес электронной почты), **Password** (Пароль) и **Retype password** (Проверка пароля) пользователя Azure Active Directory, которого вы хотите подготовить.
   2. Выберите **Roles** (Роли), **View Permissions** (Просмотр разрешений) и **Status** (Состояние).
   3. Нажмите кнопку **Submit**(Отправить).

>[!NOTE]
>Вы можете использовать любые другие средства создания учетной записи пользователя LogicMonitor или API, предоставляемые LogicMonitor для подготовки учетных записей пользователя Azure Active Directory. 
> 

## <a name="assign-users"></a>Назначить пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

**Чтобы назначить пользователей в LogicMonitor, выполните следующее.**

1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **LogicMonitor** нажмите кнопку **Назначить пользователей**.
   
   ![Назначение пользователей](./media/active-directory-saas-logicmonitor-tutorial/IC790058.png "Назначение пользователей")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-logicmonitor-tutorial/IC767830.png "Да")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).


