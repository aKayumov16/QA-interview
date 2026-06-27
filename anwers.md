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
## Что это такое

| Технология | Краткое описание | Когда использовать |
|------------|------------------|--------------------|
| **Selenium** | Библиотека‑фреймворк для автоматизации браузеров (WebDriver). Позволяет писать скрипты, которые открывают страницу, кликают, вводят текст, читают DOM и т.д. | Если нужен полный контроль над браузером, нужна кросс‑браузерная поддержка, нет ограничений по API. |
| **Selenide** | «обёртка» над Selenium, написанная на Java (и Kotlin). Предоставляет более лаконичный синтаксис, автоматическое ожидание элементов, встроенную поддержку screenshots, отчётов и т.п. | Когда хочется писать чистый, читаемый код без ручного `Thread.sleep()` и `waits`. |
| **Selenoid** | Docker‑контейнер, реализующий Selenium Grid (встроенный диспетчер). Позволяет запускать тесты параллельно в разных браузерах без настройки отдельного сервера. | Если нужно быстро развернуть «grid» в CI‑pipeline без ручной настройки Selenium Hub/Nodes. |

> **Замечание**: «Selenaid» в вопросе, скорее всего, опечатка. Правильные названия – **Selenium**, **Selenide** и **Selenoid**.

---

## Как они связаны

```
Selenium (WebDriver)  <-- базовый драйвер
          ↑
          |  (обёртка)
          v
    Selenide          ← использует Selenium под капотом
          ↑
          |  (потребуется инфраструктура)
          v
    Selenoid          ← запускает контейнеры‑браузеры, управляет WebDriver‑сессиями
```

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

---

## Итог

| Технология | Ключевые особенности | Пример применения |
|------------|----------------------|-------------------|
| **Selenium** | Низкоуровневый, кросс‑браузерный | Писать скрипты с ручным управлением ожиданиями |
| **Selenide** | Высокоуровневый, удобный API | Быстрый, чистый код, автоматическое ожидание |
| **Selenoid** | Docker‑Grid, быстрый запуск | Параллельные тесты в CI, без ручной настройки Grid |

Если вы только начинаете, попробуйте **Selenide** – он делает большинство задач проще, а **Selenoid** пригодится, когда понадобится масштабировать тесты.
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
</details>
