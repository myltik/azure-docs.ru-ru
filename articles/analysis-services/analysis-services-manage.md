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
ms.date: 02/27/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 3992e327bbe887338234fc2d516b053f0750a151
ms.openlocfilehash: fdc4e495fb3fb99022b0f7c487001b2fba42cccc
ms.lasthandoff: 02/16/2017


---
# <a name="manage-analysis-services"></a>Управление службами Analysis Services
После создания сервера служб Analysis Services в Azure некоторые задачи управления и администрирования необходимо выполнять сразу же, а некоторые — в дальнейшей работе. Например, запускать обработку для обновления данных, управлять доступом к моделям на сервере или отслеживать работоспособность сервера. Некоторые задачи управления можно выполнять только на портале Azure, другие — только в SQL Server Management Studio (SSMS), а некоторые — и там, и там.

## <a name="azure-portal"></a>Портал Azure
На [портале Azure](http://portal.azure.com/) можно создавать и удалять сервера, отслеживать их ресурсы, изменять размер и управлять доступом к серверу.  При возникновении проблем вы также можете отправить запрос на техническую поддержку.

![Получение имени сервера в Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Подключение к серверу в Azure происходит так же, как и подключение к экземпляру сервера в организации. В среде SSMS можно выполнять большое количество задач, таких как обработка данных, создание скрипта обработки, управление ролями и использование PowerShell. [Скачайте и установите SSMS](#download-and-install-ssms).
  
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
   
    Примечание. Если параметры проверки подлинности на основе Active Directory не отображаются, то, возможно, требуется выполнить обновление до [последней версии SSMS](#download-and-install-ssms).
   
    ![Подключение в среде SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

Поскольку управление вашим сервером в Azure с помощью SSMS выполняется практически так же, как и локальным сервером, мы не будем вдаваться в подробности. Все необходимые сведения см. в статье [Управление экземплярами служб Analysis Services](https://msdn.microsoft.com/library/hh230806.aspx) на сайте MSDN.

## <a name="server-administrators-and-database-users"></a>Администраторы сервера и пользователи базы данных
В службах Azure Analysis Services существует два типа пользователей, администраторы сервера и пользователи базы данных. Оба типа пользователей должны находиться в Azure Active Directory и иметь настроенный адрес электронной почты организации или имя участника-пользователя. Это отличается от локальных баз данных с табличной моделью, которые поддерживают имена пользователей домена Windows для администраторов сервера и пользователей базы данных. Чтобы узнать больше, ознакомьтесь с разделом [Управление пользователями в службах Azure Analysis Services](analysis-services-manage-users.md).

## <a name="download-and-install-ssms"></a>Скачивание и установка SSMS
[Скачайте SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

На странице скачивания вы увидите два варианта:
 
* Релиз-кандидат. Рекомендуется использовать при только подключении к предварительной версии служб Azure Analysis Services. Этот выпуск получает последние обновления и лучше всего работает с предварительной версией служб Azure Analysis Services.
* Текущий рабочий выпуск. Рекомендуется использовать при подключении и к рабочим серверам, и к предварительной версии служб Azure Analysis Services. При использовании этой версии, возможно, потребуется изменить реестр, чтобы включить аутентификацию Azure Active Directory.

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


