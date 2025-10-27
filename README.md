# Philosophers_42

<aside>
📃

[https://suspectedoceano.notion.site/Philosophers-b1bf3c57eee6420cafa7d0900b3d3216](https://www.notion.so/Philosophers-b1bf3c57eee6420cafa7d0900b3d3216?pvs=21)

https://medium.com/@ruinadd/philosophers-42-guide-the-dining-philosophers-problem-893a24bc0fe2

https://medium.com/@denaelgammal/dining-philosophers-problem-42-project-guide-mandatory-part-a20fb8dc530e

https://rom98759.github.io/Philosophers-visualizer/

https://github.com/dantonik/42-philosophers-tester

</aside>

## Basic concept

## thread

Un **thread** (ou **fil d’exécution**) est **une unité d’exécution** à l’intérieur d’un processus.

👉 Un programme = un *processus*

👉 qui peut contenir un ou plusieurs *threads* qui s’exécutent **en parallèle** dans la même mémoire.

## 基本概念

## 线程

线程是进程内部执行的基本单位， 一个程序是一个进程，进程内包含了多个同时进行的线程。

## mutex

Un **mutex** (abréviation de **MUTual EXclusion**) est un **verrou**(lock) qui

**empêche plusieurs threads d’accéder en même temps** à une même ressource partagée.

### 互斥锁

互斥锁用于避免多个线程同时访问一个共享的资源

### race condition

Une **race condition** (ou **condition de concurrence**) se produit quand **deux threads accèdent ou modifient une même donnée en même temps**, sans coordination.

Le résultat devient alors **imprévisible**, car il dépend de **l’ordre d’exécution** des threads.

### deadlock

un **deadlock** (ou **interblocage**) se produit quand **plusieurs threads attendent indéfiniment** des ressources détenues les uns par les autres.

Résultat : **aucun thread ne peut avancer** → programme bloqué.

### 竞争态

竞争态是当两个线程同时访问或修改同一个数据，未达成共识。

其结果是不能预料的，有赖于各个线程的执行顺序。

### 锁死

当多个线程争夺相同的资源，谁都无法获得，使得程序无法进行下去。 

结果是没有一个线程可以进行， 程序被锁住。 

## Objectif general

Simuler la **vie de plusieurs philosophes** autour d’une table :

- ils **mangent**, **dorment** et **pensent**
- ils utilisent **des fourchettes (mutex)** partagées
- et tu dois éviter les **problèmes de concurrence** (race condition, deadlock, etc.)

### 🪑 Le problème des philosophes

Chaque philosophe :

1. a une **fourchette à gauche** et une **à droite** 🍴
2. a besoin des **deux** pour manger 🍝
3. puis dort 😴
4. puis pense 💭
5. recommence jusqu’à ce que :
    - un philosophe **meure** de faim (n’a pas mangé à temps) 💀
    - ou que **tous** aient mangé un certain nombre de fois.

## 主要目的

模拟围坐在一张桌子周围的数个哲学家的生活：

吃饭， 睡觉， 思考

邻近的人分享一把叉子用于吃饭

应该避免竞争的问题出现（防止竞争态， 锁死等）

### 哲学家的问题

每个哲学家：

左右各有一把叉子

需要两把叉子才能吃饭

然后睡觉

然后思考

循环往复直至： 一个哲学家因为没有在要求的时间内吃饭而饿死或者所有哲学家都已经吃完了规定的餐数。 

### Les notions fundamentales

1. **Thread**
- Chaque philosophe est un **thread** (`pthread_create`).
- Chaque thread agit **en parallèle**.
- Tous exécutent la même boucle : manger → dormir → penser.

💡 Chaque thread a son propre espace d’exécution, mais **partage la mémoire** avec les autres.

1. **Mutex**
- Une **fourchette = un mutex**.
- Un philosophe doit **verrouiller** ses deux fourchettes pour manger :

```c
pthread_mutex_lock(&left_fork->fork);
pthread_mutex_lock(&right_fork->fork);
```

        Puis il les **déverrouille** :

```c
pthread_mutex_unlock(&left_fork->fork);
pthread_mutex_unlock(&right_fork->fork);
```

    Objectif : éviter qu’un autre philosophe ne “vole” une fourchette pendant qu’il mange.

### 基本概念

**1.线程**

> 每个哲学家是一个线程，每个线程同时进行，所有的线程都执行同样的循环：吃饭，睡觉， 思考。
> 
1. **互斥锁**

> 一个叉子 = 一个互斥锁
> 

> 一个哲学家需要给他的两个叉子上锁才能吃饭，然后解锁。 目的是防止在他吃饭时， 另外的哲学家“偷走“他的叉子。
> 

**锁死**

**计时**

**监控**

**保护区**

3. **Deadlock (blocage mutuel)**

c’est quand **tous les philosophes** attendent une fourchette détenue par un autre.

Exemple :

- Tous prennent leur **fourchette gauche** en même temps,
- Personne ne peut plus prendre sa **fourchette droite**,
- → le programme **bloque pour toujours**.

💡 Solution typique :

- Faire en sorte qu’un philosophe prenne **dans un ordre inverse** (droite puis gauche).
- Ou faire dormir certains avant de commencer.
- Ou vérifier avec un **monitor thread** quand la simulation doit s’arrêter.

1. **锁死**

> 这是当所有哲学家都在等待拿到被另一个哲学家拿到的叉子。
> 

> 例如：
> 

> 所有哲学家同时拿到其左侧的叉子。
> 

> 无人能拿到其右边的叉子。
> 

> 程序被锁死。
> 

> 解决方案：
> 

> 让一个哲学家以相反的顺序拿叉子（先右后左）
> 

> 让其中的某几个哲学家在开始前先睡觉
> 

> 用监控程序来确认何时模拟程序终止。
> 

### 4. **Chronométrage et gestion du temps**

Utilise `gettimeofday()` pour mesurer le temps (en ms ou µs).

Chaque philosophe doit :

- **Mourir** s’il ne mange pas avant `time_to_die`
- **Manger** pendant `time_to_eat`
- **Dormir** pendant `time_to_sleep`

Tu verras souvent une fonction comme :

```c
long gettime(int time_unit);
void precise_usleep(long usec, t_table *table);
```

 `precise_usleep` évite d’endormir trop longtemps (car `usleep` n’est pas précis à 100%).

**5. Monitor / Watcher thread**

Un **thread spécial** (souvent appelé `monitor_dinner`) observe :

- si un philosophe **dépasse son temps de vie** sans manger → il meurt 💀
- si tous les philosophes sont **“full”** → simulation terminée ✅

Ce thread utilise aussi des **mutex** pour lire les variables partagées (`last_meal_time`, `end_simulation`, etc.).

 6. **Section critique**

C’est une partie du code qui **accède à une donnée partagée**.

Exemple :

```c
pthread_mutex_lock(&table->table_mutex);
philo->last_meal_time = gettime(MILLISECOND);
pthread_mutex_unlock(&table->table_mutex);
```

Sans mutex ici, deux threads pourraient modifier la même variable au même moment → incohérence.

### 7. **Structure du projet**

| Structure | Contient | Sert à... |
| --- | --- | --- |
| `t_table` | paramètres globaux, mutex généraux, tableau de philosophes/fourchettes | gérer la simulation entière |
| `t_philo` | infos propres à chaque philosophe (id, forks, last_meal_time, etc.) | représenter un thread |
| `t_fork` | un `pthread_mutex_t` | représenter une fourchette |

8. **Cas particuliers**

- **1 philosophe** → il ne peut jamais manger → il doit mourir après `time_to_die`.

9. **Conditions de course**

Quand deux threads modifient **en même temps** la même variable :

```c
if (philo->full == false)
    philo->full = true;
```

→ Si deux threads y accèdent sans mutex, le résultat peut être incohérent.

C’est pour ça que tu vois beaucoup de fonctions “safe” :

```c
get_bool(&mutex, &variable);
set_long(&mutex, &variable, value);
```

1. **计时器**

> 用gettimeofday()来测量时间（单位是毫秒或微妙）
> 

> 每个哲学家应该：
> 

> 超过规定时间不吃饭会饿死，
> 

> 在time_to_eat时间段吃饭，
> 

> 在time_to_sleep时间段睡觉
> 

> 经常使用的函数是：
> 

> gettime 和  precise_usleep(long usec, t_table *table)
> 

> precise_usleep 是为了避免睡觉时间过长， 因为usleep 不精确。
> 

1. **监控/ 观察线程**

> 用一个特殊的线程进行观察：
> 

> 是否一个哲学家未能在规定时间内吃饭 —-饿死
> 

> 是否所有的哲学家都吃够了规定的餐数
> 

> 这个线程同样需要用互斥锁来读取共享的数据（last_meal_time, end_simulation 等）
> 

**6保护区**

> 这部分代码用于访问共享的数据。
> 

> 如果不使用互斥锁， 两个线程就会同时修改同一个数据，使结果发生偏差。
> 

1. 结构体

t_table

t_philo

t_fork

1. 特殊情况

 当只有一个哲学家时， 他永远不能吃饭， 在time_to_die时间到达后死亡。 

1. 竞争态

当两个线程同时修改一个变量时：

如果没有互斥锁的保护， 结果就会发生

偏差。 

这就是为什么在本项目中用很多的safe函数。 

 

### 📋 10. **Résumé global（总结）**

| Notion | Rôle | Exemple |
| --- | --- | --- |
| Thread | Philosophe | `pthread_create()` |
| Mutex | Fourchette / Protection | `pthread_mutex_lock()` |
| Deadlock | Blocage circulaire | ordre d’accès différent |
| Monitor | Vérifie la mort | boucle `while (!simulation_finished)` |
| Sleep précis | Temps respecté | `precise_usleep()` |
| Condition de course | Accès simultané | variable partagée sans mutex |
| Table | Données globales | `t_table` |
| Philosophe | Thread individuel | `t_philo` |

评估内容：

必填部分
错误处理
本项目使用 C 语言编写，遵循规范。
任何崩溃、未定义行为、内存泄漏或规范错误对项目而言都为 0。
在某些低速硬件上，项目可能无法正常运行。
https://projects.intra.42.fr/scale_teams/3227178/edit
2021年2月46日/13日
项目内部 哲学家 编辑
如果某些测试在您的机器上无法运行，请尝试诚实地讨论，以免将其视为错误。

Philo 代码

- 检查 Philo 代码中是否存在以下问题并寻求解释。
- 检查每个哲学家是否只有一个线程。
- 检查每个哲学家是否只有一个叉子。
- 检查每个是否有互斥锁，以及它是否用于检查值和/或更改值。
- 检查输出不应产生混乱的视图。
- 检查如何检查哲学家的死亡，以及是否存在互斥锁来确保哲学家死亡后
同时开始进食。

哲学测试
- 测试哲学家数量不得超过 200 位
- 测试 time_to_die、time_to_eat 或 time_to_sleep 的时间间隔不得小于 60 毫秒
- 测试哲学家数量为 1,800,200,200 时，哲学家不应进食，而应死亡！
- 测试哲学家数量为 5,800,200,200 时，哲学家不应死亡！
- 测试哲学家数量为 5,800,200,200,7 时，哲学家不应死亡，并且当所有哲学家都进食
至少 7 次后，模拟应停止。
- 使用 4 410 200 200 进行测试，没有人应该死亡！
- 使用 4 310 200 100 进行测试，一位哲学家应该死亡！
- 使用两位哲学家进行测试，并检查不同的时间（死亡延迟超过 10 毫秒是不可接受的）。
- 使用你的值进行测试，以检查所有规则。检查哲学家是否在正确的时间死亡，如果他们……

## **Basic Rules to Test**

For the *Dining Philosophers* project, typical rules are:

1. **Each philosopher must eat without dying** if possible.
2. **A philosopher dies** if the time since their last meal exceeds `time_to_die`.
3. **Only one fork per hand** can be held at a time.
4. **Forks are shared**, so no two philosophers can hold the same fork at the same time.
5. **Simulation ends** if either:
    - Any philosopher dies
    - Optional: all philosophers have eaten `nbr_limit_meals` times

Extra rules (for 42 projects):

- Output timestamps in ms relative to simulation start.
- Avoid deadlocks.
- Synchronize outputs properly (mutex on `printf`).

You can create **different scenarios** by changing:

- Number of philosophers `philo_nbr`
- `time_to_die` (ms)
- `time_to_eat` (ms)
- `time_to_sleep` (ms)
- Optional `nbr_limit_meals`

### **Scenario 1: Single Philosopher (edge case)**

```
./philo 1 800 200 200

```

Expectations:

- Philosopher takes the first fork.
- Cannot take second fork (doesn’t exist).
- Dies after `time_to_die` ms.

✅ Test your `lone_philo` thread and monitor logic.

Scenario 2: Two Philosophers (small concurrency)

```c
./philo 2 800 200 200

```

Expectations:

- Each philosopher can eat if they alternate correctly.
- Check **race conditions**: last meal timestamps must be updated correctly.
- Simulation ends if a philosopher dies.

Scenario 3: Three Philosophers with limited meals

```c
./philo 3 200 60 60 3

```

Expectations:

- Each philosopher eats 3 times.
- Check that **all threads finish** and `end_simulation` is set.
- Monitor should correctly detect if a philosopher dies.

scenario 4: High concurrency stress test

```c
./philo 10 800 200 200

```

- Tests multiple threads trying to take forks simultaneously.
- Check for **deadlocks**.
- Ensure mutexes on forks and printing work correctly.

## **How to Test “Dies at the Right Time”**

- Print debug messages: last meal time, fork acquisition, and death.
- Use **short `time_to_die`** to trigger death before eating.
- Example:

```c
./philo 3 100 200 200

```

Philosophers cannot eat in time → monitor must detect death.

## **Checking Mutex / Race Conditions**

- Run under **Valgrind** with `-tool=helgrind` or `-track-origins=yes`.
- Verify that no **uninitialized reads** occur in `last_meal_time`.
- Ensure mutex is used whenever shared variables are updated:

```c
pthread_mutex_lock(&philo_mutex);
philo->last_meal_time = gettime(MILLISECOND);
pthread_mutex_unlock(&philo_mutex);
```

Printing should also be mutex-protected if multiple threads write to stdout.

## **5️⃣ Observing Deadlocks**

- Check scenarios with odd/even philosophers:

```c
./philo 5 200 100 100

```

- Ensure all can eventually eat without **circular deadlocks**.
- If using “take left then right fork” strategy, add **odd/even philosopher delays**.

### ✅ TL;DR Testing Steps

1. Test **1 philosopher** → must die correctly.
2. Test **2 philosophers** → ensure proper fork sharing and no race conditions.
3. Test **n philosophers with `nbr_limit_meals`** → ensure simulation ends cleanly.
4. Use **short times** to trigger death and check monitor timing.
5. Stress test **many philosophers** → check for deadlocks.
6. Run **Valgrind** → check memory, uninitialized reads, race conditions.
7. Always compare actual timestamps vs expected `time_to_die`.

![image.png](attachment:0a4fd1f5-ede1-487a-97a9-6d63d8398464:image.png)

![Screenshot 2025-10-23 at 09-41-55 Intra Projects Philosophers Edit.png](attachment:23c021be-eae2-416e-ad56-ac26b9793c57:Screenshot_2025-10-23_at_09-41-55_Intra_Projects_Philosophers_Edit.png)

<aside>
💡

MUTEX:

</aside>

**table_mutex :** 

- set all_threads_ready (tous les threads philo peuvent acceder)
- set end_simulation (les threads philo et le thread monitor peuvent acceder)

**write_mutex**:

- write_status (tous les threads philos peuvent acceder)

**philo_mutex:**

> set last_meal_time (le thread philo et le thread monitor)
> 

> get last_meal_time (le thread monitor)
> 

> increase threads_running(le thread monitor et tous les threads philo)
> 

> get trreads_running (le thread monitor)
> 

> set philo_full (le thread philo)
> 

> get philo_full (le thread monitor)
> 

> set meals_counter(le thread philo)
> 

> get meals_counter(le thread monitor)
> 

**fork**

> chaque fork est un mutex. (pour eviter que deux philo_thread  le prend a meme temp)
> 

pourquoi utiliser mutex:

quand plusieurs threads peuvent acceder aux meme resource, pour eviver race condition, on utilise mutex, pour que seulement un thread peux le acceder un temps, quand il finit , l’autre peut acceder.  en resume, c’est pour eviter l’incohence a cause de race condition. (concurrence)

<aside>
💡

pour repondre la question :  “check how the death of a philo is verified, and if there is a mutex for prenvent a philo is dying and start eating at the same time. 

</aside>

## 1. La situation en résumé

Tu as deux threads :

- **Thread philo** → il essaie de manger → met à jour `last_meal_time`
- **Thread monitor** → vérifie s’il doit mourir → lit `last_meal_time`

Et les deux utilisent le **même mutex (`philo_mutex`)**.

Donc : **ils ne peuvent pas agir en même temps**, mais ils se “battent” pour le verrou.

---

## ⚖️ 2. Deux scénarios possibles

### 🟩 Cas 1 — Le **philosophe gagne** le mutex

1. Il lock le mutex.
2. Il met à jour `last_meal_time` (→ il “mange”).
3. Il relâche le mutex.
4. Ensuite, le moniteur prend le mutex et voit une **valeur récente**.

➡️ Le philosophe **survit**.

Même si on était *pile* à `time_to_die`, le fait qu’il ait gagné le mutex “le sauve”.

> → Le philosophe a commencé à manger juste à temps.
> 

---

### 🟥 Cas 2 — Le **moniteur gagne** le mutex

1. Il lock le mutex avant le philosophe.
2. Il lit l’ancien `last_meal_time`.
3. Il voit que le temps écoulé > `time_to_die`.
4. Il déclare le philosophe **mort**.

➡️ Même si juste après le philosophe allait manger, c’est trop tard :

> le moniteur a décrété qu’il était mort avant la mise à jour.
> 

---

## 🧩 3. Donc oui : tout dépend de “qui gagne le mutex”

Ce “compétition” entre threads est **inévitable** :

le projet `philosophers` est justement fait pour que ce genre de course soit *géré proprement*.

Mais ce n’est **pas un bug** :

- c’est une **condition limite réaliste** (comme dans la vraie vie : si tu manges *juste après* la limite, c’est trop tard 😅) ;
- le mutex garantit que la décision (mourir ou manger) sera **cohérente**, jamais incohérente ou corrompue.

---

## 🧠 4. Pourquoi c’est “juste”

Le mutex fait que :

- soit le philosophe *met à jour* `last_meal_time` avant la vérification → il vit,
- soit le moniteur *lit l’ancienne valeur* avant la mise à jour → il meurt.

Il n’y a **pas de troisième cas** (ex. “il meurt pendant qu’il mange” à cause d’un accès concurrent).

👉 Donc le résultat dépend uniquement de l’ordre d’exécution,

et **cet ordre est non déterministe** (mais toujours cohérent).

---

## 🧩 5. Bonus : pourquoi Helgrind ne doit pas crier ici

Helgrind va considérer ces deux accès comme **proprement synchronisés** car :

- chaque lecture/écriture de `last_meal_time` est protégée par le **même mutex** ;
- il n’y a **pas de data race**.

Même si “le résultat dépend de qui gagne”, c’est **logique et sain** — pas une erreur de thread safety.

pour tester avec Valgrind (flag —fair-sched=yes si necessair)

---

Ensure the code is free from data races, deadlocks, and memory leaks. You can use :

— **DRD**: Run with `valgrind --tool=drd` to check for data races.

— **Helgrind**: Run with `valgrind --tool=helgrind` to find lock issues and potential deadlocks.

— **FSanitise**: Compile with `-fsanitize=thread` to detect threading problems while running.

```jsx
./philo 5 500 200 200 3 | grep "is eating" | wc -l
```

```jsx
grep -rn "pthread_mutex”
```

```jsx
valgrind --tool=helgrind --fair-sched=yes ./philo 10 400 100 100 3

```

```jsx
nm philo | grep " [Bb] "  // pour verifier si il y a des variables globals

```
