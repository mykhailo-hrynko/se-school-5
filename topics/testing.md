# 🧪 Functional Testing for Middle Developers

Тестування — це процес перевірки, чи працює система відповідно до вимог.

**🎯 Мета тестування:**
- Знайти недоліки якомога раніше (щоб зменшити вартість помилки).
- Переконатися, що система працює коректно після змін коду чи конфігурації.

**Функціональні тести** перевіряють, що система:
- ✅ повертає очікувані результати
- ✅ виконує лише заплановані дії

**Нефункціональні** тести перевіряють додаткові вимоги. Наприклад:

- 🚀 **Performance Testing** — швидкодія системи
- 📊 **Load Testing** — чи система працює коректно під визначеним навантаженням
- 🔥 **Stress Testing** — поведінка системи в стресових умовах
- 🛡️ **Security Testing** — відсутність вразливостей у системі
- 👍 **Usability Testing** — зручність використання
- ♿ **Accessibility Testing** — доступність для користувачів з обмеженнями
- 💻 **Compatibility Testing** — перевірка на різних ОС/браузерах/пристроях
- 🌍 **Localization Testing** — перевірка перекладу, форматів

> Тестувати вручну — довго, дорого й ненадійно (через людський фактор). Тому далі розглянемо автоматизоване функціональне тестування.

## Різновиди функціонального тестування

**SUT** (System under the test) - система чи модуль, що перевіряється.

**Mock** - функція, об'єкт чи модуль, який підставляється в **SUT** замість реальної залежності, для емуляції бажаної поведінки чи перевірки параметрів виклику. Зазвичай потрібні для модулів, які ми не можемо контролювати.

Тести розподіляють на 3 рівні:

| Рівень          | Що перевіряємо (SUT)                    | Бібліотеки          |
| --------------- | --------------------------------------- | ------------------- |
| **Unit**        | Окрема функція чи модуль                | Vitest, Jest, Mocha |
| **Integration** | Компоненти разом (API + база + сервіси) |                     |
| **End to End**  | Повний шлях користувача + бізнес-логіка | Playwright, Cypress |

Подивись [виступ Kent C. Dodds](https://kentcdodds.com/blog/write-tests) щоб визначитися, яких тестів має бути більше і чому.

- Гарні тести перевіряють поведінку системи з точки зору користувача, а не конкретну реалізацію
- Test coverage 100% не гарантує, що тести якісні
- Надмірне використання моків призводить до "крихких" тестів. Мокай те що не можеш контролювати (сторонні сервіси, базу, ...)
- Якщо тести падають під час рефакторінгу, значить тестуються деталі імплементації
- Тести, як документація мають бути прості та зрозумілі

## Типова структура тесту

Тести не мають залежати одне від одного. Тому:

- перед тестом треба запустити новий застосунок (а не очікувати що він досі працює після попередніх тестів)
- після тесту треба повернути до початкового стану усі stateful ресурси (базу, моки, тощо)

```ts
type TestContext = {
  app: App;
  baseUrl: string;
};

// .sequential - to launch tests one after another
describe.sequential("Module Name", () => {
  beforeEach<TestContext>(async (t) => {
    // Prepare environment common for all tests
    t.app = createApp(testLogger, testConfig, ...);
    await t.app.start();

    // use to send requests to your api
    t.baseUrl = `http://${t.app.address}:${t.app.port}`;
  });

  afterEach<TestContext>(async (t) => {
    await t.app.stop();
    await db.reset();
    vi.clearAllMocks();
  });

  it<TestContext>("creates subscription with for new email and existing city", (t) => {
    // prepare data
    // perform tested actions
    // check result
  });

  it("fails if email is already registered", ...);
});
````

> 🧼 Тести мають бути **ізольовані**: запускати все заново, очищати state, не покладатися на попередній тест.

---

## ⚙️ Загальні поради

- Пиши код так, щоб його було **зручно тестувати**.
- Тести мають бути **простими і зрозумілими** — як документація.
- При розробці запускай код **через тести** (TDD або хоча б test-first mindset).
- Перед виправленням багу — напиши тест, який **падає**.
- Якщо система змінилась, а жоден тест не впав — значить тести **не повні**.
- Добрі тести допомагають **знайти і зрозуміти** проблему.
- Якщо впав тест вищого рівня, подумай я протестувати це раніше

## 🔁 Перевикористання тестової логіки

Дублювання коду в тестах це абсолютно нормально. Це дозволяє одразу побачити що відбувається в тесті без необхідності дивитися код в інших місцях.

Але іноді тести стають дуже громіздкими, що ускладнює розуміння. Для спрощення тестів можна використовувати підходи з шаблонами:

> Для UI-тестів. Інкапсулюють логіку взаємодії з інтерфейсом.

```ts
export class LoginPage {
  constructor(private page: Page) {}

  async goto() {
    await this.page.goto("/login");
  }

  async login(email: string, password: string) {
    await this.page.fill('[data-testid="email"]', email);
    await this.page.fill('[data-testid="password"]', password);
    await this.page.click('[data-testid="submit"]');
  }

  async getError() {
    return this.page.textContent('[data-testid="error"]');
  }
}
```

📍 Зручно для E2E з Playwright/Cypress.

---

### 🧪 Test Drivers

> Для unit/integration тестів. Інкапсулюють логіку створення SUT, моків та взаємодії з API.

```ts
export class SubscriptionDriver {
  constructor(private app: App, private db: Db) {}

  async createUser(email: string) {
    await this.db.insertUser({ email });
  }

  async subscribe(email: string, city: string) {
    return await this.app.inject({
      method: "POST",
      url: "/subscribe",
      payload: { email, city },
    });
  }
}
```

📍 Полегшує написання і читання тестів, зменшує boilerplate.

---

### ✅ Custom Assertions

> Перевикористовувані функції для перевірок.

```ts
import { validate } from "uuid";

export function toBeUuid(received: string) {
  if (typeof received !== "string") {
    return {
      pass: false,
      message: () =>
        `Expected the value to be a string, but got ${typeof received}.`,
    };
  }

  if (!validate(received)) {
    return {
      pass: false,
      message: () => `Expected the value to be a UUID, but got ${received}.`,
    };
  }

  return { pass: true, message: () => "" };
}
```

```ts
expect("hello").toBeUuid()
```
---
