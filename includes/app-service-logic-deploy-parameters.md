С помощью Azure Resource Manager можно определить параметры для значений, используемых при развертывании шаблона. В шаблоне есть раздел `parameters`, содержащий все значения параметров. Значение каждого параметра в шаблоне определяет развертываемые ресурсы.

> [!NOTE]
> Не определяйте параметры для значений, которые не меняются. Определите параметры для значений, которые зависят от развертываемого проекта или среды, в которой выполняется развертывание.

При определении параметров:

* Значения, указываемые пользователем во время развертывания, можно задать с помощью поля **allowedValues**.

* Чтобы задать стандартные значения параметру, если во время развертывания другие значения не указываются, используйте поле **defaultValue**. 
