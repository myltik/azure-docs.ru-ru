---
title: "Управление службами Azure Analysis Services | Документация Майкрософт"
description: "Узнайте, как управлять сервером служб Analysis Services в Azure."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 79491d0b-b00d-4e02-9ca7-adc99bc02fdb
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 10/31/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 193c939065979dc48243d31e7f97cd87d96bf9a8
ms.openlocfilehash: 55a016a0943885a3aaa636316808939777afb0f8


---
# <a name="manage-analysis-services"></a>Управление службами Analysis Services
После создания сервера служб Analysis Services в Azure некоторые задачи управления и администрирования необходимо выполнять сразу же, а некоторые — в дальнейшей работе. Например, запускать обработку для обновления данных, управлять доступом к моделям на сервере или отслеживать работоспособность сервера. Некоторые задачи управления можно выполнять только на портале Azure, другие — только в SQL Server Management Studio (SSMS), а некоторые — и там, и там.

## <a name="azure-portal"></a>Портал Azure
На [портале Azure](http://portal.azure.com/) можно создавать и удалять сервера, отслеживать их ресурсы, изменять размер и управлять доступом к серверу.  При возникновении проблем вы также можете отправить запрос на техническую поддержку.

![Получение имени сервера в Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Подключение к серверу в Azure происходит так же, как и подключение к экземпляру сервера в организации. В среде SSMS можно выполнять большое количество задач, таких как обработка данных, создание скрипта обработки, управление ролями и использование PowerShell.

![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

 Разница между порталом и SSMS заключается в методе проверки подлинности при подключении к серверу. Чтобы подключиться к серверу служб Analysis Services Azure, выберите **Проверка подлинности с помощью пароля Active Directory**.

 При использовании SSMS перед подключением к серверу в первый раз убедитесь, что ваше имя пользователя входит в группу "Администраторы служб Analysis Services". Дополнительные сведения см. в разделе [Администраторы сервера](#server-administrators) далее в этой статье.

### <a name="to-connect-with-ssms"></a>Подключение с помощью SSMS
1. Прежде чем подключаться к серверу, необходимо получить его имя. На **портале Azure** выберите сервер, а затем щелкните **Обзор** > **Имя сервера** и скопируйте имя сервера.
   
    ![Получение имени сервера в Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. В среде SSMS выберите **Обозреватель объектов** и щелкните **Подключиться** > **Analysis Services**.
3. В диалоговом окне **Подключение к серверу** вставьте имя сервера, а затем в раскрывающемся списке **Проверка подлинности** выберите один из следующих параметров.
   
    **Встроенная проверка подлинности Active Directory** — единый вход в федерацию Azure Active Directory с помощью Active Directory.
   
    **Проверка подлинности с помощью пароля Active Directory** — для входа используется учетная запись организации. Например, при подключении с компьютера, не присоединенного к домену.
   
    Примечание. Если параметры проверки подлинности на основе Active Directory не отображаются, возможно, в среде SSMS необходимо [включить проверку подлинности Azure Active Directory](#enable-azure-active-directory-authentication).
   
    ![Подключение в среде SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

Поскольку управление вашим сервером в Azure с помощью SSMS выполняется практически так же, как и локальным сервером, мы не будем вдаваться в подробности. Все необходимые сведения см. в статье [Управление экземплярами служб Analysis Services](https://msdn.microsoft.com/library/hh230806.aspx) на сайте MSDN.

## <a name="server-administrators"></a>Администраторы сервера
Управление администраторами сервера на портале Azure или в среде SSMS выполняется с помощью параметра **Администраторы служб Analysis Services** в колонке "Управление". Администраторы служб Analysis Services — это администраторы сервера базы данных с правами на выполнение общих задач администрирования базы данных, таких как добавление и удаление баз данных, а также управление пользователями. По умолчанию пользователь, который создает сервер на портале Azure, автоматически добавляется в список администраторов служб Analysis Services.

Полезная информация:

* службы Azure Analysis Services не поддерживают тип удостоверения Windows Live ID;  
* администраторы служб Analysis Services должны быть действительными пользователями Azure Active Directory;
* при создании сервера служб Azure Analysis Services с помощью шаблонов Azure Resource Manager администраторы служб Analysis Services добавляются из массива JSON пользователей.

В качестве администраторов служб Analysis Services и администраторов ресурсов Azure (управляют ресурсами для подписки Azure) могут использоваться разные пользователи. Это позволяет поддерживать совместимость с управляющим поведением XMLA и TSML в службах Analysis Services, а также распределять обязанности управления ресурсами Azure и базой данных Analysis Services.

Чтобы просмотреть все роли и типы доступа для своих ресурсов служб Azure Analysis Services, используйте параметр "Управление доступом (IAM)" в колонке "Управление".

## <a name="database-users"></a>Пользователи базы данных
Пользователи шаблона базы данных Azure Analysis Services должны быть пользователями вашей службы Azure Active Directory. В качестве имен пользователей, указанных для шаблона базы данных, необходимо использовать адрес электронной почты организации или имя участника-пользователя. Это отличается от локальных шаблонов баз данных, которые поддерживают имена пользователей домена Windows.

Пользователей можно добавлять, используя [назначения ролей в Azure Active Directory](../active-directory/role-based-access-control-configure.md) или язык [Tabular Model Scripting Language](https://msdn.microsoft.com/library/mt614797.aspx) (TMSL) в SQL Server Management Studio.

**Пример скрипта TMSL**

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Users"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@contoso.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="enable-azure-active-directory-authentication"></a>Включение проверки подлинности Azure Active Directory
Чтобы включить компонент проверки подлинности Azure Active Directory для SSMS в реестре, создайте текстовый файл с именем EnableAAD.reg, а затем скопируйте и вставьте в него следующее.

```
Windows Registry Editor Version 5.00
[HKEY_CURRENT_USER\Software\Microsoft\Microsoft SQL Server\Microsoft Analysis Services\Settings]
"AS AAD Enabled"="True"
```

Сохраните и откройте файл.

## <a name="troubleshooting-connection-problems"></a>Устранение неполадок с подключением
Если при подключении к серверу с помощью SSMS (на шаге 3) вы пробуете выполнить вход с помощью учетной записи, не являющейся федеративной или не входящей в Azure Active Directory, и подключиться не удается, то может потребоваться очистить кэш учетных данных. Закройте SSMS, затем выполните следующие действия.

1. В проводнике перейдите в папку `C:\Users\<user_name>\AppData\Local\`.
2. Удалите папку **AADCacheOM**.
3. В папке **Local** ищите DAT-файлы, имя которых начинается с **omlibs-tokens-cache**. Если вы найдете такие файлы, удалите их.
4. Откройте SSMS и повторите действия из раздела [Подключение с помощью SSMS](#to-connect-with-ssms) выше.

## <a name="next-steps"></a>Дальнейшие действия
Если вы все еще не развернули табличную модель нового сервера, сейчас самое время это сделать. Дополнительные сведения см. в статье [Deploy to Azure Analysis Services](analysis-services-deploy.md) (Развертывание в службы Azure Analysis Services).

Если модель для сервера развернута, к ней можно подключиться с помощью клиента или браузера. Дополнительные сведения см. в статье [Получение данных из служб Azure Analysis Services](analysis-services-connect.md).




<!--HONumber=Nov16_HO3-->


