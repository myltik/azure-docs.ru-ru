При подготовке базы данных MongoLab, MongoLab передает подключение URI в Azure в стандартном формате строки подключения в MongoDB. Это значение используется для установки подключения MongoDB через драйвер MongoDB по вашему выбору. Дополнительные сведения о строках подключения см. в разделе [Подключения][Подключения] в mongodb.org.

**Этот URI содержит имя пользователя и пароль вашей базы данных. Считайте это конфиденциальной информацией и не передавайте ее.**

Вы можете найти этот URI на портале Azure, выполнив следующие действия:

1.  Выберите **«Надстройки»**.
    ![AddonsButton][AddonsButton]
2.  Найдите нужную службу MongoLab в списке надстроек.
    ![MongolabEntry][MongolabEntry]
3.  Чтобы перейти на страницу надстройки, выберите ее имя.
4.  Нажмите **Сведения о подключении**.
    ![ConnectionInfoButton][ConnectionInfoButton]
    URI MongoLab отображает:
    ![ConnectionInfoScreen][ConnectionInfoScreen]
5.  Нажмите кнопку буфера обмена справа от значения MONGOLAB\_URI, чтобы скопировать полное значение в буфер обмена.

  [Подключения]: http://www.mongodb.org/display/DOCS/Connections
  [AddonsButton]: ./media/howto-get-connectioninfo-mongolab/button-addons.png
  [MongolabEntry]: ./media/howto-get-connectioninfo-mongolab/entry-mongolabaddon.png
  [ConnectionInfoButton]: ./media/howto-get-connectioninfo-mongolab/button-connectioninfo.png
  [ConnectionInfoScreen]: ./media/howto-get-connectioninfo-mongolab/dialog-mongolab_connectioninfo.png
