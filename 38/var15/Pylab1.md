---
jupyter:
  jupytext:
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.17.3
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
---

## Задания

Для $n$ от 1 до $10^5 \cdot N$ c шагом $100 \cdot N$, где $N = (20 - \text{номер студента в списке группе})$, произведите для пяти запусков замер среднего машинного времени исполнения программ, реализующих нижеуказанные алгоритмы и функции. 

Изобразите на графике полученные данные, отражающие зависимость среднего времени исполнения от $n$. 
Проведите теоретический анализ временной сложности рассматриваемых алгоритмов и сравните эмпирическую и теоретическую временные сложности.


### Задание 1

Сгенерируйте $n$-мерный случайный вектор $v = [v_1, v_2, ..., v_n]$ с
неотрицательными элементами. 

Для полученного вектора $v$ осуществите подсчет функций и реализацию алгоритмов:
| №  | Оценивание | Функция | Вариант | Примечание |
|:--|:----------:|:-------|:-------|:----------:|
 1.1.  доступ к элементу по индексу           | 2 балла | $f_1(i) = v(i)$                            | 2, 4, 5, 7, 9, 11, 13, 15, 16, 18 |

 1.3  произведение элементов                  | 2 балла | $f_3(v) = \prod\limits_{k=1}^{n} {v_k}$    | 1, 2, 5, 6, 7, 10, 11, 14, 15, 16? 19 |

 1.6. поиск минимума простым перебором        | 2 балла | $f_6(v) = \min(v)$                         | 3, 5, 6, 8, 10, 13, 15, 17? 19 |

 1.7. среднее арифметическое                  | 2 балла | $f_7(v) = \cfrac{1}{n} \cdot \sum\limits_{k=1}^{n} {v_k}$   | 1, 2, 4, 9, 11, 12, 13, 14, 15, 16, 18 |
```python
import random
import time
import matplotlib.pyplot as plt
import numpy as np
```

```python

def access_element(v, index):
    return v[index]

def product_elements(v):
    result = 1.0
    for x in v:
        result *= x
    return result

def find_minimum(v):
    min_val = v[0]
    for x in v:
        if x < min_val:
            min_val = x
    return min_val

def calculate_mean(v):
    return sum(v) / len(v)

def matrix_multiply(A, B):
    n = len(A)
    C = [[0 for _ in range(n)] for _ in range(n)]
    
    for i in range(n):
        for j in range(n):
            for k in range(n):
                C[i][j] += A[i][k] * B[k][j]
    
    return C

def generate_random_matrix(n):
    return [[random.random() * 100 for _ in range(n)] for _ in range(n)]

def measure_time(func, *args):
    start = time.perf_counter()
    result = func(*args)
    end = time.perf_counter()
    return end - start
```

```python
def run_measurements():
    
    N = 5 
    sizes = list(range(1, ((10**5)*5)+1, 5000))
    
    # Lists to store results
    times_access = []
    times_product = []
    times_min = []
    times_mean = []
    
    print("Starting measurements...")
    print(f"Will test {len(sizes)} different vector sizes")
    print("Each size will be measured 5 times")
    
    for n in sizes:
        
        temp_access = []
        temp_product = []
        temp_min = []
        temp_mean = []
        
        for run in range(5):  
           
            vector = [random.random() * 100 for _ in range(n)]
            index = random.randint(0, n-1)
            
            
            temp_access.append(measure_time(access_element, vector, index))
            temp_product.append(measure_time(product_elements, vector))
            temp_min.append(measure_time(find_minimum, vector))
            temp_mean.append(measure_time(calculate_mean, vector))
        
       
        times_access.append(np.mean(temp_access))
        times_product.append(np.mean(temp_product))
        times_min.append(np.mean(temp_min))
        times_mean.append(np.mean(temp_mean))
    
    
    plt.figure(figsize=(15, 10))
    
    # Plot 1: Access element
    plt.subplot(2, 2, 1)
    plt.plot(sizes, times_access, 'bo-')
    plt.title('1. Access element by index')
    plt.xlabel('Vector size')
    plt.ylabel('Time (seconds)')
    plt.grid(True)
    
    # Plot 2: Product of elements
    plt.subplot(2, 2, 2)
    plt.plot(sizes, times_product, 'ro-')
    plt.title('2. Product of elements')
    plt.xlabel('Vector size')
    plt.ylabel('Time (seconds)')
    plt.grid(True)
    
    # Plot 3: Find minimum
    plt.subplot(2, 2, 3)
    plt.plot(sizes, times_min, 'go-')
    plt.title('3. Find minimum')
    plt.xlabel('Vector size')
    plt.ylabel('Time (seconds)')
    plt.grid(True)
    
    # Plot 4: Calculate mean
    plt.subplot(2, 2, 4)
    plt.plot(sizes, times_mean, 'mo-')
    plt.title('4. Calculate mean')
    plt.xlabel('Vector size')
    plt.ylabel('Time (seconds)')
    plt.grid(True)
    
    plt.tight_layout()
    plt.show()
    
```

```python
run_measurements()
```

```python
def run_matrix_measurements():
    
    matrix_sizes = list(range(1, 101, 5))
    
    print("Starting matrix measurements...")
    print(f"Will test {len(matrix_sizes)} different matrix sizes")
    print("Each size will be measured 5 times")
    
    times_matrix = []
    
    for n in matrix_sizes:
        
        temp_matrix = []
        
        for run in range(5):
            
            A = generate_random_matrix(n)
            B = generate_random_matrix(n)
            
            temp_matrix.append(measure_time(matrix_multiply, A, B))
        
        times_matrix.append(np.mean(temp_matrix))
    
    plt.figure(figsize=(10, 8))
    plt.plot(matrix_sizes, times_matrix, 'co-', linewidth=2, markersize=6)
    plt.title('Matrix multiplication')
    plt.xlabel('Matrix size n')
    plt.ylabel('Time (seconds)')
    plt.grid(True)
    
    plt.tight_layout()
    plt.show()
    
    return times_matrix
```

```python
run_matrix_measurements()
```

```python

```
