# 📈 Performance Deep Dive


## 1. 🚀 Як завантажується сторінка

Що відбувається після того, як користувач вводить URL у браузері?

> 🔗 [Стаття: What happens when you type a URL](https://aws.amazon.com/blogs/mobile/what-happens-when-you-type-a-url-into-your-browser/)

---

## 2. 🖥 Рендеринг у браузері

Що відбувається після отримання HTML-коду?

> 🔗 [Стаття: Як браузер рендерить HTML/CSS](https://medium.com/@mustafa.abdelmogoud/how-the-browser-renders-html-css-27920d8ccaa6)

> 📺 [Відеокурс: Rendering in browser](https://www.youtube.com/playlist?list=PLAwxTw4SYaPmKmNX-INgcxQWf30KuWa_A)

---

## 3. 🔍 Lighthouse: аналіз перед оптимізацією

Спочатку вимірюємо — потім покращуємо!

📊 Основні метрики:

- [First Contentful Paint (FCP)](https://developer.chrome.com/docs/lighthouse/performance/first-contentful-paint) — перше відображення контенту
- [Largest Contentful Paint (LCP)](https://developer.chrome.com/docs/lighthouse/performance/lighthouse-largest-contentful-paint) — найбільший елемент сторінки
- [Total Blocking Time (TBT)](https://developer.chrome.com/docs/lighthouse/performance/lighthouse-total-blocking-time) — блокування основного потоку

> 📎 [Оцінка продуктивності в Lighthouse](https://developer.chrome.com/docs/lighthouse/performance/performance-scoring)

---

## 4. 🔧 Основні напрями оптимізації

- ⚖️ Розмір завантажуваних ресурсів
- 📦 Кількість запитів
- ⚙️ Час обробки
- 🔀 Паралелізація

---

## 5. ⚖️ Оптимізація розміру завантажуваних ресурсів

- Використовуй кешування в браузері
- Code splitting — завантажуй лише те, що потрібно для першого екрана
  - `<link rel="stylesheet" media="...">`
- вибір потрібного зображення (`media queries` та `srcset`)
- Сучасні формати зображень (WebP, AVIF), SVG для іконок
- Пагінація
- Мініфікація HTML/CSS/JS
- Увімкни GZIP або Brotli на сервері

> [Приклад поетапного завантаження зображень](https://www.youtube.com/watch?v=hJ7Rg1821Q0)
---

## 6. 📦 Зменшення кількості запитів

- Inline critical CSS
- Бандлінг файлів (об’єднання в один)
- отримання лише потрібних даних (REST vs GraphQL)
- Sprite: об’єднання іконок в один файл
- Уникай редиректів

---

## 7. ⚙️ Оптимізація часу обробки (вкладка Performance)

- Спрощення CSS-селекторів та JS-коду
- Уникай перебудови DOM, CSSOM, layout [змінюй `transform` та `opacity`](https://web.dev/articles/stick-to-compositor-only-properties-and-manage-layer-count)
- Зменшуй час відповіді сервера

---

## 8. 🔀 Паралелізація завантаження


- Завантажуй скрипти з `async` або `defer`
- [Завантажуй CSS асинхронно](https://web.dev/articles/defer-non-critical-css)
    ```html
    <link rel="preload" as="style" ...>
    ```
- Одразу встановлюй з'єднання з потрібними серверами
    ```html
    <link rel="preconnect">
    ```
- Використовуй CDN

---

## 9. 🌍 Навіщо потрібен CDN?

- Доставка контенту з серверів ближче до користувача
- Можливість завантажувати більше ресурсів одночасно
- Можливість повторного використання загальних бібліотек
- Зменшення навантаження на основний сервер

📎 [Детально про CDN (Leaseweb)](https://www.leaseweb.com/cdn#work)

---

## 10. SSR vs ...

- Static pages (super fast but limited content)
- Server rendered pages (fast first render, but slow update)
- SPA
  - The fastest time to first byte
  - Very fast updates
  - no content at start (SEO issues)
  - slower time to first content
  - slower time to interactive
- SPA + SSR
  - First page is rendered by the server
  - Scripts are loaded to hydrate the page
  - Updates are fast as for SPA

> [An article by Google](https://developers.google.com/web/updates/2019/02/rendering-on-the-web?hl=en)

> [the UA one at DOU](https://dou.ua/forums/topic/41585/)

## Домашнє завдання

Досягти `90` балів в Lighthouse
- Оптимізувати зображення
- Завантажувати на початку тільки те що треба
