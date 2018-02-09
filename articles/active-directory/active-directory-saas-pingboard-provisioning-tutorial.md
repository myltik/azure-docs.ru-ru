---
title: "Руководство по настройке Pingboard для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как настроить Azure Active Directory для автоматической подготовки и отзыва учетных записей пользователей в Pingboard."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: f74e890cf716cfd4bc7b41fcc4aa244969cafde5
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Руководство по настройке Pingboard для автоматической подготовки учетных записей пользователей

Цель этого руководства — показать, как настроить автоматическую подготовку и отзыв учетных записей пользователей из Azure AD в Pingboard.

## <a name="prerequisites"></a>предварительным требованиям

Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

*   Клиент Azure Active Directory.
*   [учетная запись Pro](https://pingboard.com/pricing) клиента Pingboard; 
*   учетная запись пользователя в Pingboard с разрешениями администратора. 

> [!NOTE] 
> Интеграция подготовки Azure AD зависит от API Pingboard (`https://your_domain.pingboard.com/scim/v2`), доступного для вашей учетной записи.

## <a name="assigning-users-to-pingboard"></a>Назначение пользователей в Pingboard

В Azure Active Directory для определения того, какие пользователи должны получать доступ к выбранным приложениям, используется концепция, называемая "назначение". В контексте автоматической подготовки учетных записей синхронизируются только те пользователи, которые были "назначены" приложению в Azure Active Directory. 

Перед настройкой и включением службы подготовки необходимо решить, какие пользователи в Azure Active Directory представляют пользователей, которым требуется доступ к приложению Pingboard. Когда это решение будет принято, можно будет назначить пользователей приложению Pingboard, следуя указаниям ниже.

[Назначение пользователя корпоративному приложению](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Важные советы по назначению пользователей в Pingboard

*   Для проверки конфигурации подготовки рекомендуется назначить одного пользователя Azure Active Directory в Pingboard. Дополнительные пользователи могут быть назначены позднее.

## <a name="configuring-user-provisioning-to-pingboard"></a>Настройка подготовки учетных записей пользователей в Pingboard 

В этом разделе описывается подключение к API подготовки учетной записи пользователя Azure Active Directory в Pingboard и настройка подготовки службы для создания, обновления и отмены назначения учетных записей пользователей в Pingboard на основе назначения пользователей в Azure Active Directory.

> [!TIP]
> Для Pingboard можно также включить единый вход на базе SAML. Для этого следуйте инструкциям, указанным на [портале Azure](https://portal.azure.com). Единый вход можно настроить независимо от автоматической подготовки, хотя эти две возможности дополняют друг друга.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-active-directory"></a>Чтобы настроить автоматическую подготовку учетных записей пользователей Azure Active Directory в Pingboard, сделайте следующее.

1)  На [портале Azure](https://portal.azure.com) перейдите в раздел **Azure Active Directory > Корпоративные приложения > Все приложения**.

2) Если в Pingboard уже настроен единый вход, найдите свой экземпляр Pingboard с помощью поля поиска. В противном случае щелкните **Добавить** и выполните поиск **Pingboard** в коллекции приложений. Выберите приложение **Pingboard** в результатах поиска и добавьте его в список приложений.

3)  Выберите экземпляр Pingboard, а затем перейдите на вкладку **Подготовка**.

4)  Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

![Подготовка Pingboard](./media/active-directory-saas-pingboard-provisioning-tutorial/pingboardazureprovisioning.png)
    
5) В разделе "Учетные данные администратора" выполните следующие действия:

* В текстовом поле **URL-адрес клиента** введите `https://your_domain.pingboard.com/scim/v2`, заменив your_domain фактическим именем домена.
* Войдите в [Pingboard](https://pingboard.com/) с помощью учетной записи администратора.
* Выберите Add-Ons > Integrations > Azure Active Directory (Надстройки > Интеграция > Azure Active Directory).
* Перейдите на вкладку Configure (Настройка) и выберите **Enable user provisioning from Azure** (Включить подготовку пользователей из Azure).
* Скопируйте содержимое поля **OAuth Bearer Token** (Маркер носителя OAuth) и вставьте его в текстовое поле **Secret Token** (Секретный маркер).

6) На портале Azure щелкните **Проверить подключение**, чтобы убедиться, что Azure Active Directory может подключиться к приложению Pingboard. Если подключение отсутствует, убедитесь, что у учетной записи Pingboard есть права администратора, и повторите **проверку подключения**.

7) В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите указанный ниже флажок.

8) Выберите команду **Сохранить**. 

9) В разделе "Сопоставления" выберите **Synchronize Azure Active Directory Users to Pingboard** (Синхронизировать пользователей Azure Active Directory с Pingboard).

10) В разделе **Сопоставления атрибутов** просмотрите атрибуты пользователей Azure Active Directory, которые будут синхронизированы с Pingboard. Атрибуты, выбранные как свойства **сопоставления**, используются для сопоставления учетных записей пользователей в Pingboard для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения. Дополнительные сведения см. в статье [Настройка сопоставлений атрибутов для подготовки пользователей для приложений SaaS в Azure Active Directory](active-directory-saas-customizing-attribute-mappings.md).

11) Чтобы включить службу подготовки Azure Active Directory для Pingboard, в разделе **Параметры** установите переключатель **Состояние подготовки** в положение **Включено**.

12) Чтобы запустить начальную синхронизацию пользователей, назначенных в Pingboard, нажмите кнопку **Сохранить**.

Начальная операция синхронизации занимает больше времени, чем последующие. Последующие операции синхронизации выполняются примерно каждые 20 минут при условии, что служба запущена. В разделе **Сведения о синхронизации** можно отслеживать ход выполнения и переходить по ссылкам для просмотра отчетов о подготовке, в которых зафиксированы все действия, выполняемые с приложением Pingboard службой подготовки.

Дополнительные сведения о чтении журналов подготовки Azure Active Directory см. в руководстве по [отчетам об автоматической подготовке учетных записей пользователей](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](active-directory-enterprise-apps-manage-provisioning.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Настройка единого входа](active-directory-saas-pingboard-tutorial.md)
