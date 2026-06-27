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
<summary style='font-size: 20px'><b>Spring</b></summary>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Spring MVC</summary>
    <p style='font-size: 14px'>

## Spring MVC – кратко о том, что делает и как это работает

| Что | Зачем | Как выглядит в коде |
|-----|-------|---------------------|
| **DispatcherServlet** | «Фронт‑контроллер». Принимает все HTTP‑запросы, ищет подходящий обработчик и отдаёт ответ. | В `web.xml` (или `@Configuration`) `<servlet>…</servlet>` |
| **HandlerMapping** | Определяет, какой `Controller` и метод будут обработаны конкретным запросом. | `RequestMappingHandlerMapping` (настроено автоматически) |
| **HandlerAdapter** | Позволяет DispatcherServlet вызывать найденный обработчик. | `RequestMappingHandlerAdapter` |
| **Controller** | Класс, где пишутся методы‑обработчики. | `@Controller` / `@RestController` |
| **ViewResolver** | Выбирает, какой шаблон (JSP, Thymeleaf, FreeMarker и т.д.) отобразить. | `InternalResourceViewResolver`, `ThymeleafViewResolver` |
| **Model / ModelAndView** | Хранит данные, которые передаются в шаблон. | `model.addAttribute(...)`, `return new ModelAndView(\"view\")` |
| **Binding / Validation** | Преобразует параметры запроса в объекты, валидирует их. | `@ModelAttribute`, `@Valid`, `BindingResult` |
| **Exception Handling** | Универсальная обработка ошибок в контроллерах. | `@ControllerAdvice`, `@ExceptionHandler` |
| **Interceptors** | Пред- и пост‑обработка запросов (логирование, auth, metrics). | `HandlerInterceptor` |
| **Filters** | Уровень Servlet API, работает до Spring MVC. | `javax.servlet.Filter` |
| **Multipart (File upload)** | Загрузка файлов через `MultipartFile`. | `@RequestParam(\"file\") MultipartFile file` |
| **Content Negotiation** | Выбор формата ответа (JSON, XML, HTML) по `Accept`‑header. | `@RequestMapping(produces = MediaType.APPLICATION_JSON_VALUE)` |
| **Message Converters** | Конвертируют Java‑объекты в JSON/XML и обратно. | `MappingJackson2HttpMessageConverter`, `Jaxb2RootElementHttpMessageConverter` |
| **Async Support** | Асинхронные контроллеры, `Callable`, `DeferredResult`. | `@Async`, `Callable<?>` |
| **Locale / i18n** | Поддержка многоязычности. | `LocaleResolver`, `MessageSource` |
| **Session / Flash Attributes** | Хранение данных в сессии, перенаправление с данными. | `@SessionAttributes`, `RedirectAttributes` |

---

## Как выглядит типичный контроллер

```java
@RestController          // для JSON/XML API
@RequestMapping(\"/api\")  // общий префикс
public class UserController {

    @GetMapping(\"/users/{id}\")          // GET /api/users/5
    public ResponseEntity<UserDto> getUser(@PathVariable Long id) {
        UserDto user = userService.find(id);
        return ResponseEntity.ok(user);
    }

    @PostMapping(\"/users\")              // POST /api/users
    public ResponseEntity<UserDto> create(
            @Valid @RequestBody CreateUserRequest req,
            BindingResult errors) {
        if (errors.hasErrors()) {
            throw new BadRequestException(errors);
        }
        UserDto created = userService.create(req);
        return ResponseEntity.status(HttpStatus.CREATED).body(created);
    }

    @ExceptionHandler(BadRequestException.class)  // локальная обработка ошибок
    public ResponseEntity<ApiError> handleBadRequest(BadRequestException ex) {
        return ResponseEntity.badRequest().body(new ApiError(ex.getMessage()));
    }
}
```

### Что происходит за кулисами

1. **DispatcherServlet** получает запрос `GET /api/users/5`.
2. **RequestMappingHandlerMapping** ищет метод с аннотацией `@GetMapping(\"/users/{id}\")`.  
   Находит `UserController.getUser`.
3. **RequestMappingHandlerAdapter** вызывает метод, передавая `id=5`.
4. Метод возвращает объект `UserDto`;  
   `MappingJackson2HttpMessageConverter` преобразует его в JSON.
5. Ответ отправляется клиенту.

---

## Ключевые особенности Spring MVC

| Особенность | Что даёт | Как использовать |
|-------------|----------|------------------|
| **Аннотационные контроллеры** | Чистый, декларативный код | `@Controller`, `@RestController`, `@RequestMapping` |
| **Встроенная валидация** | Удобно проверять входные данные | `@Valid`, `@NotNull`, `@Size`, `BindingResult` |
| **Общая конфигурация** | Минимум XML, максимум Java‑конфиг | `@Configuration`, `@EnableWebMvc` |
| **Поддержка нескольких view‑движков** | Можно менять шаблоны без больших изменений | `ViewResolver` |
| **Модульная обработка** | Расширяемый через `HandlerInterceptor`, `Filter` | Реализовать `HandlerInterceptor` |
| **Async** | Улучшает пропускную способность | `Callable`, `DeferredResult` |
| **Content‑Negotiation** | Одно и то же API выдаёт JSON, XML, HTML | `produces`, `consumes` |
| **Exception Handling** | Централизованное управление ошибками | `@ControllerAdvice` |
| **Spring Boot интеграция** | Автоконфиг, embedded Tomcat | `spring-boot-starter-web` |
| **Многоязычность** | Локализация сообщений | `MessageSource`, `LocaleResolver` |
| **Multipart** | Загрузка файлов | `MultipartResolver`, `MultipartFile` |
| **Security** | Защита эндпоинтов (Spring Security) | `@PreAuthorize`, `HttpSecurity` |
| **Testing** | Интеграционные тесты MVC | `MockMvc`, `@WebMvcTest` |
 </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>DispatcherServlet</summary>
    <p style='font-size: 14px'>

## DispatcherServlet – «пульт» вашего Spring‑MVC

| Что делает | Где находится | Коротко |
|------------|--------------|---------|
| **Front controller** – единственный входной пункт для всех HTTP‑запросов, которые попадают в ваш веб‑приложение | В контейнере (web.xml, `@WebServlet`, Spring Boot) | Перехватывает запрос, определяет, как его обрабатывать и что вернуть клиенту |

---

### Как это работает

1. **Приём запроса**  
   `DispatcherServlet` получает `HttpServletRequest` и `HttpServletResponse`.

2. **Определение обработчика (Handler)**
    * **HandlerMapping** (обычно `RequestMappingHandlerMapping`) ищет метод‑обработчик (`@Controller`, `@RestController`, `@RequestMapping` и т.д.) по URL, HTTP‑методу, заголовкам и др.
    * Если ничего не найдено → 404.

3. **Вызов обработчика**
    * **HandlerAdapter** (обычно `RequestMappingHandlerAdapter`) превращает найденный метод в **invocation** и вызывает его, передавая параметры (query, path, тело запроса, `@RequestBody`, `@PathVariable` и т.д.).
    * Метод возвращает объект‑модель (`Model`, `Map`, POJO, `ResponseEntity` и т.п.).

4. **Выбор представления (View)**
    * **ViewResolver** (`InternalResourceViewResolver`, `ThymeleafViewResolver`, `FreeMarkerViewResolver` и т.д.) преобразует имя вида, указанное в результате, в реальный `View` (JSP, Thymeleaf, JSON, XML и др.).
    * Если возвращён `ResponseEntity` или `@ResponseBody`, `HttpMessageConverter` сериализует объект прямо в ответ.

5. **Выполнение View**
    * `View.render()` заполняет модель и генерирует окончательный HTML/JSON/… и пишет в `HttpServletResponse`.

6. **Обработка исключений**
    * **HandlerExceptionResolver** (`ExceptionHandlerExceptionResolver`, `ResponseStatusExceptionResolver` и т.д.) ловит исключения, выброшенные в контроллерах, и преобразует их в ответы (статусы, страницы ошибок, JSON‑объекты).

7. **Фильтры и интерцепторы**
    * `HandlerInterceptor` (pre‑handle, post‑handle, after‑completion) позволяет выполнять код до и после контроллера (аутентификация, логирование, измерение времени и т.д.).
    * `WebMvcConfigurer` можно использовать, чтобы добавить свои интерцепторы, форматтеры, валидаторы и т.п.

---

### Ключевые особенности

| Особенность | Краткое описание |
|-------------|------------------|
| **Аннотационный стиль** | `@Controller`, `@RequestMapping`, `@GetMapping`, `@PostMapping` и др. |
| **Международность (i18n)** | `LocaleResolver`, `LocaleChangeInterceptor` – меняет язык в зависимости от параметра, cookie или заголовка. |
| **Тематизация (theme)** | `ThemeResolver`, `ThemeSource` – позволяет менять стили/шаблоны. |
| **Пагинация и сортировка** | `PageableHandlerMethodArgumentResolver`, `SortHandlerMethodArgumentResolver` – автоматически конвертируют параметры `page`, `size`, `sort`. |
| **Multipart‑запросы** | `MultipartResolver` (CommonsMultipartResolver, StandardServletMultipartResolver) – обрабатывает файлы. |
| **Поддержка статических ресурсов** | `DefaultServletHandlerConfigurer` (или `ResourceHandlerRegistry`) – отдаёт CSS/JS/изображения без участия MVC. |
| **Content Negotiation** | `ContentNegotiationManager` – определяет, в каком формате вернуть данные (JSON, XML, HTML). |
| **Spring Boot** | При использовании Spring Boot `DispatcherServlet` регистрируется автоматически, а `@EnableWebMvc` можно опустить – Spring Boot сам сконфигурирует всё нужное. |
| **WebSocket** | Через `WebSocketHandler` и `SockJS` можно добавить поддержку реального времени. |
| **Тестируемость** | `MockMvc` позволяет писать юнит‑тесты контроллеров, не запуская контейнер. |

---

### Минимальная конфигурация (XML)

```xml
<bean id=\"dispatcher\"
      class=\"org.springframework.web.servlet.DispatcherServlet\">
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/spring/dispatcher-config.xml</param-value>
    </init-param>
</bean>

<servlet>
    <servlet-name>dispatcher</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
</servlet>

<servlet-mapping>
    <servlet-name>dispatcher</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

### Минимальная конфигурация (Java Config)

```java
@Configuration
@EnableWebMvc
@ComponentScan(\"com.example.app\")
public class WebConfig implements WebMvcConfigurer {
    // можно переопределить ViewResolver, MessageConverters и т.д.
}
```

---

### Итог

`DispatcherServlet` – это центральный «пульт» Spring MVC.  
Он принимает запрос, ищет подходящий контроллер, вызывает его, обрабатывает результат, выбирает представление и возвращает клиенту.  
При этом он умеет обрабатывать ошибки, менять язык, темы, поддерживать multipart‑запросы, статические ресурсы и многое другое, делая разработку веб‑приложений гибкой и мощной.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>HandlerMapping и HandlerAdapter</summary>
    <p style='font-size: 14px'>

## Что такое `HandlerMapping` и `HandlerAdapter` в Spring MVC

| Компонент | Задача | Как работает |
|-----------|--------|--------------|
| **HandlerMapping** | Находит *обработчик* (обычно `@Controller`‑метод) для входящего HTTP‑запроса | 1. При получении запроса Spring вызывает все зарегистрированные `HandlerMapping`‑ы в порядке их приоритета. <br>2. Каждый `HandlerMapping` проверяет свой набор правил (URL‑шаблоны, аннотации, параметры и т.д.) и возвращает объект `HandlerExecutionChain`, в котором содержится сам *handler* и, при желании, цепочка `HandlerInterceptors`. <br>3. Если ни один `HandlerMapping` не нашёл подходящего обработчика, генерируется `404`. |
| **HandlerAdapter** | Переходит от *обработчика* к *выполнению* кода | 1. После того как `HandlerMapping` возвращает `HandlerExecutionChain`, DispatcherServlet ищет подходящий `HandlerAdapter`, который умеет работать с типом найденного обработчика (например, `RequestMappingHandlerAdapter` для `@RequestMapping`). <br>2. `HandlerAdapter` вызывает метод контроллера, передавая ему параметры из запроса (через `@RequestParam`, `@PathVariable`, `@RequestBody` и т.д.). <br>3. Полученный результат (`ModelAndView`, `ResponseEntity`, `String` и т.п.) обрабатывается дальше (рендеринг, сериализация в JSON и т.д.). |

---

## Как это выглядит в коде

```java
// 1. DispatcherServlet (корневой компонент)
DispatcherServlet servlet = new DispatcherServlet(context);

// 2. Внутри servlet.doDispatch() происходит
//    a) вызов HandlerMapping
//    b) поиск подходящего HandlerAdapter
//    c) вызов handler метода через Adapter
//    d) обработка результата и отправка ответа
```

---

## Ключевые особенности

### HandlerMapping

| Вариант | Что делает |
|---------|------------|
| `BeanNameUrlHandlerMapping` | сопоставляет URL с именем бина (`/foo` → бин `fooController`) |
| `UrlPathHelper` / `PathMatcher` | поддерживает шаблоны вида `/users/{id}` |
| `RequestMappingHandlerMapping` | основной `HandlerMapping` для аннотаций `@RequestMapping`, `@GetMapping` и т.п. |
| `SimpleUrlHandlerMapping` | простое сопоставление URL → бина с фиксированными путями |
| `AnnotationMethodHandlerMapping` (старый) | поддержка аннотаций до Spring 3.1 |

**Приоритеты** – можно задать `order` для каждого `HandlerMapping`. DispatcherServlet пробует их по возрастанию.

### HandlerAdapter

| Вариант | Что делает |
|---------|------------|
| `RequestMappingHandlerAdapter` | для методов с аннотациями `@RequestMapping` (Spring 3.1+) |
| `HttpRequestHandlerAdapter` | для простых `HttpRequestHandler`‑ов |
| `SimpleControllerHandlerAdapter` | для `Controller`‑ов, которые реализуют интерфейс `Controller` (старый стиль) |
| `ServletInvocableHandlerMethod` | внутренний класс, который вызывает конкретный метод контроллера |

**Гибкость** – если вы добавляете собственный тип обработчика (например, контроллер, реализующий ваш интерфейс), достаточно написать свой `HandlerAdapter`, поддерживающий этот тип.

---

## Как они взаимодействуют

1. **DispatcherServlet** получает `HttpServletRequest`.
2. **HandlerMapping** ищет подходящий обработчик → возвращает `HandlerExecutionChain`.
3. **DispatcherServlet** ищет `HandlerAdapter`, который умеет работать с типом `handler`.
4. `HandlerAdapter` вызывает метод контроллера, обрабатывает параметры и возвращает результат (`ModelAndView`, `ResponseEntity` и т.д.).
5. **DispatcherServlet** обрабатывает результат (рендеринг вида, сериализация JSON, редирект и т.д.) и отправляет ответ клиенту.

---

## Быстрый чек‑лист для собеседования

- **Назначение**: `HandlerMapping` → поиск обработчика, `HandlerAdapter` → вызов обработчика.
- **Типы**: `RequestMappingHandlerMapping` + `RequestMappingHandlerAdapter` (основные); `BeanNameUrlHandlerMapping`, `HttpRequestHandlerAdapter` и т.д. (дополнительно).
- **Порядок**: `order` у `HandlerMapping`, `HandlerAdapter` выбирается по типу обработчика.
- **Параметры**: `HandlerExecutionChain` может включать `HandlerInterceptor`s.
- **Наследование**: Можно написать собственные `HandlerMapping` и `HandlerAdapter` для нестандартных контроллеров.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>@Controller и @RestController</summary>
    <p style='font-size: 14px'>

## @Controller vs @RestController – разница и особенности

|  | **@Controller** | **@RestController** |
|---|---|---|
| **Что это?** | Класс‑обработчик запросов в Spring MVC. | Тот же, но уже автоматически добавляет `@ResponseBody` ко всем методам. |
| **Методы возвращают** | *View name* (JSP, Thymeleaf, FreeMarker и т.д.) или `ModelAndView`. | Любой объект, который будет сериализован в тело ответа (JSON, XML, plain text). |
| **Нужен для** | Традиционного MVC‑приложения, где клиент получает HTML‑страницы. | REST‑API, микросервисы, где клиент (браузер, мобильное приложение, другой сервис) ожидает данные в формате JSON/XML. |
| **Аннотация** | `@Controller` + `@RequestMapping` / `@GetMapping` / `@PostMapping` и т.п. | `@RestController` (это `@Controller + @ResponseBody`). |
| **Сериализация** | Не выполняется автоматически. Чтобы вернуть JSON, надо добавить `@ResponseBody` к конкретному методу или использовать `@ResponseEntity`. | Автоматически сериализует объект в JSON (или XML, если включён `HttpMessageConverter`). |
| **View Resolver** | Пытается найти `View` по имени, которое вернул метод. | **Не ищет view** – возвращает содержимое напрямую в тело ответа. |
| **Пример** | ```java<br>@Controller<br>public class PageController {<br>  @GetMapping(\"/home\")<br>  public String home(Model model) {<br>    model.addAttribute(\"msg\", \"Hello\");<br>    return \"home\"; // ищет home.jsp/thymeleaf и т.д.<br>  }<br>}<br>``` | ```java<br>@RestController<br>public class ApiController {<br>  @GetMapping(\"/api/users\")<br>  public List<User> users() {<br>    return userService.findAll(); // будет сериализовано в JSON<br>  }<br>}<br>``` |
| **@ResponseBody** | Можно добавить к отдельному методу: `@GetMapping(\"/json\") @ResponseBody public User user()`. | Не нужен – `@RestController` делает это автоматически для всех методов. |
| **Другие аннотации** | `@SessionAttributes`, `@ModelAttribute`, `@InitBinder` и т.п. | Тоже применимы (если нужны). |
| **Иерархия** | `@RestController` **extends** `@Controller`. | `@RestController` — это просто `@Controller` + `@ResponseBody`. |
| **Когда использовать?** | Если нужно отдавать HTML‑страницы, шаблоны, редиректы. | Если нужно отдавать данные в формате JSON/XML, без шаблонов. |
| **Плюсы** | Поддержка сложных представлений, редиректы, использование `ModelAndView`. | Упрощает код, не требует `@ResponseBody`/`ResponseEntity`. |
| **Минусы** | Нужно вручную сериализовать JSON, если понадобится. | Не подходит для возврата view‑ов. |

### Краткое резюме

- **`@Controller`** – класс‑обработчик, который обычно возвращает *view name* (HTML‑страницу).
- **`@RestController`** – то же самое, но все методы автоматически возвращают тело ответа (JSON/XML).
- `@RestController` = `@Controller` + `@ResponseBody`.
- Используйте `@Controller` для традиционного MVC, `@RestController` – для REST‑API.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Как происходит привязка запросов к методам (@GetMapping и т.д.) </summary>
    <p style='font-size: 14px'>

## Как Spring MVC сопоставляет запросы с методами контроллера

| Что | Как это делается | Кратко про особенности |
|-----|------------------|------------------------|
| **Определение пути и HTTP‑метода** | `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`, `@PatchMapping` – это *шорткаты* для `@RequestMapping(method = RequestMethod.XXX)`. | Можно указать путь через `value`/`path`, а также использовать шаблоны (`*`, `**`, `?`). |
| **Путь на уровне класса** | `@RequestMapping(\"/api\")` над классом + `@GetMapping(\"/users\")` над методом → `/api/users`. | Класс‑уровневая аннотация работает как префикс для всех методов. |
| **Параметры запроса** | `@RequestMapping(params = \"id\")` – метод будет вызван только если в URL есть `?id=…`. <br> `@RequestMapping(params = {\"id\", \"!name\"})` – `id` обязателен, `name` не должен присутствовать. | Позволяет различать запросы с одинаковым путем но разными параметрами. |
| **HTTP‑заголовки** | `@RequestMapping(headers = \"X-Requested-With=XMLHttpRequest\")`. | Ограничивает вызов методa только при наличии/значении заголовка. |
| **Типы контента** | `consumes = MediaType.APPLICATION_JSON_VALUE` – метод принимает только JSON.<br> `produces = MediaType.APPLICATION_XML_VALUE` – метод выдаёт только XML. | Работает с `Content-Type` и `Accept` заголовками. |
| **Имя маршрута** | `@RequestMapping(name = \"getUser\")`. | Полезно для ссылок (`MvcUriComponentsBuilder.fromMappingName(\"uc#getUser\")`). |
| **Регулярные выражения в пути** | `@GetMapping(\"/users/{id:\\\\d+}\")`. | Позволяет задать ограничения на переменные пути. |
| **Паттерны `AntPathMatcher`** | По умолчанию Spring использует `AntPathMatcher`: `*`, `**`, `?`. | `**` может покрыть несколько сегментов пути. |
| **PathPatternParser (Spring 6+)** | Можно включить через `setPatternParser(new PathPatternParser())`. | Быстрее и более точное сопоставление, но требует обновления кода. |
| **Приоритеты и неоднозначность** | Spring выбирает *самый конкретный* путь: `/users/1` > `/users/*`. Если два метода одинаково подходят, выбрасывается `IllegalStateException`. | Хорошая практика – избегать дублирующих паттернов. |
| **Кеширование** | После старта `RequestMappingHandlerMapping` строит карту `path → HandlerMethod` и кэширует её. | Быстрое сопоставление при каждом запросе. |
| **Контроллеры** | `@Controller` + `@ResponseBody` (или `@RestController`) – метод возвращает тело ответа. | `@RestController` автоматически добавляет `@ResponseBody`. |
| **Обработка ошибок** | `@ControllerAdvice` + `@ExceptionHandler` – глобальная обработка исключений, не влияющая на сопоставление. | Можно вернуть специфический HTTP‑код и тело. |

### Коротко, как это работает

1. **Регистрация**: при старте Spring сканирует классы, ищет аннотации `@RequestMapping`, `@GetMapping` и т.д.
2. **Построение карты**: для каждого метода создаётся объект `HandlerMethod`, а путь + HTTP‑метод добавляется в таблицу `RequestMappingHandlerMapping`.
3. **Приём запроса**: `DispatcherServlet` передаёт запрос `RequestMappingHandlerMapping`.
4. **Поиск**: сопоставляется путь, HTTP‑метод, параметры, заголовки, `Accept/Content‑Type`.
5. **Вызов**: найденный `HandlerMethod` исполняется, а возвращаемое значение сериализуется в ответ (если `@ResponseBody`).

> **Tip**: если вы используете `@GetMapping(\"/users/{id}\")`, добавьте `@PathVariable Long id` в параметры метода, чтобы получить значение переменной пути.
</p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Как обрабатываются параметры: @PathVariable, @RequestParam, @RequestBody, @ModelAttribute</summary>
    <p style='font-size: 14px'>

## Как Spring MVC обрабатывает параметры

| Аннотация | Что делает | Ключевые особенности |
|-----------|------------|----------------------|
| **`@PathVariable`** | Берёт часть URL‑пути и привязывает её к параметру метода. | • `@PathVariable(\"id\") Long id` – извлекает `<id>` из `/users/{id}`.<br>• Если имя параметра совпадает с переменной в пути, можно опустить аргумент: `@PathVariable Long id`. <br>• **Типы**: `String`, `Integer`, `Long`, `UUID`, `Enum`, `Collection` (для повторяющихся сегментов).<br>• **Регулярные выражения** в пути (`/users/{id:\\\\d+}`).<br>• **Опциональность**: `required = false` (Spring 4.3+) + `@PathVariable Optional<Long> id`.<br>• **Map**: `@PathVariable Map<String, String> vars` – все переменные пути. |
| **`@RequestParam`** | Вытаскивает параметр из строки запроса (или из формы). | • `@RequestParam(\"page\") int page` → `?page=2`. <br>• `required = true/false` (по умолчанию `true`). <br>• `defaultValue = \"10\"` – если параметр не передан. <br>• **Коллекции**: `List<String> ids`, `String[] ids` → `?ids=1&ids=2`. <br>• **Map**: `@RequestParam Map<String, String> allParams`. <br>• **Биндинг**: привязывает к Java‑объекту (POJO) – `@RequestParam MyForm form`. <br>• **Валидация**: `@Valid @RequestParam MyForm form`. |
| **`@RequestBody`** | Преобразует **полный** тело запроса в объект Java. | • Использует `HttpMessageConverter` (JSON → Jackson, XML → JAXB, etc.). <br>• Поддерживает `application/json`, `application/xml`, `text/plain`, `multipart/form-data` и др. <br>• **Валидация**: `@Valid @RequestBody MyDto dto`. <br>• **Потоковое чтение**: `InputStream`/`Reader` можно получить напрямую. <br>• **Обработчики ошибок**: `HttpMessageNotReadableException`, `MethodArgumentNotValidException`. |
| **`@ModelAttribute`** | Инициализирует объект из модели и/или заполняет его данными из запроса. | • На уровне **метода**: `@ModelAttribute(\"user\") User initUser()` – объект добавляется в модель до вызова контроллера.<br>• На уровне **параметра**: `@ModelAttribute User user` – Spring сначала ищет объект с таким именем в модели, а если не находит, создаёт новый и заполняет из параметров запроса.<br>• **Валидация**: `@Valid @ModelAttribute User user`.<br>• **Поддержка вложенных объектов** и **списков** (`List<Item> items`). <br>• **Имена полей**: совпадают с именами параметров, но можно переименовать через `@ModelAttribute(\"form\") Form form`. <br>• **Отличие от `@RequestParam`** – `@ModelAttribute` объединяет все параметры в один объект и работает с `BindingResult`. |

### Как это работает под капотом

1. **Resolver‑Chain** – для каждого параметра вызывается подходящий `HandlerMethodArgumentResolver`.
    * `PathVariableMethodArgumentResolver` → `@PathVariable`.
    * `RequestParamMethodArgumentResolver` → `@RequestParam`.
    * `RequestResponseBodyMethodProcessor` → `@RequestBody`.
    * `ModelAttributeMethodProcessor` → `@ModelAttribute`.

2. **Data Binding** – для `@RequestParam` и `@ModelAttribute` используется `WebDataBinder`. Он:
    * Конвертирует строки в нужные типы через `ConversionService`.
    * Выполняет валидацию (если есть `@Valid`).
    * Заполняет объект, создавая вложенные объекты при необходимости.

3. **Message Converters** – `@RequestBody` и `@ResponseBody` используют `HttpMessageConverter`.
    * Выбор конвертера определяется контент‑тайпом (`Content-Type`/`Accept`).
    * Самые распространённые: `MappingJackson2HttpMessageConverter`, `Jaxb2RootElementHttpMessageConverter`.

### Быстрый чек‑лист для интервью

- **`@PathVariable`** – нужен, если часть URL динамическая.
- **`@RequestParam`** – для простых параметров в строке запроса.
- **`@RequestBody`** – когда тело запроса – JSON/XML (обычно POST/PUT).
- **`@ModelAttribute`** – удобен для форм: объединяет все параметры в один объект и автоматически добавляет его в модель.
- Не забывай про **`required`, `defaultValue`, `@Valid`** – они делают код надёжнее.
</p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>ViewResolver</summary>
    <p style='font-size: 14px'>

## ViewResolver в Spring MVC

| Что это | Зачем |
|---------|-------|
| **ViewResolver** – это компонент, который **превращает логическое имя представления** (строку, которую возвращает контроллер) в **конкретный объект `View`** (JSP, Thymeleaf‑шаблон, XSL‑стили и т.д.). | После того как контроллер выбрал представление, Spring должен понять, какой файл и как именно отрисовать. ViewResolver отвечает за поиск, кэширование и настройку этого процесса. |

### Как работает

1. **Контроллер** возвращает строку‑имя, например `\"home\"`.
2. **DispatcherServlet** ищет в цепочке `ViewResolver`‑ов подходящий, который может преобразовать `\"home\"` в реальный объект `View`.
3. Полученный `View` отрисовывает данные из модели и возвращает клиенту.

> **Важно:** если контроллер возвращает префикс `redirect:` или `forward:`, Spring использует `RedirectView`/`InternalResourceView` без участия `ViewResolver`.

---

## Типы ViewResolver

| Тип | Что делает | Особенности | Пример конфигурации |
|-----|------------|-------------|---------------------|
| **InternalResourceViewResolver** | Разрешает имена в JSP‑файлы (или другие ресурсы, которые обрабатываются сервером). | *prefix* + *suffix* (например, `/WEB-INF/views/` + `.jsp`), кэширование, указывается `viewClass` (обычно `JstlView`). | ```java @Bean public InternalResourceViewResolver resolver() { InternalResourceViewResolver v = new InternalResourceViewResolver(); v.setPrefix(\"/WEB-INF/views/\"); v.setSuffix(\".jsp\"); return v; } ``` |
| **BeanNameViewResolver** | Ищет `View`‑бины в контексте по имени, совпадающему с логическим именем. | Позволяет использовать полностью сконфигурированные `View`‑объекты (например, `ThymeleafView`). | ```java @Bean public BeanNameViewResolver resolver() { return new BeanNameViewResolver(); } ``` |
| **UrlBasedViewResolver** | Базовый абстрактный класс, от которого наследуются другие резолверы (JSP, FreeMarker, Thymeleaf). | Позволяет задать `viewClass` и общие параметры. | Вспомогательный, обычно не объявляется напрямую. |
| **ResourceBundleViewResolver** | Загружает имена представлений из `ResourceBundle` (i18n). | Полезно, когда имена представлений зависят от локали. | ```java @Bean public ResourceBundleViewResolver resolver() { ResourceBundleViewResolver v = new ResourceBundleViewResolver(); v.setBasename(\"views\"); return v; } ``` |
| **TilesViewResolver** | Интеграция с Apache Tiles (layout‑шаблоны). | Позволяет использовать `TilesConfigurer` и `TilesView`. | ```java @Bean public TilesViewResolver resolver() { TilesViewResolver v = new TilesViewResolver(); v.setOrder(0); return v; } ``` |
| **ContentNegotiatingViewResolver** | Выбирает представление в зависимости от `Accept`‑заголовка клиента (JSON, XML, HTML и т.д.). | Работает в паре с `RequestMappingHandlerAdapter` и `HttpMessageConverter`. | ```java @Bean public ContentNegotiatingViewResolver resolver() { ContentNegotiatingViewResolver v = new ContentNegotiatingViewResolver(); v.setDefaultViews(Arrays.asList(new MappingJackson2JsonView())); return v; } ``` |
| **ThymeleafViewResolver** | Специализированный резолвер для Thymeleaf. | Позволяет задать `templateEngine`, `characterEncoding`, `cacheable`. | ```java @Bean public ThymeleafViewResolver resolver() { ThymeleafViewResolver v = new ThymeleafViewResolver(); v.setTemplateEngine(templateEngine()); v.setCharacterEncoding(\"UTF-8\"); return v; } ``` |
| **FreeMarkerViewResolver** | Специализированный резолвер для FreeMarker. | Позволяет задать `freemarkerConfiguration`, `cache`, `suffix`. | ```java @Bean public FreeMarkerViewResolver resolver() { FreeMarkerViewResolver v = new FreeMarkerViewResolver(); v.setSuffix(\".ftl\"); v.setCache(true); return v; } ``` |
| **VelocityViewResolver** | Специализированный резолвер для Velocity. | Поддерживает `velocityConfigurer`, `suffix`. | ```java @Bean public VelocityViewResolver resolver() { VelocityViewResolver v = new VelocityViewResolver(); v.setSuffix(\".vm\"); return v; } ``` |

> **Часто** в приложении используют комбинацию:
> 1. `InternalResourceViewResolver` (JSP)
> 2. `ThymeleafViewResolver` (если есть Thymeleaf)
> 3. `ContentNegotiatingViewResolver` (для API)

---

## Типы View‑движков (технологий шаблонов)

| Движок | Что это | Ключевые особенности | Когда использовать |
|--------|---------|----------------------|--------------------|
| **JSP** (JSTL, EL) | Традиционный серверный шаблон, обрабатывается контейнером (Tomcat, Jetty). | Позволяет писать Java‑код в шаблоне, но лучше избегать логики. | Классические веб‑приложения, когда нужен быстрый старт. |
| **Thymeleaf** | Современный шаблонизатор, работает как в серверном, так и в клиентском режиме. | Полностью декларативный, поддерживает HTML5, валидацию в браузере, хорошая интеграция с Spring. | Приложения, где важна читаемость шаблонов и безопасность. |
| **FreeMarker** | Шаблонизатор без Java‑кодов, использует `${}` синтаксис. | Быстрый, гибкий, поддерживает включения и макросы. | Приложения с большим количеством статических страниц. |
| **Velocity** | Старый, но всё ещё используемый. | Легковесный, прост в настройке. | Существующие проекты, где уже используется Velocity. |
| **XSLT** | Преобразование XML в XML/HTML. | Позволяет писать стили в XSLT, но сложно для больших приложений. | Когда данные приходят в XML‑формате и нужно их преобразовать. |
| **Groovy Templates** | Шаблоны Groovy, интегрированные с Spring. | Позволяют писать Groovy‑код в шаблонах. | Понимающие Groovy проекты. |
| **Mustache / Handlebars** | Логика‑свободные шаблоны. | Простые, без вложенных выражений. | Минимальные шаблоны, где нужна простота. |

> **Замечание:**  
> *ViewResolver* не ограничивается только этими движками. Можно написать свой `View` (например, генерацию PDF, SVG, CSV и т.д.) и добавить его через `BeanNameViewResolver` или `InternalResourceViewResolver` (указать `viewClass`).

---

## Ключевые особенности и нюансы

| Пункт | Что важно знать |
|-------|-----------------|
| **Порядок резолверов** | `ViewResolver`’ы обрабатываются в порядке их `order` (по умолчанию `Ordered.LOWEST_PRECEDENCE`). Вы можете задать порядок, чтобы, например, `ContentNegotiatingViewResolver` был первым. |
| **Кэширование** | Большинство резолверов кэшируют найденные `View`‑объекты (внутреннее поле `cache`). Это ускоряет повторные запросы. Можно отключить кэширование (`setCache(false)`). |
| **Префиксы и суффиксы** | Позволяют автоматически добавлять путь до папки и расширение файла (`/WEB-INF/views/` + `.jsp`). |
| **Redirect / Forward** | Если имя представления начинается с `redirect:` или `forward:`, Spring использует специальные `View`‑объекты (`RedirectView`, `InternalResourceView`). |
| **Content Negotiation** | `ContentNegotiatingViewResolver` может отдавать разные представления (JSON, XML, HTML) в зависимости от заголовка `Accept`. |
| **Tiles** | Позволяют использовать шаблоны макетов, чтобы разделить layout и содержимое. |
| **Множественные движки** | Можно одновременно использовать JSP и Thymeleaf, но нужно убедиться, что имена представлений не конфликтуют. |
| **Настройка через Java Config** | В большинстве случаев настройка `ViewResolver` делается через `@Bean` в конфигурации `@Configuration`. |
| **Переопределение** | Если нужно изменить поведение по умолчанию, можно создать собственный `ViewResolver`, наследуя `UrlBasedViewResolver` и переопределив `resolveViewName()`. |
| **Тестирование** | Для тестов удобно использовать `MockMvc` и проверять, какой `View` был выбран. |

---

## Мини‑пример: Spring Boot + Thymeleaf + JSP

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Bean
    public ThymeleafViewResolver thymeleafResolver() {
        ThymeleafViewResolver resolver = new ThymeleafViewResolver();
        resolver.setTemplateEngine(templateEngine());
        resolver.setOrder(1); // выше, чем JSP
        return resolver;
    }

    @Bean
    public InternalResourceViewResolver jspResolver() {
        InternalResourceViewResolver resolver = new InternalResourceViewResolver();
        resolver.setPrefix(\"/WEB-INF/jsp/\");
        resolver.setSuffix(\".jsp\");
        resolver.setOrder(2); // ниже
        return resolver;
    }
}
```

Контроллер:

```java
@GetMapping(\"/welcome\")
public String welcome(Model model) {
    model.addAttribute(\"msg\", \"Hello\");
    return \"welcome\"; // сначала попытается найти Thymeleaf -> /templates/welcome.html
                     // если нет, то JSP -> /WEB-INF/jsp/welcome.jsp
}
```

---

## Итоги

- **ViewResolver** – «путеводитель» от логического имени к реальному представлению.
- Существует несколько готовых резолверов (JSP, Thymeleaf, FreeMarker, Tiles, ContentNegotiating и др.).
- Каждый резолвер имеет свои настройки: префикс/суффикс, кэш, порядок, класс представления.
- Технологии шаблонов (view engines) – JSP, Thymeleaf, FreeMarker, Velocity, XSLT, Groovy и др.; они отвечают за фактическую отрисовку.
- В Spring MVC можно комбинировать несколько движков и резолверов, контролируя приоритеты и кэширование.
</p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Валидацию входных данных</summary>
    <p style='font-size: 14px'>

**Валидация входных данных в Spring MVC**  
(на примере JSR‑303/JSR‑380 – Bean Validation)

| Что | Как реализовать | Ключевые моменты |
|-----|-----------------|------------------|
| **Основная аннотация** | `@Valid` (или `@Validated`) на параметре метода | `@Valid` запускает валидатор по умолчанию. `@Validated` позволяет указать группы. |
| **Объекты‑DTO** | Класс‑DTO с аннотациями `@NotNull`, `@Size`, `@Email` и т.п. | Каждый объект валидируется рекурсивно, если вложенные поля помечены `@Valid`. |
| **BindingResult** | Параметр `BindingResult` сразу после `@Valid` | Позволяет проверить `result.hasErrors()` и вернуть пользователю ошибки. |
| **Контроллер** | ```java @RestController public class UserController { @PostMapping(\"/users\") public ResponseEntity<?> create(@Valid @RequestBody UserDto dto, BindingResult br) { … } } ``` | `@RequestBody` + `@Valid` – валидируем JSON‑пакет. |
| **Формы (MVC)** | ```java @GetMapping(\"/form\") public String form(Model model) { model.addAttribute(new UserDto()); return \"form\"; } @PostMapping(\"/form\") public String submit(@Valid @ModelAttribute UserDto dto, BindingResult br) { … } ``` | `@ModelAttribute` + `@Valid` – валидируем данные формы. |
| **Параметры запроса** | ```java @GetMapping(\"/search\") public void search(@RequestParam @NotBlank String q) { … } ``` | `@Valid` не нужен: JSR‑303 аннотации можно ставить прямо на `@RequestParam`. |
| **Пути и заголовки** | Для сложных объектов можно валидировать `@PathVariable` и `@RequestHeader` через `@Valid` и `BindingResult`. | Обычно используют простые типы, но всё равно работает. |
| **Группы валидации** | ```java @Validated(OnCreate.class) @PostMapping(\"/users\") public … create(@Valid @RequestBody UserDto dto) { … } ``` | Позволяет различать правила для создания, обновления и т.п. |
| **Пользовательские валидаторы** | 1. Создаём аннотацию `@StrongPassword`. 2. Реализуем `ConstraintValidator<StrongPassword, String>`. 3. Указываем `@Target`, `@Retention`, `@Constraint(validatedBy = …)`. | Позволяет писать бизнес‑логики валидации. |
| **InitBinder** | ```java @InitBinder public void initBinder(WebDataBinder binder) { binder.addValidators(new CustomValidator()); } ``` | Регистрация кастомных валидаторов и конвертеров на уровне контроллера. |
| **Глобальная обработка ошибок** | ```java @ControllerAdvice public class ValidationHandler { @ExceptionHandler(MethodArgumentNotValidException.class) public ResponseEntity<ErrorResponse> handle(MethodArgumentNotValidException ex) { … } } ``` | Переводим `BindingResult` в JSON‑ответ. |
| **Internationalization** | `messages.properties` (или `messages_ru.properties`). Связываем `ValidationMessages` через `messageSource`. | Валидационные сообщения берутся из файла и локализуются. |
| **Валидация коллекций** | `@Valid List<@Valid UserDto>` | `@Valid` применяет к каждому элементу. |
| **Проверка вложенных объектов** | В DTO: `@Valid @NotNull private AddressDto address;` | Рекурсивная валидация. |
| **Проверка даты** | `@Past`, `@Future`, `@DateTimeFormat(iso = ISO.DATE)` | Форматирование через Spring и проверка через Bean‑Validation. |
| **Проверка JSON‑полей** | В DTO: `@JsonFormat(pattern = \"yyyy-MM-dd\") @Past LocalDate birthDate;` | Jackson + Bean‑Validation. |

### Мини‑пример

```java
// DTO
public class UserDto {
    @NotBlank
    private String login;

    @Email
    @NotBlank
    private String email;

    @Size(min = 8)
    @StrongPassword   // пользовательский валидатор
    private String password;

    @Valid
    private AddressDto address;
}

// Контроллер
@RestController
@RequestMapping(\"/api/users\")
public class UserController {

    @PostMapping
    public ResponseEntity<?> create(@Valid @RequestBody UserDto dto, BindingResult br) {
        if (br.hasErrors()) {
            return ResponseEntity.badRequest().body(br.getAllErrors());
        }
        // business logic …
        return ResponseEntity.status(HttpStatus.CREATED).build();
    }
}
```

### Что важно помнить

1. **Порядок параметров** – `BindingResult` должен идти сразу после `@Valid`.
2. **`@Validated` vs `@Valid`** – `@Validated` позволяет задать группы (`@Validated(OnCreate.class)`), `@Valid` – только по умолчанию.
3. **Кастомные валидаторы** – регистрируются либо через `@InitBinder`, либо как `@Component` и внедряются в `@Validated`.
4. **Глобальная обработка** – удобно писать `@ControllerAdvice` для единообразного ответа об ошибках.
5. **Локализация** – поместите сообщения в `messages_*.properties` и настройте `MessageSource`.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Async обработка</summary>
    <p style='font-size: 14px'>

## Как включить async‑обработку в Spring MVC

> **Кратко** – возвращаем из контроллера объект `Callable`, `DeferredResult`, `WebAsyncTask` (или `CompletableFuture`) и Spring берёт поток из пула, чтобы выполнить запрос в фоновом режиме.  
> **Полно** – нужно настроить DispatcherServlet, пул потоков, таймауты, обработку ошибок и, при необходимости, `@Async`‑методы в сервисах.

---

### 1. Понимание «асинхронности» в Spring MVC

| Что | Как это работает |
|-----|------------------|
| **Callable / CompletableFuture** | Контроллер сразу возвращает объект, DispatcherServlet запускает его в другом потоке, а сам запрос остаётся открытым до завершения. |
| **DeferredResult** | Позволяет отложить результат до произвольного момента (например, пока не придёт событие из очереди). |
| **WebAsyncTask** | Обёртка над `Callable`, позволяет задать таймаут, callback‑методы `onTimeout`, `onError`. |
| **SseEmitter / ResponseBodyEmitter** | Для потоковой отдачи данных (Server‑Sent Events, chunked responses). |

> **Важно**: требуется контейнер Servlet 3.0+ (Tomcat 8+, Jetty 9+ и т.д.).

---

### 2. Включаем async‑поддержку в конфигурации

```java
@Configuration
@EnableWebMvc   // если вы используете ручную конфигурацию
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void configureAsyncSupport(AsyncSupportConfigurer configurer) {
        // пул потоков для async‑запросов
        configurer.setTaskExecutor(taskExecutor());
        // таймаут по умолчанию (в мс)
        configurer.setDefaultTimeout(30000); // 30 сек
    }

    @Bean
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor ex = new ThreadPoolTaskExecutor();
        ex.setCorePoolSize(10);
        ex.setMaxPoolSize(50);
        ex.setQueueCapacity(100);
        ex.setThreadNamePrefix(\"mvc-async-\");
        ex.initialize();
        return ex;
    }
}
```

> **Spring Boot**: почти всё уже сделано. Если нужно изменить таймаут, добавьте `spring.mvc.async.request-timeout=30000` в `application.yml`.

---

### 3. Пример контроллера

```java
@RestController
public class AsyncController {

    @GetMapping(\"/async\")
    public Callable<String> asyncEndpoint() {
        return () -> {
            // долгий расчёт, обращение к БД и т.д.
            Thread.sleep(2000);
            return \"Async result\";
        };
    }

    @GetMapping(\"/deferred\")
    public DeferredResult<String> deferredEndpoint() {
        DeferredResult<String> result = new DeferredResult<>(5000L);
        // асинхронно заполняем результат
        CompletableFuture.runAsync(() -> {
            try { Thread.sleep(2000); } catch (InterruptedException ignored) {}
            result.setResult(\"Deferred result\");
        });
        return result;
    }

    @GetMapping(\"/timeout\")
    public WebAsyncTask<String> timeoutEndpoint() {
        return new WebAsyncTask<>(3000, () -> {
            Thread.sleep(5000); // > timeout
            return \"Should not reach\";
        })
        .onTimeout(() -> \"Timed out\")
        .onError(ex -> \"Error: \" + ex.getMessage());
    }
}
```

---

### 4. `@Async` в сервисах

Если бизнес‑логика выполняется в отдельном пуле потоков, используйте `@Async`:

```java
@Service
public class AsyncService {

    @Async(\"taskExecutor\")   // явно указываем пул
    public CompletableFuture<String> heavyTask() {
        // долгий расчёт
        return CompletableFuture.completedFuture(\"Done\");
    }
}
```

> **Настройка**:
> ```java
> @Configuration
> @EnableAsync
> public class AsyncConfig {
>     @Bean(name = \"taskExecutor\")
>     public Executor taskExecutor() {
>         ThreadPoolTaskExecutor ex = new ThreadPoolTaskExecutor();
>         ex.setCorePoolSize(20);
>         ex.setMaxPoolSize(100);
>         ex.setQueueCapacity(200);
>         ex.setThreadNamePrefix(\"async-\");
>         ex.initialize();
>         return ex;
>     }
>
>     @Bean
>     public AsyncUncaughtExceptionHandler asyncUncaughtExceptionHandler() {
>         return new SimpleAsyncUncaughtExceptionHandler();
>     }
> }
> ```

---

### 5. Обработка ошибок и отмена

| Сценарий | Как обрабатывать |
|----------|------------------|
| Ошибка в `Callable` | `@ExceptionHandler` в контроллере, либо `onError` в `WebAsyncTask`. |
| Таймаут | `setDefaultTimeout` + `onTimeout` в `WebAsyncTask`. |
| Отмена | `DeferredResult#setTimeout` + `setResult(null)` при отмене, либо `CancellationException` в `CompletableFuture`. |
| Перенос контекста (`RequestContextHolder`) | Spring автоматически переносит, но при использовании `CompletableFuture` в `@Async` может понадобиться `@Async` + `@EnableAsync`. |

---

### 6. Что ещё стоит знать

| Что | Почему важно |
|-----|--------------|
| **Пул потоков** | Если пула не хватает, запросы будут ждать. Лучше не использовать `SimpleAsyncTaskExecutor`. |
| **Таймауты** | По умолчанию 30 сек. Если ваш сервис длительный, увеличьте. |
| **Параллельные запросы** | При `Callable` каждый запрос обрабатывается в отдельном потоке, но они могут конкурировать за ресурсы. |
| **Server‑Sent Events** | Для реального «push‑сообщения» используйте `SseEmitter`. |
| **Reactive** | Если нужна полностью неблокирующая модель, рассмотрите Spring WebFlux вместо MVC. |

---

## Итог

1. **Включите async‑поддержку** (`configureAsyncSupport` или `spring.mvc.async.*`).
2. **Настройте пул потоков** (`ThreadPoolTaskExecutor`).
3. **Возвращайте** `Callable`, `DeferredResult`, `WebAsyncTask` или `CompletableFuture` из контроллера.
4. **(Опционально)** используйте `@Async` в сервисах для фоновой работы.
5. **Управляйте таймаутами, ошибками и отменой** через конфигурацию и callback‑методы.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Interceptors</summary>
    <p style='font-size: 14px'>

## Что такое Interceptors в Spring MVC?

**Interceptors** – это специальные объекты, которые «пойманы» каждый HTTP‑запрос до того, как он попадёт к контроллеру, и после того, как контроллер обработал запрос.  
В Spring MVC они реализуются через интерфейс `HandlerInterceptor` (или наследника `HandlerInterceptorAdapter`, который уже считается устаревшим).

| Фаза | Метод | Что делает |
|------|-------|------------|
| **До** | `preHandle(HttpServletRequest, HttpServletResponse, Object handler)` | Выполняется **до** вызова метода контроллера. Может отменить дальнейшую обработку, возвращая `false`. |
| **После** | `postHandle(HttpServletRequest, HttpServletResponse, Object handler, ModelAndView mv)` | Выполняется **после** метода контроллера, но **перед** рендерингом вида. Можно изменить `ModelAndView`. |
| **После** | `afterCompletion(HttpServletRequest, HttpServletResponse, Object handler, Exception ex)` | Выполняется **после** завершения всего запроса (включая рендеринг вида). Здесь можно освобождать ресурсы, логировать ошибки и т.п. |

> **Важно:** `afterCompletion` вызывается даже при исключении, но только если `preHandle` вернул `true`.

---

## Зачем нужны Interceptors?

| Задача | Как решается через Interceptor |
|--------|--------------------------------|
| **Аутентификация/авторизация** | Проверить токен/сессию в `preHandle`. Если не валидно – вернуть `false` и отправить 401/403. |
| **Логирование** | Записывать входящие запросы, время выполнения, статус ответа. Делается в `preHandle` и/или `afterCompletion`. |
| **Измерение производительности** | Засекать время начала и окончания (`preHandle` → `afterCompletion`). |
| **Модификация запроса/ответа** | Добавлять заголовки, параметры, менять атрибуты `HttpServletRequest` (`preHandle`). |
| **Обработка локали/темы** | Устанавливать `LocaleContext` в `preHandle`. |
| **Валидация** | Проверять наличие обязательных параметров до выполнения контроллера. |
| **Кеширование** | Проверять наличие cached‑ответа в `preHandle`; если найден – отправить его сразу. |

---

## Как подключить Interceptor

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Bean
    public HandlerInterceptor myInterceptor() {
        return new MyInterceptor();      // ваш класс, реализующий HandlerInterceptor
    }

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(myInterceptor())
                .addPathPatterns(\"/api/**\")   // URL‑паттерны, к которым применяем
                .excludePathPatterns(\"/api/public/**\"); // исключения
    }
}
```

* **Каскадность** – если несколько интерцепторов зарегистрированы, они выполняются в порядке регистрации.
* **Thread‑safe** – интерцепторы обычно объявляются как синглтон‑beans, но они сами не держат состояние, поэтому безопасны для многопоточного использования.

---

## Отличия от Filter

| Feature | Filter | Interceptor |
|---------|--------|-------------|
| Уровень | Servlet (весь запрос) | MVC (только запросы, попадающие в HandlerMapping) |
| Доступ к MVC‑объектам | Нет | Есть (Handler, ModelAndView) |
| Возможность отменить обработку | Нет (можно вернуть статус) | Да (return false в `preHandle`) |
| Регистрация | `web.xml` / `FilterRegistrationBean` | `WebMvcConfigurer` |

---

## Кратко

- **Interceptors** – это пред‑ и пост‑обработчики запросов в Spring MVC.
- Реализуются через `HandlerInterceptor` (с 5.3+ – интерфейс с дефолтными методами).
- Позволяют выполнять общую логику: auth, логирование, измерения, модификацию запросов/ответов.
- Регистрация через `WebMvcConfigurer.addInterceptors`.
- Отличаются от фильтров тем, что работают внутри MVC‑контекста и могут влиять на `ModelAndView`.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Централизованная обработка исключений</summary>
    <p style='font-size: 14px'>

## Централизованная обработка исключений в Spring MVC

> **Главная идея** – вынести все `try‑catch`/логирование/форматирование ошибок в один «объединённый» слой, чтобы контроллеры оставались чистыми.

| Как это делается | Кратко |
|-------------------|--------|
| **`@ControllerAdvice`** | Аннотация над классом, которая делает его «глобальным» обработчиком. Методы с `@ExceptionHandler` из любого контроллера будут перехвачены здесь. |
| **`@RestControllerAdvice`** | Сокращение `@ControllerAdvice + @ResponseBody`. Для REST‑API. |
| **`@ExceptionHandler(...)`** | Метод‑обработчик.  <br>• Принимает исключение (или массив) <br>• Может вернуть `ResponseEntity`, `ModelAndView`, `String` (view‑name) или просто `void` (если указан `@ResponseBody`). |
| **`@ResponseStatus`** | Устанавливает HTTP‑код, если метод не возвращает `ResponseEntity`. |
| **`ResponseEntityExceptionHandler`** | Базовый класс, который уже реализует обработку типичных ошибок (валидация, `HttpMessageNotReadableException`, `NoHandlerFoundException` и пр.).  <br>Можно наследовать и переопределять нужные методы. |
| **`HandlerExceptionResolver`** | Интерфейс, которым можно реализовать собственный резолвер, но в большинстве проектов достаточно аннотаций выше. |
| **`ErrorController`** | Для обработки ошибок, которые Spring‑Boot перенаправляет на `/error`. Позволяет вернуть собственный JSON/HTML. |

### Как выглядит типичный `ControllerAdvice`

```java
@RestControllerAdvice
@Order(Ordered.HIGHEST_PRECEDENCE)   // при конфликте – первый будет применён
public class GlobalExceptionHandler {

    // 1. Специфический обработчик
    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<ErrorDto> handleNotFound(UserNotFoundException ex) {
        ErrorDto dto = new ErrorDto(HttpStatus.NOT_FOUND.value(), ex.getMessage());
        return new ResponseEntity<>(dto, HttpStatus.NOT_FOUND);
    }

    // 2. Общий обработчик (тут ловим всё остальное)
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorDto> handleAll(Exception ex) {
        ErrorDto dto = new ErrorDto(HttpStatus.INTERNAL_SERVER_ERROR.value(),
                                    \"Unexpected error\");
        return new ResponseEntity<>(dto, HttpStatus.INTERNAL_SERVER_ERROR);
    }

    // 3. Валидация (ResponseEntityExceptionHandler уже содержит метод, но можно переопределить)
    @Override
    protected ResponseEntity<Object> handleMethodArgumentNotValid(
            MethodArgumentNotValidException ex,
            HttpHeaders headers,
            HttpStatus status,
            WebRequest request) {

        List<String> errors = ex.getBindingResult()
                                .getFieldErrors()
                                .stream()
                                .map(err -> err.getField() + \": \" + err.getDefaultMessage())
                                .collect(Collectors.toList());

        ErrorDto dto = new ErrorDto(HttpStatus.BAD_REQUEST.value(), errors);
        return new ResponseEntity<>(dto, HttpStatus.BAD_REQUEST);
    }
}
```

### Ключевые особенности и нюансы

| Что важно знать | Что делает |
|-----------------|-----------|
| **Порядок приоритетов** | 1) `@ExceptionHandler` в конкретном контроллере <br>2) `@ControllerAdvice` (с учётом `@Order`) <br>3) `HandlerExceptionResolver` (например, `DefaultHandlerExceptionResolver`) <br>4) Фallback‑стандартное поведение Spring (404, 500 и пр.) |
| **Ограничение области** | `@ControllerAdvice` можно настроить: `basePackage`, `annotations`, `assignableTypes`. Это удобно, если у вас несколько модулей. |
| **Логирование** | Внутри `@ExceptionHandler` обычно делаем лог (`log.error(ex)`), а затем возвращаем клиенту понятный ответ. |
| **Код ответа** | Либо через `@ResponseStatus`, либо явно в `ResponseEntity`. |
| **Формат ответа** | Для REST – JSON (POJO, `ErrorDto`). Для MVC – `ModelAndView` с view‑name. |
| **Валидация** | `MethodArgumentNotValidException`, `BindException`. Перехватываем в `ResponseEntityExceptionHandler` или в собственном `@ControllerAdvice`. |
| **Проблемы 404/405** | При `ThrowExceptionIfNoHandlerFound=true` можно ловить `NoHandlerFoundException`. |
| **Безопасность** | `AccessDeniedException`, `AuthenticationException` можно обрабатывать, чтобы вернуть 403/401. |
| **База данных** | `DataIntegrityViolationException`, `ConstraintViolationException` – можно вернуть 400 или 409. |
| **Пользовательские исключения** | Создайте собственный класс, пометьте `@ResponseStatus` и просто ловите его в `@ControllerAdvice`. |
| **Кастомизация ошибок Spring‑Boot** | Если хотите изменить `/error`‑путь, реализуйте `ErrorController` и отдавайте свой JSON/HTML. |
| **`@Order`** | Позволяет иметь несколько `@ControllerAdvice` и задать порядок их выполнения. |
| **`@RestControllerAdvice`** | Автоматически добавляет `@ResponseBody`, поэтому не нужно писать `@ResponseBody` в каждом методе. |
| **Кеширование** | Не забывайте, что `@ControllerAdvice`‑классы могут быть `@Singleton`, поэтому используйте только thread‑safe поля. |
| **Тестируемость** | Поскольку обработчики – обычные методы, их можно тестировать с помощью `MockMvc` и `@WebMvcTest`. |
| **Переопределение `handleExceptionInternal`** | Если нужно изменить общий шаблон ответа (например, добавить `timestamp`, `requestId`), переопределите этот метод. |

### Быстрый чек‑лист перед интервью

1. **Как объявить глобальный обработчик?**
   ```java
   @RestControllerAdvice
   public class GlobalExceptionHandler { … }
   ```

2. **Как указать HTTP‑код?**
    - `@ResponseStatus(HttpStatus.NOT_FOUND)` на исключении,
    - или `return new ResponseEntity<>(…, HttpStatus.NOT_FOUND)`.

3. **Как обрабатывать валидацию?**
    - `MethodArgumentNotValidException` в `ResponseEntityExceptionHandler`,
    - либо `@ExceptionHandler(MethodArgumentNotValidException.class)`.

4. **В каком порядке обрабатываются исключения?**  
   Controller → ControllerAdvice → HandlerExceptionResolver → default.

5. **Как ограничить область `@ControllerAdvice`?**  
   `@ControllerAdvice(basePackages = \"com.myapp\")`.

6. **Как вернуть кастомную страницу ошибки?**  
   `return new ModelAndView(\"error/404\")` в `@ExceptionHandler`.

7. **Как добавить логирование?**  
   `log.error(\"Unhandled exception\", ex);`.

8. **Как использовать `@Order`?**  
   `@Order(Ordered.HIGHEST_PRECEDENCE)` – первый будет применён.

9. **Как кастомизировать `/error` в Spring Boot?**  
   Реализовать `ErrorController` и вернуть свой JSON.

10. **Как тестировать?**  
    `@WebMvcTest(GlobalExceptionHandler.class)` + `MockMvc` + `@WithMockUser` (если нужен Security).

---

**Итого** – в Spring MVC централизованную обработку исключений реализуют через `@ControllerAdvice`/`@RestControllerAdvice`, добавляя методы `@ExceptionHandler`. При желании можно наследовать `ResponseEntityExceptionHandler`, переопределять его методы и/или реализовать собственный `HandlerExceptionResolver`. Это позволяет централизовать логирование, формирование ответа и назначение HTTP‑статусов, а также легко масштабировать и тестировать систему ошибок.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Multipart загрузка файлов</summary>
    <p style='font-size: 14px'>

## Как работает multipart‑загрузка файлов в Spring MVC

| Что | Как реализуется | Что важно знать |
|-----|-----------------|-----------------|
| **Формат запроса** | `multipart/form-data` (в форме `<form enctype=\"multipart/form-data\">` или `FormData` в JS). | Каждый файл и поле отправляются как отдельный *часть* (part) с заголовками `Content-Disposition`, `Content-Type`. |
| **Парсинг** | Servlet 3.0+ (или Apache Commons‑FileUpload, если вы явно включите `CommonsMultipartResolver`). | Внутри контейнера создаётся `MultipartHttpServletRequest`. |
| **Spring‑обёртка** | `MultipartResolver` (по умолчанию `StandardServletMultipartResolver`). | Он «оборачивает» `MultipartHttpServletRequest` и предоставляет `MultipartFile`. |
| **Контроллер** | ```java
@PostMapping(value=\"/upload\", consumes=MediaType.MULTIPART_FORM_DATA_VALUE)
public ResponseEntity<?> upload(@RequestParam(\"file\") MultipartFile file) { … }
``` | `@RequestParam`, `@RequestPart`, `@ModelAttribute` – всё это может принимать `MultipartFile`. Для нескольких файлов: `MultipartFile[]`, `List<MultipartFile>`. |
| **Получение данных** | `MultipartFile` имеет методы:<br>`getOriginalFilename()`, `getSize()`, `getContentType()`, `getBytes()`, `getInputStream()`, `transferTo(File dest)` | Если файл большой, используйте `transferTo` для потоковой записи, а не `getBytes()` (это держит всё в памяти). |
| **Настройки лимитов** | В `application.yml`/`application.properties`:<br>`spring.servlet.multipart.max-file-size=10MB`<br>`spring.servlet.multipart.max-request-size=20MB` | При превышении выбрасывается `MaxUploadSizeExceededException`. Можно перехватить через `@ControllerAdvice`. |
| **Межпамятные настройки** | `MultipartResolver` может быть сконфигурирован вручную:<br>`@Bean public MultipartResolver multipartResolver() { return new StandardServletMultipartResolver(); }`<br>или через `CommonsMultipartResolver` с `setMaxUploadSize`, `setMaxInMemorySize` и т.д. | `maxInMemorySize` (порог, после которого файл пишется во временный файл). |
| **Временное хранилище** | По умолчанию – в temp‑директории сервера (`java.io.tmpdir`). | Можно задать свой `location` через `MultipartConfigElement` или `CommonsMultipartResolver#setUploadTempDir`. |
| **Кодировка** | `StandardServletMultipartResolver` использует кодировку сервлета (`request.getCharacterEncoding()`), можно задать `setDefaultEncoding(\"UTF-8\")`. | Полезно, если в форме есть русские имена файлов. |
| **Загрузка нескольких файлов** | `@RequestParam(\"files\") MultipartFile[] files` | В контроллере просто итерируемся по массиву. |
| **Совместимость с JSON** | В одном запросе можно отправить JSON‑часть + файл: `@RequestPart(\"metadata\") MyDto dto, @RequestPart(\"file\") MultipartFile file`. | `@RequestBody` нельзя использовать вместе с multipart, т.к. тело уже разбито. |
| **Обработка ошибок** | `MaxUploadSizeExceededException`, `FileSizeLimitExceededException`, `MissingServletRequestPartException` | Перехватываем через `@ExceptionHandler`/`@ControllerAdvice`. |
| **Безопасность** | Не храните файлы напрямую в публичных директориях без проверки MIME‑типа/размера. | Валидация: `file.getContentType()`, `file.getSize()`, проверка расширения. |
| **Потоковая запись** | `file.transferTo(new File(\"/path/target\"))` | Убирает нагрузку на память, особенно при больших файлах. |
| **Проверка имени** | `file.getOriginalFilename()` может содержать путь (в старых браузерах). | Нужно использовать `StringUtils.cleanPath()` из Spring. |
| **Отладка** | При `debug` логах можно увидеть, как `MultipartResolver` создаёт `MultipartFile`. | В `logback.xml` задать уровень `DEBUG` для `org.springframework.web.multipart`. |

### Быстрый пример

```java
@RestController
@RequestMapping(\"/api\")
public class FileUploadController {

    @PostMapping(value = \"/upload\",
                 consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
    public ResponseEntity<String> upload(
            @RequestPart(\"file\") MultipartFile file,
            @RequestPart(\"meta\") @Valid FileMeta meta) throws IOException {

        // Валидация
        if (file.isEmpty() || file.getSize() > 5 * 1024 * 1024)
            throw new IllegalArgumentException(\"Bad file\");

        // Сохраняем
        Path target = Paths.get(\"/data/uploads\", StringUtils.cleanPath(file.getOriginalFilename()));
        Files.copy(file.getInputStream(), target, StandardCopyOption.REPLACE_EXISTING);

        return ResponseEntity.ok(\"Uploaded: \" + target.toString());
    }
}
```

### Итоги

1. **HTTP multipart** → контейнер парсит → Spring `MultipartResolver` → `MultipartFile`.
2. **Конфигурируем лимиты** (`max-file-size`, `max-request-size`, `fileSizeThreshold`).
3. **В контроллере** используйте `@RequestParam`/`@RequestPart`/`@ModelAttribute`.
4. **Обрабатывайте ошибки** (`MaxUploadSizeExceededException`).
5. **Не держите большие файлы в памяти** – используйте `transferTo` или потоковую запись.
 </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Content negotiation</summary>
    <p style='font-size: 14px'>

## Content‑Negotiation в Spring MVC (Java)

Content‑Negotiation (CN) – это механизм, позволяющий Spring решить, в каком формате отдавать ответ (JSON, XML, HTML, plain‑text и т.д.) и какой формат принимать в запросе.  
Spring использует **`ContentNegotiationManager`** – цепочку стратегий, которые проверяют запрос в следующем порядке:

| Стратегия | Что проверяется | По умолчанию включена |
|-----------|-----------------|----------------------|
| `HeaderContentNegotiationStrategy` | Заголовок `Accept` | ✔️ |
| `PathExtensionContentNegotiationStrategy` | Суффикс URL (`.json`, `.xml`) | ✔️ |
| `ParameterContentNegotiationStrategy` | Параметр `?format=json` | ❌ |
| `FixedContentNegotiationStrategy` | Фиксированный тип (например, только `application/json`) | ❌ |

### Как настроить

#### 1. Через `WebMvcConfigurer`

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void configureContentNegotiation(ContentNegotiationConfigurer configurer) {
        configurer
            .favorPathExtension(true)          // use .json/.xml
            .favorParameter(true)              // ?format=json
            .parameterName(\"format\")           // имя параметра
            .ignoreUnknownPathExtensions(false)
            .useRegisteredSuffixes(true)       // только зарегистрированные суффиксы
            .defaultContentType(MediaType.APPLICATION_JSON) // fallback
            .mediaType(\"xml\", MediaType.APPLICATION_XML)
            .mediaType(\"json\", MediaType.APPLICATION_JSON);
    }
}
```

#### 2. Через `application.properties` (Spring Boot)

```properties
spring.mvc.contentnegotiation.favor-path-extension=true
spring.mvc.contentnegotiation.favor-parameter=true
spring.mvc.contentnegotiation.parameter-name=format
spring.mvc.contentnegotiation.ignore-unknown-path-extensions=false
spring.mvc.contentnegotiation.use-registered-suffixes=true
spring.mvc.contentnegotiation.default-content-type=application/json
spring.mvc.contentnegotiation.media-types.xml=application/xml
spring.mvc.contentnegotiation.media-types.json=application/json
```

#### 3. В контроллере

```java
@RestController
@RequestMapping(\"/api\")
public class DemoController {

    @GetMapping(value = \"/data\", produces = { MediaType.APPLICATION_JSON_VALUE,
                                              MediaType.APPLICATION_XML_VALUE })
    public MyDto getData() {
        return new MyDto(...);
    }
}
```

* `produces` – явно указывает допустимые MIME‑типы, которые может вернуть метод.
* `consumes` – аналогично для входящих данных.
* Если `produces` не указан, Spring пытается сопоставить тип из `ContentNegotiationManager`.

#### 4. Настройка `HttpMessageConverter`

Spring уже содержит конвертеры для JSON (`MappingJackson2HttpMessageConverter`), XML (`Jaxb2RootElementHttpMessageConverter`), plain‑text и т.д.  
Если нужен свой формат, добавьте свой `HttpMessageConverter` в `WebMvcConfigurer`:

```java
@Override
public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
    converters.add(new MyCustomConverter());
}
```

### Ключевые особенности

| Особенность | Что делает | Как задействовать |
|-------------|------------|-------------------|
| **Accept header** | Определяет предпочтительный MIME‑тип ответа | По умолчанию включена |
| **Path extension** | `/.json`, `/.xml` | `favorPathExtension=true` |
| **Query param** | `?format=json` | `favorParameter=true` |
| **Фиксированный тип** | Всегда отдавать один формат | `FixedContentNegotiationStrategy` |
| **Префикс MIME‑типа** | `*/*` → выбирается по умолчанию | `defaultContentType` |
| **Переопределение суффиксов** | Соответствие `xml` → `application/xml` | `mediaType(\"xml\", MediaType.APPLICATION_XML)` |
| **Игнорировать неизвестные суффиксы** | Предотвращает ошибку 406 | `ignoreUnknownPathExtensions(false)` |
| **Использовать только зарегистрированные суффиксы** | Защита от «поддельных» расширений | `useRegisteredSuffixes(true)` |
| **Переопределение `Content-Type` в запросе** | Для `POST/PUT` | `consumes`/`@RequestBody` |

### Частые проблемы

| Проблема | Причина | Решение |
|----------|---------|---------|
| 406 Not Acceptable | `Accept: */*` но нет подходящего конвертера | Добавьте нужный `HttpMessageConverter` |
| Ошибка при `/.json` | Суффикс не зарегистрирован | `useRegisteredSuffixes(false)` или зарегистрируйте суффикс |
| Плохая производительность | Слишком много стратегий | Ограничьте `favorParameter`/`favorPathExtension` |
| Ошибка сериализации XML | Не зарегистрирован Jaxb | Добавьте `@XmlRootElement` и `Jaxb2RootElementHttpMessageConverter` |

---

**Итого**:
1. Настройте `ContentNegotiationConfigurer` (или `application.properties`).
2. Укажите `produces/consumes` в контроллерах.
3. При необходимости добавьте свои `HttpMessageConverter`.
4. Проверьте, что все нужные MIME‑типы и суффиксы зарегистрированы.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Тесты контроллеров (MockMvc)</summary>
    <p style='font-size: 14px'>

## Как тестировать контроллеры Spring MVC с MockMvc

| Что | Как это делается | Что проверяем |
|-----|------------------|--------------|
| **Инициализация MockMvc** | ```java<br>@SpringBootTest<br>@AutoConfigureMockMvc<br>public class UserControllerTest {<br>  @Autowired MockMvc mockMvc;<br>}```<br>или `MockMvcBuilders.standaloneSetup(controller)` | Создаём “фальшивую” среду, в которой можно посылать HTTP‑запросы к контроллеру |
| **Подменяем сервисы** | `@MockBean UserService userService;`<br>или `@SpyBean` | Сервис‑слой не вызывается, а возвращает предопределённые значения |
| **Построитель запроса** | `mockMvc.perform(get(\"/users/1\"))`<br>`mockMvc.perform(post(\"/users\")…contentType(MediaType.APPLICATION_JSON).content(json))` | `RequestBuilder` (`get`, `post`, `put`, `delete`, …) + параметры, заголовки, тело, CSRF, сессия, cookie |
| **Проверка результата** | ```java<br>mockMvc.perform(get(\"/users/1\"))<br>  .andExpect(status().isOk())<br>  .andExpect(content().contentType(MediaType.APPLICATION_JSON))<br>  .andExpect(jsonPath(\"$.name\").value(\"John\"))<br>  .andExpect(view().name(\"userView\"))<br>  .andExpect(model().attributeExists(\"user\"))<br>  .andExpect(redirectedUrl(\"/users/1\"))<br>  .andExpect(header().string(\"Location\", \"/users/1\"))<br>  .andDo(print());<br>``` | • статус<br>• контент‑тип<br>• тело (JSON, XML, строка)<br>• view name<br>• модель<br>• заголовки, cookie, сессия<br>• перенаправления |
| **Проверка ошибок** | `status().is4xxClientError()`<br>`status().is5xxServerError()`<br>`handler().methodName(\"handleNotFound\")` | Проверяем, что контроллер выбрасывает нужный статус/обработчик |
| **Тестирование безопасности** | `@WithMockUser(username=\"admin\",roles={\"ADMIN\"})`<br>`mockMvc.perform(get(\"/admin\")…with(user(\"admin\").roles(\"ADMIN\")))`<br>`mockMvc.perform(get(\"/admin\")…with(anonymous()))` | Проверяем доступ/недоступность URL‑ов, CSRF‑защиту, JWT и т.д. |
| **Тестирование с SQL‑скриптами** | `@Sql(scripts = \"/test-data.sql\")`<br>`@SqlGroup({@Sql(...), @Sql(...)})` | Подготовка/очистка БД перед/после теста |
| **Транзакционность** | `@Transactional` | После каждого теста БД откатывается, чтобы не влиять на другие тесты |
| **Срезы контекста** | `@WebMvcTest(controllers = UserController.class)`<br>`excludeAutoConfiguration = RestDocsAutoConfiguration.class` | Загружаем только MVC‑компоненты, быстрее и изолированнее |
| **Полный контекст** | `@SpringBootTest(webEnvironment = RANDOM_PORT)` + `TestRestTemplate` | Если нужно проверить реальный HTTP‑порт (не MockMvc) |
| **Проверка модели** | `model().attribute(\"user\", hasProperty(\"id\", is(1L)))`<br>`model().attributeExists(\"users\")` | Проверяем, что контроллер добавил нужные атрибуты |
| **Проверка View** | `view().name(\"user\")` | Убедиться, что выбрана нужная шаблонная страница |
| **Проверка JSON** | `jsonPath(\"$.name\").value(\"John\")`<br>`jsonPath(\"$.roles\", hasSize(2))` | Используем JsonPath для глубоких проверок |
| **Проверка заголовков** | `header().string(\"Content-Type\", MediaType.APPLICATION_JSON_VALUE)`<br>`header().exists(\"Location\")` | Убедиться, что сервер послал нужные заголовки |
| **Проверка cookie** | `cookie().value(\"JSESSIONID\", \".*\")` | Проверяем наличие cookie |
| **Проверка сессии** | `session().attributeExists(\"user\")`<br>`session().attribute(\"user\", hasProperty(\"name\", is(\"John\")))` | Проверяем, что в сессии сохранены нужные атрибуты |
| **Проверка перенаправления** | `redirectedUrl(\"/users/1\")`<br>`redirectedUrlPattern(\"/users/*\")` | Убедиться, что контроллер делает `redirect` |
| **Отладка** | `.andDo(print())`<br>`MockMvcResultHandlers.log()` | Выводим полный HTTP‑ответ в консоль |
| **Проверка handler‑метода** | `handler().handlerType(UserController.class)`<br>`handler().methodName(\"getUser\")` | Убедиться, что именно нужный метод обработал запрос |
| **Проверка interceptor‑ов** | `mockMvc.perform(get(\"/users\")).andExpect(handler().handlerType(UserController.class))` | Убедиться, что interceptor‑ы работают корректно |
| **Проверка аргументов** | `MockMvcBuilders.standaloneSetup(controller).setCustomArgumentResolvers(new PageableHandlerMethodArgumentResolver()).build();` | Тестируем контроллер с кастомными аргументами |
| **Проверка view resolver** | `setViewResolvers(new ThymeleafViewResolver())` | Если используете собственный view resolver |
| **Проверка exception resolver** | `setHandlerExceptionResolvers(new RestExceptionHandler())` | Проверяем, как обрабатываются исключения |
| **Проверка message converter** | `setMessageConverters(new MappingJackson2HttpMessageConverter())` | Убедиться, что сериализация/десериализация работает |
| **Проверка валидаторов** | `setValidator(new LocalValidatorFactoryBean())` | Проверяем, что валидация срабатывает |
| **Проверка CSRF** | `mockMvc.perform(post(\"/users\").with(csrf()))` | Убедиться, что CSRF‑защита включена |
| **Проверка OAuth2 / JWT** | `mockMvc.perform(get(\"/api\").with(jwt().jwt(jwt -> jwt.claim(\"sub\", \"user\"))))` | Тестируем защищённые эндпоинты |
| **Проверка мультифайлов** | `mockMvc.perform(multipart(\"/upload\").file(file))` | Тестируем загрузку файлов |
| **Проверка flash attributes** | `flashAttr(\"msg\", \"Saved\")` | Проверяем, что flash‑атрибуты передаются |
| **Проверка сессии и cookie одновременно** | `sessionAttr(\"user\", user).cookie(new Cookie(\"token\", \"xyz\"))` | Убедиться, что сессия и cookie работают вместе |
| **Проверка сгенерированных ссылок** | `content().string(containsString(\"/users/1\"))` | Проверяем, что в ответе генерируются правильные ссылки |
| **Проверка через `ResultActions`** | `ResultActions result = mockMvc.perform(get(\"/users/1\"));`<br>`MvcResult mvcResult = result.andReturn();` | Можно получить `MockHttpServletResponse` и делать ручные проверки |
| **Тесты с параметрами** | `param(\"page\", \"2\").param(\"size\", \"10\")` | Проверяем пагинацию |
| **Тесты с заголовками** | `header(\"Accept-Language\", \"ru\")` | Проверяем локализацию |
| **Тесты с cookie** | `cookie(\"lang\", \"en\")` | Проверяем, как сервер реагирует на cookie |
| **Проверка сессии** | `sessionAttr(\"locale\", \"ru\")` | Проверяем, как сервер сохраняет локаль в сессии |
| **Проверка сессии и cookie** | `sessionAttr(\"user\", user).cookie(new Cookie(\"token\", \"xyz\"))` | Проверяем, что сессия и cookie работают вместе |
| **Проверка сессии и cookie одновременно** | `sessionAttr(\"user\", user).cookie(new Cookie(\"token\", \"xyz\"))` | Убедиться, что сессия и cookie работают вместе |

---

### Минимальный пример

```java
@SpringBootTest
@AutoConfigureMockMvc
class UserControllerTest {

    @Autowired MockMvc mockMvc;

    @MockBean UserService userService;

    @Test
    void shouldReturnUser() throws Exception {
        User user = new User(1L, \"John\");
        given(userService.findById(1L)).willReturn(Optional.of(user));

        mockMvc.perform(get(\"/users/1\")
                .accept(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk())
                .andExpect(content().contentType(MediaType.APPLICATION_JSON))
                .andExpect(jsonPath(\"$.name\").value(\"John\"))
                .andDo(print());
    }

    @Test
    @WithMockUser(roles = \"ADMIN\")
    void shouldCreateUser() throws Exception {
        User newUser = new User(null, \"Alice\");
        given(userService.create(any(User.class))).willReturn(new User(2L, \"Alice\"));

        mockMvc.perform(post(\"/users\")
                .contentType(MediaType.APPLICATION_JSON)
                .content(\"{\\\"name\\\":\\\"Alice\\\"}\"))
                .andExpect(status().isCreated())
                .andExpect(header().string(\"Location\", \"/users/2\"));
    }
}
```

---

### Ключевые моменты

1. **MockMvc** – это “фальшивый” HTTP‑клиент, который позволяет протестировать контроллер без запуска сервера.
2. **`@MockBean`** – заменяет реальные бины (например, сервисы) на моки, чтобы изолировать слой контроллера.
3. **`@WithMockUser` и `SecurityMockMvcRequestPostProcessors`** – позволяют быстро тестировать защищённые эндпоинты.
4. **`MockMvcResultMatchers`** – набор готовых матчеров: `status()`, `content()`, `jsonPath()`, `view()`, `model()`, `header()`, `cookie()`, `session()`, `redirectedUrl()` и др.
5. **`MockMvcResultHandlers`** – для отладки: `print()`, `log()`.
6. **Срезы контекста** (`@WebMvcTest`) – быстрее, но не загружает все бины.
7. **Транзакции и SQL‑скрипты** – помогают подготовить и очистить БД.
8. **Проверка модели и view** – важно, если вы используете серверные шаблоны.
9. **Проверка аргументов и interceptor‑ов** – если ваш контроллер использует `@RequestParam`, `Pageable`, `@AuthenticationPrincipal` и т.п.
10. **Проверка ошибок** – убедитесь, что ваш `@ControllerAdvice` возвращает корректный статус и сообщение.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Подключение Spring MVC к Spring Boot</summary>
    <p style='font-size: 14px'>

## Как подключить Spring MVC к Spring Boot

Spring Boot **«подключает»** Spring MVC автоматически.  
Ниже описаны основные шаги и особенности, которые стоит знать.

| Шаг | Что делаем | Почему это важно |
|-----|------------|------------------|
| 1 | **Добавляем зависимость** `spring-boot-starter-web` | Включает Spring MVC, Tomcat (или Jetty) и Jackson. |
| 2 | Создаём класс‑точку входа с аннотацией `@SpringBootApplication` | Это объединяет `@Configuration`, `@EnableAutoConfiguration` и `@ComponentScan`. |
| 3 | **Не добавляйте `@EnableWebMvc`**, если хотите оставить автоконфиг. | `@EnableWebMvc` отключает авто‑настройку MVC и заставляет Spring ждать ручной конфигурации. |
| 4 | Добавляем контроллеры (`@Controller`, `@RestController`) | DispatcherServlet автоматически маршрутизует запросы к методам. |
| 5 | (Опционально) **Переопределяем настройки** через `WebMvcConfigurer` | Позволяет изменить `ViewResolver`, `MessageConverters`, `Interceptors` и т.д., не отключая автоконфиг. |
| 6 | (Опционально) **Конфигурируем свойства** в `application.yml`/`application.properties` | `spring.mvc.*` позволяет задать пути статических ресурсов, формат даты и т.п. |

---

### Минимальный пример

```java
// pom.xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

```java
// DemoApplication.java
@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

```java
// HelloController.java
@RestController
public class HelloController {

    @GetMapping(\"/hello\")
    public String hello() {
        return \"Hello, Spring Boot + MVC!\";
    }
}
```

Запуск: `mvn spring-boot:run` → `http://localhost:8080/hello`

---

### Что делает Spring Boot за вас

| Что | Как реализовано |
|-----|-----------------|
| **DispatcherServlet** | Автоматически создаётся и маппится на `/`. |
| **ViewResolvers** | По умолчанию `InternalResourceViewResolver` (JSP) и `ThymeleafViewResolver` (если зависимость добавлена). |
| **Static resources** | `resources/static`, `public`, `resources` и `META-INF/resources` автоматически обслуживаются. |
| **Message Converters** | Jackson для JSON, JAXB для XML и т.д. |
| **Error handling** | `BasicErrorController` + `ErrorAttributes` → красивый JSON‑ответ. |
| **Health & Actuator** | Если добавить `spring-boot-starter-actuator`, можно получать `/actuator/health`, `/metrics` и т.п. |

---

### Как изменить настройки MVC, не отключая автоконфиг

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController(\"/home\").setViewName(\"home\");
    }

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new MyInterceptor());
    }

    @Override
    public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
        // Добавляем собственный конвертер
    }
}
```

> **Важно:** Не используйте `@EnableWebMvc` вместе с `WebMvcConfigurer`, иначе Spring MVC будет полностью «сброшен» и вам придётся конфигурировать всё вручную.

---

### Итоги

1. **Spring Boot + MVC** – подключается просто, добавив `spring-boot-starter-web`.
2. **Автоконфигурация** делает всё за вас: DispatcherServlet, view‑resolver, статические файлы, конвертеры.
3. **Гибкость** – при необходимости можно переопределить любые параметры через `WebMvcConfigurer`.
4. **Не отключайте автоконфиг** (`@EnableWebMvc`) без веской причины – это лишает вас всех преимуществ Boot.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Spring boot</summary>
    <p style='font-size: 14px'>

## Spring Boot – кратко о том, что это и какие возможности даёт

| Что | Коротко о том, как это работает |
|-----|---------------------------------|
| **Авто‑конфигурация** | Spring Boot сам «угадывает» нужные настройки и добавляет нужные бины, если в class‑path присутствуют нужные зависимости. |
| **Starter‑пакеты** | `spring-boot-starter-web`, `spring-boot-starter-data-jpa` и т.д. – готовые наборы зависимостей, которые сразу включают всё, что нужно для конкретного функционала. |
| **Встроенный сервер** | Tomcat, Jetty или Undertow запускается внутри приложения, поэтому нужно только `java -jar app.jar`. |
| **Spring Initializr** | Удобный веб‑инструмент, который генерирует готовую структуру проекта с нужными starter‑ами. |
| **Actuator** | Набор эндпоинтов (`/actuator/health`, `/metrics`, `/info`, `/env` и др.) для мониторинга и управления приложением в продакшене. |
| **DevTools** | Перезапуск приложения при изменении файлов, автосвязывание с браузером, live‑reload и т.д. |
| **Конфигурация через `application.yml`/`application.properties`** | Внешние свойства можно переопределять по профилям (`dev`, `prod`), переменным окружения, аргументам командной строки. |
| **Profiles** | Позволяют выбирать набор конфигураций в зависимости от среды. |
| **Security** | `spring-boot-starter-security` даёт готовую аутентификацию/авторизацию (basic, form‑login, OAuth2, JWT). |
| **Data** | `spring-boot-starter-data-jpa`, `spring-boot-starter-data-mongodb`, `spring-boot-starter-data-redis` и т.д. – упрощают работу с БД, кешами, очередями. |
| **WebFlux** | Реактивный стек (Spring MVC + WebFlux) для высокопроизводительных приложений. |
| **Test‑специфические модули** | `spring-boot-starter-test` включает JUnit 5, Mockito, AssertJ, Testcontainers, `@SpringBootTest`, `@WebMvcTest`, `@DataJpaTest` и др. |
| **Spring Cloud** | Расширения для микросервисов: Eureka, Config Server, Gateway, Sleuth, Zipkin, Feign, Stream, Vault и др. |
| **Docker‑поддержка** | `spring-boot-maven-plugin`/`gradle-plugin` умеют создавать jar‑образ, а также готовые Docker‑файлы. |
| **Kubernetes** | Автоматически генерирует `Deployment`, `Service`, `Ingress`, `ConfigMap` и `Secret`. |
| **OpenAPI/Swagger** | `springdoc-openapi` + `springdoc-openapi-ui` создают интерактивную документацию. |
| **Micrometer** | Сбор метрик (Prometheus, Graphite, Datadog, etc.) через Actuator. |
| **Гибкая настройка бинов** | `@ConditionalOnMissingBean`, `@ConditionalOnProperty`, `@AutoConfigurationPackage` и т.д. позволяют тонко контролировать авто‑конфиг. |
| **Плагины** | Maven и Gradle плагины упрощают сборку, тестирование и деплой. |
| **Поддержка Java 17+ и Jakarta EE 9** (Spring Boot 3) – новые версии JDK, новые пакеты. |

---

### Как это выглядит в коде

```bash
# Создаём проект через Spring Initializr
curl https://start.spring.io/starter.zip \\
  -d dependencies=web,data-jpa,actuator \\
  -d javaVersion=17 \\
  -o demo.zip
unzip demo.zip
cd demo
./mvnw spring-boot:run
```

```java
@RestController
public class HelloController {
    @GetMapping(\"/hello\")
    public String hello(@RequestParam String name) {
        return \"Hello, \" + name;
    }
}
```

```yaml
# application.yml
server:
  port: 8080
spring:
  datasource:
    url: jdbc:h2:mem:testdb
  jpa:
    hibernate:
      ddl-auto: update
```

Приложение запускается как `java -jar demo-0.0.1-SNAPSHOT.jar`.  
Actuator доступен по `/actuator/health`, `/actuator/metrics`, и т.д.

---

### Ключевые плюсы Spring Boot

| Плюс | Что он даёт |
|------|-------------|
| **Быстрый старт** | Минимум конфигурации, готовый к работе. |
| **Меньше boilerplate** | Авто‑конфиг, starter‑ы, DevTools. |
| **Гибкая настройка** | Профили, внешние свойства, переопределения. |
| **Масштабируемость** | Поддержка микросервисов, Spring Cloud, Kubernetes. |
| **Мониторинг и диагностика** | Actuator, Micrometer, логирование. |
| **Тестируемость** | Специальные тест‑модули и аннотации. |
| **Сообщество** | Большой набор готовых starter‑ов и расширений. |

---

**Итого:** Spring Boot – это «пакетный» подход к разработке Spring‑приложений, который избавляет от громоздкой конфигурации, ускоряет разработку и облегчает поддержку продакшн‑окружения. Всё, что нужно, – подключить нужный starter и настроить пару свойств. 🚀
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Dependency Injection</summary>
    <p style='font-size: 14px'>

## Что такое Dependency Injection (DI) в Spring

DI – это способ **внедрения зависимостей** (объектов, которые нужны другому объекту) из внешнего источника (контейнера).  
В Spring DI реализуется через **IoC‑контейнер** (ApplicationContext), который создаёт и управляет объектами‑bean’ами.

---

## 1. Как работает Spring DI

| Шаг | Что делает контейнер |
|-----|---------------------|
| 1. | Читает конфигурацию (XML, Java‑конфиги, аннотации). |
| 2. | Создаёт экземпляры bean’ов (по умолчанию `singleton`). |
| 3. | Находит нужные зависимости и «вставляет» их в конструктор, сеттеры или поля. |
| 4. | Выдаёт готовый объект клиенту. |

---

## 2. Способы внедрения

| Способ | Как указать | Пример |
|--------|-------------|--------|
| **Конструктор** | `@Autowired` (или `@Inject`) на конструкторе | `public UserService(UserRepository repo) { … }` |
| **Сеттер** | `@Autowired` на сеттере | `public void setRepo(UserRepository repo) { … }` |
| **Поле** | `@Autowired` на поле | `@Autowired private UserRepository repo;` |
| **Java‑конфиг** | `@Bean`‑метод с параметрами | `@Bean UserService userService(UserRepository repo) { … }` |

> **Совет:** предпочтительнее конструкторное внедрение – гарантирует, что объект полностью инициализирован сразу после создания.

---

## 3. Аннотации, которые используют Spring

| Аннотация | Что делает |
|-----------|------------|
| `@Component` / `@Service` / `@Repository` / `@Controller` | Определяет, что класс – bean, участвует в сканировании пакетов. |
| `@Bean` | Описывает метод, создающий bean в Java‑конфиге. |
| `@Autowired` | Автоматически внедряет зависимость. |
| `@Inject` | JSR‑330, эквивалент `@Autowired`. |
| `@Resource` | По имени (JNDI‑стиль). |
| `@Qualifier` | Указывает конкретный bean, если несколько совпадают. |
| `@Primary` | Выбирает основной bean, если несколько совпадают. |
| `@Lazy` | Отложенное создание bean. |
| `@Profile` | Включает bean только при активном профиле. |
| `@Value` | Внедряет простое значение (строка, число). |
| `@ConfigurationProperties` | Внедряет группу свойств из `application.yml`. |

---

## 4. Скоупы (области жизни bean’ов)

| Скоуп | Что означает |
|-------|--------------|
| `singleton` | Один экземпляр на весь контекст (по умолчанию). |
| `prototype` | Новый экземпляр при каждом запросе. |
| `request` | Один экземпляр на HTTP‑запрос (только в веб‑контексте). |
| `session` | Один экземпляр на HTTP‑сессию. |
| `application` | Один экземпляр на контекст приложения. |
| `websocket` | Один экземпляр на WebSocket‑сессию. |

> В Java‑конфиге: `@Scope(\"prototype\")`.

---

## 5. Управление конфигурацией

| Механизм | Как используется |
|----------|------------------|
| **XML** | `<bean id=\"...\" class=\"...\">` |
| **Java‑конфиг** | `@Configuration` + `@Bean` |
| **Аннотации** | `@ComponentScan` + `@Component` и т.д. |
| **@Import** | Импортирует другие конфиги. |
| **@ComponentScan** | Автоматический поиск компонентов в указанных пакетах. |

---

## 6. Профили и условные bean’ы

| Конструкция | Что делает |
|--------------|------------|
| `@Profile(\"dev\")` | Bean создаётся только при активном профиле `dev`. |
| `@Conditional` | Создаёт bean только при выполнении заданного условия. |

---

## 7. Специфические возможности

| Возможность | Что делает |
|-------------|------------|
| **Коллекции / Map** | `@Autowired List<Service> services;` – Spring внедряет все подходящие bean’ы. |
| **Optional** | `@Autowired Optional<Bean> opt;` – безопасный ввод, если bean может отсутствовать. |
| **Circular dependencies** | Разрешается через setter‑injection или `@Lazy`. |
| **Bean Post Processors** | `BeanPostProcessor`, `BeanFactoryPostProcessor` – позволяют модифицировать bean’ы до/после инициализации. |
| **AOP proxy injection** | Если bean проксируется (например, @Transactional), Spring внедряет прокси, а не сам объект. |
| **Lifecycle callbacks** | `@PostConstruct`, `@PreDestroy`; `init-method`, `destroy-method` в XML/Java‑конфиге. |
| **@Autowired(required=false)** | Не выбрасывает исключение, если зависимость не найдена. |
| **@Lazy** | Отложенное создание bean, даже если он помечен как `singleton`. |
| **@Qualifier + @Primary** | Выбор конкретного bean’а при наличии нескольких кандидатов. |

---

## 8. Как выглядит типичный конфиг

```java
@Configuration
@ComponentScan(\"com.example.app\")
@PropertySource(\"classpath:app.properties\")
public class AppConfig {

    @Bean
    @Scope(\"prototype\")
    public UserService userService(UserRepository repo) {
        return new UserService(repo);
    }

    @Bean
    @Profile(\"dev\")
    public DataSource devDataSource() {
        // настройки для dev
    }

    @Bean
    @Profile(\"prod\")
    public DataSource prodDataSource() {
        // настройки для prod
    }
}
```

---

## Кратко

- **DI** – автоматическое предоставление зависимостей через IoC‑контейнер.
- В Spring это реализовано через аннотации (`@Component`, `@Autowired`, `@Bean` и др.) и конфиги (XML/Java).
- Поддерживаются конструктор, сеттер и поле‑внедрение.
- Есть гибкие механизмы выбора конкретного bean’а (`@Qualifier`, `@Primary`).
- Контейнер управляет скоупами, профилями, жизненным циклом и условными bean’ами.
- Spring умеет работать с коллекциями, Optional, circular dependencies, AOP‑прокси и многими другими нюансами.

Эти особенности делают DI в Spring мощным и гибким инструментом для построения масштабируемых приложений.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Bean</summary>
    <p style='font-size: 14px'>

## Что такое Bean в Spring?

**Bean** – это объект, управляемый контейнером Spring. Контейнер создаёт, конфигурирует и уничтожает его в соответствии с правилами DI (dependency‑injection).

---

## Основные способы объявления

| Способ | Где объявляется | Ключевые аннотации |
|--------|-----------------|--------------------|
| **XML** | `applicationContext.xml` | `<bean id=\"\" class=\"\" …/>` |
| **Java‑конфигурация** | `@Configuration`‑класс | `@Bean`‑метод |
| **Аннотации на классе** | В любом классе | `@Component`, `@Service`, `@Repository`, `@Controller` |
| **Сканирование** | `@ComponentScan` | Автоматически ищет вышеуказанные аннотации |

---

## Ключевые свойства Bean‑defintion

| Свойство | Что оно делает |
|----------|----------------|
| **id / name** | Уникальный идентификатор (можно несколько имен) |
| **class** | Класс, который будет инстанцирован |
| **scope** | `singleton` (по умолчанию), `prototype`, `request`, `session`, `globalSession` (в web‑контексте), а также пользовательские скопы |
| **lazy-init** | Отложенная инициализация (`true`/`false`) |
| **primary** | При конфликте выбора первичный bean (`true`) |
| **depends-on** | Указывает, какие бины должны создаться раньше |
| **init‑method** / `@PostConstruct` | Метод, вызываемый после создания и DI |
| **destroy‑method** / `@PreDestroy` | Метод, вызываемый при закрытии контекста |
| **autowire‑candidate** | Позволяет отключить бина от автосвязывания |
| **autowire‑mode** (`byName`, `byType`, `constructor`) | Как происходит автосвязывание (XML‑специфично) |

---

## Жизненный цикл (Lifecycle)

1. **Создание** (instantiation)
    - Конструктор (или фабричный метод)
2. **Внедрение зависимостей** (dependency injection)
    - По конструктору, полям, сетторам
3. **Инициализация**
    - `@PostConstruct` → `InitializingBean.afterPropertiesSet()` → `@Bean(initMethod=\"…\")`
4. **Использование**
    - Бины живут до конца контекста (если `singleton`) или пока не выбросить (`prototype`)
5. **Уничтожение**
    - `@PreDestroy` → `DisposableBean.destroy()` → `@Bean(destroyMethod=\"…\")`

---

## Инъекции зависимостей

| Тип | Пример | Аннотация |
|-----|--------|-----------|
| Конструктор | `public Foo(Bar bar)` | `@Autowired`, `@Inject` |
| Поле | `@Autowired Bar bar;` | `@Autowired`, `@Inject` |
| Сеттер | `public void setBar(Bar bar)` | `@Autowired`, `@Inject` |

**@Qualifier** – уточняет, какой именно bean использовать, если несколько совпадают.  
**@Primary** – делает bean предпочтительным.  
**@Lazy** – отложенная инициализация конкретного bean.

---

## Специальные аннотации

| Аннотация | Зачем |
|-----------|-------|
| `@Component` | Базовый уровень для всех скоуп‑аннотаций |
| `@Service` | Тот же, что `@Component`, но семантически сервис |
| `@Repository` | Сервис доступа к данным + автоматическое преобразование исключений |
| `@Controller` | MVC‑контроллер |
| `@Configuration` | Класс‑конфигурация, создаёт `@Bean`‑методы |
| `@Import` | Импорт другого конфигурационного класса |
| `@ImportResource` | Импорт XML‑конфигурации |
| `@Conditional` | Создаёт bean только при выполнении условия |
| `@Profile` | Бины, активные при конкретном профиле |
| `@Enable*` | Включает автоконфигурируемые модули (например, `@EnableWebMvc`) |

---

## Плагины жизненного цикла

| Плагины | Что делают |
|---------|-----------|
| **BeanPostProcessor** | Вызывается до и после инициализации бина (например, для проксирования) |
| **BeanFactoryPostProcessor** | Позволяет изменить `BeanDefinition` до создания бинов |
| **ApplicationListener** | Обрабатывает события контекста (например, `ContextRefreshedEvent`) |
| **SmartLifecycle** | Управляет стартом/остановкой бинов с порядком |

---

## Ключевые моменты, которые стоит помнить

- **Singleton** – один объект на контекст (по умолчанию).
- **Prototype** – новый объект каждый раз при запросе.
- **Request/Session** – только в web‑контексте.
- **Lazy** – ускоряет стартап, но может вызвать `LazyInitializationException` в DAO‑слое.
- **CGLIB прокси** – используется, когда бин объявлен как `@Configuration` и методы `@Bean` вызывают друг друга.
- **Bean name vs id** – в XML `id` и `name` могут совпадать, в аннотациях имя берётся из класса (`Foo.class.getSimpleName()` по умолчанию).
- **@Scope(\"prototype\") + @Lazy** – часто используют для тестов и фабрик.
- **Bean overriding** – в последней версии Spring (5.2+) последняя объявленная конфигурация «переопределяет» предыдущую с тем же именем.
- **@ConfigurationProperties** – удобно для маппинга свойств `application.yml`.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Создание Бинов</summary>
    <p style='font-size: 14px'>

## Как создаются бины в Spring

Spring‑контейнер умеет создавать объекты (бины) разными способами.  
Ниже перечислены основные подходы и особенности, которые стоит знать, чтобы быстро ориентироваться в процессе подготовки к собеседованию.

| Способ | Как объявить | Ключевые атрибуты / особенности |
|--------|-------------|---------------------------------|
| **Аннотации‑компоненты** (`@Component`, `@Service`, `@Repository`, `@Controller`) | Поместите аннотацию над классом | *Имя бина* по умолчанию – имя класса с маленькой буквы (`myService`); можно задать явно: `@Component(\"myBean\")`. |
| **Java‑конфигурация** (`@Configuration` + `@Bean`) | Создайте класс‑конфигурацию, пометьте его `@Configuration`, объявите методы `@Bean` | Методы могут иметь `initMethod`, `destroyMethod` либо использовать `@PostConstruct`/`@PreDestroy`. `@Configuration`‑классы проксируются CGLIB‑ом, чтобы гарантировать синглтон‑поведение по умолчанию. |
| **XML‑конфигурация** (другой путь) | `<bean id=\"myBean\" class=\"com.xxx.MyClass\">` | Поддерживается, но сейчас редко используется в новых проектах. |
| **Импорт конфигураций** (`@Import`) | `@Import(OtherConfig.class)` | Позволяет подключить другие `@Configuration`‑классы, даже из сторонних библиотек. |
| **Сканирование пакетов** (`@ComponentScan`) | `@ComponentScan(\"com.xxx\")` | Автоматически ищет классы с аннотациями‑компонентами. Можно исключить/включить фильтры. |
| **Условия и профили** | `@Conditional`, `@Profile`, `@ConditionalOnMissingBean`, `@ConditionalOnProperty` (Spring Boot) | Позволяет создавать бины только при выполнении условий (например, при запуске в dev‑окружении). |
| **Зависимости** | `@Autowired`, `@Inject`, `@Resource` | *Автозаполнение* по типу, имени, конструктору, полю. Атрибут `required` (по умолчанию `true`). |
| **Выбор конкретного бина** | `@Qualifier`, `@Primary` | При наличии нескольких бинов одного типа `@Primary` делает один из них «превосходным». `@Qualifier(\"name\")` уточняет конкретный бин. |
| **Ленивая инициализация** | `@Lazy` | Бин создаётся только при первом запросе. Может применяться к `@Bean` или `@Autowired`. |
| **Скоупы** | `@Scope(\"singleton\" | \"prototype\" | \"request\" | \"session\" | \"globalSession\" | \"application\")` | <details><summary>Кратко о каждом</summary>• **singleton** (по умолчанию) – один экземпляр на контекст.<br>• **prototype** – новый экземпляр при каждом внедрении.<br>• **request/session/globalSession** – для веб‑приложений, хранится в соответствующем контексте.<br>• **application** – хранится в `ServletContext`.<br>• **custom** – можно определить свой скоуп через `CustomScopeConfigurer`. </details> |
| **Пост‑процессоры** | `BeanFactoryPostProcessor`, `BeanPostProcessor` | Позволяют модифицировать `BeanDefinition` до создания бина или выполнять действия до/после инициализации. |
| **Инициализация и уничтожение** | `@PostConstruct`, `@PreDestroy`, `initMethod`, `destroyMethod` | Вызываются автоматически при создании/уничтожении бина. |
| **Бин‑обертки** | `@ConfigurationProperties`, `@Value` | Позволяют привязывать внешние свойства к полям бина. |

---

### Быстрый чек‑лист

1. **Определение бина**
    - `@Component` (или `@Service`, `@Repository`, `@Controller`) + `@ComponentScan`
    - `@Configuration` + `@Bean`

2. **Управление зависимостями**
    - `@Autowired` (конструктор/поле/сеттер)
    - `@Qualifier` / `@Primary`

3. **Контроль жизненного цикла**
    - `@PostConstruct` / `@PreDestroy`
    - `initMethod` / `destroyMethod`

4. **Скоупы**
    - `singleton` (default)
    - `prototype`
    - `request`, `session`, `globalSession` (для веб)

5. **Условия и профили**
    - `@Profile(\"dev\")`
    - `@ConditionalOnMissingBean`
    - `@ConditionalOnProperty`

6. **Ленивая инициализация**
    - `@Lazy`

7. **Пост‑процессоры**
    - `BeanFactoryPostProcessor`
    - `BeanPostProcessor`

---

### Что стоит знать для интервью

- **Какой скоуп по умолчанию?** – `singleton`.
- **Как задать конкретный скоуп?** – `@Scope(\"prototype\")`.
- **Как решить конфликт нескольких бинов одного типа?** – `@Primary` или `@Qualifier`.
- **Как подключить бины только в dev‑окружении?** – `@Profile(\"dev\")` или `@ConditionalOnProperty`.
- **Как гарантировать, что бин создаётся только при первом обращении?** – `@Lazy`.
- **Как задать методы инициализации/уничтожения?** – `@PostConstruct`/`@PreDestroy` или `initMethod/destroyMethod`.
- **Что делает `@Configuration`‑класс?** – создаёт CGLIB‑прокси, чтобы все `@Bean`‑методы возвращали один и тот же экземпляр (singleton) по умолчанию.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Способы объявления бинов</summary>
    <p style='font-size: 14px'>

## Как объявлять бины в Spring

| Способ | Где используется | Как объявляется | Ключевые особенности |
|--------|------------------|-----------------|----------------------|
| **Аннотации‑стереотипы** (`@Component`, `@Service`, `@Repository`, `@Controller`) | Автоматическое сканирование (`@ComponentScan`) | ```java @Service public class MyService { … } ``` | - Сканируются при `@ComponentScan`.<br>- Имя бина по умолчанию – `имяКласса` с маленькой буквы, можно переопределить `@Component(\"customName\")`. |
| **`@Bean` в `@Configuration`** | Явное объявление через Java‑конфиг | ```java @Configuration public class AppConfig { @Bean public MyService myService() { return new MyService(); } } ``` | - Бин создаётся как **singleton** по умолчанию.<br>- Можно задать `@Scope`, `@Lazy`, `@Primary`, `@Qualifier`, `@DependsOn`.<br>- `initMethod`/`destroyMethod` через атрибуты метода `@Bean` или аннотации `@PostConstruct`/`@PreDestroy`. |
| **XML‑конфиг** (`<bean>`) | Старый стиль, но всё ещё актуален | ```xml <bean id=\"myService\" class=\"com.example.MyService\" scope=\"prototype\"/> ``` | - Полная свобода: можно задать все свойства, конструктор, init/destroy‑методы.<br>- Поддержка профилей через `<beans profile=\"dev\">`. |
| **`@Import` / `@ImportResource`** | Встраивание другого конфигурационного кода | ```java @Configuration @Import(OtherConfig.class) public class MainConfig {} ```<br>```java @Configuration @ImportResource(\"classpath:/beans.xml\") public class XmlConfig {} ``` | - Позволяет комбинировать Java‑ и XML‑конфиги.<br>- `@ImportResource` импортирует XML‑файлы в Java‑контекст. |
| **`@ComponentScan`** | Определение пакетов для сканирования | ```java @Configuration @ComponentScan(basePackages=\"com.example\") public class ScanConfig {} ``` | - Можно задать `excludeFilters`, `includeFilters`, `useDefaultFilters=false`.<br>- Сканирует только классы с аннотациями‑стереотипами. |
| **`@Conditional` / `@Profile`** | Условная регистрация | ```java @Configuration public class ConditionalConfig { @Bean @ConditionalOnMissingBean(MyService.class) public MyService myService() { … } } ``` | - `@Profile` активирует бины только при заданном профиле.<br>- `@Conditional` – более гибкая проверка (например, наличие свойства). |
| **`@Lazy`** | Отложенная инициализация | ```java @Component @Lazy public class HeavyService { … } ``` | - Бин создаётся только при первом внедрении. |
| **`@Scope`** | Спецификация жизненного цикла | ```java @Component @Scope(\"prototype\") public class PrototypeBean { … } ``` | - Существуют предопределённые скопы: `singleton`, `prototype`, `request`, `session`, `globalSession`, `application`. <br>- Можно создать собственный скоп. |
| **`@Primary` / `@Qualifier`** | Выбор конкретного бина при внедрении | ```java @Bean @Primary public MyService primary() … ```<br>```java @Autowired @Qualifier(\"secondary\") private MyService service; ``` | - `@Primary` делает бин предпочтительным при отсутствии `@Qualifier`. |
| **`@Bean`‑методы `static`** | Создание бина без инстанцирования конфигурации | ```java @Configuration public class StaticBean { @Bean public static MyService staticService() { return new MyService(); } } ``` | - Полезно, если бин не зависит от других бинов. |
| **`BeanFactoryPostProcessor` / `BeanPostProcessor`** | Программное изменение/обработка бинов | ```java @Component public class MyPostProcessor implements BeanPostProcessor { … } ``` | - Позволяют менять метаданные бина до/после его инициализации. |
| **`@ConfigurationProperties`** | Автоматическое связывание свойств | ```java @Component @ConfigurationProperties(prefix=\"app\") public class AppProps { private String name; … } ``` | - Создаёт бин, который автоматически заполняется из `application.yml`/`application.properties`. |
| **`@Value`** | Внедрение конкретных значений | ```java @Component public class MyBean { @Value(\"${my.prop}\") private String prop; } ``` | - Можно использовать SpEL‑выражения. |

---

### Как всё это работает вместе

1. **Сканирование** (`@ComponentScan`) находит все классы с аннотациями‑стереотипами и регистрирует их как бины.
2. **Java‑конфиг** (`@Configuration` + `@Bean`) добавляет бины, которые не попадают под автосканирование, или переопределяет уже найденные.
3. **XML‑конфиг** можно подключить через `@ImportResource` или использовать как единственный источник конфигурации.
4. **Условные аннотации** (`@Profile`, `@Conditional`) управляют, какие бины загружаются в конкретной среде.
5. **Скоупы** (`@Scope`) определяют, сколько экземпляров бина будет создано.
6. **Пост‑процессоры** (`BeanPostProcessor`, `BeanFactoryPostProcessor`) дают возможность вмешиваться в процесс создания бина.

---

#### Быстрый пример

```java
@Configuration
@ComponentScan(\"com.example\")
@Import(OtherConfig.class)
public class AppConfig {

    @Bean
    @Scope(\"prototype\")
    @Lazy
    @Primary
    public MyService myService() {
        return new MyServiceImpl();
    }

    @Bean(initMethod = \"init\", destroyMethod = \"close\")
    public DataSource dataSource() {
        return new HikariDataSource();
    }
}
```

- `MyService` будет создана **на каждый запрос** (`prototype`), но только при первом внедрении (`lazy`).
- `DataSource` выполнит `init()` после создания и `close()` при закрытии контекста.
- Сканируются все компоненты из пакета `com.example`.
- Подключается дополнительный конфиг `OtherConfig`.

Таким образом, в Spring можно объявлять бины почти любым удобным способом, комбинируя аннотации, XML, Java‑конфиг и условные механизмы. Выбирайте подход, который лучше всего подходит для конкретной задачи и команды.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Скопы в Spring</summary>
    <p style='font-size: 14px'>

## Скоупы (Scopes) в Spring

| Скоуп | Когда создаётся экземпляр | Особенности |
|-------|--------------------------|-------------|
| **`singleton`** (по умолчанию) | Один экземпляр на `ApplicationContext` | Полностью управляется контейнером, потокобезопасен, можно `@Lazy`‑инициализировать. |
| **`prototype`** | Каждый запрос/инъекцию | Контейнер создаёт новый объект, но не отслеживает его дальнейшую жизнь – уничтожить вручную. |
| **`request`** | За один HTTP‑запрос | Доступно только в *web‑aware* контексте (`WebApplicationContext`). |
| **`session`** | За один HTTP‑сессию | Тоже только в веб‑контексте, хранит данные в `HttpSession`. |
| **`application`** | За один `ServletContext` | Практически как `singleton`, но привязан к контексту сервлета. |
| **`websocket`** | За один WebSocket‑сессию | Добавлен в Spring 4.1+; хранит данные в `WebSocketSession`. |
| **`globalSession`** | За один глобальный портлет‑сессию | Используется в портлет‑приложениях (JSR‑286). |
| **`custom`** | Пользовательский | Реализуется через интерфейс `org.springframework.beans.factory.config.Scope` и регистрируется в `ScopeConfigurer`. |

### Как задать скоуп
* **XML**: `<bean id=\"...\" class=\"...\" scope=\"request\"/>`
* **Java‑конфиг**:
  ```java
  @Bean
  @Scope(\"prototype\")          // или @Scope(\"request\") и т.д.
  public MyBean myBean() { … }
  ```
* **Анотации‑сокращения** (Spring 4+):
  ```java
  @Component
  @RequestScope      // эквивалентно @Scope(\"request\")
  public class MyController { … }
  ```

### Ключевые нюансы

| Пункт | Что важно знать |
|-------|-----------------|
| **`singleton` vs. `prototype`** | `singleton` – один экземпляр, потокобезопасен, но может быть `@Lazy`. `prototype` – новый объект каждый раз, но контейнер не \"помнит\" его после выдачи. |
| **Scoped proxies** | Если `singleton`‑bean внедряет `request`/`session`‑bean, нужно использовать `@Scope(proxyMode = ScopedProxyMode.TARGET_CLASS)` или `ScopedProxyMode.INTERFACES`, иначе будет ошибка «no scope bound to bean». |
| **Веб‑скоупы** | `request`, `session`, `application`, `websocket`, `globalSession` работают только в `WebApplicationContext`. |
| **Custom scopes** | Полностью контролируются вами: можно хранить данные в кэше, базе и т.п. |
| **`@Lazy`** | Можно применять к любому скоупу, чтобы отложить создание экземпляра до первого обращения. |
| **`@Primary` и `@Qualifier`** | При наличии нескольких бинов одного типа с разными скоупами используйте `@Primary`/`@Qualifier` для выбора. |

> **Итого**: Spring предоставляет семь стандартных скоупов (singleton, prototype, request, session, application, websocket, globalSession) и позволяет добавить свои через интерфейс `Scope`. Выбирайте скоуп в зависимости от того, как долго должен жить объект и в каком контексте он используется.
</p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Как реализуется DI</summary>
    <p style='font-size: 14px'>

## Как реализуется DI в Spring

Spring — это контейнер, который **создаёт, настраивает и управляет** объектами (bean‑ами).  
DI (Dependency Injection) в Spring реализуется через **загрузку описаний bean‑ов** и **автоматическое связывание** зависимостей.

---

### 1. Как создаётся контейнер

| Способ | Что загружается | Как создаётся |
|--------|-----------------|---------------|
| **XML** | `<bean id=\"foo\" class=\"com.Foo\">...</bean>` | `ClassPathXmlApplicationContext` |
| **Java‑конфиг** | `@Configuration` + `@Bean` | `AnnotationConfigApplicationContext` |
| **Аннотации** | `@Component`, `@Service`, `@Repository`, `@Controller` + `@ComponentScan` | `AnnotationConfigApplicationContext` (или Spring Boot) |

Контейнер читает описания, создаёт **BeanDefinition** и хранит их в `BeanFactory`.

---

### 2. Самые важные способы инъекции

| Способ | Где ставится аннотация | Что происходит |
|--------|-----------------------|----------------|
| **Конструктор** | `@Autowired` (или просто конструктор в `@Configuration`/`@Component`) | Зависимости передаются в конструктор. **Рекомендуемый способ** – гарантирует неизменяемость. |
| **Сеттер** | `@Autowired` на методе‑сеттере | Spring вызывает setter после создания bean‑а. |
| **Поле** | `@Autowired` на поле | Spring использует рефлексию, чтобы установить поле. **Не рекомендуется** (трудно тестировать). |
| **Метод** | `@Autowired` на произвольном методе | Позволяет выполнить дополнительную логику после инъекции. |

> **Важно:** в Spring 4+ конструктор‑инъекция считается лучшей практикой.

---

### 3. Как выбирается конкретный bean

| Механизм | Что делает | Где используется |
|----------|------------|-------------------|
| **По типу** | По умолчанию (`@Autowired`) – ищет bean‑ы того же типа. | Любой `@Autowired`. |
| **@Qualifier** | Указывает имя/метку bean‑а. | `@Autowired @Qualifier(\"myFoo\")`. |
| **@Primary** | Делает bean‑а «превосходным» при конфликте. | `@Primary` или `@Bean @Primary`. |
| **@Resource** | JSR‑250, ищет по имени, потом по типу. | `@Resource(name=\"myFoo\")`. |
| **@Inject** | JSR‑330, как `@Autowired`. | `@Inject`. |
| **Optional / @Nullable** | Позволяет не требовать bean‑а. | `@Autowired(required=false)` или `Optional<...>`. |
| **Коллекции** | `List<T>`, `Set<T>`, `Map<String,T>` – собирает все bean‑ы нужного типа. | Автосборка коллекций. |
| **Lazy** | Отложенная инициализация. | `@Lazy` на bean‑е или `@Lazy` в `@Autowired`. |
| **Scoped Proxies** | Для `request`, `session`, `prototype`‑bean‑ов. | `@Scope(\"request\", proxyMode=ScopedProxyMode.TARGET_CLASS)` |

---

### 4. Скоупы bean‑ов

| Скоуп | Что означает | Где можно использовать |
|-------|--------------|------------------------|
| `singleton` | Один экземпляр в контексте. | По умолчанию. |
| `prototype` | Новый экземпляр при каждом `getBean()`. | Для transient‑объектов. |
| `request` | Один экземпляр на HTTP‑запрос (Spring MVC). | В веб‑приложениях. |
| `session` | Один экземпляр на HTTP‑сессию. | В веб‑приложениях. |
| `application` | Один экземпляр на `ServletContext`. | В веб‑приложениях. |
| `websocket` | Один экземпляр на WebSocket‑сессию. | В WebSocket‑приложениях. |

> Для `request`/`session` требуется `proxyMode` (JDK/CGlib), иначе Spring не сможет инъектировать их в singleton‑bean‑ы.

---

### 5. Лайф‑циклы и инициализация

| Механизм | Когда вызывается |
|----------|-----------------|
| `@PostConstruct` | После создания bean‑а и инъекции зависимостей. |
| `@PreDestroy` | Перед уничтожением bean‑а (при закрытии контекста). |
| `@Bean(initMethod=…, destroyMethod=…)` | В Java‑конфиге, можно указать методы‑инициализации/уничтожения. |
| `InitializingBean.afterPropertiesSet()` | Интерфейс Spring. |
| `DisposableBean.destroy()` | Интерфейс Spring. |

---

### 6. Конфигурационные особенности

| Фича | Как использовать |
|------|-------------------|
| **@Configuration** | Создаёт CGLIB‑прокси, обеспечивает одиночные bean‑ы. |
| **@ComponentScan** | Поиск компонентов в заданном пакете. |
| **@Import** | Импорт других конфигураций. |
| **@ImportResource** | Подключение XML‑конфигурации. |
| **@Enable*`** | Включает авто‑конфигурацию (например, `@EnableJpaRepositories`). |
| **@Profile** | Ограничивает создание bean‑ов конкретным профилем. |
| **@Conditional** | Условное создание bean‑а (например, `@ConditionalOnMissingBean`). |
| **@ConfigurationProperties** | Связывание свойств из `application.yml`/`properties`. |
| **@Value(\"${...}\")** | Внедрение конкретного свойства. |
| **@Lazy** | Отложенная инициализация bean‑а. |
| **@Scope** | Устанавливает скоуп и proxy‑режим. |

---

### 7. Инъекция в Spring Boot

Spring Boot упрощает DI:

- Автосканирование по `@SpringBootApplication` (эквивалент `@Configuration`, `@ComponentScan`, `@EnableAutoConfiguration`).
- `@Autowired` в `@Service`, `@RestController` и т.д. автоматически работают.
- `application.yml`/`application.properties` подключаются через `@ConfigurationProperties` и `@Value`.
- Автоконфигурация (`@ConditionalOnMissingBean`, `@ConditionalOnProperty`) создаёт bean‑ы только при отсутствии пользовательских.

---

## Краткое резюме

1. **Контейнер** читает описания bean‑ов (XML, Java, аннотации).
2. **DI** происходит при создании bean‑ов: конструктор → setter → поле.
3. **Выбор bean‑а** определяется типом, `@Qualifier`, `@Primary`, `@Resource` и др.
4. **Скоупы** (`singleton`, `prototype`, `request`, `session`, `application`, `websocket`).
5. **Лайф‑циклы** (`@PostConstruct`, `@PreDestroy`, `@Bean(initMethod, destroyMethod)`).
6. **Конфиг**: `@Configuration`, `@ComponentScan`, `@Import`, `@Profile`, `@Conditional`, `@ConfigurationProperties`, `@Value`.
7. **Spring Boot** добавляет авто‑конфигурацию и упрощает настройку.

Таким образом, DI в Spring — это гибкий механизм, позволяющий полностью отделить создание и настройку объектов от их использования.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Инициализация и уничтожение bean</summary>
    <p style='font-size: 14px'>

## Инициализация и уничтожение `bean` в Spring
Ниже перечислены все способы, которые можно использовать в реальном проекте.  
Старайтесь комбинировать несколько подходов – это делает код более гибким и читаемым.

| Что | Как задать | Короткое описание |
|-----|------------|-------------------|
| **Анотации** | `@PostConstruct`, `@PreDestroy` | Выполняется после создания/до уничтожения bean (только для singleton). |
| | `@Lazy` | Отложенная инициализация (bean создаётся только при первом запросе). |
| | `@DependsOn` | Указывает порядок инициализации (bean A создаётся после bean B). |
| | `@Scope(\"prototype\")` | Прототипный bean не уничтожается автоматически. |
| | `@Scope` с `proxyMode` | Создаёт прокси‑обёртку для scoped‑bean в singleton‑контексте. |
| | `@Primary`, `@Qualifier` | Выбор конкретного bean, если их несколько. |
| | `@ConfigurationProperties` + `@EnableConfigurationProperties` | Инициализация bean из `application.yml`/`properties`. |
| **Интерфейсы** | `InitializingBean` (`afterPropertiesSet()`) | Эквивалент `@PostConstruct`. |
| | `DisposableBean` (`destroy()`) | Эквивалент `@PreDestroy`. |
| | `SmartLifecycle` | Позволяет управлять стартом/остановкой компонентов (методы `start()`, `stop()`). |
| **XML‑конфигурация** | `<bean init-method=\"init\" destroy-method=\"close\" />` | Указываем имена методов вручную. |
| **Java‑конфигурация** | `@Configuration` → `@Bean(initMethod=\"init\", destroyMethod=\"close\")` | Аналогично XML, но в Java. |
| | `@Bean(initMethod=\"init\")` | Если метод называется `init`. |
| | `@Bean(destroyMethod=\"\")` | Отключаем вызов destroy‑метода (нужно, если метод не существует). |
| **Пост‑процессоры** | `BeanPostProcessor` | Добавляет логику до/после инициализации каждого bean. |
| | `BeanFactoryPostProcessor` | Изменяет определения bean до их создания. |
| **События контекста** | `ApplicationListener<ContextRefreshedEvent>` | Действие после полной инициализации контекста. |
| | `ApplicationListener<ContextClosedEvent>` | Действие при закрытии контекста. |
| | `@EventListener(ContextRefreshedEvent)` | Анотационный вариант. |
| | `@EventListener(ContextClosedEvent)` | Анотационный вариант. |
| **Контекст** | `context.close()` | Явно закрывает контекст (вызывается `destroy()` всех singleton‑beans). |
| | `SpringApplication.run()` (Boot) | Само добавляет shutdown‑hook, поэтому контекст закрывается при `SIGINT`. |

---

### Пример 1 – аннотации

```java
@Component
@Lazy
public class MyService {

    @PostConstruct
    public void init() {
        System.out.println(\"MyService создан\");
    }

    @PreDestroy
    public void cleanup() {
        System.out.println(\"MyService уничтожается\");
    }
}
```

### Пример 2 – Java‑конфигурация

```java
@Configuration
public class AppConfig {

    @Bean(initMethod = \"start\", destroyMethod = \"stop\")
    public Scheduler scheduler() {
        return new Scheduler();
    }
}
```

### Пример 3 – XML

```xml
<bean id=\"repo\"
      class=\"com.example.Repo\"
      init-method=\"init\"
      destroy-method=\"cleanup\"/>
```

### Пример 4 – `SmartLifecycle`

```java
@Component
public class MyLifecycle implements SmartLifecycle {

    private boolean running = false;

    @Override
    public void start() {
        System.out.println(\"Start\");
        running = true;
    }

    @Override
    public void stop() {
        System.out.println(\"Stop\");
        running = false;
    }

    @Override
    public boolean isRunning() {
        return running;
    }
}
```

---

## Ключевые нюансы

| Нюанс | Что нужно помнить |
|-------|-------------------|
| **Singleton vs Prototype** | `destroy` вызывается только для singleton‑beans. Прототипы уничтожаются вручную (`BeanFactory.destroyBean(...)`). |
| **Порядок инициализации** | `@DependsOn` + `@Lazy` + `@PostConstruct` → можно задать точный порядок. |
| **Прокси‑обёртки** | При `@Scope(\"request\")` в singleton‑контексте создаётся прокси. |
| **Shutdown‑hook** | В Spring Boot контекст автоматически закрывается при завершении JVM. В обычном Spring добавьте `context.registerShutdownHook()`. |
| **Тесты** | Для unit‑тестов используйте `@SpringBootTest` или `@ExtendWith(SpringExtension.class)` – тогда `@PreDestroy` будет вызываться. |
| **Ошибка init/destroy** | Если метод не найден, Spring бросит `BeanCreationException`. Чтобы избежать, можно задать `destroy-method=\"\"`. |
| **Внешние ресурсы** | При работе с `DataSource`, `EntityManagerFactory` и т.п. используйте `close()` как `destroy-method`. |
| **Многопоточность** | `SmartLifecycle` позволяет управлять порядком старта/остановки в многопоточных приложениях. |

---

### Итог

- **Аннотации** (`@PostConstruct`, `@PreDestroy`) + **интерфейсы** (`InitializingBean`, `DisposableBean`) – быстрый способ добавить код инициализации/уничтожения.
- **XML/Java‑конфигурация** (`init-method`, `destroy-method`) – нужен, если нельзя изменить класс (например, сторонний).
- **Пост‑процессоры** и **события** – позволяют централизованно управлять жизненным циклом всех bean.
- **`SmartLifecycle`** – для компонентов, которые нужно явно стартовать/остановить (например, серверы, очереди).
- **Параметры `@Scope`, `@Lazy`, `@DependsOn`** – дают контроль над порядком и временем создания.

Используйте комбинацию этих подходов в зависимости от конкретных требований вашего проекта.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>BeanPostProcessor и BeanFactoryPostProcessor</summary>
    <p style='font-size: 14px'>

## Кратко о **BeanFactoryPostProcessor** и **BeanPostProcessor**

| Что | Когда вызывается | Что может делать |
|-----|------------------|------------------|
| **BeanFactoryPostProcessor** | После того как контейнер (BeanFactory) загрузил все **bean‑definitions**, но **до** создания любых bean‑ов | Модифицировать *описания* bean‑ов (параметры, свойства, зависимостями), добавить новые bean‑ы, задать скрипты‑плейсхолдеры, настроить окружение. Он **не видит** уже созданные экземпляры. |
| **BeanPostProcessor** | После **создания** каждого bean‑а, но **перед** вызовом `@PostConstruct`/`afterPropertiesSet` и **после** – после этих инициализаций | Модифицировать *сам экземпляр* bean‑а, оборачивать его прокси‑ом, внедрять зависимости, менять его тип, логировать и т.п. |

---

## BeanFactoryPostProcessor

```java
public interface BeanFactoryPostProcessor {
    void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException;
}
```

### Ключевые особенности

| Особенность | Что значит |
|-------------|------------|
| **Выполняется один раз** | После загрузки всех bean‑definitions. |
| **Работает с `BeanDefinition`** | Можно менять свойства, типы, ссылки, добавлять новые bean‑ы. |
| **Не видит реальные объекты** | Нет доступа к уже созданным экземплярам. |
| **Может регистрировать другие `BeanPostProcessor`** | Позволяет динамически добавлять обработчики в контейнер. |
| **Поддержка `PriorityOrdered` и `Ordered`** | Позволяет задать порядок выполнения. |
| **Расширение: `BeanDefinitionRegistryPostProcessor`** | Позволяет модифицировать *реестр* bean‑ов (добавлять/удалять bean‑ы) до того, как будет создан реестр. |
| **Примеры использования** | `PropertySourcesPlaceholderConfigurer`, `AnnotationConfigUtils`, `CommonAnnotationBeanPostProcessor`, настройка `@Value`‑подстановки. |

---

## BeanPostProcessor

```java
public interface BeanPostProcessor {
    Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException;
    Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException;
}
```

### Ключевые особенности

| Особенность | Что значит |
|-------------|------------|
| **Выполняется для каждого bean‑а** | После его создания, но до `afterPropertiesSet()` и `@PostConstruct`. |
| **Может изменить bean‑объект** | Можно вернуть новый объект (например, прокси), изменить свойства и т.д. |
| **`postProcessBeforeInitialization`** | Делает изменения **до** инициализации. |
| **`postProcessAfterInitialization`** | Делает изменения **после** инициализации. |
| **Встроенные реализации** | `CommonAnnotationBeanPostProcessor`, `AutowiredAnnotationBeanPostProcessor`, `PersistenceAnnotationBeanPostProcessor`, `AnnotationAwareAspectJAutoProxyCreator` (для AOP). |
| **Может быть использован для AOP** | Создает прокси‑объекты, добавляет аспекты. |
| **Поддержка `PriorityOrdered` и `Ordered`** | Позволяет задать порядок применения. |
| **Можно вернуть `null`** | Если хотите, чтобы bean‑а не создавалось (не рекомендуется). |
| **Примеры** | Создание прокси для логирования, добавление `@Transactional`‑обёртки, изменение свойств после конструирования. |

---

## Как они взаимодействуют

1. **Контейнер создаёт `BeanFactory`.**
2. **`BeanFactoryPostProcessor`** (и `BeanDefinitionRegistryPostProcessor`) вызываются, чтобы изменить *описания* bean‑ов.
3. **Контейнер создаёт bean‑ы** по изменённым описаниям.
4. Для каждого bean‑а вызывается **`BeanPostProcessor`**:
    - `postProcessBeforeInitialization`
    - `afterPropertiesSet()` / `@PostConstruct`
    - `postProcessAfterInitialization`

---

## Быстрый пример

```java
// 1. BeanFactoryPostProcessor изменяет свойство
public class MyBeanFactoryPostProcessor implements BeanFactoryPostProcessor {
    @Override
    public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) {
        BeanDefinition bd = beanFactory.getBeanDefinition(\"myService\");
        bd.getPropertyValues().add(\"name\", \"Post-Processed\");
    }
}

// 2. BeanPostProcessor оборачивает bean в логирующий прокси
public class LoggingBeanPostProcessor implements BeanPostProcessor {
    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) {
        if (bean instanceof MyService) {
            return Proxy.newProxyInstance(
                bean.getClass().getClassLoader(),
                new Class[]{MyService.class},
                (proxy, method, args) -> {
                    System.out.println(\"Before \" + method.getName());
                    Object result = method.invoke(bean, args);
                    System.out.println(\"After \" + method.getName());
                    return result;
                });
        }
        return bean;
    }
}
```

---

## Итог

| Что | Когда | Что можно изменить |
|-----|-------|--------------------|
| **BeanFactoryPostProcessor** | После загрузки описаний, до создания bean‑ов | Описание bean‑ов, свойства, типы, регистрировать новые bean‑ы |
| **BeanPostProcessor** | После создания каждого bean‑а | Сам экземпляр bean‑а, обёртка, прокси, дополнительные свойства |

Эти два механизма позволяют гибко конфигурировать контейнер Spring как на уровне *описаний*, так и на уровне *экземпляров*.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Конкретный bean будет выбран при автосвязывании</summary>
    <p style='font-size: 14px'>

## Как гарантировать, что будет выбран именно нужный `bean` в Spring

| Как выбрать | Как объявить — пример | Что даёт |
|-------------|-----------------------|----------|
| **По имени** | ```java<br>@Component(\"myService\")<br>public class MyService { … }<br>```<br>или `<bean id=\"myService\" …>` | Имя `myService` становится единственным критерием выбора. |
| **По типу + @Primary** | ```java<br>@Service<br>@Primary<br>public class DefaultService implements Service { … }<br>``` | Если несколько `Service`‑ов, Spring берёт `DefaultService`. |
| **По типу + @Qualifier** | ```java<br>@Service(\"special\")<br>public class SpecialService implements Service { … }<br>```<br>И в точке внедрения: <br>```java<br>@Autowired<br>@Qualifier(\"special\")<br>private Service service;<br>``` | Вы явно указываете, какой именно bean нужен. |
| **По имени через @Resource** | ```java<br>@Resource(name=\"myService\")<br>private Service service;<br>``` | Имена берутся из `id`/`name` bean‑а. |
| **По имени через @Inject/@Named** | ```java<br>@Inject<br>@Named(\"myService\")<br>private Service service;<br>``` | Аналогично `@Resource`, но JSR‑330. |
| **По условию** (`@Conditional`, `@Profile`, `@ConditionalOnMissingBean`, `@ConditionalOnBean`) | ```java<br>@Configuration<br>public class AppConfig {<br>  @Bean<br>  @ConditionalOnMissingBean(Service.class)<br>  public Service defaultService() { … }<br>}<br>``` | Бин создаётся только при выполнении условия. |
| **По профилю** | ```java<br>@Service<br>@Profile(\"dev\")<br>public class DevService implements Service { … }<br>``` | Бин активен только в профиле `dev`. |
| **По ленивой инициализации** | ```java<br>@Lazy<br>public class HeavyService { … }<br>``` | Бин создаётся только при первом запросе. |
| **По скоупу** | ```java<br>@Scope(\"prototype\")<br>public class PrototypeService { … }<br>``` | Каждый запрос получает новый экземпляр. |
| **По порядку** (`@Order`) | ```java<br>@Component<br>@Order(1)<br>public class FirstService implements Service { … }<br>``` | При множестве `Service` порядок внедрения можно задать. |

### Что делать, если в контейнере несколько bean‑ов того же типа?

1. **`@Primary`** – помечает «доменный» bean, который будет выбран по умолчанию.
2. **`@Qualifier`** – либо указываете имя (`@Qualifier(\"myService\")`), либо создаёте собственный аннотационный квалификатор (`@MyQualifier`).
3. **`@Resource(name=\"…\")`** – выбирает bean по имени.
4. **`@Inject/@Named`** – альтернативы `@Resource`.

> **Важно**: если вы объявили несколько bean‑ов того же типа и не указали ни `@Primary`, ни `@Qualifier`, Spring бросит `NoUniqueBeanDefinitionException`.

### Как задать имя bean‑а

| Где задаётся | Как выглядит |
|--------------|--------------|
| `@Component`, `@Service`, `@Repository`, `@Controller` | `@Service(\"myService\")` |
| `@Bean` | `@Bean(name = \"myService\")` |
| XML | `<bean id=\"myService\" …>` |
| Метод‑фабрика | `public MyService myService() { … }` – имя по умолчанию `myService` |

### Минимальный набор правил для гарантированного выбора

1. **Уникальное имя** – задайте уникальный `id`/`name` и используйте `@Resource` или `@Qualifier`.
2. **`@Primary`** – если имя не нужно, но есть несколько реализаций, пометьте одну как `@Primary`.
3. **Условные аннотации** – если бин должен существовать только в определённой среде (`@Profile`, `@Conditional`).
4. **Не забудьте про `@Autowired(required = false)`** – если бин может отсутствовать, но вы не хотите ошибки.

> **Пример**
> ```java
> @Service(\"paymentService\")
> public class PaymentServiceImpl implements PaymentService { … }
> 
> @Component
> public class OrderProcessor {
>     @Autowired
>     @Qualifier(\"paymentService\")
>     private PaymentService paymentService;
> }
> ```

Таким образом, сочетание имени, `@Primary`, `@Qualifier`, профилей и условных аннотаций даёт полный контроль над тем, какой именно bean будет внедрён в нужный момент.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Включить/выключить bean в зависимости от профиля</summary>
    <p style='font-size: 14px'>

## Как включать/выключать `@Bean` по профилю в Spring

| Что | Как сделать | Коротко |
|-----|-------------|---------|
| **Аннотация `@Profile`** | `@Profile(\"dev\")` на классе/методе | Бин создаётся только если активен профиль *dev* |
| **Несколько профилей** | `@Profile({\"dev\",\"test\"})` | Бин создаётся, если активен *dev* **или** *test* |
| **Исключение профиля** | `@Profile(\"!prod\")` | Бин создаётся в **всех** профилях, кроме *prod* |
| **Профиль по умолчанию** | `@Profile(\"default\")` (или без `@Profile`) | Бин создаётся, когда не задан ни один профиль |
| **Профиль на уровне конфигурации** | `@Configuration @Profile(\"dev\")` | Весь `@Configuration` загружается только в *dev* |
| **Профиль на уровне компонента** | `@Component @Profile(\"test\")` | Компонент/сервис/репозиторий включается только в *test* |
| **Профиль на `@Bean`‑методе** | `@Bean @Profile(\"dev\")` | Тот же принцип, но в `@Configuration` |
| **XML‑конфигурация** | `<beans profile=\"dev\"> … </beans>` | Тот же механизм, но через XML |
| **Профили в `application.yml`/`properties`** | ```yaml<br>spring:<br>  profiles:<br>    active: dev<br>``` | Можно задать через файл конфигурации |
| **Профили из переменных среды** | `SPRING_PROFILES_ACTIVE=dev` | Переменная окружения |
| **Профили из командной строки** | `--spring.profiles.active=dev` | При запуске приложения |
| **Профили в коде** | `SpringApplication.setAdditionalProfiles(\"dev\")` | Программно задать дополнительные профили |

---

## Дополнительные условные аннотации (не только `@Profile`)

| Аннотация | Что делает | Когда удобно |
|-----------|------------|-------------|
| `@ConditionalOnProperty` | Бин создаётся, если задана (или не задана) конкретная система/приложения‑свойство | Конфигурируемость через `application.yml` |
| `@ConditionalOnExpression` | Позволяет использовать SpEL‑выражение | Сложные условия (например, `@ConditionalOnExpression(\"${feature.enabled:true}\")`) |
| `@ConditionalOnClass` | Бин создаётся, если в classpath присутствует указанный класс | Подключение к сторонним библиотекам |
| `@ConditionalOnMissingClass` | Бин создаётся, если класс **отсутствует** | Вариант «fallback» |
| `@ConditionalOnBean` | Создание только если задан другой бин | Зависимости между бинами |
| `@ConditionalOnMissingBean` | Создание только если бин **не существует** | Переопределение |
| `@ConditionalOnResource` | Бин создаётся, если существует ресурс (файл, URL) | Конфигурируемость через файлы |
| `@ConditionalOnWebApplication` / `@ConditionalOnNotWebApplication` | Условие для веб‑или не‑веб‑контекста | Web‑пакет vs. CLI |
| `@ConditionalOnSpringBootFeature` | Условие для включения/выключения фич Spring Boot | Редко, но полезно в автоконфигурации |

> **Совет:** используйте `@Profile` для разделения сред (dev, test, prod), а остальные `@Conditional…` – для более тонкой настройки внутри одной среды.

---

## Пример

```java
@Configuration
public class AppConfig {

    @Bean
    @Profile(\"dev\")
    public DataSource devDataSource() {
        return new EmbeddedDatabaseBuilder()
                .setType(EmbeddedDatabaseType.H2)
                .build();
    }

    @Bean
    @Profile(\"prod\")
    public DataSource prodDataSource() {
        return DataSourceBuilder.create()
                .url(\"jdbc:postgresql://prod-db:5432/app\")
                .username(\"app\")
                .password(\"secret\")
                .build();
    }

    @Bean
    @ConditionalOnProperty(name = \"feature.audit.enabled\", havingValue = \"true\")
    public AuditService auditService() {
        return new AuditServiceImpl();
    }
}
```

- `devDataSource` будет создан только в профиле `dev`.
- `prodDataSource` – только в `prod`.
- `auditService` включится, если в `application.yml` прописано `feature.audit.enabled=true`.

---

## Итог

1. **`@Profile`** – самый простой способ включать/выключать бины по среде.
2. **Профили задаются** в `application.yml`, переменных среды, командной строки или программно.
3. **Дополнительно** можно использовать `@Conditional…` для более гибкой логики.
4. **Не забывайте**: если профиль не активен, бин просто не создаётся – никаких ошибок, если он не нужен.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>lazy‑инициализация</summary>
    <p style='font-size: 14px'>

## Что такое lazy‑инициализация в Spring?

Lazy‑инициализация (отложенная инициализация) — это механизм, при котором Spring‑контейнер **не создаёт** бину сразу при старте приложения, а создаёт его **только в момент первого запроса** к нему.

> **Сравнение**  
> *Eager* (по умолчанию) – все singleton‑бины создаются в момент загрузки контекста.  
> *Lazy* – создаются только при первом вызове.

---

## Где применять

| Когда использовать | Зачем | Как реализовать |
|--------------------|-------|-----------------|
| **Большой набор бинов** | Уменьшить время старта и потребление памяти | `@Lazy` на классе/методе/инъекции, `lazy-init=\"true\"` в XML |
| **Тяжёлые ресурсы** (бД, сеть, внешние сервисы) | Не блокировать старт при отсутствии сети/БД | `@Lazy` на бине, `spring.main.lazy-initialization=true` в Boot |
| **Периодически используемые компоненты** | Не создавать, если они не нужны | `@Lazy` + условный вызов |
| **Циклические зависимости** | Разорвать цикл, делая один из бинов «посредником» | `@Lazy` на одном из зависимых бинов |
| **Тесты** | Избежать создания ненужных бинов в unit‑тестах | `@Lazy` + `@MockBean`/`@TestConfiguration` |
| **Микросервис с несколькими профилями** | Задержать инициализацию бинов, относящихся к неактивным профилям | `@Lazy` + `@Profile` |

> **Не стоит** использовать lazy‑инициализацию в тех случаях, когда бин нужен сразу после старта (например, слушатели событий, планировщики, сервисы, которым нужна инициализация конфигурации).

---

## Как включить

### 1. Аннотации

| Аннотация | Что делает | Пример |
|-----------|------------|--------|
| `@Lazy` (на классе, методе `@Bean`, `@Component`, `@Configuration`) | Делает бин ленивым | `@Lazy @Component class EmailSender { … }` |
| `@Lazy` (на `@Autowired`) | Инъекция через прокси, объект создаётся только при первом использовании | `@Autowired @Lazy private EmailSender sender;` |
| `@Lazy` (на `@Import` / `@Configuration` в `@Configuration`) | Ленивая загрузка другого конфигурационного класса | `@Import(@Lazy UserConfig.class)` |
| `@Lazy` (на `@ComponentScan` в XML) | Ленивая сканировка всех найденных компонентов | `<context:component-scan base-package=\"com.app\" lazy-init=\"true\"/>` |

### 2. XML

```xml
<bean id=\"heavyBean\" class=\"com.app.Heavy\" lazy-init=\"true\"/>
```

### 3. Spring Boot

```properties
# Включаем lazy‑инициализацию для всех singleton‑бинов
spring.main.lazy-initialization=true
```

> **Важно:** при использовании `spring.main.lazy-initialization=true` *все* singleton‑бины становятся ленивыми, но можно переопределить конкретные бины через `@Lazy`/`lazy-init=\"true\"`.

---

## Особенности и нюансы

| Пункт | Что важно знать |
|-------|-----------------|
| **Промежуточные прокси** | При `@Lazy` Spring создаёт прокси‑объект, который «всплывает» в реальный бин только при первом вызове. Это работает с AOP‑прокси, но может скрыть ошибки инициализации. |
| **Циклические зависимости** | Если два singleton‑бины ссылаются друг на друга, делаем один из них `@Lazy`. Spring создаст прокси‑объект, а реальный бин будет создан позже, разорвав цикл. |
| **Профили и условия** | `@Lazy` не отменяет работу `@Profile` или `@Conditional`. Если профиль не активен, бин всё равно не создаётся, независимо от `@Lazy`. |
| **Производительность** | Первое обращение к ленивому бину может занять больше времени, чем при eager. Поэтому используйте lazy только там, где это действительно экономит ресурсы. |
| **Тесты** | В unit‑тестах часто полезно отключать lazy‑инициализацию (`@SpringBootTest(lazyInitialization = false)`) чтобы убедиться, что все зависимости корректно создаются. |
| **Singleton vs Prototype** | Только singleton‑бины могут быть лениво инициализированы по умолчанию. Прототипные бины создаются каждый раз, когда они запрашиваются. |
| **Lazy в @Configuration** | Делает сам класс‑конфигурацию ленивой, т.е. методы `@Bean` не вызываются до первого запроса. Это удобно, если конфигурация тяжёлая. |
| **Параметры `spring.main.lazy-initialization`** | Включает lazy‑инициализацию глобально. Если нужно сделать только часть бинов ленивыми, используйте аннотации. |
| **Контекстные свойства** | В XML можно задать `lazy-init=\"true\"` в `<context:component-scan>`, но это применится ко всем найденным компонентам. |

---

## Примеры кода

```java
@Configuration
public class AppConfig {

    @Bean
    @Lazy          // лениво создаём этот бин
    public HeavyService heavyService() {
        return new HeavyService();
    }

    @Bean
    public SimpleService simpleService(@Lazy HeavyService heavy) {
        return new SimpleService(heavy); // heavy будет создан только при первом вызове
    }
}
```

```java
@Component
@Lazy
public class EmailSender {
    public void send(String msg) { ... }
}

@Service
public class NotificationService {
    @Autowired
    @Lazy
    private EmailSender emailSender;  // прокси, создаётся при первом вызове send()
}
```

```xml
<context:component-scan base-package=\"com.app\" lazy-init=\"true\"/>
```

---

## Итоги

* **Lazy‑инициализация** — отложенный запуск бина до первого обращения.
* Используется, когда хочется **уменьшить время старта** и **потребление памяти**.
* Можно включить через аннотации `@Lazy`, XML‑атрибут `lazy-init`, или глобально в Spring Boot (`spring.main.lazy-initialization=true`).
* Обязательно учитывайте **производительность при первом вызове**, **циклические зависимости**, **прокси‑поведение** и **контекстные профили**.

Соблюдая эти правила, вы сможете оптимизировать старты и управлять ресурсами вашего Spring‑приложения.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Пользовательский скоп</summary>
    <p style='font-size: 14px'>

## Как реализовать пользовательский скоп в Spring

Ниже – «скелет» реализации, но в нём упомянуты все ключевые моменты, которые стоит знать на интервью.

| Что | Как реализовать | Что важно помнить |
|-----|-----------------|-------------------|
| **1. Интерфейс Scope** | `org.springframework.beans.factory.config.Scope` | 5 методов: `getObject`, `remove`, `registerDestructionCallback`, `resolveContextualObject`, `getConversationId`. |
| **2. Хранилище объектов** | Обычно `ConcurrentHashMap<String, Object>` (ключ – имя бина + идентификатор контекста). | Нужно обеспечить потокобезопасность, а также хранить `DestructionCallback`. |
| **3. Контекст (conversation)** | Идентификатор контекста (например, ID потока, ID сессии, tenant‑id). | Для каждого контекста создаётся отдельный `Map`. |
| **4. registerDestructionCallback** | Сохраняем `Runnable` в отдельной структуре и вызываем его в `remove()` или при завершении контекста. | Для сессии/запроса Spring сам удаляет, но для кастомного – вы должны вызвать вручную. |
| **5. resolveContextualObject** | Можно вернуть контекстные объекты (например, `HttpServletRequest`). | Нужно вернуть `null`, если не поддерживается. |
| **6. getConversationId** | Возвращает строку‑идентификатор текущего контекста. | Для request‑scope – `HttpServletRequest.getSession().getId()`. Для thread‑scope – `Thread.currentThread().getId()`. |
| **7. Регистрация скопа** | 1) `CustomScopeConfigurer` (XML/Java)  2) `BeanFactoryPostProcessor` → `beanFactory.registerScope(name, scopeInstance)` | Регистрация должна пройти до создания бинов. |
| **8. Аннотация @Scope** | `@Scope(value = \"myScope\", proxyMode = ScopedProxyMode.TARGET_CLASS)` | `proxyMode` нужен, если бин с кастомным скопом внедряется в singleton‑bean. |
| **9. Прокси** | Если `proxyMode != NO`, Spring создаст CGLIB/Java‑interface‑прокси. | Прокси обеспечивает «ленивую» инициализацию и правильную подмену при каждом запросе к бину. |
| **10. Параллельность** | Для thread‑scope используйте `ThreadLocal`. Для tenant‑scope – `ConcurrentHashMap<tenantId, Map<beanName, bean>>`. | Убедитесь, что все операции атомарны. |
| **11. Уничтожение** | В `remove()` вызывайте сохранённые `DestructionCallback`. | Важно, чтобы ресурсы (DB‑сессии, файлы, sockets) освобождались. |
| **12. Примеры использования** | *Thread‑scope* – кэш на уровне потока. <br>*Tenant‑scope* – отдельные бины для каждого арендатора. <br>*Test‑scope* – изолировать бины в unit‑тестах. | В тестах можно регистрировать скоп “test” и вручную очищать его. |

---

## Мини‑пример: Thread‑Scope

```java
public class ThreadScope implements Scope {

    private final ThreadLocal<Map<String, Object>> objects = ThreadLocal.withInitial(HashMap::new);
    private final ThreadLocal<Map<String, Runnable>> callbacks = ThreadLocal.withInitial(HashMap::new);

    @Override
    public Object getObject(String name, ObjectFactory<?> objectFactory) {
        Map<String, Object> map = objects.get();
        return map.computeIfAbsent(name, k -> objectFactory.getObject());
    }

    @Override
    public Object remove(String name) {
        Map<String, Object> map = objects.get();
        Runnable cb = callbacks.get().remove(name);
        if (cb != null) cb.run();
        return map.remove(name);
    }

    @Override
    public void registerDestructionCallback(String name, Runnable callback) {
        callbacks.get().put(name, callback);
    }

    @Override
    public Object resolveContextualObject(String key) {
        return null; // не поддерживаем
    }

    @Override
    public String getConversationId() {
        return String.valueOf(Thread.currentThread().getId());
    }
}
```

**Регистрация:**

```java
@Configuration
public class ScopeConfig {

    @Bean
    public static BeanFactoryPostProcessor scopeConfigurer() {
        return beanFactory -> beanFactory.registerScope(\"thread\", new ThreadScope());
    }
}
```

**Использование:**

```java
@Component
@Scope(value = \"thread\", proxyMode = ScopedProxyMode.TARGET_CLASS)
public class PerThreadService {
    // …
}
```

---

## Что ещё стоит вспомнить

1. **Проблема синглтона + кастомный скоп** – если бин с кастомным скопом внедряется в singleton‑bean, без прокси вы получите *единственный* экземпляр. Поэтому `proxyMode = TARGET_CLASS` почти всегда нужен.
2. **Время жизни** – в Spring есть `Scope.SCOPE_PROTOTYPE`, `SCOPE_SINGLETON`, `SCOPE_REQUEST`, `SCOPE_SESSION`, `SCOPE_APPLICATION`. Кастомный скоп должен явно управлять “конверсией” (start/stop) контекста.
3. **Тесты** – можно зарегистрировать скоп “test” и в `@AfterEach` очищать `ThreadLocal`/`Map`. Это удобно для unit‑тестов, где нет настоящего запроса/сессии.
4. **Логирование** – полезно логировать `getConversationId()` и `remove()`, чтобы отследить утечки.
5. **Ключ бина** – в `getObject` используйте `beanFactory.getObjectName()` + `getConversationId()` как ключ, чтобы избежать коллизий между разными контекстами.

---

### Итог

- **Определите** `Scope`‑интерфейс.
- **Реализуйте** все методы, учитывая потокобезопасность и уничтожение.
- **Зарегистрируйте** скоп до создания бинов.
- **Аннотируйте** нужные бины `@Scope(\"myScope\", proxyMode = …)`.
- **Управляйте** жизненным циклом (remove, destruction callbacks).
- **Проверьте** в тестах, что контекст очищается.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Configuration Spring MVC</summary>
    <p style='font-size: 14px'>

## Конфигурация Spring MVC (Java‑ и XML‑варианты)

| Что | Как это выглядит | Ключевые особенности |
|-----|-----------------|----------------------|
| **Основной servlet** | `DispatcherServlet` – центральный роутер. В XML‑конфиге через `<servlet>`, в Java‑конфиге через `WebApplicationInitializer` (`AbstractAnnotationConfigDispatcherServletInitializer`). | Делает **HandlerMapping → HandlerAdapter → ViewResolver**. |
| **Java‑конфигурация** | `@Configuration` + `@EnableWebMvc` + `@ComponentScan` | Подключает автоконфиг Spring MVC (`WebMvcAutoConfiguration` в Spring Boot) и переопределяет любые дефолтные бины. |
| **XML‑конфигурация** | `<mvc:annotation-driven/>` + `<mvc:resources/>` + `<mvc:default-servlet-handler/>` | Эквивалент `@EnableWebMvc`. |
| **HandlerMapping** | `RequestMappingHandlerMapping` (по аннотациям) | Поддерживает `@RequestMapping`, `@GetMapping`, `@PostMapping` и т.д. |
| **HandlerAdapter** | `RequestMappingHandlerAdapter` | Вызов методов контроллеров, биндинг параметров, валидация. |
| **ViewResolver** | `InternalResourceViewResolver` (JSP), `ThymeleafViewResolver`, `FreeMarkerViewResolver` | Определяет, как искать и рендерить представления. |
| **Static resources** | `<mvc:resources mapping=\"/resources/**\" location=\"/resources/\" />` | Перенаправляет запросы к статике через `ResourceHandlerRegistry`. |
| **Locale & i18n** | `LocaleResolver` (`CookieLocaleResolver`, `SessionLocaleResolver`) + `LocaleChangeInterceptor` | Позволяет менять язык через параметр `?lang=ru`. |
| **Multipart** | `MultipartResolver` (`StandardServletMultipartResolver` / `CommonsMultipartResolver`) | Обрабатывает `MultipartFile` в контроллерах. |
| **Validation** | `@Valid`, `@Validated`, `BindingResult` | Валидация POJO‑ов, кастомные валидаторы. |
| **Exception handling** | `@ExceptionHandler`, `@ControllerAdvice` | Глобальная обработка ошибок. |
| **Interceptors** | `HandlerInterceptor` (`preHandle`, `postHandle`, `afterCompletion`) | Логи, аутентификация, CORS‑интерцептор. |
| **Argument / Return value resolvers** | `HandlerMethodArgumentResolver`, `HandlerMethodReturnValueHandler` | Пользовательские типы параметров/результатов. |
| **Content‑Negotiation** | `ContentNegotiationManager`, `HttpMessageConverter` | Авто‑выбор JSON, XML, XML‑Jackson, etc. |
| **Async** | `@Async`, `Callable`, `DeferredResult`, `WebAsyncTask` | Асинхронные контроллеры. |
| **CORS** | `@CrossOrigin` (или `CorsRegistry` в `WebMvcConfigurer`) | Разрешение запросов из других доменов. |
| **Profiles & environment** | `@Profile`, `@PropertySource`, `@ConfigurationProperties`, `@Value` | Разные настройки для dev/prod. |
| **WebMvcConfigurer** | Реализация интерфейса (или `WebMvcConfigurerAdapter`, но он устарел) | Переопределяет любые дефолтные бины: `addInterceptors`, `addResourceHandlers`, `configureViewResolvers` и т.д. |
| **Spring Boot** | `spring-boot-starter-web` + `@SpringBootApplication` | Автоматически подгружает `WebMvcAutoConfiguration`; можно отключить через `@SpringBootApplication(exclude = {WebMvcAutoConfiguration.class})` и подключить свою конфиг‑класс. |
| **WebApplicationInitializer** | `AbstractAnnotationConfigDispatcherServletInitializer` | Java‑конфиг без `web.xml`. |
| **XML‑конфиг в Spring Boot** | `META-INF/spring-webmvc.xml` | Можно подключить вручную, но редко требуется. |

### Как собрать всё вместе (Java‑конфиг)

```java
@Configuration
@EnableWebMvc
@ComponentScan(\"com.example.web\")
public class WebConfig implements WebMvcConfigurer {

    // ViewResolver
    @Bean
    public InternalResourceViewResolver viewResolver() {
        InternalResourceViewResolver vr = new InternalResourceViewResolver();
        vr.setPrefix(\"/WEB-INF/views/\");
        vr.setSuffix(\".jsp\");
        return vr;
    }

    // Static resources
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler(\"/static/**\")
                .addResourceLocations(\"/static/\");
    }

    // Locale
    @Bean
    public LocaleResolver localeResolver() {
        CookieLocaleResolver lr = new CookieLocaleResolver();
        lr.setDefaultLocale(Locale.ENGLISH);
        return lr;
    }

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LocaleChangeInterceptor());
    }

    // Multipart
    @Bean
    public MultipartResolver multipartResolver() {
        return new StandardServletMultipartResolver();
    }

    // Custom message converter (Jackson)
    @Override
    public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
        converters.add(new MappingJackson2HttpMessageConverter());
    }
}
```

### Быстрый чек‑лист

1. **DispatcherServlet** → `@EnableWebMvc` + `WebApplicationInitializer`.
2. **Контроллеры** – `@Controller` / `@RestController`.
3. **Маршрутизация** – `@RequestMapping`, `@GetMapping` и т.д.
4. **Параметры** – `@PathVariable`, `@RequestParam`, `@RequestBody`, `@RequestHeader`.
5. **Валидация** – `@Valid` + `BindingResult`.
6. **Вьюхи** – `ViewResolver`.
7. **Статика** – `ResourceHandlerRegistry`.
8. **Файлы** – `MultipartResolver`.
9. **i18n** – `LocaleResolver` + `LocaleChangeInterceptor`.
10. **Кросс‑доменные** – `@CrossOrigin` / `CorsRegistry`.
11. **Профили** – `@Profile`, `@PropertySource`.
12. **Глобальные исключения** – `@ControllerAdvice`.
13. **Аспекты** – `HandlerInterceptor`.
14. **Async** – `Callable`, `DeferredResult`.
15. **Content‑Negotiation** – `HttpMessageConverter`.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Configuration Spring Boot</summary>
    <p style='font-size: 14px'>

## Конфигурация в Spring Boot – кратко и ёмко

| Что | Как это работает | Ключевые аннотации/настройки |
|-----|------------------|-----------------------------|
| **Файлы конфигурации** | `application.properties` / `application.yml` – основной источник свойств. | `spring-boot-starter` автоматически загружает их. |
| **Порядок загрузки** | 1. `bootstrap.properties` (если есть)  <br>2. `application.properties`/`yml`  <br>3. Переменные окружения  <br>4. Командная строка | Переменные окружения и CLI переопределяют свойства. |
| **Профили** | `spring.profiles.active=dev` – выбирает набор свойств и конфиг‑классы. | `@Profile(\"dev\")`, `application-dev.yml` |
| **Внедрение свойств** | `@Value(\"${my.prop}\")` или `@ConfigurationProperties` | `@ConfigurationProperties(prefix=\"my\")` |
| **Автоконфигурация** | Spring Boot подключает `AutoConfiguration`‑модули (DataSource, JPA, MVC и т.д.) | `@SpringBootApplication` + `@EnableAutoConfiguration` |
| **Исключения** | Отключить автоконфиг: `spring.autoconfigure.exclude=org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration` | `exclude = {...}` в `@SpringBootApplication` |
| **Собственные конфиги** | Создайте `@Configuration`‑класс, объявляйте `@Bean`. | `@Configuration`, `@Bean`, `@Import` |
| **Условные бин‑создания** | Бины создаются только при выполнении условий. | `@ConditionalOnClass`, `@ConditionalOnMissingBean`, `@ConditionalOnProperty`, `@ConditionalOnExpression`, `@ConditionalOnWebApplication`, `@ConditionalOnMissingClass`, `@ConditionalOnResource` |
| **Подключение внешних файлов** | `@PropertySource(\"classpath:extra.properties\")` | `@PropertySource` |
| **Объект‑обёртка** | Свойства привязываются к POJO‑классам. | `@ConfigurationProperties`, `@ConfigurationPropertiesScan` |
| **Разрешение свойств** | Переменные вида `${VAR:default}` → fallback‑значения. | `@Value(\"${prop:default}\")` |
| **SpEL в свойствах** | `${my.prop:#{T(java.lang.Math).random()}}` | `#{...}` |
| **Конфигурация тестов** | `@SpringBootTest` + `@TestPropertySource` | `@TestPropertySource` |
| **DevTools** | Авто‑перезапуск, live‑reload, горячая перезагрузка. | `spring-boot-devtools` |
| **Actuator** | `/actuator/health`, `/actuator/metrics` и т.д. | `spring-boot-starter-actuator` |
| **Config Server** (Spring Cloud) | Централизованное хранение конфигураций. | `spring-cloud-config-server`, `spring-cloud-config-client` |
| **Переменные окружения** | `SPRING_PROFILES_ACTIVE=prod` → `spring.profiles.active=prod` | Автоматическое сопоставление `SNAKE_CASE` → `camelCase` |
| **Порядок бин‑создания** | `@DependsOn`, `@Lazy`, `@PostConstruct` | `@DependsOn`, `@Lazy` |
| **Объект‑обёртка** | `Environment` и `PropertySources` – доступ к свойствам во время выполнения. | `Environment`, `PropertySources` |

---

### Краткое резюме

1. **Spring Boot автоматически ищет** `application.properties`/`yml` и загружает их по определённому порядку.
2. **Профили** позволяют переключать наборы свойств и конфиг‑классов.
3. **Автоконфигурация** избавляет от ручного создания большинства бинов, но можно **исключить** нужные модули.
4. **Собственные конфиги** создаются в `@Configuration`‑классах и могут быть **условными** (`@Conditional*`).
5. Свойства можно привязывать к POJO через `@ConfigurationProperties` или внедрять напрямую через `@Value`.
6. **Переменные окружения** и **командная строка** всегда имеют более высокий приоритет, чем файлы.
7. Для тестов и разработки удобно использовать **DevTools** и **Actuator**.
8. В продакшене часто применяют **Spring Cloud Config** для централизованного управления конфигурацией.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Spring Data JPA</summary>
    <p style='font-size: 14px'>

## Spring Data JPA – быстрый старт

Spring Data JPA – это часть экосистемы Spring, которая делает работу с **JPA‑провайдерами** (обычно Hibernate) проще и «умнее».  
Вместо того чтобы писать много шаблонного кода, вы объявляете репозитории – интерфейсы, и Spring сам генерирует реализацию.

---

## Что делает Spring Data JPA?

| Что | Как это работает | Краткое объяснение |
|-----|------------------|--------------------|
| **Репозитории** | `CrudRepository`, `JpaRepository`, `PagingAndSortingRepository` | Интерфейсы‑контракты. Spring создаёт реализацию под капотом. |
| **CRUD‑операции** | `save`, `findById`, `delete`, `findAll` | Никакого ручного SQL‑кода – вызов метода = запрос. |
| **Построение запросов по имени** | `findByLastNameAndAge(String lastName, int age)` | Spring строит JPQL/SQL по шаблону имени метода. |
| **Кастомные запросы** | `@Query(\"SELECT u FROM User u WHERE u.email = :email\")` | Вы пишете JPQL (или нативный SQL), Spring выполняет. |
| **Модифицирующие запросы** | `@Modifying @Query(\"UPDATE User u SET u.active = false WHERE u.id = :id\")` | Обновление/удаление без `save`. |
| **Пагинация и сортировка** | `Pageable`, `Page<T>`, `Sort` | `findAll(Pageable)` – удобно для больших наборов. |
| **Спецификации** | `JpaSpecificationExecutor<T>` | Создаём гибкие критерии с помощью `Specification`. |
| **Query‑DSL** | `@EnableJpaRepositories` + `QueryDslPredicateExecutor` | Тип‑безопасные запросы через DSL. |
| **Query‑by‑Example (QBE)** | `Example<T>` | Поиск по «примеру» объекта. |
| **Проекции** | Интерфейсы/DTO‑классы | Выбираем только нужные поля, ускоряем запросы. |
| **Entity‑Graph** | `@EntityGraph` | Предопределяем, какие связи загружать. |
| **Query‑Hints** | `@QueryHints` | Переопределяем поведение кэша, индексации и т.д. |
| **Аудит** | `@CreatedDate`, `@LastModifiedDate`, `@CreatedBy`, `@LastModifiedBy` | Автоматически заполняем метаданные. |
| **Оптимистичная блокировка** | `@Version` | Автоматический контроль конфликтов обновлений. |
| **Транзакции** | `@Transactional` | Управление транзакциями над репозиториями. |
| **Встроенный кэш** | `@Cacheable`, `@CacheEvict` | Первая загрузка кэшируется, последующие – берутся из кэша. |
| **Наследование репозиториев** | `interface UserRepository extends JpaRepository<User, Long>, UserRepositoryCustom` | Вы можете добавить собственную реализацию. |
| **Связанные сущности** | `@ManyToOne`, `@OneToMany`, `@JoinColumn` | Spring‑Data не меняет JPA‑мэппинг, но умеет «подключать» связи по запросу. |

---

## Как подключить

```java
@Configuration
@EnableJpaRepositories(basePackages = \"com.example.repo\")
public class JpaConfig {
    @Bean
    public LocalContainerEntityManagerFactoryBean entityManagerFactory() {
        // конфигурируем Hibernate, datasource и др.
    }
}
```

или в `application.yml`:

```yaml
spring:
  data:
    jpa:
      repositories:
        enabled: true
  jpa:
    hibernate:
      ddl-auto: update
```

---

## Пример репозитория

```java
public interface UserRepository extends JpaRepository<User, Long>, JpaSpecificationExecutor<User> {

    // 1. CRUD + pagination
    Page<User> findByLastName(String lastName, Pageable pageable);

    // 2. Поиск по имени и возрасту
    List<User> findByFirstNameAndAge(String firstName, int age);

    // 3. Кастомный JPQL
    @Query(\"SELECT u FROM User u WHERE u.email = :email\")
    Optional<User> findByEmail(@Param(\"email\") String email);

    // 4. Проекция
    interface UserNameProjection {
        String getFirstName();
        String getLastName();
    }
    List<UserNameProjection> findAllProjectedBy();
}
```

---

## Кратко о преимуществах

| Преимущество | Что экономит |
|--------------|-------------|
| **Меньше кода** | Не пишете `EntityManager` вручную |
| **Быстрая разработка** | Быстрый старт CRUD |
| **Гибкость** | Любой JPA‑провайдер, собственные запросы |
| **Пагинация** | Готовый `Pageable` |
| **Транзакции** | `@Transactional` на уровне репозитория |
| **Тестируемость** | Можно мокать репозитории |
| **Оптимизация** | Проекции, EntityGraph, QueryHints |

---

### Итог

Spring Data JPA – это «умный слой» над JPA, который избавляет от шаблонного кода и даёт мощные инструменты: автогенерацию запросов, пагинацию, проекции, спецификации и многое другое. С ним вы быстро строите надёжные, читаемые и поддерживаемые слои доступа к данным.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Configuration Spring Data JPA</summary>
    <p style='font-size: 14px'>

## Конфигурация Spring Data JPA (общее представление)

| Что | Как настроить | Краткое пояснение |
|-----|---------------|-------------------|
| **Источник данных** | `spring.datasource.*` (URL, username, password, driver) | Автоматически создаётся `DataSource`. |
| **EntityManagerFactory** | `spring.jpa.*` (или `LocalContainerEntityManagerFactoryBean` в Java‑конфиге) | Создаёт `EntityManagerFactory` из `DataSource`. |
| **Транзакции** | `spring.jpa.open-in-view` + `@EnableTransactionManagement` (или Spring Boot делает это автоматически) | Управление транзакциями через `JpaTransactionManager`. |
| **Репозитории** | `@EnableJpaRepositories(basePackages = \"com.foo.repo\")` | Автоматически создаёт бины‑репозитории, реализующие `JpaRepository`, `PagingAndSortingRepository` и др. |
| **Сканирование сущностей** | `@EntityScan(basePackages = \"com.foo.model\")` | Определяет, где искать `@Entity`. |
| **Данные Hibernate** | `spring.jpa.hibernate.ddl-auto` (none, create, update, validate, create-drop) | Как генерировать/проверять схему. |
| | `spring.jpa.show-sql` | Показывать SQL‑запросы в логах. |
| | `spring.jpa.properties.hibernate.format_sql` | Форматировать SQL. |
| | `spring.jpa.properties.hibernate.dialect` | Указать диалект (например, `org.hibernate.dialect.PostgreSQLDialect`). |
| | `spring.jpa.properties.hibernate.naming.physical-strategy` & `implicit-strategy` | Настраивать имена таблиц/полей. |
| **Пагинация и сортировка** | Репозитории, которые расширяют `PagingAndSortingRepository` | Методы `findAll(Pageable)`, `findAll(Sort)`. |
| **Спецификации** | `JpaSpecificationExecutor<T>` | Создавать сложные запросы через `Specification`. |
| **Нативные запросы** | `@Query(value = \"...\", nativeQuery = true)` | Выполнять SQL напрямую. |
| **JPQL/QL запросы** | `@Query(\"SELECT e FROM Entity e WHERE e.name = :name\")` | Синтаксис JPA‑JPQL. |
| **Изменения в репозитории** | `@Modifying` + `@Transactional` | Для `UPDATE`/`DELETE` запросов. |
| **Entity‑Graph** | `@EntityGraph(attributePaths = {\"orders\"})` | Указывает, какие связанные сущности загружать вместе. |
| **Аудит** | `@EnableJpaAuditing` + `AuditingEntityListener` | Автоматически заполняет `@CreatedDate`, `@LastModifiedDate`, `@CreatedBy`, `@LastModifiedBy`. |
| **Optimistic Locking** | `@Version` | Автоматический контроль конфликтов обновлений. |
| **Кэширование второго уровня** | `@Cacheable` + `hibernate.cache.use_second_level_cache` | Уровень кэша Hibernate. |
| **Пакетные операции** | `hibernate.jdbc.batch_size`, `hibernate.order_inserts`, `hibernate.order_updates` | Оптимизация вставок/обновлений. |
| **Проблемы с LOB/LOB‑не‑контекстуальный режим** | `spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true` | Поддержка LOB в некоторых БД. |
| **Проблемы с TimeZone** | `spring.jpa.properties.hibernate.jdbc.time_zone=UTC` | Согласование часовых зон. |
| **Настройка транзакций** | `@Transactional(propagation = Propagation.REQUIRES_NEW)` | Создание новых транзакций. |
| **Пользовательские репозитории** | `@EnableJpaRepositories(repositoryBaseClass = MyBaseRepositoryImpl.class)` | Расширение стандартного поведения репозиториев. |
| **Spring Boot vs. Java‑конфиг** | В Boot большинство пунктов выставляется автоматически. При ручной настройке нужно объявить `@Bean` для `DataSource`, `EntityManagerFactory`, `TransactionManager`. | |

### Минимальный Java‑конфиг (не Spring Boot)

```java
@Configuration
@EnableJpaRepositories(basePackages = \"com.foo.repo\")
@EntityScan(basePackages = \"com.foo.model\")
@EnableTransactionManagement
public class JpaConfig {

    @Bean
    public DataSource dataSource() {
        return DataSourceBuilder.create()
                .driverClassName(\"org.postgresql.Driver\")
                .url(\"jdbc:postgresql://localhost:5432/foo\")
                .username(\"user\")
                .password(\"pass\")
                .build();
    }

    @Bean
    public LocalContainerEntityManagerFactoryBean entityManagerFactory(DataSource ds) {
        LocalContainerEntityManagerFactoryBean emf = new LocalContainerEntityManagerFactoryBean();
        emf.setDataSource(ds);
        emf.setPackagesToScan(\"com.foo.model\");

        HibernateJpaVendorAdapter vendorAdapter = new HibernateJpaVendorAdapter();
        vendorAdapter.setGenerateDdl(true);
        vendorAdapter.setShowSql(true);
        vendorAdapter.setDatabase(Database.POSTGRESQL);
        emf.setJpaVendorAdapter(vendorAdapter);

        Map<String, Object> props = new HashMap<>();
        props.put(\"hibernate.hbm2ddl.auto\", \"update\");
        props.put(\"hibernate.dialect\", \"org.hibernate.dialect.PostgreSQLDialect\");
        emf.setJpaPropertyMap(props);

        return emf;
    }

    @Bean
    public PlatformTransactionManager transactionManager(EntityManagerFactory emf) {
        return new JpaTransactionManager(emf);
    }
}
```

### Краткие примеры репозитория

```java
public interface UserRepository extends JpaRepository<User, Long>, JpaSpecificationExecutor<User> {
    // авто-генерация: SELECT * FROM users WHERE email = :email
    Optional<User> findByEmail(String email);

    @Query(\"SELECT u FROM User u WHERE u.status = :status\")
    List<User> findByStatus(@Param(\"status\") UserStatus status);

    @Modifying
    @Transactional
    @Query(\"UPDATE User u SET u.active = false WHERE u.lastLogin < :date\")
    int deactivateOldUsers(@Param(\"date\") LocalDate date);
}
```

### Что стоит помнить

| Что | Как избежать проблем |
|-----|----------------------|
| **Lazy loading** | Не использовать в `@Transactional(readOnly = true)` без `@EntityGraph` или `join fetch`. |
| **Нативные запросы** | Проверять, что они совместимы с выбранной БД. |
| **DDL‑генерация** | В продакшене обычно `validate` или `none`. |
| **Кэш** | Настраивать только если действительно нужен второй уровень кэша. |
| **Транзакции** | Писать сервисный слой (`@Transactional`) вместо репозитория. |

> **Итог**: Spring Data JPA упрощает работу с JPA, но всё равно требует понимания ключевых компонентов (DataSource, EntityManagerFactory, репозитории, транзакции) и возможностей Hibernate (пагинация, спецификации, кэш, аудит). Настройка обычно делается через `application.yml` в Spring Boot, а при ручном конфиге – через `@Configuration`‑бины.
</p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Spring Aretmis</summary>
    <p style='font-size: 14px'>

## Spring + Apache Artemis: Что и как работает

| Что | Как Spring использует | Что важно знать |
|-----|-----------------------|-----------------|
| **Artemis** | 1‑дисциплинарный брокер (AMQP 1.0, OpenWire, STOMP, MQTT). | Работает как обычный JMS‑брокер, но поддерживает AMQP‑протокол. |
| **JMS** | Spring‑пакет `spring-jms` + `spring-boot-starter-artemis`. | Оба используют API JMS 2.0 (Java EE 7). |
| **ConnectionFactory** | `ActiveMQConnectionFactory` (Artemis‑собственная реализация). | Можно конфигурировать через `application.yml` или Java‑конфиг. |
| **JmsTemplate** | `JmsTemplate` – удобная обёртка для отправки. | Позволяет задать `MessageConverter`, `pubSubDomain`, `defaultDestination`. |
| **Listener** | `@JmsListener` + `JmsListenerContainerFactory`. | Автоматически создаёт `MessageListenerContainer` (Concurrent). |
| **Transactions** | Spring `@Transactional` + `JmsTransactionManager`. | Поддержка XA‑транзакций (если брокер и БД в одном JTA‑контексте). |
| **Message converters** | `MappingJackson2MessageConverter`, `SimpleMessageConverter`, `ActiveMQMessageConverter`. | Позволяет отправлять/получать POJO, JSON, XML. |
| **Dead‑Letter & Expiry** | Artemis‑специфичные адреса (`dead-letter-address`, `expiry-address`). | Через `ActiveMQConnectionFactory` можно задать `deadLetterAddress`, `expiryAddress`. |
| **Redelivery & DLQ** | `redeliveryPolicy` в `ActiveMQConnectionFactory`. | Настраивает количество попыток, задержку и DLQ‑адрес. |
| **Cluster & HA** | `discoveryGroup` / `clusteredConnectionFactory`. | Авто‑фэйл‑овер и балансировка нагрузки. |
| **AMQP support** | Через `org.apache.activemq.artemis.jms.client.ActiveMQConnectionFactory` с `protocol=amqp`. | Позволяет подключаться к AMQP‑кластерам из Spring‑приложения. |
| **JNDI** | `ArtemisJndiProperties` + `ConnectionFactory` через JNDI. | Полезно для старых приложений, которые используют JNDI‑lookup. |
| **Spring‑Boot auto‑config** | `org.springframework.boot.autoconfigure.jms.artemis.ArtemisAutoConfiguration`. | Авто‑создаёт `ConnectionFactory`, `JmsTemplate`, `JmsListenerContainerFactory`. |
| **Spring‑AMQP vs. Spring‑JMS** | Для AMQP‑протокола можно использовать `spring-amqp` + `spring-boot-starter-amqp`. | Если нужен AMQP‑пакетный API, а не JMS‑API. |

---

## Быстрый старт (Spring Boot)

```yaml
# application.yml
spring:
  artemis:
    mode: native          # native broker (embedded) или remote
    host: localhost
    port: 61616
    user: artemis
    password: password
    # Artemis‑specific
    connection-factory:
      max-connections: 10
      discovery-group-name: myCluster
      discovery-group-address: 228.1.1.1
      discovery-group-port: 57123
    # Dead‑letter & expiry
    dead-letter-address: DLQ
    expiry-address: Expiry
```

```java
@Configuration
public class ArtemisConfig {

    @Bean
    public JmsTemplate jmsTemplate(ConnectionFactory cf) {
        JmsTemplate jt = new JmsTemplate(cf);
        jt.setMessageConverter(new MappingJackson2MessageConverter());
        jt.setPubSubDomain(false); // очередь
        return jt;
    }

    @Bean
    public JmsListenerContainerFactory<?> jmsFactory(ConnectionFactory cf) {
        DefaultJmsListenerContainerFactory factory = new DefaultJmsListenerContainerFactory();
        factory.setConnectionFactory(cf);
        factory.setConcurrency(\"5-10\"); // пул слушателей
        factory.setSessionTransacted(true);
        return factory;
    }
}
```

```java
@Component
public class OrderListener {

    @JmsListener(destination = \"orders\", containerFactory = \"jmsFactory\")
    public void receive(Order order) {
        // обработка
    }
}
```

```java
@Service
public class OrderService {

    @Autowired
    private JmsTemplate jmsTemplate;

    public void place(Order order) {
        jmsTemplate.convertAndSend(\"orders\", order);
    }
}
```

---

## Особенности Artemis, которые стоит помнить

| Фича | Как использовать в Spring |
|------|---------------------------|
| **AMQP 1.0** | `org.apache.activemq.artemis.jms.client.ActiveMQConnectionFactory` с `protocol=amqp`. |
| **OpenWire** | Стандартный JDBC‑подобный протокол, совместимый со старой ActiveMQ. |
| **Topic vs Queue** | `pubSubDomain=true` в `JmsTemplate` → Topic; `false` → Queue. |
| **Durable подписки** | `@JmsListener(destination=\"topic\", subscription=\"subName\", containerFactory=\"jmsFactory\")`. |
| **Message grouping** | `JmsTemplate.setMessageGroupId(\"group1\")`. |
| **Message priority** | Через `Message.setJMSPriority(int)`. |
| **Redelivery & DLQ** | `cf.setRedeliveryPolicy(...)`. |
| **Clustered connections** | `discoveryGroupName`, `discoveryGroupAddress`. |
| **Failover** | `cf.setFailoverOnException(true)`. |
| **JMS 2.0 API** | Можно использовать `JMSContext` напрямую: `try (JMSContext ctx = cf.createContext()) { ... }`. |
| **JMX** | Artemis exposes MBeans; Spring Boot Actuator can подключить `artemis` endpoint. |
| **Metrics** | `artemis-metrics` integration, Prometheus exporter. |
</p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Configuration Spring Artemis</summary>
    <p style='font-size: 14px'>

## Конфигурация Spring + Apache Artemis

> **Кратко** – Artemis – это брокер сообщений, совместимый с JMS 2.0.  
> **Spring** (Spring Boot или Spring Framework) автоматически подхватывает его, если в classpath‑е есть `artemis-jms-client`.  
> Ниже – основные пункты, которые стоит знать при настройке.

---

### 1. Зависимости

```xml
<!-- Spring Boot -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-artemis</artifactId>
</dependency>
```

Для обычного Spring (не Boot):

```xml
<dependency>
    <groupId>org.apache.activemq</groupId>
    <artifactId>artemis-jms-client</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jms</artifactId>
</dependency>
```

---

### 2. Настройка через `application.properties` / `application.yml`

```yaml
spring:
  artemis:
    mode: native          # (embedded / native). Для внешнего брокера ставим `native`
    host: localhost
    port: 61616
    user: admin
    password: admin
    # Дополнительно:
    #   - ssl-enabled: true
    #   - pool:
    #       max-connections: 10
    #       idleTimeout: 30000
```

> **Параметры**
> * `mode`: `embedded` – встроенный брокер (поднимается Spring Boot), `native` – подключение к внешнему.
> * `pool.max-connections`: пул соединений, полезен при высоких нагрузках.
> * `ssl-enabled`: включить TLS/SSL.
> * `pool.prefetch`: количество сообщений, которые слушатель может «выделять» за раз (см. ниже).

---

### 3. Автоматическая конфигурация (Spring Boot)

Spring Boot создаёт:

| Компонент | Что делает |
|-----------|------------|
| `ConnectionFactory` | Создаёт соединения с Artemis. |
| `JmsTemplate` | Упрощает отправку сообщений. |
| `JmsListenerContainerFactory` | Создаёт контейнеры слушателей (`@JmsListener`). |
| `MessageConverter` | По умолчанию использует `SimpleMessageConverter`. |

> **Нужно только** добавить `@EnableJms` (если не Boot) и использовать `@JmsListener`.

---

### 4. Настройка вручную (Spring Framework)

```java
@Configuration
@EnableJms
public class ArtemisConfig {

    @Bean
    public ArtemisConnectionFactory connectionFactory() {
        ArtemisConnectionFactory cf = new ArtemisConnectionFactory();
        cf.setBrokerURL(\"tcp://localhost:61616\");
        cf.setUser(\"admin\");
        cf.setPassword(\"admin\");
        // Пул соединений
        cf.setPoolMaxSize(10);
        return cf;
    }

    @Bean
    public JmsTemplate jmsTemplate(ConnectionFactory cf) {
        JmsTemplate jt = new JmsTemplate(cf);
        jt.setDefaultDestinationName(\"myQueue\");
        jt.setPubSubDomain(false); // queue, а не topic
        return jt;
    }

    @Bean
    public DefaultJmsListenerContainerFactory jmsListenerContainerFactory(
            ConnectionFactory cf) {
        DefaultJmsListenerContainerFactory factory = new DefaultJmsListenerContainerFactory();
        factory.setConnectionFactory(cf);
        factory.setConcurrency(\"5-10\"); // пул слушателей
        factory.setSessionAcknowledgeMode(Session.CLIENT_ACKNOWLEDGE);
        factory.setCacheLevel(AbstractJmsListenerContainerFactory.CacheLevel.DESTINATION);
        return factory;
    }
}
```

---

### 5. Отправка сообщений

```java
@Service
public class Producer {

    @Autowired
    private JmsTemplate jmsTemplate;

    public void send(String msg) {
        jmsTemplate.convertAndSend(\"myQueue\", msg);
    }
}
```

---

### 6. Приём сообщений

```java
@Component
public class Consumer {

    @JmsListener(destination = \"myQueue\")
    public void onMessage(String msg) {
        System.out.println(\"Получено: \" + msg);
    }

    // Для топика (pub/sub)
    @JmsListener(destination = \"myTopic\", containerFactory = \"jmsListenerContainerFactory\")
    public void onTopic(String msg) {
        System.out.println(\"Топик: \" + msg);
    }
}
```

> **Особенности слушателей**
> * `concurrency` – диапазон потоков (например, `\"5-15\"`).
> * `maxMessagesPerTask` – лимит сообщений в одном потоке.
> * `receiveTimeout` – таймаут получения.
> * `cacheLevel` – кэширование соединений/сессий/целей.

---

### 7. Продвинутые возможности Artemis + Spring

| Фича | Как использовать |
|------|-------------------|
| **Durable subscription** | `@JmsListener(destination = \"topic\", subscriber = \"mySub\", pubSubDomain = true)` |
| **Message selector** | `@JmsListener(destination = \"myQueue\", selector = \"priority > 5\")` |
| **Priority & TTL** | `jmsTemplate.setPriority(5); jmsTemplate.setTimeToLive(60000);` |
| **Transaction** | `@Transactional` + `sessionAcknowledgeMode(Session.SESSION_TRANSACTED)` |
| **DLQ (Dead Letter Queue)** | В Artemis задаётся через `dead-letter-address` в конфиге брокера. Spring автоматически перенаправляет. |
| **Redelivery policy** | В Artemis задаётся в `broker.xml` (`redelivery-delay`, `max-redelivery-attempts`). |
| **SSL/TLS** | `spring.artemis.ssl-enabled=true` + `trust-store`, `key-store`. |
| **JNDI** | Если брокер развернут в контейнере, можно подключиться через JNDI (`artemis-jms-client` + `artemis-jndi`). |
| **Prefetch** | `factory.setPrefetchPolicy(new SimplePrefetchPolicy(100))` – число сообщений, которые слушатель может «выделять» за раз. |
| **Connection pooling** | `ArtemisConnectionFactory.setPoolMaxSize(...)`, `setPoolIdleTimeout(...)`. |
| **Batch sending** | `jmsTemplate.setBatchSize(50)` – отправка пачек сообщений. |
| **Message conversion** | `jmsTemplate.setMessageConverter(new MappingJackson2MessageConverter())` – авто‑конвертация JSON. |

---

### 8. Тестирование

```java
@ExtendWith(SpringExtension.class)
@SpringBootTest
class ArtemisTest {

    @Autowired
    private JmsTemplate jmsTemplate;

    @Test
    void testSendReceive() {
        jmsTemplate.convertAndSend(\"testQueue\", \"Hello\");
        String msg = (String) jmsTemplate.receiveAndConvert(\"testQueue\");
        assertEquals(\"Hello\", msg);
    }
}
```

> **Tip** – для unit‑тестов можно использовать `@SpringBootTest` с `embedded-artemis` (поднимает брокер в памяти).

---

## Кратко про ключевые моменты

1. **Автоконфиг** (Boot) – почти всё готово, настройка только в `application.yml`.
2. **`ConnectionFactory` + `JmsTemplate`** – отправка.
3. **`@JmsListener`** + контейнеры – приём.
4. **Пул соединений / prefetch** – важны при высокой нагрузке.
5. **Durable/selector** – гибкая фильтрация и подписки.
6. **Transaction & DLQ** – надёжность.
7. **SSL** – безопасность.
8. **MessageConverter** – автоматический (JSON, XML, POJO).
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Spring Kafka</summary>
    <p style='font-size: 14px'>

## Что такое Spring Kafka?

Spring Kafka – это библиотека, которая упрощает интеграцию приложений Spring (Java) с Apache Kafka.  
Она позволяет быстро писать продюсеров и консьюмеров, управлять настройками и обрабатывать ошибки без ручного кода.

---

## Основные компоненты

| Компонент | Зачем нужен |
|-----------|-------------|
| **KafkaTemplate** | Отправка сообщений в Kafka (продюсер) |
| **@KafkaListener** | Аннотация для методов, которые будут слушать топики (консьюмер) |
| **ConcurrentKafkaListenerContainerFactory** | Создаёт контейнеры слушателей, управляет потоками и настройками |
| **ProducerFactory / ConsumerFactory** | Создают `KafkaProducer` и `KafkaConsumer` с нужными параметрами |
| **KafkaProperties** | Конфигурация из `application.yml`/`application.properties` |

---

## Как настроить

```yaml
spring:
  kafka:
    bootstrap-servers: localhost:9092
    producer:
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.apache.kafka.common.serialization.StringSerializer
    consumer:
      group-id: my-group
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      auto-offset-reset: earliest
```

> **Важно:**
> * `bootstrap-servers` – адреса брокеров.
> * `group-id` – идентификатор группы консьюмеров, нужен для балансировки нагрузки.
> * `auto-offset-reset` – как вести себя, если смещения не найдены (`earliest`, `latest`).

---

## Отправка сообщений

```java
@Autowired
private KafkaTemplate<String, String> kafkaTemplate;

public void send(String topic, String key, String value) {
    kafkaTemplate.send(topic, key, value);
}
```

* Можно использовать `Future<RecordMetadata>`, чтобы узнать статус отправки.
* Для сериализации можно задать свой класс (например, Avro, Protobuf).

---

## Приём сообщений

```java
@KafkaListener(topics = \"my-topic\", groupId = \"my-group\")
public void listen(String message) {
    System.out.println(\"Received: \" + message);
}
```

### Параметры `@KafkaListener`

| Параметр | Что делает |
|----------|------------|
| `topics` | Топики, которые слушаем |
| `groupId` | Группа консьюмеров (можно переопределить из `application.yml`) |
| `containerFactory` | Указывает, какой `ConcurrentKafkaListenerContainerFactory` использовать |
| `errorHandler` | Хэндлер для обработки ошибок (см. ниже) |

---

## Настройка контейнера слушателя

```java
@Bean
public ConcurrentKafkaListenerContainerFactory<String, String> kafkaListenerContainerFactory() {
    ConcurrentKafkaListenerContainerFactory<String, String> factory =
            new ConcurrentKafkaListenerContainerFactory<>();
    factory.setConsumerFactory(consumerFactory());
    factory.setConcurrency(3); // 3 потока для одного слушателя
    factory.getContainerProperties().setAckMode(ContainerProperties.AckMode.MANUAL_IMMEDIATE);
    return factory;
}
```

* `concurrency` – количество потоков (параллельных консьюмеров).
* `ackMode` – как подтверждать сообщения:
    * `BATCH` – по умолчанию, авто‑подтверждение после каждой порции.
    * `MANUAL` / `MANUAL_IMMEDIATE` – ручное подтверждение через `Acknowledgment`.

---

## Обработка ошибок

```java
@Bean
public SeekToCurrentErrorHandler errorHandler() {
    return new SeekToCurrentErrorHandler(new FixedBackOff(0L, 2)); // 2 попытки без паузы
}
```

* `SeekToCurrentErrorHandler` – при ошибке переходит к следующему смещению.
* `DeadLetterPublishingRecoverer` – можно отправлять ошибочные сообщения в DLQ.

---

## Транзакции

```java
@Bean
public KafkaTransactionManager<String, String> kafkaTransactionManager(ProducerFactory<String, String> pf) {
    return new KafkaTransactionManager<>(pf);
}
```

* Позволяет группировать `send` в одну транзакцию (commit/rollback).
* В сочетании с `@Transactional` можно делать атомарные операции.

---

## Другие особенности

| Фича | Что делает |
|------|------------|
| **Idempotent Producer** | Устанавливаем `enable.idempotence=true` → гарантирует, что дублирующие сообщения не будут записаны. |
| **Exactly‑once** | Включаем транзакции + `enable.idempotence`. |
| **Schema Registry** | При работе с Avro/Protobuf можно подключить Confluent Schema Registry через `KafkaAvroSerializer`. |
| **Kafka Streams** | Можно подключить через `spring-kafka-streams` для потоковой обработки. |
| **Topic Auto‑Creation** | Если `auto.create.topics.enable=true`, Kafka создаст топик автоматически при первом обращении. |
| **Metrics** | Spring Kafka интегрируется с Micrometer – можно собирать метрики по потреблению/производству. |
| **Record Header** | Можно передавать заголовки (headers) вместе с сообщением – полезно для метаданных. |
| **Batch Listener** | `@KafkaListener` может принимать `List<ConsumerRecord<...>>` для пакетной обработки. |
| **Transactional Listener** | `@Transactional` над `@KafkaListener` → транзакция вокруг обработки сообщения. |
| **AckOnError** | В `AckMode.MANUAL_IMMEDIATE` можно отклонять сообщение вручную, если обработка не удалась. |
| **Offset Commit Strategy** | `enable.auto.commit=false` + ручной commit → более надёжный контроль смещений. |
| **Partition Assignment** | Можно задать `partition` в `@KafkaListener` для фиксированной привязки. |
| **Consumer Rebalance Listener** | Реализовать `ConsumerRebalanceListener` для обработки событий перераспределения партиций. |

---

## Минимальный рабочий пример

```java
@SpringBootApplication
public class KafkaDemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(KafkaDemoApplication.class, args);
    }

    @Bean
    public CommandLineRunner demo(KafkaTemplate<String, String> template) {
        return args -> template.send(\"demo-topic\", \"key1\", \"Hello, Kafka!\");
    }

    @KafkaListener(topics = \"demo-topic\", groupId = \"demo-group\")
    public void listen(String msg) {
        System.out.println(\"Got: \" + msg);
    }
}
```

---

## Итоги

* **Spring Kafka** делает интеграцию с Kafka простой и «Spring‑образной**.
* Основные задачи: отправка (`KafkaTemplate`) и приём (`@KafkaListener`).
* Управление настройками через `application.yml`, `ProducerFactory`, `ConsumerFactory`.
* Возможности: транзакции, idempotence, DLQ, retry, manual ack, metrics, stream‑processing.
* Всё можно настроить через `ConcurrentKafkaListenerContainerFactory` и `ErrorHandler`.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Configuration Spring Kafka</summary>
    <p style='font-size: 14px'>

## Конфигурация Spring + Kafka (Spring Kafka)

Ниже — «практический чек‑лист» того, как подключить и настроить Kafka в Spring‑Boot‑приложении, и какие особенности стоит знать.

| Шаг | Что делаем | Ключевые детали |
|-----|------------|-----------------|
| **1. Добавляем зависимости** | ```xml <dependency> <groupId>org.springframework.kafka</groupId> <artifactId>spring-kafka</artifactId> </dependency> ``` | Для Spring Boot уже есть `spring-boot-starter-kafka`. |
| **2. Включаем поддержку Kafka** | `@EnableKafka` | Делает `KafkaListenerContainerFactory` доступным. В Spring Boot это обычно не нужно, но удобно в чистом Spring. |
| **3. На уровне `application.yml` (или `.properties`) задаём базовые параметры** | ```yaml spring.kafka.bootstrap-servers: localhost:9092<br>spring.kafka.consumer.group-id: myGroup<br>spring.kafka.consumer.auto-offset-reset: earliest<br>spring.kafka.consumer.key-deserializer: org.apache.kafka.common.serialization.StringDeserializer<br>spring.kafka.consumer.value-deserializer: org.apache.kafka.common.serialization.StringDeserializer<br>spring.kafka.producer.key-serializer: org.apache.kafka.common.serialization.StringSerializer<br>spring.kafka.producer.value-serializer: org.apache.kafka.common.serialization.StringSerializer``` | * `auto-offset-reset` – как обрабатывать «пустой» сдвиг (earliest/latest).<br>* `group-id` – идентификатор группы потребителей.<br>* `max.poll.records`, `max.poll.interval.ms` – тонкая настройка. |
| **4. Создаём фабрику продюсера** | ```java @Configuration public class KafkaProducerConfig { @Bean public ProducerFactory<String, String> producerFactory() { Map<String,Object> props = new HashMap<>(); props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, \"localhost:9092\"); props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class); props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class); return new DefaultKafkaProducerFactory<>(props); } @Bean public KafkaTemplate<String, String> kafkaTemplate() { return new KafkaTemplate<>(producerFactory()); } }``` | `KafkaTemplate` – удобный сервис для отправки сообщений. Можно использовать `send()` с `ListenableFuture` или `sendAndReceive` (если настроена RPC‑поддержка). |
| **5. Создаём фабрику потребителя** | ```java @Configuration public class KafkaConsumerConfig { @Bean public ConsumerFactory<String, String> consumerFactory() { Map<String,Object> props = new HashMap<>(); props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, \"localhost:9092\"); props.put(ConsumerConfig.GROUP_ID_CONFIG, \"myGroup\"); props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class); props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class); return new DefaultKafkaConsumerFactory<>(props); } @Bean public ConcurrentKafkaListenerContainerFactory<String, String> kafkaListenerContainerFactory() { ConcurrentKafkaListenerContainerFactory<String, String> factory = new ConcurrentKafkaListenerContainerFactory<>(); factory.setConsumerFactory(consumerFactory()); factory.setConcurrency(3); // 3 потоков<br>factory.getContainerProperties().setAckMode(ContainerProperties.AckMode.MANUAL_IMMEDIATE); return factory; } }``` | * `setConcurrency` – количество потоков‑потребителей (часто = число партиций).<br>* `AckMode` – как управлять смещениями (AUTO, MANUAL, RECORD). |
| **6. Пишем слушатель** | ```java @KafkaListener(topics = \"myTopic\", groupId = \"myGroup\") public void listen(String payload, @Header(KafkaHeaders.RECEIVED_MESSAGE_KEY) String key, Acknowledgment ack) { System.out.println(\"Received: \" + payload); ack.acknowledge(); }``` | `@KafkaListener` – аннотация‑обёртка над `MessageListener`. Можно принимать `ConsumerRecord`, `Message`, `Headers` и т.д. |
| **7. Ошибки и переотправка** | * `SeekToCurrentErrorHandler` (Spring Kafka 2.3+) – при ошибке перемещает смещение назад и переотправляет.<br>* `DefaultErrorHandler` – более гибкая реализация с retry‑политиками.<br>* `ErrorHandler` можно настроить через `factory.setErrorHandler(...)`. | Учитывайте `maxAttempts`, `backOff` и `skipOn`. |
| **8. Транзакции** | ```java @Configuration public class TxConfig { @Bean public KafkaTransactionManager<String, String> kafkaTransactionManager(ProducerFactory<String, String> pf) { return new KafkaTransactionManager<>(pf); } }```<br>В продюсере: `kafkaTemplate.executeInTransaction(t -> { t.send(...); return null; });` | Позволяет гарантировать «атомарность» отправки нескольких сообщений. |
| **9. Создание топиков автоматически** | ```java @Bean public NewTopic myTopic() { return TopicBuilder.name(\"myTopic\").partitions(3).replicas(1).build(); }``` | `KafkaAdmin` (встроенный в Spring Kafka) создаёт топики при старте. |
| **10. Работа с сериализаторами/десериализаторами** | * `StringSerializer/Deserializer` – простая строка.<br>* `ByteArraySerializer/Deserializer` – байты.<br>* `JsonSerializer/Deserializer` (из `spring-kafka`) – сериализация POJO в JSON.<br>* `Avro` – через Confluent Schema Registry (добавляем `kafka-avro-serializer`). | Важно, чтобы *key* и *value* имели одинаковые типы в продюсере и потребителе. |
| **11. Подключение к Confluent Schema Registry** | ```properties spring.kafka.properties.schema.registry.url=http://localhost:8081``` | Позволяет использовать Avro/Protobuf/JSON Schema. |
| **12. Monitoring и metrics** | Spring Kafka интегрируется с Micrometer: `<dependency>org.springframework.boot:spring-boot-starter-actuator</dependency>`. Метрики: `kafka_consumer_records_consumed_total`, `kafka_producer_record_send_total` и т.д. | Можно подключить Prometheus, Grafana. |
| **13. Kafka Streams** | Если нужно обрабатывать поток данных, используйте `KafkaStreamsConfiguration` и `StreamsBuilder`. | В Spring Boot есть `spring-kafka-streams`. |
| **14. Тестирование** | * `EmbeddedKafkaBroker` (Spring Kafka) – запускает локальный брокер для unit‑/integration‑тестов.<br>* `KafkaTemplate` + `@KafkaListener` в тестах. | Позволяет писать реплицируемые тесты без внешнего Kafka. |
| **15. Безопасность** | * `security.protocol=SSL` + `ssl.truststore.location`, `ssl.keystore.location`.<br>* `sasl.mechanism=PLAIN` + `sasl.jaas.config`. | В `application.yml` задаём `spring.kafka.properties.*`. |
| **16. Параметры производительности** | * `batch.size`, `linger.ms`, `compression.type` – в продюсере.<br>* `fetch.min.bytes`, `fetch.max.wait.ms`, `max.poll.records` – в потребителе. | Настраивать под конкретный сценарий. |
| **17. Параметры группы потребителей** | * `enable.auto.commit` (false, если используете MANUAL).<br>* `auto.offset.reset` (earliest/latest). | Делают поведение надёжным при перезапусках. |
| **18. Отключение автокоммита** | В `consumerFactory` указываем `props.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, false);` | Полный контроль над смещениями. |
| **19. Кастомные Interceptors** | * `ProducerInterceptor` – логирование/модификация сообщений.<br>* `ConsumerInterceptor` – аналогично. | Можно добавить через `props.put(ProducerConfig.INTERCEPTOR_CLASSES_CONFIG, MyInterceptor.class.getName());` |
| **20. Компоненты Spring Cloud Stream** | Если хотите использовать abstraction над Kafka, можно перейти на Spring Cloud Stream, но это отдельный слой. | В Spring Cloud Stream есть `functions`, `binder`, `bindings`. |

---

### Минимальный пример

```yaml
# application.yml
spring:
  kafka:
    bootstrap-servers: localhost:9092
    consumer:
      group-id: myGroup
      auto-offset-reset: earliest
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.apache.kafka.common.serialization.StringDeserializer
    producer:
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.apache.kafka.common.serialization.StringSerializer
```

```java
@Configuration
@EnableKafka
public class KafkaConfig {

    @Bean
    public ProducerFactory<String, String> producerFactory() {
        Map<String, Object> props = new HashMap<>();
        props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, \"localhost:9092\");
        props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        return new DefaultKafkaProducerFactory<>(props);
    }

    @Bean
    public KafkaTemplate<String, String> kafkaTemplate() {
        return new KafkaTemplate<>(producerFactory());
    }

    @Bean
    public ConsumerFactory<String, String> consumerFactory() {
        Map<String, Object> props = new HashMap<>();
        props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, \"localhost:9092\");
        props.put(ConsumerConfig.GROUP_ID_CONFIG, \"myGroup\");
        props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        return new DefaultKafkaConsumerFactory<>(props);
    }

    @Bean
    public ConcurrentKafkaListenerContainerFactory<String, String> kafkaListenerContainerFactory() {
        var factory = new ConcurrentKafkaListenerContainerFactory<String, String>();
        factory.setConsumerFactory(consumerFactory());
        factory.setConcurrency(2);
        factory.getContainerProperties().setAckMode(ContainerProperties.AckMode.MANUAL_IMMEDIATE);
        return factory;
    }
}
```

```java
@Component
public class MyListener {

    @KafkaListener(topics = \"myTopic\", groupId = \"myGroup\")
    public void listen(String msg, Acknowledgment ack) {
        System.out.println(\"Got: \" + msg);
        ack.acknowledge(); // подтверждаем
    }
}
```

```java
@Component
public class MyProducer {

    private final KafkaTemplate<String, String> template;

    public MyProducer(KafkaTemplate<String, String> template) {
        this.template = template;
    }

    public void send(String msg) {
        template.send(\"myTopic\", msg);
    }
}
```

---

## Краткие «памятки»

- **`@EnableKafka`** – не обязателен в Spring Boot, но удобен в чистом Spring.
- **`KafkaTemplate`** – отправка, `send()` возвращает `ListenableFuture`.
- **`@KafkaListener`** – слушатель, может принимать `Message`, `ConsumerRecord`, `Headers` и т.д.
- **Транзакции** – `KafkaTransactionManager` + `executeInTransaction`.
- **Ошибка** – `SeekToCurrentErrorHandler` / `DefaultErrorHandler`.
- **Параллельность** – `setConcurrency` (часто = partitions).
- **Автокоммит** – отключаем (`enable.auto.commit=false`) и используем `AckMode.MANUAL*`.
- **Topic auto‑create** – `NewTopic` + `KafkaAdmin`.
- **Сериализаторы** – String, Json, Avro, Protobuf.
- **Мониторинг** – Micrometer + Actuator.
- **Тесты** – `EmbeddedKafkaBroker`.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Spring Security</summary>
    <p style='font-size: 14px'>

## Spring Security – краткое руководство

Spring Security – модуль Spring, который обеспечивает **аутентификацию** и **авторизацию** для приложений Java.  
Ниже перечислены основные возможности и как их обычно конфигурируют.

| Что | Как это выглядит | Как использовать в коде |
|-----|-----------------|------------------------|
| **Аутентификация** | Логин/пароль, OAuth2, JWT, LDAP, SAML, OpenID‑Connect | `AuthenticationManager`, `UserDetailsService`, `AuthenticationProvider` |
| **Авторизация** | Роли, группы, выражения Spring EL (`hasRole('ADMIN')`), ACL | `@PreAuthorize`, `@Secured`, `@RolesAllowed` |
| **Фильтры** | `SecurityFilterChain` (Spring Boot 3+), `WebSecurityConfigurerAdapter` (до 5.7) | Bean `SecurityFilterChain` с `HttpSecurity` |
| **CSRF** | Защита от подделки запросов | `csrf().disable()` для REST‑API, иначе `csrf()` включён по умолчанию |
| **CORS** | Ограничение доступа к API с разных доменов | `cors()` + `CorsConfigurationSource` |
| **Сессии** | Stateful (HTTP‑сессия), stateless (JWT), remember‑me, session‑fixation protection, concurrency control | `sessionManagement()`, `rememberMe()`, `sessionFixation()` |
| **Пароли** | BCrypt, Argon2, PBKDF2, SCrypt | `PasswordEncoder` (например, `BCryptPasswordEncoder`) |
| **Пользовательские сервисы** | In‑memory, JDBC, LDAP, кастомный `UserDetailsService` | `UserDetailsService`, `JdbcUserDetailsManager` |
| **Метод‑уровень** | `@EnableGlobalMethodSecurity`, `@EnableReactiveMethodSecurity` | Аннотации над методами |
| **ACL** | Доступ по объектам (пользователь/роль/ресурс) | `AclAuthorizationStrategy`, `MutableAclService` |
| **OAuth2/OpenID Connect** | Клиент‑приложение, ресурс‑сервер | `OAuth2LoginConfigurer`, `OAuth2ResourceServerConfigurer` |
| **JWT** | Stateless‑токен, подпись, expiration | `JwtDecoder`, `JwtEncoder` |
| **SAML / OIDC** | SSO с внешними IdP | `Saml2LoginConfigurer`, `OidcClientInitiatedLogoutSuccessHandler` |
| **Безопасность заголовков** | HSTS, X‑Content‑Type‑Options, X‑Frame‑Options, CSP | `headers()` в `HttpSecurity` |
| **Обработка ошибок** | Custom `AuthenticationEntryPoint`, `AccessDeniedHandler` | `exceptionHandling()` |
| **Тестирование** | `@WithMockUser`, `@WithSecurityContext`, `MockMvc` + `SecurityMockMvcRequestPostProcessors` | Аннотации и helper‑методы |
| **Веб‑сокеты** | `WebSocketMessageBrokerSecurity` | `channel()` в `MessageSecurityMetadataSourceRegistry` |
| **Reactive** | Spring WebFlux, `ReactiveSecurityFilterChain` | `ReactiveSecurityFilterChain` bean |
| **Пользовательские фильтры** | Добавить свой фильтр в цепочку | `addFilterBefore(...)`, `addFilterAfter(...)` |
| **События** | `AuthenticationSuccessEvent`, `AuthenticationFailureEvent`, `LogoutSuccessEvent` | `ApplicationListener` |
| **Конфигурация через YAML / Java** | Spring Boot autoconfiguration + overrides | `application.yml` + `@Configuration` |

---

### Как выглядит типичная конфигурация (Java‑конфиг)

```java
@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)   // @PreAuthorize
public class SecurityConfig {

    @Bean
    SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            // Аутентификация
            .formLogin(form -> form
                .loginPage(\"/login\")
                .permitAll()
            )
            // Авторизация
            .authorizeHttpRequests(auth -> auth
                .requestMatchers(\"/admin/**\").hasRole(\"ADMIN\")
                .anyRequest().authenticated()
            )
            // CSRF & CORS
            .csrf(csrf -> csrf.disable())      // для REST‑API
            .cors(Customizer.withDefaults())
            // Сессии
            .sessionManagement(session -> session
                .sessionCreationPolicy(SessionCreationPolicy.IF_REQUIRED)
                .invalidSessionUrl(\"/session-invalid\")
            )
            // Remember‑me
            .rememberMe(remember -> remember
                .key(\"uniqueAndSecret\")
                .tokenValiditySeconds(86400)
            )
            // Ошибки
            .exceptionHandling(ex -> ex
                .accessDeniedPage(\"/403\")
            );
        return http.build();
    }

    @Bean
    public UserDetailsService users() {
        return new InMemoryUserDetailsManager(
            User.withUsername(\"user\")
                .password(passwordEncoder().encode(\"pass\"))
                .roles(\"USER\")
                .build(),
            User.withUsername(\"admin\")
                .password(passwordEncoder().encode(\"admin\"))
                .roles(\"ADMIN\")
                .build()
        );
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

---

### Ключевые моменты, которые стоит помнить

1. **`SecurityFilterChain`** – основной пункт входа в цепочку фильтров.
2. **`PasswordEncoder`** – никогда не храните пароли в plain‑text.
3. **`@PreAuthorize`** и **`@Secured`** – мощные способы ограничения доступа на уровне методов.
4. **CSRF** включён по умолчанию; отключайте только для чистых REST‑API (или используйте токены).
5. **JWT** – лучший способ реализовать stateless‑авторизацию для микросервисов.
6. **OAuth2/OpenID Connect** – используйте `spring-boot-starter-oauth2-client` и `spring-boot-starter-oauth2-resource-server`.
7. **Тесты** – `@WithMockUser` упрощает проверку защищённых эндпойнтов.
8. **Пользовательские фильтры** – добавляйте только если стандартные не подходят; помните о правильном порядке.
9. **Сессии** – настройте `sessionFixation()` и `maximumSessions()` для безопасности.
10. **Заголовки безопасности** – включите `headers().frameOptions().sameOrigin()` и `headers().httpStrictTransportSecurity()`.
</p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Configuration Spring Security</summary>
    <p style='font-size: 14px'>

## Конфигурация Spring Security (Java‑конфиг)

> **Важно** – начиная с Spring Security 5.7 класс `WebSecurityConfigurerAdapter` объявлен устаревшим.  
> Вместо него создаётся `@Bean SecurityFilterChain` и (при необходимости) `@Bean AuthenticationManager`.

---

### 1. Основные компоненты

| Компонент | Что делает |
|-----------|------------|
| **SecurityFilterChain** | Последовательность фильтров (CSRF, CORS, авторизация, аутентификация и т.д.). |
| **AuthenticationManager** | Разрешает аутентификацию (проверка логина/пароля, JWT, OAuth2 и др.). |
| **UserDetailsService** | Загружает данные пользователя (имя, пароль, роли). |
| **PasswordEncoder** | Хэширование паролей (`BCryptPasswordEncoder`, `Argon2PasswordEncoder`, `PBKDF2PasswordEncoder` и т.д.). |
| **SecurityContextRepository** | Сохраняет `SecurityContext` (обычно в сессии). |
| **AccessDecisionManager** | Выводит решение о доступе по ролям/атрибутам. |

---

### 2. Конфигурация `SecurityFilterChain`

```java
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            // 1. Авторизация
            .authorizeHttpRequests(auth -> auth
                .requestMatchers(\"/public/**\").permitAll()          // открытые URL
                .requestMatchers(\"/admin/**\").hasRole(\"ADMIN\")      // только ADMIN
                .anyRequest().authenticated()                      // остальные требуют входа
            )

            // 2. Аутентификация
            .formLogin(form -> form
                .loginPage(\"/login\")                               // собственная страница
                .permitAll()
            )
            .httpBasic(Customizer.withDefaults())                 // Basic Auth (для API)

            // 3. CSRF и CORS
            .csrf(csrf -> csrf.disable())                         // для REST‑API
            .cors(Customizer.withDefaults())

            // 4. Сессии
            .sessionManagement(session -> session
                .sessionCreationPolicy(SessionCreationPolicy.IF_REQUIRED)
                .maximumSessions(1).maxSessionsPreventsLogin(true)
            )

            // 5. Remember‑Me
            .rememberMe(remember -> remember
                .key(\"uniqueAndSecret\")
                .tokenValiditySeconds(86400)
            )

            // 6. Logout
            .logout(logout -> logout
                .logoutUrl(\"/logout\")
                .logoutSuccessUrl(\"/login?logout\")
                .deleteCookies(\"JSESSIONID\")
            )

            // 7. Хуки ошибок
            .exceptionHandling(ex -> ex
                .authenticationEntryPoint(new HttpStatusEntryPoint(HttpStatus.UNAUTHORIZED))
                .accessDeniedHandler(new HttpStatusEntryPoint(HttpStatus.FORBIDDEN))
            );

        return http.build();
    }
}
```

> **Ключевые моменты**
> * `authorizeHttpRequests` – декларативный способ указать, кто может что делать.
> * `hasRole(\"ROLE\")` автоматически добавляет префикс `ROLE_`.
> * `anyRequest().authenticated()` оставляет остальное защищённым.
> * `csrf().disable()` удобно для чистых REST‑API (используются токены).
> * `sessionManagement()` позволяет ограничить количество сессий, настроить фиксацию сессии и т.п.

---

### 3. Аутентификация

| Тип | Как настроить |
|-----|---------------|
| **Локальный (логин/пароль)** | `UserDetailsService` + `PasswordEncoder`. Пример: `InMemoryUserDetailsManager` или `JdbcUserDetailsManager`. |
| **LDAP** | `ldapAuthentication()` в `AuthenticationManagerBuilder`. |
| **OAuth2 / OpenID Connect** | `oauth2Login()` и `oauth2Client()`. |
| **JWT** | Создаём собственный `OncePerRequestFilter`, который читает токен из заголовка, валидирует и ставит `Authentication` в `SecurityContextHolder`. |
| **Basic / Digest** | `httpBasic()` / `httpDigest()`. |
| **Custom** | Реализуем `AuthenticationProvider` и регистрируем его в `AuthenticationManager`. |

---

### 4. Авторизация

* **URL‑уровень** – `authorizeHttpRequests` (см. выше).
* **Метод‑уровень** – аннотации:
    * `@PreAuthorize(\"hasRole('ADMIN')\")`
    * `@PostAuthorize(...)`
    * `@Secured(\"ROLE_USER\")` (Spring Security 3+)
    * `@RolesAllowed(\"USER\")` (JPA/Jakarta EE).  
      Для включения понадобится `@EnableGlobalMethodSecurity(prePostEnabled = true, securedEnabled = true)`.

---

### 5. Дополнительные настройки

| Что | Как настроить |
|-----|---------------|
| **CSRF токен** | `csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse())` |
| **CORS** | `cors(Customizer.withDefaults())` + `CorsConfigurationSource` |
| **Headers** | `headers(headers -> headers.frameOptions().sameOrigin())` |
| **SameSite** | В `CookieSerializer` установить `SameSite` значение |
| **AccessDecisionManager** | `accessDecisionManager(customAdManager)` |
| **Method Security Expression Handler** | `securityExpressionHandler(customHandler)` |
| **Обработка ошибок** | `exceptionHandling().authenticationEntryPoint(...)` и `accessDeniedHandler(...)` |
| **Logout Handlers** | `logoutHandler(customLogoutHandler)` |
| **Custom Filters** | `addFilterBefore(myFilter, UsernamePasswordAuthenticationFilter.class)` |

---

### 6. Что ещё стоит знать

| Фича | Коротко |
|------|---------|
| **PasswordEncoder** | Не храните пароли в открытом виде. `BCryptPasswordEncoder` – стандарт. |
| **Roles vs Authorities** | Роли – это просто атрибуты с префиксом `ROLE_`. |
| **Session Fixation** | `sessionManagement().sessionFixation().migrateSession()` (по умолчанию). |
| **Multiple `SecurityFilterChain`** | Можно объявить несколько бинов; порядок определяется `@Order`. |
| **Ignore static assets** | `web.ignoring().requestMatchers(PathRequest.toStaticResources().atCommonLocations());` |
| **OAuth2 Resource Server** | `oauth2ResourceServer().jwt()` для JWT‑подписей. |
| **OAuth2 Client** | `oauth2Client()` + `authorizedClientService`. |
| **LDAP** | `ldapAuthentication().userDnPatterns(\"uid={0},ou=people\")`. |
| **JDBC** | `jdbcAuthentication()` + таблицы `users` и `authorities` (или кастомные SQL). |

---

## Итог

Spring Security – это гибкая, «конфиг‑по‑умолчанию» система безопасности, где вы можете:

1. **Определить, кто может что** – через URL‑или метод‑уровень.
2. **Выбрать механизм аутентификации** – от простого ин‑мемори до OAuth2/JWT/LDAP.
3. **Настроить защиту** – CSRF, CORS, сессии, remember‑me, заголовки.
4. **Расширять** – собственные фильтры, провайдеры, обработчики ошибок.

Соблюдая упомянутые рекомендации, вы получите надёжную и масштабируемую защиту вашего приложения.
    </p>
    </details>
</details>
<details>    
<summary style='font-size: 20px'><b>GIT</b></summary>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Общее</summary>
    <p style='font-size: 14px'>

## Что такое Git?
Git – это **распределённая система контроля версий**.
* Каждый участник имеет полную копию репозитория (включая историю).
* Любая операция локальная (commit, branch, merge, rebase), а синхронизация с удалённым репозиторием – отдельный шаг (push, pull, fetch).

---

## Основные понятия

| Понятие | Что это |
|---------|---------|
| **Commit** | Снимок проекта в момент времени. Содержит SHA‑1‑хеш, автор, дату и сообщение. |
| **Branch** | Указатель на конкретный commit. Позволяет развивать параллельные ветки. |
| **Merge** | Объединяет две ветки, создавая новый commit (или «fast‑forward»). |
| **Rebase** | Перемещает целую ветку поверх другого commit, «переигрывая» историю. |
| **Tag** | Неподвижный маркер (обычно для релизов). |
| **Remote** | Удалённый репозиторий (origin, upstream, fork). |
| **HEAD** | Текущий активный commit (обычно символический указатель на ветку). |
| **Ref** | Любой указатель (branch, tag, remote‑branch). |
| **Object** | Blob (файл), Tree (каталог), Commit, Tag – фундаментальные единицы Git. |
| **Reflog** | Журнал всех перемещений HEAD, полезен для восстановления «потерянных» коммитов. |

---

## Базовый набор команд

| Команда | Что делает | Пример |
|---------|------------|--------|
| `git init` | Создаёт новый репозиторий | `git init` |
| `git clone <url>` | Клонирует удалённый репозиторий | `git clone https://github.com/user/repo.git` |
| `git add <file>` | Добавляет файл в индекс | `git add main.java` |
| `git commit -m \"msg\"` | Сохраняет снимок в истории | `git commit -m \"Add login feature\"` |
| `git status` | Показывает статус файлов | `git status` |
| `git log` | История коммитов | `git log --oneline --graph --decorate` |
| `git diff` | Сравнение изменений | `git diff` |
| `git branch` | Список веток | `git branch` |
| `git branch <new>` | Создаёт ветку | `git branch featureX` |
| `git checkout <branch>` | Переходит на ветку | `git checkout featureX` |
| `git switch <branch>` | Альтернатива checkout | `git switch featureX` |
| `git merge <branch>` | Объединяет ветку | `git merge featureX` |
| `git rebase <branch>` | Перемещает ветку | `git rebase main` |
| `git pull` | fetch + merge | `git pull origin main` |
| `git push` | Отправляет изменения | `git push origin main` |
| `git fetch` | Получает новые объекты без слияния | `git fetch origin` |

---

## Расширенные возможности

### 1. **Branching & Merging**
| Функция | Описание | Пример |
|---------|----------|--------|
| `git merge --no-ff <branch>` | Создаёт merge‑commit даже при fast‑forward | `git merge --no-ff featureX` |
| `git merge --squash <branch>` | Сливает изменения в один commit | `git merge --squash featureX` |
| `git merge --abort` | Откатывает merge при конфликте | `git merge --abort` |
| `git cherry-pick <commit>` | Переносит отдельный commit в текущую ветку | `git cherry-pick abc123` |
| `git revert <commit>` | Создаёт новый commit, отменяющий предыдущий | `git revert abc123` |

### 2. **Rebase (интерактивный)**
* `git rebase -i <base>` – открывает редактор для переупорядочивания, squash‑а, исправления и удаления коммитов.

### 3. **Stash**
* `git stash` – временно сохраняет несохранённые изменения.
* `git stash pop` – применяет и удаляет stash.
* `git stash apply` – применяет без удаления.
* `git stash list` – список stash‑ов.

### 4. **Tagging**
* `git tag v1.0` – создаёт аннотированный tag.
* `git tag -a v1.0 -m \"Release 1.0\"` – аннотированный tag с сообщением.
* `git push origin v1.0` – отправляет тег в удалённый репозиторий.

### 5. **Submodule / Subtree**
| Функция | Что делает |
|---------|------------|
| `git submodule add <url> <path>` | Добавляет внешний репозиторий как подпроект. |
| `git submodule update --init --recursive` | Инициализирует и обновляет submodule. |
| `git subtree add --prefix=dir <url> <branch>` | Включает внешний репозиторий как поддиректорию. |

### 6. **Hooks**
* `pre-commit`, `commit-msg`, `post-commit`, `pre-push`, `pre-receive`, `update` – скрипты, выполняющиеся на разных этапах.
* Позволяют автоматизировать линтеры, тесты, подпись коммитов и т.д.

### 7. **Конфигурация**
* `git config --global user.name \"Иван Иванов\"`
* `git config --global user.email \"ivan@example.com\"`
* `git config alias.co checkout` – создаёт алиас.
* Файлы `.gitconfig`, `.gitignore`, `.gitattributes` – глобальные/проектные настройки.

### 8. **Reflog & Recovery**
* `git reflog` – показывает историю перемещений HEAD.
* `git reset --hard <hash>` – откатывает до нужного коммита.

### 9. **Git LFS (Large File Storage)**
* Хранит большие файлы вне обычного репозитория, заменяя их указателями.
* `git lfs install`, `git lfs track \"*.psd\"`.

### 10. **Рабочие деревья (worktree)**
* `git worktree add <path> <branch>` – создаёт дополнительное рабочее дерево, позволяя работать в нескольких ветках одновременно.

---

## Практический workflow (пример)

1. **Fork** → `git clone` → `git checkout -b featureX`
2. **Разработка** → `git add .` → `git commit -m \"Feature X\"`
3. **Rebase** на актуальную `main`:
   ```bash
   git fetch origin
   git rebase origin/main
   ```
4. **Разрешение конфликтов** (если есть) → `git add <resolved>` → `git rebase --continue`
5. **Push** ветки в свой репозиторий: `git push -u origin featureX`
6. Открыть Pull Request → код‑ревью → merge (через GitHub Flow)
7. После merge – `git pull origin main` в локальной `main`

---

## Полезные команды (шпаргалка)

| Команда | Что делает |
|---------|------------|
| `git log --oneline --graph --decorate` | Читабельный граф |
| `git diff --staged` | Разница между индексом и HEAD |
| `git branch -d <branch>` | Удалить ветку (если мерджена) |
| `git branch -D <branch>` | Принудительно удалить |
| `git stash -u` | Сохраняет не‑tracked файлы |
| `git reset --soft HEAD~1` | Откатить последний commit, оставить в индексе |
| `git reset --hard HEAD~1` | Откатить и удалить изменения |
| `git reflog expire --expire=now --all && git gc --prune=now --aggressive` | Очистка reflog и сборка мусора |

---

## Итоги

* **Git** – мощная, гибкая система, где всё основано на **объектах** и **указателях**.
* Умеет работать как локально, так и с удалёнными репозиториями.
* Поддерживает сложные сценарии ветвления, слияния, перезаписи истории, stash‑ы, субмодули, LFS, хуки и др.
* Знание основных команд + понимание того, как они взаимодействуют, даст уверенность при работе над проектом и при собеседовании на позицию *Инженер по автоматизации тестирования (Java)*.

Если нужно углубиться в какую‑то деталь (например, rebase vs merge, конфиг хуков, GitFlow), дайте знать!
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Ветки</summary>
    <p style='font-size: 14px'>

## Что такое ветка в Git

| Что | Как это выглядит |
|-----|------------------|
| **Ветка** – это просто указатель (pointer) на коммит. | `master → f7a1c3d …` |
| **HEAD** – специальный указатель, который всегда указывает на *текущую* ветку (или на конкретный коммит, если HEAD «отсоединён»). | `HEAD → master` |
| **Отслеживаемая ветка** – локальная ветка, которая знает, с какой удалённой веткой синхронизироваться. | `origin/master` → `master` |

---

## Создание веток

| Команда | Что делает |
|---------|------------|
| `git branch <name>` | Создаёт ветку, но не переключает на неё. |
| `git checkout -b <name>` | Создаёт ветку и сразу переключает. |
| `git switch -c <name>` | Новый, более «чистый» способ (`git checkout` устарел). |
| `git checkout --track origin/<remote‑name>` | Создаёт локальную ветку, отслеживающую удалённую. |
| `git switch --track origin/<remote‑name>` | То же, но в стиле `switch`. |

---

## Переключение между ветками

| Команда | Что делает |
|---------|------------|
| `git checkout <name>` | Переключает на ветку. |
| `git switch <name>` | Современная версия. |
| `git checkout <commit>` | Переключает на конкретный коммит (HEAD становится «detached»). |
| `git switch --detach <commit>` | То же, но явно «detached». |

> **Detached HEAD** – это состояние, когда `HEAD` указывает не на ветку, а на конкретный коммит. Любые новые коммиты «потеряются» при переключении назад, если не сохранить их ссылкой (например, создать ветку).

---

## Удаление веток

| Команда | Что делает |
|---------|------------|
| `git branch -d <name>` | Удаляет ветку, если она полностью слияния с родителем. |
| `git branch -D <name>` | Форсированное удаление (не проверяет слияние). |
| `git push origin --delete <name>` | Удаляет ветку на удалённом репозитории. |

---

## Переименование веток

| Команда | Что делает |
|---------|------------|
| `git branch -m <old> <new>` | Переименовывает локальную ветку. |
| `git push origin :<old> <new>` | Удаляет старую ветку на сервере и пушит новую. |
| `git push -u origin <new>` | Устанавливает upstream для новой ветки. |

---

## Слияние веток

| Опция | Что делает |
|-------|------------|
| `--no-ff` | Создаёт *merge commit* даже при fast‑forward. |
| `--ff-only` | Разрешает только fast‑forward; иначе abort. |
| `--squash` | Сливает изменения в один коммит (без merge commit). |
| `--no-commit` | Сливает, но не создаёт коммит автоматически. |
| `--commit` | Создаёт коммит после `--no-commit`. |

> **Fast‑forward** – когда ветка, в которую сливаем, «потянута» до конца ветки‑источника, без параллельных изменений. В этом случае Git просто двигает указатель ветки.

---

## Rebase

| Команда | Что делает |
|---------|------------|
| `git rebase <base>` | Перемещает текущую ветку поверх `<base>`. |
| `git rebase -i <base>` | Интерактивный rebase: можно squash, reorder, edit. |
| `git rebase --abort` | Отменяет rebase (вернёт исходный HEAD). |
| `git rebase --continue` | Завершает rebase после разрешения конфликтов. |

> **Rebase** меняет историю: ваши коммиты «перезаписываются» поверх новой базы. Полезно для чистой линейной истории, но не стоит rebasing публичных веток.

---

## Конфликты

- **При merge**: Git помечает конфликтные файлы, нужно вручную исправить и `git add`, потом `git commit`.
- **При rebase**: аналогично, но после исправления нужно `git rebase --continue`.

---

## Управление удалёнными ветками

| Команда | Что делает |
|---------|------------|
| `git remote add <name> <url>` | Добавляет новый удалённый репозиторий. |
| `git fetch <remote>` | Загружает обновления, но не сливает. |
| `git pull <remote> <branch>` | Фетч + merge (или rebase, если настроено). |
| `git push <remote> <branch>` | Отправляет ветку на сервер. |
| `git push -u <remote> <branch>` | Устанавливает upstream (следует при первом пуше). |
| `git fetch --prune` | Удаляет отслеживаемые ветки, которые исчезли на сервере. |

---

## Инструменты и полезные команды

| Команда | Что делает |
|---------|------------|
| `git branch -a` | Показывает все ветки (локальные + удалённые). |
| `git branch -r` | Только удалённые. |
| `git branch -vv` | Смотрит, какие ветки отслеживают какие коммиты, и их статус. |
| `git branch --merged` | Список веток, полностью слитых с текущей. |
| `git branch --no-merged` | Список веток, которые ещё не слиты. |
| `git branch --contains <commit>` | Ветви, содержащие указанный коммит. |
| `git branch --list <pattern>` | Фильтрует ветки по паттерну. |
| `git reflog` | История перемещений указателя (HEAD, веток). |
| `git worktree add <path> <branch>` | Создаёт отдельную рабочую директорию для ветки (полезно при одновременной работе над несколькими ветками). |

---

## Конфигурация веток

| Переменная | Описание |
|------------|----------|
| `branch.autosetupmerge` | Автоматически настраивать upstream при `checkout -b` (значения: `true`/`false`). |
| `branch.autosetuprebase` | Если `always`, то `git pull` будет делать rebase вместо merge. |
| `push.default` | Определяет, что пушит `git push` без аргументов (`simple`, `current`, `matching`, `upstream`). |
| `branch.<name>.remote` / `branch.<name>.merge` | Указание, с какой удалённой веткой синхронизировать локальную. |

---

## Стратегии ветвления

| Стратегия | Когда использовать |
|-----------|--------------------|
| **GitFlow** | Для крупных проектов с релизами, hotfix‑ами, feature‑ветками. |
| **GitHub Flow** | Для небольших проектов, где все изменения проходят через pull‑request. |
| **GitLab Flow** | Комбинация GitFlow и issue‑tracking. |
| **Trunk‑based Development** | Небольшие коммиты в «trunk» (main/master), часто раздаёт с CI/CD. |

> В каждой стратегии есть свои правила именования веток: `feature/*`, `bugfix/*`, `release/*`, `hotfix/*`.

---

## Защита веток

> На платформах вроде GitHub, GitLab, Bitbucket можно настроить «branch protection»:

- **Require pull‑request reviews** – обязательный review.
- **Require status checks** – CI должен пройти.
- **Restrict who can push** – только определённые роли.
- **Prevent force push** – запрещает `git push --force`.
- **Require signed commits** – подпись коммитов.

---

## Кратко

1. **Ветка** – это указатель на коммит.
2. Создаём: `git switch -c <name>` (или `git checkout -b`).
3. Переключаем: `git switch <name>`.
4. Сливаем: `git merge` (fast‑forward, no‑ff, squash).
5. Rebase: `git rebase <base>`.
6. Конфликты решаем вручную, потом `git add` + `commit`/`rebase --continue`.
7. Удаляем: `git branch -d`/`-D`.
8. Пушим/фетчим: `git push`, `git fetch`.
9. Управляем удалёнными ветками: `origin/branch`.
10. Защищаем ветки через настройки репозитория.

> Используйте `git branch -a`, `git branch --merged`, `git reflog` для ориентации в истории.
 </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Pull requests</summary>
    <p style='font-size: 14px'>

## Pull‑Requests (PR) в Git

> **Важно:** PR – это не часть самого Git, а механизм, реализованный в сервисах вроде GitHub, GitLab, Bitbucket. Он объединяет *git‑команды* (`branch`, `merge`, `rebase`) с *проверками* и *обзорами*.

---

### 1️⃣ Что такое PR

- **Запрос на слияние** изменений из одной ветки (`feature‑branch`) в другую (`main`, `develop` и т.д.).
- Позволяет:
    - Просмотреть diff.
    - Комментировать конкретные строки.
    - Запустить CI‑пайплайны.
    - Получить одобрения от коллег.
    - Автоматически слить, если всё прошло успешно.

---

### 2️⃣ Как создаётся PR

1. **Создаём ветку** и делаем коммиты.
2. **Push** ветки в удалённый репозиторий.
3. На GitHub/GitLab/Bitbucket нажимаем **New Pull Request**.
4. Выбираем:
    - `base` – ветка, куда будет слито.
    - `compare` – ветка с вашими изменениями.
5. Заполняем заголовок и описание (можно использовать шаблон).

---

### 3️⃣ Ключевые особенности и возможности PR

| # | Особенность | Что это делает? | Как использовать? |
|---|-------------|-----------------|-------------------|
| 1 | **Ветки** | PR связывает две ветки. | Выбирайте правильные `base` и `compare`. |
| 2 | **Обзор (Review)** | Коллеги могут оставить комментарии, одобрить или запросить изменения. | Добавляйте *reviewers* и ждите их ответа. |
| 3 | **Статус‑чексы** | CI‑тесты, линтеры, сканеры. | Включите в *Branch protection* → *Require status checks*. |
| 4 | **Merge‑опции** | 3 способа объединения: <br>• **Merge commit** (сохраняет историю ветки). <br>• **Squash merge** (объединяет все коммиты в один). <br>• **Rebase merge** (переписывает историю). | Выбирается кнопкой «Merge». |
| 5 | **Fast‑forward merge** | Если ветка `feature` не разветвилась, можно просто переместить указатель. | В GitHub включается автоматически, если не конфликтует. |
| 6 | **Конфликты** | При конфликте GitHub показывает конфликтующие файлы. | Решайте вручную, `git add`, `git commit`, `git push`. |
| 7 | **Draft PR** | В стадии черновика, пока не готов к обзору. | Переключить в режиме *Draft* → *Ready for review*. |
| 8 | **Авто‑слияние** | Слияние автоматически, если все проверки и одобрения выполнены. | Включается в настройках PR. |
| 9 | **Labels, Milestones, Assignees** | Пометки, цели и ответственный. | Добавляйте через боковую панель. |
|10 | **Code Owners** | Автоматически назначают ревьюеров. | Настраиваются в `.github/CODEOWNERS`. |
|11 | **Inline comments** | Комментарии к конкретной строке кода. | Кликните строку → «Add comment». |
|12 | **Close issues automatically** | Если в PR упоминается `#123`, он автоматически закроет issue после слияния. | Пишите `Fixes #123` в описании. |
|13 | **Reopen/Close PR** | Можно закрыть без слияния или открыть снова. | Кнопки «Close pull request» / «Reopen pull request». |
|14 | **Pull‑request templates** | Стандартный формат заголовка и описания. | Создайте файл `.github/PULL_REQUEST_TEMPLATE.md`. |
|15 | **Branch protection** | Ограничения: требуются ревью, статус‑чексы, линейная история и т.п. | Настраивается в *Settings → Branches*. |

---

### 4️⃣ Как выглядит интерфейс PR

```markdown
📝 Заголовок PR
🔖 Теги: bug, feature
🗃️ Base: main
📥 Compare: feature/login
💬 Описание:
- Что изменилось
- Как тестировать

📁 Diff
- Добавлены файлы
- Изменены строки

🗒️ Комментарии
- Reviewer A: \"Можно убрать `console.log`\"
- Reviewer B: \"Approve\"

✅ Статус‑чексы
- CI: Passed
- Linter: Passed

👥 Reviewers
- @alice
- @bob

⚙️ Merge options
- Merge commit
- Squash merge
- Rebase merge
```

---

### 5️⃣ Быстрый чек‑лист перед слиянием

1. **Все коммиты** прошли тесты → status checks green.
2. **Нужные ревью** одобрены (или включено авто‑слияние).
3. **Нет конфликтов** – если есть, решите их.
4. **Описание** понятно и содержит информацию о том, как проверить.
5. **Проверено** что PR не закрывает лишние issues.

---

### 6️⃣ Итог

Pull‑request – это удобный мост между *git‑операциями* и *командной работой*. Он объединяет:

- **Технические** действия (`merge`, `rebase`, `squash`).
- **Процессные** шаги (review, CI, approvals).
- **Организационные** элементы (labels, milestones, code owners).
 </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Merge</summary>
    <p style='font-size: 14px'>

## Merge vs Rebase – кратко и полностью

| Что | Как работает | Когда удобно | Что может сломать |
|-----|--------------|--------------|-------------------|
| **Merge** | Создаёт новый *merge‑commit*, объединяя две ветки. | Сохраняет «историю» как она была, удобно для больших команд. | Не линейная история, может быть «грязной» при множестве merge‑commit. |
| **Rebase** | Перезаписывает коммиты целевой ветки так, как будто они были сделаны поверх другой ветки. | Чистая линейная история, проще читать и отлаживать. | Перезаписывает SHA‑1, поэтому нельзя делать rebase публичной ветки без `git push --force`. |

---

## Merge

```bash
git checkout feature
git merge master
```

### Что происходит

1. Git ищет **общий предок** (merge‑base) веток `feature` и `master`.
2. Создаёт новый коммит‑merge, у которого два родителя: HEAD `feature` и HEAD `master`.
3. Если изменения конфликтуют – пользователь вручную решает конфликт, затем делает `git add` и `git commit`.

### Параметры

| Параметр | Что делает |
|----------|------------|
| `--no-ff` | Принудительно создаёт merge‑commit даже при fast‑forward. |
| `--squash` | Сливает все изменения в один коммит, но **не** создаёт merge‑commit. |
| `--no-commit` | Выполняет merge, но не делает коммита, позволяет вручную изменить сообщение. |
| `-s <strategy>` | Выбирает стратегию слияния (recursive, resolve, ours, theirs, octopus). |

### Особенности

- **Сохраняет ветвление**: в логах виден «шаг» слияния, что удобно для отслеживания, где и почему появились новые фичи.
- **Безопасен для публичных веток**: никто не теряет историю, даже если кто‑то сделает `git push` до merge.
- **Может создавать «грязную» историю**: при частом слиянии веток появляется много merge‑commit‑ов.

---

## Rebase

```bash
git checkout feature
git rebase master
```

### Что происходит

1. Git берёт все коммиты из `feature`, которые не есть в `master`.
2. Пересоздаёт их один за другим поверх HEAD `master`.
3. Если конфликт – пользователь решает его, делает `git add`, затем `git rebase --continue`.

### Параметры

| Параметр | Что делает |
|----------|------------|
| `-i` | Интерактивный rebase: можно переупорядочивать, менять сообщения, squash‑ить. |
| `--onto <newbase> <upstream>` | Перемещает ветку так, чтобы она была основана на `<newbase>`, а не на `<upstream>`. |
| `--preserve-merges` / `--rebase-merges` | Сохраняет merge‑коммиты, но «переписывает» их так, чтобы они были валидны после rebase. |
| `--keep-base` | Сохраняет исходный базовый коммит в истории. |
| `-p` | То же, что `--preserve-merges` (старый синтаксис). |
| `--autosquash` | Автоматически squash‑ит коммиты, помеченные `fixup!` или `squash!`. |
| `-X ours | theirs` | При конфликте берёт изменения из выбранной стороны. |
| `-S` | Подпишет коммиты GPG. |
| `-X theirs` | При конфликте берёт изменения из ветки, на которую делается rebase. |

### Интерактивный rebase

```bash
git rebase -i HEAD~3
```

Откроется редактор с командой для каждого коммита:

| Команда | Что делает |
|---------|------------|
| `pick` | Оставить как есть. |
| `reword` | Изменить сообщение. |
| `edit` | Поставить паузу, чтобы изменить коммит. |
| `squash` | Объединить с предыдущим коммитом, объединяя сообщения. |
| `fixup` | Как `squash`, но убирает сообщение. |
| `exec <cmd>` | Выполнить произвольную команду. |
| `drop` | Удалить коммит. |

### Особенности

- **Линейная история**: после rebase ветка выглядит как одна непрерывная цепочка коммитов.
- **Перезаписывает SHA‑1**: каждый «переигранный» коммит получает новый идентификатор. Поэтому **не** делайте rebase ветки, уже отправленной в общий репозиторий.
- **Удобен для «очистки» ветки** перед `git push` или PR: можно объединить мелкие коммиты в логичную структуру.
- **Можно использовать для «переноса» изменений**: `git rebase master` – это как «поднять» feature‑ветку над последней версией master.

---

## Когда использовать Merge

- **Работа в больших командах**: сохраняет контекст ветвления, удобно видеть, где происходили merge‑commit‑ы.
- **Слияние публичных веток**: например, `master` → `release` → `master` – merge безопасен.
- **Сохранение истории**: если важно видеть «историю» как она была, не потеряв ветвление.

## Когда использовать Rebase

- **Чистая история**: если нужно, чтобы лог был линейным, без лишних merge‑commit‑ов.
- **Перед PR**: упрощает ревью, так как все изменения находятся в одном фрагменте.
- **Переосмысление ветки**: если нужно «переписать» историю перед публикацией (например, убрать лишние коммиты, поправить сообщения).

---

## Практические советы

| Ситуация | Что делать |
|----------|------------|
| **Хотите обновить feature ветку** | `git rebase master` (или `git pull --rebase`). |
| **Хотите быстро слить ветку в master** | `git merge --no-ff feature` (чтобы сохранить merge‑commit). |
| **Хотите объединить несколько коммитов** | `git rebase -i HEAD~N` → `squash`/`fixup`. |
| **Проблема с конфликтами при rebase** | `git status` → исправьте файлы → `git add` → `git rebase --continue`. |
| **Устранить конфликт без rebase** | `git merge -X theirs` (или `ours`). |
| **Переосмыслить базу ветки** | `git rebase --onto newbase oldbase feature`. |
| **Сохранять merge‑коммиты при rebase** | `git rebase -p` (или `--preserve-merges`). |
| **Не перезаписывать публичную ветку** | Не делайте rebase публичной ветки; используйте merge. |

---

## Итоговое сравнение

|   | Merge | Rebase |
|---|-------|--------|
| **История** | Сохраняет ветвление (merge‑commit). | Линейная, переписывает коммиты. |
| **Безопасность** | Безопасен для публичных веток. | Небезопасен для публичных веток без `--force`. |
| **Чистота** | Меньше «грязной» истории, но с merge‑commit. | Чистая линейная история. |
| **Влияние на коллаборацию** | Хорошо подходит для больших команд. | Лучше для небольших или отдельных PR‑ов. |
| **Сложность** | Прост в использовании. | Требует осторожности (конфликты, force‑push). |
 </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Conflicts</summary>
    <p style='font-size: 14px'>

## Как Git сообщает о конфликте

| Команда | Что показывает |
|---------|----------------|
| `git status` | Файлы с конфликтами помечаются как *unmerged* |
| `git diff --name-only --diff-filter=U` | Список конфликтующих файлов |
| `git diff` | Показывает сами конфликтные участки |

В конфликтном файле появляются маркеры:

```text
<<<<<<< HEAD
ваша версия
=======
чужая версия
>>>>>>> branch-name
```

## Как выбрать, что оставить

| Команда | Что делает |
|---------|------------|
| `git checkout --ours <file>` | Сохраняет вашу (HEAD) версию |
| `git checkout --theirs <file>` | Сохраняет чужую (branch) версию |
| `git add <file>` | Отмечает файл как «решённый» |
| `git commit` | Завершает merge/rebase/… после всех `git add` |

> **Важно** – после `git add` можно сразу `git commit`, но если хотите изменить сообщение, используйте обычный `git commit`.

## Быстрые варианты выбора

| Команда | Что делает |
|---------|------------|
| `git merge -X ours <branch>` | В конфликтных местах всегда берёт вашу версию |
| `git merge -X theirs <branch>` | В конфликтных местах всегда берёт чужую версию |
| `git merge -s ours <branch>` | Создаёт merge‑commit, но полностью игнорирует изменения из `<branch>` |
| `git merge -s resolve <branch>` | Простой три‑сторонний merge (старый вариант) |

## Как отменить/продолжить конфликтный процесс

| Сценарий | Команда |
|----------|---------|
| **Abort merge** | `git merge --abort` (или `git reset --merge`) |
| **Abort rebase** | `git rebase --abort` |
| **Abort cherry‑pick** | `git cherry-pick --abort` |
| **Abort revert** | `git revert --abort` |
| **Continue rebase/cherry‑pick/revert** | `git rebase --continue`, `git cherry-pick --continue`, `git revert --continue` |
| **Skip problematic commit** | `git rebase --skip`, `git cherry-pick --skip`, `git revert --skip` |

## Автоматическое распознавание уже решённых конфликтов

```bash
git config rerere.enabled true   # включаем
git config rerere.autoupdate true  # обновляем автоматически
```

После первого ручного разрешения конфликтов Git запомнит решение. При следующем конфликте в тех же строках он предложит применить то же решение автоматически.

## Внешние инструменты для сравнения

```bash
git mergetool --tool=meld          # запускает Meld
git mergetool --tool=vimdiff       # запускает vimdiff
git mergetool --no-prompt          # не спрашивает подтверждения
```

> **Настройки**:  
> `git config mergetool.meld.path` – путь к исполняемому файлу  
> `git config mergetool.keepBackup false` – не сохранять резервные копии

## Как изменить стиль конфликтных маркеров

```bash
git config merge.conflictStyle diff3   # показывает контекст из трёх версий
git config merge.conflictStyle merge   # обычные <<<<<<< / ======= / >>>>>>> (по умолчанию)
```

## Когда конфликт может возникнуть

- `git merge <branch>`
- `git rebase <branch>`
- `git cherry-pick <commit>`
- `git revert <commit>`
- `git pull` (если pull делает merge)

## Полезные советы

| Совету | Почему это удобно |
|--------|------------------|
| **Проверяйте конфликтные файлы**: `git diff --check` покажет, где остались маркеры |
| **Старайтесь решать конфликт сразу**: `git add` после `git checkout --ours/--theirs` |
| **Используйте `git status` регулярно**: он всегда покажет, что осталось неразрешённым |
| **Автоматизируйте с `rerere`**: экономит время при тех же конфликтах |
| **Включайте `merge.ff false`**: если хотите всегда видеть merge‑commit, даже при fast‑forward |

> **Кратко**: конфликт в Git – это просто несовпадение трёх версий одного файла. Вы вручную решаете, какую часть оставить, отмечаете файл как решённый (`git add`), и завершаете merge/rebase. Git предоставляет множество команд и опций, чтобы ускорить этот процесс и сделать его более предсказуемым.
    </p>
    </details>
</details>

<details>    
<summary style='font-size: 20px'><b>SQL</b></summary>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Создание и удаление таблиц</summary>
    <p style='font-size: 14px'>

## Создание таблиц в PostgreSQL

| Что делаем | Как это выглядит | Коротко о нюансах |
|------------|------------------|-------------------|
| **Определяем схему (schema)** | `CREATE SCHEMA my_schema;`<br>`SET search_path TO my_schema;` | Таблицы по умолчанию создаются в схеме `public`, но можно менять. |
| **Создаём таблицу** | ```sql<br/>CREATE TABLE my_table ( <br>  id          SERIAL PRIMARY KEY, <br>  name        TEXT NOT NULL, <br>  age         INT CHECK (age >= 0), <br>  created_at  TIMESTAMP DEFAULT CURRENT_TIMESTAMP <br>);<br>``` | - `SERIAL` → автоинкрементная колонка (создаёт последовательность).<br>- `PRIMARY KEY` → уникальный индекс + NOT NULL.<br>- `CHECK` → пользовательская валидация.<br>- `DEFAULT` → значение, если не указано. |
| **Типы данных** | `INT, BIGINT, SERIAL, BIGSERIAL, TEXT, VARCHAR(n), CHAR(n), DATE, TIMESTAMP, BOOLEAN, NUMERIC, UUID, JSONB` | Выбирайте тип, который лучше подходит. `JSONB` хранит бинарный JSON. |
| **Ограничения** | `UNIQUE`, `NOT NULL`, `CHECK`, `FOREIGN KEY`, `REFERENCES` | `FOREIGN KEY` может ссылаться на другую таблицу: `FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE`. |
| **Индексы (необязательно в CREATE TABLE)** | `CREATE INDEX idx_name ON my_table(name);` | Автоматически создаётся индекс для `PRIMARY KEY` и `UNIQUE`. |
| **Параметры таблицы** | `TABLESPACE`, `WITH (OIDS=FALSE)`, `TABLESPACE` | Обычно не нужны, но полезны для больших таблиц или конкретных файловых систем. |
| **Условие «если не существует»** | `CREATE TABLE IF NOT EXISTS my_table (...);` | Позволяет избежать ошибки, если таблица уже есть. |
| **Наследование (нечасто используется)** | `CREATE TABLE child (LIKE parent INCLUDING ALL);` | Позволяет копировать структуру родительской таблицы. |

> **Совет**: всегда задавайте `PRIMARY KEY` и/или `UNIQUE` там, где нужно гарантировать уникальность строк. Это ускорит поиск и обеспечит целостность данных.

---

## Удаление таблиц

| Что делаем | Как это выглядит | Коротко о нюансах |
|------------|------------------|-------------------|
| **Удаляем таблицу** | `DROP TABLE my_table;` | Удаляет саму таблицу и все её индексы. |
| **Условие «если существует»** | `DROP TABLE IF EXISTS my_table;` | Не генерирует ошибку, если таблица отсутствует. |
| **Удаление с каскадом** | `DROP TABLE my_table CASCADE;` | Удаляет таблицу и все объекты, которые на неё ссылаются (вьюхи, функции, другие таблицы с FK). |
| **Удаление без каскада** | `DROP TABLE my_table RESTRICT;` | Отклонит удаление, если есть внешние ключи или другие зависимости. |
| **Удаление схемы** | `DROP SCHEMA my_schema CASCADE;` | Удаляет схему и все объекты внутри неё. |
| **Проверка зависимостей** | `SELECT * FROM pg_depend WHERE refobjid = 'my_table'::regclass;` | Показывает, какие объекты зависят от таблицы. |

> **Важный момент**: `DROP TABLE` **не** удаляет связанные последовательности, если вы использовали `SERIAL`. Если хотите удалить и последовательность, используйте `DROP SEQUENCE my_table_id_seq;` либо `DROP TABLE my_table CASCADE;`.

---

## Быстрый чек‑лист перед созданием/удалением

| Проверка | Команда |
|----------|---------|
| Таблица уже есть? | `SELECT to_regclass('my_table');` |
| Нужно ли удалять зависимости? | `SELECT * FROM pg_depend WHERE refobjid = 'my_table'::regclass;` |
| Нужно ли хранить в отдельной tablespace? | `CREATE TABLESPACE ts_name LOCATION '/path';`<br>`CREATE TABLE my_table (...) TABLESPACE ts_name;` |

---

### Кратко

1. **Создаём таблицу** – `CREATE TABLE`, указываем колонки, типы, ограничения.
2. **Опционально** – `IF NOT EXISTS`, указываем схему, таблисвпейс, наследование.
3. **Удаляем** – `DROP TABLE`, добавляем `IF EXISTS`, `CASCADE`/`RESTRICT`.
4. **Управляем зависимостями** – проверяем `pg_depend`, используем `CASCADE` при необходимости.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Простые операторы</summary>
    <p style='font-size: 14px'>

## Простые операторы SELECT в PostgreSQL
*(без `JOIN`, `GROUP BY`, агрегатных функций и сложных подзапросов)*

Ниже – краткое руководство по основным конструкциям, которые обычно встречаются в простых запросах.  
Все примеры используют таблицу **`employees`**:

```sql
CREATE TABLE employees (
    id          SERIAL PRIMARY KEY,
    first_name  TEXT,
    last_name   TEXT,
    age         INT,
    salary      NUMERIC,
    department  TEXT
);
```

---

### 1. `WHERE` – фильтрация строк
```sql
SELECT * FROM employees
WHERE age > 30
  AND department = 'Sales';
```

* **NULL‑значения**
  ```sql
  WHERE department IS NULL          -- только строки без отдела
  WHERE department IS NOT NULL
  ```
* **Операторы сравнения**  
  `=`, `<>`, `!=`, `<`, `>`, `<=`, `>=`
* **`IN` / `NOT IN`**
  ```sql
  WHERE department IN ('HR', 'IT')
  ```
* **`BETWEEN`** (см. ниже)
* **`LIKE` / `ILIKE`** (см. ниже)
* **`IS DISTINCT FROM / IS NOT DISTINCT FROM`**  
  безопасный способ сравнения с `NULL`

---

### 2. `LIKE` – шаблонное сравнение строк
```sql
SELECT * FROM employees
WHERE last_name LIKE 'S%';          -- заканчивается на S
```

| Оператор | Пояснение | Пример |
|----------|-----------|--------|
| `%` | Любое количество символов (включая 0) | `S%` – любые фамилии, начинающиеся на S |
| `_` | Один любой символ | `S__` – любые фамилии из 3 букв, начинающиеся на S |
| `ESCAPE` | Указываем символ‑экранирование | `LIKE 'C\\_%' ESCAPE '\\'` – буква `C` + символ `_` + любые символы |
| `ILIKE` | То же, но без учёта регистра | `WHERE last_name ILIKE 's%'` |
| `SIMILAR TO` | Похожий на `REGEXP` | `WHERE last_name SIMILAR TO 'S[aeiou]%'` |

---

### 3. `BETWEEN` – диапазон
```sql
SELECT * FROM employees
WHERE age BETWEEN 25 AND 35;      -- 25 ≤ age ≤ 35
```

* Включительно: границы входят.
* Можно использовать в `NOT BETWEEN`.

---

### 4. `ORDER BY` – сортировка
```sql
SELECT * FROM employees
ORDER BY age DESC, last_name ASC;
```

* **Направления**: `ASC` (по умолчанию), `DESC`
* **NULL‑значения**
  ```sql
  ORDER BY salary NULLS FIRST  -- NULL‑ы будут первыми
  ORDER BY salary NULLS LAST   -- NULL‑ы будут последними
  ```
* **Сортировка по алиасу**
  ```sql
  SELECT age, salary, age*salary AS product
  FROM employees
  ORDER BY product DESC;
  ```
* **Сортировка в нескольких колонках** – перечисление через запятую.

---

### 5. `LIMIT` и `OFFSET` – ограничение количества строк
```sql
SELECT * FROM employees
ORDER BY id
LIMIT 10;            -- первые 10 строк
```

```sql
SELECT * FROM employees
ORDER BY id
LIMIT 10 OFFSET 20;  -- 21‑30 строка
```

* `OFFSET` может быть 0 (по умолчанию).
* `LIMIT 0` возвращает пустой набор.
* `LIMIT ALL` (по умолчанию) – без ограничения.

---

### 6. `DISTINCT` – уникальные строки
```sql
SELECT DISTINCT department FROM employees;
```

* Можно использовать с несколькими колонками:  
  `SELECT DISTINCT department, salary FROM employees;`

---

### 7. `SELECT`‑выражения (подзапросы в `WHERE`)
```sql
SELECT * FROM employees
WHERE salary > (
    SELECT AVG(salary) FROM employees
);
```

* Подзапрос может возвращать скалярное значение (`SELECT ... LIMIT 1`) или набор строк (`IN`).

---

### 8. `NOT` – отрицание
```sql
SELECT * FROM employees
WHERE NOT department = 'HR';
-- то же самое:
SELECT * FROM employees
WHERE department <> 'HR';
```

---

### 9. `OR` / `AND` – логические операторы
```sql
SELECT * FROM employees
WHERE (age < 25 OR age > 60)
  AND department = 'IT';
```

* Приоритет `AND` выше `OR`; можно использовать скобки для ясности.

---

### 10. Регулярные выражения (необязательно, но полезно)
```sql
SELECT * FROM employees
WHERE last_name ~ '^S';          -- начинается с S (регистрозависимый)
WHERE last_name ~* '^s';         -- начинается с s (регистронезависимый)
```

* `~` – регистрозависимое совпадение
* `~*` – регистронезависимое
* `!~` / `!~*` – отрицание

---

## Краткое резюме

| Оператор | Что делает | Ключевые особенности |
|----------|------------|----------------------|
| `WHERE` | Фильтрует строки | NULL‑сравнение, `IN`, `BETWEEN`, `LIKE`, `SIMILAR TO`, `REGEXP` |
| `LIKE` | Шаблонное сравнение | `%`, `_`, `ESCAPE`, `ILIKE` |
| `BETWEEN` | Диапазон | Включительно |
| `ORDER BY` | Сортировка | `ASC/DESC`, `NULLS FIRST/LAST`, сортировка по алиасу |
| `LIMIT` / `OFFSET` | Ограничение | `LIMIT 0`, `OFFSET 0` |
| `DISTINCT` | Уникальные строки | С несколькими колонками |
| `NOT` | Отрицание | `<>`, `NOT IN`, `NOT BETWEEN` |
| `OR` / `AND` | Логика | Приоритет, скобки |
| Регулярные выражения | Сложные шаблоны | `~`, `~*`, `!~`, `!~*` |
 </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>JOIN, GROUP BY и агрегирующии функции</summary>
    <p style='font-size: 14px'>

## 1. JOIN‑ы в PostgreSQL

| Тип | Синтаксис | Что делает |
|-----|-----------|------------|
| **INNER JOIN** (по умолчанию) | `FROM a JOIN b ON a.id = b.a_id` | Возвращает только совпадающие строки. |
| **LEFT/LEFT OUTER JOIN** | `FROM a LEFT JOIN b ON a.id = b.a_id` | Все строки из `a`. Если нет совпадения – `NULL` в колонках `b`. |
| **RIGHT/RIGHT OUTER JOIN** | `FROM a RIGHT JOIN b ON a.id = b.a_id` | Всё из `b`, а `a` может быть `NULL`. |
| **FULL OUTER JOIN** | `FROM a FULL JOIN b ON a.id = b.a_id` | Всё из обоих, `NULL` где нет совпадения. |
| **CROSS JOIN** | `FROM a CROSS JOIN b` | Картезианский продукт (все комбинации). |
| **NATURAL JOIN** | `FROM a NATURAL JOIN b` | Автоматически соединяет по всем колонкам с одинаковыми именами. |
| **JOIN … USING** | `FROM a JOIN b USING (id)` | Сокращённый вариант `ON a.id = b.id`. |
| **JOIN … LATERAL** | `FROM a JOIN LATERAL (SELECT … FROM b WHERE b.a_id = a.id) AS sub ON true` | Подзапрос может ссылаться на строки из `a`. |

> **Порядок соединения**  
> Планировщик PostgreSQL может менять порядок JOIN‑ов. Если нужен строгий порядок, оборачивайте в скобки:  
> `FROM (a JOIN b ON …) JOIN c ON …`.

> **Псевдонимы**  
> Любой объект в `FROM` (таблица, подзапрос, CTE) может иметь псевдоним:  
> `FROM users u JOIN orders o ON u.id = o.user_id`.

---

## 2. GROUP BY

### Базовый синтаксис
```sql
SELECT col1, col2, SUM(col3) AS total
FROM   table
GROUP BY col1, col2;
```

* **Все неагрегированные колонки** должны быть перечислены в `GROUP BY` (или по их позициям: `GROUP BY 1, 2`).
* Можно группировать по выражениям: `GROUP BY (first_name || ' ' || last_name)`.
* Группировка по **композитным типам** (`ROW(col1, col2)`).

> **HAVING**  
> Фильтр после группировки:  
> `HAVING COUNT(*) > 5`.

> **ORDER BY**  
> Можете сортировать по любому выражению, даже не включённому в SELECT, но обычно сортируют по группировочным колонкам или агрегатам.

---

## 3. Агрегатные функции

| Функция | Что считает / возвращает | Особенности |
|---------|--------------------------|-------------|
| `COUNT(*)` | Кол‑во строк | Всегда считает, даже `NULL`. |
| `COUNT(col)` | Кол‑во не‑NULL значений | |
| `COUNT(DISTINCT col)` | Уникальные не‑NULL | |
| `SUM(col)` | Сумма | Может использовать `FILTER (WHERE …)` |
| `AVG(col)` | Среднее | |
| `MIN(col)`, `MAX(col)` | Минимум/максимум | |

> **FILTER** (PostgreSQL 9.4+)  
> Позволяет агрегировать только часть строк:
> ```sql
> SELECT SUM(amount) FILTER (WHERE paid = true) AS paid_total
> FROM orders;
> ```

> **DISTINCT** внутри агрегата  
> `SUM(DISTINCT col)` – суммирует только уникальные значения.

---

## 4. Практические советы

1. **Не используйте агрегаты в `WHERE`.** Они вычисляются после `WHERE`. Вместо этого используйте `HAVING` или оконные функции.
2. **`GROUP BY` + `DISTINCT ON`** – не нужно, они служат разным целям.
3. **Имена колонок** в `SELECT` после `GROUP BY` можно переименовать:
   ```sql
   SELECT col1 AS c1, SUM(col2) AS total
   FROM t
   GROUP BY col1;
   ```
4. **Позиции в `GROUP BY`** удобно использовать, если колонок много:
   ```sql
   SELECT col1, col2, SUM(col3)
   FROM t
   GROUP BY 1, 2;
   ```
   
---

### Мини‑пример

```sql
-- Считаем количество заказов и их общую сумму по каждому пользователю,
-- включая пользователей без заказов (LEFT JOIN) и выводим обобщённые строки.
SELECT u.id,
       u.name,
       COUNT(o.id)          AS orders_cnt,
       SUM(o.total_amount)  AS total_sum
FROM   users u
LEFT JOIN orders o ON o.user_id = u.id
GROUP BY ROLLUP (u.id, u.name)
HAVING COUNT(o.id) > 0 OR GROUPING(u.id) = 1;   -- Показываем только группы с заказами и итог
ORDER BY u.id;
```

* `ROLLUP` создаёт строку‑итог: `GROUPING(u.id)=1`.
* `HAVING` фильтрует только нужные группы.

---

**Итого:**
- **JOIN** – связывает таблицы (INNER, LEFT, RIGHT, FULL, CROSS, NATURAL, LATERAL).
- **GROUP BY** – группирует строки, можно использовать ROLLUP/CUBE/GROUPING SETS.
- **Агрегатные функции** – `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`, `ARRAY_AGG`, `STRING_AGG`, `JSON_AGG`, `BOOL_AND`, `BOOL_OR`, `FILTER`, `DISTINCT`, оконные аналоги.
 </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Insert</summary>
    <p style='font-size: 14px'>

## INSERT в PostgreSQL – полное руководство

> **Кратко**: `INSERT` добавляет строки в таблицу.  
> **Особенности**: множественные строки, `DEFAULT`, `RETURNING`, `ON CONFLICT` (upsert), `INSERT … SELECT`, CTE‑ы, триггеры, производительность, ограничения.

---

### 1 Базовый синтаксис

```sql
INSERT INTO schema.table_name (col1, col2, col3)
VALUES (val1, val2, val3);
```

* Если список колонок опущен, значения вставляются в порядке, объявленном в таблице.
* Для каждой строки можно указать набор значений.

---

### 2 Множественные строки

```sql
INSERT INTO users (id, name, email)
VALUES
  (1, 'Alice', 'alice@example.com'),
  (2, 'Bob',   'bob@example.com'),
  (3, 'Carol', 'carol@example.com');
```

* Операция атомарна: либо все строки вставятся, либо ни одна (если возникнет ошибка).

---

### 3 `DEFAULT` и `DEFAULT VALUES`

```sql
-- Заполнить все колонки их дефолтными значениями
INSERT INTO logs DEFAULT VALUES;

-- Оставить конкретную колонку с дефолтом
INSERT INTO users (name, email) VALUES ('Dave', DEFAULT);
```

* Полезно, когда колонка имеет `DEFAULT` выражение или автоинкремент (`SERIAL`, `IDENTITY`).

---

### 4 `RETURNING`

```sql
INSERT INTO orders (customer_id, total)
VALUES (42, 99.99)
RETURNING id, created_at;
```

* Возвращает указанные колонки из вставленных строк – удобно для получения сгенерированных ID, timestamps и т.п.

---

### 5 `INSERT … SELECT`

```sql
INSERT INTO archive_orders (order_id, customer_id, total, archived_at)
SELECT id, customer_id, total, NOW()
FROM orders
WHERE status = 'completed';
```

* Позволяет копировать данные из одной таблицы в другую (или в ту же таблицу с фильтрацией/модификацией).

---

### 6 Ограничения и валидация

* **NOT NULL** – вставка `NULL` в такую колонку завершится ошибкой.
* **CHECK** – проверяет условие; вставка, нарушающая правило, отклоняется.
* **FOREIGN KEY** – вставка строки с внешним ключом, который не существует в родительской таблице, приводит к ошибке (если не указано `ON DELETE SET NULL` и т.п.).
* **UNIQUE / PRIMARY KEY** – конфликт обрабатывается `ON CONFLICT` или приводит к ошибке.

---

### 7 Транзакции

* Вставка (`INSERT`) – атомарна внутри транзакции.
* Если вы откатываете транзакцию (`ROLLBACK`), все вставленные строки удаляются.
* При `COMMIT` изменения становятся видимыми другим сессиям.

---

## Пример полного сценария

```sql
-- 1. Создание таблицы
CREATE TABLE users (
    id          serial PRIMARY KEY,
    name        text NOT NULL,
    email       text UNIQUE NOT NULL,
    created_at  timestamp with time zone DEFAULT now()
);

-- 2. Вставка одной строки
INSERT INTO users (name, email)
VALUES ('Alice', 'alice@example.com')
RETURNING id;

-- 3. Множественная вставка
INSERT INTO users (name, email)
VALUES
  ('Bob',   'bob@example.com'),
  ('Carol', 'carol@example.com');

-- 4. Upsert: обновляем email, если ID уже есть
INSERT INTO users (id, name, email)
VALUES (1, 'Alice', 'alice@new.com')
ON CONFLICT (id) DO UPDATE
SET email = EXCLUDED.email;

-- 5. INSERT FROM SELECT
INSERT INTO users (name, email)
SELECT name, email
FROM temp_new_users
WHERE email NOT LIKE '%@spam%';

-- 6. COPY (пример из файла)
COPY users (name, email) FROM '/tmp/users.csv' WITH (FORMAT csv);
```
</p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Update</summary>
    <p style='font-size: 14px'>

## UPDATE в PostgreSQL – кратко и понятно

| Что | Как это работает | Ключевые нюансы |
|-----|------------------|-----------------|
| **Базовый синтаксис** | `UPDATE table_name SET column1 = value1, column2 = value2 WHERE condition;` | Обновляется **одна** таблица за раз. |
| **SET‑выражения** | `SET column = expression`<br>`SET (col1, col2) = (val1, val2)`<br>`SET column = DEFAULT` | Можно менять несколько колонок одновременно, использовать выражения, `DEFAULT` (значение по умолчанию), `NULL`. |
| **WHERE** | Ограничивает строки, которые будут обновлены. | Если `WHERE` опустить – обновятся **все** строки. |
| **RETURNING** | `... RETURNING *;` | Возвращает обновлённые строки (полностью или выбранные колонки). Полезно для получения новых значений без дополнительного `SELECT`. |
| **LIMIT / ORDER BY** | Начиная с PostgreSQL 13: `UPDATE ... SET ... WHERE ... ORDER BY ... LIMIT n;` | Позволяет обновить только часть строк (например, первые 10). |
| **FROM** | `UPDATE table1 SET col = t2.value FROM table2 t2 WHERE table1.id = t2.id;` | Позволяет обновлять, используя данные из другой таблицы (join). |
| **UPDATE JSON/ARRAY** | `SET json_col = json_col || '{\"new\":\"val\"}'`<br>`SET arr_col = array_append(arr_col, 5)` | Работают обычные функции/операторы PostgreSQL. |
| **Row‑level locks** | При UPDATE строка блокируется автоматически. | Можно явно задать `FOR UPDATE` в SELECT перед UPDATE, чтобы получить тот же lock. |
| **Проблемы и советы** | 1. **Индексы** – используйте WHERE‑индексы, иначе обновится много строк.<br>2. **Большие обновления** – делайте в пакетах, чтобы не держать lock над множеством строк долго.<br>3. **Проверка ограничений** – все CHECK/FOREIGN‑KEY проверяются после каждой строки.<br>4. **Порядок** – если используете `ORDER BY`, будьте внимательны: порядок обновления может влиять на результаты. |

### Быстрый чек‑лист

| # | Что проверить/запомнить | Почему |
|---|------------------------|--------|
| 1 | `WHERE` обязательно, если не всё. | Иначе обновятся все строки. |
| 2 | `RETURNING` – удобно, но не всегда нужно. | Позволяет сразу увидеть результат. |
| 3 | `FROM` нужен, если берёте данные из другой таблицы. | Без него нельзя сделать join. |
| 4 | `LIMIT/OFFSET` доступно только с 13‑й версии. | Нужно знать версию сервера. |
| 5 | `DEFAULT` ставит значение по умолчанию. | Полезно, если нужно сбросить колонку. |
| 6 | Триггеры и каскадные FK могут изменить результат. | Не забывайте о них при проектировании. |
| 7 | Вставка + обновление – используйте `ON CONFLICT DO UPDATE`. | Это более эффективный способ upsert. |

> `UPDATE` – это команда, которая меняет строки в одной таблице. Вы задаёте, какие колонки менять, какие значения им поставить, фильтруете строки через `WHERE`, а при необходимости используете `FROM`, `LIMIT`, `RETURNING` и т.д. PostgreSQL гарантирует, что операции выполняются атомарно и не портят данные, но стоит помнить про индексы, триггеры и ограничения.
</p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>JSONB</summary>
    <p style='font-size: 14px'>

## Что такое `jsonb` в PostgreSQL

| Что | Как работает |
|-----|--------------|
| **Тип данных** | Хранит JSON в *бинарном* формате (не в строке). |
| **Преимущества** | Быстрее чтения/записи, меньше места, можно индексировать, поддерживает быстрые операции. |
| **Особенности** | 1. Удаляет дубликаты ключей и сортирует их лексикографически. <br>2. Не сохраняет порядок ключей. <br>3. При обновлении создаётся **новый** объект (не‑in‑place). <br>4. Можно хранить `null`‑значения. |

---

## Основные операторы

| Оператор | Что делает | Пример |
|----------|------------|--------|
| `->` | Возвращает JSON‑значение как `jsonb`. | `data->'address'` |
| `->>` | Возвращает JSON‑значение как текст. | `data->>'city'` |
| `#>` | Возвращает вложенный объект/массив по пути (массив индексов/ключей). | `data#>'{address,street}'` |
| `#>>` | Как `#>` но как текст. | `data#>>'{address,zip}'` |
| `||` | Конкатенация/слияние двух `jsonb`. | `data || '{\"age\":30}'` |
| `@>` | Проверяет, содержит ли левый `jsonb` правый. | `data @> '{\"name\":\"John\"}'` |
| `<@` | Проверяет, содержится ли левый `jsonb` в правом. | `data <@ '{\"name\":\"John\",\"age\":30}'` |
| `?` | Проверка наличия ключа. | `data ? 'name'` |
| `?|` | Проверка наличия **любого** из ключей. | `data ?| array['name','age']` |
| `?&` | Проверка наличия **всех** указанных ключей. | `data ?& array['name','age']` |
| `@?` | JSONPath‑поиск (требует `jsonb_path_ops` или `jsonb_ops`). | `data @? '$.address.street'` |
| `@@` | Текстовый поиск по JSONPath. | `data @@ '$.address.street ? (@ like_regex \".*Main.*\")'` |

---

## Полезные функции

| Функция | Зачем | Пример |
|---------|-------|--------|
| `jsonb_each(jsonb)` | Возвращает пары `key, value` как строки | `SELECT * FROM jsonb_each(data)` |
| `jsonb_each_text(jsonb)` | То же, но значения как `text` | `SELECT * FROM jsonb_each_text(data)` |
| `jsonb_object_keys(jsonb)` | Список ключей | `SELECT jsonb_object_keys(data)` |
| `jsonb_array_elements(jsonb)` | Разворачивает массив | `SELECT * FROM jsonb_array_elements(data)` |
| `jsonb_array_length(jsonb)` | Длина массива | `SELECT jsonb_array_length(data->'items')` |
| `jsonb_typeof(jsonb)` | Возвращает тип (`object`, `array`, `string`, `number`, `boolean`, `null`) | `SELECT jsonb_typeof(data)` |
| `jsonb_set(jsonb, path, new_value)` | Обновляет вложенное значение (возвращает новый JSON) | `SELECT jsonb_set(data, '{address,zip}', '\"12345\"')` |
| `jsonb_insert(jsonb, path, new_value, before)` | Вставляет элемент в массив | `SELECT jsonb_insert(data, '{items}', '\"new\"', false)` |
| `jsonb_strip_nulls(jsonb)` | Удаляет все ключи с `null` | `SELECT jsonb_strip_nulls(data)` |
| `jsonb_pretty(jsonb)` | Красивый вывод | `SELECT jsonb_pretty(data)` |
| `jsonb_path_query(jsonb, jsonpath)` | Выполняет JSONPath‑запрос | `SELECT jsonb_path_query(data, '$.address[*].city')` |
| `jsonb_path_exists(jsonb, jsonpath)` | Проверяет наличие результата JSONPath | `SELECT jsonb_path_exists(data, '$.address.street')` |

---

## Лучшие практики

1. **Используйте `jsonb`, а не `json`** – быстрее, меньше места, индексируемость.
2. **Не храните в `jsonb` только простые ключ‑значения** – если структура известна, лучше использовать обычные колонки.
3. **Обновления**: `jsonb_set` возвращает новый объект, поэтому обновление одной записи может затронуть несколько вложенных полей – учитывайте это при расчёте нагрузки.
4. **Индексы**: создавайте GIN‑индексы только для тех запросов, где они действительно нужны. Не создавайте индексы на каждый ключ – это быстро будет «плохим».
5. **Проверка наличия ключа**: `?`, `?|`, `?&` работают быстрее, чем `jsonb_exists`/`jsonb_exists_any`.
6. **JSONPath** (`@?`, `@@`) удобно для сложных запросов, но требует `jsonb_path_ops`/`jsonb_ops`. Если нужна частая проверка, лучше использовать обычные колонки.
7. **Удаление `null`**: `jsonb_strip_nulls` полезно для экономии места.
8. **Преобразование**: `jsonb_build_object` и `jsonb_build_array` создают JSON быстро и читаемо.

---

## Мини‑пример

```sql
CREATE TABLE orders (
    id serial PRIMARY KEY,
    data jsonb NOT NULL
);

INSERT INTO orders (data) VALUES
('{
    \"id\": 1,
    \"customer\": {\"name\": \"Alice\", \"city\": \"NY\"},
    \"items\": [
        {\"product\":\"Book\",\"qty\":2},
        {\"product\":\"Pen\",\"qty\":5}
    ]
}');

-- Найти заказ, где в адресе есть город \"NY\"
SELECT * FROM orders WHERE data->'customer'->>'city' = 'NY';

-- Проверить наличие ключа \"customer\"
SELECT * FROM orders WHERE data ? 'customer';

-- Добавить новый товар
UPDATE orders
SET data = jsonb_set(data, '{items,2}', '{\"product\":\"Notebook\",\"qty\":1}')
WHERE id = 1;

-- Слияние с новым объектом
UPDATE orders
SET data = data || '{\"status\":\"shipped\"}'
WHERE id = 1;

-- Индексировать ключ \"city\" для быстрого поиска
CREATE INDEX idx_city ON orders ((data->'customer'->>'city'));
```

---

### Итог

`jsonb` – мощный тип для хранения и работы с полуструктурированными данными в PostgreSQL. Он предоставляет множество операторов и функций, позволяет создавать эффективные индексы и быстро выполнять сложные запросы. Главное – правильно оценить, какие части данных действительно нужно хранить в `jsonb`, а какие лучше вынести в обычные колонки.
</p>
    </details>
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
    <summary style='font-size: 16px'>DataProvider в TestNG</summary>
    <p style='font-size: 14px'>

## Что такое DataProvider в TestNG

`@DataProvider` – это способ передавать в тест множество наборов данных.  
Тест запускается столько раз, сколько строк вернёт `DataProvider`, и каждый раз получает
одно из этих наборов.

### Почему это удобно?
* **Повторное использование кода** – один тест может проверять разные варианты входных данных.
* **Чистый код** – данные вынесены из теста, а не прописаны вручную в `@Test`.
* **Лёгкая интеграция** – можно подключать данные из CSV, Excel, базы и т.п.

---

## Как это работает

```java
public class LoginTest {

    // 1. Определяем DataProvider
    @DataProvider(name = \"loginData\")
    public Object[][] loginData() {
        return new Object[][]{
                {\"user1\", \"pass1\"},
                {\"user2\", \"wrong\"},
                {\"admin\", \"admin123\"}
        };
    }

    // 2. Тест, который использует DataProvider
    @Test(dataProvider = \"loginData\")
    public void testLogin(String username, String password) {
        // логика теста
        System.out.printf(\"Пробуем логин: %s / %s%n\", username, password);
        // assert … (проверка результата)
    }
}
```

1. **`@DataProvider`**  
   Метод должен возвращать `Object[][]`.  
   Каждый вложенный массив – один набор параметров.

2. **`@Test(dataProvider = \"имя\")`**  
   Тест получает параметры из указанного `DataProvider`.

---

## Ключевые моменты

| Что | Как делается |
|-----|--------------|
| **Имя DataProvider** | `@DataProvider(name = \"myProvider\")` |
| **Приём параметров** | Можно передавать `Method m` для доступа к методу теста. |
| **Сложные типы** | Можно возвращать объекты, списки, карты и т.д. |
| **Группы** | `@DataProvider` может быть связан с группами через `@Test(groups = {\"fast\"})`. |
| **Параллельный запуск** | При `parallel = true` в `@DataProvider` каждый набор данных выполняется в отдельном потоке. |

---

## Пример с параметром `Method`

```java
@DataProvider(name = \"methodBased\")
public Object[][] methodBased(Method m) {
    if (m.getName().equals(\"testA\")) {
        return new Object[][]{{1, 2}, {3, 4}};
    }
    return new Object[][]{{5, 6}};
}

@Test(dataProvider = \"methodBased\")
public void testA(int a, int b) { /* ... */ }

@Test(dataProvider = \"methodBased\")
public void testB(int a, int b) { /* ... */ }
```

---

## Итог

`@DataProvider` позволяет легко и гибко управлять входными данными тестов, делая их более читаемыми и поддерживаемыми.  
С его помощью можно быстро проверить множество сценариев, не дублируя код.
    </p>
    </details>
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>SoftAssert в TestNG</summary>
    <p style='font-size: 14px'>

## Soft Assert в TestNG

### Что это?

`SoftAssert` позволяет собирать все ошибки в тесте, а не останавливаться на первой.  
Тест продолжает выполняться, а в конце можно проверить все собранные утверждения.

### Когда использовать?

- Когда нужно проверить несколько полей/условий одновременно.
- Когда дальнейшие шаги теста не зависят от предыдущих ошибок.

### Как это работает

```java
import org.testng.asserts.SoftAssert;
import org.testng.annotations.Test;

public class SoftAssertExample {

    @Test
    public void testMultipleConditions() {
        SoftAssert soft = new SoftAssert();

        // 1. Проверяем заголовок
        soft.assertEquals(getPageTitle(), \"Home\", \"Wrong title\");

        // 2. Проверяем наличие элемента
        soft.assertTrue(isElementVisible(\"loginBtn\"), \"Login button not visible\");

        // 3. Проверяем текст кнопки
        soft.assertEquals(getButtonText(\"loginBtn\"), \"Login\", \"Button text wrong\");

        // В конце собираем результаты
        soft.assertAll();   // Если хоть одно assertAll() не выполнено → тест FAIL
    }

    // Моки для примера
    private String getPageTitle() { return \"Home\"; }
    private boolean isElementVisible(String id) { return true; }
    private String getButtonText(String id) { return \"Login\"; }
}
```

### Ключевые моменты

| Функция | Описание |
|--------|----------|
| `assertEquals`, `assertTrue`, `assertFalse` и др. | Сохраняют ошибку, но не бросают исключение сразу |
| `assertAll()` | Проверяет все собранные ошибки. Если есть хотя бы одна, бросает `AssertionError` и тест считается неуспешным |

### Преимущества

- **Полный отчёт**: видите все ошибки сразу, а не одну за одной.
- **Удобно для UI‑тестов**: можно проверить сразу несколько полей/элементов.

### Недостатки

- **Нужно помнить о `assertAll()`** – иначе ошибки останутся незамеченными.
- **Тест может продолжить выполнение после критической ошибки**, что иногда не желаемо.

---

**Итого:**  
`SoftAssert` – инструмент, который позволяет собирать все ошибки в тесте и проверять их одновременно. Это удобно, когда нужно проверить несколько условий в одном тестовом методе, но важно не забыть вызвать `assertAll()` в конце.
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
    <summary style='font-size: 16px'>Настройка Kafka</summary>
    <p style='font-size: 14px'>

## Как поднять Kafka локально (Linux/Mac / Windows)

> **Требования**  
> • Java 8+ (JDK)  
> • 1 ГБ свободного места (для логов)  
> • 1‑2 ГБ оперативной памяти

| Шаг | Что делаем | Команда / описание |
|-----|------------|--------------------|
| 1 | Установить Java | `sudo apt install openjdk-11-jdk`  (или `brew install openjdk@11`) |
| 2 | Скачать Kafka | `wget https://downloads.apache.org/kafka/3.7.0/kafka_2.13-3.7.0.tgz` |
| 3 | Распаковать | `tar -xzf kafka_2.13-3.7.0.tgz && cd kafka_2.13-3.7.0` |
| 4 | Настроить `config/server.properties` | Переменные ниже |
| 5 | Запустить Zookeeper | `bin/zookeeper-server-start.sh config/zookeeper.properties` |
| 6 | Запустить Kafka‑broker | `bin/kafka-server-start.sh config/server.properties` |
| 7 | Создать тему (topic) | `bin/kafka-topics.sh --create --topic test --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1` |
| 8 | Проверить | `bin/kafka-console-producer.sh --topic test --bootstrap-server localhost:9092` → пишем сообщения, потом: `bin/kafka-console-consumer.sh --topic test --from-beginning --bootstrap-server localhost:9092` |

---

## Параметры `server.properties` (с нуля)

> **Файл**: `config/server.properties`

| Параметр | Значение | Что делает |
|----------|----------|------------|
| `broker.id` | `0` | Уникальный ID брокера в кластере |
| `listeners` | `PLAINTEXT://:9092` | Порт, на котором слушает клиент |
| `log.dirs` | `/tmp/kafka-logs` | Путь к директориям логов |
| `num.network.threads` | `3` | Кол‑во потоков для сети |
| `num.io.threads` | `8` | Кол‑во потоков для ввода/вывода |
| `socket.send.buffer.bytes` | `102400` | Буфер отправки |
| `socket.receive.buffer.bytes` | `102400` | Буфер приёма |
| `socket.request.max.bytes` | `104857600` | Максимальный размер запроса (100 МБ) |
| `log.retention.hours` | `168` | Сколько часов хранить логи (7 дней) |
| `log.segment.bytes` | `1073741824` | Размер сегмента (1 ГБ) |
| `zookeeper.connect` | `localhost:2181` | Адрес Zookeeper |
| `auto.create.topics.enable` | `true` | Авто‑создание тем, если они не существуют |
| `delete.topic.enable` | `true` | Разрешить удалять темы |
| `group.initial.rebalance.delay.ms` | `0` | Задержка перед ребалансировкой групп |
| `offsets.topic.replication.factor` | `1` | Репликация топика смещений (для 1‑брокера) |

> **Совет**: Если запускаете только один брокер, `replication-factor` и `min.insync.replicas` можно оставить `1`. Для реального кластера нужны `replication-factor ≥ 3` и `min.insync.replicas ≥ 2`.

---

## Минимальный Java‑пример

```java
// Producer
Properties props = new Properties();
props.put(\"bootstrap.servers\", \"localhost:9092\");
props.put(\"key.serializer\", \"org.apache.kafka.common.serialization.StringSerializer\");
props.put(\"value.serializer\", \"org.apache.kafka.common.serialization.StringSerializer\");

Producer<String, String> p = new KafkaProducer<>(props);
p.send(new ProducerRecord<>(\"test\", \"key\", \"Hello Kafka!\"));
p.close();

// Consumer
Properties propsC = new Properties();
propsC.put(\"bootstrap.servers\", \"localhost:9092\");
propsC.put(\"group.id\", \"demo\");
propsC.put(\"key.deserializer\", \"org.apache.kafka.common.serialization.StringDeserializer\");
propsC.put(\"value.deserializer\", \"org.apache.kafka.common.serialization.StringDeserializer\");
propsC.put(\"auto.offset.reset\", \"earliest\");

KafkaConsumer<String, String> c = new KafkaConsumer<>(propsC);
c.subscribe(Collections.singletonList(\"test\"));
while (true) {
    for (ConsumerRecord<String, String> record : c.poll(Duration.ofMillis(100))) {
        System.out.printf(\"offset=%d key=%s value=%s%n\",
                          record.offset(), record.key(), record.value());
    }
}
c.close();
```
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
    <details style='margin-left: 20px'>
    <summary style='font-size: 16px'>Jenkins pipelines</summary>
    <p style='font-size: 14px'>

## Что такое Jenkins Pipeline?

Jenkins — это сервер CI/CD, а **Pipeline** — это способ описать всю сборку, тестирование и деплой как *программный скрипт*.  
Пайплайн позволяет:Пайплайн записывается в файл `Jenkinsfile` и хранится в репозитории вместе с кодом.

### Почему Pipeline полезен?

| Преимущество | Что это значит |
|--------------|----------------|
| **Версионирование** | Пайплайн‑код хранится в Git, как и ваш проект. |
| **Повторяемость** | Один и тот же файл запускается на любой машине. |
| **Модульность** | Можно разделять пайплайн на отдельные этапы (checkout, build, test, deploy). |
| **Отчёты** | Jenkins автоматически собирает отчёты о сборке и тестах. |

---

## Минимальный пример: Gradle + TestNG

### 1. `build.gradle`

```groovy
plugins {
    id 'java'
}

repositories {
    mavenCentral()
}

dependencies {
    // TestNG как фреймворк для юнит‑тестов
    testImplementation 'org.testng:testng:7.6.1'
}

// Настраиваем задачу test, чтобы она использовала TestNG
test {
    useTestNG()
    // Если нужно, можно указать конкретные тесты
    // include '**/*MyTest*.class'
}
```

> **Важно**: убедитесь, что в репозитории есть тесты, которые находятся в `src/test/java` и используют аннотации TestNG (`@Test`, `@BeforeMethod` и т.д.).

### 2. `Jenkinsfile` (Declarative Pipeline)

```groovy
pipeline {
    agent any                     // Запускаем на любой доступной ноде

    tools {
        // Jenkins должен иметь установленный Gradle
        gradle 'Gradle_7.6'       // Имя версии из “Global Tool Configuration”
    }

    stages {
        stage('Checkout') {
            steps {
                // Получаем код из репозитория
                git branch: 'main', url: 'https://github.com/your-org/your-repo.git'
            }
        }

        stage('Build') {
            steps {
                // Собираем проект
                sh './gradlew clean assemble'
            }
        }

        stage('Test') {
            steps {
                // Запускаем юнит‑тесты TestNG
                sh './gradlew test'

                // Публикуем результаты тестов в Jenkins
                // Jenkins понимает формат JUnit XML, а TestNG генерирует его
                junit 'build/test-results/test/TEST-*.xml'
            }
        }

        stage('Archive Artifacts') {
            steps {
                // Архивируем артефакты сборки (jar, war и т.д.)
                archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
            }
        }
    }

    post {
        // Что делать после каждой сборки
        always {
            // Убираем временные файлы, если нужно
            cleanWs()
        }
        success {
            echo 'Build succeeded!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}
```

### 3. Как это работает

| Стадия | Что делает | Что видите в Jenkins |
|--------|------------|----------------------|
| `Checkout` | Клонирует репозиторий | Вкладка **Pipeline** показывает `git`-команды |
| `Build` | `gradlew assemble` | В консоли виден вывод Gradle |
| `Test` | `gradlew test` + `junit` | На вкладке **Tests** появляется таблица с количеством пройденных/непройденных тестов |
| `Archive Artifacts` | Сохраняет jar‑файл | Вкладка **Artifacts** позволяет скачать jar |

---

## Быстрый старт

1. **Установите Jenkins** (или воспользуйтесь Jenkins‑X, CloudBees, GitHub Actions и т.д.).
2. В **Global Tool Configuration** добавьте Gradle (выберите нужную версию).
3. В **Manage Jenkins → Manage Plugins** установите:
    - *Gradle Plugin* (для `tools { gradle '...' }`)
    - *JUnit Plugin* (для `junit` шага)
4. Создайте новый **Pipeline** job и укажите путь к `Jenkinsfile` (обычно `Jenkinsfile` в корне репозитория).
5. Запустите job – Jenkins сам выполнит все стадии.

---

## Плюсы и минусы

| Плюсы | Минусы |
|-------|--------|
| Автоматизация всего цикла разработки | Нужно поддерживать Jenkins и плагины |
| Возможность интеграции с другими инструментами (SonarQube, Nexus, Slack) | Пайплайн может стать громоздким при больших проектах |
| Быстрый отклик о статусе сборки | Нужен опыт на Groovy/Declarative Pipeline |
</p>
    </details>
</details>
