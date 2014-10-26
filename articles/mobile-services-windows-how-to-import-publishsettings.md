<properties linkid="develop-mobile-how-to-guides-import-publish-settings" urlDisplayName="Import your subscription publish settings file in Visual Studio 2013" pageTitle="Import your publish settings file in Visual Studio 2013 | Mobile Services" metaKeywords="Azure import publishsettings, mobile services" description="Learn how to import a subscription publish settings file for your Azure Mobile Services application in Visual Studio 2013." title="Import your subscription publish settings file in Visual Studio 2013" documentationCenter="Mobile" services="" solutions="" manager="" editor="" videoId="" scriptId="" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Импорт файла параметров публикации подписки в Visual Studio 2013

Перед созданием мобильной службы необходимо импортировать в Visual Studio файл параметров публикации из подписки Azure. Это позволяет Visual Studio подключиться к Azure от вашего имени.

1.  В Visual Studio 2013 откройте обозреватель решений, щелкните правой кнопкой проект, затем выберите команду **Добавить** и **Подключенная служба...**

    ![добавление подключенной службы][добавление подключенной службы]

2.  В диалоговом окне диспетчера служб нажмите **Создать службу...**, выберите **\<Импорт...\>** в разделе **Подписка** в диалоговом окне «Создание мобильной службы».

    ![создание новой мобильной службы из VS 2013][создание новой мобильной службы из VS 2013]

3.  В подписках Azure для импорта нажмите **Скачать файл подписки**, войдите в учетную запись Azure (если требуется), щелкните кнопку **Сохранить**, когда обозреватель запросит сохранить файл.

    ![загрузка файла подписки в VS][загрузка файла подписки в VS]

    <div class="dev-callout"><strong>Примечание.</strong> <p>Окно входа в систему отображается в браузере, который может находиться за окном Visual Studio. Запишите себе, где вы сохранили загруженный файл .publishsettings. Этот шаг можно пропустить, если проект уже подключен к вашей подписке Azure.</p></div>

4.  Нажмите **Обзор**, найдите расположение, где вы сохранили файл .publishsettings, выберите его, затем нажмите **Открыть** и **Импортировать**.

    ![импорт подписки в VS][импорт подписки в VS]

    Visual Studio импортирует данные, необходимые для подключения к вашей подписке Azure. Если в вашей подписке уже есть одна или несколько существующих мобильных служб, отображаются имена служб.

    <div class="dev-callout"><strong>Примечание о безопасности.</strong> <p>После импорта настроек публикации удалите скачанный PUBLISHSETTINGS-файл, поскольку он содержит информацию, которая может использоваться другими пользователями для доступа к вашей учетной записи. Защитите файл, если планируете сохранить его для использования в других подключенных проектах приложений.</p></div>

<!-- Anchors.  Images.  URLs. -->

  [добавление подключенной службы]: ./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png
  [создание новой мобильной службы из VS 2013]: ./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png
  [загрузка файла подписки в VS]: ./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png
  [импорт подписки в VS]: ./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png
