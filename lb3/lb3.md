## Комп'ютерні системи імітаційного моделювання
## СПм-24-3, **Васильченко Олександр Сергійович**
### Лабораторна робота №**3**. Використання засобів обчислювального интелекту для оптимізації імітаційних моделей
<br>

### 1. Варіант 4, модель у середовищі NetLogo:
- **Назва:** Urban Suite – Pollution  
- **Посилання на модель:** http://www.netlogoweb.org/launch#http://www.netlogoweb.org/assets/modelslib/Curricular%20Models/Urban%20Suite/Urban%20Suite%20-%20Pollution.nlogo  
---

### 2. Вербальний опис моделі
Модель відтворює взаємодію між людьми (people), деревами (trees) та забрудненням повітря (pollution) у місті з електростанціями.  
Люди рухаються випадково, інколи саджають дерева, можуть розмножуватись і помирають за нестачі здоров'я.  
Електростанції щотакту викидають забруднення на клітині, що дифундує по полю. Дерева очищують повітря на своїй клітині та у сусідів (cleanup), але втрачають здоров'я. Забруднення зменшує здоров'я людей. Симуляція триває, доки у світі залишаються люди.

---

### 3. Керуючі параметри (інтерфейсні слайдери)
- **initial-population** — початкова кількість людей.  
- **birth-rate** — імовірність народження нової людини, якщо її **health > 4**.  
- **planting-rate** — імовірність, що людина посадить дерево цього такту.  
- **power-plants** — кількість електростанцій (клітин), які постійно генерують забруднення.  
- **polluting-rate** — інтенсивність забруднення на клітині електростанції за такт.

---

### Налаштування BehaviorSearch

### Вкладка Model
Параметри моделі:
```
["power-plants" 15]
["initial-population" 50]
["birth-rate" 0.1]
["polluting-rate" [0 1 5]]
["planting-rate" [0 0.01 0.1]]
```

Measure:
```
count people
```
Step limit: **400**

<img width="743" height="507" alt="image" src="https://github.com/user-attachments/assets/929f5d4c-97ca-4d01-ad23-d443d526a5d7" />

---

### Вкладка Search Objective
Goal: **Maximize Fitness**  
Collected measure: **MEAN_ACROSS_STEPS**  
Replicates: **10**  

<img width="731" height="461" alt="image" src="https://github.com/user-attachments/assets/004d1c18-1653-4910-bb91-ddc36a271506" />

---

### Вкладка Search Algorithm
Обрано: **StandardGA**  
evaluation-limit: **100**  
use fitness caching: **true**

Параметри ГА:
- mutation‑rate: 0.03  
- population‑size: 50  
- crossover‑rate: 0.7  
- population-model: generational  
- tournament-size: 3  

<img width="729" height="489" alt="image" src="https://github.com/user-attachments/assets/c33fd785-7daf-416f-a54c-b7e6a5b04144" />


---

### Діалог запуску BehaviorSearch

<img width="600" height="359" alt="image" src="https://github.com/user-attachments/assets/657011ec-f840-4744-ba85-4f6e7c8deb86" />

---

### Результати експериментів

### Генетичний алгоритм (StandardGA)
Найкращі значення параметрів:
```
power-plants = 15
initial-population = 50
birth-rate = 0.1
polluting-rate = 0
planting-rate = 0
Fitness = 535
```

<img width="723" height="523" alt="image" src="https://github.com/user-attachments/assets/96a30a1c-975c-42c8-909e-ace49138fc06" />


Після отримання оптимальних значень параметрів у BehaviorSearch модель була запущена безпосередньо в середовищі NetLogo з цими налаштуваннями. Під час запуску модель відпрацювала стабільно й без помилок.
<img width="841" height="733" alt="image" src="https://github.com/user-attachments/assets/a8156b5c-faba-40e7-9718-b7be047fb36b" />

---

### Випадковий пошук (RandomSearch)
Найкращі значення:
```
power-plants = 15
initial-population = 50
birth-rate = 0.1
polluting-rate = 2
planting-rate = 0.06
Fitness = 443
```

<img width="722" height="526" alt="image" src="https://github.com/user-attachments/assets/b681290d-86f0-435b-a01c-b7dd601a8008" />

Після отримання оптимальних значень параметрів у BehaviorSearch модель була запущена безпосередньо в середовищі NetLogo з цими налаштуваннями. Під час запуску модель відпрацювала стабільно й без помилок.
<img width="847" height="673" alt="image" src="https://github.com/user-attachments/assets/62e9d5e0-dcaa-4d9b-9c92-c62f4e8cdbe1" />

---

## Висновки
У ході лабораторної роботи було проведено оптимізацію параметрів моделі Urban Suite - Pollution за допомогою інструменту BehaviorSearch. Було виконано повний цикл дослідження: визначення параметрів, вибір міри придатності, налаштування цільової функції, запуск двох методів пошуку та подальша перевірка коректності моделі у середовищі NetLogo.
Порівняння результатів показало, що генетичний алгоритм (StandardGA) забезпечив кращий рівень фітнесу (535), на відміну від випадкового пошуку (443). Це демонструє здатність ГА ефективніше знаходити комбінації параметрів, які максимізують кількість людей у моделі протягом симуляції. Однак обидва підходи дали логічні значення параметрів - низький рівень забруднення та помірне озеленення однозначно підвищують чисельність населення.
Перевірка оптимальних параметрів у середовищі NetLogo підтвердила коректність роботи імітаційної моделі: симуляції запускалися стабільно, поведінка агентів відповідала очікуванням, а отримана динаміка населення повністю відповідала результатам BehaviorSearch.

