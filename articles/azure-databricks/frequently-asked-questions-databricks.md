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
ms.date: 11/15/2017
ms.author: nitinme
ms.openlocfilehash: b6b001087cba5f8550d4fea3e4a2f7c1c865beae
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2017
---
# <a name="azure-databricks-preview-common-questions-and-help"></a>Распространенные вопросы и рекомендации по предварительной версии Azure Databricks

В этой статье перечислены самые популярные вопросы, которые связаны с Azure Databricks. Также здесь описаны наиболее распространенные проблемы, с которыми вы можете столкнуться при использовании Azure Databricks. Дополнительные сведения об Azure Databricks см. в статье [What is Azure Databricks?](what-is-azure-databricks.md) (Что такое Azure Databricks?) 

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

### <a name="issue-your-account-email-does-not-have-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Проблема. Для вашей учетной записи {адреса электронной почты} не назначена роль владельца или участника в ресурсе рабочей области Databricks на портале Azure.

**Сообщение об ошибке**

"Your account {email} does not have Owner or Contributor role on the Databricks workspace resource in the Azure portal" (Для вашей учетной записи {адрес электронной почты} не назначена роль владельца или участника в ресурсе рабочей области Databricks на портале Azure). Это сообщение об ошибке может отображаться также для гостевых пользователей клиента. Обратитесь к администратору, чтобы он предоставил вам доступ или добавил вас в качестве пользователя непосредственно в рабочей области Databricks. 

**Решение**

Чтобы инициализировать клиент, нужно войти от имени обычного пользователя (не гостевого). Также у этого пользователя должны быть роль участника для ресурса рабочей области Databricks. Чтобы предоставить доступ пользователю, воспользуйтесь вкладкой **Управление доступом (IAM)** в рабочей области Azure Databricks на портале Azure.

### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Проблема. Ваша учетная запись {адрес электронной почты} не зарегистрирована в Databricks 

**Решение**

Если вы не создавали рабочую область самостоятельно, а только добавлены в качестве ее пользователя, обратитесь к тому, кто создал эту рабочую область, и попросите добавить вашу учетную запись через консоль администрирования Azure Databricks. Эта процедура описана в статье [Adding and managing users](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html) (Добавление пользователей и управление ими). Если в созданной вами рабочей области отображается такое сообщение об ошибке, попробуйте еще раз выполнить действие "Инициализация рабочей области" на портале Azure.

### <a name="issue-cloud-provider-launch-failure-a-cloud-provider-error-was-encountered-while-setting-up-the-cluster"></a>Проблема. Сбой запуска поставщика облачных служб: при настройке кластера произошла ошибка поставщика облачных служб.

**Сообщение об ошибке**

"Cloud Provider Launch Failure: A cloud provider error was encountered while setting up the cluster" (Сбой запуска поставщика облачных служб: при настройке кластера произошла ошибка поставщика облачных служб). Дополнительные сведения см. в руководстве по Databricks. Код ошибки Azure: PublicIPCountLimitReached. Сообщение об ошибке Azure: "Cannot create more than 60 public IP addresses for this subscription in this region" (Нельзя создать более 60 общедоступных IP-адресов для этой подписки в этом регионе).

**Решение**

Для кластеров Azure Databricks используется один общедоступный IP-адрес в каждом узле. Если для подписки уже использованы все общедоступные IP-адреса, отправьте нам [запрос на увеличение квоты](https://docs.microsoft.com/en-us/azure/azure-supportability/resource-manager-core-quotas-request). Выберите значение **Квота** для параметра **Тип проблемы** и **Networking ARM** (Сети ARM) — для параметра **Тип квоты**. Введите в поле **Сведения** запрос на увеличение квоты для общедоступных IP-адресов (например, если установлен лимит 60, а вам нужно создать кластер на 100 узлов, подайте запрос на увеличение лимита до 160).

## <a name="next-steps"></a>Дальнейшие действия
Пошаговые инструкции по созданию фабрики данных версии 2 см. в следующих руководствах:

- [Краткое руководство по началу работы с Azure Databricks](quickstart-create-databricks-workspace-portal.md).
- [What is Azure Databricks?](what-is-azure-databricks.md) (Что такое Azure Databricks).

