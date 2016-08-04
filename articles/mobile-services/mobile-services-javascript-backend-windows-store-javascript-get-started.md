<properties
	pageTitle="Приступая к работе с мобильными службами в приложениях Магазина Windows на JavaScript | Мобильные службы Azure"
	description="Следуйте указаниям этого учебника, чтобы приступить к использованию мобильных служб Azure для разработки приложений Магазина Windows на JavaScript."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="javascript"
	ms.topic="get-started-article"
	ms.date="07/21/2016"
	ms.author="glenga"/>

# Приступая к работе с мобильными службами

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Аналогичные сведения для мобильных приложений см. в статье [Создание приложения Windows в мобильных приложениях](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started.md).

В этом учебнике показано, как добавить облачную серверную службу в приложение Магазина Windows на JavaScript с помощью мобильных служб Azure. В этом учебнике вы создадите новую мобильную службу и простое приложение *To do list*, хранящее данные приложения в новой мобильной службе. Мобильная служба, которая будет создана, использует JavaScript для бизнес-логики на стороне сервера.

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
* [Visual Studio 2013 Express для Windows]

## Создание новой мобильной службы

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Создание нового приложения для Магазина Windows

После создания мобильной службы можно выполнить простые действия для быстрого запуска на классическом портале Azure, чтобы создать приложение Магазина Windows 8.1 на JavaScript, которое подключается к вашей мобильной службе.

1.  На [классическом портале Azure] щелкните **Мобильные службы** и выберите только что созданную мобильную службу.


2. На вкладке быстрого начала работы щелкните **Windows** в разделе **Выбор платформы**, а затем разверните узел **Создание нового приложения для магазина Windows**.

3. Если вы еще не сделали этого, загрузите и установите [Visual Studio 2013][Visual Studio 2013 Express for Windows] на локальном компьютере или виртуальной машине.

4. Чтобы создать таблицу для хранения данных приложения, нажмите кнопку **Создание таблицы TodoItem**.

5. В разделе **Загрузить и запустить приложение** выберите язык для своего приложения, а затем нажмите кнопку **Загрузить**.

  	При этом будут загружены файлы проекта для примера по созданию приложения *To do list*, подключаемого к вашей мобильной службе. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

## Запустите приложение Windows

Последний раздел учебника — построение и выполнение нового приложения.

1. Перейдите в расположение, где были сохранены сжатые файлы проекта, извлеките файлы на компьютере и откройте файл решения в Visual Studio.

2. Нажмите клавишу **F5** для повторного построения проекта, после чего запустите приложение.

3. В приложении введите содержательный текст, например *Работа с учебником*, в поле **Вставить в TodoItem**, затем щелкните **Сохранить**.

   	Отправляет запрос POST в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются во втором столбце в приложении.

4. (Необязательно) Запустите приложение еще раз и обратите внимание, что данные, сохраненные на предыдущем этапе, загружаются из мобильной службы после запуска приложения.
 
4. На [классическом портале Azure] щелкните вкладку **Данные**, а затем щелкните таблицу **TodoItems**.

   	Это позволяет просматривать данные, добавленные в таблицу приложением.

>[AZURE.NOTE] Можно просмотреть код доступа к мобильной службе для запроса и вставки данных, который находится в файле default.js.

## Дальнейшие действия
Теперь, когда вы закончили изучение краткого руководства, узнайте, как работать с [клиентом мобильных служб для HTML или JavaScript](mobile-services-html-how-to-use-client-library.md).

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Visual Studio 2013 Express for Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Visual Studio 2013 Express для Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[классическом портале Azure]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0727_2016-->