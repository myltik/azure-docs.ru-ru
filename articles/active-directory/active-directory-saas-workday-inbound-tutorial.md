---
title: Руководство. Настройка Workday для входящей синхронизации | Microsoft Docs
description: Узнайте, как использовать Workday в качестве источника данных удостоверений для Azure Active Directory.
services: active-directory
author: MarkusVi
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/10/2016
ms.author: markvi

---
# <a name="tutorial:-configuring-workday-for-inbound-synchronization"></a>Руководство. Настройка Workday для входящей синхронизации
Цель этого руководства — показать, какие действия необходимо выполнить в Workday и Azure AD для импорта пользователей из Workday в Azure AD. 

Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка Azure AD Premium
* Клиент в Workday

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Workday 
2. Создание пользователя системы интеграции 
3. Создание группы безопасности 
4. Назначение пользователя системы интеграции группе безопасности 
5. Настройка параметров группы безопасности 
6. Активация изменений политики безопасности 
7. Настройка импорта пользователей в Azure AD 

## <a name="enabling-the-application-integration-for-workday"></a>Включение интеграции приложений для Workday
В этом разделе показано, как включить интеграцию приложений для Workday.

### <a name="steps:"></a>Шаги:
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-workday-inbound-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-workday-inbound-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавление приложения](./media/active-directory-saas-workday-inbound-tutorial/IC749321.png "Add application")
5. В поле поиска введите **Workday**.
   
    ![Добавить приложение из коллекции](./media/active-directory-saas-workday-inbound-tutorial/IC701021.png "Add an application from gallerry")
6. В области результатов выберите Workday и нажмите кнопку "Завершить", чтобы добавить приложение.
   
    ![Коллекция приложений](./media/active-directory-saas-workday-inbound-tutorial/IC701022.png "Application gallery")

## <a name="creating-an-integration-system-user"></a>Создание пользователя системы интеграции
### <a name="steps:"></a>Шаги:
1. В области **Workday Workbench** введите в поле поиска "create user", а затем щелкните ссылку **Create Integration System User** (Создать пользователя системы интеграции). 
   
    ![Создать пользователя](./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "Create user")
2. Завершите задачу **Создание пользователя системы интеграции** , указав имя пользователя и пароль для нового пользователя системы интеграции.  Оставьте флажок "Запросить новый пароль при следующем входе" снятым, так как этот пользователь будет осуществлять вход в систему программными средствами. Оставьте значение по умолчанию 0 для времени ожидания сеанса в минутах, что не позволит сеансам пользователя завершаться раньше времени. 
   
    ![Create Integration System User](./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "Create Integration System User")

## <a name="creating-a-security-group"></a>Создание группы безопасности
Для сценария, описанного в данном руководстве, необходимо создать ничем не ограниченную группу безопасности системы интеграции и назначить ей пользователя.

### <a name="steps:"></a>Шаги:
1. Введите в поле поиска текст "создать группу безопасности", а затем щелкните **Создать группу безопасности**. 
   
    ![Создать группу безопасности](./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "CreateSecurity Group")
2. Завершите задачу создания группы безопасности.  Выберите группу безопасности системы интеграции без ограничений в раскрывающемся списке "Тип клиентский группы безопасности", чтобы создать группу безопасности, члены в которую будут добавляться явным образом. 
   
    ![Создать группу безопасности](./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "CreateSecurity Group")

## <a name="assigning-the-integration-system-user-to-the-security-group"></a>Назначение пользователя системы интеграции группе безопасности
### <a name="steps:"></a>Шаги:
1. Введите в поле поиска текст "изменить группу безопасности", а затем щелкните **Изменить группу безопасности**. 
   
    ![Изменить группу безопасности](./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "Edit Security Group")
2. Найдите и выберите новую группу безопасности интеграции по имени. 
   
    ![Изменить группу безопасности](./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "Edit Security Group")
3. Добавьте нового пользователя системы интеграции в новую группу безопасности. 
   
    ![Группа безопасности системы](./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "System Security Group")  

## <a name="configuring-security-group-options"></a>Настройка параметров группы безопасности
На этом этапе вы предоставляете группе безопасности новые разрешения на выполнение операций **Get** и **Put** с объектами, защищенными приведенными ниже политиками безопасности домена.

* External Account Provisioning
* Worker Data: Public Worker Reports
* Worker Data: All Positions
* Worker Data: Current Staffing Information
* Worker Data: Business Title on Worker Profile

### <a name="steps:"></a>Шаги:
1. В поле поиска введите "политики безопасности домена", а затем нажмите ссылку "Политики безопасности домена для функциональной области".  
   
    ![Политики безопасности домена](./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "Domain Security Policies")  
2. Найдите систему и выберите функциональную область **Система** .  Нажмите кнопку **ОК**.  
   
    ![Политики безопасности домена](./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "Domain Security Policies")  
3. В списке политик безопасности для функциональной области системы разверните узел "Администрирование безопасности" и выберите политику безопасности домена External Account Provisioning.  
   
    ![Политики безопасности домена](./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "Domain Security Policies")  
4. Нажмите кнопку **Изменить разрешения**, а затем на странице диалогового окна **Изменение разрешений** добавьте новую группу безопасности в список групп безопасности с разрешениями интеграции **Get** и **Put**. 
   
    ![Изменить разрешение](./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "Edit Permission")  
5. Повторите шаг 1 (см. выше), чтобы вернуться на экран для выбора функциональных областей. Затем введите в строке поиска текст "персонал", выберите функциональную область "Персонал" и нажмите кнопку **ОК**.
   
    ![Политики безопасности домена](./media/active-directory-saas-workday-inbound-tutorial/IC750990.png "Domain Security Policies")  
6. В списке политик безопасности для функциональной области "Персонал" разверните узел Worker Data: Staffing и повторите шаг 4 для каждой из оставшихся политик безопасности:
   
   * Worker Data: Public Worker Reports
   * Worker Data: All Positions
   * Worker Data: Current Staffing Information
   * Worker Data: Business Title on Worker Profile

    ![Политики безопасности домена](./media/active-directory-saas-workday-inbound-tutorial/IC750991.png "Domain Security Policies")  







## <a name="activating-security-policy-changes"></a>Активация изменений политики безопасности
### <a name="steps:"></a>Шаги:
1. Введите "активировать" в поле поиска и затем нажмите ссылку "Активировать ожидающие изменения политики безопасности". 
   
    ![Активировать](./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "Activate") 
2. Начните выполнять задачу активации ожидающих изменений политики безопасности: введите комментарий для проведения аудита и нажмите кнопку **ОК**. 
   
    ![Активировать ожидающие изменения безопасности](./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "Activate Pending Security")   
3. Завершите задачу на следующем экране, установив флажок подтверждения и нажав кнопку **ОК**. 
   
    ![Активировать ожидающие изменения безопасности](./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "Activate Pending Security")  

## <a name="configuring-user-import-in-azure-ad"></a>Настройка импорта пользователей в Azure AD
Цель этого раздела — описать настройку Azure AD для импорта пользователей из Workday.

### <a name="steps:"></a>Шаги:
1. На странице интеграции с приложением **Workday** щелкните **Настроить импорт пользователей**, чтобы открыть диалоговое окно **Настройка подготовки к работе**.
2. На странице **Параметры и учетные данные администратора** выполните приведенные ниже действия, после чего нажмите кнопку **Далее**. 
   
    ![Параметры и учетные данные администратора](./media/active-directory-saas-workday-inbound-tutorial/IC750995.png "Settings and admin credentials")  
   
    а. В текстовое поле "Workday admin user" (Имя пользователя администратора Workday) введите имя пользователя, созданного в разделе "Creating an integration system user" (Создание пользователя системы интеграции).
   
    b. В текстовое поле "Workday admin password" (Пароль администратора Workday) введите пароль пользователя, созданного в разделе "Creating an integration system user" (Создание пользователя системы интеграции).
   
    c. В текстовое поле "Workday tenant URL" (URL-адрес клиента Workday) введите URL-адрес клиента Workday.
3. На странице **Проверить подключение** щелкните **Начать тест**, чтобы подтвердить подключение, а затем нажмите кнопку **Далее**. 
   
    ![Проверить подключение](./media/active-directory-saas-workday-inbound-tutorial/IC750996.png "Test connection")  
4. На странице **Параметры подготовки к работе** нажмите кнопку **Далее**. 
   
    ![Параметры подготовки к работе](./media/active-directory-saas-workday-inbound-tutorial/IC750997.png "Provisioning options")
5. В диалоговом окне **Начать подготовку к работе** нажмите кнопку **Завершить**. 
   
    ![Начать подготовку к работе](./media/active-directory-saas-workday-inbound-tutorial/IC750998.png "Start provisioning")

Теперь можно перейти в раздел **Пользователи** и проверить, выполнен ли импорт пользователя Workday.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--HONumber=Oct16_HO2-->


