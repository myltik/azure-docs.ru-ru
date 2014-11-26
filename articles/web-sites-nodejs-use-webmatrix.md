<properties linkid="web-site-with-webmatrix" urlDisplayName="Website with WebMatrix" pageTitle="Node.js website with WebMatrix - Azure tutorial" metaKeywords="" description="A tutorial that teaches you how to WebMatrix to develop and deploy a Node.js application to an Azure website." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build and deploy a Node.js website to Azure using WebMatrix" authors="larryfr" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Создание и развертывание веб-сайта Node.js в Azure с использованием WebMatrix

В этом учебнике показано, как создать приложение Node.js и развернуть его на веб-сайте Azure с помощью WebMatrix. WebMatrix представляет собой бесплатное средство веб-разработки от корпорации Майкрософт, которое содержит все необходимое для разработки веб-сайта. WebMatrix включает несколько возможностей, которые делают использование Node.js более удобным, в том числе автозавершение кода, готовые шаблоны, а также поддержка редактором Jade, LESS и CoffeeScript. Дополнительные сведения о [WebMatrix для Azure][WebMatrix для Azure].

После завершения работы с этим учебником вы получите веб-сайт Node.js, работающий в Azure.

Снимок экрана завершенного приложения приведен ниже:

![Веб-сайт Node в Azure][Веб-сайт Node в Azure]

[WACOM.INCLUDE [создать-учетная запись-и-веб-сайт-примечание](../includes/create-account-and-websites-note.md)]

## Вход в Azure

Выполните следующие действия, чтобы создать веб-сайт Azure.

<div class="dev-callout"><strong>Примечание.</strong>
<p>Чтобы пройти этот учебный курс, потребуется учетная запись Azure с включенной возможностью веб-сайтов Windows Azure.</p>
<p>Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Бесплатная пробная версия Azure</a>.</p>
</div>

1.  Запуск WebMatrix
2.  Если это первый случай использования WebMatrix, будет выведен запрос на вход в Azure. В противном случае можно нажать кнопку **Вход** и выбрать команду **Добавить учетную запись** Выберите **Вход** с использованием своей учетной записи Майкрософт.

    ![Добавление учетной записи][Добавление учетной записи]

3.  Если вы уже зарегистрировались для учетной записи Azure, можно войти в систему с использованием учетной записи Майкрософт.

    ![Вход в Azure][Вход в Azure]

## Создание сайта с помощью встроенного шаблона для Azure

1.  На начальном экране нажмите кнопку **Создать** и выберите **Коллекция шаблонов**, чтобы создать новый веб-сайт из коллекции шаблонов.

    ![Новый сайт из коллекции шаблонов][Новый сайт из коллекции шаблонов]

2.  В диалоговом окне **Веб-сайт по шаблону** выберите **Узел**, а затем **Экспресс-сайт**. Наконец, нажмите кнопку **Далее**. Если отсутствуют какие-либо необходимые компоненты для шаблона **Экспресс-сайт**, будет предложено установить их.

    ![Выберите шаблон экспресс-сайта][Выберите шаблон экспресс-сайта]

3.  После входа в Azure вы можете создать веб-сайт Azure для локального сайта. Выберите уникальное имя и выберите центр обработки данных, где планируется создать сайт:

    ![Создание сайта в Azure][Создание сайта в Azure]

4.  По завершении создания веб-сайта WebMatrix откроется интегрированная среда разработки WebMatrix.

    ![Интегрированная среда разработки WebMatrix][Интегрированная среда разработки WebMatrix]

## Публикация изменений в Azure

1.  В WebMatrix нажмите кнопку **Публикация** на ленте **Главная**, чтобы открыть диалоговое окно **Просмотр публикации** для веб-сайта.

    ![Предварительный просмотр публикации][Предварительный просмотр публикации]

2.  Нажмите кнопку **Продолжить**. После завершения публикации URL-адрес веб-сайта на платформе Azure будет отображаться в нижней части интегрированной среды разработки WebMatrix.

    ![Публикация завершена][Публикация завершена]

3.  Щелкните по ссылке, чтобы открыть веб-сайт в браузере.

    ![Экспресс-сайт][Экспресс-сайт]

## Изменение и повторная публикация приложения

Вы можете с легкостью изменить и повторно опубликовать свое приложение. Здесь вы внесете простое изменение в заголовке в файле **index.jade** и повторно опубликуете приложение.

1.  В WebMatrix выберите **Файлы**, а затем откройте папку **Представления**. Откройте файл **index.jade**, дважды щелкнув его.

    ![WebMatrix — просмотр index.jade][WebMatrix — просмотр index.jade]

2.  Измените вторую строку следующим образом.

        p Welcome to #{title} with WebMatrix on Azure!

3.  Сохраните изменения и нажмите значок публикации. Наконец, нажмите кнопку **Продолжить** в диалоговом окне **Просмотр публикации** и дождитесь, пока обновление будет опубликовано.

    ![Предварительный просмотр публикации][1]

4.  После завершения публикации щелкните по ссылке, возвращенной по завершении процесса публикации, для просмотра обновленного сайта.

    ![Веб-сайт Node в Azure][Веб-сайт Node в Azure]

## Дальнейшие действия

Для получения дополнительных сведений о версиях Node.js, входящих в состав Windows Azure, а также для указания версии, которая будет использоваться в приложении, см. раздел [Указание версии Node.js в приложении Azure][Указание версии Node.js в приложении Azure].

При возникновении проблем с приложением после его развертывания в Windows Azure сведения о диагностике проблемы можно найти в разделе [Отладка приложения Node.js на веб-сайтах Windows Azure][Отладка приложения Node.js на веб-сайтах Windows Azure].

  [WebMatrix для Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409
  [Веб-сайт Node в Azure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png
  [Добавление учетной записи]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
  [Вход в Azure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
  [Новый сайт из коллекции шаблонов]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
  [Выберите шаблон экспресс-сайта]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png
  [Создание сайта в Azure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png
  [Интегрированная среда разработки WebMatrix]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
  [Предварительный просмотр публикации]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png
  [Публикация завершена]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
  [Экспресс-сайт]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
  [WebMatrix — просмотр index.jade]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
  [1]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
  [Указание версии Node.js в приложении Azure]: /ru-ru/documentation/articles/nodejs-specify-node-version-azure-apps/
  [Отладка приложения Node.js на веб-сайтах Windows Azure]: http://www.windowsazure.com/ru-ru/develop/nodejs/how-to-guides/Debug-Website/
