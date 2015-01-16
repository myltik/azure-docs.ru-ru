<properties urlDisplayName="Website with WebMatrix" pageTitle="Веб-сайт node.js с использованием WebMatrix - учебник по Azure" metaKeywords="" description="Учебник, в котором показано, как использовать WebMatrix для разработки и развертывания приложения Node.js на веб-сайте Azure." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build and deploy a Node.js website to Azure using WebMatrix" authors="larryfr" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />


# Создание и развертывание веб-сайта Node.js в Azure с использованием WebMatrix

В этом учебнике показано, как создать приложение Node.js и развернуть его на веб-сайте Azure с помощью WebMatrix. WebMatrix представляет собой бесплатное средство веб-разработки от корпорации Майкрософт, которое содержит все необходимое для разработки веб-сайта. WebMatrix включает несколько возможностей, которые делают использование Node.js более удобным, в том числе автозавершение кода, готовые шаблоны, а также поддержка редактором Jade, LESS и CoffeeScript. Дополнительные сведения о [WebMatrix для Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409).

После завершения работы с этим учебником вы получите веб-сайт Node.js, работающий в Azure.
 
Снимок экрана завершенного приложения приведен ниже.

![Azure node Web site][webmatrix-node-completed]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Вход в Azure

Выполните следующие действия, чтобы создать веб-сайт Azure.

<div class="dev-callout"><strong>Примечание.</strong>
<p>Чтобы пройти этот учебный курс, потребуется учетная запись Azure с включенной возможностью веб-сайтов Windows Azure.</p>
<p>Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Бесплатная пробная версия Azure</a>.</p>
</div>
<br />

1. Запуск WebMatrix
2. Если это первый случай использования WebMatrix, будет выведен запрос на вход в Azure.  В противном случае можно нажать кнопку **Вход** и выбрать команду **Добавить учетную запись**.  Выберите **Вход** с использованием своей учетной записи Майкрософт.

	![Add Account][addaccount]

3. Если вы уже зарегистрировались для учетной записи Azure, можно войти в систему с использованием учетной записи Майкрософт.

	![Sign into Azure][signin]	


##  Создание сайта с помощью встроенного шаблона для Azure

1. На начальном экране нажмите кнопку **Создать** и выберите **Коллекция шаблонов**, чтобы создать новый веб-сайт из коллекции шаблонов:

	![New site from Template Gallery][sitefromtemplate]

2. В диалоговом окне **Веб-сайт по шаблону** выберите **Node**, а затем - **Экспресс-сайт**. Наконец, нажмите кнопку **Далее**. Если отсутствуют какие-либо необходимые компоненты для шаблона **Экспресс-сайт**, вам будет предложено установить их.

	![select express template][webmatrix-templates]

3. После входа в Azure вы можете создать веб-сайт Azure для локального сайта.  Выберите уникальное имя и выберите центр обработки данных, где планируется создать сайт: 

	![Create site on Azure][nodesitefromtemplateazure]
	
4. По завершении создания веб-сайта WebMatrix откроется интегрированная среда разработки WebMatrix.

	![webmatrix ide][webmatrix-ide]

##Публикация изменений в Azure

1. В WebMatrix нажмите кнопку **Публикация** на ленте **Главная**, чтобы открыть диалоговое окно **Просмотр публикации** для веб-сайта.

	![publish preview][webmatrix-node-publishpreview]

2. Нажмите кнопку **Продолжить**. После завершения публикации URL-адрес веб-сайта на платформе Azure будет отображаться в нижней части интегрированной среды разработки WebMatrix.

	![publish complete][webmatrix-publish-complete]

3. Щелкните по ссылке, чтобы открыть веб-сайт в браузере.

	![Express web site][webmatrix-node-express-site]

##Изменение и повторная публикация приложения

Вы можете легко изменить и повторно опубликовать свое приложение. Здесь вы внесете простое изменение в заголовке в файле **index.jade** и повторно опубликуете приложение.

1. В WebMatrix выберите **Файлы**, а затем откройте папку **Представления**. Откройте файл **index.jade**, дважды щелкнув его.

	![webmatrix viewing index.jade][webmatrix-modify-index]

2. Измените вторую строку следующим образом.

		p Welcome to #{title} with WebMatrix on Azure!

3. Сохраните изменения и нажмите значок публикации. Наконец, нажмите кнопку **Продолжить** в диалоговом окне **Просмотр публикации** и дождитесь, пока обновление не будет опубликовано.

	![publish preview][webmatrix-republish]

4. После завершения публикации щелкните по ссылке, возвращенной по завершении процесса публикации, для просмотра обновленного сайта.

	![Azure node Web site][webmatrix-node-completed]

##Дальнейшие действия

Для получения дополнительных сведений о версиях Node.js, входящих в состав Windows Azure, а также для указания версии, которая будет использоваться в приложении, см. раздел [Установка версии Node.js в приложении Azure](/ru-ru/documentation/articles/nodejs-specify-node-version-azure-apps/).

При возникновении проблем с приложением после его развертывания в Windows Azure сведения о диагностике проблемы можно найти в разделе [Отладка приложения Node.js для веб-сайтов Windows Azure](http://www.windowsazure.com/ru-ru/develop/nodejs/how-to-guides/Debug-Website/).


[Портал управления Azure]: http://manage.windowsazure.com
[Веб-сайт WebMatrix]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix для Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

[Публикация веб-сайта Azure с помощью Git]: /ru-ru/develop/nodejs/common-tasks/publishing-with-git/
[бесплатно]: /ru-ru/pricing/free-trial
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
