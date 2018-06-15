---
title: Приступая к работе с подключенной службой Key Vault в Visual Studio (проекты ASP.NET Core) | Документация Майкрософт
description: Это руководство поможет вам узнать, как добавить поддержку Key Vault в веб-приложение ASP.NET или ASP.NET Core.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: 64d1a404eac955f47308f01edd56b3d008e250a0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33781783"
---
# <a name="get-started-with-key-vault-connected-service-in-visual-studio-aspnet-core-projects"></a>Приступая к работе с подключенной службой Key Vault в Visual Studio (проекты ASP.NET Core)

> [!div class="op_single_selector"]
> - [Приступая к работе](vs-key-vault-aspnet-core-get-started.md)
> - [Что произошло?](vs-key-vault-aspnet-core-what-happened.md)

В этой статье приведены дополнительные рекомендации, которые потребуются вам после того, как вы добавите хранилище ключей в проект ASP.NET Core, щелкнув команду **Add Connected Services** (Добавить подключенные службы) в Visual Studio. Если служба еще не добавлена в проект, то это можно сделать в любое время, следуя инструкциям в разделе [Добавление хранилища ключей в веб-приложение с помощью функции "Подключенные службы" в Visual Studio](vs-key-vault-add-connected-service.md).

Изменения, которые вносятся в проект при добавлении подключенной службы, описаны в статье [Что произошло с моим проектом ASP.NET Core?](vs-key-vault-aspnet-core-what-happened.md)

## <a name="after-you-connect"></a>После подключения

1. Добавьте секрет в хранилище ключей в Azure. Чтобы перейти на соответствующую страницу на портале, щелкните ссылку "Управлять секретами, хранящимися в этом Key Vault". Если вы закрыли страницу или проект, то можете перейти на эту страницу на [портале Azure](https://portal.azure.com). Для этого выберите **Все службы**, в разделе **Безопасность** выберите **Key Vault**, затем выберите только что созданное хранилище ключей.

   ![Переход на портал](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. В разделе "Key Vault" для созданного вами хранилища ключей выберите **Секреты**, затем выберите **Generate/Import** (Создать и импортировать).

   ![Создание и импорт секрета](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Введите секрет, например MySecret, и присвойте ему любое строковое значение для проверки, затем нажмите кнопку **Создать**.

   ![Создание секрета](media/vs-key-vault-add-connected-service/create-a-secret.jpg)
 
1. (Необязательно.) Введите еще один секрет, но на этот раз поместите его в категорию, присвоив ему имя **Secrets--MySecret**. Эта синтаксическая конструкция задает категорию **Secrets**, которая содержит секрет **MySecret**.
1. Теперь вы можете указывать созданные секреты в проекте Visual Studio, используя следующие выражения в коде.
 
   ```csharp
      config["MySecret"] // Access a secret without a section
      config["Secrets:MySecret"] // Access a secret in a section
      config.GetSection("Secrets")["MySecret"] // Get the configuration section and access a secret in it.
   ```

Поздравляем! Теперь ваше веб-приложение может использовать хранилище ключей для доступа к безопасно хранящимся секретам.

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите группу ресурсов, если она больше не нужна. При этому будут удалены хранилище ключей и связанные ресурсы. Чтобы удалить группу ресурсов на портале, сделайте следующее:

1. В поле поиска в верхней части портала введите имя группы ресурсов. Если в результатах поиска отображается группа ресурсов, используемая в этом кратком руководстве, выберите ее.
2. Выберите **Удалить группу ресурсов**.
3. В поле **Введите имя группы ресурсов:** введите имя группы ресурсов и выберите **Удалить**.

# <a name="next-steps"></a>Дополнительная информация

Узнайте больше о разработке для Key Vault в [руководстве разработчика для Key Vault](key-vault-developers-guide.md).