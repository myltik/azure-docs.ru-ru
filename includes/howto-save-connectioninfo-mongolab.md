Хотя MongoLab URI можно скопировать в код, рекомендуется настроить его в среде для простоты управления. Таким образом при изменении URI его можно обновить через портал Azure без обращения к коду.


1. На портале Azure выберите **Веб-приложения**.
1. Щелкните имя веб-приложения в списке веб-приложений. ![WebAppEntry][entry-website] Откроется панель мониторинга веб-приложения.

1. Нажмите кнопку **Настройка** в строке меню. ![WebAppDashboardConfig][focus-mongolab-websitedashboard-config]

1. Прокрутите вниз к разделу "Строки подключения". ![WebAppConnectionStrings][focus-mongolab-websiteconnectionstring]

1. В качестве **Имени** введите MONGOLAB_URI.
1. В качестве **Значение** вставьте строку подключения, полученную в предыдущем разделе.
1. Выберите **Настраиваемый** в выпадающем списке **Тип** (вместо используемого по умолчанию **SQLAzure**).
1. Нажмите кнопку **Сохранить** на панели инструментов. ![SaveWebApp][button-website-save]

**Примечание.** Azure добавляет префикс **CUSTOMCONNSTR_** в эту переменную, поэтому код выше ссылается на **CUSTOMCONNSTR_MONGOLAB_URI.**

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png

<!---HONumber=July15_HO3-->