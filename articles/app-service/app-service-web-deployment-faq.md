---
title: Часто задаваемые вопросы о развертывании в веб-приложениях Azure | Документация Майкрософт
description: Ответы на часто задаваемые вопросы о развертывании в веб-приложениях службы приложений Azure.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 54c99cc9dde6a9706c75401dd75b98f8f5de7cec
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34068076"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Часто задаваемые вопросы о развертывании в веб-приложениях Azure

В этой статье приведены ответы на часто задаваемые вопросы о проблемах развертывания в [веб-приложениях службы приложений Azure](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Я только начинаю работу с веб-приложениями службы приложений. Как опубликовать код?

Опубликовать код можно несколькими способами:

*   Развертывание с помощью Visual Studio. В Visual Studio щелкните проект веб-приложения правой кнопкой мыши и выберите **Опубликовать**.
*   Развертывание с помощью FTP-клиента. На портале Azure загрузите профиль публикации веб-приложения, в который требуется развернуть код. Затем добавьте файлы в папку \site\wwwroot, используя те же учетные данные профиля публикации FTP.

Дополнительные сведения см. в статье [Развертывание приложения в службе приложений Azure](app-service-deploy-local-git.md).

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this"></a>При попытке выполнить развертывание из Visual Studio отображается сообщение об ошибке. Как решить эту проблему?

При использовании пакета SDK предыдущей версии может появиться следующее сообщение об ошибке: "Ошибка при развертывании для ресурса "имя_ресурса" в группе ресурсов "группа_ресурсов": MissingRegistrationForLocation. Подписка не зарегистрирована для типа ресурсов "компоненты" в расположении "Центральная часть США". Повторно зарегистрируйтесь для данного поставщика, чтобы получить доступ к этому расположению". 

Чтобы устранить эту ошибку, обновите [пакет SDK до последней версии](https://azure.microsoft.com/downloads/). Если вы видите это сообщение и имеете последнюю версию пакета SDK, отправьте запрос на техническую поддержку.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Как развернуть приложения ASP.NET в службу приложений из Visual Studio?
<a id="deployasp"></a>

Сведения о развертывании веб-приложения ASP.NET в веб-приложение службы приложений с помощью Visual Studio 2017 см. в руководстве [Создание веб-приложения ASP.NET в Azure](app-service-web-get-started-dotnet.md).

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Каковы различные типы учетных данных развертывания?

Служба приложений поддерживает два типа учетных данных для развертывания локальной системы Git и развертывания FTP(S). Дополнительные сведения о настройке учетных данных развертывания см. в статье [Настройка учетных данных развертывания службы приложений Azure](app-service-deployment-credentials.md).

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Какова структура файла или каталога веб-приложения службы приложений?

Сведения о структуре файла приложения службы приложений см. в [этом репозитории GitHub](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Как устранить ошибку FTP 550 "Недостаточно пространства на диске", которая появляется при попытке доступа к файлам по протоколу FTP?

Если вы видите это сообщение об ошибке, скорее всего, вы превысили дисковую квоту плана обслуживания веб-приложения. Возможно, вам потребуется выбрать более высокий уровень служб в зависимости от необходимого дискового пространства. Дополнительные сведения о ценах на планы и ограничениях ресурсов см. на [этой странице](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Как настроить непрерывное развертывание веб-приложения служб приложений?

Непрерывное развертывание можно настроить из нескольких ресурсов, в том числе Visual Studio Team Services, OneDrive, GitHub, Bitbucket, Dropbox и других репозиториев Git. Эти параметры доступны на портале. Дополнительные сведения о настройке непрерывного развертывания см. в [этом полезном руководстве](app-service-continuous-deployment.md).

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Как устранить проблемы с непрерывным развертыванием из GitHub и Bitbucket?

Сведения о проблемах с непрерывным развертыванием из GitHub или Bitbucket см. в статье [Investigating continuous deployment](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment) (Изучение непрерывного развертывания).

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this"></a>Не удается подключиться к сайту по протоколу FTP и опубликовать код. Как решить эту проблему?

Чтобы устранить проблемы FTP-подключения, сделайте следующее:

1. Убедитесь, что вы ввели правильное имя узла и учетные данные. Подробные сведения о различных типах учетных данных и способе их использования см. в статье [Deployment credentials](https://github.com/projectkudu/kudu/wiki/Deployment-credentials) (Учетные данные развертывания).
2. Убедитесь, что FTP-порты не заблокированы брандмауэром. Эти порты должны иметь следующие параметры:
    * Порт подключения для управления FTP: 21
    * Порт подключения к данным FTP: 989, 10001–10300.

## <a name="how-do-i-publish-my-code-to-app-service"></a>Как опубликовать код в службу приложений?

Шаблоны быстрого запуска Azure позволяют развернуть приложение на основе стека развертывания и выбранного метода. Чтобы применить шаблон, перейдите на портал Azure и выберите **Параметры** > **Развертывание приложения**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Почему приложение иногда перезапускается после развертывания в службу приложений?

Дополнительные сведения об условиях, при которых приложение может перезапускаться после развертывания, см. в статье [Deployment vs. runtime issues](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts") (Проблемы развертывания и среды выполнения). Как упомянуто в этой статье, служба приложений развертывает файлы в папку wwwroot. Она никогда напрямую не перезапускает приложение.

## <a name="how-do-i-integrate-visual-studio-team-services-code-with-app-service"></a>Как интегрировать код Visual Studio Team Services с помощью службы приложений?

Реализовать непрерывное развертывание с помощью Visual Studio Team Services можно двумя способами:

*   Используйте проект Git. Подключитесь через службу приложений, используя параметры развертывания этого репозитория.
*   Используйте проект системы управления версиями Team Foundation (TFVC). Разверните код с помощью агента сборки службы приложений.

В обоих случаях непрерывное развертывание кода зависит от имеющихся рабочих процессов разработчика и процедур проверки. Дополнительные сведения вы найдете в следующих статьях: 

*   [Implement continuous deployment of your app to an Azure website](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs) (Реализация непрерывного развертывания приложения на веб-сайте Azure)
*   [Set up a Visual Studio Team Services account so it can deploy to a web app](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App) (Настройка параметров развертывания в веб-приложение для учетной записи Visual Studio Team Services)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Как развернуть приложение в службу приложений по протоколу FTP или FTPS?

Сведения о развертывании веб-приложения в службу приложений по протоколу FTP или FTPS см. в [этой статье](app-service-deploy-ftp.md).
