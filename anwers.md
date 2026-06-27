# Подготовка
<details>    
<summary style='font-size: 20px'><b>Общее о проектах</b></summary>
</details>

<details>    
<summary style='font-size: 20px'><b>Тестирование</b></summary>
</details>

<details>    
<summary style='font-size: 20px'><b>Selenium</b></summary>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Selenium, Selenide, Selenoid</summary>
    <p style='font-size: 14px'>

| Технология | Краткое описание | Когда использовать |
|------------|------------------|--------------------|
| **Selenium** | Библиотека‑фреймворк для автоматизации браузеров (WebDriver). Позволяет писать скрипты, которые открывают страницу, кликают, вводят текст, читают DOM и т.д. | Если нужен полный контроль над браузером, нужна кросс‑браузерная поддержка, нет ограничений по API. |
| **Selenide** | «обёртка» над Selenium, написанная на Java (и Kotlin). Предоставляет более лаконичный синтаксис, автоматическое ожидание элементов, встроенную поддержку screenshots, отчётов и т.п. | Когда хочется писать чистый, читаемый код без ручного `Thread.sleep()` и `waits`. |
| **Selenoid** | Docker‑контейнер, реализующий Selenium Grid (встроенный диспетчер). Позволяет запускать тесты параллельно в разных браузерах без настройки отдельного сервера. | Если нужно быстро развернуть «grid» в CI‑pipeline без ручной настройки Selenium Hub/Nodes. |

---

## Пример кода на Java

### 1. Selenium (чистый WebDriver)

```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

public class SeleniumDemo {
    public static void main(String[] args) {
        System.setProperty(\"webdriver.chrome.driver\", \"/path/to/chromedriver\");
        WebDriver driver = new ChromeDriver();

        driver.get(\"https://example.com\");
        WebElement search = driver.findElement(By.name(\"q\"));
        search.sendKeys(\"Selenium\");
        search.submit();

        System.out.println(\"Title: \" + driver.getTitle());
        driver.quit();
    }
}
```

### 2. Selenide (чистый, коротко)

```java
import static com.codeborne.selenide.Selenide.*;
import static com.codeborne.selenide.Condition.*;

public class SelenideDemo {
    public static void main(String[] args) {
        open(\"https://example.com\");

        $(\"#search\").setValue(\"Selenide\").pressEnter();
        $(\"#result\").shouldHave(text(\"Selenide docs\"));

        closeWebDriver();  // закрыть браузер
    }
}
```

> **Плюсы Selenide**  
> - Автоматическое ожидание (`shouldHave`, `shouldBe`) – не надо писать `WebDriverWait`.  
> - Автоснимки при ошибках.  
> - Удобный API для работы с таблицами, выпадающими списками и т.д.

### 3. Как запустить тесты через Selenoid

```bash
# Запускаем Selenoid (Docker)
docker run -d \\
  -p 4444:4444 \\
  -p 5555:5555 \\
  -v /var/run/docker.sock:/var/run/docker.sock \\
  aerokube/selenoid:latest-release \\
  --conf /etc/selenoid/selenoid.json

# В конфиге указываем нужные браузеры, например Chrome 100
```

В тестах просто указываем URL Selenoid:

```java
System.setProperty(\"webdriver.remote.url\", \"http://localhost:4444/wd/hub\");
```

Теперь Selenium/Selenide будет запускать браузеры внутри Docker‑контейнеров, управляемых Selenoid.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Основные методы Selenium</summary>
    <p style='font-size: 14px'>

## Основные методы Selenium (Java)

| Категория | Метод | Что делает |
|-----------|-------|------------|
| **Инициализация драйвера** | `WebDriver driver = new ChromeDriver();` | Запускает браузер Chrome |
| **Навигация** | `driver.get(\"https://example.com\");`<br>`driver.navigate().to(\"https://example.com\");` | Переходит по URL |
| **Получение информации о странице** | `driver.getTitle();`<br>`driver.getCurrentUrl();`<br>`driver.getPageSource();` | Возвращает заголовок, текущий URL, исходный код |
| **Поиск элементов** | `driver.findElement(By.id(\"foo\"));`<br>`driver.findElements(By.className(\"bar\"));` | Находит один/много элементов |
| **Взаимодействие с элементами** | `element.click();`<br>`element.sendKeys(\"text\");`<br>`element.clear();` | Нажатие, ввод текста, очистка поля |
| **Получение данных из элементов** | `element.getText();`<br>`element.getAttribute(\"value\");` | Текст элемента, значение атрибута |
| **Работа с окнами/фреймами** | `driver.switchTo().window(handle);`<br>`driver.switchTo().frame(\"frameName\");`<br>`driver.switchTo().defaultContent();` | Переключение между окнами и фреймами |
| **Аллерты** | `driver.switchTo().alert();` | Открывает всплывающее окно | `Alert alert = driver.switchTo().alert();` |
| **Куки** | `driver.manage().addCookie(new Cookie(\"name\", \"value\"));`<br>`driver.manage().deleteCookieNamed(\"name\");` | Добавление/удаление cookie |
| **Ждёт (Wait)** | `driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);`<br>`WebDriverWait wait = new WebDriverWait(driver, 10);`<br>`wait.until(ExpectedConditions.visibilityOfElementLocated(By.id(\"foo\")));` | Неявное ожидание, явное ожидание |
| **Действия (Actions)** | `Actions actions = new Actions(driver);`<br>`actions.moveToElement(el).click().perform();` | Клик, перетаскивание, hover |
| **Завершение работы** | `driver.close();`<br>`driver.quit();` | Закрывает окно/браузер |
</p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Основные методы Selenide</summary>
    <p style='font-size: 14px'>

## Основные методы Selenide (Java)

| Что делает | Как вызвать | Краткое пояснение |
|------------|-------------|-------------------|
| **Открыть страницу** | `open(\"https://example.com\")` | Перейти на URL. |
| **Найти элемент по CSS‑селектору** | `$(\"#id\")`, `$(\".class\")`, `$(\"input[name='q']\")` | Возвращает `SelenideElement`. |
| **Найти элемент по XPath** | `$(By.xpath(\"//div[@data-test='button']\"))` | Аналогично CSS, но с XPath. |
| **Найти элемент в пределах другого** | `$(\"#parent\").find(\".child\")` | Скопировать поиск внутри родителя. |
| **Найти элемент в пределах другого (с Java 8)** | `$(\"#parent\").$(By.cssSelector(\".child\"))` | То же, но более читаемо. |
| **Нажать на элемент** | `$(\"#button\").click()` | Клик по элементу. |
| **Ввести текст** | `$(\"#input\").setValue(\"Selenide\")` | Записывает в поле. |
| **Получить текст** | `String txt = $(\"#label\").getText();` | Читает видимый текст. |
| **Проверка состояния** | `$(\"#checkbox\").shouldBe(Condition.checked)` | Ожидание, что чекбокс отмечен. |
| **Проверка текста** | `$(\"#title\").shouldHave(Condition.text(\"Welcome\"))` | Ожидание, что содержит текст. |
| **Проверка видимости** | `$(\"#modal\").shouldBe(Condition.visible)` | Ожидание, что элемент видим. |
| **Проверка отсутствия** | `$(\"#ads\").shouldNotBe(Condition.visible)` | Ожидание, что элемент скрыт. |
| **Установить задержку ожидания** | `Configuration.timeout = 5000;` | Установить таймаут в миллисекундах. |
| **Скриншот** | `Selenide.screenshot(\"name\")` | Сохраняет скриншот. |
| **Получить текущий URL** | `String url = WebDriverRunner.url();` | Возвращает адрес страницы. |

---

### Минимальный пример

```java
import static com.codeborne.selenide.Selenide.*;
import static com.codeborne.selenide.Condition.*;

public class Demo {
    public static void main(String[] args) {
        // 1. Открываем страницу
        open(\"https://demoqa.com/text-box\");

        // 2. Заполняем поля
        $(\"#userName\").setValue(\"John Doe\");
        $(\"#userEmail\").setValue(\"john@example.com\");
        $(\"#currentAddress\").setValue(\"123 Main St\");
        $(\"#permanentAddress\").setValue(\"123 Main St\");

        // 3. Отправляем форму
        $(\"#submit\").click();

        // 4. Проверяем, что данные отображаются
        $(\"#output\").shouldHave(text(\"John Doe\"));
    }
}
```
</p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Основы Selenoid</summary>
    <p style='font-size: 14px'>

## Что такое Selenoid?

| Что | Как работает |
|-----|---------------|
| **Selenoid** | Лёгкая альтернатива Selenium Grid, которая запускает браузеры в Docker‑контейнерах. |
| **Преимущества** | Быстрее, меньше ресурсов, можно быстро менять версии браузеров, легко масштабировать. |
| **Требования** | Docker (или Docker‑Compose) + небольшая конфигурация. |

---

## Как это выглядит в действии

1. **Запускаем Selenoid**

   ```bash
   docker run -d --name selenoid \\
     -p 4444:4444 \\
     -v /var/run/docker.sock:/var/run/docker.sock \\
     -v $(pwd)/config.json:/etc/selenoid/config.json \\
     aerokube/selenoid:latest-release
   ```

2. **Подключаемся из Java**
   ```java
   import org.openqa.selenium.WebDriver;
   import org.openqa.selenium.remote.DesiredCapabilities;
   import org.openqa.selenium.remote.RemoteWebDriver;
   import java.net.URL;

   public class SelenoidDemo {
       public static void main(String[] args) throws Exception {
           DesiredCapabilities caps = new DesiredCapabilities();
           caps.setCapability(\"browserName\", \"chrome\");
           caps.setCapability(\"browserVersion\", \"latest\");
           caps.setCapability(\"selenoid:options\", Map.of(
                   \"enableVNC\", true,
                   \"enableVideo\", false
           ));

           WebDriver driver = new RemoteWebDriver(
                   new URL(\"http://localhost:4444/wd/hub\"),
                   caps
           );

           driver.get(\"https://example.com\");
           System.out.println(driver.getTitle());
           driver.quit();
       }
   }
   ```

   *Важно*: указываем `selenoid:options` – это настройки, специфичные для Selenoid.

3. **Управление версиями браузеров**  
   Создаём файл `config.json` (или используем Docker‑Compose) с перечнем поддерживаемых браузеров и их образов:

   ```json
   {
     \"browsers\": {
       \"chrome\": {
         \"default\": \"90.0\",
         \"versions\": {
           \"90.0\": {
             \"image\": \"aerokube/chrome:90.0\",
             \"port\": \"4444\"
           }
         }
       },
       \"firefox\": {
         \"default\": \"88.0\",
         \"versions\": {
           \"88.0\": {
             \"image\": \"aerokube/firefox:88.0\",
             \"port\": \"4444\"
           }
         }
       }
     }
   }
   ```
</p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Основы Selenium Grid</summary>
    <p style='font-size: 14px'>

## Что такое Selenium Grid?

Selenium Grid – это инструмент, который позволяет **разбивать** выполнение ваших тестов по разным машинам (или виртуальным машинам/контейнерам) и **параллельно** запускать их.  
В простых словах:
- **Hub** – центральный сервер, куда «подключаются» все тесты.
- **Node** – отдельная машина/контейнер, на которой реально открывается браузер и выполняется тест.

Грид экономит время: вместо того, чтобы ждать, пока один тест завершится, сразу запускается несколько одновременно.

---

## Как это работает

| Шаг | Что делаем | Что происходит |
|-----|------------|----------------|
| 1 | Запускаем **Hub** | Создаём точку входа для всех тестов |
| 2 | Подключаем **Nodes** | Каждый node регистрируется в hub и сообщает, какие браузеры доступны |
| 3 | Запускаем тесты | Тесты отправляют запросы в hub, который «пробрасывает» их к нужному node'у |
| 4 | Получаем результаты | Hub собирает отчёты от всех nodes и возвращает их в тестовый Runner |

---

## Минимальный пример (Java)

### 1. Запуск Hub

```bash
java -jar selenium-server-4.8.0.jar hub
```

Hub будет слушать `http://localhost:4444/grid/console`.

### 2. Запуск Node (на той же машине, но можно и на другой)

```bash
java -jar selenium-server-4.8.0.jar node --detect-drivers
```

Node автоматически найдёт драйверы (ChromeDriver, GeckoDriver и т.д.) и зарегистрируется в hub.

> **Tip:** Можно указать конкретный URL hub'а, если node запускается на другом сервере:  
> `--hub http://<hub-ip>:4444/grid/register`

### 3. Тест на Grid

```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.remote.RemoteWebDriver;
import org.openqa.selenium.remote.DesiredCapabilities;
import java.net.URL;

public class GridExample {
    public static void main(String[] args) throws Exception {
        // Указываем URL hub'а
        URL hubUrl = new URL(\"http://localhost:4444/wd/hub\");

        // Задаём, какой браузер нам нужен
        DesiredCapabilities caps = DesiredCapabilities.chrome();

        // Открываем соединение с hub'ом
        WebDriver driver = new RemoteWebDriver(hubUrl, caps);

        // Ваш тест
        driver.get(\"https://example.com\");
        System.out.println(\"Title: \" + driver.getTitle());

        driver.quit();
    }
}
```

> **Важно**: `RemoteWebDriver` – это клиент, который говорит hub'у, куда отправлять команды. Hub сам решит, какой node выполнит тест.

---

## Когда стоит использовать Grid

| Сценарий | Почему Grid помогает |
|----------|----------------------|
| **Параллельные тесты** | Запускается несколько тестов одновременно, время сокращается. |
| **Кросс‑браузерное тестирование** | Один hub, несколько nodes с разными браузерами. |
| **Тесты в разных ОС** | Nodes могут работать на Windows, macOS, Linux. |
| **Тесты в облаке** | Можно подключить облачные сервисы (Sauce Labs, BrowserStack) как Nodes. |
</p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Основы RemoteWebDriver</summary>
    <p style='font-size: 14px'>

## Что такое `RemoteWebDriver`?

`RemoteWebDriver` – это реализация интерфейса **WebDriver**, которая позволяет управлять браузером, который запущен **не на той же машине**, где выполняется ваш тест.  
Он посылает команды по сети (HTTP) к **Selenium Grid** или к любому другому серверу, который реализует протокол WebDriver.

---

## Как это работает

| Шаг | Что происходит |
|-----|----------------|
| 1 | Вы создаёте объект `RemoteWebDriver` и передаёте ему **URL сервера** (например, `http://localhost:4444/wd/hub`) и **DesiredCapabilities** (настройки браузера). |
| 2 | `RemoteWebDriver` открывает TCP‑соединение с сервером и начинает отправлять HTTP‑запросы вида `POST /session` (создать сессию), `GET /session/{id}/element`, `POST /session/{id}/click`, и т.д. |
| 3 | Сервер (Grid, Selenium Standalone, BrowserStack, SauceLabs и др.) получает запрос, запускает браузер (если ещё не запущен) и выполняет действие. |
| 4 | Результат возвращается клиенту в виде JSON, который `RemoteWebDriver` преобразует в объекты Java (`WebElement`, `String`, `Boolean` и т.д.). |

> **Плюс** – можно запускать тесты на разных машинах/операционных системах, в разных браузерах, параллельно.  
> **Минус** – небольшая задержка из‑за сетевого обмена.

---

## Минимальный пример (Java)

```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.remote.DesiredCapabilities;
import org.openqa.selenium.remote.RemoteWebDriver;
import org.openqa.selenium.By;
import java.net.URL;

public class RemoteTest {
    public static void main(String[] args) throws Exception {
        // 1. Указываем URL Selenium Grid (или Standalone)
        URL gridUrl = new URL(\"http://localhost:4444/wd/hub\");

        // 2. Настраиваем желаемый браузер
        DesiredCapabilities caps = new DesiredCapabilities();
        caps.setBrowserName(\"chrome\");   // Chrome
        caps.setPlatform(org.openqa.selenium.Platform.ANY);

        // 3. Создаём RemoteWebDriver
        WebDriver driver = new RemoteWebDriver(gridUrl, caps);

        // 4. Открываем страницу и делаем простое действие
        driver.get(\"https://www.example.com\");
        System.out.println(\"Title: \" + driver.getTitle());

        // 5. Закрываем браузер и сессию
        driver.quit();
    }
}
```

> **Важно**:
> * `gridUrl` – это адрес сервера, к которому вы подключаетесь.
> * `DesiredCapabilities` можно заменить на `ChromeOptions`, `FirefoxOptions` и т.д.
> * Если вы используете облачные сервисы (BrowserStack, SauceLabs), в их URL обычно включены логин и пароль в формате `https://user:pass@hub.browserstack.com/wd/hub`.

---

## Краткие отличия от обычного `WebDriver`

| Параметр | `WebDriver` (локальный) | `RemoteWebDriver` |
|----------|-------------------------|-------------------|
| Где запускается браузер | На той же машине, где тест | На удалённом сервере |
| Как создаётся сессия | `new ChromeDriver()` | `new RemoteWebDriver(url, caps)` |
| Параллельность | Требует локального управления | Легко масштабируется через Grid |
| Задержка | Минимальная | Сеть + сериализация JSON |

---

## Когда стоит использовать `RemoteWebDriver`?

1. **Параллельные тесты** – запуск сотен тестов на разных машинах/браузерах.
2. **Тесты на разных ОС** – Windows, macOS, Linux, мобильные платформы.
3. **Интеграция с CI/CD** – Jenkins, GitLab CI, GitHub Actions, где тесты запускаются в контейнерах.
4. **Облачные сервисы** – BrowserStack, SauceLabs, TestingBot и др.

 </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Ожидания в Selenium</summary>
    <p style='font-size: 14px'>

## Ожидания (Waits) в Selenium

Ожидания позволяют дождаться, пока элемент станет доступен, видимым, кликабельным и т.д. Это помогает избежать ошибок `NoSuchElementException`, `ElementNotVisibleException` и т.п.

| Тип ожидания | Когда использовать | Как выглядит в Java |
|--------------|--------------------|---------------------|
| **Implicit wait** | Когда вам нужно, чтобы *все* поиски элементов автоматически ждут до указанного времени. | `driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);` |
| **Explicit wait** | Когда нужно дождаться конкретного условия для конкретного элемента. | `WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));` |
| **Fluent wait** | Когда нужно настроить частоту проверки, игнорировать исключения и задать таймаут. | `Wait<WebDriver> wait = new FluentWait<>(driver).withTimeout(Duration.ofSeconds(30)).pollingEvery(Duration.ofSeconds(5)).ignoring(NoSuchElementException.class);` |

---

### 1. Implicit Wait

```java
WebDriver driver = new ChromeDriver();
driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);
// Теперь поиск element будет ждать до 10 сек. автоматически
WebElement btn = driver.findElement(By.id(\"submit\"));
```
`Implicit wait` – это глобальная настройка драйвера, которая говорит Selenium, сколько времени он должен «потягивать» поиск элемента, прежде чем выбросить исключение `NoSuchElementException`.

*Плюс:* прост в использовании.  
*Минус:* влияет на все элементы, может замедлить тесты, не всегда предсказуемый результат.

---

### 2. Explicit Wait

```java
WebDriver driver = new ChromeDriver();
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));

WebElement loginBtn = wait.until(
        ExpectedConditions.elementToBeClickable(By.id(\"login\"))
);
loginBtn.click();
```
`Explicit Wait` – это «умное» ожидание. В отличие от `implicit wait`, которое заставляет драйвер ждать *всегда* при поиске элемента, explicit wait ждёт только тот элемент, который вам нужен, и только до тех пор, пока условие не выполнится.

*Плюс:* точный контроль над тем, что и когда ждём.  
*Минус:* нужно писать условие для каждого элемента.

---

### 3. Fluent Wait

```java
WebDriver driver = new ChromeDriver();

Wait<WebDriver> wait = new FluentWait<>(driver)
        .withTimeout(Duration.ofSeconds(30))      // общий таймаут
        .pollingEvery(Duration.ofSeconds(5))     // интервал проверки
        .ignoring(NoSuchElementException.class); // игнорируем ошибку

WebElement searchBox = wait.until(driver1 ->
        driver1.findElement(By.id(\"search\"))
);
searchBox.sendKeys(\"Selenium\");
```
`Fluent Wait` — это «умное» ожидание, потому что позволяет гибко управлять частотой проверки и исключениями, делая тесты более надёжными и быстрыми. Он особенно полезен, когда элемент появляется в разное время, а не сразу.
> `Explicit Wait` – это просто удобный способ реализации `Fluent Wait` с предопределёнными условиями.  
> `Fluent Wait` более гибок: вы можете задать собственный polling‑интервал, игнорировать любые исключения и писать произвольную логику ожидания.

*Плюс:* гибко настраиваемое.  
*Минус:* чуть сложнее читать.

---

### Полезные `ExpectedConditions`

```java
ExpectedConditions.visibilityOfElementLocated(By.cssSelector(\".loader\"))
ExpectedConditions.invisibilityOfElementLocated(By.id(\"spinner\"))
ExpectedConditions.elementToBeClickable(By.xpath(\"//button[text()='Submit']\"))
ExpectedConditions.presenceOfAllElementsLocatedBy(By.className(\"item\"))
```
 </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Ожидания в Selenide</summary>
    <p style='font-size: 14px'>

## Что такое ожидания в Selenide?

В Selenide **ожидания** – это встроенный механизм, который автоматически ждёт, пока элемент станет доступным, видимым, кликабельным и т.д.  

| Что ждём | Как пишем | Что делает |
|----------|-----------|------------|
| **Элемент виден** | `$(\"#login\").shouldBe(Condition.visible)` | Ждёт, пока элемент появится на странице |
| **Элемент кликабелен** | `$(\"#login\").shouldBe(Condition.enabled)` | Ждёт, пока элемент не станет неактивным |
| **Текст в элементе** | `$(\"#error\").shouldHave(Condition.text(\"Ошибка\"))` | Ждёт, пока текст совпадёт |
| **Только что появился** | `$(\"#new\").should(Condition.exist)` | Ждёт, пока элемент появится в DOM |
| **Только что исчез** | `$(\"#old\").shouldNot(Condition.exist)` | Ждёт, пока элемент исчезнет |

### Как это работает

1. **Внутренний таймаут** – по умолчанию 4 с (можно изменить через `Configuration.timeout`).
2. В течение этого времени Selenide проверяет условие каждые 100 мс.
3. Если условие выполняется – тест продолжается.
4. Если таймаут истек – выбрасывается `WaitTimeoutException` и тест падает.

### Изменяем таймаут только для одного элемента

```java
$(\"#login\")
    .shouldBe(Condition.visible, Duration.ofSeconds(10)); // 10 сек
```

### Пример полноценного теста

```java
import static com.codeborne.selenide.Selenide.*;
import static com.codeborne.selenide.Condition.*;

public class LoginTest {
    @Test
    public void shouldLoginSuccessfully() {
        // 1. Открываем страницу
        open(\"https://example.com/login\");

        // 2. Заполняем поля
        $(\"#username\").setValue(\"user\");
        $(\"#password\").setValue(\"pass\");

        // 3. Кликаем кнопку (ждём, пока она будет кликабельна)
        $(\"#loginBtn\").click();

        // 4. Ожидаем, что появится приветствие
        $(\"#welcome\").shouldBe(visible, Duration.ofSeconds(8));

        // 5. Проверяем, что текст корректный
        $(\"#welcome\").shouldHave(text(\"Добро пожаловать, user!\"));
    }
}
```
## Разница между `shouldBe` и `should` в Selenide

| Метод | Что делает | Какой аргумент принимает | Коротко о применении |
|-------|------------|--------------------------|---------------------|
| `shouldBe` | Проверяет **состояние** элемента (например, видимость, наличие, активность). | `Condition` (или массив `Condition`), например `visible`, `enabled`, `text(\"Hello\")` | Используется, когда нужно убедиться, что элемент **соответствует** определённому состоянию. |
| `should` | Проверяет **какой‑то** **условный** результат, но может принимать **пользовательские** условия, функции‑лямбды, а также `Condition` как в `shouldBe`. | `Condition` или `Condition` + `timeout`/`waitUntil` | Используется, когда нужно проверить **любое условие**, иногда с собственным таймаутом или более сложной логикой. |

### Пример

```java
// 1. Проверяем, что элемент видим
$(\"#loginButton\").shouldBe(Condition.visible);

// 2. Проверяем, что у элемента текст «Войти»
$(\"#loginButton\").shouldHave(Condition.exactText(\"Войти\"));

// 3. Проверяем, что элемент содержит нужный текст, но с таймаутом 10 сек
$(\"#loginButton\").should(Condition.text(\"Войти\"), Duration.ofSeconds(10));

// 4. Пользовательское условие (например, цвет текста)
$(\"#loginButton\").should(driver -> {
    String color = driver.findElement(By.id(\"loginButton\")).getCssValue(\"color\");
    return \"rgba(0, 128, 0, 1)\".equals(color);
});
```
</p>
    </details>
</details>
<details>    
<summary style='font-size: 20px'><b>Java</b></summary>
</details>

<details>    
<summary style='font-size: 20px'><b>SQL</b></summary>
</details>

<details>    
<summary style='font-size: 20px'><b>Наш проект</b></summary>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>TestNG</summary>
    <p style='font-size: 14px'>

## Что такое TestNG?

TestNG (Test Next Generation) – это фреймворк для модульного и функционального тестирования Java‑приложений.  
Он вдохновлен JUnit, но добавляет более гибкую и мощную схему аннотаций, конфигурации и параллельного выполнения тестов.

---

## Ключевые аннотации

| Аннотация | Что делает | Когда использовать |
|-----------|------------|--------------------|
| `@Test` | Описывает сам тестовый метод | В каждом тесте |
| `@BeforeSuite` | Выполняется один раз до всех тестов в Suite | Инициализация ресурсов |
| `@AfterSuite` | Выполняется один раз после всех тестов | Очистка ресурсов |
| `@BeforeTest` | До каждого `<test>` в XML | Подготовка окружения |
| `@AfterTest` | После каждого `<test>` | Очистка окружения |
| `@BeforeClass` | До первого метода в классе | Создание объектов, которые нужны всем методам класса |
| `@AfterClass` | После последнего метода в классе | Уничтожение общих объектов |
| `@BeforeMethod` | До каждого тестового метода | Инициализация данных для конкретного теста |
| `@AfterMethod` | После каждого тестового метода | Очистка данных после теста |
| `@DataProvider` | Предоставляет данные для параметризованных тестов | Тесты с разными входными данными |
| `@Parameters` | Читает параметры из XML | Конфигурирование тестов |

---

## Пример простого теста

```java
import org.testng.Assert;
import org.testng.annotations.*;

public class CalculatorTest {

    private Calculator calc;

    @BeforeClass
    public void setUp() {
        calc = new Calculator(); // простая модель калькулятора
    }

    @Test(priority = 1, description = \"Проверка сложения\")
    public void testAdd() {
        int result = calc.add(2, 3);
        Assert.assertEquals(result, 5, \"Сложение 2+3 должно дать 5\");
    }

    @Test(dataProvider = \"multiplicationData\", description = \"Проверка умножения\")
    public void testMultiply(int a, int b, int expected) {
        int result = calc.multiply(a, b);
        Assert.assertEquals(result, expected,
                String.format(\"Умножение %d*%d должно дать %d\", a, b, expected));
    }

    @DataProvider(name = \"multiplicationData\")
    public Object[][] dataProviderMethod() {
        return new Object[][]{
                {2, 3, 6},
                {5, 0, 0},
                {7, 4, 28}
        };
    }

    @AfterClass
    public void tearDown() {
        calc = null; // освобождаем ресурсы
    }
}
```

---

## Конфигурация через `testng.xml`

```xml
<!DOCTYPE suite SYSTEM \"https://testng.org/testng-1.0.dtd\" >
<suite name=\"MySuite\" parallel=\"tests\" thread-count=\"2\">

    <test name=\"CalculatorTests\">
        <classes>
            <class name=\"CalculatorTest\"/>
        </classes>
    </test>

</suite>
```

* `parallel=\"tests\"` – параллельное выполнение разных `<test>` блоков.
* `thread-count=\"2\"` – сколько потоков использовать.

---

## Параллельное тестирование

TestNG позволяет запускать тесты параллельно:

```java
@Test(timeOut = 3000) // 3 сек
public void longRunningTest() {
    // код, который может занять до 3 секунд
}
```

При `parallel=\"methods\"` тестовые методы будут выполняться в отдельных потоках.

---

## Группы тестов

```java
@Test(groups = {\"fast\", \"math\"})
public void quickTest() { ... }

@Test(groups = {\"slow\", \"api\"})
public void longApiTest() { ... }
```

В `testng.xml` можно включать/исключать группы:

```xml
<groups>
    <run>
        <include name=\"fast\"/>
    </run>
</groups>
```

---

## Выводы

* **TestNG** – мощный, гибкий фреймворк для Java‑тестов.
* Используйте аннотации для управления порядком и подготовкой/очисткой окружения.
* Параметризованные тесты (`@DataProvider`) упрощают проверку разных наборов данных.
* Конфигурация через XML даёт контроль над параллелизмом и группами.
 </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Maven</summary>
    <p style='font-size: 14px'>

## Что такое Maven?

Maven – это **система управления проектами** и **сборки** для Java (и других JVM‑языков).  
Она позволяет:

1. **Организовать структуру проекта** (src/main/java, src/test/java и т.д.).
2. **Управлять зависимостями** – библиотеками, которые нужны вашему коду.
3. **Выполнять сборку** (компиляцию, тесты, упаковку в JAR/WAR).
4. **Публиковать артефакты** в репозитории (Maven Central, Nexus, Artifactory).

---

## Стандартная структура проекта

```
my-app/
├─ pom.xml                ← главный файл конфигурации
├─ src/
│  ├─ main/
│  │  ├─ java/            ← ваш исходный код
│  │  └─ resources/       ← статические файлы (XML, properties)
│  └─ test/
│     ├─ java/            ← тесты
│     └─ resources/
└─ target/                ← сгенерированные файлы (JAR, классы)
```

---

## `pom.xml` – главный конфиг

```xml
<project xmlns=\"http://maven.apache.org/POM/4.0.0\"
         xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\"
         xsi:schemaLocation=\"http://maven.apache.org/POM/4.0.0
                             http://maven.apache.org/xsd/maven-4.0.0.xsd\">

    <modelVersion>4.0.0</modelVersion>

    <!-- Базовая информация -->
    <groupId>com.example</groupId>
    <artifactId>my-app</artifactId>
    <version>1.0.0</version>
    <packaging>jar</packaging>

    <!-- Зависимости (libraries) -->
    <dependencies>
        <!-- JUnit 5 -->
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter</artifactId>
            <version>5.10.0</version>
            <scope>test</scope>
        </dependency>
        <!-- Spring Boot (если нужен) -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>3.2.2</version>
        </dependency>
    </dependencies>

    <!-- Плагины (build tools) нужны для выполнения всех реальных задач по сборке и управлению проектом -->
    <build>
        <plugins>
            <!-- Компилятор Java -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.12.0</version>
                <configuration>
                    <source>17</source>
                    <target>17</target>
                </configuration>
            </plugin>

            <!-- Surefire – запуск тестов -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.1.2</version>
            </plugin>
        </plugins>
    </build>
</project>
```

### Ключевые элементы

| Элемент | Что делает |
|---------|------------|
| `groupId` | Идентификатор группы (часто пакет `com.example`). |
| `artifactId` | Имя проекта. |
| `version` | Версия артефакта. |
| `packaging` | Какой файл будет создан (`jar`, `war`, `pom`). |
| `<dependencies>` | Список зависимостей. |
| `<build>` | Плагины, которые выполняют задачи сборки. |

---

## Maven‑lifecycle (цикл сборки)

1. **validate** – проверить, что всё правильно (проект существует).
2. **compile** – компиляция `src/main/java`.
3. **test** – запуск тестов (`src/test/java`).
4. **package** – собрать артефакт (`jar`, `war`).
5. **install** – установить артефакт в локальный репозиторий (`~/.m2`).
6. **deploy** – отправить артефакт в удалённый репозиторий (Nexus, Artifactory).

Можно запустить конкретный этап, например:

```bash
mvn compile          # только компиляция
mvn test             # только тесты
mvn package          # сборка JAR
```

---

## Как добавить зависимость

```bash
mvn dependency:tree   # посмотреть все зависимости
```

Если зависимость не найдена, добавьте репозиторий:

```xml
<repositories>
    <repository>
        <id>central</id>
        <url>https://repo.maven.apache.org/maven2</url>
    </repository>
</repositories>
```

---

## Быстрый старт: создаём новый проект

```bash
mvn archetype:generate \\
  -DgroupId=com.example \\
  -DartifactId=my-app \\
  -DarchetypeArtifactId=maven-archetype-quickstart \\
  -DinteractiveMode=false
```

После этого появится готовая структура проекта и `pom.xml`.

---

## Полезные команды

| Команда | Что делает |
|---------|------------|
| `mvn clean` | Удалить `target/` и начать с чистого листа. |
| `mvn package` | Собрать JAR/WAR. |
| `mvn test` | Запустить все юнит‑тесты. |
| `mvn dependency:tree` | Показать дерево зависимостей. |
| `mvn -X` | Включить подробный лог (для отладки). |

---

## Итоги

- **Maven** упрощает управление зависимостями и сборкой.
- Главное – `pom.xml` – описание проекта, зависимостей и плагинов.
- Стандартная структура проекта делает код понятным и совместимым с CI/CD.
- Вопросы, связанные с Maven, обычно касаются: как добавить зависимость, как изменить фазу сборки, как работать с профилями и т.д.

Если понадобится конкретный пример (например, как подключить TestNG или как настроить профиль для dev/production) – дайте знать!
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Gradle</summary>
    <p style='font-size: 14px'>

## Что такое Gradle?

| Что | Как это выглядит |
|-----|------------------|
| **Gradle** – система сборки, которая умеет **компилировать код, собирать артефакты, запускать тесты, генерировать отчёты** и даже **публиковать** их. |
| **DSL** (Domain‑Specific Language) – Gradle пишет **скрипты** на Groovy или Kotlin. Самый популярный – `build.gradle` (Groovy) или `build.gradle.kts` (Kotlin). |
| **Плагины** – «модульные» расширения, которые добавляют готовые задачи: `java`, `application`, `maven-publish`, `jacoco`, `checkstyle` и т.д. |

---

**Gradle Wrapper** – это способ «упаковать» Gradle вместе с проектом, чтобы любой, кто работает над кодом, мог запускать сборку без установки Gradle вручную.

| Что делает | Как выглядит |
|------------|--------------|
| **Гарантирует одинаковую версию** Gradle для всех участников | `gradlew` (Linux/macOS) / `gradlew.bat` (Windows) |
| **Не требует установки** Gradle на машине | В репозитории проекта находятся файлы `gradle/wrapper/gradle-wrapper.jar` и `gradle/wrapper/gradle-wrapper.properties` |
| **Облегчает CI/CD** – скрипты просто вызывают `./gradlew build` | |

## Структура простого `build.gradle` (Groovy)

```groovy
plugins {
    id 'java'                // основной плагин для Java‑проектов
    id 'application'         // позволяет запускать приложение
}

group = 'com.example'
version = '1.0.0'

sourceCompatibility = JavaVersion.VERSION_17
targetCompatibility = JavaVersion.VERSION_17

repositories {
    mavenCentral()           // где искать зависимости
}

dependencies {
    implementation 'org.apache.commons:commons-lang3:3.12.0' // обычная зависимость
    testImplementation 'org.junit.jupiter:junit-jupiter:5.9.0' // тесты
}

application {
    mainClass = 'com.example.Main'   // точка входа
}

tasks.test {
    useTestNG()               // говорим Gradle, что будем использовать TestNG
}
```

### Как запустить

```bash
./gradlew build          # собрать проект и запустить тесты
./gradlew run            # запустить приложение (если подключён плагин application)
./gradlew clean          # удалить все сгенерированные файлы
```

> **Gradle Wrapper** (`gradlew`/`gradlew.bat`) гарантирует, что любой, кто клонирует репозиторий, будет использовать *точно* ту же версию Gradle, что и автор проекта.  
> Создать его можно командой: `gradle wrapper --gradle-version 8.0`.

---

## Основные понятия

| Понятие | Что это |
|---------|---------|
| **Project** | единица, которую собирают (может быть корневой и подпроектами) |
| **Task** | конкретное действие (например, `compileJava`, `test`, `jar`) |
| **Configuration** | набор зависимостей, которые нужны для конкретного фазы сборки |
| **Plugin** | набор задач и конфигураций, добавляемый в проект |
| **Source Set** | группы исходных файлов (`main`, `test`) |

---

## Как добавить собственную задачу

```groovy
task hello {
    doLast {
        println 'Привет, Gradle!'
    }
}
```

Запустить: `./gradlew hello`.

---

## Мульти‑проектный билд

`settings.gradle`:

```groovy
rootProject.name = 'my-app'
include 'core', 'api', 'web'
```

Каждый подпроект имеет свой собственный `build.gradle`, но они наследуют общие настройки из `rootProject`.

---

## Быстродействие и кэш

| Фича | Как помогает |
|------|--------------|
| **Gradle Daemon** | Сохраняет JVM в фоне, ускоряет последующие сборки |
| **Incremental Build** | Пересобирает только изменённые файлы |
| **Build Cache** | Делит кэш между машинами, экономит время в CI |
| **Configuration on Demand** | Конфигурирует только нужные проекты |

В `gradle.properties` можно включить:

```properties
org.gradle.daemon=true
org.gradle.parallel=true
org.gradle.caching=true
```

---

## Тестирование

- `tasks.test` – стандартная задача для JUnit/TestNG.
- Можно добавить собственный набор тестов:

```groovy
task integrationTest(type: Test) {
    description = 'Runs integration tests'
    group = 'verification'
    testClassesDirs = sourceSets.integrationTest.output.classesDirs
    classpath = sourceSets.integrationTest.runtimeClasspath
    useTestNG()
}
```

Затем подключить к `check`:

```groovy
check.dependsOn integrationTest
```

---

## CI/CD

Для Jenkins, GitHub Actions, GitLab CI и др. достаточно добавить:

```yaml
- name: Build
  run: ./gradlew build --no-daemon
- name: Test
  run: ./gradlew test
- name: Publish
  run: ./gradlew publish
```

Gradle автоматически определит, какие задачи нужны, и выполнит их.

---

## Полезные команды

| Команда | Что делает |
|---------|------------|
| `./gradlew clean` | Удаляет `build/` каталог |
| `./gradlew assemble` | Собирает JAR без тестов |
| `./gradlew check` | Запускает тесты + другие проверки (если есть) |
| `./gradlew dependencies` | Показывает дерево зависимостей |
| `./gradlew tasks` | Список всех доступных задач |

---

## Итоги

- **Gradle** – гибкая и быстрая система сборки для Java (и многих других языков).
- **DSL** – Groovy/Kotlin, легко читается и расширяется.
- **Плагины** – добавляют готовые задачи, не надо писать их вручную.
- **Wrapper** – гарантирует совместимость версий.
- **Кэш и Daemon** – делают сборки быстрыми даже для больших проектов.
- **Мульти‑проект** – удобно организовать крупные проекты с несколькими модулями.
 </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Jackson</summary>
    <p style='font-size: 14px'>

## Что такое Jackson?

Jackson — это популярная Java‑библиотека для работы с JSON.  
Она умеет **сериализовать** (превращать Java‑объекты в JSON) и **десериализовать** (JSON → Java‑объекты), а также предоставляет гибкие инструменты для настройки поведения.

---

## Ключевые компоненты

| Компонент | Зачем нужен |
|-----------|-------------|
| `ObjectMapper` | «главный» класс, который делает сериализацию/десериализацию |
| `JsonNode` | дерево JSON‑значений (если не нужно привязывать к POJO) |
| `JsonSerializer` / `JsonDeserializer` | пользовательские (де)серилизаторы |
| Аннотации (`@JsonProperty`, `@JsonIgnore`, …) | управляют поведением при (де)сериализации |
| `JsonFactory` | низкоуровневая настройка потоков (если нужен парсер/генератор вручную) |

---

## Быстрый старт

### 1. Maven/Gradle зависимость

```xml
<!-- Maven -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.17.0</version>
</dependency>
```

```gradle
// Gradle
implementation 'com.fasterxml.jackson.core:jackson-databind:2.17.0'
```

### 2. Сериализация POJO → JSON

```java
import com.fasterxml.jackson.databind.ObjectMapper;

public class User {
    public int id;
    public String name;

    // Конструктор, геттеры/сеттеры необязательны
}

ObjectMapper mapper = new ObjectMapper();
User user = new User();
user.id = 1;
user.name = \"Alice\";

String json = mapper.writeValueAsString(user);
// {\"id\":1,\"name\":\"Alice\"}
```

### 3. Десериализация JSON → POJO

```java
String json = \"{\\\"id\\\":2,\\\"name\\\":\\\"Bob\\\"}\";
User user = mapper.readValue(json, User.class);
// user.id == 2, user.name == \"Bob\"
```

### 4. Управление полями через аннотации

```java
import com.fasterxml.jackson.annotation.JsonIgnore;
import com.fasterxml.jackson.annotation.JsonProperty;

public class User {
    public int id;

    @JsonProperty(\"full_name\")   // В JSON будет key «full_name»
    public String name;

    @JsonIgnore                 // Поле игнорируется при (де)сериализации
    public String password;
}
```

### 5. Динамический JSON: `JsonNode`

```java
JsonNode tree = mapper.readTree(\"{\\\"a\\\":1,\\\"b\\\":[2,3]}\");
int a = tree.get(\"a\").asInt();          // 1
ArrayNode arr = (ArrayNode) tree.get(\"b\");
int second = arr.get(1).asInt();        // 3
```

### 6. Настройка `ObjectMapper`

```java
ObjectMapper mapper = new ObjectMapper()
        .findAndRegisterModules()          // регистрирует все доступные модули (например, для Java Time)
        .disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS) // ISO‑8601
        .setSerializationInclusion(JsonInclude.Include.NON_NULL); // не выводить null‑значения
```

## Основные возможности

| Что | Как это делается |
|-----|------------------|
| **Аннотации** (`@JsonProperty`, `@JsonIgnore`, `@JsonFormat` и др.) | Помещаются над полями/методами. |
| **Переименование полей** | `@JsonProperty(\"new_name\")` |
| **Игнорировать неизвестные поля** | `mapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);` |
| **Убирать `null` в JSON** | `mapper.setSerializationInclusion(JsonInclude.Include.NON_NULL);` |
| **Формат дат** | `mapper.setDateFormat(new SimpleDateFormat(\"yyyy-MM-dd\"));` |
| **Переименовать все поля в snake_case** | `mapper.setPropertyNamingStrategy(PropertyNamingStrategies.SNAKE_CASE);` |
| **Пользовательские сериализаторы/десериализаторы** | `module.addSerializer(...) / addDeserializer(...)` |
| **Модули** (`JavaTimeModule`, `JaxbAnnotationModule`, `KotlinModule` и др.) | `mapper.registerModule(new JavaTimeModule());` |


---

## Когда использовать кастомные (де)серилизаторы

```java
public class LocalDateSerializer extends JsonSerializer<LocalDate> {
    @Override
    public void serialize(LocalDate value, JsonGenerator gen, SerializerProvider serializers)
            throws IOException {
        gen.writeString(value.format(DateTimeFormatter.ISO_DATE));
    }
}

public class LocalDateDeserializer extends JsonDeserializer<LocalDate> {
    @Override
    public LocalDate deserialize(JsonParser p, DeserializationContext ctxt)
            throws IOException {
        return LocalDate.parse(p.getValueAsString(), DateTimeFormatter.ISO_DATE);
    }
}

@JsonSerialize(using = LocalDateSerializer.class)
@JsonDeserialize(using = LocalDateDeserializer.class)
public LocalDate birthDate;
```

---

## Кратко о потоках (если нужен контроль над памятью)

```java
// Чтение большого JSON‑файла без загрузки всего в память
JsonFactory factory = new JsonFactory();
try (JsonParser parser = factory.createParser(new File(\"big.json\"))) {
    while (parser.nextToken() != JsonToken.END_OBJECT) {
        // обработка токенов
    }
}
```
</p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Retrofit 2</summary>
    <p style='font-size: 14px'>

## Что такое Retrofit 2

Retrofit – это тип‑безопасный HTTP‑клиент для Android и Java.  
Он позволяет:

1. **Определить API** как интерфейс Java.
2. **Автоматически сериализовать/десериализовать** JSON в POJO‑ы.
3. Работать с `Call<T>`, `Observable<T>`, `CompletableFuture<T>` и т.д.

В автотестах Retrofit удобно, потому что:

- Вы пишете **один** интерфейс, а не много `HttpURLConnection`.
- В ответах сразу получаете POJO‑ы, а не строки.
- Можно легко мокать ответы через `MockWebServer`.

---

## Как подключить

```xml
<!-- pom.xml -->
<dependency>
    <groupId>com.squareup.retrofit2</groupId>
    <artifactId>retrofit</artifactId>
    <version>2.9.0</version>
</dependency>
<dependency>
    <groupId>com.squareup.retrofit2</groupId>
    <artifactId>converter-jackson</artifactId>
    <version>2.9.0</version>
</dependency>
```

---

## Пример: Получить информацию о пользователе

### 1. POJO

```java
import com.fasterxml.jackson.annotation.JsonProperty;

public class User {
    public int id;

    @JsonProperty(\"first_name\")
    public String firstName;

    @JsonProperty(\"last_name\")
    public String lastName;

    public String email;
}
```

> **Почему Jackson?**  
> `@JsonProperty` позволяет задать имена полей, которые отличаются от Java‑стиля.

### 2. Интерфейс API

```java
import retrofit2.Call;
import retrofit2.http.GET;
import retrofit2.http.Path;

public interface ApiService {
    @GET(\"users/{id}\")
    Call<User> getUser(@Path(\"id\") int userId);
}
```

### 3. Создание Retrofit‑клиента

```java
import retrofit2.Retrofit;
import retrofit2.converter.jackson.JacksonConverterFactory;

Retrofit retrofit = new Retrofit.Builder()
        .baseUrl(\"https://api.example.com/\")
        .addConverterFactory(JacksonConverterFactory.create(new ObjectMapper()))
        .build();

ApiService api = retrofit.create(ApiService.class);
```

### 4. Вызов в тесте

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class ApiTest {

    @Test
    void testGetUser() throws Exception {
        Call<User> call = api.getUser(1);
        User user = call.execute().body();   // синхронный вызов

        assertNotNull(user);
        assertEquals(1, user.id);
        assertEquals(\"John\", user.firstName);
    }
}
```

> **Обратите внимание**
> - `execute()` – синхронный вызов, удобен для юнит‑тестов.
> - Если нужно асинхронно, используйте `enqueue()` и `Callback<T>`.

---

## Мокирование с MockWebServer

```java
import okhttp3.mockwebserver.MockResponse;
import okhttp3.mockwebserver.MockWebServer;

MockWebServer server = new MockWebServer();
server.enqueue(new MockResponse()
        .setBody(\"{\\\"id\\\":1,\\\"first_name\\\":\\\"John\\\",\\\"last_name\\\":\\\"Doe\\\",\\\"email\\\":\\\"john@example.com\\\"}\")
        .setResponseCode(200));

Retrofit mockRetrofit = new Retrofit.Builder()
        .baseUrl(server.url(\"/\"))
        .addConverterFactory(JacksonConverterFactory.create())
        .build();

ApiService mockApi = mockRetrofit.create(ApiService.class);
User user = mockApi.getUser(1).execute().body();

assertEquals(\"John\", user.firstName);
```

---

## Краткие советы

| Что делать | Как |
|------------|-----|
| **Проверка заголовков** | `call.execute().header(\"Content-Type\")` |
| **Тестировать ошибки** | `call.execute().errorBody()` |
| **Параллельные запросы** | `CompletableFuture<User> future = call.enqueue(...);` |
| **Проверка URL** | `server.takeRequest().getPath()` |
</p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>JWT токены</summary>
    <p style='font-size: 14px'>

## JWT — что это и зачем его использовать в тестах

| Что | Пояснение |
|-----|-----------|
| **JWT (JSON Web Token)** | Строка, состоящая из 3 частей: header, payload и signature. Она передаётся в заголовке `Authorization: Bearer <token>` и позволяет серверу удостовериться, что запрос пришёл от аутентифицированного пользователя. |
| **Когда нужен JWT в тестах** | Чтобы проверить, что эндпоинты работают только для авторизованных пользователей (или работают и без токена). |
| **Как получить токен** | 1️⃣ **Логин** – запрос к `/auth/login` (или любому другому эндпоинту) возвращает токен.  2️⃣ **Тестовый токен** – можно хранить в переменной, если он статичен. |
| **Как использовать** | Подключить `OkHttp`‑интерцептор к `Retrofit`, который автоматически добавит заголовок `Authorization` к каждому запросу. |

---

## Мини‑пример: TestNG + Retrofit 2

### 1. Maven зависимости

```xml
<dependencies>
    <!-- Retrofit -->
    <dependency>
        <groupId>com.squareup.retrofit2</groupId>
        <artifactId>retrofit</artifactId>
        <version>2.9.0</version>
    </dependency>
    <dependency>
        <groupId>com.squareup.retrofit2</groupId>
        <artifactId>converter-gson</artifactId>
        <version>2.9.0</version>
    </dependency>

    <!-- OkHttp -->
    <dependency>
        <groupId>com.squareup.okhttp3</groupId>
        <artifactId>okhttp</artifactId>
        <version>4.12.0</version>
    </dependency>

    <!-- TestNG -->
    <dependency>
        <groupId>org.testng</groupId>
        <artifactId>testng</artifactId>
        <version>7.9.0</version>
        <scope>test</scope>
    </dependency>

    <!-- JWT (для парсинга, если нужен) -->
    <dependency>
        <groupId>com.auth0</groupId>
        <artifactId>java-jwt</artifactId>
        <version>4.4.0</version>
    </dependency>
</dependencies>
```

### 2. API‑интерфейс

```java
interface ApiService {

    // 1️⃣ Получаем токен
    @POST(\"/auth/login\")
    Call<AuthResponse> login(@Body LoginRequest request);

    // 2️⃣ Защищённый эндпоинт
    @GET(\"/protected/resource\")
    Call<Resource> getProtectedResource();
}
```

```java
// DTO‑ы
class LoginRequest { public String username; public String password; }
class AuthResponse { public String token; }
class Resource   { public String data; }
```

### 3. Интерцептор, добавляющий JWT

```java
class JwtInterceptor implements Interceptor {
    private final Supplier<String> tokenSupplier;   // ленивый токен

    JwtInterceptor(Supplier<String> tokenSupplier) {
        this.tokenSupplier = tokenSupplier;
    }

    @Override
    public Response intercept(Chain chain) throws IOException {
        Request original = chain.request();
        String token = tokenSupplier.get();

        Request requestWithAuth = original.newBuilder()
                .header(\"Authorization\", \"Bearer \" + token)
                .build();

        return chain.proceed(requestWithAuth);
    }
}
```

### 4. Тестовый класс TestNG

```java
@Test
public class JwtTests {

    private static final String BASE_URL = \"https://api.example.com\";
    private ApiService api;
    private static String jwtToken;   // хранится после логина

    @BeforeClass
    public void setup() {
        // 1️⃣ Получаем токен один раз
        OkHttpClient client = new OkHttpClient.Builder()
                .build();

        Retrofit retrofit = new Retrofit.Builder()
                .baseUrl(BASE_URL)
                .client(client)
                .addConverterFactory(GsonConverterFactory.create())
                .build();

        ApiService authApi = retrofit.create(ApiService.class);
        LoginRequest req = new LoginRequest();
        req.username = \"user\";
        req.password = \"pass\";

        try {
            Response<AuthResponse> response = authApi.login(req).execute();
            if (!response.isSuccessful() || response.body() == null) {
                throw new RuntimeException(\"Login failed\");
            }
            jwtToken = response.body().token;
        } catch (IOException e) {
            throw new RuntimeException(e);
        }

        // 2️⃣ Создаём Retrofit, который будет всегда ставить токен
        OkHttpClient clientWithAuth = new OkHttpClient.Builder()
                .addInterceptor(new JwtInterceptor(() -> jwtToken))
                .build();

        api = new Retrofit.Builder()
                .baseUrl(BASE_URL)
                .client(clientWithAuth)
                .addConverterFactory(GsonConverterFactory.create())
                .build()
                .create(ApiService.class);
    }

    @Test
    public void testProtectedEndpoint() throws IOException {
        Response<Resource> resp = api.getProtectedResource().execute();
        assertTrue(resp.isSuccessful(), \"HTTP 200 expected\");
        assertNotNull(resp.body(), \"Response body should not be null\");
        System.out.println(\"Got data: \" + resp.body().data);
    }

    @Test
    public void testUnauthorizedAccess() throws IOException {
        // создаём клиент без токена
        OkHttpClient clientWithoutAuth = new OkHttpClient.Builder().build();
        ApiService unauthApi = new Retrofit.Builder()
                .baseUrl(BASE_URL)
                .client(clientWithoutAuth)
                .addConverterFactory(GsonConverterFactory.create())
                .build()
                .create(ApiService.class);

        Response<Resource> resp = unauthApi.getProtectedResource().execute();
        assertEquals(401, resp.code(), \"Should be 401 Unauthorized\");
    }
}
```

### 5. Что проверяем

| Тест | Что проверяется |
|------|-----------------|
| `testProtectedEndpoint` | Токен действительно работает, эндпоинт отдаёт данные. |
| `testUnauthorizedAccess` | Без токена сервер отвечает 401. |
| `testExpiredToken` (не реализован в примере) | Можно добавить второй тест, где токен вручную исчерпываем и проверяем 401. |

### 6. Как отладить JWT

Если нужно увидеть, что внутри токена:

```java
DecodedJWT decoded = JWT.decode(jwtToken);
System.out.println(\"Issuer: \" + decoded.getIssuer());
System.out.println(\"Subject: \" + decoded.getSubject());
System.out.println(\"Expiration: \" + decoded.getExpiresAt());
```

---

## Кратко

1. **Получаем токен** через эндпоинт логина (или берём тестовый).
2. **Добавляем `Authorization: Bearer <token>`** в каждый запрос с помощью OkHttp‑интерцептора.
3. **Пишем TestNG‑тесты**:
    * с токеном – проверяем, что всё работает;
    * без токена – проверяем, что сервер отвечает 401.
4. При необходимости **декодируем JWT** для проверки полей (issuer, exp, sub).
 </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>CompletableFuture.runAsync в автотестах</summary>
    <p style='font-size: 14px'>

## Что такое `CompletableFuture.runAsync`
`CompletableFuture.runAsync` – это статический метод, который запускает **Runnable**‑задачу в отдельном потоке (по умолчанию в `ForkJoinPool.commonPool()`) и сразу возвращает объект `CompletableFuture<Void>`.
Плюсы для автотестов:

| Преимущество | Как это помогает |
|--------------|------------------|
| **Параллельность** | Запускаете несколько тестов одновременно, а не последовательно. |
| **Не блокирует основной поток** | Тестовый фреймворк может продолжать работу, пока задачи выполняются. |
| **Удобный API** | `thenRun`, `thenAccept`, `thenCompose` – легко строить цепочки действий. |

### Как дождаться завершения всех тестов

```java
CompletableFuture<Void> f1 = CompletableFuture.runAsync(() -> testA());
CompletableFuture<Void> f2 = CompletableFuture.runAsync(() -> testB());
CompletableFuture<Void> f3 = CompletableFuture.runAsync(() -> testC());

CompletableFuture<Void> all = CompletableFuture.allOf(f1, f2, f3);
all.join();   // ждём завершения всех
```

## Возвращаемое значение

`runAsync` **не умеет возвращать результат** – он всегда `CompletableFuture<Void>`.  
Если вам нужен результат, используйте **`supplyAsync`**:

```java
CompletableFuture<Integer> sumFuture =
        CompletableFuture.supplyAsync(() -> {
            // вычисляем сумму, например
            return 2 + 3;
        });

int result = sumFuture.join();   // 5
```

### Комбинирование `runAsync` и `supplyAsync`

Можно комбинировать, если сначала выполняете `void`‑задачу, а потом возвращаете результат:

```java
CompletableFuture<String> future =
        CompletableFuture.runAsync(() -> heavyOperation())
                         .thenApplyAsync(v -> \"Done\"); // `v` – `Void`

String status = future.join();   // \"Done\"
```

### Пример автотеста с параллельными запросами и результатом

```java
@Test
void parallelApiTests() {
    CompletableFuture<String> login = CompletableFuture.supplyAsync(() -> {
        // логин, возвращает токен
        return api.login(\"user\", \"pass\");
    });

    CompletableFuture<List<User>> users = login.thenComposeAsync(token ->
        CompletableFuture.supplyAsync(() -> api.getUsers(token))
    );

    // Ожидаем оба результата
    CompletableFuture<Void> all = CompletableFuture.allOf(login, users);

    all.join(); // тесты завершены

    // Проверяем результат
    assertEquals(\"expectedToken\", login.join());
    assertFalse(users.join().isEmpty());
}
```

### Полезные нюансы

| Что нужно знать | Как использовать |
|-----------------|------------------|
| **Пул потоков** | По умолчанию `ForkJoinPool.commonPool()`. Для большего контроля: `runAsync(runnable, customExecutor)` |
| **Обработка ошибок** | `exceptionally`, `handle` – чтобы перехватывать исключения из параллельных задач |
| **Состояние** | Избегайте общих переменных без синхронизации – это может привести к гонкам |
| **Тестовые фреймворки** | JUnit 5 поддерживает асинхронные тесты: `assertTimeoutPreemptively(Duration.ofSeconds(5), () -> future.join());` |

### Когда НЕ стоит использовать `runAsync`

- Если задача **зависит от результата предыдущей** – используйте `thenCompose`/`thenRun`.
- Если тест **чувствителен к порядку выполнения** – параллелизм может вводить nondeterminism.
- Если задача **многоразовна** и **не требует параллелизма** – проще оставить её синхронной.
</p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Artemis</summary>
    <p style='font-size: 14px'>

## Как читать и отправлять сообщения в **Apache ActiveMQ Artemis** (Java)

Artemis – это брокер сообщений.  
С его помощью можно легко разослать сообщение и получить его обратно.

Ниже – минимальный пример, который показывает:

1. Создание соединения с брокером.
2. Отправку (`Producer`) текстового сообщения.
3. Получение (`Consumer`) того же сообщения.

> **Важно** – убедитесь, что у вас запущен Artemis и на нём открыт нужный `queue` (или `topic`).  
> По умолчанию в Artemis создаётся очередь `jms.queue.default`.

---

### 1. Добавляем зависимости

```xml
<!-- pom.xml -->
<dependencies>
    <!-- Artemis JMS клиент -->
    <dependency>
        <groupId>org.apache.activemq</groupId>
        <artifactId>artemis-jms-client</artifactId>
        <version>2.31.0</version>
    </dependency>
</dependencies>
```

---

### 2. Создание подключения и отправка сообщения

```java
import jakarta.jms.*;

public class ArtemisSender {

    public static void main(String[] args) throws JMSException {
        // 1. Создаём ConnectionFactory (URL можно менять под ваш broker)
        ConnectionFactory cf = new ActiveMQConnectionFactory(
                \"tcp://localhost:61616\"); // адрес брокера

        // 2. Создаём соединение
        try (Connection connection = cf.createConnection()) {
            connection.start();                       // включаем поток сообщений

            // 3. Создаём сессию (auto-commit)
            try (Session session = connection.createSession(
                    Session.AUTO_ACKNOWLEDGE)) {
                // 4. Получаем очередь (или создаём, если её нет)
                Destination dest = session.createQueue(\"my.queue\");

                // 5. Создаём Producer
                MessageProducer producer = session.createProducer(dest);

                // 6. Создаём текстовое сообщение
                TextMessage msg = session.createTextMessage(
                        \"Привет, Artemis!\");

                // 7. Отправляем сообщение
                producer.send(msg);
                System.out.println(\"Сообщение отправлено\");
            }
        }
    }
}
```

---

### 3. Чтение сообщения

```java
import jakarta.jms.*;

public class ArtemisReceiver {

    public static void main(String[] args) throws JMSException {
        ConnectionFactory cf = new ActiveMQConnectionFactory(
                \"tcp://localhost:61616\");

        try (Connection connection = cf.createConnection()) {
            connection.start();

            try (Session session = connection.createSession(
                    Session.AUTO_ACKNOWLEDGE)) {
                Destination dest = session.createQueue(\"my.queue\");

                // Создаём Consumer
                MessageConsumer consumer = session.createConsumer(dest);

                // Ожидаем сообщение (timeout 5 сек)
                Message msg = consumer.receive(5000);
                if (msg instanceof TextMessage) {
                    String body = ((TextMessage) msg).getText();
                    System.out.println(\"Получено: \" + body);
                } else {
                    System.out.println(\"Сообщение не найдено или не текстовое\");
                }
            }
        }
    }
}
```

> **Стандартные режимы JMS (настраивает клиент)**
> - AUTO_ACKNOWLEDGE (Автоматический)Брокер удаляет сообщение сразу, как только клиент принял его в метод receive() или обработал в onMessage(). Самый простой режим, но если клиент упадет во время обработки кода, сообщение пропадет.
> - CLIENT_ACKNOWLEDGE (Вручную клиентом)Клиент сам вызывает message.acknowledge(). Брокер удаляет это сообщение и все предыдущие, полученные в этой сессии. Защищает от потери данных при сбоях в коде.
> - DUPS_OK_ACKNOWLEDGE (С риском дубликатов)Автоматическое подтверждение, но группами (пакетами) ради экономии сети. Быстрее, чем AUTO, но при аварии клиента брокер может прислать некоторые сообщения повторно.
> - Транзакции (Session.SESSION_TRANSACTED)Включается через createSession(true, 0). Сообщения подтверждаются пачкой только при вызове session.commit(). При ошибке делается session.rollback(), и вся пачка возвращается в очередь.
---

### Кратко о ключевых моментах

| Шаг | Что делаем | Ключевое слово |
|-----|------------|----------------|
| 1   | Создаём `ConnectionFactory` | `ActiveMQConnectionFactory` |
| 2   | Создаём `Connection` | `createConnection()` |
| 3   | Создаём `Session` | `createSession()` |
| 4   | Получаем/создаём `Destination` (queue/topic) | `createQueue()` / `createTopic()` |
| 5   | Отправка: `MessageProducer` | `createProducer()` |
| 6   | Приём: `MessageConsumer` | `createConsumer()` |
| 7   | Отправляем/получаем сообщение | `send()` / `receive()` |
</p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Kafka</summary>
    <p style='font-size: 14px'>

## Что такое Kafka

Kafka – это распределённый брокер сообщений, где **производители (producers)** публикуют сообщения в *топики*, а **потребители (consumers)** читают их.  
Топик разбит на *партиции* – это упорядоченные потоки, которые позволяют распределять нагрузку и масштабировать чтение.

---

## 1. Отправка (производитель)

```java
import org.apache.kafka.clients.producer.*;

import java.util.Properties;

public class SimpleProducer {
    public static void main(String[] args) {
        // 1. Настройки
        Properties props = new Properties();
        props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, \"localhost:9092\");
        props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG,
                  \"org.apache.kafka.common.serialization.StringSerializer\");
        props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG,
                  \"org.apache.kafka.common.serialization.StringSerializer\");

        // 2. Создаём Producer
        Producer<String, String> producer = new KafkaProducer<>(props);

        // 3. Отправляем сообщение
        ProducerRecord<String, String> record =
                new ProducerRecord<>(\"my-topic\", \"key1\", \"Hello Kafka!\");

        // синхронная отправка (можно использовать send() + callback)
        try {
            RecordMetadata metadata = producer.send(record).get();
            System.out.printf(\"Отправлено в partition=%d, offset=%d%n\",
                              metadata.partition(), metadata.offset());
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            producer.close();
        }
    }
}
```

* `bootstrap.servers` – адреса узлов кластера.
* `key/value serializer` – как сериализовать данные (String, JSON, Avro и т.д.).
* `send()` возвращает `Future<RecordMetadata>`, откуда можно узнать, куда попало сообщение.

---

## 2. Чтение (потребитель)

```java
import org.apache.kafka.clients.consumer.*;

import java.time.Duration;
import java.util.Collections;
import java.util.Properties;

public class SimpleConsumer {
    public static void main(String[] args) {
        // 1. Настройки
        Properties props = new Properties();
        props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, \"localhost:9092\");
        props.put(ConsumerConfig.GROUP_ID_CONFIG, \"my-group\");
        props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG,
                  \"org.apache.kafka.common.serialization.StringDeserializer\");
        props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG,
                  \"org.apache.kafka.common.serialization.StringDeserializer\");
        // Автоматически подтверждать чтение
        props.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, \"earliest\");

        // 2. Создаём Consumer
        KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);

        // 3. Подписываемся на топик
        consumer.subscribe(Collections.singletonList(\"my-topic\"));

        // 4. Бесконечный цикл чтения
        try {
            while (true) {
                ConsumerRecords<String, String> records =
                        consumer.poll(Duration.ofMillis(500));
                for (ConsumerRecord<String, String> record : records) {
                    System.out.printf(\"Получено: key=%s, value=%s, partition=%d, offset=%d%n\",
                                      record.key(), record.value(),
                                      record.partition(), record.offset());
                }
            }
        } finally {
            consumer.close();
        }
    }
}
```
>AUTO_OFFSET_RESET_CONFIG:
> - earliest: Начать читать топик с самого первого, самого старого доступного сообщения (с нуля).
> - latest (по умолчанию): Игнорировать старые сообщения и читать только те, которые начнут поступать в топик после включения вашего клиента.
> - none: Если старого офсета нет, не запускаться вовсе и выбросить ошибку.

* `group.id` – идентификатор группы потребителей; все участники группы делят чтение партиций.
* `auto.offset.reset` – куда начинать читать, если у группы нет сохранённого offset (`earliest`/`latest`).
* `poll()` возвращает пакет записей; `Duration` задаёт таймаут ожидания.

---

## 3. Краткие советы

| Что | Как сделать |
|-----|--------------|
| **Сериализация** | Используйте готовые сериализаторы (`StringSerializer`, `ByteArraySerializer`) или пишите свои (например, Jackson для JSON). |
| **Параллелизм** | Паратиции позволяют одновременно читать/писать из разных потоков. |
| **Транзакции** | Для атомарного набора записей используйте `producer.initTransactions()` / `send()` / `commitTransaction()`. |
| **Мониторинг** | Kafka предоставляет метрики через JMX. |
| **Безопасность** | Включите SSL/TLS и SASL по необходимости. |

---

## Итог

1. **Producer** → `ProducerRecord` → `producer.send()`
2. **Consumer** → `consumer.subscribe()` → `consumer.poll()`

С этими примерами вы уже можете отправлять и читать простые текстовые сообщения в Kafka. Далее можно подключать более сложные типы сериализации, управлять offset‑ами вручную, использовать транзакции и т.д. 🚀
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Java SQL</summary>
    <p style='font-size: 14px'>

## Как работать с БД в Java (JDBC)

| Что | Что это | Почему важно |
|-----|---------|--------------|
| **JDBC** | API, встроенный в JDK, для работы с любой реляционной БД | Позволяет писать чистый Java‑код без сторонних библиотек |
| **Driver** | Специфичный для каждой БД (MySQL, PostgreSQL, Oracle и т.д.) | Переводит JDBC‑запросы в протокол БД |
| **Connection** | Открывает соединение с БД | Необходимо для всех операций |
| **Statement / PreparedStatement** | Выполняет SQL‑запросы | `PreparedStatement` защищает от SQL‑инъекций и кэширует план |
| **ResultSet** | Итератор по строкам результата | Позволяет читать данные |
| **Transactions** | `commit` / `rollback` | Гарантирует атомарность операций |
| **Connection Pool** | Хранилище открытых соединений | Уменьшает накладные расходы на открытие/закрытие |

---

### 1. Подключение к БД

```java
import java.sql.*;

public class JdbcDemo {
    // URL формата: jdbc:driver://host:port/dbname
    private static final String URL = \"jdbc:postgresql://localhost:5432/testdb\";
    private static final String USER = \"postgres\";
    private static final String PASS = \"secret\";

    public static void main(String[] args) {
        try (Connection conn = DriverManager.getConnection(URL, USER, PASS)) {
            System.out.println(\"Connected!\");
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

> **Tip** – В `pom.xml` добавьте зависимость драйвера:
> ```xml
> <dependency>
>     <groupId>org.postgresql</groupId>
>     <artifactId>postgresql</artifactId>
>     <version>42.7.4</version>
> </dependency>
> ```

---

### 2. Выполнение запроса

```java
String sql = \"SELECT id, name, email FROM users WHERE status = ?\";
try (Connection conn = DriverManager.getConnection(URL, USER, PASS);
     PreparedStatement ps = conn.prepareStatement(sql)) {

    ps.setString(1, \"ACTIVE\");           // параметр
    try (ResultSet rs = ps.executeQuery()) {
        while (rs.next()) {
            int id = rs.getInt(\"id\");
            String name = rs.getString(\"name\");
            System.out.printf(\"%d: %s%n\", id, name);
        }
    }
}
```

---

### 3. Вставка/обновление

```java
String insert = \"INSERT INTO users (name, email, status) VALUES (?, ?, ?)\";
try (Connection conn = DriverManager.getConnection(URL, USER, PASS);
     PreparedStatement ps = conn.prepareStatement(insert, Statement.RETURN_GENERATED_KEYS)) {

    ps.setString(1, \"Alice\");
    ps.setString(2, \"alice@example.com\");
    ps.setString(3, \"ACTIVE\");
    int affected = ps.executeUpdate();

    if (affected == 1) {
        try (ResultSet keys = ps.getGeneratedKeys()) {
            if (keys.next()) {
                long newId = keys.getLong(1);
                System.out.println(\"Inserted id: \" + newId);
            }
        }
    }
}
```

---

### 4. Транзакции

```java
try (Connection conn = DriverManager.getConnection(URL, USER, PASS)) {
    conn.setAutoCommit(false);          // включаем ручную транзакцию

    // 1. Перенос баланса
    try (PreparedStatement ps1 = conn.prepareStatement(
            \"UPDATE accounts SET balance = balance - ? WHERE id = ?\")) {
        ps1.setBigDecimal(1, new BigDecimal(\"100.00\"));
        ps1.setInt(2, 1);
        ps1.executeUpdate();
    }

    // 2. Получение денег
    try (PreparedStatement ps2 = conn.prepareStatement(
            \"UPDATE accounts SET balance = balance + ? WHERE id = ?\")) {
        ps2.setBigDecimal(1, new BigDecimal(\"100.00\"));
        ps2.setInt(2, 2);
        ps2.executeUpdate();
    }

    conn.commit();                      // всё прошло – фиксируем
} catch (SQLException e) {
    // откат при ошибке
    try { conn.rollback(); } catch (SQLException ex) { ex.printStackTrace(); }
}
```

---

### 5. Пул соединений (HikariCP)

```java
import com.zaxxer.hikari.*;

HikariDataSource ds = new HikariDataSource();
ds.setJdbcUrl(URL);
ds.setUsername(USER);
ds.setPassword(PASS);
ds.setMaximumPoolSize(10);

try (Connection conn = ds.getConnection()) {
    // ваш код
}
```
</p>
    </details>
</details>
