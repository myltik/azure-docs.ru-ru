
<!--
includes/sql-database-create-new-database-portal.md

Latest Freshness check:  2016-04-11 , carlrab.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started-tutorial.md

-->
## Создание новой базы данных SQL Azure

Чтобы создать новую базу данных SQL Azure на новом или существующем логическом сервере базы данных SQL Azure, выполните следующие действия на портале Azure.

1. Если вы еще этого не сделали, подключитесь к [порталу Azure](http://portal.azure.com).
2. Щелкните **Создать**, введите **База данных SQL**, а затем выберите **База данных SQL (новая база данных)**.

     ![новая база данных](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-1.png)

3. Щелкните "База данных SQL (новая база данных)".

     ![новая база данных](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-2.png)
   
4. Нажмите кнопку **Создать**, чтобы создать новую базу данных в службе баз данных SQL.

     ![новая база данных](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-3.png)

5. Укажите значения для следующих свойств сервера:

 - Имя базы данных
 - "Подписка" (только если у вас несколько подписок);
 - "Группа ресурсов" (если вы только начинаете работу, используйте группу ресурсов логического сервера);
 - Выберите источник (пустую базу данных, демонстрационные данные или резервную копию базы данных Azure). Для переноса локальной базы данных SQL Server или загрузки данных с помощью BCP воспользуйтесь ссылками в конце этой статьи.
 - "Сервер" (новый или существующий логический сервер);
 - "Имя для входа администратора сервера";
 - Пароль
 - "Ценовая категория" (если вы только начинаете работу, используйте значение по умолчанию S0);
 - "Параметры сортировки" (только если выбрана пустая база данных).

        ![new database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-4.png)

6.  Щелкните **Создать**. В области уведомлений вы увидите, что развертывание началось.

     ![новая база данных](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-5.png)

7. Прежде чем переходить к следующему шагу, дождитесь завершения развертывания.

     ![новая база данных](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-6.png)

<!---HONumber=AcomDC_0803_2016-->