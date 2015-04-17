<properties 
	pageTitle="Удаленная отладка приложения API службы приложений Azure" 
	description="Использование Visual Studio для удаленной отладки приложения API службы приложений Azure." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="bradyg;tarcher"/>

# Удаленная отладка приложения API службы приложений Azure 

## Обзор

Возможности удаленной отладки Visual Studio были расширены и теперь включают поддержку приложений API в службе приложений Azure. Это означает, что вы можете использовать знакомые средства отладки для просмотра выполняющегося кода в Azure. В этом разделе показано, как использовать **Клиент приложений API** Visual Studio для создания клиентского кода, который вызывает развернутое приложение API. Затем вы отладите одновременно клиентское приложение и приложение API во время выполнения приложения API в облаке.

Это последний из трех учебников серии:

1. В учебнике [Создание приложения API](app-service-dotnet-create-api-app.md) вы создали проект приложения API. 
* В учебнике [Развертывание приложения API](app-service-dotnet-deploy-api-app.md) вы развернули приложение API в подписке Azure.
* В этом учебнике вы используете Visual Studio для удаленной отладки кода во время его выполнения в Azure.

## Создание клиента приложения API 

Средства приложения API в Visual Studio упрощают написание кода на C#, который вызывает приложения API Azure с рабочего стола, из хранилища и мобильных приложений. 

В Visual Studio откройте решение, которое содержит приложение API из [первого](app-service-dotnet-create-api-app.md) учебника. Щелкните правой кнопкой мыши решение и последовательно выберите пункты **Добавить** > **Новый проект**.

![Add a new project](./media/app-service-dotnet-remotely-debug-api-app/01-add-new-project-v3.png)

Выберите категорию **Рабочий стол Windows** и шаблон проекта **Консольное приложение**.

![Add a new project](./media/app-service-dotnet-remotely-debug-api-app/02-contact-list-console-project-v3.png)

Щелкните правой кнопкой мыши проект консольного приложения и последовательно выберите пункты **Добавить** > **Клиент приложения API Azure**. 

![Add a new Client](./media/app-service-dotnet-remotely-debug-api-app/03-add-azure-api-client-v3.png)
	
В диалоговом окне выберите параметр **Скачать**. В раскрывающемся списке щелкните приложение API, созданное ранее. Нажмите кнопку **ОК**. 

![Generation Screen](./media/app-service-dotnet-remotely-debug-api-app/04-select-the-api-v3.png)

Мастер скачает файл метаданных API и создаст типизированный интерфейс для вызова приложения API.

![Generation Happening](./media/app-service-dotnet-remotely-debug-api-app/05-metadata-downloading-v3.png)

После создания кода вы увидите новую папку в обозревателе решений с именем приложения API. В этой папке содержится код, который реализует модели клиента и данных. 

![Generation Complete](./media/app-service-dotnet-remotely-debug-api-app/06-code-gen-output-v3.png)

Откройте файл **Program.cs** из корня проекта и замените метод **Main** следующим кодом: 

	static void Main(string[] args)
    {
        var client = new ContactsList();

        // Send GET request.
        var contacts = client.Contacts.Get();
        foreach (var c in contacts)
        {
            Console.WriteLine("{0}: {1} {2}",
                c.Id, c.Name, c.EmailAddress);
        }

        // Send POST request.
		client.Contacts.Post(new Models.Contact
	    {
	        EmailAddress = "lkahn@contoso.com",
	        Name = "Loretta Kahn",
	        Id = 4
	    });

        Console.WriteLine("Finished");
        Console.ReadLine();
    }

В меню **Вид** выберите **Обозреватель серверов**. В окне обозревателя серверов разверните узел *Azure > Службы приложений**. Найдите группу ресурсов, созданную при развертывании приложения API. Щелкните правой кнопкой мыши узел приложения API и выберите **Подключить отладчик**. 

![Attaching debugger](./media/app-service-dotnet-remotely-debug-api-app/08-attach-debugger-v3.png)

Удаленный отладчик попытается подключиться. В некоторых случаях может потребоваться повторно щелкнуть пункт **Подключить отладчик**, чтобы установить подключение, поэтому при сбое повторите попытку.

![Attaching debugger](./media/app-service-dotnet-remotely-debug-api-app/09-attaching-v3.png)

После установки подключения откройте файл **ContactsController.cs** в проекте приложения API и добавьте точки останова в методы `Get` и `Post`. Сначала они могут не отображаться как активные, но, если удаленный отладчик подключен, вы можете начать отладку. 

![Applying breakpoints to controller](./media/app-service-dotnet-remotely-debug-api-app/10-breakpoints-v3.png)

Для отладки щелкните правой кнопкой мыши консольное приложение в обозревателе решений и последовательно выберите пункты **Отладка** > **Запустить новый экземпляр**. Теперь вы можете отладить приложение API удаленно, а клиентское приложение - локально и увидеть весь поток данных. 

На следующем снимке экрана показан отладчик, достигший точки останова для метода `Post`. Вы увидите, что была выполнена десериализация контактных данных из клиента в строго типизированный объект `Contact`. 

![Debugging the local client to hit the API](./media/app-service-dotnet-remotely-debug-api-app/12-debugging-live-v3.png)

## Сводка

Удаленная отладка для приложений API упрощает просмотр выполнения кода в службе приложений Azure. Подробные данные диагностики и отладки доступны непосредственно в Visual Studio IDE для удаленно запущенных приложений API Azure.


<!--HONumber=49-->