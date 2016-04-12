<properties
	pageTitle="Добавление API Excel в среду PowerApps Enterprise | Microsoft Azure"
	description="Создание или настройка нового API Excel в среде службы приложений организации"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>

# Создание API Excel в PowerApps Enterprise

Добавьте API Excel в среду службы приложений (клиента) организации.

## Создание API на портале Azure

1. Войдите на [портал Azure](https://portal.azure.com/) с помощью рабочей учетной записи. Например, это может быть адрес *ИмяПользователя*@*ВашаКомпания*.com. При этом выполняется автоматический вход в подписку вашей компании.
 
2. На панели задач выберите **Обзор**: ![][4]

3. Вы можете прокрутить список и найти PowerApps или набрать *powerapps* на клавиатуре: ![][5]

4. В **PowerApps** выберите **Управление интерфейсами API**: ![Переход к зарегистрированным API][1]

5. В разделе **Управление интерфейсами API** выберите **Добавить**, чтобы добавить новый API: ![Добавление API][2]

6. Введите описательное **имя** API.
	
7. В качестве **источника** укажите **доступные API**, чтобы выбрать встроенные API, а затем выберите **Excel**: ![выбор API Excel][3]

8. Нажмите кнопку **ОК**, чтобы завершить эти действия.

После их выполнения в среду службы приложений вашей организации будет добавлен новый API Excel.

## Сводка и дальнейшие действия
С помощью инструкций в этом разделе вы добавили API Excel в свою среду PowerApps Enterprise. Теперь необходимо предоставить пользователям доступ к этому API, чтобы они могли добавить его в свои приложения:

[Добавление подключения и предоставление доступа пользователям](powerapps-manage-api-connection-user-access.md)



<!--References-->
[1]: ./media/powerapps-create-api-excel/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-excel/add-api.PNG
[3]: ./media/powerapps-create-api-excel/select-excel-api.PNG
[4]: ./media/powerapps-create-api-excel/browseall.png
[5]: ./media/powerapps-create-api-excel/allresources.png

<!---HONumber=AcomDC_0309_2016-->