<properties
	pageTitle="Установка набора средств Azure для Eclipse"
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
    ms.date="03/28/2016" 
	ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

# Установка набора средств Azure для Eclipse

В набор средств Azure для Eclipse входят шаблоны и функциональные возможности для простого создания, разработки, тестирования и развертывания приложений Azure с помощью среды разработки Eclipse. Это проект с открытым кодом, исходный код которого доступен по лицензии Apache License 2.0 на сайте проекта в GitHub по следующему URL-адресу:

<https://github.com/microsoft/azure-tools-for-java>

Ниже приведен процесс установки набора средств Azure для Eclipse.

[AZURE.INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## Установка набора средств Azure для Eclipse

1. Запустите Eclipse.
2. В меню Eclipse выберите <strong>Help</strong> (Справка), а затем <strong>Install New Software</strong> (Установка нового программного обеспечения), как показано на следующей схеме. ![Установка набора средств Azure для Eclipse][ic590123]
3. В диалоговом окне <strong>Available Software</strong> (Доступное программное обеспечение) в поле <strong>Work with</strong> (Работа с) введите <strong>http://dl.microsoft.com/eclipse</strong> и нажмите клавишу <strong>ВВОД</strong>.
4. В области <strong>Name</strong> (Имя) установите флажок <strong>Azure Toolkit for Eclipse</strong> (Набор средств Azure для Eclipse) и снимите флажок <strong>Contact all update sites during install to find required software</strong> (Проверить все сайты обновления во время установки для поиска требуемого ПО). Экран должен выглядеть следующим образом: ![Установка набора средств Azure для Eclipse][ic719482]
5. Если вы развернете узел <strong>Azure Toolkit for Eclipse</strong> (Набор средств Azure для Eclipse), то увидите следующие элементы:
    * **Azure Access Control Services Filter** (Фильтр служб контроля доступа Azure) — этот компонент обеспечивает поддержку проверки подлинности пользователей приложения с помощью служб Azure ACS.
    * **Azure Common Plugin** (Общий подключаемый модуль Azure) — этот компонент содержит общие функциональные возможности, используемые другими компонентами.
    * **Azure Toolkit for Eclipse** (Набор средств Azure для Eclipse) — этот компонент содержит логику конфигурации проекта, мастер публикации в облако и пользовательский интерфейс.
    * **Microsoft JDBC Driver 4.0 for SQL Server** (Microsoft JDBC Driver 4.0 для SQL Server) — этот компонент упрощает разработку приложений с использованием Базы данных SQL.
    * **Package for Apache Qpid Client Libraries for JMS** (Пакет для клиентских библиотек Apache Qpid для JMS) — этот компонент предоставляет клиентскую библиотеку JMS из проекта Apache Qpid, чтобы ваше приложение могло использовать обмен сообщениями на основе протокола AMQP в Azure
    * **Package for Azure Libraries for Java** (Пакет для библиотек Azure для Java) — этот компонент позволяет создавать приложения Azure в Java, чтобы воспользоваться преимуществами масштабируемых облачных вычислительных ресурсов Azure.
    * **Application Insights Plugin for Java** (Подключаемый модуль Application Insights для Java) — этот компонент позволяет использовать службы регистрации и анализа телеметрии Azure для приложений и экземпляров серверов.
6. Нажмите кнопку **Далее**. (Если при установке набора средств возникают необычные задержки, убедитесь, что снят флажок **Contact all update sites during install to find required software** [Проверить все сайты обновления во время установки для поиска требуемого ПО].)
7. В диалоговом окне **Install Details** (Сведения об установке) нажмите кнопку **Далее**.
8. В диалоговом окне **Review Licenses** (Просмотр лицензий) ознакомьтесь с условиями лицензионного соглашения. Если вы принимаете условия лицензионных соглашений, нажмите кнопку **I accept the terms of the license agreements** (Я принимаю условия лицензионных соглашений), а затем нажмите кнопку **Готово**. (В следующих действиях предполагается, что вы принимаете условия лицензионных соглашений. Если вы не принимаете эти условия, завершите процесс установки.)
9. Если отображается запрос на перезапуск Eclipse для завершения установки, нажмите кнопку **Перезагрузить сейчас**.

## См. также

[Набор средств Azure для Eclipse]

[Создание приложения Hello World для Azure в Eclipse]

[Новые возможности набора средств Azure для Eclipse]

Дополнительные сведения об использовании Azure с Java можно найти в [центре разработчиков Java для Azure].

<!-- URL List -->

[Набор средств Azure для Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[центре разработчиков Java для Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Создание приложения Hello World для Azure в Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Новые возможности набора средств Azure для Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic590123]: ./media/azure-toolkit-for-eclipse-installation/ic590123.png
[ic719482]: ./media/azure-toolkit-for-eclipse-installation/ic719482.png

<!---HONumber=AcomDC_0330_2016-->