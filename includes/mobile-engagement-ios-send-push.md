### <a name="grant-access-to-your-push-certificate-to-mobile-engagement"></a>Предоставьте доступ к сертификату push-уведомлений для Служб мобильного взаимодействия
Чтобы разрешить Службам мобильного взаимодействия отправлять push-уведомления от вашего имени, необходимо предоставить доступ к сертификату. Это можно сделать путем настройки и ввода сертификата на портале Служб мобильного взаимодействия. Убедитесь, что получили сертификат P12, как описано в [документации Apple](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)

1. Перейдите на портал Служб мобильного взаимодействия. Убедитесь, что все правильно, и нажмите кнопку **Выполнить охват** внизу.
   
    ![](./media/mobile-engagement-ios-send-push/engage-button.png)
2. Щелкните станицу **Параметры** на портале Mobile Engagement. Выберите здесь раздел **Системное push-уведомление** , чтобы передать ваш сертификат P12:
   
    ![](./media/mobile-engagement-ios-send-push/engagement-portal.png)
3. Выберите сертификат P12, отправьте его и введите пароль:
   
    ![](./media/mobile-engagement-ios-send-push/native-push-settings.png)

## <a id="send"></a>Отправка уведомления в приложение
Создадим простую кампанию push-уведомлений, которая отправит push-уведомление в приложение.

1. Перейдите на вкладку **Рекламная кампания** на портале Служб мобильного взаимодействия.
2. Щелкните **Создать объявление** , чтобы создать кампанию push-уведомлений.
   
    ![](./media/mobile-engagement-ios-send-push/new-announcement.png)
3. Настройте первые поля кампании:
   
    ![](./media/mobile-engagement-ios-send-push/campaign-first-params.png)
   
   * Задайте **имя** кампании. 
   * Выберите **Время доставки** **Только вне приложения** — это простой тип push-уведомлений Apple с произвольным текстом.
   * В тексте уведомления введите сначала **заголовок** в первой строке push-уведомления.
   * Затем введите **сообщение** во второй строке.
4. Прокрутите окно вниз и в разделе содержимого выберите пункт **Только уведомления**
   
    ![](./media/mobile-engagement-ios-send-push/campaign-content.png)
5. Вы настроили простейшую кампанию. Теперь прокрутите окно вниз и нажмите кнопку **Создать** , чтобы сохранить push-уведомлений. 
6. Наконец, щелкните **Активировать** , чтобы отправить push-уведомление. 
   
    ![](./media/mobile-engagement-ios-send-push/campaign-activate.png)
7. Вы сможете получить уведомление на устройстве iOS в центре уведомлений следующим образом:
   
    ![](./media/mobile-engagement-ios-send-push/iphone-notification.png)
8. Если с вашим устройством iOS связаны часы Apple Watch, уведомление на отображается на Apple Watch:
   
    ![](./media/mobile-engagement-ios-send-push/apple-watch.png)

