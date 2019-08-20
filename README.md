# python-interview-cheatsheet
Topics related to python [RU]

## Python code execution
Python это некоторый интерфейс, описывающий язык программирования и имеющий свою [спецификацию](https://docs.python.org/3.6/reference/index.html). Соответственно, существуют различные реализации этого языка, такие как:
* CPython. Эталонная реализация на C. Пеозволяет писать C-расширения на языке C;
* PyPy. Альтернативаня реализация интерпретатора, преимуществами которого являются скорость, JIT-компиляция, меньшее количество потребляемой памяти и микротреды за счет собственной реализации стека вызовов. Написана на python;
* Jython. Реализация языка Python на языке программирования Java. Имеет стандартную библиотеку как питона, так и джавы. Исходный код компилируется в байткод, который исполняется JVM;
* IronPython. Реализация языка, интегрированная с платформой .NET и написанная на C#. Скомпилированный байткод исполняется виртуальной машиной CLR.

Каждая реализация интерпретатора привносит с собой преимущества того или иного стэка технологий.
![Python interpreters](https://hsto.org/getpro/habr/post_images/29e/2ab/f88/29e2abf8809b486658fb09922c2fe9f5.png)

Написанный код на питоне проходит через несколько стадий перед его непосредственным выполнением:
* исходный код компилируется в байткод(.pyc)
* полученный байткод строка за строкой интерпретируется виртуальной машиной
![Python](https://hsto.org/getpro/habr/post_images/a8d/3ff/ab7/a8d3ffab74108d33d3feed2e2c196d11.png)

### Python virtual machine 
Некоторые языки программирования(такие как C, C++) компилируются в инструкции, которые напрямую исполняются на CPU. Преимуществами такого подхода является скорость выполнения в ущерб скорости компиляции. В Python другой подход -- использование виртуальной машины, на которой исполняется скомпилированный байткод. Интерпретатор в бесконечном цикле считывается байткод и выполняет инструкцию одна за другой. 
### .pyc .pyo .pyz
Python3 компилируют подгружаемые модули в файлы с расширением .pyc и помещает их в директорию __pycache__. Во второй версии питона были также файлы .pyo и .pyz. Файлы с расширением .pyo получаются с использованием флага для оптимизации -O. Файлы .pyz это сжатые в zip архив питоновские файлы.
![](https://qph.fs.quoracdn.net/main-qimg-5c2034261d85b3f6ee309333d33e40e4-c)
![](https://qph.fs.quoracdn.net/main-qimg-3e206084409ab4314fd23edce79adfde-cО)

## Отличия Python2 и Python3
* Python2 - по дефолту строки хранятся в кодировке ascii, Python3 - в кодировке utf-8.
* В python2 print - это выражение, в python3 - функция.
* В python2 `3/2 = 1`. В python3 `3/2 = 1.5`.
* В pyhton2 xrange, в python3 range.
* Официально поддержка python2 закончится в 2020 году.

## Python types and operations
### Dictionaries

![](https://2.bp.blogspot.com/-_Dm0QfKLUSU/XGqT9NNwfyI/AAAAAAAACfE/ffZ47KptX3gZdXuZpwqb3PBRvgsN09GYwCLcBGAs/s1600/python-2-vs-3-2018-768x1339.png)

#### Six
six - специальный модуль для безболезненного перевода кода на втором питоне в код для третьего питона и наоборот.

## Запуск скриптов
Запустить скрипт можно просто указав название скрипта в качестве аргумента: `python script.py`.

Полезные ключи:
* `-m` -- использовать встроенный модуль. Пример `python3 -m venv venv_name`, `python3 -m pip install requests`. Примечание: совместно с установкой python устанавливается и pip. 
* `-i` -- включить интерактивную оболочку после запуска скрипта. `python3 script.py -i`
* `-O` -- убрать все assert стейтменты. Оптмизация
* `-OO` -- убрать все assert стейтменты и docstrings. Оптмизиация
* `-u` -- сделать вывод в stdout и stderr небуферизированным.
* `-c` -- выполнить команду. `python3 -c "1 + 2"`.

Переменные окружения:
`PYTHONUNBUFFERED=1` -- управляет выводом в stdout и stderr. 

### Exec, eval
В питоне можно запускать код из самого питона. 
* eval(evaluate) выполняет простые выражения и вовзращает результат.
``` python
>>> eval('1 + 1')
2
```
Небезопасно. Можно использовать, например, `os.remove()`
* exec(execute) выполняет стейтменты и выполняет код, ничего не возвращая.
``` python
>>> exec("""a = 1 + 1
... b = a + 1
... print(b)
... """)
```
### Ast, code, codeop
Ast -- abstract syntax tree. Питон после парсинга токенов строит специальную древовидную структуру для исполнения кода.

code -- специальный модуль, предоставляющий возможность заимпелентить цикл read-eval-print в коде.

codeop -- альтернатива модулю code. Предоставляет некоторые утилиты для цикла read-eval-print.

### Code objects. 
Если обратиться к объекту rфункции, то можно заметить специальный атрибут `__code__`, который хранит скомпилированный байткод функции.
```python
>>> def sum(a, b):
...     my_const = 1
...     return sum([a, b])
... 
>>> sum.__code__
<code object sum at 0x7fc0a6f3eae0, file "<stdin>", line 1>
```
Этот объект ассоциирован с тремя тьюплами: 
* локальные переменные(`co_varnames`), включая аргументы.
```
>>>sum.__code__.co_varnames
('a', 'b', 'my_const')
```
* глобальные переменные(`co_names`)
```
>>>sum.__code__.co_names
('sum',)
```
* константы (`co_consts`)
```
>>>sum.__code__.co_consts
(None, 1)
```


## Type
На уровне C питоновский объект представляет из себя структуру, в которой главными атрибутами является количество ссылок на объект и тип этого объекта. Тип определяет, какие операции поддерживаются данным объектом.
```C
typedef struct _object {
    _PyObject_HEAD_EXTRA
    Py_ssize_t ob_refcnt;
    struct _typeobject *ob_type;
} PyObject;
```

В питоне объекты могут быть изменяемыми(mutable) и неизменемыми(immutable).
В случае с неизменяемыми объектами для изменения объекта нужно создать новый. Например, строка это неизмеяемый тип, а список -- изменяемый. 

Когда мы пишем выражение вида `some_str = "something here"` в памяти создается объект str и имя some_str связывается с этим объектом. Количество ссылок у объекта в памяти увеличивается на 1. Когда он удаляется оператором del -- уменьшается на 1.

Mutable
```python
>>> names = ['John']
>>> id(names)
140144987772488
>>> names.append('Gabe')
>>> id(names)
140144987772488
```
Immutable
```python
>>> name = 'John'
>>> id(name)
140144987780240
>>> name = 'Gabe'
>>> id(name)
140144987780296
```
 
Mutable

| type | Mutalbe |
|------|---------|
|list  | True    | 
|dict  | True    |
|set   | True    |
|bytearray | True    |

Immutable

| type | Mutalbe |
|-------|--------|
|frozenset | False |
| int   | False |
| str |False |
| float| False |
| tupe | False |
| bool | False |
|bytes | False    |



### Виды эквивалентности объектов
Функция id() возвращает идентичность объекта(иденфикатор) в виде числового значения, обычно указывающего на ячейку в памяти. Оператор **is** позволяет узнать эквивалентность идентификаторов. Оператор **==** тпозволяет узнать эквивалентность значений. 

```pythonte
>>> a = 'super string 12345aaaaa'
>>> b = 'super string 12345aaaaa'
>>> a is b
False
>>> a == b
True
```

Примечание: в целях производительности CPython кэширует некоторые числа(от -5 до 256) и строки.
```python
>>> first = 256
>>> second = 256
>>> first is second
True
>>> first = 257
>>> second = 257
>>> first is second
False
```

## Types and operations
### Dictionaries
Словарь представляет собой мутабельный контейнерный тип-отображение, хранящий отображение ключей в виде хэшируемых объектов на некоторые другие объекты(хэш таблица).

Создать словарь можно следующим образом:
```python
>>> numbers = {'one': 1, 'two': 2}
>>> numbers
{'one': 1, 'two': 2}
>>> numbers = dict(one=1, two=2)
>>> numbers
{'one': 1, 'two': 2}
>>> numbers = dict.fromkeys(['one', 'two'], 'default')
>>> numbers
{'one': 'default', 'two': 'default'}
>>> numbers = dict([('one', 1), ('two', 2)])
>>> numbers
{'one': 1, 'two': 2}
```

Основные операции:
* получить количество элементов в словаре 
```python
>>> len(numbers)
```
* прочитать элемент по ключу. При отсутвии элемента возникает исключение `KeyError`. Сабкласс может заимпелементить метод `__missing__(key)`, который будет вызываться при отсутствии ключа *key*.
```python
>>> numbers
{'one': 1, 'two': 2}
>>> numbers['one']
1
>>> numbers['not_existing_key']
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'not_existing_key'
```

* сопоставить ключ со значением
```python
>>> numbers
{'one': 1, 'two': 2}
>>> numbers['three'] = 3
>>> numbers
{'one': 1, 'two': 2, 'three': 3}
```
* удалить элемент по ключу
```python
>>> numbers
{'one': 1, 'two': 2, 'three': 3}
>>> del numbers['three']
>>> numbers
{'one': 1, 'two': 2}
```
* проверить, присутствует ли ключ в словаре
```python
>>> numbers
{'one': 1, 'two': 2}
>>> 'one' in numbers
True
>>> 1 in numbers
False
```
* получить итератор по ключам
```python
>>> numbers
{'one': 1, 'two': 2}
>>> it = iter(numbers)
>>> next(it)
'one'
```
* очистить словарь 
```python
>>> numbers
{'one': 1, 'two': 2}
>>> numbers.clear()
>>> numbers
{}
```
* взять поверхностную копию объекта
```python
>>> numbers = {'one': 1, 'two': 2}
>>> numbers_copy = numbers.copy()
>>> numbers is numbers_copy
False
>>> numbers == numbers_copy
True
```
* вернуть элемент по ключу, а вслучае отсутствия - элемент по умолчанию
```python
>>> numbers
{'one': 1, 'two': 2}
>>> numbers.get('three', 'empty')
'empty'
>>> numbers
{'one': 1, 'two': 2}
```
* получить список пар ключ/значение(элементов) в виде view-obj
```python
>>> numbers
{'one': 1, 'two': 2}
>>> numbers.items()
dict_items([('one', 1), ('two', 2)])
```
* получить список ключей в виде view-obj
```python
>>> numbers
{'one': 1, 'two': 2}
>>> numbers.keys()
dict_keys(['one', 'two'])
```
* получить список значений в виде view-obj
```python
>>> numbers
{'one': 1, 'two': 2}
>>> numbers.values()
dict_values([1, 2])
```
* удалить элемент по ключу и вернуть его
```python
>>> numbers.pop('two')
2
>>> numbers.pop('two')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'two'
```
* получить элемент по ключу и в случае отсутвия добавить его в словарь, вернув значение
```python
>>> numbers 
{'one': 1}
>>> two = numbers.setdefault('two', 2)
>>> two
2
>>> numbers
{'one': 1, 'two': 2}
```
* обновить словарь новыми данными(в виде словаря)
```python
>>> numbers
{'one': 1, 'two': 2}
>>> numbers.update({'three': 3})
>>> numbers
{'one': 1, 'two': 2, 'three': 3}
```
