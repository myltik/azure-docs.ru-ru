---
title: Добавление поддержки Key Vault в проект ASP.NET с помощью Visual Studio | Документация Майкрософт
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
ms.openlocfilehash: b4fed559b6364149170dc8b1da421c9c3ee1203c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635769"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Добавление хранилища ключей в веб-приложение с помощью функции "Подключенные службы" в Visual Studio

В этом руководстве вы узнаете, как легко добавить все, что нужно, чтобы приступить к использованию Azure Key Vault для управления секретами веб-проектов в Visual Studio при работе с проектом ASP.NET Core или проектом ASP.NET любого типа. С помощью функции "Подключенные службы" в Visual Studio 2017 можно использовать Visual Studio, чтобы автоматически добавлять пакеты NuGet и параметры конфигурации, необходимые для подключения к хранилищу ключей в Azure. 

Подробные сведения об изменениях, вносимых функцией "Подключенные службы" в проект для поддержки Key Vault, можно получить из раздела [Что произошло с моим проектом ASP.NET при добавлении подключенной службы Key Vault в Visual Studio?](vs-key-vault-aspnet-what-happened.md) или [Что произошло с моим проектом ASP.NET Core при добавлении подключенной службы Key Vault в Visual Studio?](vs-key-vault-aspnet-core-what-happened.md)

## <a name="prerequisites"></a>предварительным требованиям

- **Подписка Azure**. Если у вас нет подписки, вы можете зарегистрироваться для использования [бесплатной учетной записи](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2017 версии 15.7** с установленной рабочей нагрузкой **Веб-разработка**. [Скачайте это приложение](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Для ASP.NET (не Core) требуются средства разработки для платформы .NET Framework 4.7.1, которые не устанавливается по умолчанию. Чтобы установить их, запустите Visual Studio Installer, щелкните **Изменить** и выберите **Отдельные компоненты**. Затем справа разверните элемент **ASP.NET и разработка веб-приложений** и выберите **Средства разработки для платформы .NET Framework 4.7.1**.
- Откроется веб-проект ASP.NET 4.7.1 или ASP.NET Core 2.0.

## <a name="add-key-vault-support-to-your-project"></a>Добавление поддержки Key Vault в проект

1. В **обозревателе решений** выберите **Добавить** > **Подключенная служба**.
   Откроется страница "Подключенная служба" с перечнем служб, которые можно добавить в проект.
1. В меню доступных служб выберите **Безопасно храните секреты в Azure Key Vault**.

   ![Выбор пункта "Безопасно храните секреты в Azure Key Vault"](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   Если вы выполнили вход в Visual Studio и с вашей учетной записью связана подписка Azure, отобразится страница с раскрывающимся списком ваших подписок.
1. Выберите подписку, которую вы хотите использовать, а затем создайте новое или выберите существующее хранилище ключей. Можно также щелкнуть ссылку "Изменить", чтобы изменить автоматически созданное имя.

   ![Выберите свою подписку.](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. Введите имя для хранилища ключей.

   ![Переименование хранилища ключей и выбор группы ресурсов](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. Выберите существующую или создайте новую группу ресурсов, которой будет автоматически присвоено уникальное имя.  Если вы хотите создать группу с другим именем, можно использовать [портал Azure](https://portal.azure.com). После этого закройте страницу и перезагрузите ее, чтобы обновить список групп ресурсов.
1. Выберите регион для создания хранилища ключей. Если ваше веб-приложение размещено в Azure, выберите регион, в котором размещается это веб-приложение, чтобы обеспечить оптимальную производительность.
1. Выберите ценовую модель. Дополнительные сведения см. в разделе [Цены на Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
1. Нажмите кнопку "ОК", чтобы принять параметры конфигурации.
1. Щелкните **Добавить**, чтобы создать хранилище ключей. Процесс создания может завершиться ошибкой, если вы выбрали имя, которое уже используется.  В этом случае воспользуйтесь ссылкой **Изменить**, чтобы переименовать хранилище ключей, и повторите попытку.

   ![Добавление подключенной службы в проект](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. Теперь добавьте секрет в хранилище ключей в Azure. Чтобы перейти на соответствующую страницу на портале, щелкните ссылку "Управлять секретами, хранящимися в этом Key Vault". Если вы закрыли страницу или проект, то можете перейти на эту страницу на [портале Azure](https://portal.azure.com). Для этого выберите **Все службы**, в разделе **Безопасность** выберите **Key Vault**, затем выберите только что созданное хранилище ключей.

   ![Переход на портал](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. В разделе "Key Vault" для созданного вами хранилища ключей выберите **Секреты**, затем выберите **Generate/Import** (Создать и импортировать).

   ![Создание и импорт секрета](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Введите секрет, например MySecret, и присвойте ему любое строковое значение для проверки, затем нажмите кнопку **Создать**.

   ![Создание секрета](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (Необязательно.) Введите еще один секрет, но на этот раз поместите его в категорию, присвоив ему имя Secrets--MySecret. Эта синтаксическая конструкция задает категорию Secrets, которая содержит секрет MySecret.
 
Теперь можно обращаться к секретам в коде. Дальнейшие действия отличаются в зависимости от того, используется ASP.NET 4.7.1 или ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core-projects"></a>Обращение к секретам в коде (для проектов ASP.NET Core)

1. Теперь вы можете указывать созданные секреты в проекте ASP.NET Core в Visual Studio, используя следующие выражения в коде.
 
   ```csharp
      config["MySecret"] // Access a secret without a section
      config["Secrets:MySecret"] // Access a secret in a section
      config.GetSection("Secrets")["MySecret"] // Get the configuration section and access a secret in it.
   ```

1. На CSHTML-странице, например About.cshtml, добавьте директиву @inject в начало файла, чтобы настроить переменную для доступа к конфигурации хранилища ключей.

   ```cshtml
      @inject Microsoft.Extensions.Configuration.IConfiguration config
   ```

1. Для проверки можно убедиться в доступности значения секрета, отобразив его на одной из страниц. Используйте @config для ссылки на переменную конфигурации.
 
   ```cshtml
      <p> @config["MySecret"] </p>
      <p> @config.GetSection("Secrets")["MySecret"] </p>
      <p> @config["Secrets:MySecret"] </p>
   ```

1. Выполните сборку веб-приложения и запустите его. Затем перейдите на страницу About.cshtml и просмотрите значение секрета.

## <a name="access-your-secrets-in-code-aspnet-471-projects"></a>Обращение к секретам в коде (для проектов ASP.NET 4.7.1)

1. Измените файл web.config следующим образом. Ключи представляют собой заполнители, которые AzureKeyVault ConfigurationBuilder заменит значениями секретов из хранилища ключей.

   ```xml
     <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="MySecret" value="dummy1"/>
       <add key="Secrets--MySecret" value="dummy2"/>
     </appSettings>
   ```

1. В HomeController в метод контроллера About добавьте приведенные ниже строки для получения секрета и его сохранения во ViewBag.
 
   ```csharp
            var secret = ConfigurationManager.AppSettings["MySecret"];
            var secret2 = ConfigurationManager.AppSettings["Secrets--MySecret"];
            ViewBag.Secret = $"Secret: {secret}";
            ViewBag.Secret2 = $"Secret2: {secret2}";
   ```

1. В представлении About.cshtml добавьте следующую команду, чтобы отобразить значение секрета (только для тестирования).

   ```csharp
      <h3>@ViewBag.Secret</h3>
      <h3>@ViewBag.Secret2</h3>
   ```

Поздравляем! Теперь вы убедились, веб-приложение может использовать хранилище ключей для доступа к безопасно хранящимся секретам.

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите группу ресурсов, если она больше не нужна. При этому будут удалены хранилище ключей и связанные ресурсы. Чтобы удалить группу ресурсов на портале, сделайте следующее:

1. В поле поиска в верхней части портала введите имя группы ресурсов. Если в результатах поиска отображается группа ресурсов, используемая в этом кратком руководстве, выберите ее.
2. Выберите **Удалить группу ресурсов**.
3. В поле **Введите имя группы ресурсов:** введите имя группы ресурсов и выберите **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о разработке для Key Vault в [руководстве разработчика для Key Vault](key-vault-developers-guide.md).