# upg_gg — техническая документация

Сайт-исследование ниши CS2 скинов для IT-бизнеса. Продакшн: https://yuppi090r-ctrl.github.io/upg_gg/

---

## Структура

```
upg_gg/
├── index.html                  — главная + секция "Карта исследования"
├── business.html               — TAM/SAM/SOM
├── revenue.html                — трафик и выручка конкурентов
├── money.html                  — воронки монетизации
├── reality.html                — голос рынка, скам-кейсы
├── gtm.html                    — GTM, регуляторика, M&A
├── upgrader-pro/index.html     — deep-dive Upgrader.pro (red/orange accent)
├── skin-club/index.html        — deep-dive Skin.Club (cyan accent внутри контента)
├── skin-club/screenshots/      — 10 скринов платформы Skin.Club
├── previews/                   — 7 JPG превью для карты сайта (1400×900)
├── favicon.svg
├── playground/                 — .gitignore, локальные эксперименты
├── RESEARCH.md / FUNNELS.md    — заметки исследования
└── DOC.md                      — этот файл
```

---

## Deploy

GitHub Pages от `main`. Push → через 30-90 сек обновление на проде.

```bash
git add -A
git commit -m "..."
git push origin main
```

Cache-buster для проверки: `?v=N` в URL (Pages + CDN кэш иногда держится).

---

## Unified паттерны (важно при правках)

Если правишь один из этих элементов — проверь на ВСЕХ 8 страницах.

### 1. Nav (8 ссылок везде)

Порядок: **Обзор · Бизнес · Доходы · Воронка $ · Голос рынка · GTM · Upgrader.pro · Skin.Club**

- На странице, соответствующей ссылке — проставлен `class="active"` серверно
- Deep-dive страницы имеют те же 8 ссылок (с `../` путями)
- Mobile-nav содержит те же ссылки (с более длинными текстами типа "Бизнес-аналитика")

### 2. Auto-hide шапка при скролле

**CSS** (на всех 8 страницах):
```css
nav { transition: transform .35s cubic-bezier(.4,0,.2,1); }
nav.nav-hidden { transform: translateY(-100%); }
```

**JS**:
```js
let lastScrollY = 0;
window.addEventListener('scroll', () => {
    const y = window.scrollY;
    if (y > 240 && y > lastScrollY + 4) navbar.classList.add('nav-hidden');
    else if (y < lastScrollY - 4 || y < 120) navbar.classList.remove('nav-hidden');
    lastScrollY = y;
}, {passive: true});
```

### 3. Hero padding-top

На мобиле ≥6rem обязательно (fixed-шапка 70-80px). Базовое правило:
```css
@media(max-width:768px) { .hero { padding: 6rem 1.25rem 4rem; } }
```

### 4. Футер — единая стилистика

Даже на deep-dive: фиолетовый акцент, не тематический цвет страницы.
```css
footer { background: linear-gradient(0deg, rgba(139,92,246,.04), transparent); }
.footer-links h5, .footer-meta h5 { color: var(--accent2); }
```
Mobile: `footer { padding: 3rem 1.25rem 1.5rem }`, `.footer-links a { padding: .7rem 0; min-height: 44px; display: flex; align-items: center }`.

### 5. H1 с `<br>` — обязательно пробел

**Неправильно:** `<h1>Слово1<br><span>Слово2</span></h1>` (textContent склеится)
**Правильно:** `<h1>Слово1 <br><span>Слово2</span></h1>`

### 6. URL deep-dive

Использовать `upgrader-pro/` и `skin-club/` (без `/index.html`).

---

## CSS-переменные (общие для всех 8 страниц)

```css
--bg: #07060e
--surface: #0f0d1a / --surface2: #16132a / --surface3: #1e1a35
--text: #f4f2ff / --text2: #9d97c0 / --text3: #6b6490
--accent: #8b5cf6 (фиолетовый)
--accent2: #c4b5fd (светло-фиолет — h5, nav active)
--accent-glow: rgba(139,92,246,.2)
--green: #10b981 / --red: #ef4444 / --orange: #f59e0b
--blue: #3b82f6 / --pink: #ec4899 / --cyan: #06b6d4
--gradient: linear-gradient(135deg, #8b5cf6, #ec4899, #6366f1) — фиолетовый (main)
--gradient2: linear-gradient(135deg, #06b6d4, #10b981) — циан (Skin.Club)
--gradient3: linear-gradient(135deg, #f59e0b, #ef4444) — оранжевый (Upgrader.pro)
```

---

## Карта сайта (секция на index)

Находится между hero и market (`#sitemap`). 7 карточек `.site-card` в сетке `auto-fit minmax(280px, 1fr)`.

При добавлении новой страницы:
1. Сделать скрин 1400×900 (см. previews/)
2. Добавить JPG в `previews/NN_pagename.jpg`
3. Добавить карточку `<a class="site-card">` в `#sitemap`
4. Обновить nav на всех 8 страницах

---

## Research правила (важно!)

Из опыта прошлых сессий — [`memory/feedback_upg_gg_research.md`](~/.claude/projects/-Users-homeadmin-Desktop-VS-Claude/memory/feedback_upg_gg_research.md):

1. **Рой агентов 4-5 штук** параллельно (financial + legal + funnel + reviews + tech) — один агент растягивает на час, рой за 10 мин.
2. **Строго headless Playwright MCP** — запрещены `chrome-devtools-mcp` и `superpowers-chrome-mcp` (перехватывают живой Chrome Босса).
3. **Режим А без галлюцинаций** — "не достал → пиши не достал". Придуманные отзывы/цитаты = потеря доверия.
4. **Верификация отдельным pass** — после сбора фактов запускать 3 верификатора. Ошибки раннего ресёрча: LLC Transtrade ≠ Upgrader, Vitality НЕ ушли, Change.org петиция была выдумана.
5. **HTML-валидация** после больших правок (>200 строк) — один `</h4>` вместо `</div>` сломал DOM на мобиле (было у skin-club).

---

## Ключевые факты (верифицированные)

### Upgrader.pro
- Innospace LTD (Кипр HE 466465, 17.10.2024), домен .pro — 02.07.2025
- Связь с RustMagic через Qualitas Services LLC (Belize 200159823)
- Трафик 1.2M/мес (+123% MoM), РФ 63% / УА 13% / DE 5%
- Trustpilot 2.9/5 (13 отзывов: 38% 5★ / 62% 1★)
- Telegram @upgraderCS ~92.7K
- Маркетинг "RTP до 99%" — оценка реального 55-75%
- Механика: x2/x4/x8, шансы 35/55/75%

### Skin.Club
- Moontain Limited (Кипр HE 410299, 19.06.2020), домен с 2017-09-04
- **Vitality активно партнёр** (это НЕ ошибка ранних данных)
- ACMA formal warning 17.07.2025 (AU)
- Трафик 4.3M/мес (-5.5% MoM), US 13% / ES 8% / DE 8%
- Trustpilot 3.5/5 (1 712 отзывов, 65%/4%/1%/2%/28%)
- skin.club.com.au (AU-дочка) 1.3/5 (311 отзывов после ACMA)
- 200+ кейсов, case battles до 15, upgrader x20
- House edge 11-18% (Reddit tests), до 40-60% на дешёвых

### НЕ связаны с Upgrader
- LLC Transtrade (Бишкек) = cs2case.win/io
- ITSFAIL OU (Таллин) = GGDrop

---

## Валюта

Все суммы в **$** по курсу ~90₽/$1 (апрель 2026). Рубли не использовать.
