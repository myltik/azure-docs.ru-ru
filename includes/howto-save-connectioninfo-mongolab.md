Хотя MongoLab URI можно скопировать в код, рекомендуется настроить его в среде для простоты управления. Таким образом при изменении URI его можно обновить через портал Azure без обращения к коду.

1. На портале Azure выберите **Веб-приложения**.
2. Щелкните имя веб-приложения в списке веб-приложений.  
   ![WebAppEntry][entry-website]  
   Откроется панель мониторинга веб-приложения.
3. Нажмите кнопку **Настройка** в строке меню.  
   ![WebAppDashboardConfig][focus-mongolab-websitedashboard-config]
4. Прокрутите вниз к разделу "Строки подключения".  
   ![WebAppConnectionStrings][focus-mongolab-websiteconnectionstring]
5. В поле **Имя** введите MONGOLAB_URI.
6. В качестве **Значение**вставьте строку подключения, полученную в предыдущем разделе.
7. Выберите **Настраиваемый** в выпадающем списке **Тип** (вместо используемого по умолчанию **SQLAzure**).
8. Нажмите кнопку **Сохранить** на панели инструментов.  
   ![SaveWebApp][button-website-save]

**Примечание.** Azure добавляет префикс **CUSTOMCONNSTR\_** в эту переменную, поэтому код выше ссылается на **CUSTOMCONNSTR\_MONGOLAB_URI.**

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png


<!--HONumber=Jan17_HO3-->


