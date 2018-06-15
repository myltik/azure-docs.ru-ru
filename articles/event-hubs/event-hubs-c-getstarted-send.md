---
title: Отправка событий в концентраторы событий Azure с помощью C | Документация Майкрософт
description: Отправка событий в концентраторы событий Azure с помощью C
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 12/4/2017
ms.author: sethm
ms.openlocfilehash: e3267b54fa0c8593e0f9366c009656f36e4094ef
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34807821"
---
# <a name="send-events-to-azure-event-hubs-using-c"></a>Отправка событий в концентраторы событий Azure с помощью C

## <a name="introduction"></a>Введение
Концентраторы событий — это высокомасштабируемая система, способная принимать миллионы событий в секунду, благодаря которой приложения могут обрабатывать и анализировать большие объемы данных от подключенных устройств и приложений. После сбора данных в концентраторах событий их можно преобразовать и сохранить с помощью любого поставщика аналитики в реальном времени или в кластере хранилища.

Дополнительные сведения см. в разделе [Обзор концентраторов событий](https://docs.microsoft.com/azure/event-hubs/event-hubs-overview).

Из этого руководства вы узнаете, как отправлять события в концентратор событий с помощью консольного приложения на языке C. Чтобы ознакомиться с особенностями получения событий, выберите соответствующий язык в оглавлении слева.

Для работы с этим учебником требуется:

* Среда разработки C. В этом учебнике предполагается, что применяется стек gcc на виртуальной машине Azure Linux с Ubuntu 14.04.
* [Microsoft Visual Studio](https://www.visualstudio.com/).
* Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="send-messages-to-event-hubs"></a>Отправка сообщений в центры событий
Из этого раздела вы узнаете, как написать приложение для отправки событий в концентратор на языке C. В коде используется библиотека Proton AMQP из [проекта Apache Qpid](http://qpid.apache.org/). Эта процедура аналогична использованию очередей и разделов службы "Служебная шина" с AMQP на C, как показано [в этом примере](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Дополнительную информацию см. в [документации по Qpid Proton](http://qpid.apache.org/proton/index.html).

1. Чтобы установить Qpid Proton, следуйте инструкциям для своей среды на [странице Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html).
2. Для компиляции библиотеки Proton установите следующие пакеты.
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Скачайте [библиотеку Qpid Proton](http://qpid.apache.org/proton/index.html) и извлеките ее, например:
   
    ```shell
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Создайте каталог построения, скомпилируйте и установите:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. В рабочем каталоге создайте новый файл с именем **sender.c** со следующим кодом. Обязательно замените значения для ключа и имени, имени концентратора событий и пространства имен SAS. Также необходимо заменить версию ключа **SendRule** , закодированную как URL-адрес, созданную ранее. Выполнить кодировку как URL-адрес можно [здесь](http://www.w3schools.com/tags/ref_urlencode.asp).
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. Скомпилируйте файл при условии **gcc**:
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > В этом коде для периода отправки используется значение 1. Это обеспечивают максимальную скорость принудительной отправки сообщений. Рекомендуем сгруппировать сообщения в приложении, чтобы увеличить пропускную способность. Информацию об использовании библиотеки Qpid Proton в этой и других средах, а также на платформах, для которых предоставляются привязки (сейчас это Perl, PHP, Python и Ruby), см. на [странице Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html).


## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о концентраторах событий см. в следующих источниках:

* [Event Hubs overview](event-hubs-what-is-event-hubs.md)
* [Часто задаваемые вопросы о концентраторах событий](event-hubs-faq.md)

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
