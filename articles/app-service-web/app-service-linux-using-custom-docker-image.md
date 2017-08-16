---
title: "Как применить пользовательский образ Docker для веб-приложения Azure на платформе Linux | Документация Майкрософт"
description: "Узнайте, как применить пользовательский образ Docker для веб-приложения Azure на платформе Linux."
keywords: "служба приложений azure, веб-приложение, docker, контейнер"
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
ms.translationtype: HT
ms.sourcegitcommit: 1dbb1d5aae55a4c926b9d8632b416a740a375684
ms.openlocfilehash: f51cacb33251d479f48a39014cc2db60a23358d5
ms.contentlocale: ru-ru
ms.lasthandoff: 08/07/2017

---

# <a name="using-a-custom-docker-image-for-azure-web-app-on-linux"></a>Применение пользовательского образа Docker для веб-приложения Azure на платформе Linux #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


Служба приложений включает предопределенные стеки приложений на платформе Linux с поддержкой определенных версий, включая PHP 7.0 и Node.js 4.5. Для размещения этих готовых стеков служба приложений на платформе Linux использует контейнеры Docker. Вы также можете использовать пользовательский образ Docker для развертывания веб-приложения в стек приложений, который еще не определен в Azure. Пользовательские образы Docker могут размещаться как в открытых, так и закрытых репозиториях Docker.


## <a name="how-to-set-a-custom-docker-image-for-a-web-app"></a>Практическое руководство. Определение пользовательского образа Docker для веб-приложения
Вы можете применять пользовательские образы Docker как для новых, так и для существующих веб-приложений. Создавая веб-приложение на платформе Linux на [портале Azure](https://portal.azure.com/#create/Microsoft.AppSvcLinux), нажмите кнопку **Настроить контейнер**, чтобы определить пользовательский образ Docker.

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

2.  Для параметра **Источник образа** выберите **Частный реестр**. Укажите значения в полях **Image and optional tag name** (Образ и дополнительный тег) и **URL-адрес сервера** для частного реестра, а также введите учетные данные в поля **Имя пользователя для входа** и **Пароль**. Щелкните **Сохранить**.

    ![Настройка образа Docker из закрытого реестра][4]


## <a name="how-to-set-the-port-used-by-your-docker-image"></a>Практическое руководство. Определение порта, который используется образом Docker ##

Если вы применяете для веб-приложения пользовательский образ Docker, вы можете включить переменную среды `WEBSITES_PORT` в файл Docker, который будет добавлен в созданный контейнер. Рассмотрим следующий пример файла Docker для приложения Ruby.

    FROM ruby:2.2.0
    RUN mkdir /app
    WORKDIR /app
    ADD . /app
    RUN bundle install
    CMD bundle exec puma config.ru -p WEBSITES_PORT -e production

В последней строке команды видно, что переменная среды WEBSITES_PORT передается во время выполнения. Помните, что в командах учитывается регистр.

Ранее платформа использовала параметр приложения `PORT`. Мы планируем отказаться от этого параметра приложения и перейти исключительно к `WEBSITES_PORT`.

Если используется существующий образ Docker, созданный другим пользователем, возможно, вам понадобится указать для приложения порт, отличный от порта 80. Чтобы настроить порт, добавьте параметр `WEBSITES_PORT` со значением, как показано ниже.

![Настройка параметра приложения PORT для пользовательского образа Docker][6]


## <a name="how-to-switch-back-to-using-a-built-in-image"></a>Практическое руководство. Переход на использование встроенного образа ##

Чтобы перейти от применения пользовательского образа к встроенному, сделайте следующее.

1. На [портале Azure](https://portal.azure.com) найдите веб-приложение на платформе Linux, а затем в разделе **Параметры** щелкните **Служба приложений**.

2. Выберите свой **стек среды выполнения**, чтобы применить встроенный образ, и нажмите кнопку **Сохранить**. 

![Настройка встроенного образа Docker][5]


## <a name="troubleshooting"></a>Устранение неполадок ##

Если приложение не запускается из пользовательского образа Docker, проверьте журналы Docker в каталоге LogFiles. Доступ к этому каталогу можно получить либо на сайте SCM, либо через FTP.
Чтобы записать `stdout` и `stderr` из контейнера, необходимо включить **ведение журнала контейнера Docker** в разделе **Журналы диагностики**.

![Включение ведения журнала][8]

![Просмотр журналов Docker с помощью Kudu][7]

Получить доступ к сайту SCM можно, щелкнув **Дополнительные средства** в меню **Средства разработки**.

## <a name="next-steps"></a>Дальнейшие действия ##

Приступите к работе с веб-приложением на платформе Linux, воспользовавшись приведенными ниже ссылками.   

* [Введение в веб-приложение Azure на платформе Linux](./app-service-linux-intro.md)
* [Создание веб-приложений в веб-приложении Azure на платформе Linux](./app-service-linux-how-to-create-web-app.md)
* [Использование конфигурации PM2 для Node.js в веб-приложении Azure на платформе Linux](./app-service-linux-using-nodejs-pm2.md)
* [Вопросы и ответы о веб-приложении службы приложений Azure на платформе Linux](app-service-linux-faq.md)

Если у вас возникли вопросы или проблемы, вы можете опубликовать их на [нашем форуме](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).


<!--Image references-->
[1]: ./media/app-service-linux-using-custom-docker-image/new-configure-container.png
[2]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-dockerhub-public.png
[3]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-dockerhub-private.png
[4]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-privateregistry.png
[5]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-builtin.png
[6]: ./media/app-service-linux-using-custom-docker-image/setting-port.png
[7]: ./media/app-service-linux-using-custom-docker-image/kudu-docker-logs.png
[8]: ./media/app-service-linux-using-custom-docker-image/logging.png

