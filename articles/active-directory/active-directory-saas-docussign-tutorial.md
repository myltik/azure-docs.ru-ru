<properties
	pageTitle="Учебник. Интеграция Azure Active Directory с DocuSign | Microsoft Azure"
	description="Узнайте, как настроить единый вход между Azure Active Directory и DocuSign."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="jeedes"/>


# Учебник. Интеграция Azure Active Directory с DocuSign

Цель данного руководства — показать интеграцию Azure и DocuSign. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

- Действующая подписка на Azure
- Тенант в DocuSign



Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. [Включение интеграции приложений для DocuSign](#enabling-the-application-integration-for-docusign) 


2. [Настройка единого входа](#configuring-single-sign-on)


3. [Настройка подготовки учетных записей](#configuring-account-provisioning)


4. [Назначение пользователей](#assigning-users)




<br><br>![Настройка единого входа][0]<br>


 

## Включение интеграции приложений для DocuSign

В этом разделе показано, как включить интеграцию приложений для DocuSign.

### Чтобы включить интеграцию приложений для DocuSign, выполните следующие действия:

1. На классическом портале Azure в области навигации слева щелкните **Active Directory**. <br><br>![Настройка единого входа][1]<br>

2. Из списка Каталог выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в верхнем меню представления каталога щелкните **Приложения**. <br><br>![Настройка единого входа][2]<br>

4. В нижней части страницы нажмите кнопку **Добавить**. <br><br>![Приложения][3]<br>

5. В диалоговом окне «Что необходимо сделать» выберите **Добавить приложение из коллекции**. <br><br>![Настройка единого входа][4]<br>


6. В поле поиска введите **DocuSign**. <br><br>![Настройка единого входа][5]<br>

7. В области результатов выберите **DocuSign** и нажмите кнопку **Завершить**, чтобы добавить приложение. <br><br>![Настройка единого входа][6]<br>




## Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в DocuSign со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.


### Чтобы настроить единый вход, выполните следующие действия.

1. На странице интеграции с приложением **DocuSign** классического портала Azure AD щелкните **Настройка единого входа**, чтобы открыть диалоговое окно "Настройка единого входа". <br><br>![Настройка единого входа][7]<br>

2. На странице **Как пользователи должны входить в DocuSign?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку «Далее». <br><br>![Настройка единого входа][8]<br>

3. На странице **Настройка URL-адреса приложения** в текстовом поле **URL-адрес входа в DocuSign** введите URL-адрес тенанта DocuSign и нажмите кнопку **Далее**. URL-адрес имеет следующий формат: *https://<yourcompanyname>.docusign.net/Member/MemberLogin.aspx?ssoname=<yourSSOInstanceName>* <br><br>![Настройка единого входа][9]<br>


    > [AZURE.TIP] Если вы не знаете URL-адрес приложения для вашего тенанта, обратитесь в DocuSign по адресу SSOSetup@Docusign.com и получите URL-адрес для единого входа, инициированного поставщиком услуг.
 

4. На странице **Настройка единого входа в DocuSign** нажмите кнопку **Загрузить сертификат**, а затем сохраните файл сертификата на локальном компьютере. <br><br>![Настройка единого входа][10]<br>


5. В другом окне веб-браузера войдите на сайт **DocuSign** своей компании с правами администратора.


6. Разверните меню пользователя вверху, щелкните **Предпочтения**, а затем в области навигации слева разверните **Управление учетными записями** и щелкните **Функции**. <br><br>![Настройка единого входа][11]<br>

7. Щелкните **Настройка SAML** и выберите ссылку **Настроить SAML**.



8. В разделе **Настройка SAML 2.0** выполните следующие действия: <br><br>![Настройка единого входа][13]<br>


    а. На странице **Настройка единого входа в DocuSign** классического портала Azure скопируйте значение поля "URL-адрес издателя" и вставьте его в текстовое поле **URL-адрес конечной точки поставщика удостоверений**.

    > [AZURE.IMPORTANT] Если этот параметр конфигурации недоступен, обратитесь к своему менеджеру DocuSign или напишите письмо в службу поддержки единого входа ([SSOSetup@docusign.com](mailto:SSOSetup@docusign.com)).
 
    b. Чтобы передать скачанный сертификат, нажмите кнопку **Обзор**.


    c. Установите флажок **Включить имя, фамилию и адрес электронной почты**.


    d. Щелкните **Сохранить**.


9. На классическом портале Azure выберите **подтверждение конфигурации единого входа** и нажмите кнопку **Далее**. <br><br>![Приложения][14]<br>

10. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. <br><br>![Приложения][15]<br>


 

## Настройка подготовки учетных записей

В этом разделе показано, как включить подготовку учетных записей пользователей Active Directory для DocuSign.

### Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.

1. На странице **интеграции приложений DocuSign** **классического портала Azure** щелкните **Настроить подготовку учетных записей пользователей**, чтобы открыть диалоговое окно "Настроить подготовку учетных записей". <br><br>![Настройка подготовки учетных записей][30]<br>
 

2. Чтобы включить автоматическую подготовку учетных записей пользователей, на странице **Параметры и учетные данные администратора** укажите учетные данные учетной записи DocuSign с достаточными правами и нажмите кнопку **Далее**. <br><br>![Настройка подготовки учетных записей][31]<br>

3. В диалоговом окне **Проверка соединения** нажмите кнопку **Начать проверку** и после успешного завершения проверки щелкните **Далее**. <br><br>![Настройка подготовки учетных записей][32]<br>

3. На странице **Подтверждение** нажмите кнопку **Завершить**.

<br><br>![Настройка подготовки учетных записей][33]<br>
 

## Назначение пользователей

Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которым нужно разрешить работу с приложением, назначив их.

### Чтобы назначить пользователей приложению DocuSign, выполните следующие действия:

1. На **классическом портале Azure** создайте тестовую учетную запись.

2. На странице **Интеграция с приложением DocuSign** щелкните **Назначить пользователей**. <br><br>![Назначение пользователей][40]<br>
 

3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да** для подтверждения назначения.

<br><br>![Назначение пользователей][41]<br>


Подождите 10 минут и убедитесь, что учетная запись синхронизирована с DocuSign.

В качестве первого шага проверки можно проверить состояние подготовки, щелкнув "Панель мониторинга" в разделе D на странице интеграции приложения DocuSign классического портала Azure. <br><br>![Назначение пользователей][42]<br>

Об успешном завершении цикла подготовки пользователя говорит соответствующий статус: <br><br>![Назначение пользователей][43]<br>


Если вы хотите проверить параметры единого входа, откройте панель доступа.

Дополнительные сведения о панели доступа см. в статье «Общие сведения о панели доступа».





## Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_06.png
[11]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_07.png

[13]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_09.png
[14]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_18.png

<!---HONumber=AcomDC_0302_2016-->