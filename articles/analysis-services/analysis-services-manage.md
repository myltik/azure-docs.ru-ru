---
title: "Управление службами Azure Analysis Services | Документация Майкрософт"
description: "Узнайте, как управлять сервером служб Analysis Services в Azure."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 79491d0b-b00d-4e02-9ca7-adc99bc02fdb
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: cd452ff46f8a87fd04eaca037b6bc0120ca86e4d
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2017
---
# <a name="manage-analysis-services"></a>Управление службами Analysis Services
После создания сервера служб Analysis Services в Azure некоторые задачи управления и администрирования необходимо выполнять сразу же, а некоторые — в дальнейшей работе. Например, запускать обработку для обновления данных, управлять доступом к моделям на сервере или отслеживать работоспособность сервера. Некоторые задачи управления можно выполнять только на портале Azure, другие — только в SQL Server Management Studio (SSMS), а некоторые — и там, и там.

## <a name="azure-portal"></a>Портал Azure
На [портале Azure](http://portal.azure.com/) можно создавать и удалять серверы, отслеживать их ресурсы, изменять размер и управлять доступом к серверу.  При возникновении проблем вы также можете отправить запрос на техническую поддержку.

![Получение имени сервера в Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Подключение к серверу в Azure происходит так же, как и подключение к экземпляру сервера в организации. В среде SSMS можно выполнять большое количество задач, таких как обработка данных, создание скрипта обработки, управление ролями и использование PowerShell.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Скачивание и установка SSMS
Чтобы получить все новейшие функции и максимальное удобство при подключении к серверу Azure Analysis Services, используйте последнюю версию SSMS. 

[Скачайте SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Подключение с помощью SSMS
 При использовании SSMS перед подключением к серверу в первый раз убедитесь, что ваше имя пользователя входит в группу "Администраторы служб Analysis Services". Дополнительные сведения см. в разделе [Администраторы сервера](#server-administrators) далее в этой статье.

1. Прежде чем подключаться к серверу, необходимо получить его имя. На **портале Azure** выберите сервер, а затем щелкните **Обзор** > **Имя сервера** и скопируйте имя сервера.
   
    ![Получение имени сервера в Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. В среде SSMS выберите **Обозреватель объектов** и щелкните **Подключиться** > **Analysis Services**.
3. В диалоговом окне **Подключение к серверу** вставьте имя сервера, а затем в раскрывающемся списке **Проверка подлинности** выберите один из следующих типов проверки подлинности.
   
    **Проверка подлинности Windows** — для входа используются учетные данные Windows (домен\имя_пользователя и пароль).

    **Проверка подлинности с помощью пароля Active Directory** — для входа используется учетная запись организации. Например, при подключении с компьютера, не присоединенного к домену.

    **Универсальная проверка подлинности Active Directory** — используется [неинтерактивная или многофакторная проверка подлинности](../sql-database/sql-database-ssms-mfa-authentication.md). 
   
    ![Подключение в среде SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Администраторы сервера и пользователи базы данных
В службах Azure Analysis Services существует два типа пользователей, администраторы сервера и пользователи базы данных. Оба типа пользователей должны находиться в Azure Active Directory и иметь настроенный адрес электронной почты организации или имя участника-пользователя. Дополнительные сведения см. в разделе о [проверке подлинности и разрешениях пользователя](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Устранение неполадок с подключением
Если при подключении с помощью SSMS возникают проблемы, может потребоваться очистить кэш входа. На диске ничего не кэшируется. Чтобы очистить кэш, закройте и перезапустите процесс подключения. 

## <a name="next-steps"></a>Дальнейшие действия
Если вы все еще не развернули табличную модель нового сервера, сейчас самое время это сделать. Дополнительные сведения см. в статье [Deploy to Azure Analysis Services](analysis-services-deploy.md) (Развертывание в службы Azure Analysis Services).

Если модель для сервера развернута, к ней можно подключиться с помощью клиента или браузера. Дополнительные сведения см. в статье [Получение данных из служб Azure Analysis Services](analysis-services-connect.md).

