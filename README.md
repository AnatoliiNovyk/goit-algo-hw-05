# goit-algo-hw-05
## Домашнє завдання до теми "Алгоритми пошуку"
### Завдання 1: Метод `delete` для `HashTable`
*Додамо метод `delete` до реалізації `HashTable`, яка наведена у конспекті. Метод `delete` буде видаляти пару ключ-значення з таблиці.*
```python
class HashTable:
    def __init__(self, size):
        self.size = size
        self.table = [[] for _ in range(self.size)]

    def hash_function(self, key):
        return hash(key) % self.size

    def insert(self, key, value):
        key_hash = self.hash_function(key)
        key_value = [key, value]

        if not self.table[key_hash]:
            self.table[key_hash].append(key_value)
            return True
        else:
            for pair in self.table[key_hash]:
                if pair[0] == key:
                    pair[1] = value  # Оновлюємо значення, якщо ключ вже існує
                    return True
            self.table[key_hash].append(key_value) # Додаємо нову пару, якщо ключа немає
            return True

    def get(self, key):
        key_hash = self.hash_function(key)
        if self.table[key_hash]:
            for pair in self.table[key_hash]:
                if pair[0] == key:
                    return pair[1]
        return None

    def delete(self, key):
        key_hash = self.hash_function(key)
        if self.table[key_hash]:
            for i, pair in enumerate(self.table[key_hash]):
                if pair[0] == key:
                    self.table[key_hash].pop(i)
                    return True
        return False

# Тестування HashTable з методом delete
H = HashTable(5)
H.insert("apple", 10)
H.insert("orange", 20)
H.insert("banana", 30)
H.insert("mango", 40) # Може потрапити в той же бакет, що й apple, якщо hash("mango") % 5 == hash("apple") % 5

print("HashTable перед видаленням:")
for i, bucket in enumerate(H.table):
    print(f"Бакет {i}: {bucket}")

print("\nЗначення для 'apple':", H.get("apple")) # Очікуємо 10
print("Видалення 'apple':", H.delete("apple")) # Очікуємо True
print("Значення для 'apple' після видалення:", H.get("apple")) # Очікуємо None

print("Видалення 'grape' (неіснуючий ключ):", H.delete("grape")) # Очікуємо False

print("\nHashTable після видалень:")
for i, bucket in enumerate(H.table):
    print(f"Бакет {i}: {bucket}")

H.insert("apple", 15) # Додаємо знову
print("\nЗначення для 'apple' після повторного додавання:", H.get("apple")) # Очікуємо 15
print("HashTable після повторного додавання 'apple':")
for i, bucket in enumerate(H.table):
    print(f"Бакет {i}: {bucket}")
```
##### Результат:

```
HashTable перед видаленням:
Бакет 0: []
Бакет 1: [['apple', 10], ['orange', 20], ['mango', 40]]
Бакет 2: []
Бакет 3: [['banana', 30]]
Бакет 4: []

Значення для 'apple': 10
Видалення 'apple': True
Значення для 'apple' після видалення: None
Видалення 'grape' (неіснуючий ключ): False

HashTable після видалень:
Бакет 0: []
Бакет 1: [['orange', 20], ['mango', 40]]
Бакет 2: []
Бакет 3: [['banana', 30]]
Бакет 4: []

Значення для 'apple' після повторного додавання: 15
HashTable після повторного додавання 'apple':
Бакет 0: []
Бакет 1: [['orange', 20], ['mango', 40], ['apple', 15]]
Бакет 2: []
Бакет 3: [['banana', 30]]
Бакет 4: []
```
### Завдання 2: Двійковий пошук для дробових чисел
*Реалізуємо функцію двійкового пошуку для відсортованого масиву дробових чисел. Функція повертатиме кортеж: (кількість ітерацій, "верхня межа"). "Верхня межа" - це найменший елемент масиву, який є більшим або рівним заданому значенню.*
```python
def binary_search_with_upper_bound(arr, target):
    low = 0
    high = len(arr) - 1
    iterations = 0
    upper_bound_candidate = None

    if not arr:
        return (iterations, None)

    # Спочатку знайдемо елемент або визначимо місце для "верхньої межі"
    # Цей цикл шукає перше значення, що >= target
    search_low = 0
    search_high = len(arr) -1
    
    temp_iterations_for_bound = 0
    # Цикл для знаходження "верхньої межі"
    # Ідея полягає в тому, щоб знайти найлівіший елемент, який >= target
    res_idx = -1
    
    current_low, current_high = 0, len(arr) - 1
    while current_low <= current_high:
        temp_iterations_for_bound +=1
        mid = (current_low + current_high) // 2
        if arr[mid] >= target:
            res_idx = mid
            current_high = mid - 1 # Шукаємо лівіше, чи є ще кращий кандидат
        else:
            current_low = mid + 1
            
    if res_idx != -1:
        upper_bound_candidate = arr[res_idx]
    else: # Всі елементи менші за target
        upper_bound_candidate = None

    # Тепер окремо підрахуємо ітерації для знаходження самого елемента (якщо він є)
    # відповідно до умови "кількість ітерацій, потрібних для знаходження елемента"
    # Якщо елемент не знайдено, кількість ітерацій буде відповідати пошуку до кінця.
    
    # Цей цикл для підрахунку ітерацій до знаходження елемента
    iterations_for_find = 0
    low_find, high_find = 0, len(arr) - 1
    found_at_iteration = -1

    while low_find <= high_find:
        iterations_for_find += 1
        mid_find = (low_find + high_find) // 2
        if arr[mid_find] == target:
            # Елемент знайдено, це і є кількість ітерацій для знаходження
            found_at_iteration = iterations_for_find
            break 
        elif arr[mid_find] < target:
            low_find = mid_find + 1
        else:
            high_find = mid_find - 1
    
    # Якщо елемент був знайдений, використовуємо ці ітерації.
    # Якщо ні, то iterations_for_find показує кількість ітерацій, витрачених на пошук.
    # Завдання просить "кількість ітерацій, потрібних для знаходження елемента".
    # Якщо елемент не знайдено, то "потрібних для знаходження" може інтерпретуватися як
    # кількість ітерацій до моменту, коли стало ясно, що його немає.
    # Тому `iterations_for_find` є коректним.

    return (iterations_for_find, upper_bound_candidate)

# Тестування двійкового пошуку
sorted_floats = [1.0, 2.5, 3.0, 4.5, 5.0, 6.5, 7.0, 8.5]
print(f"Масив: {sorted_floats}")

# Елемент існує
target1 = 4.5
iters1, ub1 = binary_search_with_upper_bound(sorted_floats, target1)
print(f"Пошук {target1}: Ітерацій = {iters1}, Верхня межа = {ub1}") # Очікуємо: Ітерацій = 1-3 (залежно від реалізації), Верхня межа = 4.5

# Елемент не існує, але є верхня межа
target2 = 4.0
iters2, ub2 = binary_search_with_upper_bound(sorted_floats, target2)
print(f"Пошук {target2}: Ітерацій = {iters2}, Верхня межа = {ub2}") # Очікуємо: Ітерацій = 3-4, Верхня межа = 4.5

# Елемент не існує, знаходиться між елементами
target3 = 6.0
iters3, ub3 = binary_search_with_upper_bound(sorted_floats, target3)
print(f"Пошук {target3}: Ітерацій = {iters3}, Верхня межа = {ub3}") # Очікуємо: Ітерацій = 2-4, Верхня межа = 6.5

# Елемент менший за всі
target4 = 0.5
iters4, ub4 = binary_search_with_upper_bound(sorted_floats, target4)
print(f"Пошук {target4}: Ітерацій = {iters4}, Верхня межа = {ub4}") # Очікуємо: Ітерацій = 3-4, Верхня межа = 1.0

# Елемент більший за всі
target5 = 9.0
iters5, ub5 = binary_search_with_upper_bound(sorted_floats, target5)
print(f"Пошук {target5}: Ітерацій = {iters5}, Верхня межа = {ub5}") # Очікуємо: Ітерацій = 3-4, Верхня межа = None

# Порожній масив
empty_arr = []
iters_empty, ub_empty = binary_search_with_upper_bound(empty_arr, 5.0)
print(f"Пошук в порожньому масиві: Ітерацій = {iters_empty}, Верхня межа = {ub_empty}") # Очікуємо: Ітерацій = 0, Верхня межа = None

# Масив з одним елементом
single_arr = [10.0]
iters_s1, ub_s1 = binary_search_with_upper_bound(single_arr, 10.0)
print(f"Пошук 10.0 в {single_arr}: Ітерацій = {iters_s1}, Верхня межа = {ub_s1}") # Очікуємо: Ітерацій = 1, Верхня межа = 10.0
iters_s2, ub_s2 = binary_search_with_upper_bound(single_arr, 5.0)
print(f"Пошук 5.0 в {single_arr}: Ітерацій = {iters_s2}, Верхня межа = {ub_s2}") # Очікуємо: Ітерацій = 1, Верхня межа = 10.0
iters_s3, ub_s3 = binary_search_with_upper_bound(single_arr, 15.0)
print(f"Пошук 15.0 в {single_arr}: Ітерацій = {iters_s3}, Верхня межа = {ub_s3}")# Очікуємо: Ітерацій = 1, Верхня межа = None
```

##### Результат:
```
Масив: [1.0, 2.5, 3.0, 4.5, 5.0, 6.5, 7.0, 8.5]
Пошук 4.5: Ітерацій = 1, Верхня межа = 4.5
Пошук 4.0: Ітерацій = 3, Верхня межа = 4.5
Пошук 6.0: Ітерацій = 3, Верхня межа = 6.5
Пошук 0.5: Ітерацій = 3, Верхня межа = 1.0
Пошук 9.0: Ітерацій = 4, Верхня межа = None
Пошук в порожньому масиві: Ітерацій = 0, Верхня межа = None
Пошук 10.0 в [10.0]: Ітерацій = 1, Верхня межа = 10.0
Пошук 5.0 в [10.0]: Ітерацій = 1, Верхня межа = 10.0
Пошук 15.0 в [10.0]: Ітерацій = 1, Верхня межа = None
```

### Завдання 3: Порівняння ефективності алгоритмів пошуку підрядка
*Порівняємо ефективність алгоритмів Боєра-Мура, Кнута-Морріса-Пратта та Рабіна-Карпа на основі двох текстових файлів.  Будемо використовувати timeit для вимірювання часу виконання.*

Спочатку визначимо функції для кожного алгоритму пошуку, спираючись на надані матеріали курсу.
```python
import timeit

# --- Реалізація алгоритму Кнута-Морріса-Пратта (KMP) ---
# З "Алгоритм Кнута-Морріса-Пратта.pdf"
def compute_lps(pattern):
    lps = [0] * len(pattern)
    length = 0
    i = 1
    while i < len(pattern):
        if pattern[i] == pattern[length]:
            length += 1
            lps[i] = length
            i += 1
        else:
            if length != 0:
                length = lps[length - 1]
            else:
                lps[i] = 0
                i += 1
    return lps

def kmp_search(main_string, pattern):
    M = len(pattern)
    N = len(main_string)
    if M == 0: return 0
    if N == 0 or M > N : return -1
    
    lps = compute_lps(pattern)
    
    i = 0  # індекс для main_string
    j = 0  # індекс для pattern
    
    while i < N:
        if pattern[j] == main_string[i]:
            i += 1
            j += 1
        
        if j == M:
            return i - j # Повертаємо індекс першого входження
        elif i < N and pattern[j] != main_string[i]:
            if j != 0:
                j = lps[j - 1]
            else:
                i += 1
    return -1 # Підрядок не знайдено

# --- Реалізація алгоритму Боєра-Мура (BM) ---
# "Алгоритм Боєра-Мура"
def build_shift_table(pattern):
    table = {}
    length = len(pattern)
    # Для кожного символу в підрядку (крім останнього) встановлюємо зсув
    for index, char in enumerate(pattern[:-1]): # [cite: 318]
        table[char] = length - 1 - index # [cite: 320]
    # Якщо останнього символу немає в таблиці, зсув буде дорівнювати довжині підрядка [cite: 318]
    # Або якщо він є, але це його останнє входження, то зсув на довжину підрядка
    # (для символів, що не входять в алфавіт паттерну, але зустрічаються в тексті)
    table.setdefault(pattern[-1], length) # [cite: 318]
    return table

# Спрощена реалізація Боєра-Мура для тестування (з лекції)
def boyer_moore_search_v2(text, pattern):
    m = len(pattern)
    n = len(text)
    if m > n: return -1
    if m == 0: return 0

    shift_table = build_shift_table(pattern)

    s = 0 # Зсув паттерна відносно тексту
    while s <= n - m:
        j = m - 1 # Індекс для паттерна, починаємо з кінця
        while j >= 0 and pattern[j] == text[s + j]:
            j -= 1
        
        if j < 0: # Знайдено
            return s
            # Для пошуку всіх входжень:
            # s += (m - shift_table.get(text[s + m - 1], 0) if s + m < n else 1) 
        else:
            # Зсув на основі символу text[s+j], де відбулась невідповідність.
            # Використовуємо зсув з таблиці для символу text[s+j].
            # Якщо символу немає в таблиці, зсув на довжину паттерну m.
            # Таблиця зсувів, яку ми будуємо (build_shift_table), містить:
            # table[char] = довжина_патерну - 1 - індекс_останнього_входження_char_в_pattern[:-1]
            # table.setdefault(pattern[-1], довжина_патерну)
            # Ця таблиця призначена для зсуву на основі символу в тексті, що *вирівняний з останнім символом патерну*.
            # Коли невідповідність на pattern[j] vs text[s+j], класичний зсув "поганого символу"
            # це max(1, j - позиція_останнього_входження_text[s+j]_в_pattern[0...j-1])
            # Для простоти і відповідності до того, як часто пояснюють базовий BM,
            # зсунемо на основі text[s+m-1] (останній символ поточного вікна тексту).
            
            char_to_shift_by_in_text = text[s + m - 1] # Символ в тексті, що відповідає останньому символу патерну
            shift_val = shift_table.get(char_to_shift_by_in_text, m)
            s += shift_val
            
            # Важливо: Цей зсув є спрощеною версією. 
            # Більш точний зсув "поганого символу" залежить від text[s+j].
            # Наприклад: s += max(1, j - (m - 1 - shift_table.get(text[s+j], m))) - якщо таблиця зберігає індекс.
            # Або просто: s += 1, для найпростішого, але неефективного варіанту.
            # Поточний зсув `s += shift_val` є одним з поширених спрощень.
    return -1


# --- Реалізація алгоритму Рабіна-Карпа (RK) ---
# "Алгоритм Рабіна-Карпа"
def polynomial_hash(s, base, modulus):
    n = len(s)
    hash_value = 0
    for i in range(n):
        hash_value = (hash_value * base + ord(s[i])) % modulus
    return hash_value

def rabin_karp_search(main_string, substring):
    n = len(main_string)
    m = len(substring)

    if m == 0: return 0
    if n == 0 or m > n: return -1

    base = 256
    modulus = 101 

    substring_hash = polynomial_hash(substring, base, modulus)
    current_slice_hash = polynomial_hash(main_string[:m], base, modulus)

    h_multiplier = pow(base, m - 1, modulus) # [cite: 369]

    for i in range(n - m + 1):
        if substring_hash == current_slice_hash:
            if main_string[i:i+m] == substring:
                return i 

        if i < n - m:
            current_slice_hash = (current_slice_hash - ord(main_string[i]) * h_multiplier) % modulus # [cite: 380]
            current_slice_hash = (current_slice_hash * base + ord(main_string[i+m])) % modulus # [cite: 380]
            if current_slice_hash < 0: 
                current_slice_hash += modulus
    return -1


# Функція для читання файлу
def read_file(filepath):
    try:
        with open(filepath, 'r', encoding='utf-8') as f:
            content = f.read()
            # print(f"Файл {filepath} успішно прочитано з кодуванням UTF-8.")
            return content
    except UnicodeDecodeError:
        # print(f"Не вдалося прочитати {filepath} з UTF-8. Спроба з CP1251...")
        try:
            with open(filepath, 'r', encoding='cp1251') as f:
                content = f.read()
                # print(f"Файл {filepath} успішно прочитано з кодуванням CP1251.")
                return content
        except Exception as e_cp1251:
            print(f"Не вдалося прочитати файл {filepath} ні з UTF-8, ні з CP1251. Помилка CP1251: {e_cp1251}")
            return ""
    except FileNotFoundError:
        print(f"Файл {filepath} не знайдено.")
        return ""
    except Exception as e:
        print(f"Загальна помилка при читанні файлу {filepath}: {e}")
        return ""

# Завантаження текстів
text1_content = read_file("стаття 1.txt")
text2_content = read_file("стаття 2.txt")

# Визначення підрядків для пошуку
# Для статті 1
pattern1_existing = "алгоритм" 
pattern1_fictional = "вигаданийпідрядок123" 

# Для статті 2
pattern2_existing = "рекомендаційної системи" 
pattern2_fictional = "фантастичнийтекстXYZ"

search_algorithms = {
    "KMP": kmp_search,
    "Boyer-Moore": boyer_moore_search_v2,
    "Rabin-Karp": rabin_karp_search
}

datasets = {
    "Стаття 1": {
        "text": text1_content,
        "patterns": {
            "існуючий ('алгоритм')": pattern1_existing,
            "вигаданий ('вигаданийпідрядок123')": pattern1_fictional
        }
    },
    "Стаття 2": {
        "text": text2_content,
        "patterns": {
            "існуючий ('рекомендаційної системи')": pattern2_existing,
            "вигаданий ('фантастичнийтекстXYZ')": pattern2_fictional
        }
    }
}

results = {}
number_of_runs = 100 

if not text1_content and not text2_content: # Перевіряємо, чи хоча б один текст завантажено
    print("Не вдалося завантажити тексти статей або вони порожні. Порівняння неможливе.")
else:
    for text_name, data in datasets.items():
        print(f"\n--- Обробка тексту: {text_name} ---")
        results[text_name] = {}
        current_text = data["text"]
        if not current_text: # Додаткова перевірка на порожній текст
            print(f"Текст '{text_name}' порожній. Пропуск.")
            continue
            
        for pattern_desc, current_pattern in data["patterns"].items():
            print(f"  Пошук підрядка ({pattern_desc}), довжина {len(current_pattern)}:")
            results[text_name][pattern_desc] = {}
            if not current_pattern:
                print("    Порожній підрядок. Пропуск.")
                continue

            for algo_name, search_function in search_algorithms.items():
                # Формуємо setup_code динамічно, щоб уникнути імпорту всього підряд в timeit
                setup_parts = [f"from __main__ import {search_function.__name__}, current_text, current_pattern"]
                if algo_name == "KMP":
                    setup_parts.append("from __main__ import compute_lps")
                elif algo_name == "Boyer-Moore":
                    setup_parts.append("from __main__ import build_shift_table")
                elif algo_name == "Rabin-Karp":
                     setup_parts.append("from __main__ import polynomial_hash")
                
                setup_code_final = "\n".join(setup_parts)
                
                # Передаємо функції глобально в timeit, щоб уникнути проблем з __main__
                # та забезпечити доступність всіх необхідних функцій
                timeit_globals = {
                    'current_text': current_text, 
                    'current_pattern': current_pattern,
                    search_function.__name__: search_function # Головна функція пошуку
                }
                # Додаємо допоміжні функції, якщо вони потрібні
                if algo_name == "KMP":
                    timeit_globals['compute_lps'] = compute_lps
                elif algo_name == "Boyer-Moore":
                    timeit_globals['build_shift_table'] = build_shift_table
                elif algo_name == "Rabin-Karp":
                    timeit_globals['polynomial_hash'] = polynomial_hash


                try:
                    time_taken = timeit.timeit(
                        f"{search_function.__name__}(current_text, current_pattern)",
                        setup=setup_code_final, # Це може не знадобитися, якщо globals працює коректно
                        globals=timeit_globals,
                        number=number_of_runs
                    )
                    avg_time = time_taken / number_of_runs
                    results[text_name][pattern_desc][algo_name] = avg_time
                    print(f"    Час для {algo_name}: {avg_time:.6f} секунд (середнє з {number_of_runs} запусків)")
                except Exception as e:
                    print(f"    Помилка при виконанні {algo_name} для '{pattern_desc}' в '{text_name}': {e}")
                    results[text_name][pattern_desc][algo_name] = float('inf') 


# Формування звіту у Markdown
md_report = "# Звіт про ефективність алгоритмів пошуку підрядка\n\n"

# Перевіряємо, чи є результати перед формуванням звіту
if results and any(results.values()): # Перевірка, що results не порожній і містить дані
    for text_name, pattern_results in results.items():
        md_report += f"## {text_name}\n\n"
        
        text_to_analyze = datasets.get(text_name, {}).get("text", "")
        text_len = len(text_to_analyze)
        md_report += f"Довжина тексту: {text_len} символів.\n\n"
        
        md_report += "| Тип підрядка                       | Алгоритм      | Час виконання (сек) |\n"
        md_report += "|------------------------------------|---------------|---------------------|\n"
        for pattern_desc, algo_times in pattern_results.items():
            
            current_pattern_str = datasets.get(text_name, {}).get("patterns", {}).get(pattern_desc, "")
            pattern_len = len(current_pattern_str)
            
            sorted_algo_times = sorted(algo_times.items(), key=lambda item: item[1])
            
            first_entry = True
            for algo_name, time_taken in sorted_algo_times:
                pattern_display_name = f"{pattern_desc} (довжина: {pattern_len})"
                if first_entry:
                    md_report += f"| {pattern_display_name:<34} | {algo_name:<13} | {time_taken:<19.6f} |\n"
                    first_entry = False
                else:
                    md_report += f"| {'':<34} | {algo_name:<13} | {time_taken:<19.6f} |\n"
        md_report += "\n"

    md_report += "## Висновки\n\n"

    overall_fastest_algo_counts = {} 

    for text_name, pattern_results in results.items():
        fastest_in_text_total_time = {} # {algo: total_time_for_this_text}
        md_report += f"### Для тексту '{text_name}':\n"
        for pattern_desc, algo_times in pattern_results.items():
            if not algo_times: continue
            
            valid_algo_times = {k: v for k, v in algo_times.items() if v != float('inf')}
            if not valid_algo_times:
                md_report += f"- Для підрядка '{pattern_desc}': не вдалося визначити найшвидший (можливо, через помилки).\n"
                continue

            best_algo_for_pattern = min(valid_algo_times, key=valid_algo_times.get)
            best_time_for_pattern = valid_algo_times[best_algo_for_pattern]
            md_report += f"- Для підрядка '{pattern_desc}': найшвидший **{best_algo_for_pattern}** ({best_time_for_pattern:.6f} сек).\n"
            
            for algo, time_val in algo_times.items():
                if time_val != float('inf'):
                    fastest_in_text_total_time[algo] = fastest_in_text_total_time.get(algo, 0) + time_val
        
        if fastest_in_text_total_time:
            overall_best_for_text_algo = min(fastest_in_text_total_time, key=fastest_in_text_total_time.get)
            md_report += f"  - **Загалом для '{text_name}'**: найефективнішим (за сумою часу для існуючого та вигаданого підрядків) виявився **{overall_best_for_text_algo}**.\n\n"
            
            overall_fastest_algo_counts[overall_best_for_text_algo] = overall_fastest_algo_counts.get(overall_best_for_text_algo, 0) + 1


    md_report += "### Загальні висновки по всім текстам:\n"
    if overall_fastest_algo_counts:
        sorted_overall_fastest = sorted(overall_fastest_algo_counts.items(), key=lambda item: item[1], reverse=True)
        md_report += "Рейтинг алгоритмів за частотою найкращих сукупних результатів по текстах:\n"
        for i, (algo, count) in enumerate(sorted_overall_fastest):
             md_report += f"{i+1}. **{algo}** (був найшвидшим за сумою часу в {count} тексті/текстах)\n"
    else:
        md_report += "Не вдалося визначити загальний рейтинг через відсутність даних або помилки.\n"

    md_report += "\n**Детальний аналіз:**\n"
    md_report += "- **Алгоритм Кнута-Морріса-Пратта (KMP)**: Має хорошу теоретичну складність $O(N+M)$. Його продуктивність стабільна і не сильно залежить від вмісту тексту та підрядка. Він ефективний, особливо коли алфавіт великий або підрядок має повторювані частини.\n"
    md_report += "- **Алгоритм Боєра-Мура (BM)**: Часто є найшвидшим на практиці для великих алфавітів та довгих підрядків, оскільки дозволяє пропускати значні частини тексту. Його ефективність може знижуватись на коротких алфавітах або якщо підрядок дуже короткий. *У нашому тестуванні використовувалась спрощена версія BM, що може вплинути на його відносну продуктивність.*\n"
    md_report += "- **Алгоритм Рабіна-Карпа (RK)**: Продуктивність залежить від обраної хеш-функції, модуля та базового числа. У середньому показує $O(N+M)$, але в найгіршому випадку (через колізії) може деградувати до $O(NM)$. Ефективний для пошуку множинних патернів.\n\n"
    md_report += "Конкретні результати залежать від характеристик текстів (довжина, алфавіт) та підрядків (довжина, наявність у тексті). "
    md_report += "Для вигаданих підрядків алгоритми, що швидко відкидають невідповідності (як BM), можуть показувати кращі результати. "
    md_report += "Для існуючих підрядків різниця може бути менш помітною, якщо вони знаходяться на початку тексту.\n"

else: # Якщо results порожній або не містить даних
    md_report += "## Результати відсутні\n\n"
    md_report += "Не вдалося отримати результати вимірювань. Можливо, файли статей не були завантажені або виникли інші помилки під час виконання.\n"


print("\n\n--- Markdown Звіт ---")
print(md_report)
```

##### Результат:

```
--- Обробка тексту: Стаття 1 ---
  Пошук підрядка (існуючий ('алгоритм')), довжина 8:
    Час для KMP: 0.000072 секунд (середнє з 100 запусків)
    Час для Boyer-Moore: 0.000013 секунд (середнє з 100 запусків)
    Час для Rabin-Karp: 0.000075 секунд (середнє з 100 запусків)
  Пошук підрядка (вигаданий ('вигаданийпідрядок123')), довжина 20:
    Час для KMP: 0.003380 секунд (середнє з 100 запусків)
    Час для Boyer-Moore: 0.000263 секунд (середнє з 100 запусків)
    Час для Rabin-Karp: 0.004331 секунд (середнє з 100 запусків)

--- Обробка тексту: Стаття 2 ---
  Пошук підрядка (існуючий ('рекомендаційної системи')), довжина 23:
    Час для KMP: 0.000023 секунд (середнє з 100 запусків)
    Час для Boyer-Moore: 0.000008 секунд (середнє з 100 запусків)
    Час для Rabin-Karp: 0.000030 секунд (середнє з 100 запусків)
  Пошук підрядка (вигаданий ('фантастичнийтекстXYZ')), довжина 20:
    Час для KMP: 0.005409 секунд (середнє з 100 запусків)
    Час для Boyer-Moore: 0.000364 секунд (середнє з 100 запусків)
    Час для Rabin-Karp: 0.006866 секунд (середнє з 100 запусків)
```

#### Звіт про ефективність алгоритмів пошуку підрядка

##### Стаття 1

Довжина тексту: 12658 символів.

| Тип підрядка                       | Алгоритм      | Час виконання (сек) |
|------------------------------------|---------------|---------------------|
| існуючий ('алгоритм') (довжина: 8) | Boyer-Moore   | 0.000013            |
|                                    | KMP           | 0.000072            |
|                                    | Rabin-Karp    | 0.000075            |
| вигаданий ('вигаданийпідрядок123') | Boyer-Moore   | 0.000263            |
| (довжина: 20)                      | KMP           | 0.003380            |
|                                    | Rabin-Karp    | 0.004331            |

##### Стаття 2

Довжина тексту: 17591 символів.

| Тип підрядка                        | Алгоритм      | Час виконання (сек) |
|-------------------------------------|---------------|---------------------|
| існуючий ('рекомендаційної системи')| Boyer-Moore   | 0.000008            |
| (довжина: 23)                       | KMP           | 0.000023            |
|                                     | Rabin-Karp    | 0.000030            |
| вигаданий ('фантастичнийтекстXYZ')  | Boyer-Moore   | 0.000364            |
| (довжина: 20)                       | KMP           | 0.005409            |
|                                     | Rabin-Karp    | 0.006866            |

##### Висновки

##### Для тексту 'Стаття 1':
- Для підрядка 'існуючий ('алгоритм')': найшвидший **Boyer-Moore** (0.000013 сек).
- Для підрядка 'вигаданий ('вигаданийпідрядок123')': найшвидший **Boyer-Moore** (0.000263 сек).
  - **Загалом для 'Стаття 1'**: найефективнішим (за сумою часу для існуючого та вигаданого підрядків) виявився **Boyer-Moore**.

##### Для тексту 'Стаття 2':
- Для підрядка 'існуючий ('рекомендаційної системи')': найшвидший **Boyer-Moore** (0.000008 сек).
- Для підрядка 'вигаданий ('фантастичнийтекстXYZ')': найшвидший **Boyer-Moore** (0.000364 сек).
  - **Загалом для 'Стаття 2'**: найефективнішим (за сумою часу для існуючого та вигаданого підрядків) виявився **Boyer-Moore**.

##### Загальні висновки по всім текстам:
Рейтинг алгоритмів за частотою найкращих сукупних результатів по текстах:
**Boyer-Moore** (був найшвидшим за сумою часу в 2 тексті/текстах)

**Детальний аналіз:**
- **Алгоритм Кнута-Морріса-Пратта (KMP)**: Має хорошу теоретичну складність `O(N+M)`. Його продуктивність стабільна і не сильно залежить від вмісту тексту та підрядка. Він ефективний, особливо коли алфавіт великий або підрядок має повторювані частини.
- **Алгоритм Боєра-Мура (BM)**: Часто є найшвидшим на практиці для великих алфавітів та довгих підрядків, оскільки дозволяє пропускати значні частини тексту. Його ефективність може знижуватись на коротких алфавітах або якщо підрядок дуже короткий. У нашому тестуванні використовувалась спрощена версія **BM**, що може вплинути на його відносну продуктивність.
- **Алгоритм Рабіна-Карпа (RK)**: Продуктивність залежить від обраної хеш-функції, модуля та базового числа. У середньому показує `O(N+M)`, але в найгіршому випадку (через колізії) може деградувати до `O(NM)`. Ефективний для пошуку множинних патернів.

Конкретні результати залежать від характеристик текстів (довжина, алфавіт) та підрядків (довжина, наявність у тексті). Для вигаданих підрядків алгоритми, що швидко відкидають невідповідності (як **BM**), можуть показувати кращі результати. Для існуючих підрядків різниця може бути менш помітною, якщо вони знаходяться на початку тексту.
