При подготовке базы данных MongoLab, MongoLab передает подключение URI в Azure в стандартном формате строки подключения в MongoDB. Это значение используется для установки подключения MongoDB через драйвер MongoDB по вашему выбору. Дополнительные сведения о строках подключения см. в разделе [Подключения](http://www.mongodb.org/display/DOCS/Connections) в mongodb.org.

**Этот URI содержит имя пользователя и пароль вашей базы данных. Считайте это конфиденциальной информацией и не передавайте ее.**

Вы можете найти этот URI на портале Azure, выполнив следующие действия:

1. Выберите **Надстройки**. ![AddonsButton][button-addons]
1. Найдите нужную службу MongoLab в списке надстроек. ![MongolabEntry][entry-mongolabaddon]
1. Чтобы перейти на страницу надстройки, выберите ее имя.
1. Нажмите кнопку **Сведения о подключении** ![ConnectionInfoButton][button-connectioninfo] Отобразится URI MongoLab: ![ConnectionInfoScreen][screen-connectioninfo]  
1.  Нажмите кнопку буфера обмена справа от значения MONGOLAB\_URI, чтобы скопировать полное значение в буфер обмена.

[entry-mongolabaddon]: ./media/howto-get-connectioninfo-mongolab/entry-mongolabaddon.png
[button-connectioninfo]: ./media/howto-get-connectioninfo-mongolab/button-connectioninfo.png
[screen-connectioninfo]: ./media/howto-get-connectioninfo-mongolab/dialog-mongolab_connectioninfo.png
[button-addons]: ./media/howto-get-connectioninfo-mongolab/button-addons.png

<!---HONumber=August15_HO6-->