.<properties 
	pageTitle="Создание и развертывание веб-приложения Node.js в Azure с использованием WebMatrix" 
	description="В этом учебнике показано, как с помощью WebMatrix разработать приложение Node.js и развернуть его в веб-приложения службы приложений Azure." 
	services="app-service\web" 
	documentationCenter="nodejs" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

.<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="08/11/2016"
	ms.author="robmcm"/>


# Создание и развертывание веб-приложения Node.js в Azure с использованием WebMatrix

В этом учебнике показано, как с помощью WebMatrix разработать приложение Node.js и развернуть его в веб-приложения [службы приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714). WebMatrix представляет собой бесплатное средство веб-разработки от корпорации Майкрософт, которое содержит все необходимое для разработки веб-сайта или веб-приложения. WebMatrix включает несколько возможностей, которые делают использование Node.js более удобным, в том числе автозавершение кода, готовые шаблоны, а также поддержка редактором Jade, LESS и CoffeeScript. Дополнительные сведения о [WebMatrix](https://www.microsoft.com/web/webmatrix/)

После завершения работы с этим учебником вы получите веб-приложение Node.js, работающее в службе приложений Azure.
 
Снимок экрана завершенного приложения приведен ниже:

![Веб-сайт Node в Azure][webmatrix-node-completed]

[AZURE.INCLUDE [замечание по созданию учетной записи и веб-сайтов](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.

## Вход в Azure

Выполните следующие действия, чтобы создать веб-приложение в службе приложений Azure.

1. Запуск WebMatrix
2. Если это первый случай использования WebMatrix, будет выведен запрос на вход в Azure. В противном случае можно нажать кнопку **Вход** и выбрать команду **Добавить учетную запись** Выберите **Вход** с использованием своей учетной записи Майкрософт.

	![Добавление учетной записи][addaccount]

3. Если вы уже зарегистрировались для учетной записи Azure, можно войти в систему с использованием учетной записи Майкрософт.

	![Вход в Azure][signin]


## Создание сайта с помощью встроенного шаблона для Azure

1. На начальном экране нажмите кнопку **Создать** и выберите **Коллекция шаблонов**, чтобы создать новый веб-сайт из коллекции шаблонов.

	![Новый сайт из коллекции шаблонов][sitefromtemplate]

2. В диалоговом окне **Веб-сайт по шаблону** выберите **Узел**, а затем **Экспресс-сайт**. Наконец, нажмите кнопку **Далее**. Если отсутствуют какие-либо необходимые компоненты для шаблона **Экспресс-сайт**, будет предложено установить их.

	![Выберите шаблон экспресс-сайта][webmatrix-templates]

3. После входа в Azure вы можете создать веб-приложение службы приложений Azure для локального сайта. Выберите уникальное имя и центр обработки данных, где будет создано веб-приложение службы приложений Azure:

	![Создание сайта в Azure][nodesitefromtemplateazure]
	
4. По завершении создания локального сайта и веб-приложения службы приложений Azure откроется интегрированная среда разработки WebMatrix.

	![Интегрированная среда разработки WebMatrix][webmatrix-ide]

##Публикация изменений в Azure

1. В WebMatrix нажмите кнопку **Публикация** на ленте **Главная**, чтобы открыть диалоговое окно **Просмотр публикации** для сайта.

	![Предварительный просмотр публикации][webmatrix-node-publishpreview]

2. Нажмите кнопку **Продолжить**. По завершении публикации URL-адрес веб-приложения службы приложений Azure будет отображаться в нижней части интегрированной среды разработки WebMatrix

	![Публикация завершена][webmatrix-publish-complete]

3. Щелкните ссылку, чтобы открыть веб-приложение службы приложений в браузере.

	![Экспресс веб-приложение][webmatrix-node-express-site]

##Изменение и повторная публикация приложения

Вы можете с легкостью изменить и повторно опубликовать свое приложение. Здесь вы внесете простое изменение в заголовке в файле **index.jade** и повторно опубликуете приложение.

1. В WebMatrix выберите **Файлы**, а затем откройте папку **Представления**. Откройте файл **index.jade**, дважды щелкнув его.

	![WebMatrix — просмотр index.jade][webmatrix-modify-index]

2. Измените строку параграфа следующим образом.

		p Welcome to #{title} with WebMatrix on Azure!

3. Сохраните изменения и нажмите значок публикации. Наконец, нажмите кнопку **Продолжить** в диалоговом окне **Просмотр публикации** и дождитесь, пока обновление будет опубликовано.

	![Предварительный просмотр публикации][webmatrix-republish]

4. По завершении публикации щелкните ссылку, возвращенную по завершении процесса публикации, для просмотра обновленного веб-приложения службы приложений.

	![Веб-приложение Node в Azure][webmatrix-node-completed]

##Дальнейшие действия

Для получения дополнительных сведений о версиях Node.js, входящих в состав Microsoft Azure, а также для указания версии, которая будет использоваться в приложении, см. раздел [Указание версии Node.js в приложении Azure](../nodejs-specify-node-version-azure-apps.md).

При возникновении проблем с приложением после его развертывания в Azure сведения о диагностике проблемы можно найти в разделе [Отладка веб-приложения Node.js в службе приложений Azure](web-sites-nodejs-debug.md).

## Изменения
* Указания по изменениям при переходе от веб-сайтов к службе приложений см. в разделе [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

[WebMatrix WebSite]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix for Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

[webmatrix-node-completed]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png
[webmatrix-templates]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png

[webmatrix-node-publishpreview]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png

[webmatrix-ide]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
[webmatrix-publish-complete]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
[webmatrix-node-express-site]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
[webmatrix-modify-index]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
[webmatrix-republish]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
[addaccount]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
[nodesitefromtemplateazure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png
 

<!---HONumber=AcomDC_0817_2016-->