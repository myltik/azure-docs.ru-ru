Хотя MongoLab URI можно скопировать в код, рекомендуется настроить его в среде для простоты управления. Таким образом при изменении URI его можно обновить через портал Azure без обращения к коду.

1.  На портале Azure нажмите кнопку **Веб-сайты**.
2.  Выберите имя веб-сайта в списке веб-сайтов.
    ![WebSiteEntry][WebSiteEntry]
    Появится панель мониторинга веб-сайта.

3.  Нажмите кнопку **Настройка** в строке меню.  
    ![WebSiteDashboardConfig][WebSiteDashboardConfig]

4.  Перейдите ниже к разделу Строки подключения.
    ![WebSiteConnectionStrings][WebSiteConnectionStrings]

5.  В качестве **Имени** введите MONGOLAB\_URI.
6.  В качестве **Значение** вставьте строку подключения, полученную в предыдущем разделе.
7.  Выберите **Настраиваемый** в выпадающем списке **Тип** (вместо используемого по умолчанию **SQLAzure**).
8.  Нажмите кнопку **Сохранить** на панели инструментов.  
    ![SaveWebSite][SaveWebSite]

**Примечание.**Azure добавляет к этой переменной префикс **CUSTOMCONNSTR\_**, поэтому приведенный выше код ссылается на **CUSTOMCONNSTR\_MONGOLAB\_URI.**

  [WebSiteEntry]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
  [WebSiteDashboardConfig]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
  [WebSiteConnectionStrings]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
  [SaveWebSite]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png
