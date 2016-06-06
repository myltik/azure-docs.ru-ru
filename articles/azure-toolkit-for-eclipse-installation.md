<properties
	pageTitle="Установка набора средств Azure для Eclipse | Microsoft Azure"
	description="Узнайте, как установить набор средств Azure для Eclipse."
	services=""
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="multiple"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="05/19/2016" 
	ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

# Установка набора средств Azure для Eclipse

В набор средств Azure для Eclipse входят шаблоны и функциональные возможности для простого создания, разработки, тестирования и развертывания приложений Azure с помощью среды разработки Eclipse. Набор средств Azure для Eclipse — это проект с открытым кодом, исходный код которого доступен по лицензии MIT на сайте проекта в GitHub по следующему URL-адресу:

<https://github.com/microsoft/azure-tools-for-java>

Ниже приведен процесс установки набора средств Azure для Eclipse.

[AZURE.INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## Установка набора средств Azure для Eclipse

1. Запустите Eclipse.

1. В меню Eclipse выберите **Help** (Справка), а затем Install **New Software** (Установка нового программного обеспечения), как показано на следующей схеме.

    ![Установка набора средств Azure для Eclipse][01]

1. В диалоговом окне **Available Software** (Доступное программное обеспечение) в текстовом поле **Work with** (Работа с) введите **http://dl.microsoft.com/eclipse** и нажмите клавишу **ВВОД**.

1. В области **Name** (Имя) установите флажок **Azure Toolkit for Eclipse** (Набор средств Azure для Eclipse) и снимите флажок **Contact all update sites during install to find required software** (Проверить все сайты обновления во время установки для поиска требуемого ПО). Экран должен выглядеть следующим образом:

    ![Установка набора средств Azure для Eclipse][02]

1. Если вы развернете узел **Azure Toolkit for Eclipse** (Набор средств Azure для Eclipse), то увидите следующие элементы:

    * **Application Insights Plugin for Java** (Подключаемый модуль Application Insights для Java) — этот компонент позволяет использовать службы регистрации и анализа телеметрии Azure для приложений и экземпляров серверов.
    * **Фильтр служб контроля доcтупа Azure** — этот компонент обеспечивает поддержку проверки подлинности пользователей приложения с помощью Azure ACS, используя сценарии единого входа и перемещая логику удостоверений из приложения.
    * **Общий подключаемый модуль Azure** — этот компонент предоставляет функциональные возможности, необходимые другим компонентам набора средств.
    * **Обозреватель Azure для Eclipse** — этот компонент предоставляет функциональные возможности, необходимые другим компонентам набора средств.
    * **Подключаемый модуль Azure для Eclipse с Java** — этот компонент обеспечивает поддержку проектов, помогающих собирать, тестировать и развертывать приложения Java для облака Microsoft Azure в Eclipse и с помощью командной строки.
    * **Подключаемый модуль веб- приложений Azure с Java** — этот компонент обеспечивает поддержку развертывания веб-приложений Java в контейнерах веб-приложений Microsoft Azure.
    * **Драйвер Microsoft JDBC 4.2 для SQL Server** — этот компонент предоставляет API JDBC для SQL Server и Базу данных SQL Microsoft Azure для платформы Java Enterprise Edition 8.
    * **Package for Apache Qpid Client Libraries for JMS** (Пакет для клиентских библиотек Apache Qpid для JMS) — этот компонент предоставляет клиентские компоненты JMS из проекта Apache Qpid, чтобы ваше приложение могло использовать обмен сообщениями на основе протокола AMQP в Azure.
    * **Пакет для библиотек Microsoft Azure для Java** — этот компонент предоставляет API для доступа к службам Microsoft Azure, таким как хранилище, служебная шина, среда выполнения службы и т. д.

1. Нажмите кнопку **Далее**. (Если при установке набора средств возникают необычные задержки, убедитесь, что снят флажок **Contact all update sites during install to find required software** [Проверить все сайты обновления во время установки для поиска требуемого ПО].)

1. В диалоговом окне **Install Details** (Сведения об установке) нажмите кнопку **Далее**.

    ![Просмотр сведений об установке][03]

1. В диалоговом окне **Review Licenses** (Просмотр лицензий) ознакомьтесь с условиями лицензионного соглашения. Если вы принимаете условия лицензионных соглашений, нажмите кнопку **I accept the terms of the license agreements** (Я принимаю условия лицензионных соглашений), а затем нажмите кнопку **Готово**. (В следующих действиях предполагается, что вы принимаете условия лицензионных соглашений. Если вы не принимаете эти условия, завершите процесс установки.)

    ![Просмотр лицензий][04]

    Eclipse скачает и установит необходимые пакеты.

    ![Ход установки][05]

1. Если отображается запрос на перезапуск Eclipse для завершения установки, нажмите кнопку **Yes** (Да).

    ![Запрос перезапуска][06]

## См. также

[Набор средств Azure для Eclipse]

[Создание веб-приложения Hello World для Azure в Eclipse]

[Новые возможности набора средств Azure для Eclipse]

Дополнительные сведения об использовании Azure с Java можно найти в [центре разработчиков Java для Azure].

<!-- URL List -->

[Набор средств Azure для Eclipse]: ./azure-toolkit-for-eclipse.md
[центре разработчиков Java для Azure]: https://azure.microsoft.com/develop/java/
[Создание веб-приложения Hello World для Azure в Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Новые возможности набора средств Azure для Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md

<!-- IMG List -->

[01]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-01.png
[02]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-02.png
[03]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-03.png
[04]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-04.png
[05]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-05.png
[06]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-06.png

<!---HONumber=AcomDC_0525_2016-->