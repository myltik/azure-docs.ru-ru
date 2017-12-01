---
title: "Распространенные вопросы и рекомендации по Azure Databricks | Документация Майкрософт"
description: "Ответы на часто задаваемые вопросы и сведения об устранении неполадок для Azure Databricks."
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: nitinme
ms.openlocfilehash: 6bb542537ec713be272f7e58e0b247763214ef4a
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2017
---
# <a name="azure-databricks-preview-common-questions-and-help"></a>Распространенные вопросы и рекомендации по предварительной версии Azure Databricks

В этой статье перечислены самые популярные вопросы, которые связаны с Azure Databricks. Также здесь описаны наиболее распространенные проблемы, с которыми вы можете столкнуться при использовании Azure Databricks. Дополнительные сведения об Azure Databricks см. в статье [Что такое Azure Databricks?](what-is-azure-databricks.md). 

## <a name="common-questions"></a>Часто задаваемые вопросы

В этом разделе рассматриваются распространенные вопросы, связанные с Azure Databricks.

### <a name="can-i-use-my-own-keys-for-local-encryption"></a>Можно ли использовать собственные ключи для локального шифрования? 
В текущем выпуске использование собственных ключей из Azure Key Vault не поддерживается. 

### <a name="can-i-use-azure-vnets-with-azure-databricks"></a>Можно ли использовать виртуальные сети Azure с Azure Databricks?
При подготовке Azure Databricks создается новая виртуальная сеть. В текущем выпуске вы не можете использовать собственную виртуальную сеть Azure.

### <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>Как открыть Azure Data Lake Store из записной книжки? 

1. Подготовьте субъект-службу в Azure Active Directory и запишите ее ключ.
2. Назначьте необходимые разрешения для этого субъекта-службы в Azure Data Lake Store.
3. Чтобы открыть файл из Azure Data Lake Store, укажите в записной книжке учетные данные субъекта-службы.

Дополнительные сведения см. в статье об [использовании Data Lake Store с Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html#azure-data-lake-store).

## <a name="troubleshooting"></a>Устранение неполадок

В этом разделе описано устранение распространенных проблем с Azure Databricks.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Проблема. Эта подписка не зарегистрирована для использования пространства имен Microsoft.Databricks

**Сообщение об ошибке**

This subscription is not registered to use the namespace ‘Microsoft.Databricks’ (Эта подписка не зарегистрирована для использования пространства имен Microsoft.Databricks). Сведения о регистрации подписок см. на странице https://aka.ms/rps-not-found. (Код: MissingSubscriptionRegistration)

**Решение**

1. Перейдите на [портал Azure](https://portal.azure.com).
2. Щелкните **Подписки**, выберите используемую подписку и щелкните **Поставщики ресурсов**. 
3. В списке поставщиков ресурсов нажмите кнопку **Зарегистрировать** рядом с **Microsoft.Databricks**. Для регистрации поставщика ресурсов требуется роль участника или владельца в подписке.


### <a name="issue-your-account-email-does-not-have-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Проблема. Для вашей учетной записи {адреса электронной почты} не назначена роль владельца или участника в ресурсе рабочей области Databricks на портале Azure.

**Сообщение об ошибке**

"Your account {email} does not have Owner or Contributor role on the Databricks workspace resource in the Azure portal" (Для вашей учетной записи {адрес электронной почты} не назначена роль владельца или участника в ресурсе рабочей области Databricks на портале Azure). Это сообщение об ошибке может отображаться также для гостевых пользователей клиента. Обратитесь к администратору, чтобы он предоставил вам доступ или добавил вас в качестве пользователя непосредственно в рабочей области Databricks. 

**Решение**

Ниже приведено несколько решений этой проблемы.

* Чтобы инициализировать клиент, нужно войти от имени обычного пользователя (не гостевого). Также у этого пользователя должны быть роль участника для ресурса рабочей области Databricks. Чтобы предоставить доступ пользователю, воспользуйтесь вкладкой **Управление доступом (IAM)** в рабочей области Azure Databricks на портале Azure.

* Эта ошибка также может возникнуть, если доменное имя электронной почты назначено нескольким каталогам Active Directory. Чтобы обойти эту проблему, создайте пользователя в каталоге Active Directory, который содержит подписку с рабочей областью Databricks.

    а. На портале Azure перейдите к Azure Active Directory, щелкните **Пользователи и группы** и нажмите кнопку **Добавить пользователя**.

    b. Добавьте пользователя с адресом электронной почты в формате `@<tenant_name>.onmicrosoft.com` вместо @<ваш_домен>. Адрес электронной почты <имя_клиента>.onmicrosoft.com, связанный с Active Directory, можно найти на вкладке **Пользовательские домены** в разделе сведений об Azure Active Directory на портале Azure.
    
    c. Назначьте этому пользователю роль **участника** для ресурса рабочей области Databricks.
    
    d. Войдите на портал Azure с учетными данными нового пользователя и найдите рабочую область Databricks.
    
    д. Запустите рабочую область Databricks от имени этого пользователя.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Проблема. Ваша учетная запись {адрес электронной почты} не зарегистрирована в Databricks 

**Решение**

Если вы не создавали рабочую область самостоятельно, а только добавлены в качестве ее пользователя, обратитесь к тому, кто создал эту рабочую область, и попросите добавить вашу учетную запись через консоль администрирования Azure Databricks. Эта процедура описана в статье [Adding and managing users](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html) (Добавление пользователей и управление ими). Если в созданной вами рабочей области отображается такое сообщение об ошибке, попробуйте еще раз выполнить действие "Инициализация рабочей области" на портале Azure.

### <a name="issue-cloud-provider-launch-failure-publicipcountlimitreached-a-cloud-provider-error-was-encountered-while-setting-up-the-cluster"></a>Проблема. Сбой запуска поставщика облачных служб (PublicIPCountLimitReached): при настройке кластера произошла ошибка поставщика облачных служб.

**Сообщение об ошибке**

"Cloud Provider Launch Failure: A cloud provider error was encountered while setting up the cluster" (Сбой запуска поставщика облачных служб: при настройке кластера произошла ошибка поставщика облачных служб). Дополнительные сведения см. в руководстве по Databricks. Код ошибки Azure: PublicIPCountLimitReached. Сообщение об ошибке Azure: "Cannot create more than 60 public IP addresses for this subscription in this region" (Нельзя создать более 60 общедоступных IP-адресов для этой подписки в этом регионе).

**Решение**

Для кластеров Azure Databricks используется один общедоступный IP-адрес в каждом узле. Если для подписки уже использованы все общедоступные IP-адреса, отправьте нам [запрос на увеличение квоты](https://docs.microsoft.com/en-us/azure/azure-supportability/resource-manager-core-quotas-request). Выберите значение **Квота** для параметра **Тип проблемы** и **Сети ARM** — для параметра **Тип квоты**. Введите в поле **Сведения** запрос на увеличение квоты для общедоступных IP-адресов. Например, если установлен лимит 60, а вам нужно создать кластер на 100 узлов, создайте запрос на увеличение лимита до 160.

### <a name="issue-cloud-provider-launch-failure-missingsubscriptionregistration-a-cloud-provider-error-was-encountered-while-setting-up-the-cluster"></a>Проблема. Сбой запуска поставщика облачных служб (MissingSubscriptionRegistration): при настройке кластера произошла ошибка поставщика облачных служб

**Сообщение об ошибке**

"Cloud Provider Launch Failure: A cloud provider error was encountered while setting up the cluster" (Сбой запуска поставщика облачных служб: при настройке кластера произошла ошибка поставщика облачных служб). Дополнительные сведения см. в руководстве по Databricks.
Код ошибки Azure: MissingSubscriptionRegistration. Сообщение об ошибке Azure: The subscription is not registered to use namespace 'Microsoft.Compute' (Эта подписка не зарегистрирована для использования пространства имен Microsoft.Compute). Сведения о регистрации подписок см. на странице https://aka.ms/rps-not-found.

**Решение**

1. Перейдите на [портал Azure](https://portal.azure.com).
2. Щелкните **Подписки**, выберите используемую подписку и щелкните **Поставщики ресурсов**. 
3. В списке поставщиков ресурсов нажмите кнопку **Зарегистрировать** рядом с **Microsoft.Compute**. Для регистрации поставщика ресурсов требуется роль участника или владельца в подписке.

Более подробные инструкции см. в статье [Поставщики и типы ресурсов](../azure-resource-manager/resource-manager-supported-services.md).

## <a name="next-steps"></a>Дальнейшие действия
Пошаговые инструкции по созданию фабрики данных версии 2 см. в следующих руководствах:

- [Краткое руководство по началу работы с Azure Databricks](quickstart-create-databricks-workspace-portal.md).
- [What is Azure Databricks?](what-is-azure-databricks.md) (Что такое Azure Databricks).

