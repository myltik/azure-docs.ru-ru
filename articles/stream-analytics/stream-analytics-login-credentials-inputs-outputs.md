<properties 
	pageTitle="Stream Analytics: смена учетных данных для источников входных данных и мест назначения выходных данных | Microsoft Azure" 
	description="Информация о том, как обновить учетные данные для источников входных данных и мест назначения выходных данных Stream Analytics"
	keywords="учетные данные для входа в систему"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="07/27/2016" 
	ms.author="jeffstok"/>

#Смена учетных данных для входа в систему

##Аннотация
Azure Stream Analytics в настоящее время не допускает замены учетных данных в источниках входных данных и местах назначения выходных данных во время выполнения задания.

Хотя Azure Stream Analytics и не поддерживает возобновление задания с последнего вывода, мы хотим рассказать обо всем процессе, чтобы свести к минимуму задержки между остановкой и запуском задания и сменой учетных данных для входа в систему.

##Часть 1. Подготовка нового набора учетных данных
Эта часть касается следующих источников входных данных и мест назначения выходных данных:

* Хранилище BLOB-объектов
* Концентраторы событий
* База данных SQL
* Хранилище таблиц

Сведения, касающиеся других источников входных данных и мест назначения выходных данных см. в части 2.

###Хранилище больших двоичных объектов и табличное хранилище
1.  Перейдите к расширению «Хранилище» на портале управления Azure: ![рисунок1][graphic1]
2.  Найдите хранилище, используемое заданием, и перейдите в него: ![рисунок2][graphic2]
3.  Щелкните команду «Упр. ключами доступа»: ![рисунок3][graphic3]
4.  На выбор предлагается первичный и вторичный ключ доступа. **Выберите тот, который не используется заданием**.
5.  Нажмите кнопку повторного создания: ![рисунок4][graphic4]
6.  Скопируйте созданный ключ: ![рисунок5][graphic5]
7.  Перейдите к части 2.

###Концентраторы событий
1.  Перейдите к расширению Service Bus на портале управления Azure: ![рисунок6][graphic6]
2.  Найдите пространство имен Service Bus, используемое заданием, и перейдите в него: ![рисунок7][graphic7]
3.  Если в задании для пространства имен Service Bus используется политика общего доступа, перейдите к шагу 6.
4.  Перейдите на вкладку «Концентраторы событий»: ![рисунок8][graphic8]
5.  Найдите концентратор событий, используемый заданием, и перейдите к нему: ![рисунок9][graphic9]
6.  Перейдите на вкладку «Настройка»: ![рисунок10][graphic10]
7.  В раскрывающемся списке «Имя политики» найдите политику общего доступа, используемую заданием: ![рисунок11][graphic11]
8.  На выбор предлагается первичный и вторичный ключ. **Выберите тот, который не используется заданием**.
9.  Нажмите кнопку повторного создания: ![рисунок12][graphic12]
10. Скопируйте созданный ключ: ![рисунок13][graphic13]
11. Перейдите к части 2.

###База данных SQL

>[AZURE.NOTE] Примечание. Необходимо подключиться к службе базы данных SQL. Здесь будет показано, как это сделать с помощью средств управления на портале управления Azure, но вы можете также использовать клиентские средства, например SQL Server Management Studio.

1.  Перейдите к расширению «Базы данных SQL» на портале управления Azure: ![рисунок14][graphic14]
2.  Найдите базы данных SQL, используемые заданием, и **щелкните ссылку на сервер** в той же строке: ![рисунок15][graphic15]
3.  Щелкните команду «Управление»: ![рисунок16][graphic16]
4.  Введите главный ключ базы данных: ![рисунок17][graphic17]
5.  Введите свое имя пользователя, пароль и нажмите кнопку «Вход»: ![рисунок18][graphic18]
6.  Щелкните «Создать запрос»: ![рисунок19][graphic19]
7.  Введите следующий запрос, заменив <login\_name> своим именем пользователя, а <enterStrongPasswordHere> — новым паролем. `CREATE LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>'`
8.  Щелкните «Выполнить»: ![рисунок20][graphic20]
9.  Вернитесь к шагу 2, но в этот раз щелкните базу данных: ![рисунок21][graphic21]
10. Щелкните команду «Управление»: ![рисунок22][graphic22]
11. Введите свое имя пользователя, пароль и нажмите кнопку «Вход»: ![рисунок23][graphic23]
12. Щелкните «Создать запрос»: ![рисунок24][graphic24]
13. Введите следующий запрос, заменив <user\_name> именем, по которому можно будет идентифицировать данный вход в контексте этой базы данных (можно, например, указать то же значение, которое было введено для <login\_name>), и замените <login\_name> новым именем пользователя. `CREATE USER <user_name> FROM LOGIN <login_name>`
14. Щелкните «Выполнить»: ![рисунок25][graphic25]
15. Теперь следует предоставить новому пользователю такие же роли и права, как у исходного пользователя.
16. Перейдите к части 2.

##Часть 2. Остановка задания Stream Analytics
1.  Перейдите к расширению Stream Analytics на портале управления Azure. ![рисунок26][graphic26]
2.  Найдите задание и перейдите к нему: ![рисунок27][graphic27]
3.  Перейдите на вкладку «Входные данные» или на вкладку «Выходные данные» в зависимости от того, где выполняется смена учетных данных. ![рисунок28][graphic28]
4.  Щелкните команду «Остановить» и убедитесь, что задание остановлено: ![рисунок29][graphic29] Дождитесь остановки задания.
5.  Найдите источник входных данных или место назначения выходных данных, для которых нужно сменить учетные данные, и перейдите к нему: ![рисунок30][graphic30]
6.  Перейдите к части 3.

##Часть 3. Изменение учетных данных для задания Stream Analytics

###Хранилище больших двоичных объектов и табличное хранилище
1.	Найдите поле «Ключ учетной записи хранения» и вставьте в него созданный ключ: ![рисунок31][graphic31]
2.	Щелкните команду «Сохранить» и подтвердите сохранение изменений: ![рисунок32][graphic32]
3.	При сохранении изменений автоматически запускается проверка подключения. Убедитесь, что она прошла успешно.
4.	Перейдите к части 4.

###Концентраторы событий
1.	Найдите поле «Ключ политики концентратора событий» и вставьте созданный новый ключ: ![рисунок33][graphic33]
2.	Щелкните команду «Сохранить» и подтвердите сохранение изменений: ![рисунок34][graphic34]
3.	При сохранении изменений автоматически запускается проверка подключения. Убедитесь, что она прошла успешно.
4.	Перейдите к части 4.

###Power BI
1.	Нажмите кнопку «Возобновить авторизацию»:
* ![рисунок35][graphic35]
* Вы получите следующее подтверждение:
* ![рисунок36][graphic36]
2.	Щелкните команду «Сохранить» и подтвердите сохранение изменений: ![рисунок37][graphic37]
3.	При сохранении изменений автоматически запускается проверка подключения. Убедитесь, что она прошла успешно.
4.	Перейдите к части 4.

###База данных SQL
1.	Найдите поля «Имя пользователя» и «Пароль» и вставьте в них только что созданный набор учетных данных: ![рисунок38][graphic38]
2.	Щелкните команду «Сохранить» и подтвердите сохранение изменений: ![рисунок39][graphic39]
3.	При сохранении изменений автоматически запускается проверка подключения. Убедитесь, что она прошла успешно.
4.	Перейдите к части 4.

##Часть 4. Запуск задания с момента последней остановки
1.	Покиньте страницу источника входных данных или места назначения выходных данных: ![рисунок40][graphic40]
2.	Щелкните команду «Начать»: ![рисунок41][graphic41]
3.	Выберите время последней остановки и нажмите кнопку «ОК»: ![рисунок42][graphic42]
4.	Перейдите к часть 5.

##Часть 5. Удаление старого набора учетных данных
Эта часть касается следующих источников входных данных и мест назначения выходных данных:
* Хранилище BLOB-объектов
* Концентраторы событий
* База данных SQL
* Хранилище таблиц

###Хранилище больших двоичных объектов и табличное хранилище
Повторите часть 1 для ключа доступа, который использовался ранее заданием для обновления неиспользуемого сейчас ключа доступа.

###Концентраторы событий
Повторите часть 1 для ключа, который использовался ранее заданием для обновления неиспользуемого сейчас ключа.

###База данных SQL
1.	Вернитесь в окно запроса, описанное на шаге 7 в части 1, и введите следующий запрос, заменив <previous\_login\_name> именем пользователя, которое использовалось ранее вашим заданием. `DROP LOGIN <previous_login_name>`
2.	Щелкните «Выполнить»: ![рисунок43][graphic43]

Должно появиться следующее подтверждение:

	Command(s) completed successfully.

## Получение справки
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/ru-RU/home?forum=AzureStreamAnalytics).

## Дальнейшие действия

- [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
- [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
- [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-login-credentials-inputs-outputs/1-stream-analytics-login-credentials-inputs-outputs.png
[graphic2]: ./media/stream-analytics-login-credentials-inputs-outputs/2-stream-analytics-login-credentials-inputs-outputs.png
[graphic3]: ./media/stream-analytics-login-credentials-inputs-outputs/3-stream-analytics-login-credentials-inputs-outputs.png
[graphic4]: ./media/stream-analytics-login-credentials-inputs-outputs/4-stream-analytics-login-credentials-inputs-outputs.png
[graphic5]: ./media/stream-analytics-login-credentials-inputs-outputs/5-stream-analytics-login-credentials-inputs-outputs.png
[graphic6]: ./media/stream-analytics-login-credentials-inputs-outputs/6-stream-analytics-login-credentials-inputs-outputs.png
[graphic7]: ./media/stream-analytics-login-credentials-inputs-outputs/7-stream-analytics-login-credentials-inputs-outputs.png
[graphic8]: ./media/stream-analytics-login-credentials-inputs-outputs/8-stream-analytics-login-credentials-inputs-outputs.png
[graphic9]: ./media/stream-analytics-login-credentials-inputs-outputs/9-stream-analytics-login-credentials-inputs-outputs.png
[graphic10]: ./media/stream-analytics-login-credentials-inputs-outputs/10-stream-analytics-login-credentials-inputs-outputs.png
[graphic11]: ./media/stream-analytics-login-credentials-inputs-outputs/11-stream-analytics-login-credentials-inputs-outputs.png
[graphic12]: ./media/stream-analytics-login-credentials-inputs-outputs/12-stream-analytics-login-credentials-inputs-outputs.png
[graphic13]: ./media/stream-analytics-login-credentials-inputs-outputs/13-stream-analytics-login-credentials-inputs-outputs.png
[graphic14]: ./media/stream-analytics-login-credentials-inputs-outputs/14-stream-analytics-login-credentials-inputs-outputs.png
[graphic15]: ./media/stream-analytics-login-credentials-inputs-outputs/15-stream-analytics-login-credentials-inputs-outputs.png
[graphic16]: ./media/stream-analytics-login-credentials-inputs-outputs/16-stream-analytics-login-credentials-inputs-outputs.png
[graphic17]: ./media/stream-analytics-login-credentials-inputs-outputs/17-stream-analytics-login-credentials-inputs-outputs.png
[graphic18]: ./media/stream-analytics-login-credentials-inputs-outputs/18-stream-analytics-login-credentials-inputs-outputs.png
[graphic19]: ./media/stream-analytics-login-credentials-inputs-outputs/19-stream-analytics-login-credentials-inputs-outputs.png
[graphic20]: ./media/stream-analytics-login-credentials-inputs-outputs/20-stream-analytics-login-credentials-inputs-outputs.png
[graphic21]: ./media/stream-analytics-login-credentials-inputs-outputs/21-stream-analytics-login-credentials-inputs-outputs.png
[graphic22]: ./media/stream-analytics-login-credentials-inputs-outputs/22-stream-analytics-login-credentials-inputs-outputs.png
[graphic23]: ./media/stream-analytics-login-credentials-inputs-outputs/23-stream-analytics-login-credentials-inputs-outputs.png
[graphic24]: ./media/stream-analytics-login-credentials-inputs-outputs/24-stream-analytics-login-credentials-inputs-outputs.png
[graphic25]: ./media/stream-analytics-login-credentials-inputs-outputs/25-stream-analytics-login-credentials-inputs-outputs.png
[graphic26]: ./media/stream-analytics-login-credentials-inputs-outputs/26-stream-analytics-login-credentials-inputs-outputs.png
[graphic27]: ./media/stream-analytics-login-credentials-inputs-outputs/27-stream-analytics-login-credentials-inputs-outputs.png
[graphic28]: ./media/stream-analytics-login-credentials-inputs-outputs/28-stream-analytics-login-credentials-inputs-outputs.png
[graphic29]: ./media/stream-analytics-login-credentials-inputs-outputs/29-stream-analytics-login-credentials-inputs-outputs.png
[graphic30]: ./media/stream-analytics-login-credentials-inputs-outputs/30-stream-analytics-login-credentials-inputs-outputs.png
[graphic31]: ./media/stream-analytics-login-credentials-inputs-outputs/31-stream-analytics-login-credentials-inputs-outputs.png
[graphic32]: ./media/stream-analytics-login-credentials-inputs-outputs/32-stream-analytics-login-credentials-inputs-outputs.png
[graphic33]: ./media/stream-analytics-login-credentials-inputs-outputs/33-stream-analytics-login-credentials-inputs-outputs.png
[graphic34]: ./media/stream-analytics-login-credentials-inputs-outputs/34-stream-analytics-login-credentials-inputs-outputs.png
[graphic35]: ./media/stream-analytics-login-credentials-inputs-outputs/35-stream-analytics-login-credentials-inputs-outputs.png
[graphic36]: ./media/stream-analytics-login-credentials-inputs-outputs/36-stream-analytics-login-credentials-inputs-outputs.png
[graphic37]: ./media/stream-analytics-login-credentials-inputs-outputs/37-stream-analytics-login-credentials-inputs-outputs.png
[graphic38]: ./media/stream-analytics-login-credentials-inputs-outputs/38-stream-analytics-login-credentials-inputs-outputs.png
[graphic39]: ./media/stream-analytics-login-credentials-inputs-outputs/39-stream-analytics-login-credentials-inputs-outputs.png
[graphic40]: ./media/stream-analytics-login-credentials-inputs-outputs/40-stream-analytics-login-credentials-inputs-outputs.png
[graphic41]: ./media/stream-analytics-login-credentials-inputs-outputs/41-stream-analytics-login-credentials-inputs-outputs.png
[graphic42]: ./media/stream-analytics-login-credentials-inputs-outputs/42-stream-analytics-login-credentials-inputs-outputs.png
[graphic43]: ./media/stream-analytics-login-credentials-inputs-outputs/43-stream-analytics-login-credentials-inputs-outputs.png
 

<!---HONumber=AcomDC_0921_2016-->