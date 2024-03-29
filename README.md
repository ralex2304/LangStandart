# Стандарт промежуточного представления программы компилятором

## Общее

Кодировка файла `cp1251`

## Команды
|Num| Name             |Type    | Description |
|---|------------------|--------|-------------|
| 1 | CMD_SEPARATOR    | LIST   | Разделитель команд. Имитирует список. Левый потомок - команда, правого или нет, или такой же разделитель
| 2 | VAR_DEFINITION   | BINARY | Определение переменной. Слева лист типа переменная, справа либо ничего, либо выражение
| 3 | CONST_VAR_DEF    | UNARY  | Опциональный родитель VAR_DEFINITION и ARRAY_DEFINITION
| 4 | ARRAY_DEFINITION | BINARY | Определение массива. Слева поддерево: (VAR_SEPARATOR (переменная) (константное выражение - индекс)). Справа либо ничего, либо список выражений через VAR_SEPARATOR
| 5 | FUNC_DEFINITION  | BINARY | Определение функции. Слева поддерево: (VAR_SEPARATOR (переменная) (поддерево аргументов (список VAR_SEPARATOR'ов))). Справа список команд через CMD_SEPARATOR
| 6 | ASSIGNMENT       | BINARY | Присваивание. Слева лист переменная, справа выражение
| 7 | ASSIGNMENT_ADD   | BINARY |
| 8 | ASSIGNMENT_SUB   | BINARY |
| 9 | ASSIGNMENT_MUL   | BINARY |
|10 | ASSIGNMENT_DIV   | BINARY |
|11 | ARRAY_ELEM       | BINARY | Элемент массива. Слева лист переменная - имя массива, справа выражение - индекс элемента
|15 | VAR_SEPARATOR    | LIST   | Имитатор списка для аргументов функции и т.п.
|16 | FUNC_CALL        | BINARY | Вызов функции. Слева лист переменная, справа список выражений через VAR_SEPARATOR
|17 | RETURN           | UNARY  | Возврат из функции. Слева ничего, справа выражение
|20 | MATH_ADD         | BINARY | Сложение
|21 | MATH_SUB         | BINARY | Вычитание
|22 | MATH_MUL         | BINARY | Умножение
|23 | MATH_DIV         | BINARY | Деление
|24 | MATH_SQRT        | UNARY  | Корень
|25 | MATH_SIN         | UNARY  | Синус
|26 | MATH_COS         | UNARY  | Косинус
|27 | MATH_NEGATIVE    | UNARY  | Унарный минус
|28 | MATH_DIFF        | BINARY | Оператор дифф. Слева выражение, справа лист-переменная с номером переменной, по которой дифф.
|40 | LOGIC_GREAT      | BINARY | >
|41 | LOGIC_LOWER      | BINARY | <
|42 | LOGIC_NOT_EQUAL  | BINARY | !=
|43 | LOGIC_EQUAL      | BINARY | ==
|44 | LOGIC_GREAT_EQ   | BINARY | >=
|45 | LOGIC_LOWER_EQ   | BINARY | <=
|50 | PREFIX_ADD       | BINARY | ++x <br> 1) Слева обязательно переменная, справа, либо следующий препост-оператор, либо ничего, либо переменная (последние два варианта означают одно и то же). В таком списке операторов все переменные должны иметь один номер. Такое дублирование сделано для оптимального чтения на бекенде; <br> 2) Сначала только префиксные операторы, потом только постфиксные. То есть префиксный не может быть потомком постфиксного. <br><br> Аналогично для всех препост-операторов
|51 | PREFIX_SUB       | BINARY | --x
|52 | POSTFIX_ADD      | BINARY | x++
|53 | POSTFIX_SUB      | BINARY | x--
|60 | WHILE            | BINARY | while. Слева вычисляемое выражение, справа либо список команд, либо ELSE
|61 | DO_WHILE         | BINARY | do {} while (). Аналогично, ELSE нельзя
|63 | IF               | BINARY | Аналогично
|64 | DO_IF            | BINARY | do {} if () - не спрашивайте, можете забить
|66 | ELSE             | BINARY | Слева список команд, если выполняется основная ветвь, справа если else ветвь
|67 | BREAK            | LEAF   | break
|68 | CONTINUE         | LEAF   | continue
|69 | NEW_SCOPE        | UNARY  | Новая область видимости переменных. Слева ничего, справа список команд
|70 | IN               | LEAF   | команда асма `in`
|71 | OUT              | UNARY  | команда асма `out`. Слева ничего, справа выражение
|72 | SHOW             | LEAF   | команда асма `shw`
|73 | SET_FPS          | UNARY  | команда асма `fps`. Слева ничего, справа выражение, которое можно вычислить во время компиляции (константное)

## Типы узлов

- `1` - оператор. Значение - номер оператора
- `2` - число. Значение - число
- `3` - переменная. Значение - номер переменной

## Формат

Инфиксная запись

Каждый узел характеризуется дебаг инфой, типом, значением и потомками. Если нет потомка, что `(nil)`

```({<путь до исходника>, <номер строки>, <номер символа с начала строки>, <номер первого символа строки с начала файла>}, <тип>, <значение>, <левый потомок> <правый потомок>)```

E.g.
```({"main.code", 1, 2, 3}, 0, 0, (nil) (nil))```


## Файл

Сначала идёт дерево. Затем `\n` и список имён переменных (каждое имя на новой строке). Нумерация с 0, соответсвует порядку

E.g.
```
({"main.code", 1, 2, 3}, 0, 0, (nil) (nil))
x
y
z

```
