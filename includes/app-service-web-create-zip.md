## <a name="create-a-project-zip-file"></a>Создание ZIP-файла проекта

Создайте ZIP-архив всего содержимого проекта. При выполнении следующей команды в окне терминала используется инструмент по умолчанию:

```
# Bash
zip -r myAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myAppFiles.zip
``` 

В дальнейшем вы отправите этот ZIP-файл в Azure и развернете его в службе приложений.