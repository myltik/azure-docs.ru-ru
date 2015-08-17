
### redisCacheName

Имя создаваемого кэша Redis Azure.

    "redisCacheName": {
      "type": "string"
    }

### redisCacheSKU

Ценовая категория нового кэша Azure Redis.

    "redisCacheSKU": {
      "type": "string",
      "allowedValues": [ "Basic", "Standard" ],
      "defaultValue": "Basic"
    }

В шаблоне определены значения, допустимые для этого параметра (Basic и Standard). Если значение не указано, параметру назначается значение по умолчанию (Basic). Уровень Basic предоставляет один узел с различными размерами (до 53 ГБ). Уровень Standard предоставляет два узла (основной и реплика) с различными размерами (до 53 ГБ) и соглашением об уровне обслуживания 99,9 %.

### redisCacheFamily

Семейство для SKU.

    "redisCacheFamily": {
      "type": "string",
      "defaultValue": "C"
    }

### redisCacheCapacity

Размер нового экземпляра кэша Azure Redis.

    "redisCacheCapacity": {
      "type": "int",
      "allowedValues": [ 0, 1, 2, 3, 4, 5, 6 ],
      "defaultValue": 0
    }

В шаблоне определяются значения, допустимые для этого параметра (0, 1, 2, 3, 4, 5 и 6). Если значение не указано, параметру назначается значение по умолчанию (0). Эти числа соответствуют следующим размерам кэша: 0 = 250 МБ, 1 = 1 ГБ, 2 = 2,5 ГБ, 3 = 6 ГБ, 4 = 13 ГБ, 5 = 26 ГБ, 6 = 53 ГБ

### redisCacheVersion

Версия сервера Redis нового кэша.

    "redisCacheVersion": {
      "type": "string",
      "defaultValue": "2.8"
    }

<!---HONumber=August15_HO6-->