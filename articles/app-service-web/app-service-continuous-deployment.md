---
title: "Непрерывное развертывание в службе приложений Azure | Документация Майкрософт"
description: "Узнайте, как включить непрерывное развертывание в службе приложений Azure."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: dariagrigoriu
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 8f4bfb71e25691b3c3eec51186e533202a0f9db0
ms.lasthandoff: 03/21/2017


---
# <a name="continuous-deployment-to-azure-app-service"></a>Непрерывное развертывание в службе приложений Azure
В этом руководстве описано, как настроить рабочий процесс непрерывного развертывания для приложения [службы приложений Azure] . Служба приложений Azure интегрируется с BitBucket, GitHub и [Visual Studio Team Services (VSTS](https://www.visualstudio.com/team-services/)), что обеспечивает непрерывное развертывание, когда Azure извлекает последние обновления из проекта, опубликованные в одной из этих служб. Непрерывное развертывание очень удобно для проектов, которые часто обновляются несколькими участниками.

Чтобы узнать, как вручную настроить непрерывное развертывание из облачного репозитория, которого нет списке на портале Azure (например, [GitLab](https://gitlab.com/)), см. раздел [Setting up continuous deployment using manual steps](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps) (Настройка непрерывного развертывания вручную).

## <a name="overview"></a>Включение непрерывного развертывания
Чтобы включить непрерывное развертывание, выполните следующие действия.

1. Опубликуйте содержимое приложения в репозиторий, который будет использоваться для непрерывного развертывания.  
    Дополнительные сведения о публикации проекта в этих службах см. в статьях, посвященных [Create a repo (GitHub)], [Create a repo (BitBucket)] и [началу работы с VSTS].
2. В колонке меню приложения на [портале Azure] выберите пункт **Развертывание приложения > Параметры развертывания**. Щелкните **Выбор источника** и укажите источник развертывания.  
   
    ![](./media/app-service-continuous-deployment/cd_options.png)
   
   > [!NOTE]
   > Настроить учетную запись VSTS для развертывания службы приложений можно с помощью этого [руководства](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).
   > 
   > 
3. Пройдите авторизацию.
4. В колонке **Источник развертывания** выберите проект и ветвь. Закончив, нажмите кнопку **OK**.
   
    ![](./media/app-service-continuous-deployment/github_option.png)
   
   > [!NOTE]
   > При включении непрерывного развертывания с использованием GitHub или BitBucket будут отображаться и открытые, и закрытые проекты.
   > 
   > 
   
    Служба приложений создает связь с выбранным репозиторием, извлекает файлы из указанного филиала и поддерживает клон репозитория для приложения службы приложений. При настройке непрерывного развертывания VSTS на портале Azure для интеграции используется [подсистема развертывания Kudu](https://github.com/projectkudu/kudu/wiki) службы приложений. Она автоматизирует задачи сборки и развертывания при каждом вызове `git push`. Настраивать отдельно непрерывное развертывание в VSTS не нужно. Когда этот процесс завершится, в колонке **Параметры развертывания** для приложения отобразится активное развертывание.
5. Чтобы проверить развертывание приложения, щелкните **URL-адрес** в верхней части колонки приложения на портале Azure.
6. Чтобы убедиться, что непрерывное развертывание выполняется из выбранного репозитория, отправьте в этот репозиторий изменения. Приложение должно обновиться в соответствии с этими изменениями вскоре после их отправки в репозиторий. Проверить применение в приложении соответствующих изменений можно в колонке **Параметры развертывания** для этого приложения.

## <a name="VSsolution"></a>Непрерывное развертывание решения Visual Studio
Передача решения Visual Studio в службу приложений Azure не сложнее передачи простого файла index.html. Процесс развертывания службы приложений упрощает выполнение всех операций, включая восстановление зависимостей NuGet и создание двоичных файлов приложения. Вы можете воспользоваться практическими рекомендациями по управлению версиями только в репозитории Git, а всем остальным займется процесс развертывания службы приложений Azure.

Операции по передаче решения Visual Studio в службу приложений будут такими же, как в [предыдущем разделе](#overview), если вы настроите решение и репозиторий следующим образом.

* С помощью функции управления версиями в Visual Studio создайте файл `.gitignore`, как на рисунке ниже. Или вручную добавьте в корневую папку репозитория файл `.gitignore` с тем же содержимым, что и в [примере GITIGNORE-файла](https://github.com/github/gitignore/blob/master/VisualStudio.gitignore).
  
  ![](./media/app-service-continuous-deployment/VS_source_control.png)
* Добавьте в репозиторий все дерево каталогов решения с файлом .sln в корневой папке репозитория.

Настроив, как указано, репозиторий и задав непрерывную публикацию приложения Azure из одного из сетевых репозиториев Git, можно разрабатывать приложение ASP.NET локально в Visual Studio и непрерывно разворачивать свой код путем простой отправки изменений в сетевой репозиторий Git.

## <a name="disableCD"></a>Отключение непрерывного развертывания
Чтобы отключить непрерывное развертывание, выполните следующие действия.

1. В колонке меню приложения на [портале Azure] выберите пункт **Развертывание приложения > Параметры развертывания**. В колонке **Параметры развертывания** щелкните **Отключить**.
   
    ![](./media/app-service-continuous-deployment/cd_disconnect.png)
2. Ответив **Да** на запрос подтверждения, можно вернуться к колонке приложения, чтобы выбрать **Развертывание приложения > Параметры развертывания**, если вам нужно настроить публикацию из другого источника.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Изучение распространенных проблем с непрерывным развертыванием](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Использование PowerShell для Azure]
* [Средства командной строки Azure для Mac и Linux]
* [Документация по Git]
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)
* [Использование Azure для автоматического создания конвейера CI/CD для развертывания приложения ASP.NET 4](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)

> [!NOTE]
> Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](https://azure.microsoft.com/try/app-service/), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.
> 
> 

[службы приложений Azure]: https://azure.microsoft.com/en-us/documentation/articles/app-service-changes-existing-services/
[портале Azure]: https://portal.azure.com
[VSTS Portal]: https://www.visualstudio.com/en-us/products/visual-studio-team-services-vs.aspx
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Использование PowerShell для Azure]: /powershell/azureps-cmdlets-docs
[Средства командной строки Azure для Mac и Linux]:../cli-install-nodejs.md
[Документация по Git]: http://git-scm.com/documentation

[Create a repo (GitHub)]: https://help.github.com/articles/create-a-repo (Создание репозитория GitHub)
[Create a repo (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo (Создание репозитория BitBucket)
[началу работы с VSTS]: https://www.visualstudio.com/docs/vsts-tfs-overview (Приступая к работе с VSTS)
[Continuous delivery to Azure using Visual Studio Team Services]: ../articles/cloud-services/cloud-services-continuous-delivery-use-vso.md

