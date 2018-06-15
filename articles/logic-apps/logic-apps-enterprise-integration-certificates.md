---
title: Защита сообщений B2B с помощью сертификатов в приложениях Azure Logic Apps | Документация Майкрософт
description: Добавление сертификатов для защиты сообщений B2B с помощью пакета интеграции Enterprise
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 7ba76a15792fe40b2a628b030f06930641d197a3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299340"
---
# <a name="secure-b2b-messages-with-certificates"></a>Защита сообщений B2B с помощью сертификатов

Иногда требуется сохранять конфиденциальность связи B2B. Для безопасного обмена сообщениями B2B в приложениях интеграции предприятия, а конкретно в приложениях логики, можно добавить сертификаты в учетную запись интеграции. Сертификаты представляют собой цифровые документы, используемые для аутентификации участников обмена электронными данными.
Сертификаты помогают защищать обмен данными следующими способами:

* шифрование содержимого сообщения;
* цифровая подпись сообщений.  

В приложениях интеграции Enterprise можно использовать следующие сертификаты:

* Открытые сертификаты, которые необходимо приобрести в центре сертификации (ЦС).
* Закрытые сертификаты, которые можно выдать самостоятельно. Эти сертификаты иногда называются самозаверяющими сертификатами.

## <a name="upload-a-public-certificate"></a>Передача общего сертификата

Чтобы использовать *открытый сертификат* в приложениях логики с возможностями "бизнес-бизнес", необходимо сначала передать его в учетную запись интеграции. После определения свойств в создаваемых [соглашениях](logic-apps-enterprise-integration-agreements.md) сертификат можно будет использовать для защиты сообщений "бизнес-бизнес".

1. Войдите на [портале Azure](https://portal.azure.com).

2. В главном меню Azure выберите **Все службы**. Введите в поле поиска "интеграция" и выберите в результатах поиска **Учетные записи интеграции**.

   ![Поиск учетной записи интеграции](media/logic-apps-enterprise-integration-certificates/overview-1.png)  

3. В разделе **Учетные записи интеграции** выберите учетную запись интеграции, в которую нужно добавить сертификат.

   ![Выберите учетную запись интеграции, в которую необходимо добавить сертификат.](media/logic-apps-enterprise-integration-certificates/overview-3.png)  

4. Выберите плитку **Сертификаты**.  

   ![Выбор плитки "Сертификаты"](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

5. В разделе **Сертификаты** выберите **Добавить**.

   ![Кнопка "Добавить"](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

6. В разделе **Добавление сертификата** предоставьте сведения для вашего сертификата.
   
   1. Укажите **имя** сертификата. В качестве типа сертификата выберите **Открытый**.

   2. Справа от поля **Сертификат** выберите значок папки. 
   Найдите и выберите файл сертификата, который требуется отправить. 
   Когда все будет готово, нажмите кнопку **ОК**.

      ![Передача открытого сертификата](media/logic-apps-enterprise-integration-certificates/certificate-3.png)

   Azure отправит сертификат после проверки сделанного выбора.

   ![Просмотр нового сертификата](media/logic-apps-enterprise-integration-certificates/certificate-4.png) 

## <a name="upload-a-private-certificate"></a>Передача закрытого сертификата

Чтобы использовать *закрытый сертификат* в приложениях логики с возможностями "бизнес-бизнес", необходимо сначала передать этот сертификат в учетную запись интеграции. Также потребуется закрытый ключ, который необходимо сначала добавить в [хранилище Azure Key Vault](../key-vault/key-vault-get-started.md). 

После определения свойств в создаваемых [соглашениях](logic-apps-enterprise-integration-agreements.md) сертификат можно будет использовать для защиты сообщений "бизнес-бизнес".

> [!NOTE]
> Для закрытых сертификатов не забудьте добавить соответствующий открытый сертификат, который должен отображаться в настройках отправки и получения [соглашения AS2](logic-apps-enterprise-integration-as2.md) для подписывания и шифрования сообщений.

1. [Добавьте закрытый ключ в хранилище Azure Key Vault](../key-vault/key-vault-get-started.md#add) и укажите **имя ключа**.
   
2. Авторизуйте Azure Logic Apps для выполнения операций в Azure Key Vault. Для предоставления доступа субъекту-службе Logic Apps используйте команду PowerShell [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy), например:

   `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Войдите на [портале Azure](https://portal.azure.com).

4. В главном меню Azure выберите **Все службы**. Введите в поле поиска "интеграция" и выберите в результатах поиска **Учетные записи интеграции**.

   ![Поиск учетной записи интеграции](media/logic-apps-enterprise-integration-certificates/overview-1.png) 

5. В разделе **Учетные записи интеграции** выберите учетную запись интеграции, в которую нужно добавить сертификат.

6. Выберите плитку **Сертификаты**.  

   ![Выбор плитки "Сертификаты"](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

7. В разделе **Сертификаты** выберите **Добавить**.   

   ![Выбор кнопки "Добавить"](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

8. В разделе **Добавление сертификата** предоставьте сведения для вашего сертификата.
   
   1. Укажите **имя** сертификата. В качестве типа сертификата выберите **Закрытый**.

   2. Справа от поля **Сертификат** выберите значок папки. 
   Найдите и выберите файл сертификата, который требуется отправить. 
   Кроме того, выберите **группу ресурсов**, **хранилище ключей** и **имя ключа**. 
   Когда все будет готово, нажмите кнопку **ОК**.

      ![Добавление сертификата](media/logic-apps-enterprise-integration-certificates/privatecertificate-1.png)

   Azure отправит сертификат после проверки сделанного выбора.

   ![Просмотр нового сертификата](media/logic-apps-enterprise-integration-certificates/privatecertificate-2.png)

## <a name="next-steps"></a>Дополнительная информация

* [Создание соглашения "бизнес-бизнес"](logic-apps-enterprise-integration-agreements.md)
