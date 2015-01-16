
Основой для этого учебника служит [приложение GetStartedWithMobileServices][приложение GetStartedWithMobileServices] — проект универсального приложения Windows в Visual Studio 2013. Пользовательский интерфейс этого приложения такой же, как у приложения, которое создается при быстром начале работы с мобильными службами, за исключением того, что добавленные элементы хранятся локально в памяти.

1.  Загрузите версию примера приложения GetStartedWithMobileServices на C\# с [веб-сайта с примерами кода для разработчиков].

2.  В Visual Studio 2013 откройте загруженный проект и просмотрите файл MainPage.xaml.cs в папке проекта GetStartedWithData.Shared.

   Обратите внимание: добавленные объекты **TodoItem** хранятся в объекте **ObservableCollection\<TodoItem\>** во внутренней памяти.

3.  Нажмите клавишу **F5** для повторного построения проекта, после чего запустите приложение.

4.  В приложении введите какой-либо текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**

    ![][0]

   Обратите внимание на то, что сохраненный текст отображается.

5.  Щелкните правой кнопкой мыши проект Windows Phone 8.1, выберите пункт **Назначить запускаемым проектом** и нажмите клавишу **F5**, чтобы запустить приложение Магазина Windows Phone.

    ![][1]

6.  Повторите шаги 3 и 4, чтобы убедиться в том, что пример ведет себя аналогичным образом.

  [приложение GetStartedWithMobileServices]: http://go.microsoft.com/fwlink/p/?LinkID=510826
  [0]: ./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup.png
  [1]: ./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup-wp8.png
