# Final verdict — meta-adversarial разбор W3 и первой рецензии

Документ оценивает manifest_v3.md, ux_guide_v0.2.md и adversarial_review_v1.md. Цель — независимый вердикт, а не подтверждение первого ревьюера.

## Executive summary

AIO в формулировке манифеста ("точка входа в интернет — диалог, конкурируем за три рынка") нежизнеспособна как стратегия захвата массового рынка на Западе. Эмпирика против. Но первый ревьюер передёрнул в нескольких местах: TEE сломан не полностью (физический доступ остаётся out of scope для cloud-сценариев частично), Wack 2026 не верифицирован вообще (надо убрать из манифеста), Signal-model "победил через поглощение" — упрощение, поглощение прошло без metadata-победы. Notion и Discord первый ревьюер недооценил как western precedent для chat-first/workspace-first консолидации.

Главный strategic path — protocol-first reference client под нишевую threat model манифеста (ЕС-граждане заботящиеся о privacy + русскоязычная диаспора + журналисты в авторитарных юрисдикциях). Манифест прав в принципах, неправ в риторике "конкурируем за три рынка". AIO-функциональность (assistant + messenger + browser в одном UX) — правильное продуктовое решение для ниши, но не стратегия победы над Chrome/iMessage/ChatGPT. Это ниша Tor + Signal + Brave, не WeChat.

Главный риск — TEE-фундамент для server-side AI mediation сломан (TEE.Fail подтверждён), и манифест/UX-guide должны переписать privacy режимы под новую реальность: либо local-only, либо cloud-with-trust, без "Trusted compute". Второй главный риск — federation поглощение (XMPP-сценарий), но против него в 2025-2026 работает DMA как реальное регуляторное давление, чего не было в эпоху Google Talk.

## Где первый ревью прав, где переборщил

### Прав

1. AIO как "конкурируем за три рынка" эмпирически проигрышная позиция. Подтверждается провалами Cortana, Bixby, Rabbit R1, Humane Pin. ChatGPT с 900M WAU (февраль 2026, OpenAI) добавил четвёртую категорию, не вытеснил три. Google search market share упал, но не катастрофически: Google всё ещё 89% U.S. web traffic в 2025, при этом 69% Google searches заканчиваются без клика (Similarweb июль 2025) — это zero-click, а не уход к ChatGPT. Это сильный negative argument.

2. TEE.Fail и Battering RAM реальны и серьёзны. Подтверждено Georgia Tech / Purdue paper, Intel/AMD/NVIDIA confirm, AMD прямо заявил что физические атаки out of scope и фиксов не будет. Принцип 8 в части "Trusted compute AI" нужно переписывать. Это не nuance — это сломанный фундамент для server-side AI mediation в untrusted-cloud threat model.

3. XMPP-сценарий реален. Google Talk дропнул XMPP federation в мае 2013, API окончательно умер в феврале 2015, Google Talk закрыт в 2017 (FSFE blog, EFF). Facebook Chat дропнул XMPP в 2015. Pattern подтверждается.

4. Anchor-проблема ZKP age verification недоассесена в манифесте. Верно. Эмитент = государство для подавляющего большинства паспорт-based credentials. Plurality эмитентов в ЕС не помогает в РФ.

5. Hard architectural invariants держатся только для "тех кто умеет проверить хеш" — для ниши Tor-уровня. 99% живёт на дефолте. Это в манифесте признано в принципе 7, но дальше "забывается".

### Переборщил

1. **TEE "DEBUNKED для production"** — overstated. Атаки требуют физического доступа к серверу (interposer на DDR4/DDR5 шине). Это меняет threat model, но не делает TEE бесполезным. Для угрозы "rogue datacenter employee" или "compelled cloud provider" — да, сломан. Для угрозы "remote attacker через сеть" — TEE остаётся слоем defense. AMD/Intel явно не отрицают атаки, а декларируют их out of scope. Манифесту нужен downgrade принципа, а не удаление. Реальная формулировка: "TEE как defense against remote/software, не как E2E equivalent против provider with physical access".

2. **Wack et al. 2026 Clemson** — первый ревьюер пометил NOT_FOUND, и я подтверждаю: публикации с этим названием/выводом не существует ни в Clemson Media Forensics Hub releases, ни в Google Scholar по Morgan Wack, ни в arXiv. Wack действительно работает по теме (PNAS Nexus 2025 "Generative propaganda", Policy Studies Journal 2025 "Legislating Uncertainty"), но конкретной работы по Community Notes failure mode нет. Это серьёзная ошибка в манифесте. Удалить ссылку или заменить на корректные: arxiv 2510.00650 "Threats to the sustainability of Community Notes on X" (октябрь 2025), arxiv 2502.14132 "Can Community Notes Replace Professional Fact-Checkers?" (5% notes ссылаются на pro fact-checkers), и PNAS 2024 "Did the Roll-Out reduce engagement" — обе работы говорят что Community Notes слишком медленны (median 10.4ч до первой ноты + 7.2ч до helpful status, только 13.55% постов получают helpful note между 2021-2025). Это failure mode, но без специфики "сложная правдоподобная дезинформация".

3. **Signal model "победил через поглощение"** — упрощение. Signal Protocol (X3DH + Double Ratchet) действительно в WhatsApp с 2016, в Google Messages RCS 1-on-1 с 2021 + group chat с 2023, Skype Private Conversations, Facebook Messenger Secret Conversations. Но: iMessage НЕ использует Signal Protocol (Apple свой проприетарный protocol с 2011, переходит на post-quantum PQ3 в 2024). RCS Apple-implementation — это Apple's own RCS support с iOS 18, при этом cross-platform RCS НЕ E2E encrypted, только intra-Google или intra-Apple. И главное: WhatsApp защищает контент, но Meta видит ВСЕ metadata и контролирует ключевые backups. Это не "победа" в сильном смысле, это адопция криптослоя без защиты от платформы. Манифесту нужно учесть — protocol-first без своего клиента не решит metadata leak.

4. **"Federation не держит сетевой эффект — XMPP collapse это эталонный кейс"** — игнорирует то что в 2025-2026 регуляторный climate другой. DMA вступил в силу март 2024, в апреле 2025 EC выписал Apple €500M и Meta €200M. WhatsApp в ноябре 2025 запустил interop с BirdyChat и Haiket для EU-пользователей по требованию DMA. Это первый случай когда крупный мессенджер вынужден federation. XMPP-эпоха не имела такого инструмента. Это не отменяет паттерн, но окно для protocol-first сейчас шире чем в 2013.

5. **Discord/Telegram как western precedent** — первый ревьюер упомянул, но недооценил. Discord 259M MAU, $725M ARR, 54% юзеров в non-gaming сообществах (education, crypto, devs) — это chat-first платформа которая выросла в community + voice + payments + bot platform без захвата browser/search. Telegram 1B MAU март 2025, $1B revenue 2025, mini apps 150-190M MAU стабилизировались. Это два western кейса где chat-first AIO частично работает — не в формате "вытеснил Chrome", а в формате "стал основной surface для своей user base". Это полезный data point для гибридной стратегии W3.

6. **Notion как productivity-AIO** — недооценен. 100M users сентябрь 2024, $500M ARR в 2025, 50% Fortune 500. Notion консолидировал docs + database + wiki + project management и сейчас добавляет AI agents. Это western успех "одно вместо нескольких" в специфической вертикали (knowledge work). Не побеждает Linear/Figma но сосуществует. Урок для W3 — AIO-консолидация работает в нише, не как horizontal play.

7. **Scoring 9/20 vs 15/20** — нумерология. Нет прозрачной rubric, веса субъективны, "feasibility" и "defensibility" мерять равными — спорно. Использовать как вектор обсуждения, не как количественный аргумент. Я не воспроизвожу этот scoring в финальном вердикте.

8. **Возражение автора "поглотят и извратят"** — первый ревью игнорирует. Это критический пункт. История на стороне автора частично: XMPP, RSS, OpenGraph (Facebook embedded RSS только для своих preview), email federation сжалось до Gmail/Outlook/Apple. Но: Tor выжил 25 лет, Signal выжил, Matrix вырос до 11K+ federateable серверов и 100K+ всего, Tchap 360K MAU французского правительства, Bundeswehr BwMessenger 100K+. Federation выживает в нише с ясным threat model, не в открытой конкуренции за consumer mass-market. Первый ревью прав в выводе ("ниша как Tor"), но не отвечает на возражение автора "значит поглощение неизбежно если стандарт успешен". Honest answer — поглощение возможно но требует от поглотителя криптотехнически совместимой реализации, и даже частичная адопция (как Signal Protocol в WhatsApp) сохраняет контентную защиту. Адопция стандарта = частичная победа даже если клиент-монополист.

## Независимая верификация

| Утверждение | Verdict | Источник | Комментарий |
|---|---|---|---|
| TEE.Fail атака на Intel SGX/TDX и AMD SEV-SNP DDR5 | VERIFIED | tee.fail, Bleeping Computer октябрь 2025, Hacker News | Custom interposer <$1000, Georgia Tech + Purdue. Атаки практичны, ключи извлекаются включая attestation keys. AMD/Intel вынесли физический доступ out of scope. |
| Battering RAM <$50 на DDR4, не DDR5 | VERIFIED | batteringram.eu, Dark Reading октябрь 2025 | Только DDR4 в текущей реализации. Mitigation требует фундаментального redesign memory encryption. |
| ChatGPT 800M WAU октябрь 2025, 900M февраль 2026 | VERIFIED | OpenAI announcement, demandsage, ALM | Рост продолжается, но web traffic share упал на 22 п.п. за год (Gemini растёт). |
| Google U.S. search 89%, 69% zero-click 2025 | VERIFIED | First Page Sage, Similarweb июль 2025 | Zero-click это не "ушли к ChatGPT", это AI Overviews + featured snippets внутри Google. |
| Beeper Mini убит Apple декабрь 2023 | VERIFIED | TechCrunch, MacRumors, Wikipedia | Apple обрубил протокольно, бридж не выжил. Урок: bridge без cooperation от target = временное решение. |
| Threads federation реально работает | PARTIALLY | Meta engineering, TechCrunch июнь 2025 | EU-юзеры исключены, нельзя follow Mastodon обратно из Threads, нельзя reply на Mastodon-посты, Mastodon ~1.4M MAU vs Threads 100M MAU. Адопция federation среди Threads-юзеров минимальна. |
| Matrix 100K+ homeservers, Tchap 360K MAU, Bundeswehr 100K+ | VERIFIED | matrix.org/blog июнь 2025, element.io case studies, Sifted | 11347 federateable серверов через matrixrooms.info апрель 2025. Это самая успешная federation-платформа сейчас. |
| DMA WhatsApp interop ноябрь 2025 | VERIFIED | Meta about.fb.com, TechCrunch, Privacy Guides | BirdyChat и Haiket первые партнёры. Маленькие игроки, не Signal/Telegram. Реальный test ещё впереди. |
| DMA Apple sideloading и €500M штраф апрель 2025 | VERIFIED | EC, Apple developer docs, MacRumors | Apple ввёл sideloading в iOS 17.4, EC закрыла investigation в апреле 2025 нашла breach. Apple обновлял правила в июне 2025 (CTC 5%). |
| Signal Protocol в WhatsApp/Google Messages, не в iMessage | VERIFIED | Wikipedia Signal Protocol, signal.org | iMessage свой proprietary, перешёл на PQ3 в 2024. Cross-platform RCS НЕ E2E. WhatsApp защищает content, Meta видит metadata. |
| Wack et al. 2026 Clemson Community Notes | NOT_VERIFIED | morganwack.com, Clemson Media Forensics Hub | Конкретной публикации не существует. У Wack есть PNAS Nexus 2025 и Policy Studies Journal 2025, но не на эту тему. Манифесту нужно убрать или заменить ссылку. |
| Maples 2024 Replika 3% suicidal ideation halted | VERIFIED + контекст | npj Mental Health Research 2024, Matters Arising 2024 | Цифра корректная (30 из 1006 без подсказки). Rebuttal в том же журнале указывает: автор Maples — CEO Atypical AI (educational GenAI), не раскрытый conflict of interest. Sexual component Replika и industry interest не покрыт в paper. |
| Discord 259M MAU, 656M registered, $725M ARR 2024 | VERIFIED | Programming Helper Tech, statista, whop.com | Western chat-first платформа выросла без AIO-риторики. 54% non-gaming. |
| Telegram 1B MAU март 2025, mini apps 150-190M MAU | VERIFIED | demandsage, propellerads, Earlybird | Mini apps стабилизировались после пика 1.44B сентябрь 2024 (это был spike crypto/games). Не подтверждает "западный AIO в чате". |
| Notion 100M users September 2024, $500M ARR 2025 | VERIFIED | Notion announcements, contrary research | Productivity AIO, не chat-first. Сосуществует с Linear/Figma. |
| Community Notes 11% notes reach helpful, 15.5h median | VERIFIED | arxiv 2502.14132, arxiv 2510.00650 | Failure mode реален: timing problem + coverage gap. Манифесту нужно использовать эти источники вместо Wack. |
| EU AVB v2 октябрь 2025, ZKP, Дания/Франция/Греция/Италия/Испания пилот | VERIFIED | EC announcement, Biometric Update | Эстония и Бельгия отказались от Jutland Declaration, не от blueprint. |

## Финальный strategic вердикт

### Жизнеспособна ли AIO-стратегия для W3

**Нет в формулировке "точка входа в интернет — диалог, конкурируем за три рынка одновременно для массового пользователя".** Это путь Rabbit/Humane.

**Да в формулировке "AIO-продукт для конкретной ниши с ясной threat model, без претензии на массовый захват".** Discord не "победил" мессенджеры — он стал основной surface для gaming + community niches. Notion не "победил" docs — он стал основной surface для knowledge work. W3 может стать основной surface для privacy-conscious-Europeans + русскоязычная диаспора + journalists/activists в авторитарных юрисдикциях.

Ключевое различие: AIO как продуктовое решение (один UX вместо N) ≠ AIO как стратегия захвата (вытеснить Chrome/iMessage/ChatGPT). Манифест путает эти два уровня. Первое — реалистично и правильно. Второе — провал.

### Где манифест действительно слаб

1. Рhetorика "точка входа — диалог" заявляет horizontal play против established players. Не подкреплена strategy doc, ресурсами, partnership map. Удалить или ограничить.
2. Принцип 8 "trusted compute с TEE" — сломанный фундамент. Переписать.
3. Wack et al. 2026 — не верифицирован. Удалить или заменить.
4. Anchor problem ZKP — признана но не проработана для авторитарных юрисдикций. Дать конкретный fallback (например, peer attestation + социальный graph — слабее но без эмитента-государства).
5. Hard invariants не различают "криптографически энфорсимые" и "социально энфорсимые". Первый ревью прав, нужна ясная классификация в манифесте.
6. UX Guide раздел 12 (TEE-based AI mediation) — переписать под realistic privacy modes.
7. Browser kill switch как mitigation против риска №7 (захват ассистента) — слабый. Нужен structurally стronger ответ: возможно — обратная архитектура (browser as default surface, assistant on demand), как минимум для child mode.

### Где первый ревью overshot

1. TEE — broken для одного threat scenario, не для всех. Downgrade, не удаление.
2. Signal model "поглощение" — упрощение. Signal Protocol защищает content, не metadata. Это не победа в сильном смысле.
3. Federation collapse — игнорирует DMA как новый регуляторный инструмент, которого не было в эпоху XMPP.
4. Western AIO precedent table неполна — Discord, Notion, Telegram, Slack показывают что vertical AIO работает.
5. Scoring rubric непрозрачный — нумерология.
6. Возражение "EEE поглотят open standard" поверхностно адресовано.

### Strategic path с максимальным шансом

**Гибрид: protocol-first спецификация + W3 как reference client для конкретной ниши + явное targeting privacy-niches Запада, не massive horizontal.**

Конкретно:
- W3 публикует спецификацию trust marker + privacy mode + inline citation как extension над Matrix protocol. Цель — Element, Beeper, FluffyChat, и подобные клиенты могут адоптировать.
- Reference client W3 имеет AIO UX (один input, three modes) и конкурирует за нишу, не за massive market.
- Threat model манифеста ↔ user base: privacy-conscious Europeans (DMA-aware), русскоязычная диаспора в ЕС, journalists/activists в авторитарных юрисдикциях. Это 5-15M потенциальных users, не 1B.
- Browser mode остаётся в продукте как escape hatch, но не позиционируется как "конкурент Chrome". Это feature, не категория.

### Если protocol-first — как набрать critical mass

1. **Не пытаться быть стандартом сразу.** Опубликовать спецификацию как extension над Matrix, не как новый протокол.
2. **Начать с одного killer-партнёра.** Element (Matrix reference client) или Beeper (был куплен Automattic за $125M в апреле 2024 — у них есть ресурс и интерес к Matrix). Или французский Tchap (правительство, 360K MAU, готов к экспериментам в области privacy).
3. **Использовать DMA как leverage.** WhatsApp interop с BirdyChat и Haiket в ноябре 2025 — окно для маленьких игроков получить интероп с большими. W3 может зайти в этот же coridor через 6-12 месяцев, не через 5 лет.
4. **Public goods funding, не VC.** NLnet (есть NGI Zero программы), Mozilla Open Source Support, Open Society, ECF (European Cultural Foundation), частные фонды типа Sovereign Tech Fund (немецкий, финансирует open source). Это даёт 1-3M EUR в год без давления growth metrics, что даёт 5+ лет runway для protocol-first.
5. **Открытая governance с самого начала.** Не "наш протокол", а "процесс через рабочую группу с Matrix Foundation, EFF, ECF". Иначе протокол не будет принят как legitimate стандарт.

### Если AIO — как избежать судьбы Rabbit/Humane

1. **Никакого hardware.** Software-only, web + mobile + desktop. Hardware = $50M+ minimum, 24+ месяцев до первого юнита, single point of failure.
2. **Никакого "we're the new smartphone".** Позиционирование как "specialized tool for X niche", не "general purpose AI device".
3. **Killer use case на старте.** Один. Не три. Например: "secure communication для journalists с inline AI-fact-check и трансcript-protection". Или: "русскоязычная privacy-first мессенджер с AI-помощью для эмигрантов в ЕС, без российских властей в цепочке". Узкая позиционировка → growth → расширение.
4. **Дистрибуция через partnerships, не consumer ads.** Reporters Without Borders, EFF, OCCRP для journalist-фокуса. Meduza, Important Stories для русскоязычной диаспоры.
5. **TEE из стека убрать или переписать.** Local-only + cloud-with-clear-disclosure режимы. Без обещания "мы видим но не читаем" — это сломано к октябрю 2025.

### Если гибрид — как разделить

- **Слой 1 (протокол).** Спецификация trust marker / inline citation / privacy mode metadata. Open standard, governance через Matrix Foundation или новую neutral org. Не зависит от W3 как клиента.
- **Слой 2 (reference client).** W3 — один из клиентов, реализующих спецификацию. AIO UX, но не претензия на захват рынка. Reference client как Tor Browser для Tor protocol.
- **Слой 3 (vertical applications).** Specialized верстки клиента под конкретные ниши. "W3 for journalists", "W3 for diaspora", "W3 for parents" (child mode focus). Каждая — отдельный go-to-market.

## Executable план по фазам

### Phase 0 (0-3 месяца) — стратегические решения и research

**Цели:**
- Зафиксировать стратегию (protocol-first hybrid против AIO horizontal)
- Закрыть факт-чек проблемы манифеста
- Определить начальную нишу

**Действия:**
1. Переписать manifest_v3.md:
   - Удалить или заменить Wack et al. 2026 ссылку на arxiv 2510.00650 + arxiv 2502.14132 + PNAS 2024 dl.acm.org/doi/10.1145/3686967
   - Принцип 8 "trusted compute" переписать: убрать гарантии untrusted-cloud privacy через TEE, оставить TEE как defense-in-depth для remote/software атак
   - Принципы разделить на криптографически энфорсимые (5, 8, 10 в текущей нумерации invariants) и социально энфорсимые (1, 2, 3, 4, 6, 9). Явно указать.
   - Убрать или ограничить риторику "точка входа в интернет — диалог". Заменить на "AI-ассистент со встроенным мессенджером и режимом прозрачности к источникам — для пользователей которым нужны эти три модальности в одном защищённом контексте".
2. Research questions:
   - Anchor problem fallback: возможен ли peer attestation для возрастной верификации в авторитарной юрисдикции? (литература: Web of Trust models, Brave's anonymous attestation, Privacy Pass)
   - Что разрешает DMA messaging interop в практической реализации к 2026? (продолжение мониторинга BirdyChat/Haiket → больших игроков)
   - Какие public goods funders уже финансируют Matrix-ecosystem проекты? (NLnet, NGI Zero, Sovereign Tech Fund)
3. Niche selection. Один из:
   - Privacy-conscious Europeans (5-15M потенциал, DMA-aware, английский/немецкий/французский)
   - Русскоязычная диаспора в ЕС (3-5M, специфический threat model манифеста)
   - Journalists в high-risk юрисдикциях (50K-200K, через RSF/OCCRP partnerships)
4. Decision: protocol-first hybrid или narrow-niche AIO. Зависит от funding и команды (см. resources).

**Kill criteria для Phase 0:**
- Если не нашли ни одного potential public goods funder через 3 месяца — пересмотреть scope
- Если research показал что DMA не открывает interop window для маленьких игроков (только для gatekeepers) — отказаться от "leverage DMA" и закладываться на Tor-style ниша longer term
- Если Wack et al. 2026 не верифицируется и манифест не переписан — не начинать prototype phase

### Phase 1 (3-9 месяцев) — minimum lovable product в нише

**Гипотеза:** Если 500-2000 active users в выбранной нише используют W3 как primary surface для chat + AI-fact-check + browser в течение 30 дней, AIO-UX в этой нише валидируется.

**Scope:**
- Reference client (mobile + web), Matrix-compatible
- Три режима в одном UX: assistant + person (Matrix) + browser
- Local-only AI mode + cloud AI mode (без TEE, с явным consent)
- 2-3 trust presets для выбранной ниши
- Inline citations для AI ответов
- Identity export (Matrix-compatible)
- БЕЗ: child mode, helpline integration, parent dashboard, age verification, federation extensions поверх Matrix

**Метрики успеха (через 6 месяцев после launch):**
- 500+ MAU в выбранной нише
- Median session duration > 5 минут (не tab-and-leave)
- D30 retention > 25% (Matrix benchmark ~15-20% для self-hosted)
- < 30% юзеров переключают primary мессенджер обратно после 14 дней (sticky test)

**Kill criteria для Phase 1:**
- D30 retention < 10% после 6 месяцев — продукт не нужен в этой нише, pivot к другой или kill
- 0 organic mentions в нишевых сообществах (RSF, EFF blog, ru-эмигрантские каналы) после 4 месяцев — distribution не работает
- Нет ни одного третьего клиента / extension от внешних разработчиков на спецификацию — protocol thesis провалился

### Phase 2 (9-18 месяцев) — расширение либо pivot

**Сценарий А (Phase 1 hits metrics):**
- Расширить scope: child mode + parent dashboard, age verification (EU AVB v2 integration), helpline integration
- Запустить вторую нишу
- Опубликовать спецификацию trust marker + inline citation как Matrix MSC (Matrix Spec Change) — формальная процедура для adoption другими клиентами
- Начать переговоры с Element/Beeper/Tchap о partial adoption спецификации
- Funding round 2: либо follow-on от public goods funders, либо careful VC если выручка от premium есть

**Сценарий Б (Phase 1 misses metrics):**
- Pivot к гибриду: оставить продукт для самой engaged части юзеров, фокус сместить на спецификацию-only path
- W3 client становится reference implementation, не product
- Команда сжимается до 3-5 человек, runway растёт

**Kill criteria для Phase 2:**
- Если ни одна спецификация не принята как Matrix MSC через 12 месяцев — protocol thesis не работает в этой governance модели, нужен новый план
- Если затраты на удержание юзеров (CAC) растут быстрее чем engagement (LTV proxy) — это distribution problem без структурного решения

### Phase 3+ (18+ месяцев) — сценарии scale

**Сценарий А (продукт + протокол работают):**
- 20-50K MAU, multiple ниши, partnerships с established privacy orgs
- Спецификация принята 2-3 другими клиентами
- Это успех в формате "Tor + Signal", не WeChat. Достаточно для жизнеспособной организации, не для unicorn exit.

**Сценарий Б (только протокол):**
- W3 client closed, спецификация живёт через Matrix ecosystem
- Команда — 2-3 человека на standards work, funded long-term через public goods
- Это меньший импакт, но не failure

**Сценарий В (consolidation):**
- Acquisition или merger с Element / Beeper / другим Matrix-player
- W3 spec становится частью того продукта
- Это успешный exit для команды, частичная победа для манифеста

## Risks register (top-5)

| Риск | Probability | Impact | Mitigation |
|---|---|---|---|
| TEE model сломан, нет E2E privacy для cloud AI mediation | 95% (уже реализовался) | Высокий | Переписать принцип 8 и UX Guide раздел 12. Local-only + cloud-with-explicit-disclosure модель. Никаких гарантий "provider не может прочитать" пока FHE/MPC не станут практичными для LLM (5-10 лет). |
| Поглощение федерации крупным игроком (XMPP-сценарий) | 50% в 5-летнем горизонте | Высокий | Использовать DMA как защитный механизм. Открытая governance с самого начала через neutral foundation. Не позиционироваться как клиент-монополист. |
| Distribution failure в выбранной нише | 60% | Высокий | Phase 1 kill criteria. Никаких heavy bets до валидации. Partnerships вместо paid acquisition. |
| Funding не находится | 40% | Высокий | Public goods first, не VC. Подача параллельно в NLnet, Mozilla, Sovereign Tech Fund на старте. Минимизировать burn rate. |
| Wack et al. как пример более широких verification holes в манифесте | 30% что есть ещё несколько unsourced claims | Средний | Внешний fact-check манифеста до Phase 1. Каждая ссылка проверяется. |

## Resource estimate

**Phase 0 (3 месяца):**
- 2 FTE: tech lead/product (вы) + research/policy person (part-time возможно)
- $30K-50K (compute, legal, travel для partnerships discussions)
- Capital: minimal, может быть личный или мини-grant

**Phase 1 (6 месяцев до launch + 6 месяцев validation = реально нужно держать команду):**
- 4-6 FTE: 2 backend (Matrix integration, AI orchestration), 1 mobile, 1 web, 1 product/UX, 1 community/distribution
- $400K-700K на 9 месяцев (зарплаты EU-rate, infra, design)
- Capital: первый public goods round

**Phase 2 (9 месяцев):**
- 6-10 FTE если сценарий А, 2-3 если Б
- $700K-1.5M в зависимости от сценария
- Capital: follow-on funding или revenue от premium

**Phase 3+:**
- Сильно зависит от выбранного сценария
- Realistic: $1-3M annual operating cost для Tor/Signal-scale организации в 5-летнем горизонте

## Open questions которые остаются неснятыми

1. **Anchor problem для авторитарных юрисдикций.** Манифест признаёт plurality эмитентов "не устраняет, а смягчает", но не предлагает альтернативу. Peer attestation через социальный граф? Web of trust? Криптоэкономические схемы (stake-based attestation)? Это требует отдельного research deep-dive.

2. **Что значит "client verifies, doesn't trust" когда reproducible build не масштабируется на 99% юзеров.** Принцип 7 манифест признаёт но честно говорит "ниша Tor". Это значит manifest не может обещать защиту mass-market. Нужно явно зафиксировать в маркетинге и UX onboarding.

3. **AI mediation в чате двух людей без TEE.** Если "Trusted compute" сломан, то остаётся local-only (capability bound) или cloud (provider читает). Нет middle ground. UX Guide раздел 12 написан под middle ground которого больше нет. Что делать с co-write и invited assistance use cases при cloud-only AI?

4. **DMA dynamics через 2-3 года.** WhatsApp interop в ноябре 2025 — первая итерация. Что когда BirdyChat/Haiket провалятся (probable) и DMA enforcement столкнётся с резистенсом Meta? Эта траектория определяет protocol-first window.

5. **Помощь vs зависимость в child mode без раннего детского пилота.** UX Guide описывает invariants, но эмпирика по child mode minimal. Нужен пилот с реальными семьями ДО Phase 1 release child mode features. Это отдельная research-фаза, не Phase 1 scope.

6. **Co-parenting и cross-jurisdiction child mode.** Манифест явно отдаёт это политическому пространству. Но без architectural ответа на двух родителей в разных юрисдикциях через помощь helpline — feature gap.

7. **Voice mode как функциональный а не задушевный.** UX Guide раздел 14 признаёт open question. Если W3 в нише privacy-conscious + journalists, voice — primary channel в часть use cases (interview transcription, conflict reporting). Это ещё одна Phase 2+ задача.

## Жёсткий короткий вывод

Манифест W3 идеологически прав, технически корректен в большинстве деталей (с тремя верифицируемыми исключениями: Wack 2026, TEE без downgrade, anchor problem), но стратегически переигрывает в позиционировании. AIO-риторика "точка входа в интернет — диалог" — overreach. Реальный путь — protocol-first hybrid с reference client для конкретной ниши, использующий DMA-окно 2025-2026 для interop с большими игроками.

Первый ревьюер прав в главном (AIO horizontal провалится) но overshot в мелочах (TEE полностью сломан, Signal model "поглощение", западный AIO precedent). Финальный путь — не один из двух предложенных вариантов, а синтез: продуктовый AIO в нише + спецификация для широкого ecosystem.

Главное действие сейчас — переписать manifest_v3.md в части риторики, принципа 8 и Wack-ссылки. Это можно сделать за 1-2 недели. Без этого Phase 0 нельзя начинать честно.
