# Реєстр джерел і доказів

Реєстр доповнює [технічний звіт](01-technical-report.md). Кожне посилання на вихідний код фіксує повний SHA коміту. Діапазони тут можуть бути довшими за 3–5 рядків, оскільки це навігація до контексту; безпосередні цитати у звіті розбиті на короткі фрагменти з порядковими поясненнями.

## 1. Зафіксовані версії

| Офіційний репозиторій | Коміт, використаний у всьому комплекті |
|---|---|
| Uniswap/v2-core | `6a9e7c97860676e0992f22a49665760444c1cdf5` |
| Uniswap/v3-core | `5ea68a77cb541c6b3812df783be71ba8c081e25f` |
| Uniswap/v4-core | `bea7f6d1c11cd5dc4f0d5151ffa37a1c239feee6` |

SHA визначає знімок репозиторію, а не адресу контракту чи доказ тотожності deployed bytecode. Номери рядків перевіряються саме для цього знімка. Дата підготовки комплекту: 27 жовтня 2026 року. Офіційна документація може оновлюватися; точну реалізацію підтверджує зафіксований код.

## 2. V2: баланс після callback і економічний інваріант

| Джерело | Що підтверджує |
|---|---|
| [UniswapV2Pair.sol, L30–36](https://github.com/Uniswap/v2-core/blob/6a9e7c97860676e0992f22a49665760444c1cdf5/contracts/UniswapV2Pair.sol#L30-L36) | `lock` блокує повторний вхід у захищені функції того самого pair |
| [UniswapV2Pair.sol, L44–47](https://github.com/Uniswap/v2-core/blob/6a9e7c97860676e0992f22a49665760444c1cdf5/contracts/UniswapV2Pair.sol#L44-L47) | `_safeTransfer` перевіряє успіх ERC-20-виклику та повернене значення |
| [UniswapV2Pair.sol, L72–85](https://github.com/Uniswap/v2-core/blob/6a9e7c97860676e0992f22a49665760444c1cdf5/contracts/UniswapV2Pair.sol#L72-L85) | Оновлення резервів і накопичення ціни за попередніми резервами та минулим часом |
| [UniswapV2Pair.sol, L159–174](https://github.com/Uniswap/v2-core/blob/6a9e7c97860676e0992f22a49665760444c1cdf5/contracts/UniswapV2Pair.sol#L159-L174) | Вхід у `swap`, оптимістичний output, callback адресату `to`, читання фактичних балансів |
| [UniswapV2Pair.sol, L176–187](https://github.com/Uniswap/v2-core/blob/6a9e7c97860676e0992f22a49665760444c1cdf5/contracts/UniswapV2Pair.sol#L176-L187) | Визначення input, корекція на комісію, `K`-перевірка, оновлення резервів |

## 3. V3: ціна, активна ліквідність і оплата розрахованого обміну

| Джерело | Що підтверджує |
|---|---|
| [IUniswapV3PoolState.sol, L46–48](https://github.com/Uniswap/v3-core/blob/5ea68a77cb541c6b3812df783be71ba8c081e25f/contracts/interfaces/pool/IUniswapV3PoolState.sol#L46-L48) | Визначення `liquidity` як поточної ліквідності в діапазоні |
| [UniswapV3Pool.sol, L56–74](https://github.com/Uniswap/v3-core/blob/5ea68a77cb541c6b3812df783be71ba8c081e25f/contracts/UniswapV3Pool.sol#L56-L74) | Поля `Slot0`: ціна, tick, параметри observations і `unlocked` |
| [UniswapV3Pool.sol, L89–109](https://github.com/Uniswap/v3-core/blob/5ea68a77cb541c6b3812df783be71ba8c081e25f/contracts/UniswapV3Pool.sol#L89-L109) | Активна `liquidity`, сховища ticks/observations і спільний модифікатор блокування |
| [UniswapV3Pool.sol, L596–615](https://github.com/Uniswap/v3-core/blob/5ea68a77cb541c6b3812df783be71ba8c081e25f/contracts/UniswapV3Pool.sol#L596-L615) | `swap` вручну перевіряє та вимикає `slot0.unlocked` |
| [UniswapV3Pool.sol, L640–671](https://github.com/Uniswap/v3-core/blob/5ea68a77cb541c6b3812df783be71ba8c081e25f/contracts/UniswapV3Pool.sol#L640-L671) | Пошук наступного tick і розрахунок окремого кроку swap |
| [UniswapV3Pool.sol, L709–725](https://github.com/Uniswap/v3-core/blob/5ea68a77cb541c6b3812df783be71ba8c081e25f/contracts/UniswapV3Pool.sol#L709-L725) | Перетин tick, знак `liquidityNet`, оновлення активної ліквідності |
| [UniswapV3Pool.sol, L732–755](https://github.com/Uniswap/v3-core/blob/5ea68a77cb541c6b3812df783be71ba8c081e25f/contracts/UniswapV3Pool.sol#L732-L755) | Запис oracle, ціни, tick і ліквідності до зовнішнього callback |
| [UniswapV3Pool.sol, L771–787](https://github.com/Uniswap/v3-core/blob/5ea68a77cb541c6b3812df783be71ba8c081e25f/contracts/UniswapV3Pool.sol#L771-L787) | Output адресату `recipient`, callback на `msg.sender`, обидві перевірки `IIA`, розблокування |
| [UniswapV3Pool.sol, L791–814](https://github.com/Uniswap/v3-core/blob/5ea68a77cb541c6b3812df783be71ba8c081e25f/contracts/UniswapV3Pool.sol#L791-L814) | Окрема функція `flash` і `uniswapV3FlashCallback` |
| [TickMath.sol, L18–23](https://github.com/Uniswap/v3-core/blob/5ea68a77cb541c6b3812df783be71ba8c081e25f/contracts/libraries/TickMath.sol#L18-L23) | Зв’язок tick із `sqrt(1.0001^tick)` та масштабом Q64.96 |
| [SqrtPriceMath.sol, L145–173](https://github.com/Uniswap/v3-core/blob/5ea68a77cb541c6b3812df783be71ba8c081e25f/contracts/libraries/SqrtPriceMath.sol#L145-L173) | Обчислення дельти token0 за ліквідністю та межами квадратного кореня ціни, кероване округлення |
| [Oracle.sol, L30–43](https://github.com/Uniswap/v3-core/blob/5ea68a77cb541c6b3812df783be71ba8c081e25f/contracts/libraries/Oracle.sol#L30-L43) | Накопичення `tick × час` і часу, поділеного на ліквідність |
| [IUniswapV3SwapCallback.sol, L7–19](https://github.com/Uniswap/v3-core/blob/5ea68a77cb541c6b3812df783be71ba8c081e25f/contracts/interfaces/callback/IUniswapV3SwapCallback.sol#L7-L19) | Семантика додатних сум, що належать пулу, та вимога автентифікації викликача callback |

## 4. V4: сесія, тимчасовий облік і hooks

| Джерело | Що підтверджує |
|---|---|
| [PoolKey.sol, L10–22](https://github.com/Uniswap/v4-core/blob/bea7f6d1c11cd5dc4f0d5151ffa37a1c239feee6/src/types/PoolKey.sol#L10-L22) | Ідентифікація пулу через валюти, комісію, крок ticks і hooks |
| [PoolManager.sol, L95–114](https://github.com/Uniswap/v4-core/blob/bea7f6d1c11cd5dc4f0d5151ffa37a1c239feee6/src/PoolManager.sol#L95-L114) | `onlyWhenUnlocked`, заборона вкладеного `unlock`, callback, перевірка `NonzeroDeltaCount`, повторний lock |
| [Lock.sol, L10–26](https://github.com/Uniswap/v4-core/blob/bea7f6d1c11cd5dc4f0d5151ffa37a1c239feee6/src/libraries/Lock.sol#L10-L26) | Запис і читання transient-прапорця через `tstore`/`tload` |
| [CurrencyDelta.sol, L9–40](https://github.com/Uniswap/v4-core/blob/bea7f6d1c11cd5dc4f0d5151ffa37a1c239feee6/src/libraries/CurrencyDelta.sol#L9-L40) | Ключ `(target, currency)` і збереження знакової дельти в transient storage |
| [NonzeroDeltaCount.sol, L12–34](https://github.com/Uniswap/v4-core/blob/bea7f6d1c11cd5dc4f0d5151ffa37a1c239feee6/src/libraries/NonzeroDeltaCount.sol#L12-L34) | Transient-лічильник ненульових записів |
| [PoolManager.sol, L367–383](https://github.com/Uniswap/v4-core/blob/bea7f6d1c11cd5dc4f0d5151ffa37a1c239feee6/src/PoolManager.sol#L367-L383) | Лічильник змінюється на переходах нуль ↔ ненуль; окремий облік двох валют |
| [PoolManager.sol, L187–226](https://github.com/Uniswap/v4-core/blob/bea7f6d1c11cd5dc4f0d5151ffa37a1c239feee6/src/PoolManager.sol#L187-L226) | Порядок swap, hooks і запису дельт користувача та hook |
| [Pool.sol, L439–449](https://github.com/Uniswap/v4-core/blob/bea7f6d1c11cd5dc4f0d5151ffa37a1c239feee6/src/libraries/Pool.sol#L439-L449) | Стан пулу змінюється під час операції, а не лише під час фінального settlement |
| [PoolManager.sol, L278–307](https://github.com/Uniswap/v4-core/blob/bea7f6d1c11cd5dc4f0d5151ffa37a1c239feee6/src/PoolManager.sol#L278-L307) | `sync`, `take`, `settle` та `settleFor` |
| [PoolManager.sol, L348–365](https://github.com/Uniswap/v4-core/blob/bea7f6d1c11cd5dc4f0d5151ffa37a1c239feee6/src/PoolManager.sol#L348-L365) | Обчислення фактично сплаченого ERC-20 за різницею балансів; native-оплата через `msg.value` |
| [IHooks.sol, L9–21](https://github.com/Uniswap/v4-core/blob/bea7f6d1c11cd5dc4f0d5151ffa37a1c239feee6/src/interfaces/IHooks.sol#L9-L21) | Правильна назва інтерфейсу та залежність дозволів hook від бітів його адреси |
| [Hooks.sol, L129–155](https://github.com/Uniswap/v4-core/blob/bea7f6d1c11cd5dc4f0d5151ffa37a1c239feee6/src/libraries/Hooks.sol#L129-L155) | Зовнішній виклик hook і перевірка повернутого selector |
| [Hooks.sol, L247–314](https://github.com/Uniswap/v4-core/blob/bea7f6d1c11cd5dc4f0d5151ffa37a1c239feee6/src/libraries/Hooks.sol#L247-L314) | Дозволи `beforeSwap`/`afterSwap` і обробка повернених дельт |
| [IUnlockCallback.sol, L4–9](https://github.com/Uniswap/v4-core/blob/bea7f6d1c11cd5dc4f0d5151ffa37a1c239feee6/src/interfaces/callback/IUnlockCallback.sol#L4-L9) | Інтерфейс callback контракту, який відкрив сесію; це окремий механізм від hooks |

## 5. Першоджерела архітектури та математики

| Джерело | Розділ / використання |
|---|---|
| [Uniswap V2 Core Whitepaper](https://uniswap.org/whitepaper.pdf) | §2.2 — ціновий оракул; §2.3 — Flash Swaps; §3.3 — reentrancy mutex |
| [Uniswap V3 Core Whitepaper](https://uniswap.org/whitepaper-v3.pdf) | §2 — концентрована ліквідність; §5 — оракули; §6 — ticks і представлення стану |
| [Офіційний посібник V2 Flash Swaps](https://developers.uniswap.org/docs/protocols/v2/guides/flash-swaps) | Зміст `data`, callback і погашення тією самою або іншою валютою |
| [V4 Architecture](https://developers.uniswap.org/docs/protocols/v4/concepts/architecture) | Singleton, hooks і архітектурні причини зміни витрат |
| [V4 PoolManager](https://developers.uniswap.org/docs/protocols/v4/concepts/poolmanager) | Зберігання станів пулів і організація операцій |
| [V4 Flash Accounting](https://developers.uniswap.org/docs/protocols/v4/concepts/flash-accounting) | Взаємне погашення дельт і скорочення проміжних переказів |
| [V4 Hooks](https://developers.uniswap.org/docs/protocols/v4/concepts/hooks) | Точки життєвого циклу, дозволи та pool-specific налаштування |
| [EIP-1153](https://eips.ethereum.org/EIPS/eip-1153) | `TLOAD`/`TSTORE`, область видимості, очищення наприкінці транзакції та відкат |

Для точних назв функцій, умов і порядку записів пріоритет має код обраного коміту. Whitepapers пояснюють модель; документація — інтеграційний контекст. Приклад: автоматичне очищення transient storage описує EIP, а обов’язковість нульових дельт забезпечує явна перевірка в `PoolManager.unlock`.

## 6. Допоміжні джерела навчального посібника

| Джерело | Що пояснює |
|---|---|
| [Solidity: Introduction to Smart Contracts](https://docs.soliditylang.org/en/latest/introduction-to-smart-contracts.html) | Контракти, стан і базова модель виконання |
| [Solidity: Block and Transaction Properties](https://docs.soliditylang.org/en/latest/units-and-global-variables.html#block-and-transaction-properties) | Значення `msg.sender` і відмінність від `tx.origin` |
| [Solidity: External Function Calls](https://docs.soliditylang.org/en/latest/control-structures.html#external-function-calls) | Передавання керування між контрактами |
| [Solidity: Error Handling](https://docs.soliditylang.org/en/latest/control-structures.html#error-handling-assert-require-revert-and-exceptions) | `revert`, низькорівневі виклики й обробка помилок |
| [Uniswap Labs: A Primer on V3 Math](https://blog.uniswap.org/uniswap-v3-math-primer) | Формат Q96, ціна та decimals |
| [Concentrated Liquidity](https://developers.uniswap.org/docs/get-started/concepts/liquidity-providers/concentrated-liquidity) | Діапазони позицій і активна ліквідність |
| [V2 Flash Swaps](https://developers.uniswap.org/docs/protocols/v2/concepts/flash-swap) | Оптимістична видача та завершення розрахунку |
| [V2 Oracles](https://developers.uniswap.org/docs/protocols/v2/concepts/oracles) | Накопичувачі ціни та вікно усереднення |
| [V4: Building with Flash Accounting](https://developers.uniswap.org/docs/protocols/v4/guides/flash-accounting) | Знаки дельт і композиція операцій |
| [V4: Unlock Callback & Deltas](https://developers.uniswap.org/docs/protocols/v4/guides/unlock-callback-and-deltas) | Організація callback, оплата та отримання активів |

## 7. Правила перевірки посилань

1. Посилання на код має вигляд `https://github.com/Uniswap/<repo>/blob/<40-символьний-SHA>/<path>#L<start>-L<end>`.
2. Файл і діапазон мають існувати саме в цьому коміті; назва функції має відповідати поясненню.
3. У цитаті зберігаються порядок рядків, відступи та оригінальні коментарі. Українські пояснення розташовані поза цитатою.
4. Під час зміни SHA потрібно повторно перевірити всі номери рядків; зміна лише URL недостатня.
5. Реєстр не підміняє посилань біля цитат у звіті.

Оформлення HTML-презентації спирається на [офіційну документацію Tailwind Play CDN](https://tailwindcss.com/docs/installation/play-cdn); вона не є джерелом тверджень про Uniswap.
