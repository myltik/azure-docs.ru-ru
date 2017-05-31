---
title: "Публикация контейнера Docker с помощью набора средств Azure для Eclipse | Документы Майкрософт"
description: "Вы можете узнать, как опубликовать веб-приложение в Microsoft Azure в виде контейнера Docker с помощью набора средств Azure для Eclipse."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 746bb0a073396b84fa8592adda6748a2a5692ee8
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017


---

# <a name="publish-a-web-app-as-a-docker-container-by-using-the-azure-toolkit-for-eclipse"></a>Публикация веб-приложения в виде контейнера Docker с помощью набора средств Azure для Eclipse

Контейнеры Docker широко используются при развертывании веб-приложений. При этом разработчики могут объединить все зависимости и файлы проекта в одном пакете, а затем развернуть его на сервере. Набор средств Azure для Eclipse упрощает этот процесс для разработчиков на языке Java, предоставляя функции *публикации в виде контейнера Docker* при развертывании в Microsoft Azure. В этой статье описаны действия, необходимые для публикации приложений в Azure в качестве контейнеров Docker.

> [!NOTE]
> Дополнительные сведения о Docker см. на [веб-сайте Docker].
>

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="publish-your-web-app-to-azure-by-using-a-docker-container"></a>Публикация веб-приложения в Azure с помощью контейнера Docker

1. Откройте проект веб-приложения в Eclipse.

2. Чтобы запустить мастер **публикации в виде контейнера Docker**, выполните одно из следующих действий:

   * В представлении **Навигатор** щелкните правой кнопкой мыши проект, щелкните **Azure**, а затем выберите **Publish as Docker Container** (Опубликовать как контейнер Docker).

      ![Представление навигатора, команда публикации в виде контейнера Docker][PUB01]

   * На панели инструментов Eclipse нажмите кнопку **Опубликовать**, а затем выберите **Publish as Docker Container** (Опубликовать как контейнер Docker).

      ![Панель инструментов Eclipse, команда публикации в виде контейнера Docker][PUB02]
      
    После этого откроется мастер **развертывания контейнера Docker в Azure**.

    ![Мастер развертывания контейнера Docker в Azure][PUB03]

3. В окне **ввода имени образа, выбора пути артефакта и проверки используемого узла Docker** сделайте следующее:

    а. В текстовом поле **Docker image name** (Имя образа Docker) введите уникальное имя узла Docker. (Мастер создаст это имя автоматически, но его можно изменить.)

    b. В области **Узлы** отображаются все созданные вами узлы Docker. Выполните одно из приведенных ниже действий.

    * Если вы уже создали узел Docker, вы можете развернуть веб-приложение в нем.
    * Чтобы создать узел Docker, нажмите кнопку **Добавить**.  
      
    После этого откроется диалоговое окно **Create Docker Host** (Создание узла Docker).

    ![Мастер развертывания контейнера Docker в Azure][PUB04a]

4. В окне **Настройка новой виртуальной машины** укажите приведенные ниже параметры для узла Docker. (Мастер создаст большинство параметров автоматически, но их можно изменить.)

   а. **Имя**. Введите уникальное имя узла Docker. (Это не указанное выше имя образа Docker.)

   b. **Подписка**. Укажите, какая подписка Azure будет использоваться для узла.

   c. **Регион**. Укажите географический регион, где размещен узел.

   г) На вкладке **Host OS and Size** (ОС и размер узла):
     * **Host OS** (ОС узла). Укажите операционную систему виртуальной машины, которая содержит узел.
     * **Size** (Размер). Укажите размер виртуальной машины узла.

   д. На вкладке **Resource Group** (Группа ресурсов):
     * **Новая группа ресурсов**. Создайте группу ресурсов для узла.
     * **Существующая группа ресурсов**. Укажите имеющуюся группу ресурсов из учетной записи Azure.

   f. На вкладке **Network** (Сеть):
     * **New virtual network** (Новая виртуальная сеть). Создайте виртуальную сеть для узла.
     * **Existing virtual network** (Имеющаяся виртуальная сеть). Укажите имеющуюся виртуальную сеть из учетной записи Azure.

   g. На вкладке **Storage** (Хранилище):
     * **Создать учетную запись хранения**. Создайте учетную запись хранения для узла.
     * **Existing storage account** (Имеющаяся учетная запись хранения). Укажите имеющуюся учетную запись хранения из учетной записи Azure.

5. Щелкните **Далее**.

6. В окне **Configure log in credentials and port settings** (Настройка учетных данных для входа в систему и параметров порта) выберите один из следующих параметров.

    * **Import credentials from Azure Key Vault** (Импортировать учетные данные из Azure Key Vault). Укажите сохраненный ранее набор учетных данных, хранящихся в подписке Azure.

      >[!NOTE]
      >Хранилище Azure Key Vault, созданное с помощью определенной учетной записи или определенного субъекта-службы, не становится автоматически доступным для другой учетной записи или субъекта-службы, которые относятся к той же подписке. Чтобы разрешить другой учетной записи или субъекту-службе использовать Key Vault, нужно добавить их на портале Azure.

    * **New log in credentials** (Новые учетные данные для входа). Создайте набор учетных данных для входа в систему. Если вы выбрали этот параметр, сделайте следующее:
    
      * На вкладке **Учетные данные виртуальной машины** выберите один из следующих параметров для учетной записи входа на виртуальную машину узла Docker.

          * **Имя пользователя.** Укажите имя пользователя для входа на виртуальную машину.
          * **Пароль** и **Подтверждение.** Укажите пароль для входа на виртуальную машину.
          * **SSH.** Укажите параметры Secure Shell (SSH) узла Docker. Вы можете выбрать один из следующих параметров:
            * **None** (Нет): указывает, что виртуальная машина не разрешает SSH-подключения.
            * **Auto-generate** (Автоматическое создание). Автоматически создает необходимые параметры для SSH-подключения.
            * **Import from directory** (Импорт из каталога). Позволяет указать каталог, содержащий набор ранее сохраненных параметров SSH. В частности, каталог должен содержать два следующих файла:
                * *id_rsa*. Этот файл содержит идентификационные данные RSA для пользователя.
                * *id_rsa.pub*. Этот файл содержит открытый ключ RSA, который используемый для проверки подлинности.
        
        ![Создание узла Docker][PUB05]

      * На вкладке **Docker Daemon Access** (Доступ к управляющей программе Docker) укажите следующие параметры.

          * **Docker Daemon port** (Порт управляющей программы Docker). Укажите уникальный TCP-порт для узла Docker.
          * **Безопасность TLS**. Введите параметры безопасности транспортного уровня для узла Docker. Вы можете выбрать один из следующих параметров:
            * **None** (Нет): указывает, что виртуальная машина не разрешает TLS-подключения.
            * **Auto-generate** (Автоматическое создание). Автоматически создает необходимые параметры для TLS-подключения.
            * **Import from directory** (Импорт из каталога). Позволяет указать каталог, содержащий набор ранее сохраненных параметров TLS. В частности, каталог должен содержать шесть следующих файлов:
                * *ca.pem* и *ca-key.pem*. Эти файлы содержат сертификат и открытый ключ для центра сертификации TLS.
                * *cert.pem* и *key.pem*. Эти файлы содержат сертификат клиента и открытый ключ, которые будут использоваться для аутентификации TLS.
                * *server.pem* и *server-key.pem*. Эти файлы содержат сертификат сервера и открытый ключ для узла.

        ![Создание узла Docker][PUB06]

7. Указав предыдущие сведения, нажмите кнопку **Готово**.

8. В мастере **развертывания контейнера Docker в Azure** нажмите кнопку **Далее**.

   ![Мастер развертывания контейнера Docker в Azure][PUB07]

9. В окне **Configure the Docker container to be created** (Настройка создаваемого контейнера Docker) выполните следующие действия.

   а. В текстовом поле **Docker container name** (Имя контейнера Docker) введите уникальное имя контейнера Docker.

   b. Выберите один из следующих образов Docker:
     * **Predefined Docker image** (Предопределенный образ Docker). Укажите имеющийся образ Docker из Azure.

       >[!NOTE]
       >Список образов Docker в этом поле состоит из нескольких образов, в которые набор средств Azure устанавливает исправление таким образом, чтобы артефакт развертывался автоматически.

     * **Custom Dockerfile** (Пользовательский Dockerfile). Укажите сохраненный ранее Dockerfile на локальном компьютере.

       >[!NOTE]
       >Это расширенная функция для разработчиков, желающих развернуть собственный Dockerfile. Однако за правильность сборки Dockerfile отвечают сами разработчики, использующие эту возможность. Набор средств Azure не проверяет содержимое пользовательского Dockerfile, поэтому при наличии проблем в Dockerfile развертывание может завершиться ошибкой. Кроме того, набор средств Azure ожидает, что пользовательский Dockerfile содержит артефакт веб-приложения, и попытается установить HTTP-подключение. Если разработчики публикуют артефакт другого типа, то после развертывания могут возникнуть некритичные ошибки.

   c. **Port settings** (Параметры порта). Укажите уникальную привязку TCP-порта для контейнера Docker.

     ![Окно настройки создаваемого контейнера Docker][PUB08]

10. Выполнив все предыдущие действия, нажмите кнопку **Готово**.

Набор средств Azure начинает развертывание веб-приложения в контейнере Docker Azure. 

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о наборах средств Azure для Java IDE см. в следующих ресурсах:

* [Набор средств Azure для Eclipse]
  * [Новые возможности набора средств Azure для Eclipse]
  * [Установка набора средств Azure для Eclipse]
  * [Инструкции по входу для набора средств Azure для Eclipse]
  * [Создание веб-приложения Azure (цен. категория "Базовый") с помощью Eclipse]
* [Набор средств Azure для IntelliJ]
  * [Новые возможности набора средств Azure для IntelliJ]
  * [Установка набора средств Azure для IntelliJ]
  * [Инструкции по входу для набора средств Azure для IntelliJ]
  * [Создание веб-приложения Azure (цен. категория "Базовый") в IntelliJ]

Дополнительные сведения об использовании Azure см. в [центре разработчиков Java для Azure] и на странице [средств Java для Visual Studio Team Services].

Дополнительные материалы по Docker доступны на официальном [веб-сайте Docker].

<!-- URL List -->

[Набор средств Azure для Eclipse]: ./azure-toolkit-for-eclipse.md
[Набор средств Azure для IntelliJ]: ./azure-toolkit-for-intellij.md
[Создание веб-приложения Azure (цен. категория "Базовый") с помощью Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Создание веб-приложения Azure (цен. категория "Базовый") в IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Установка набора средств Azure для Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Установка набора средств Azure для IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Инструкции по входу для набора средств Azure для Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Инструкции по входу для набора средств Azure для IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Новые возможности набора средств Azure для Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Новые возможности набора средств Azure для IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[центре разработчиков Java для Azure]: https://azure.microsoft.com/develop/java/
[средств Java для Visual Studio Team Services]: https://java.visualstudio.com/ (Инструменты Java для Visual Studio Team Services)

[веб-сайте Docker]: https://www.docker.com/

<!-- IMG List -->

[PUB01]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB01.png
[PUB02]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB02.png
[PUB03]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB03.png
[PUB04a]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04a.png
[PUB04b]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04b.png
[PUB04c]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04c.png
[PUB04d]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04d.png
[PUB05]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB05.png
[PUB06]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB06.png
[PUB07]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB07.png
[PUB08]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB08.png
