
## <a id="register"></a>Регистрация приложения для работы с push-уведомлениями

* [Зарегистрируйте идентификатор для своего приложения](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingProfiles/MaintainingProfiles.html#//apple_ref/doc/uid/TP40012582-CH30-SW991). При регистрации приложения установите необязательный флажок **Push-уведомления** на панели **Службы приложений**.

> [AZURE.NOTE]Создайте явный идентификатор приложения (не подстановочный знак идентификатора приложения), а для параметра **ИД пакета** используйте тот же **ИД пакета**, что и в проекте быстрого запуска Xcode. Также очень важно при регистрации идентификатора приложения выбрать параметр **Push-уведомления** на панели **Службы приложений** . Push-уведомления не будут работать, если этот флажок не установлен.

* Теперь [включите push-уведомления для приложения](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW6). Можно создать сертификат SSL "Разработка" или "Распространение" (не забудьте позже выбрать соответствующий параметр — "Изолированная среда" или "Рабочая среда" — на портале мобильных служб).

* Далее [убедитесь, что идентификатор приложения обеспечивает работу push-уведомлений](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW8).

* Наконец, [обновите профили подготовки в проекте быстрого запуска Xcode](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW10) и затем [убедитесь, что профиль подготовки был создан или повторно сгенерирован, чтобы включить push-уведомления](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW12).

<!---HONumber=July15_HO3-->