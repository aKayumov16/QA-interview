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
        .addConverterFactory(JacksonConverterFactory.create())
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
</details>
