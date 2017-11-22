---
title: "Настройка присоединенных к домену кластеров HDInsight с помощью доменных служб Azure Active Directory — Azure | Документация Майкрософт"
description: "Узнайте, как установить и настроить кластеры HDInsight, присоединенные к домену, с помощью доменных служб Azure Active Directory."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/10/2017
ms.author: saurinsh
ms.openlocfilehash: cf8532334f03f72691fa09e8dbdd02b78072cf38
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2017
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>Настройка присоединенных к домену кластеров HDInsight с помощью доменных служб Azure Active Directory

Присоединенные к домену кластеры обеспечивают функциональные возможности безопасности многопользовательского предприятия в HDInsight. Присоединенные к домену кластеры HDInsight подключаются к доменам Active Directory. Таким образом, пользователи домена могут использовать свои учетные данные домена для аутентификации в кластерах и выполнения заданий по обработке больших объемов данных. 

Есть три способа настройки контроллера домена, к которому может подключиться присоединенный к домену кластер HDInsight:

- доменные службы Azure Active Directory (Azure AD DS);
- локальная служба Active Directory;
- контроллер домена Active Directory на виртуальных машинах Azure IaaS.

Из этой статьи вы узнаете, как настроить кластер HDInsight, присоединенный к домену, с использованием доменных служб Azure Active Directory.

## <a name="create-azure-adds"></a>Создание службы Azure AD DS

Перед созданием кластера HDInsight необходимо создать службу Azure AD DS. Дополнительные сведения о создании службы Azure AD DS см. в статье [Включение доменных служб Azure Active Directory с помощью портала Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Только администраторы клиентов имеют разрешения на создание доменных служб. 

После подготовки доменной службы необходимо создать учетную запись службы в группе **администраторов контроллера домена Azure AD** для создания кластера HDInsight. Учетная запись службы должна принадлежать глобальному администратору в Azure AD.

## <a name="create-a-domain-joined-hdinsight-cluster"></a>Создание присоединенного к домену кластера HDInsight

Следующим шагом является создание кластера HDInsight с использованием AAD DS и учетной записи службы, созданной в предыдущем разделе.

Проще разместить доменную службу Azure AD и кластер HDInsight в одной и той же виртуальной сети Azure. Если они находятся в разных виртуальных сетях, необходимо установить пиринг между этими сетями. Дополнительные сведения см. в статье [Пиринг между виртуальными сетями](../../virtual-network/virtual-network-peering-overview.md).

При создании кластера HDInsight, присоединенного к домену, необходимо указать следующие параметры:

- **Имя домена**. Имя домена, который связан с Azure AD DS. Например, contoso.onmicrosoft.com.
- **Имя пользователя домена**. Учетная запись службы в группе администраторов контроллера домена Azure AD, созданная в предыдущем разделе. Например, hdiadmin@contoso.onmicrosoft.com. Этот пользователь домена является администратором данного кластера HDInsight, присоединенного к домену.
- **Пароль домена**. Пароль учетной записи службы.
- **Подразделение**. Уникальное имя подразделения, которое необходимо использовать с кластером HDInsight. Например: OU=HDInsightOU,DC=contoso,DC=onmicrosohift,DC=com. Если такого подразделения не существует, кластер HDInsight попытается создать его. 
- **LDAPS URL** (URL-адрес LDAPS). Например, ldaps://contoso.onmicrosoft.com:636.
- **Access user group** (Группа пользователей с доступом). Группы безопасности, пользователей которых нужно синхронизировать с кластером. Например, HiveUsers. Чтобы указать несколько групп пользователей, разделяйте их запятой (,).
 
На следующем снимке экрана показаны конфигурации на портале Azure:

![Конфигурация доменных служб Active Directory в присоединенном к домену кластере Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Дальнейшие действия
* Сведения о настройке политик Hive и выполнении запросов Hive для присоединенного к домену кластера HDInsight см. [здесь](apache-domain-joined-run-hive.md).
* Сведения о подключении к присоединенным к домену кластерам HDInsight с использованием SSH см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

