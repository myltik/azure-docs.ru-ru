---
title: "Публикация контейнера Docker с помощью набора средств Azure для IntelliJ | Документация Майкрософт"
description: "Вы можете узнать, как опубликовать веб-приложение в Microsoft Azure в виде контейнера Docker с помощью набора средств Azure для IntelliJ."
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
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: f8ae15f4a5edb3cd84b5bb2302e1a5f709b57f82
ms.lasthandoff: 04/22/2017


---

# <a name="how-to-publish-a-web-app-as-a-docker-container-using-the-azure-toolkit-for-intellij"></a>Как опубликовать веб-приложение в виде контейнера Docker с помощью набора средств Azure для IntelliJ

Контейнеры Docker широко используются для развертывания веб-приложений. При этом разработчики могут объединить все зависимости и файлы проекта в одном пакете для развертывания на сервере. Набор средств Azure для IntelliJ упрощает этот процесс для разработчиков на Java, предоставляя функции *публикации в качестве контейнера Docker* при развертывании в Microsoft Azure. В этой статье описаны действия, необходимые для публикации приложений в Azure в качестве контейнеров Docker.

> [!NOTE]
>
> Дополнительные сведения о Docker см. на [веб-сайте Docker].
>

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="publishing-your-web-app-to-azure-using-a-docker-container"></a>Публикация веб-приложения в Azure с помощью контейнера Docker

> [!NOTE]
>
> Чтобы опубликовать веб-приложение, необходимо создать готовый к развертыванию артефакт. Дополнительные сведения см. в разделе [Дополнительные сведения о создании артефактов](#artifacts) далее в этой статье.
>
> Кроме того, когда вы выполните развертывание с помощью мастера по крайней мере один раз, большинство параметров, указанных в этом пошаговом руководстве, будет использоваться по умолчанию при повторном запуске этого мастера.
>

1. Откройте проект веб-приложения в IntelliJ.

1. Используйте один из следующих методов, чтобы запустить мастер публикации в виде контейнера Docker.

   * Щелкните правой кнопкой мыши проект в окне инструмента **Project** (Проект), щелкните **Azure**, а затем выберите **Publish as Docker Container** (Опубликовать как контейнер Docker).  ![Публикация в виде контейнера Docker][PUB01]

   * Щелкните меню **Publish Group** (Публикация группы) на панели инструментов IntelliJ, а затем выберите **Publish as Docker Container** (Опубликовать как контейнер Docker).  ![Публикация в виде контейнера Docker][PUB02]

1. При отображении мастера **Deploy Docker Container on Azure** (Развертывание контейнера Docker в Azure) появится диалоговое окно, как показано ниже. ![Мастер развертывания контейнера Docker в Azure][PUB03]

   а. Введите в текстовом поле **Docker image name** (Имя образа Docker) уникальное имя для узла Docker. (Мастер создаст это имя автоматически, но его можно изменить.)

   b. В окне **Hosts** (Узлы) отображаются все созданные вами узлы Docker.
      * Если вы еще не создали узлы Docker, этот раздел диалогового окна будет пуст.
      * Если вы уже создали узлы Docker, то сможете развернуть веб-приложение на существующем узле. В противном случае выполните описанные ниже действия, чтобы создать узел Docker.

1. Чтобы создать узел Docker, щелкните зеленый знак "плюс" (**+**). Откроется диалоговое окно **Create Docker Host** (Создание узла Docker).
      ![Мастер развертывания контейнера Docker в Azure][PUB04a]

   а. Укажите для узла Docker приведенные ниже параметры. (Мастер создаст большинство параметров автоматически, но их можно изменить.)

      * **Name** (Имя): уникальное имя узла Docker. (Это не указанное выше имя образа Docker.)

      * **Subscription** (Подписка): укажите, какая подписка Azure будет использоваться для узла.
      
      * **Region** (Регион): укажите географический регион, где будет размещаться узел.
      
      * На вкладке **OS and Size** (ОС и размер):
         * **Host OS** (ОС узла): укажите операционную систему для виртуальной машины, которая будет содержать ваш узел.
         * **Size** (Размер): укажите размер виртуальной машины для узла.

      * На вкладке **Resource Group** (Группа ресурсов):
         * **New resource group** (Новая группа ресурсов): позволяет создать группу ресурсов для узла.
         * **Existing resource group** (Существующая группа ресурсов): позволяет указать существующую группу ресурсов из учетной записи Azure.

      * На вкладке **Network** (Сеть):
         * **New virtual network** (Новая виртуальная сеть): позволяет создать виртуальную сеть для узла.
         * **Existing virtual network** (Существующая виртуальная сеть): позволяет указать существующую виртуальную сеть из учетной записи Azure.

      * На вкладке **Storage** (Хранилище):
         * **New storage account** (Новая учетная запись хранения): позволяет создать учетную запись хранения для узла.
         * **Existing storage account** (Существующая учетная запись хранения): позволяет указать существующую учетную запись хранения из учетной записи Azure.

   b. Указав перечисленные выше параметры, нажмите кнопку **Next** (Далее).

   c. Выберите один из следующих параметров для входа на виртуальную машину узла Docker.    ![Создание узла Docker][PUB05]

      * **Import credentials from Azure Key Vault** (Импортировать учетные данные из Azure Key Vault): позволяет указать сохраненный ранее набор учетных данных, хранящихся в подписке Azure.

      > [!NOTE]
      > Хранилище Azure Key Vault, создаваемое с помощью определенной учетной записи или определенного субъекта-службы, не становится автоматически доступным для другой учетной записи или субъекта-службы, которые относятся к той же подписке. Чтобы разрешить другой учетной записи или субъекту-службе использовать Key Vault, нужно добавить их с помощью портала Azure.

      * **New log in credentials** (Новые учетные данные для входа): позволяет создать набор учетных данных для входа, для которых потребуется указать следующие параметры на вкладке **VM Credentials** (Учетные данные для виртуальной машины):
         * **Username** (Имя пользователя): указывает имя пользователя для входа на виртуальную машину.
         * **Password** (Пароль) и **Confirm** (Подтверждение): указывают пароль для входа на виртуальную машину.
         * **SSH**: задает параметры Secure Shell (SSH) для узла Docker. Доступны следующие параметры:
            * **None** (Нет): указывает, что виртуальная машина не разрешает SSH-подключения.
            * **Auto-generate** (Автоматическое создание): этот параметр позволяет автоматически создать необходимые параметры для SSH-подключения.
            * **Import from directory** (Импорт из каталога): позволяет указать каталог, содержащий набор ранее сохраненных параметров SSH. В частности, каталог должен содержать два следующих файла:
               * *id_rsa*: этот файл содержит идентификационные данные RSA для пользователя.
               * *id_rsa.pub*: этот файл содержит открытый ключ RSA, который будет использоваться для аутентификации.
        
      * На вкладке **Docker Daemon Access** (Доступ к управляющей программе Docker) укажите следующие параметры. ![Создание узла Docker][PUB06]

         * **Docker Daemon port** (Порт управляющей программы Docker): задает уникальный TCP-порт для узла Docker.
         * **TLS Security** (Безопасность TLS): задает параметры TLS для узла Docker. Доступны следующие параметры:
            * **None** (Нет): указывает, что виртуальная машина не разрешает TLS-подключения.
            * **Auto-generate** (Автоматическое создание): этот параметр позволяет автоматически создать необходимые параметры для TLS-подключения.
            * **Import from directory** (Импорт из каталога): позволяет указать каталог, содержащий набор ранее сохраненных параметров TLS. В частности, каталог должен содержать шесть следующих файлов:
               * *ca.pem* и *ca-key.pem*: эти файлы содержат сертификат и открытый ключ для центра сертификации TLS.
               * *cert.pem* и *key.pem*: эти файлы содержат сертификат клиента и открытый ключ, которые будут использоваться для аутентификации TLS.
               * *server.pem* и *server-key.pem*: эти файлы содержат сертификат сервера и открытый ключ для узла.

   г) Указав все перечисленные выше параметры, нажмите кнопку **Finish** (Готово).

1. Когда снова отобразится мастер **развертывания контейнера Docker в Azure**, нажмите кнопку **Next** (Далее).
   ![Мастер развертывания контейнера Docker в Azure][PUB07]

1. На последней странице мастера укажите следующие параметры.

   * **Docker container name** (Имя контейнера Docker): это уникальное имя контейнера Docker.

   * Выберите один из следующих образов Docker:

      * **Predefined Docker image** (Предопределенный образ Docker): позволяет указать существующий образ Docker из Azure.

      > [!NOTE]
      > Список образов Docker в этом раскрывающемся меню состоит из нескольких образов, которые в набор средств Azure устанавливает исправление таким образом, чтобы артефакт развертывался автоматически.

      * **Custom Dockerfile** (Пользовательский Dockerfile): позволяет указать сохраненный ранее Dockerfile на локальном компьютере.

      > [!NOTE]
      > Это расширенная функция для разработчиков, желающих развернуть собственный Dockerfile. Однако за правильность сборки Dockerfile отвечают сами разработчики, использующие эту возможность. Набор средств Azure не проверяет содержимое пользовательского Dockerfile, поэтому при наличии проблем в Dockerfile развертывание может завершиться ошибкой. Кроме того, набор средств Azure ожидает, что пользовательский Dockerfile содержит артефакт веб-приложения, и попытается установить HTTP-подключение. Если разработчики публикуют артефакт другого типа, то после развертывания могут возникнуть некритичные ошибки.

   * **Port settings** (Параметры порта): указывает уникальную привязку TCP-порта для контейнера Docker.
   ![Мастер развертывания контейнера Docker в Azure][PUB08]

1. Выполнив все перечисленные выше шаги, нажмите кнопку **Finish** (Готово).

Набор средств Azure начнет развертывание веб-приложения в Azure в контейнере Docker, и если только вы не настроили IntelliJ для развертывания в фоновом режиме, появится диалоговое окно, в котором отображается ход развертывания. 
![Ход выполнения развертывания][PUB09]

<a name="artifacts"></a>
## <a name="additional-information-about-creating-artifacts"></a>Дополнительные сведения о создании артефактов

Чтобы создать готовый к развертыванию артефакт, выполните следующее.

1. Откройте проект веб-приложения в IntelliJ.

1. В меню **File** (Файл) выберите **Project Structure** (Структура проекта).
   ![Меню "Project Structure" (Структура проекта)][ART01]

1. Щелкните зеленый знак "плюс" (**+**), чтобы добавить артефакт, затем щелкните **Web Application: Artifact** (Веб-приложение: артефакт).
   ![Добавление артефакта][ART02]

1. Укажите имя артефакта без расширения WAR и нажмите кнопку **ОК**.
   ![Свойства артефакта][ART03]

Дополнительные сведения о создании артефактов в IntelliJ см. в разделе [Configuring Artifacts] (Настройка артефактов) на веб-сайте JetBrains.

## <a name="see-also"></a>См. также
Дополнительные сведения о наборах средств Azure для Java IDE см. по следующим ссылкам:

* [Набор средств Azure для Eclipse]
  * [Новые возможности набора средств Azure для Eclipse]
  * [Установка набора средств Azure для Eclipse]
  * [Инструкции по входу для набора средств Azure для Eclipse]
  * [Создание веб-приложения Hello World для Azure в Eclipse]
* [Набор средств Azure для IntelliJ]
  * [Новые возможности набора средств Azure для IntelliJ]
  * [Установка набора средств Azure для IntelliJ]
  * [Инструкции по входу для набора средств Azure для IntelliJ]
  * [Создание веб-приложения Hello World для Azure в IntelliJ]

Дополнительные сведения об использовании Azure см. в [центре разработчиков Java для Azure] и на странице [инструментов Java для Visual Studio Team Services].

Дополнительные материалы по Docker доступны на официальном [веб-сайте Docker].

<!-- URL List -->

[Набор средств Azure для Eclipse]: ./azure-toolkit-for-eclipse.md
[Набор средств Azure для IntelliJ]: ./azure-toolkit-for-intellij.md
[Создание веб-приложения Hello World для Azure в Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Создание веб-приложения Hello World для Azure в IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Установка набора средств Azure для Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Установка набора средств Azure для IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Инструкции по входу для набора средств Azure для Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Инструкции по входу для набора средств Azure для IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Новые возможности набора средств Azure для Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Новые возможности набора средств Azure для IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[центре разработчиков Java для Azure]: https://azure.microsoft.com/develop/java/
[инструментов Java для Visual Studio Team Services]: https://java.visualstudio.com/ (Инструменты Java для Visual Studio Team Services)

[веб-сайте Docker]: https://www.docker.com/
[Configuring Artifacts]: https://www.jetbrains.com/help/idea/2016.1/configuring-artifacts.html

<!-- IMG List -->

[PUB01]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB01.png
[PUB02]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB02.png
[PUB03]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB03.png
[PUB04a]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB04a.png
[PUB04b]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB04b.png
[PUB04c]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB04c.png
[PUB04d]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB04d.png
[PUB05]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB05.png
[PUB06]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB06.png
[PUB07]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB07.png
[PUB08]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB08.png
[PUB09]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB09.png

[ART01]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/ART01.png
[ART02]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/ART02.png
[ART03]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/ART03.png

