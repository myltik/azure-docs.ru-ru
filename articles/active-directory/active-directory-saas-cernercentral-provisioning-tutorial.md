---
title: "Руководство по настройке Cerner Central для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как настроить Azure Active Directory для автоматической подготовки и отзыва учетных записей пользователей в Cerner Central."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: stevenpo
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: asmalser-msft
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 6ffab1ae12d257510b410a782a724d8d1b8b9272
ms.contentlocale: ru-ru
ms.lasthandoff: 05/31/2017

---

# <a name="tutorial-configuring-cerner-central-for-automatic-user-provisioning"></a>Руководство по настройке Cerner Central для автоматической подготовки пользователей


Цель этого руководства — показать, как настроить автоматическую подготовку и отзыв учетных записей пользователей Azure AD в Cerner Central. 

## <a name="prerequisites"></a>Предварительные требования

Сценарий, описанный в этом учебнике, предполагает, что у вас уже есть:

*   Клиент Azure Active Directory.
*   Клиент Cerner Central. 
*   Учетная запись администратора в Cerner Central. 

> [!NOTE]
> Для интеграции Azure Active Directory с Cerner Central используется протокол [SCIM](http://www.simplecloud.info/).

## <a name="assigning-users-to-cerner-central"></a>Добавление пользователей в Cerner Central

В Azure Active Directory для определения того, какие пользователи должны получать доступ к выбранным приложениям, используется концепция, называемая "назначение". В контексте автоматической подготовки учетной записи синхронизированы будут только те пользователи и группы, которые были "назначены" приложению в Azure AD. 

Перед настройкой и включением службы подготовки необходимо решить, какие пользователи или группы в Azure AD представляют пользователей, которым нужен доступ к Cerner Central. Когда этот вопрос будет решен, этих пользователей можно будет назначить приложению Cerner Central, следуя приведенным ниже указаниям.

[Назначение корпоративному приложению пользователя или группы](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Важные рекомендации по назначению пользователей приложению Cerner Central

*    Рекомендуем сначала назначить приложению Cerner Central одного пользователя Azure AD и с его помощью протестировать конфигурацию подготовки. Дополнительные пользователи и/или группы можно назначить позднее.

*    Назначая пользователя приложению Cerner Central, в диалоговом окне назначения необходимо выбрать роль **Пользователь**. Роль "Доступ по умолчанию" не работает для подготовки.


## <a name="configuring-user-provisioning-to-cerner-central"></a>Настройка подготовки пользователей в Cerner Central

В этом разделе мы подключим Azure AD к API подготовки учетных записей в Cerner Central (через протокол SCIM), а также настроим службу подготовки для создания, изменения и отключения назначенных учетных записей пользователей в Cerner Central, используя пользователей и группы в Azure AD.

> [!TIP]
> Для Cerner Central также можно включить единый вход на основе SAML. Для этого следуйте инструкциям, указанным на [портале Azure(https://portal.azure.com). Единый вход можно настроить независимо от автоматической подготовки, хотя две эти функции дополняют друг друга.


### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Настройка автоматической подготовки учетных записей пользователей Azure AD в Cerner Central


Для подготовки учетных записей пользователей в Cerner Central вам нужно создать системную учетную запись и токен носителя OAuth, которые Azure AD сможет использовать для подключения к конечной точке Cerner SCIM. Также перед развертыванием в производственной среде настоятельно рекомендуем выполнять интеграцию в песочнице Cerner.

1.    Сначала убедитесь, что у пользователей, управляющих интеграцией Cerner и Azure AD, есть учетная запись CernerCare. Она требуется для доступа к документации, необходимой для выполнения инструкций. При необходимости воспользуйтесь указанным ниже URL-адресом, чтобы создавать учетные записи CernerCare в любой применимой среде.

   * Песочница: https://sandboxcernercare.com/accounts/create

   * Производственная среда: https://cernercare.com/accounts/create  

2.    Далее создайте для Azure AD системную учетную запись. Выполните инструкции ниже, чтобы отправить запрос на получение системной учетной записи для песочницы и рабочей среды.

   * Инструкции: https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Песочница: https://sandboxcernercentral.com/system-accounts/

   * Производственная среда: https://cernercentral.com/system-accounts/

3.    Далее создайте токен носителя OAuth для каждой системной учетной записи. Для этого выполните приведенные ниже инструкции.

   * Инструкции: https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Песочница: https://sandboxcernercentral.com/system-accounts/

   * Производственная среда: https://cernercentral.com/system-accounts/

4. Чтобы завершить настройку с помощью Azure AD, необходимо получить идентификатор области для списка пользователей в Cerner. Сведения о получении идентификатора см. по адресу https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIM. 

5. Теперь можно настроить Azure AD для подготовки учетных записей пользователей в Cerner. Войдите на [портал Azure](https://portal.azure.com) и перейдите в раздел **Azure Active Directory > Корпоративные приложения > Все приложения**.

6. Если в Cerner Central уже настроен единый вход, найдите свой экземпляр Cerner Central с помощью поля поиска. В противном случае щелкните **Добавить** и выполните поиск **Cerner Central** в коллекции приложений. Выберите Cerner Central в результатах поиска и добавьте его в свой список приложений.

7.    Выберите экземпляр Cerner Central и перейдите на вкладку **Подготовка**.

8.    Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

   ![Подготовка Cerner Central](./media/active-directory-saas-cernercentral-provisioning-tutorial/Cerner.PNG)

9.  Заполните следующие поля в разделе **Учетные данные администратора**:

   * В поле **URL-адрес клиента** введите URL-адрес в формате, указанном ниже, заменив User-Roster-Realm-ID идентификатором области, полученным на шаге 4.

> https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/Users 

   * В поле **Секретный токен** введите токен носителя OAuth, созданный на шаге 3, и нажмите кнопку **Проверить подключение**.

   * В правом верхнем углу портала должно отобразиться оповещение об успешной проверке подключения.

10. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок ниже.

11. Щелкните **Сохранить**. 

12. В разделе **Сопоставления атрибутов** просмотрите атрибуты пользователей и групп, которые будут синхронизированы из Azure AD в Cerner Central. Обратите внимание, что атрибуты, которые выбраны в качестве свойств **сопоставления**, будут использоваться для сопоставления учетных записей пользователей и групп для операций обновления в Cerner Central. Нажмите кнопку "Сохранить", чтобы подтвердить все изменения.

13. Чтобы включить службу подготовки Azure AD для Cerner Central, в разделе **Параметры** установите переключатель **Состояние подготовки** в положение **Включено**.

14. Щелкните **Сохранить**. 

После этого будет запущена начальная синхронизация всех пользователей и групп, назначенных приложению Cerner Central в разделе "Пользователи и группы". Обратите внимание, что начальная синхронизация будет занимать больше времени, чем последующие операции синхронизации. Последующие операции синхронизации выполняются примерно каждые 20 минут, при условии, что служба запущена. В разделе **Сведения о синхронизации** можно отслеживать ход синхронизации и с помощью ссылок просматривать отчеты по подготовке, в которых описаны все действия, выполняемые службой подготовки в отношении приложения Cerner Central.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Руководство. Интеграция Azure Active Directory с Cerner Central](active-directory-saas-cernercentral-tutorial.md)
* [Управление подготовкой учетных записей пользователей для корпоративных приложений](active-directory-enterprise-apps-manage-provisioning.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Дальнейшие действия
* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting).

