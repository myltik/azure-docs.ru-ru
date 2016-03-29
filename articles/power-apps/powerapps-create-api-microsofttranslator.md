<properties
	pageTitle="Добавление API Microsoft Translator в службу PowerApps Enterprise | Microsoft Azure"
	description="Создание и настройка нового API Microsoft Translator в среде службы приложений организации"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>

# Создание API Microsoft Translator в PowerApps Enterprise

> [AZURE.SELECTOR]
- [Приложения логики](../articles/connectors/create-api-microsofttranslator.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-microsofttranslator.md)

Добавьте API Microsoft Translator в среду службы приложений (клиента) организации. 

## Создание API на портале Azure

1. Войдите на [портал Azure](https://portal.azure.com/) с помощью рабочей учетной записи. Например, это может быть адрес *ИмяПользователя*@*ВашаКомпания*.com. При этом выполняется автоматический вход в подписку вашей компании.
 
2. На панели задач выберите **Обзор**:  
![][7]

3. Вы можете прокрутить список и найти PowerApps или набрать *powerapps* на клавиатуре:  
![][8]  

4. В разделе **PowerApps** выберите **Управление интерфейсами API**:  
![Переход к зарегистрированным API][1]

5. В разделе **Управление интерфейсами API** выберите **Добавить**, чтобы добавить новый API:  
![Добавление API][2]

6. Введите описательное **имя** для API.  
	
7. В поле **Источник** укажите **Доступные API**, чтобы выбрать встроенные API, а затем выберите **Microsoft Translator**:  
![выбор AIP Microsoft Translator][3]

8. Выберите **Настройки — Настроить обязательные параметры**:  
![настройка параметров API Microsoft Translator][4]

9. Введите *Идентификатор клиента* и *Секрет клиента* своего приложения Microsoft Translator. Если приложения нет, см. раздел "Регистрация приложения Microsoft Translator для использования с PowerApps" в этой статье, где описывается создание нужных значений идентификатора и секрета.

9. Нажмите кнопку **ОК**, чтобы завершить эти действия.

После их выполнения в вашу среду службы приложений будет добавлен новый API Microsoft Translator.


## (Необязательно.) Регистрация приложения Microsoft Translator для использования с PowerApps

Если у вас нет приложения Microsoft Translator с идентификатором и секретом, выполните следующие действия, чтобы создать приложение и получить нужные значения. 


1. Перейдите на страницу [разработчика Azure Data Market][5] и выполните вход со своей учетной записью Майкрософт. 

2. Выберите **Регистрация**.

3. В окне **Регистрация приложения**:  

	1. Введите значение в поле **Идентификатор клиента**.  
	2. Укажите **имя** приложения.  
	3. Введите фиктивное значение для параметра **URL-адрес перенаправления**. Например, введите *https://contosoredirecturl*.  
	4. Введите **описание**.  
	5. Нажмите кнопку **Создать**.  

	![Регистрация приложения][6]

Создается новое приложение Microsoft Translator. Вы можете использовать это приложение при настройке API Microsoft Translator на портале Azure. 

## См. раздел "Интерфейсы REST API".

Справочник по [REST API Microsoft Translator](../connectors/create-api-microsofttranslator.md).

## Сводка и дальнейшие действия
С помощью инструкций в этом разделе вы добавили API Microsoft Translator в службу PowerApps Enterprise. Теперь необходимо предоставить пользователям доступ к этому API, чтобы они могли добавлять его в свои приложения: 

[Добавление подключения и предоставление доступа пользователям](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-microsofttranslator/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-microsofttranslator/add-api.PNG
[3]: ./media/powerapps-create-api-microsofttranslator/select-microsofttranslator-api.PNG
[4]: ./media/powerapps-create-api-microsofttranslator/configure-microsofttranslator-api.PNG
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/powerapps-create-api-microsofttranslator/register-your-application.PNG
[7]: ./media/powerapps-create-api-microsofttranslator/browseall.png
[8]: ./media/powerapps-create-api-microsofttranslator/allresources.png

<!----HONumber=AcomDC_0309_2016-->


