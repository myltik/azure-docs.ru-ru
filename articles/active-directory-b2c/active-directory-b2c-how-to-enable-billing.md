---
title: "Как связать подписку Azure с клиентом Azure AD B2C | Документы Майкрософт"
description: "Пошаговое руководство по настройке выставления счетов для клиента Azure AD B2C в подписке Azure."
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: joroja
translationtype: Human Translation
ms.sourcegitcommit: 0475c0f209cde80177df7dbf23eaf8dd17a44752
ms.openlocfilehash: b5754a08e0683344cc97bdc664ed26ef9a9cf34d


---
# <a name="linking-an-azure-subscription-to-an-azure-b2c-tenant-to-pay-for-usage-charges"></a>Связывание подписки Azure с клиентом Azure B2C для оплаты за использование
> [!IMPORTANT]
> Ожидается в ближайшее время. Эта функция недоступна для всех клиентов B2C.

Счета за текущее использование Azure Active Directory B2C (или Azure AD B2C) выставляются в подписке Azure. Администратор клиента должен явным образом связать созданный клиент Azure AD B2C с подпиской Azure.  Для этого нужно создать ресурс Azure AD "Клиент B2C" в целевой подписке Azure. К одной подписке Azure можно привязать несколько клиентов B2C вместе с другими ресурсами Azure (например, виртуальные машины, хранилища данных, приложения логики).


> [!IMPORTANT]
> Последние сведения о выставлении счетов за использование и ценах на B2C см. на [этой странице](
https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="step-1---create-an-azure-ad-b2c-tenant"></a>Шаг 1. Создание клиента Azure AD B2C

Сначала нужно создать клиент B2C. Пропустите этот шаг, если вы уже создали целевого клиента B2C. [Начало работы с Azure AD B2C](https://azure.microsoft.com/documentation/articles/active-directory-b2c-get-started/)

## <a name="step-2---open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Шаг 2. Открытие портала Azure со страницей подписки Azure в клиенте Azure AD
Перейдите по адресу portal.azure.com. Переключитесь в клиент Azure AD, в котором содержится подписка Azure, которую нужно использовать. Этот клиент Azure AD отличается от клиента B2C. На портале Azure щелкните имя учетной записи в правом верхнем углу панели мониторинга, чтобы выбрать клиента Azure AD. Чтобы продолжить, требуется подписка Azure. [Оформление подписки Azure](https://account.windowsazure.com/signup?showCatalog=True)

![Переход к своему клиенту Azure AD](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="step-3---create-a-b2c-tenant-resource-in-azure-marketplace"></a>Шаг 3. Создание ресурса "Клиент B2C" в Azure Marketplace
Откройте Marketplace, щелкнув соответствующий значок или выбрав зеленый знак плюса в левом верхнем углу панели мониторинга.  Найдите и выберите Azure Active Directory B2C. Нажмите кнопку Создать.
Выбор ![Marketplace](./media/active-directory-b2c-how-to-enable-billing/marketplace.png)

![Поиск AD B2C](./media/active-directory-b2c-how-to-enable-billing/searchb2c.png)

В диалоговом окне создания ресурса Azure AD B2C содержатся следующие параметры.

1. Azure AD B2C Tenant (Клиент Azure AD B2C). Выберите клиент Azure AD B2C из раскрывающегося списка.  Отображаются только допустимые клиенты Azure AD B2C.  Допустимыми являются клиенты B2C, в которых вы являетесь глобальным администратором клиента B2C и которые в настоящее время не связаны с подпиской Azure.

2. Azure AD B2C Resource name (Имя ресурса Azure AD B2C). Для имени автоматически устанавливается значение, соответствующее имени домена клиента B2C.

3. "Подписка". Активная подписка Azure, в которой вы являетесь администратором или соадминистратором.  В одну подписку Azure можно добавить несколько клиентов Azure AD B2C.

4. "Группа ресурсов" и "Расположение группы ресурсов". Эти параметры позволяют создать несколько ресурсов Azure.  Этот выбор не оказывает влияния на расположение, производительность или выставление счетов за клиент B2C.

5. Параметр "Закрепить на панели мониторинга" позволяет быстро перейти к сведениям о выставлении счетов за клиента B2C и параметрам клиента B2C. ![Создание ресурса B2C](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="step-4---manage-your-b2c-tenant-resources-optional"></a>Шаг 4. Управление ресурсами клиента B2C (необязательно)
После завершения развертывания в целевой группе ресурсов и подписке Azure будет создан ресурс "Клиент B2C".  В списке ресурсов Azure отобразится новый ресурс типа "Клиент B2C".

![Создание ресурса B2C](./media/active-directory-b2c-how-to-enable-billing/b2cresourcedashboard.png)

Щелкнув ресурс "Клиент B2C", вы можете сделать следующее.
- Щелкнуть имя подписки, чтобы просмотреть сведения о выставлении счетов. См. раздел "Выставление счетов и использование".
- Щелкнуть Azure AD B2C Settings (Параметры Azure AD B2C), чтобы открыть новую вкладку браузера непосредственно в колонке параметров клиента B2C.
- Отправить запрос на техническую поддержку.
- Переместить ресурс "Клиент B2C" в другую подписку Azure или в другую группу ресурсов.  Плата за использование будет начисляться в подписке Azure, куда перемещен ресурс.

![Параметры ресурсов B2C](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)


## <a name="next-steps"></a>Дальнейшие действия
После того как вы выполните эти шаги для каждого клиента B2C, счета за подписку Azure будут выставляться в соответствии с вашим соглашением Azure Direct или Enterprise.
- Просмотр сведений об использовании и выставлении счетов в выбранной подписке Azure
- Просмотр подробных отчетов о ежедневном использовании с помощью API отчетов об использовании (подлежит уточнению)



<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/        
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/    



<!--HONumber=Dec16_HO4-->


