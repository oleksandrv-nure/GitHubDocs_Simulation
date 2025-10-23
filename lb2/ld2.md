
# Комп'ютерні системи імітаційного моделювання  
## СПм-24-3, **Васильченко Олександр**
### Лабораторна робота №**2**. Редагування імітаційних моделей у середовищі NetLogo

<br>

### Варіант **4**, модель у середовищі NetLogo:
**Urban Suite - Pollution** 

<br>

## Мета роботи
Отримати уявлення про синтаксис та можливості NetLogo та навчитися модифікувати логіку поведінки наявної моделі. Для варіанту 4 - реалізувати позитивний вплив електростанцій на:  
1) ймовірність появи нових людей;  
2) ймовірність висадки дерев у клітинах поблизу електростанцій;   
3) додати одну або більше змін на власний розсуд.

---

## Внесені зміни у вихідну логіку моделі, **за варіантом**

### 1) Маркування клітин поблизу електростанцій
Додано прапорець `near-plant?` у патчів і процедуру його оновлення за радіусом `power-plant-radius`:

```netlogo
patches-own [
  pollution
  is-power-plant?
  near-plant?                 ;; ADDED: within radius of any power plant
]

to update-near-plant
  ask patches [
    set near-plant? any? patches with [ is-power-plant? ] in-radius power-plant-radius
  ]
end
```

### 2) Позитивний вплив на народжуваність поблизу електростанцій
Базова ймовірність народження підвищується на величину `plant-birth-bonus`, якщо поточний патч біля станції:
```netlogo
to reproduce  ;; person procedure
  let local-birth-rate birth-rate
  if [near-plant?] of patch-here [
    set local-birth-rate birth-rate + plant-birth-bonus    ;; ADDED
  ]

  if health > 4 and random-float 1 < local-birth-rate [
    hatch-people 1 [
      set health 5
      set color black
    ]
  ]
end
```

### 3) Більша ймовірність висадки дерев біля станцій
Шанс висадки збільшується на `planting-near-plant-bonus`:
```netlogo
to maybe-plant  ;; person procedure
  let local-planting-rate planting-rate
  if [near-plant?] of patch-here [
    set local-planting-rate planting-rate + planting-near-plant-bonus   ;; ADDED
  ]

  if random-float 1 < local-planting-rate [
    hatch-trees 1 [
      set health 5
      set color green
    ]
  ]
end
```

> **Інтерфейсні елементи:** слайдери `power-plant-radius`, `plant-birth-bonus`, `planting-near-plant-bonus`.
<img width="830" height="713" alt="image" src="https://github.com/user-attachments/assets/6d47c2df-e94f-4630-89d7-a182c28980d0" />
---

### A) Planting near plant — Обчислювальний експеримент

**Мета.** Оцінити вплив локального бонусу до висадки дерев у клітинах поблизу електростанцій (`planting-near-plant-bonus`) на кінцеві метрики моделі — чисельність людей, чисельність дерев і сумарне забруднення на 200-му тіку. 

**Налаштування :**
- `initial-population = 30`
- `power-plants = 10`
- `polluting-rate = 3`
- `birth-rate = 0.1`
- `planting-rate = 0.05`
- `power-plant-radius = 4`
- `plant-birth-bonus = 0`, 
- Тривалість: авто-стоп на 200 тіках

**Сценарій A (без бонусу до посадки):** `planting-near-plant-bonus = 0`  
**Сценарій B (з бонусом до посадки):** `planting-near-plant-bonus від 0.01 до 0.2`

**Що фіксувати:**  
- `final count people`
- `final count trees`
- `final sum [pollution] of patches`  

**Таблиця для результатів:**

| planting-near-plant-bonus | final people | final trees | final pollution |
|---|---:|---:|---:|
| A: 0 | 11 | 89 | 403 |
| B: 0.02 | 3 | 73 | 518 |
| B: 0.04 | 82 | 285 | 180 |
| B: 0.06 | 196 | 802 | 196 |
| B: 0.08 | 26 | 227 | 108 |
| B: 0.1 | 91 | 252 | 66 |
| B: 0.12 | 130 | 666 | 7.7 |
| B: 0.14 | 195 | 775 | 10 |
| B: 0.16 | 473 | 1450 | 3 |
| B: 0.18 | 329 | 1481 | 0 |
| B: 0.2 | 253| 818 | 5 |
<img width="1085" height="614" alt="image" src="https://github.com/user-attachments/assets/ff255cdb-74d8-4be1-8e03-06937233010c" />

**Висновки:** 
Без бонусу до посадки озеленення слабке, забруднення високе, популяція людей зростає повільно. Невеликі бонуси помітного ефекту не дають і інколи навіть погіршують ситуацію — це пояснюється стохастикою моделі. Починаючи з середніх значень бонусу видно зсув: дерев стає більше, забруднення падає, а кількість людей зростає. На високих значеннях тенденція загалом зберігається, хоча можливі поодинокі немонотонні відхилення (сплески чи просідання) через випадковість симуляції.


---
## Внесені зміни, **на власний розсуд**

### B) Health-aware migration (міграція за станом здоров’я)
Коли здоров’я агента нижче порогу, він намагається перейти в **чистішу** клітину в радіусі. Вибір — **випадкова** клітина серед тих, що мають **мінімальне** забруднення.

Додано параметри:
```netlogo
  low-health-threshold   ;; нижче цього значення вмикаємо "оздоровчу" міграцію
  seek-clean-radius      ;; радіус пошуку кращого місця (патчів)
  health-seek-prob       ;; імовірність спробувати міграцію на цьому тіку
```

Заміна руху `wander`:
```netlogo
to wander  ;; person procedure
  ;; Health-aware migration
  if (health < low-health-threshold) and (0 < health-seek-prob) and (random-float 1 < health-seek-prob) [
    let here patch-here
    let candidates patches in-radius seek-clean-radius with [ self != here ]
    if any? candidates [
      let min-pol min [ pollution ] of candidates
      let best one-of candidates with [ pollution = min-pol ]  ;; random among the cleanest
      face best
      fd 1
      set health health - 0.05
      stop
    ]
  ]
  ;; fallback: random walk
  rt random-float 50
  lt random-float 50
  fd 1
  set health health - 0.1
end
```

> **Інтерфейсні елементи:** слайдери `low-health-threshold`, `seek-clean-radius`, `health-seek-prob`.

### Авто-стоп симуляції
Щоб експерименти були відтворювані, модель автоматично зупиняється після 200 тіків:
```netlogo
to go
  if ticks >= 200 [ stop ]     ;; ADDED
  ...
end
```

---

## Керування моделлю (інтерфейс)
- **initial-population** — початкова кількість людей
- **birth-rate** — базова ймовірність народження
- **planting-rate** — базова ймовірність висадки дерев
- **power-plants** — кількість електростанцій
- **polluting-rate** — річний викид станції
- **power-plant-radius** — радіус “впливу станції” для бонусів
- **plant-birth-bonus** — додаткова ймовірність народження біля станції
- **planting-near-plant-bonus** — додаткова ймовірність висадки біля станції
- **low-health-threshold**, **seek-clean-radius**, **health-seek-prob** — параметри health-aware міграції

**Монітори:** `count people`, `count trees`, `sum [pollution] of patches`.  

---

## Обчислювальний експеримент
**Мета:** оцінити вплив health-aware міграції на забруднення, кількість дерев та чисельність населення.

**Налаштування :**
- `initial-population = 30`
- `power-plants = 10`
- `polluting-rate = 3`
- `birth-rate = 0.1`
- `planting-rate = 0.05`
- `power-plant-radius = 1`
- `plant-birth-bonus = 0`, 
- `planting-near-plant-bonus = 0`
- `low-health-threshold = 2`
- `seek-clean-radius = 4`
- Тривалість: авто-стоп на 200 тіках

**Сценарій A (без міграції):** `health-seek-prob = 0`  
**Сценарій B (з міграцією):** `health-seek-prob від 0.1 до 1`

**Що фіксувати:**  
- `final count people`
- `final count trees`
- `final sum [pollution] of patches`  

**Таблиця для результатів:**

| health-seek-prob | final people | final trees | final pollution |
|---|---:|---:|---:|
| A: 0 | 60 |  68 | 590 |
| A: 0 | 0 | 34 | 400 |
| B: 0.1 | 3 | 88 | 266 |
| B: 0.2 | 91 | 360 | 85 |
| B: 0.3 | 138 | 431 | 60 |
| B: 0.4 | 196 | 481 | 124 |
| B: 0.5 | 200 | 662 | 64 |
| B: 0.6 | 208 |  481 | 48 |
| B: 0.7 | 80 | 242 | 408 |
| B: 0.8 | 150 | 611 | 55 |
| B: 0.9 | 447 | 916 | 31 |
| B: 1 | 783 | 1502 | 19 |

<img width="1083" height="618" alt="image" src="https://github.com/user-attachments/assets/5d6c020d-c820-4e14-aa68-c34f32056333" />


**Висновки:** 
За заданих налаштувань health-aware міграція покращує стан системи: без міграції результати нестабільні аж до вимирання популяції та високої полюції (400–590), тоді як зі зростанням health-seek-prob від 0.1 до 1.0 забруднення зменшується (мінімум 19), а кількість людей і дерев зростає; найкращий ефект спостерігається при 0.8–1.0, а провал на 0.7 виглядає як стохастична аномалія.
