Хотя MongoLab URI можно скопировать в код, рекомендуется настроить его в среде для простоты управления. Таким образом при изменении URI его можно обновить через портал Azure без обращения к коду.


1. На портале Azure нажмите кнопку **Веб-сайты**.
1. Щелкните имя веб-сайта в списке веб-сайтов.  
![WebSiteEntry][entry-website]  
Появится панель мониторинга веб-сайта.

1. Нажмите кнопку **Настройка** в строке меню.  
![WebSiteDashboardConfig][focus-mongolab-websitedashboard-config]

1. Перейдите ниже к разделу Строк подключения.  
![WebSiteConnectionStrings][focus-mongolab-websiteconnectionstring]

1. В качестве **Имени** введите MONGOLAB_URI.
1. В качестве **Value** вставьте строку подключения, полученную в предыдущем разделе.
1. Выберите **Настраиваемый** в выпадающем списке **Тип** (вместо используемого по умолчанию **SQLAzure**).
1. Нажмите кнопку **Сохранить** на панели инструментов.  
![SaveWebSite][button-website-save]

**Примечание.** Azure добавляет префикс **CUSTOMCONNSTR\_** в эту переменную, поэтому код выше ссылается на **CUSTOMCONNSTR\_MONGOLAB_URI.**

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png
<!--HONumber=42-->
