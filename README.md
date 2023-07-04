# Modul_2
<details>
  <summary>Пред- и постусловия</summary>
  
## Пред- и постусловия:

Я не буду заострять внимание на этих понятиях, так как они уже нам знакомы из теории тестирования, лишь вкратце скажу:


- Предусловие - то, что будет выполнено перед запуском теста.
- Постусловие - то, что будет выполнено после прохождения теста.

Самый простой пример использования пред и постусловий - это вынос инициализации драйвера в предусловие и закрытие браузера в постусловие

Но перед реализацией примера давайте посмотрим, какие методы отвечают за `пред` и `пост-условия`:

- `setup()` - все, что находится внутри метода, будет выполнено перед запуском каждого теста внутри класса.
- `teardown()` - все, что находится внутри метода, будет выполнено после завершение каждого теста внутри класса

Структура будет выглядеть следующим образом:

```

class TestLogin: # Название тестового класса
		
     def setup(): # Пред-условие для тестов внутри класса
	  pass

     # Тут пишутся тесты

     def teardown(): # Пост-условие для тестов внутри класса
	  pass

```
Т.е по сути своей это обертка для наших тестов.
Давайте теперь реализуем вынос инициализации драйвера в `setup()`, а закрытие браузера вынесем в `teardown()`


```

class TestLogin: # Название тестового класса

    def setup(self): # Пред-условие для тестов внутри класса
        self.service = Service(ChromeDriverManager().install())
        self.driver = webdriver.Chrome(service=self.service)

    # Тут пишутся тесты

    def teardown(self): # Пост-условие для тестов внутри класса
        self.driver.close() # Закрытие браузера


```       
        
Ну а теперь добавим простейший тест на открытие страницы


```

class TestLogin: # Название тестового класса

    def setup(self):
	print("Выполняюсь до теста")
        self.service = Service(ChromeDriverManager().install())
        self.driver = webdriver.Chrome(service=self.service)

    def test_open_login_page(self):
        self.driver.get("https://demoqa.com/login")
        assert self.driver.current_url == "https://demoqa.com/login", "Ошибка"

    def teardown(self):
        self.driver.close()
	print("Выполняюсь после теста")
	
	
```

Принты добавлены, чтобы в терминале было явно видно, что до и после теста все работает, теперь просто запустим его нажав на кнопку `play` напротив теста. Все работает)

Но в случае, если мы запустим тест через терминал командой `pytest test_login.py`, то увидим, что наши принты не печатаются в терминале. Как этого избежать рассматривается в следующей главе.

</details>

<details>
<summary>Базовые опции запуска тестов</summary>	
	
## Базовые опции запуска тестов:
	
У pytest существует множество параметров для запуска, и они очень полезны, но в этой главе мы изучим 2 базовых.
- `-s` - данный параметр как раз будет отображать принты, которые прописаны в коде
- `-v` - данный параметр будет предоставлять расширенный лог запуска тестов


Давайте к примерам:


1. Пример с принтами в коде `(- s)`
	
```	

class TestLogin: # Название тестового класса

    def setup(self):
        print("Выполняюсь до теста")
        self.service = Service(ChromeDriverManager().install())
        self.driver = webdriver.Chrome(service=self.service)

    def test_open_login_page(self):
        self.driver.get("https://demoqa.com/login")
        assert self.driver.current_url == "https://demoqa.com/login", "Ошибка"

    def teardown(self):
        self.driver.close()
        print("Выполняюсь после теста")	
	
</details>

```

Запустим тест с использованием параметра `-s`

```
pytest test_login.py -s

```
В результате, мы увидим наш заветный принт

```

platform darwin -- Python 3.10.5, pytest - 7.2.0, pluggy-1.0.0
rootdir: /User/Mila/PytestClasses/1_Module_Pytest/lesson_2
collected 1 item

______________________________________
test_login.py Выполняюсь до теста    !
                                     !
Выполняюсь после теста               !
______________________________________

	
	
```

2. Пример расширения лога (- v)

Давайте запустим тест, используя стандартную команду

```

  pytest test_login.py

		
```

В результате получаем такой лог:

```

---------------------------------------------------------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------------------------------------------------------
(venv) -> lesson_2 pytest test_login.ry -s
zsh: /opt/homebrew/bin/pytest: bad interpreter: /opt/homebrew/opt/python@3.8: no such file or directory
----------------------------------------------------------------------------------------------------------------------------------------
test_login.py Выполняюсь до теста
Выполняюсь после теста

		
```
А теперь запусти тест, используя параметр -v

```

  pytest test_login.py -v

		
```

В результате мы увидим то, какой тест запускаем, к какому классу он относится и статус выполнения теста

Как итог, я рекомендую использовать оба параметра и запускать тесты таким образом:

```

  pytest test_login.py -s -v

		
```
или
```

  pytest test_login.py -sv

		
```

</details>

<details>
<summary>Запуск тестов по их названию</summary>	
	
## Запуск тестов по их названию:

Часто есть необходимость запустить какой-то конкретный тест, а не кучу, соответственно, было бы здорово иметь такую возможность, и она есть)

Чтобы запустить тест по его названию, используйте опцию -k при запуске pytest.
Давайте рассмотрим пример, в котором у нас есть два следующих теста:

```

class TestLogin: # Название тестового класса

    def setup(self):
        print("Выполняюсь до теста")
        self.service = Service(ChromeDriverManager().install())
        self.driver = webdriver.Chrome(service=self.service)
	self.driver.get("https://demoqa.com/login")

    def test_open_login_page(self):
        assert self.driver.current_url == "https://demoqa.com/login", "Ошибка"
	
    def test_check_availiability_after_refresh(self):
        self.driver.refresh()
        assert "Заголовок страницы" in self.driver.page_source, "Страница не загрузилась"

    def teardown(self):
        self.driver.close()
        print("Выполняюсь после теста")
	
```
Но нам нужно запустить конкретный тест, к примеру, test_open_login_page, тогда для этого мы просто передаем название теста в качестве аргумента опции -k:

```

  pytest -k "test_open_login_page"

		
```

pytest -k "test_open_login_page"

</details>

<details>
  <summary>Дебаг тестов через pytest</summary>
  
## Дебаг тестов через pytest:

В pytest для дебага тестов я рекомендую использовать встроенный метод:
- `pytest.set_trace()` - данный метод служит брейкпоинтом

Использование `pytest.set_trace()` в коде максимально простое, вы просто вставляете эту строчку там, где хотите остановить выполнение теста.

Но перед этим необходимо импортировать pytest в файл с нашими тестами:

```
import pytest
```
Пример использования `pytest.set_trace()`

```
import pytest

from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager


class TestLogin: # Название тестового класса

    def setup(self):
        print("Выполняюсь до теста")
        self.service = Service(ChromeDriverManager().install())
        self.driver = webdriver.Chrome(service=self.service)

    def test_open_login_page(self):
        self.driver.get("https://demoqa.com/login")
	login_field = self.driver.find_element("xpath", "//input[@id='userName']")
	pytest.set_trace() # Включаем дебаг
	login_field.send_keys("Alexey")
        assert login_field.get_attribute("value") == "Alexey", "Некорректный логин"
		
    def teardown(self):
        self.driver.close()
	print("Выполняюсь после теста")
```
После того, как мы запустим тест, он в обычном режиме начнет свое выполнение, но остановится в том месте, где мы прописали `pytest.set_trace()`

После остановки теста, в терминале появится интерфейс управления дебагом, тут все просто, не пугайтесь)

```
>/Users/Mila/Pytest02/lesson02
-> login_field.send_keys("Mila")

(Pdb)

```

Тут важно понять ЧТО мы с вами видим!
- В первой строке мы видим исполняемый файл (файл с тестами)
- Во второй строке видим следующий шаг теста (он даже обозначается стрелочкой next), который идет после установленного нами `pytest.set_trace()`
- (Pdb) - строка ввода нашего дебагера

Теперь, чтобы двигаться в режиме дебага, можно использовать 2 способа:

1. Написать next в терминале, это переход на следующую строчку кода в нашем тесте.
Соответственно, выполнится шаг `login_field.send_keys(”Mila”)` и будет предложен следующий шаг:

```
-> login_field.send_keys("Mila")
(Pdb) next
> /Users/Mila/.../lrsson02/test_login_page2.py(16)
-> assert login_field.get_attribute("value") = "Mila", "Некорректный логин"

(Pdb)
```
2. Скопировать нужную строчку кода и вставить в терминал, более гибкий способ, потому что мы можем писать любой код, что в некоторых случаях поможет найти решение или гибко определить проблему.

```
-> login_field.send_keys("Mila")
(Pdb) assert login_field..get_attribute("value") = "Mila", "Некорректный логин"

(Pdb)
```

После того, как вы закончили с дебагом, необходимо остановить дебаг режим, и это можно сделать просто прописав exit.

</details>

<details>
  <summary>Задание</summary>
  
## Задание 1:

Основываясь на изученных темах, выполнить следующее задание:
1. Создать тестовый файл
2. В методе `setup()`
- Инициализировать драйвер
- Сгенерировать рандомный логин с доменом @yandex.ru
- Сгенерировать рандомный пароль
3. Написать тест, который вводит данные в соответствующие поля формы регистрации на сайте: https://www.freeconferencecall.com/

<details>











