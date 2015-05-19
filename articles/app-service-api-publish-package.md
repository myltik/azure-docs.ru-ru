<properties 
	pageTitle="Создание пакета приложения API" 
	description="Сведения о публикации пакета приложения API в Azure Marketplace." 
	services="app-service\api" 
	documentationCenter=".net"
	authors="guangyang"
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="guayan"/>

# Публикация пакета приложения API в Azure Marketplace

## Обзор

В этой статье показано, как опубликовать пакет приложения API в [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/).

- Сведения о создании приложения API см. в статье [Создание приложения API с помощью Visual Studio](app-service-dotnet-create-api-app.md).
- Сведения о создании пакета приложения API см. в статье [Создание пакета приложения API](app-service-api-create-package).

## Общая схема процесса публикации

Ниже приводится общая схема процесса публикации

1. Создайте пакет Nuget для приложения API, следуя инструкциям в учебнике [Создание пакета приложения API](app-service-api-create-package).
2. Опубликуйте его в коллекции Nuget на сайте https://apiapps.nuget.org.
3. Затем он будет автоматически синхронизирован с [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/).
4. Откройте [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/) и [предварительную версию портала Azure](https://portal.azure.com), чтобы проверить комплексную работу.

## Публикация в коллекции Nuget

1. Перейдите на сайт https://apiapps.nuget.org.

    ![Домашняя страница коллекции Nuget](./media/app-service-api-publish-package/nuget-homepage.png)

2. Щелкните **Вход** и войдите в систему с использованием учетной записи Azure.
3. Перейдите на вкладку **Приложения API**, щелкните элемент **Отправка приложения API** и отправьте пакет приложения API.

    ![Страница отправки пакета в коллекцию NuGet](./media/app-service-api-publish-package/nuget-upload-package-page.png)

4. В разделе **Пространство имен** в раскрывающемся списке будут показаны все проверенные домены клиентов Azure Active Directory вашей учетной записи Azure. Убедитесь, что выбрано значение, соответствующее свойству пространства имен в файле apiapp.json вашего пакета приложения API. Это проверка выполняется для того, чтобы издатели использовали действительные пространства имен для своих пакетов приложения API.
5. Убедитесь, что не установлен флажок **Не включать это приложение API в список**.
6. Щелкните **Опубликовать**.
7. При наличии ошибок проверки исправьте их и повторите отправку.

## Просмотр пакета приложения API в Azure Marketplace

Через несколько минут ваш пакет приложения API будет синхронизирован с Azure Marketplace. Чтобы проверить заголовок, описание, значок и другие данные, перейдите по этой [ссылке](http://azure.microsoft.com/marketplace/api-apps/). Если что-то необходимо изменить, просто внесите необходимые изменения в пакет приложения API и опубликуйте его повторно.

![Страницы приложения API Azure Marketplace](./media/app-service-api-publish-package/acom-marketplace-apiapp-page.png)

## Развертывание пакета приложения API на портале предварительной версии Azure

Вы также можете войти в [предварительную версию портала Azure](https://portal.azure.com) с помощью учетной записи Azure \(эта учетная запись может отличаться от используемой для публикации пакета приложения API\). Здесь можно попробовать создание пакета приложения API на портале предварительной версии Azure. Если что-то необходимо изменить, просто внесите необходимые изменения в пакет приложения API и опубликуйте его повторно.

Подробные сведения о развертывании пакета приложения API на портале Azure см. в примере развертывании DropboxConnector [здесь](app-service-api-connnect-your-app-to-saas-connector.md).

<!--HONumber=52-->
