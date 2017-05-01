---
title: "Добавление приложения Java в веб-приложения службы приложений Azure"
description: "В этом учебнике показано, как добавить страницу или приложение в экземпляр веб-приложений службы приложений Azure, который уже настроен на использование Java."
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 9b46528b-e2d0-4f26-b8d7-af94bd8c31ef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: bc23bf745f0e212de8ea652c8e57e4797013e3cc
ms.lasthandoff: 04/06/2017


---
# <a name="add-a-java-application-to-azure-app-service-web-apps"></a>Добавление приложения Java в веб-приложения службы приложений Azure
После инициализации веб-приложения в [службе приложений Azure][Azure App Service], как описано в статье [Создание веб-приложения Java в службе приложений Azure](web-sites-java-get-started.md), приложение можно передать, поместив WAR-файл в папку **webapps**.

Путь к папке **webapps** зависит от настроек экземпляра веб-приложений.

* Если веб-приложение настроено с использованием Azure Marketplace, то папка **webapps** находится по пути **d:\home\site\wwwroot\bin\сервер\_приложений\webapps**, где **сервер\_приложений** — имя сервера приложений в экземпляре веб-приложений. 
* После настройки веб-приложения в пользовательском интерфейсе Azure путь к папке **webapps** будет иметь вид **d:\home\site\wwwroot\webapps**. 

Обратите внимание, что для отправки веб-страниц или приложения можно использовать систему управления версиями, в том числе в [сценариях непрерывной интеграции](app-service-continuous-deployment.md). FTP позволяет также передать приложение или веб-страницы. Дополнительные сведения о развертывании приложений посредством протокола FTP см. в разделе [Развертывание приложения в службе приложений Azure].

Примечание для веб-приложений Tomcat. Сразу после передачи WAR-файла в папку **webapps** сервер приложений Tomcat определит это и автоматически загрузит этот файл. Обратите внимание, что при копировании файлов (кроме WAR-файлов) в КОРНЕВОЙ каталог необходимо перезапустить сервер приложений, прежде чем использовать эти файлы. Работа функции автозагрузки для запущенных в Azure веб-приложений Java Tomcat основана на добавлении нового WAR-файла либо новых файлов или каталогов в папку **webapps**. 

<a name="see-also"></a>

## <a name="see-also"></a>См. также
Дополнительные сведения об использовании Azure с Java можно найти в [Центре разработчиков Java для Azure].

[application-insights-app-insights-java-get-started](../application-insights/app-insights-java-get-started.md)

<!-- URL List -->

[Центре разработчиков Java для Azure]: https://azure.microsoft.com/develop/java/
[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Развертывание приложения в службе приложений Azure]: ./web-sites-deploy.md

