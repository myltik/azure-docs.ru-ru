<properties linkid="develop-java-tutorials-web-site-add-app" urlDisplayName="Add an application to your Java website" pageTitle="Add an application to your Java website" metaKeywords="" description="This tutorial shows you how to add a page or application to your Java website on Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Add an application to your Java website" videoId="" scriptId="" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Добавление приложения на веб-сайт Java в Azure

После инициализации веб-сайта Java, как описано в разделе [Приступая к работе с веб-сайтами Microsoft Azure и Java][Приступая к работе с веб-сайтами Microsoft Azure и Java], можно загрузить свое приложение, поместив WAR-файл в папку **webapps**.

Путь перехода к папке **webapps** зависит от настройки веб-сайта.

-   Если вы настроили веб-сайт с помощью галереи приложений Azure, путь к папке **webapps** имеет вид **d:\\home\\site\\wwwroot\\bin\\application\_server\\webapps**, где **application\_server** — имя сервера приложений, действительного для веб-сайта.
-   Если вы настроили веб-сайт с помощью пользовательского интерфейса настройки Azure, путь к папке **webapps** будет иметь вид **d:\\home\\site\\wwwroot\\webapps**.

Обратите внимание, что для отправки веб-страниц или приложения можно использовать систему управления версиями, в том числе в сценариях непрерывной интеграции. Инструкции по использованию системы управления версиями с веб-сайтами см. в разделе [Публикация из системы управления версиями на веб-сайты Azure][Публикация из системы управления версиями на веб-сайты Azure]. Загружать приложения или веб-страницы можно также с помощью FTP.

Примечание для веб-сайтов Tomcat. Сразу после загрузки WAR-файла в папку **webapps** сервер приложений Tomcat определит это и автоматически загрузит файл. Обратите внимание, что при копировании файлов (кроме WAR-файлов) в КОРНЕВОЙ каталог необходимо перезапустить сервер приложений, прежде чем использовать эти файлы. Работа функции автозагрузки для запущенных в Azure веб-сайтов Java Tomcat основана на добавлении нового WAR-файла либо новых файлов или каталогов в папку **webapps**.

  [Приступая к работе с веб-сайтами Microsoft Azure и Java]: ../web-sites-java-get-started
  [Публикация из системы управления версиями на веб-сайты Azure]: ../web-sites-publish-source-control
