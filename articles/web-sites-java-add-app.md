<properties urlDisplayName="Add an application to your Java website" pageTitle="Добавление приложения на веб-сайт Java" metaKeywords="" description="В этом учебнике показано, как добавить страницу или приложение на веб-сайт на Java в Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Add an application to your Java website" videoId="" scriptId="" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# Добавление приложения на веб-сайт Java в Azure

После инициализации веб-сайта Java, как описано в документе [Начало работы с веб-сайтами Microsoft Azure и Java](../web-sites-java-get-started) можно загрузить свое приложение, разместив WAR-файл в папке **webapps**.

Путь к папке **webapps** зависит от настроек веб-сайта.

- Если веб-сайт настроен с использованием коллекции приложений Azure, то папка **webapps** находится по следующему пути: **d:\home\site\wwwroot\bin\application\_server\webapps**, где **application\_server** - имя сервера приложения на веб-сайте. 
- После настройки веб-сайта в пользовательском интерфейсе Azure путь к папке **webapps** будет иметь вид **webapps**. 

Обратите внимание, что для отправки веб-страниц или приложения можно использовать систему управления версиями, в том числе в сценариях непрерывной интеграции. Инструкции по использованию системы управления версиями с веб-сайтами см. в разделе [Публикация из системы управления версиями на веб-сайты Azure](../web-sites-publish-source-control). Загрузить приложения или веб-страницы можно также с помощью FTP.

Примечание для веб-сайтов Tomcat. Сразу после загрузки WAR-файла в папку **webapps** сервер приложений Tomcat определит это и автоматически загрузит файл. Обратите внимание, что при копировании файлов (кроме WAR-файлов) в КОРНЕВОЙ каталог необходимо перезапустить сервер приложений, прежде чем использовать эти файлы. Работа функции автозагрузки для запущенных в Azure веб-сайтов Java Tomcat основана на добавлении нового WAR-файла либо новых файлов или каталогов в папку **webapps**. 
