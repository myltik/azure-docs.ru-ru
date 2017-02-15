---
title: "Как применять пользовательские образы Docker в службе приложений Azure на платформе Linux | Документация Майкрософт"
description: "Как применять пользовательские образы Docker в службе приложений на платформе Linux"
keywords: "служба приложений azure, веб-приложение, docker, контейнер"
services: app-service
documentationcenter: 
author: naziml
manager: wpickett
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: naziml
translationtype: Human Translation
ms.sourcegitcommit: 54b38c7f8cf685387ac639653d208a00cefbc3fa
ms.openlocfilehash: f2a03c58ab09ef09ea2fc482a978bf0d9c23f212


---

# <a name="using-a-custom-docker-image-for-app-service-on-linux"></a>Применение пользовательских образов Docker в службе приложений на платформе Linux #

Служба приложений включает предопределенные стеки приложений на платформе Linux с поддержкой определенных версий, включая PHP 7.0 и Node.js 4.5. Для размещения этих готовых стеков служба приложений на платформе Linux использует контейнеры Docker. Вы также можете использовать пользовательский образ Docker для развертывания веб-приложения в стек приложений, который еще не определен в Azure. Пользовательские образы Docker могут размещаться как в открытых, так и закрытых репозиториях Docker.


## <a name="how-to-set-a-custom-docker-image-for-a-new-web-app"></a>Практическое руководство. Определение пользовательского образа Docker для нового веб-приложения
Вы можете применять пользовательские образы Docker как для новых, так и для существующих веб-приложений. Создавая веб-приложение на платформе Linux на [портале Azure](https://portal.azure.com), нажмите кнопку **Настроить контейнер**, чтобы определить пользовательский образ Docker:

![Определение пользовательского образа Docker для нового веб-приложения на платформе Linux][1]


## <a name="how-to-use-a-custom-docker-image-from-docker-hub"></a>Практическое руководство. Применение пользовательского образа Docker из Docker Hub ##
Чтобы применить пользовательский образ Docker из Docker Hub, сделайте следующее.

1. На [портале Azure](https://portal.azure.com) найдите веб-приложение на платформе Linux, а затем в разделе **Параметры** щелкните **Контейнер Docker**.

2.  Выберите **Docker Hub** как **источник изображения** и укажите его тип (**открытый** или **закрытый**), а затем укажите **образ и дополнительный тег**, например `node:4.5`. **Команда startup** задается автоматически на основе содержимого, определенного в файле образа Docker, но вы также можете задать собственные команды.  

    ![Настройка образа из открытого репозитория Docker Hub][2]

    Если вы используете образ из закрытого репозитория Docker Hub, вам нужно ввести учетные данные Docker Hub (**имя пользователя для входа** и **пароль**).

    ![Настройка образа из закрытого репозитория Docker Hub][3]

3. Настроив контейнер, нажмите кнопку **Сохранить**.

## <a name="how-to-use-a-docker-image-from-a-private-image-registry"></a>Как использовать образ Docker из закрытого реестра образов ##
Чтобы использовать образ Docker из закрытого реестра образов, сделайте следующее.

1. На [портале Azure](https://portal.azure.com) найдите веб-приложение на платформе Linux, а затем в разделе **Параметры** щелкните **Контейнер Docker**.

2.  Выберите **Закрытый реестр** как **источник изображения**, а затем укажите **образ и дополнительный тег**, **URL-адрес сервера** закрытого реестра, а также учетные данные (**имя пользователя для входа** и **пароль**), после чего нажмите кнопку **Сохранить**.

    ![Настройка образа Docker из закрытого реестра][4]


## <a name="how-to-set-the-port-used-by-your-docker-image"></a>Практическое руководство. Определение порта, который используется образом Docker ##

Если вы применяете для веб-приложения пользовательский образ Docker, вы можете включить переменную среды `PORT` в файл Docker, который будет добавлен в созданный контейнер. Рассмотрим следующий пример файла Docker для приложения Ruby.

    FROM ruby:2.2.0
    RUN mkdir /app
    WORKDIR /app
    ADD . /app
    RUN bundle install
    CMD bundle exec puma config.ru -p $PORT -e production

В последней строке команды видно, что переменная среды PORT передается во время выполнения. Помните, что в командах учитывается регистр.

Если используется существующий образ Docker, созданный другим пользователем, возможно, вам понадобится указать для приложения порт, отличный от порта 80. 

Это можно сделать, добавив параметр приложения `PORT` вместе со значением, которое требуется для образа:

![Настройка параметра приложения PORT для пользовательского образа Docker][6]


## <a name="how-to-switch-back-to-using-a-built-in-image"></a>Практическое руководство. Переход на использование встроенного образа ##

Чтобы перейти от применения пользовательского образа к встроенному, сделайте следующее.

1. На [портале Azure](https://portal.azure.com) найдите веб-приложение на платформе Linux, а затем в разделе **Параметры** щелкните **Служба приложений**.

2. Выберите свой **стек среды выполнения**, чтобы применить встроенный образ, и нажмите кнопку **Сохранить**. 

![Настройка встроенного образа Docker][5]


## <a name="troubleshooting"></a>Устранение неполадок ##

Если приложение не запускается из пользовательского образа Docker, проверьте журналы Docker в каталоге LogFiles/Docker. Доступ к этому каталогу можно получить либо на сайте SCM, либо через FTP. 

![Просмотр журналов Docker с помощью Kudu][7]

Получить доступ к сайту SCM можно, щелкнув **Дополнительные средства** в меню **Средства разработки**.

## <a name="next-steps"></a>Дальнейшие действия ##

Просмотрите следующие материалы по началу работы со службой приложений на платформе Linux.   

* [Вводные сведения о службе приложений на платформе Linux](./app-service-linux-intro.md)
* [Creating Web Apps in App Service on Linux](./app-service-linux-how-to-create-a-web-app.md) (Создание веб-приложений в службе приложений на платформе Linux)
* [Использование конфигурации PM2 для Node.js в веб-приложениях на платформе Linux](./app-service-linux-using-nodejs-pm2.md)

Если у вас возникли вопросы или проблемы, вы можете опубликовать их на [нашем форуме](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).


<!--Image references-->
[1]: ./media/app-service-linux-using-custom-docker-image/new-configure-container.png
[2]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-dockerhub-public.png
[3]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-dockerhub-private.png
[4]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-privateregistry.png
[5]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-builtin.png
[6]: ./media/app-service-linux-using-custom-docker-image/setting-port.png
[7]: ./media/app-service-linux-using-custom-docker-image/kudu-docker-logs.png



<!--HONumber=Nov16_HO3-->


