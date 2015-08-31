
* [Зарегистрируйте идентификатор для своего приложения](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingProfiles/MaintainingProfiles.html#//apple_ref/doc/uid/TP40012582-CH30-SW991). Создайте явный идентификатор приложения (не подстановочный знак идентификатора приложения), а для параметра **ИД пакета** используйте тот же **ИД пакета**, что и в проекте быстрого запуска Xcode. Также необходимо выбрать параметр **Push-уведомления**. 

* Теперь [включите push-уведомления для приложения](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW6). Можно создать сертификат SSL "Разработка" или "Распространение" (не забудьте позже выбрать соответствующий параметр на портале Azure).

* После этого [убедитесь, что идентификатор приложения обеспечивает работу push-уведомлений](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW8).

* Наконец, [обновите профили подготовки в проекте быстрого запуска Xcode](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW10) и [убедитесь, что профиль подготовки был создан или повторно сгенерирован, чтобы включить push-уведомления](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW12).

<!---HONumber=August15_HO8-->