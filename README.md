


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



