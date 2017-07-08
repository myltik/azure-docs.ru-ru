---
title: "Развертывание приложения в службе приложений Azure с помощью FTP/S | Документация Майкрософт"
description: "Узнайте, как развернуть приложение в службе приложений Azure с помощью FTP или FTPS."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2016
ms.author: cephalin;dariac
ms.translationtype: Human Translation
ms.sourcegitcommit: 816113f7635a003e22a5172113e5039dbcc1ceac
ms.openlocfilehash: 5dc546849bd02ccf4d02f3e6363a3e2fc3898259
ms.contentlocale: ru-ru
ms.lasthandoff: 07/06/2017


---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Развертывание приложения в службе приложений Azure с помощью FTP или FTPS
В этой статье показано, как с помощью FTP или FTPS развернуть веб-приложение, серверную частью мобильного приложения или приложение API в [службе приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

Конечная точка FTP или FTPS для приложения уже активна. Чтобы обеспечить развертывание через FTP или FTPS, не требуется никаких настроек. 

<a name="step1"></a>
## <a name="step-1-set-deployment-credentials"></a>Шаг 1. Настройка учетных данных развертывания

Для доступа вашего приложения к FTP-серверу необходимо сначала настроить учетные данные развертывания. 

Чтобы задать или сбросить учетные данные развертывания, изучите раздел [Учетные данные развертывания службы приложений Azure](app-service-deployment-credentials.md). В этом руководстве демонстрируется использование учетных данных уровня пользователя.

## <a name="step-2-get-ftp-connection-information"></a>Шаг 2. Получение информации о подключении по FTP

1. Откройте [колонку ресурсов](../azure-resource-manager/resource-group-portal.md#manage-resources) приложения на [портале Azure](https://portal.azure.com).
2. Выберите **Обзор** в левом меню и запишите значения **Пользователь FTP или развертывания**, **Имя узла FTP** и **Имя узла FTPS**. 

    ![Информация о подключении по FTP](./media/web-sites-deploy/FTP-Connection-Info.PNG)

    > [!NOTE]
    > Значение параметра **Пользователь FTP или развертывания** отображается на портале Azure, включая имя приложения, чтобы обеспечить правильный контекст для FTP-сервера.
    > Эти же сведения можно отобразить, выбрав **Свойства** в левом меню. 
    >
    > Кроме того, пароль развертывания никогда не отображается. Если вы забыли пароль развертывания, вернитесь к [шагу 1](#step1) и сбросьте его.
    >
    >

## <a name="step-3-deploy-files-to-azure"></a>Шаг 3. Развертывание файлов в Azure

1. Подключитесь к приложению из клиента FTP ([Visual Studio](https://www.visualstudio.com/vs/community/), [FileZilla](https://filezilla-project.org/download.php?type=client) и т. п.), используя полученные сведения.
3. Скопируйте файлы и соответствующую им структуру каталогов в Azure в каталог [**/site/wwwroot**](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (или в каталог веб-заданий **/site/wwwroot/App_Data/Jobs/**).
4. Перейдите по URL-адресу приложения, чтобы убедиться, что приложение работает правильно. 

> [!NOTE] 
> В отличие от [развертываний на основе Git](app-service-deploy-local-git.md), развертывание по FTP не поддерживает следующие инструменты автоматизации развертывания: 
>
> - восстановление зависимостей (например, в инструментах автоматизации NuGet, NPM, PIP и Composer);
> - компиляция двоичных файлов .NET;
> - создание файла Web.config (вот [пример для Node.js](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps)).
> 
> Необходимо вручную выполнить восстановление, сборку и создание этих необходимых файлов на локальном компьютере, а затем развернуть их вместе с приложением.
>
>

## <a name="next-steps"></a>Дальнейшие действия

Чтобы изучить более сложные сценарии развертывания, ознакомьтесь с [развертыванием в Azure с помощью Git](app-service-deploy-local-git.md). Развертывание в Azure на основе Git обеспечивает систему управления версиями, восстановление пакета, MSBuild и многое другое.

## <a name="more-resources"></a>Дополнительные ресурсы

* [Создание веб-приложения PHP-MySQL и его развертывание с помощью FTP](web-sites-php-mysql-deploy-use-ftp.md).
* [Учетные данные развертывания службы приложений Azure](app-service-deploy-ftp.md)

