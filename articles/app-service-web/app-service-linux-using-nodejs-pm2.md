---
title: "Использование конфигурации PM2 для Node.js в веб-приложении Azure на платформе Linux | Документация Майкрософт"
description: "Использование конфигурации PM2 для Node.js в веб-приложении Azure на платформе Linux."
keywords: "служба приложений azure, веб-приложение, nodejs, pm2, linux, oss"
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: fb420f32-6d74-49c7-992f-0ed5616e66e7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7ad48d42f8cc847ece199a2372c20430c4c8424e
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="use-pm2-configuration-for-nodejs-in-azure-web-app-on-linux"></a>Использование конфигурации PM2 для Node.js в веб-приложении Azure на платформе Linux

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


При использовании стека приложений Node.js для веб-приложения Azure на платформе Linux вы можете задать загрузочный файл Node.js, как показано на следующем рисунке.

![Указание загрузочного файла Node.js][1]

Это позволяет выполнить следующие задачи:

* определить скрипт запуска приложения Node.js (например, /bin/server.js);
* определить файл конфигурации PM2, который будет использоваться для приложения Node.js (например, /foo/process.json).
  
  > [!NOTE]
  > Если вы хотите настроить автоматический перезапуск процессов Node.js после изменения определенных файлов, задайте конфигурацию PM2. В противном случае приложение не будет перезапускаться после получения уведомлений об изменениях (например, после изменения кода приложения).
  > 
  > 

Все доступные варианты можно просмотреть в [документации по файлу процесса](http://pm2.keymetrics.io/docs/usage/application-declaration/) Node.js, но ниже приведен пример файла process.json.

        {
          "name"        : "worker",
          "script"      : "/bin/server.js",
          "instances"   : 1,
          "merge_logs"  : true,
          "log_date_format" : "YYYY-MM-DD HH:mm Z",
          "watch": ["/bin/server.js", "foo.txt"],
          "watch_options": {
            "followSymlinks": true,
            "usePolling"   : true,
            "interval"    : 5
          }
        }

В этой конфигурации следует обратить внимание на следующее:

* Свойство script указывает скрипт запуска приложения.
* Свойство instances указывает, сколько экземпляров процесса node нужно запустить. Если приложение выполняется на более крупных виртуальных машинах с несколькими ядрами, мы рекомендуем использовать максимальное количество ресурсов, указав более высокое значение для этого параметра.
* Массив watch указывает все файлы, после изменения которых нужно перезапустить процесс node.
* В настоящее время из-за способа подключения содержимого приложения для параметра usePolling свойства watch_options нужно указать значение true.

## <a name="next-steps"></a>Дальнейшие действия
* [Что такое веб-приложение Azure на платформе Linux?](app-service-linux-intro.md)
* [Вопросы и ответы о веб-приложении службы приложений Azure на платформе Linux](app-service-linux-faq.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-nodejs-pm2/nodejs-startup-file.png

