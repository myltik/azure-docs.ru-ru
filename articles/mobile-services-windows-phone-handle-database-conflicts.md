<properties linkid="develop-mobile-tutorials-optimistic-concurrent-data-wp8" urlDisplayName="Optimistic concurrency" pageTitle="Handle database write conflicts with optimistic concurrency (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to handle database write conflicts on both the server and in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conlicts" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Обработка конфликтов записи базы данных

<div class="dev-center-tutorial-selector sublanding">
<a href="/ru-ru/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/" title="Магазин Windows C#">Магазин Windows C#</a>
<a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-handle-database-conflicts/" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a>
<a href="/ru-ru/develop/mobile/tutorials/handle-database-write-conflicts-wp8/" title="Windows Phone" class="current">Windows Phone</a>
</div>

Этот учебник поможет вам лучше понять, как обрабатывать конфликты, возникающие, когда два или более клиентов пишут в одной и той же записи базы данных в приложениях Windows Phone 8. В некоторых сценариях два и более клиента могут одновременно записывать изменения в один и тот же элемент. Без какого либо определения конфликтов последняя запись должна переопределять любые предыдущие обновления, даже если они привели к нежелательным результатам. Мобильные службы предоставляют поддержку для обнаружения и разрешения таких конфликтов. В этом разделе описываются шаги, которые позволят обрабатывать конфликты записи базы данных как на сервере, так и в приложении.

В этом учебнике вы добавите функции в приложение быстрого запуска для обработки разногласий, которые возникают при обновлении базы данных TodoItem. В этом учебнике рассматриваются следующие основные действия:

1.  [Обновите приложение, чтобы разрешить обновления][Обновите приложение, чтобы разрешить обновления]
2.  [Включите в приложении обнаружение конфликтов][Включите в приложении обнаружение конфликтов]
3.  [Протестируйте в приложении конфликты записи базы данных][Протестируйте в приложении конфликты записи базы данных]
4.  [Автоматическое разрешение конфликтов в серверных скриптах][Автоматическое разрешение конфликтов в серверных скриптах]

Для работы с данным учебником требуется следующее

-   Microsoft Visual Studio 2012 Express для Windows Phone 8 или более поздней версии.
-   [Пакет SDK для Windows Phone 8][Пакет SDK для Windows Phone 8], выполняемый в среде Windows 8.
-   [Учетная запись Azure][Учетная запись Azure]
-   Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].
-   Пакет NuGet для мобильных служб Azure 1.1.0 или более поздней версии. Для получения последней версии выполните следующие действия:

    1.  Откройте проект в Visual Studio, щелкните его правой кнопкой мыши в обозревателе решений, а затем щелкните **Управление пакетами NuGet**.

        ![][0]

    2.  Разверните раздел **В сети** и щелкните **Microsoft и .NET**. В поле "Поиск" введите **Мобильные службы Azure**. Нажмите **Установка** в пакете NuGet **Мобильных служб Azure**.

        ![][1]

## <a name="uiupdate"></a><span class="short-header">Обновление пользовательского интерфейса</span>Обновление приложения для предоставления разрешения на выполнение обновлений

В этом разделе вы обновите пользовательский интерфейс TodoList, что позволит обновлять текст для каждого элемента в элементе управления «поле со списком». Поле со списком будет содержать поле флажка и текстовое поле для каждого элемента в таблице базы данных. Можно будет обновить текстовое поле TodoItem. Приложение будет обрабатывать событие `LostFocus` из этого текстового поля для обновления элемента в базе данных.

1.  Откройте в Visual Studio проект, скачанный во время работы с учебником [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].
2.  В обозревателе решений Visual Studio откройте файл MainPage.xaml, замените определение `phone:LongListSelector` на приведенный ниже элемент ListBox и сохраните изменения.

        <ListBox Grid.Row="4" Grid.ColumnSpan="2" Name="ListItems">
            <ListBox.ItemTemplate>
                <DataTemplate>
                    <StackPanel Orientation="Horizontal">
                        <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Margin="10,5" VerticalAlignment="Center"/>
                        <TextBox x:Name="ToDoText" Width="330" Text="{Binding Text, Mode=TwoWay}" AcceptsReturn="False" LostFocus="ToDoText_LostFocus"/>
                    </StackPanel>
                </DataTemplate>
            </ListBox.ItemTemplate>
        </ListBox>

3.  В обозревателе решений Visual Studio откройте файл MainPage.xaml.cs и добавьте следующую директиву `using`.

        using System.Threading.Tasks;

4.  В обозревателе решений Visual Studio откройте файл MainPage.xaml.cs. Добавьте обработчик событий MainPage для текстового поля события `LostFocus`, как показано ниже.

        private async void ToDoText_LostFocus(object sender, RoutedEventArgs e)
        {
            TextBox tb = (TextBox)sender;
            TodoItem item = tb.DataContext as TodoItem;
            //let's see if the text changed
            if (item.Text != tb.Text)
            {
                item.Text = tb.Text;
                await UpdateToDoItem(item);
            }
        }

5.  В файле MainPage.xaml.cs добавьте определение для метода MainPage `UpdateToDoItem()`, на который ссылается обработчик событий, как показано ниже.

        private async Task UpdateToDoItem(TodoItem item)
        {
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message, "Update Failed", MessageBoxButton.OK);
            }
        }

Теперь, когда текстовое окно теряет фокус, приложение снова записывает изменения текста для каждого элемента в базе данных.

## <a name="enableOC"></a><span class="short-header">Включение оптимистичного параллелизма</span>Включение в приложении функции обнаружения конфликтов

В некоторых сценариях два и более клиента могут одновременно записывать изменения в один и тот же элемент. Без какого либо определения конфликтов последняя запись должна переопределять любые предыдущие обновления, даже если они привели к нежелательным результатам. При [управлении оптимистичным параллелизмом][управлении оптимистичным параллелизмом] предполагается, что каждая транзакция может фиксироваться, поэтому не использует блокировки каких-либо ресурсов. Перед фиксацией транзакции управление оптимистичным параллелизмом проверяет, что никакие другие транзакции не изменили данные. Если данные были изменены, фиксирующая транзакция откатывается. Мобильные службы Azure поддерживают управление оптимистичным параллелизмом за счет отслеживания изменений каждого элемента с помощью столбца системного свойства `__version`, который добавляется к каждой таблице. В этом разделе описано, как сделать так, чтобы приложение определяло эти конфликты записи с помощью системного свойства `__version`. Приложение будет получать уведомления от `MobileServicePreconditionFailedException` во время попытки обновления, если запись была изменена с момента последнего запроса. Затем оно сможет выбрать, зафиксировать свои изменения в базе данных или оставить без изменений последнее изменение в базе данных. Дополнительные сведения о системных свойствах мобильных служб см. в [Системные свойства][Системные свойства]

1.  В файле MainPage.xaml.cs обновите определение класса **TodoItem** с помощью приведенного ниже кода, чтобы добавить в него системное свойство **__version**. Благодаря этому будет включена поддержка обнаружения конфликтов записи.

        public class TodoItem
        {
            public string Id { get; set; }            
            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }            
            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }            
            [JsonProperty(PropertyName = "__version")]
            public string Version { set; get; }
        }

    <div class="dev-callout"><strong>Примечание.</strong>
<p>При использовании нетипизированных таблиц включите оптимистичный параллелизм, добавив флаг &laquo;Версия&raquo; в системные свойства таблицы.</p>
<pre><code>//Enable optimistic concurrency by retrieving __version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
</code></pre>
</div>

2.  После добавления свойства `Version` к классу `TodoItem` приложение во время обновления будет получать уведомления об исключении `MobileServicePreconditionFailedException`, если запись была изменена с момента последнего запроса. Это исключение включает последнюю версию элемента с сервера. В файле MainPage.xaml.cs добавьте следующий код, чтобы обрабатывать исключение в методе `UpdateToDoItem()`.

        private async Task UpdateToDoItem(TodoItem item)
        {
            Exception exception = null;
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (MobileServicePreconditionFailedException<TodoItem> writeException)
            {
                exception = writeException;
            }
            catch (Exception ex)
            {
                exception = ex;
            }
            if (exception != null)
            {
                if (exception is MobileServicePreconditionFailedException<TodoItem>)
                {
                    //conflict detected, the item has changed since the last query
                    await ResolveConflict(item, ((MobileServicePreconditionFailedException<TodoItem>)exception).Item);
                }
                else
                    MessageBox.Show(exception.Message, "Update Failed", MessageBoxButton.OK);
            }
        }

3.  В файле MainPage.xaml.cs добавьте определение для метода`ResolveConflict()`, на который ссылается `UpdateToDoItem()`. Обратите внимание, что для разрешения конфликта перед принятием пользователем решения необходимо в качестве версии локального элемента задать обновленную версию с сервера. В противном случае конфликт будет возникать постоянно.

        private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)     
        {
            // Ask user to choose between the local text value or leaving the 
            // server's updated text value
            MessageBoxResult mbRes = MessageBox.Show(String.Format("The item has already been updated on the server.\n\n" +
                                                                   "Server value: {0} \n" +
                                                                   "Local value: {1}\n\n" +
                                                                   "Press OK to update the server with the local value.\n\n" +
                                                                   "Press CANCEL to keep the server value.", serverItem.Text, localItem.Text), 
                                                                   "CONFLICT DETECTED ", MessageBoxButton.OKCancel);
            // OK : After examining the updated text from the server, overwrite it
            //      with the changes made in this client.
            if (mbRes == MessageBoxResult.OK)
            {
                // Update the version of the item to the latest version
                // to resolve the conflict. Otherwise the exception
                // will be thrown again for the attempted update.
                localItem.Version = serverItem.Version;
                // Recursively updating just in case another conflict 
                // occurs while the user is deciding.
                await this.UpdateToDoItem(localItem);
            }
            // CANCEL : After examining the updated text from the server, leave 
            // the server item intact and refresh this client's query discarding 
            // the proposed changes.
            if (mbRes == MessageBoxResult.Cancel)
            {
                RefreshTodoItems();
            }
        }

## <a name="test-app"></a><span class="short-header">Тестирование приложения</span>Тестирование конфликтов записи базы данных в приложении

В этом разделе будет протестирован код, обрабатывающий конфликты записи путем запуска приложения в двух различных эмуляторах Windows Phone 8 (WVGA и WVGA 512 МБ). Оба клиентских приложения будут пытаться обновить свойство `text` одного и того же элемента, что потребует от пользователя разрешить конфликт.

1.  В Visual Studio убедитесь, что параметр **Эмулятор WVGA 512МБ** выбирается в раскрывающемся списке в качестве целевого объекта развертывания, как показано на следующем снимке экрана.

    ![][2]

2.  В Visual Studio выберите в меню команду **СБОРКА**, а затем — команду **Развернуть решение**. Если эмулятор ранее не выполнялся, эмулятору потребуется несколько минут для загрузки операционной системы Windows Phone 8. Проверьте в окне вывода в нижней части, что сборка и развертывание в эмуляторе Windows Phone 8 выполнены успешно.

    ![][3]

3.  В Visual Studio измените целевой объект развертывания в поле раскрывающегося списка на **Эмулятор WVGA**.

    ![][4]

4.  В Visual Studio выберите в меню команду **СБОРКА**, а затем — команду **Развернуть решение**. Проверьте в окне вывода в нижней части, что сборка и развертывание в эмуляторе Windows Phone 8 выполнены успешно.

    ![][3]

5.  Поместите оба эмулятора таким образом, чтобы они отображались рядом друг с другом. Мы можем имитировать конфликты параллельной записи, осуществляемой клиентскими приложениями, работающими в этих эмуляторах. Проведите пальцем справа налево в обоих эмуляторах, чтобы просмотреть список установленных приложений. Перейдите к нижней части каждого списка и щелкните приложение **todolist**.

    ![][5]

6.  В левом эмуляторе измените значение свойства `text` последнего элемента TodoItem на **Тест записи 1**, а затем щелкните второе текстовое поле, чтобы обработчик событий `LostFocus` обновил базу данных. На следующем снимке экрана показан пример.

    ![][6]

7.  На этом этапе соответствующий элемент в правом эмуляторе имеет старую версию и старое значение текста. В правом эмуляторе введите **Test Write 2** для свойства текста. Затем выберите второе текстовое поле, чтобы обработчик событий `LostFocus` в правом эмуляторе попытался обновить базу данных со старой версией.

    ![][7]

8.  Так как версия, использованная при попытке обновления, не соответствует версии сервера, пакет SDK для мобильных служб высылает уведомление `MobileServicePreconditionFailedException`, позволяющее приложению разрешить этот конфликт. Для разрешения конфликта можно нажать **ok**, чтобы зафиксировать значения из правого приложения. Можно также щелкнуть **отмена**, чтобы отменить значения в правом приложении, оставляя зафиксированными значения из левого приложения.

    ![][8]

## <a name="scriptsexample"></a><span class="short-header">Обработка конфликтов при помощи сценариев</span>Автоматическое разрешение конфликтов в серверных сценариях

Можно обнаруживать и разрешать конфликты записи в скриптах сервера. Это хорошая идея — использовать записанную логику вместо взаимодействия с пользователем для разрешения конфликта. В этом разделе вы добавите скрипт со стороны сервера в таблицу TodoItem для приложения. Логика, которую будет использовать этот скрипт для разрешения конфликтов, выглядит следующим образом:

-   Если в поле `complete` таблицы TodoItem указано значение true, таблица считается завершенной и `text` больше не может быть изменен.
-   Если в поле `complete` таблицы TodoItem указано значение false, будут продолжаться попытки обновить `text`.

Дальнейшие действия описывают добавление серверного скрипта обновления и его тестирование.

1.  Выполните вход на [портал управления Azure][портал управления Azure], щелкните элемент **Мобильные службы**, а затем выберите свое приложение.

    ![][9]

2.  Откройте вкладку **Данные** и щелкните таблицу **TodoItem**.

    ![][10]

3.  Щелкните элемент **Сценарий** и выберите операцию **Обновление**.

    ![][11]

4.  Замените имеющийся сценарий следующей функцией и нажмите кнопку **Сохранить**:

        function update(item, user, request) { 
            request.execute({ 
                conflict: function (serverRecord) {
                    // Only committing changes if the item is not completed.
                    if (serverRecord.complete === false) {
                        //write the updated item to the table
                        request.execute();
                    }
                    else
                    {
                        request.respond(statusCodes.FORBIDDEN, 'The item is already completed.');
                    }
                }
            }); 
        }   

5.  Измените текст TodoItem для последнего элемента в приложении в левом эмуляторе. Затем выберите второе текстовое поле, чтобы обработчик событий `LostFocus` обновил базу данных.

    ![][6]

6.  В правом эмуляторе введите другое значение для текстового свойства последнего элемента таблицы TodoItem. Затем выберите второе текстовое поле, чтобы обработчик событий `LostFocus` в правом эмуляторе попытался обновить базу данных со старой версией.

    ![][7]

7.  Обратите внимание, что, поскольку серверный сценарий разрешил конфликт, позволив обновление, так как соответствующий элемент не был помечен как завершенный, в приложении не было обнаружено исключение. Чтобы увидеть, что обновление выполнено действительно успешно, нажмите кнопку **Обновить** в приложении в левом эмуляторе для повторного запроса к базе данных.

    ![][12]

8.  В приложении в левом эмуляторе установите флажок для завершения последнего элемента TodoItem.

    ![][13]

9.  В приложении в правом эмуляторе попытайтесь обновить тот же текст TodoItem и вызвать событие `LostFocus`. В ответ на конфликт сценарий разрешил его, отказавшись от обновления, потому что элемент уже завершен.

    ![][14]

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показано, как сделать, чтобы приложение для Windows Phone 8 обрабатывало конфликты записи при работе с данными в мобильных службах. Далее проработайте один из следующих учебников в серии, посвященной работе с данными:

-   [Проверка и изменение данных с помощью скриптов][Проверка и изменение данных с помощью скриптов]
    <br/>Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

-   [Уточнение запросов посредством разбиения по страницам][Уточнение запросов посредством разбиения по страницам]
    <br/>Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемых в одном запросе.

После завершения ряда данных можно попробовать выполнить один из следующих учебников по Windows Phone 8.

-   [Приступая к работе с проверкой подлинности][Приступая к работе с проверкой подлинности]
    <br/>Дополнительные сведения о проверке подлинности пользователей приложения.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]
    <br/>Сведения об отправке в приложение простейших push-уведомлений с помощью мобильных служб.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [Обновите приложение, чтобы разрешить обновления]: #uiupdate
  [Включите в приложении обнаружение конфликтов]: #enableOC
  [Протестируйте в приложении конфликты записи базы данных]: #test-app
  [Автоматическое разрешение конфликтов в серверных скриптах]: #scriptsexample
  [Пакет SDK для Windows Phone 8]: http://go.microsoft.com/fwlink/p/?LinkID=268374
  [Учетная запись Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/
  [Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-wp8
  [0]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
  [1]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png
  [управлении оптимистичным параллелизмом]: http://go.microsoft.com/fwlink/?LinkId=330935
  [Системные свойства]: http://go.microsoft.com/fwlink/?LinkId=331143
  [2]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-EmulatorWVGA512MB.png
  [3]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-build-deploy-wp8.png
  [4]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-EmulatorWVGA.png
  [5]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-start-apps-oc-wp8.png
  [6]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write1-wp8.png
  [7]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write2-wp8.png
  [8]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-exception-wp8.png
  [портал управления Azure]: https://manage.windowsazure.com/
  [9]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-services-selection.png
  [10]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-portal-data-tables.png
  [11]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-insert-script-users.png
  [12]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-insync-wp8.png
  [13]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-complete-checkbox-wp8.png
  [14]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-already-completed-wp8.png
  [Проверка и изменение данных с помощью скриптов]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
  [Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-wp8
  [Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-wp8
  [Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-wp8
