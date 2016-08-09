Это условие будет оценивать поле адреса электронной почты каждого нового интереса Salesforce. Если адрес электронной почты содержит *amazon.com*, то результатом оценки условия будет *True*.

1. Выберите **+ Новый шаг**. ![Условие Salesforce, изображение 1](./media/connectors-create-api-salesforce/condition-1.png)
- Выберите **Добавить условие**. ![Условие Salesforce, изображение 2](./media/connectors-create-api-salesforce/condition-2.png)
- Щелкните **Выберите значение**. ![Условие Salesforce, изображение 3](./media/connectors-create-api-salesforce/condition-3.png)
- Выберите маркер *Электронная почта* из интереса триггера. ![Условие Salesforce, изображение 4](./media/connectors-create-api-salesforce/condition-4.png)
- Выберите *Содержит*. ![Условие Salesforce, изображение 5](./media/connectors-create-api-salesforce/condition-5.png)
- Щелкните **Выберите значение** в нижней части элемента управления. ![Условие Salesforce, изображение 6](./media/connectors-create-api-salesforce/condition-6.png)
- Введите *amazon.com* в качестве значения, по которому вы хотите оценивать адрес электронной почты нового интереса. Если адрес электронной почты содержит *amazon.com*, то результат оценки условия принимает значение *True*, что позволяет перейти к другим действиям в приложении логики. ![Условие Salesforce, изображение 7](./media/connectors-create-api-salesforce/condition-7.png)
- Сохраните приложения логики.

<!---HONumber=AcomDC_0727_2016-->