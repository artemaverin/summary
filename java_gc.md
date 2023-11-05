## Garbage Collection

[1. Чем Java отличается от C++?](#1-Чем-Java-отличается-от-C)

[2. Что такое менеджер памяти?](#2-Что-такое-менеджер-памяти)

[3. Какой механизм используется в Java для управления памятью?](#3-Какой-механизм-используется-в-Java-для-управления-памятью)

[4. Опишите процесс работы сборщика мусора?](#4-Опишите-процесс-работы-сборщика-мусора)

## 1. Чем Java отличается от C++?

автоматическая сборка мусора отличает Java от языков более низкого уровня, в частности С/С++.

## 2. Что такое менеджер памяти?

Менеджер памяти — часть компьютерной программы (как прикладной, так и операционной системы), обрабатывающая запросы на выделение и освобождение оперативной памяти или (для некоторых архитектур ЭВМ) запросы на включение заданной области памяти в адресное пространство процессора.

В Java, “Менеджер памяти” - это часть среды исполнения Java (JVM), которая отвечает за управление памятью, выделяя ее для объектов, которые создает и использует ваша Java-программа.

Когда вы создаете новый объект в Java, менеджер памяти выделяет память для его хранения. Когда объект больше не используется, менеджер памяти освобождает эту память обратно, чтобы она могла быть использована для других целей.

Важно понимать, что менеджер памяти управляет только памятью для объектов. Он не имеет ничего общего с оперативной памятью или памятью компьютера, на котором выполняется программа. Вместо этого, менеджер памяти работает внутри виртуальной машины Java (JVM) и управляет памятью в рамках этой виртуальной машины.

## 3. Какой механизм используется в Java для управления памятью?

В Java используется несколько механизмов для управления памятью. `Один из них - это сборка мусора (Garbage Collection)`. Сборка мусора - это процесс, во время которого менеджер памяти ищет все объекты, которые больше не используются программой, и удаляет их из памяти.

Кроме того, Java также использует технику под названием “аллокатор памяти” для управления памятью на более низком уровне. Алокатор памяти отвечает за выделение и освобождение памяти для объектов на протяжении всего времени выполнения программы.

`GC смотрит на достижимость объекта по ссылке. Это значит, что если мы не можем получить к нему доступ из программы, т.е. у нас нет ссылки на него, то он помечается как мусор и будет удален при следующей сборке мусора.`

## 4. Опишите процесс работы сборщика мусора

Прежде чем удалить объект, нужно знать, где он находится. Давайте рассмотрим структуру памяти в Java. Все объекты хранятся в куче (heap). Куча делится на поколения (generation). Есть всего два поколения: молодое (young) и старое (old). Young generation состоит из eden и survivor space.

![image](https://github.com/artemaverin/summary/assets/97846877/2e67f82c-1f51-43dc-9cf3-2cf2569ef139)

# Сборка мусора

Для сборки мусора используется алгоритм пометок (Mark & Sweep). Этот алгоритм состоит из трех этапов:

- Mark (маркировка). На первом этапе GC сканирует все объекты и помечает живые (объекты, которые все еще используются). На этом шаге выполнение программы приостанавливается. Поэтому этот шаг также называется "Stop the World" .

- Sweep (очистка). На этом шаге освобождается память, занятая объектами, не отмеченными на предыдущем шаге.

- Compact (уплотнение). Объекты, пережившие очистку, перемещаются в единый  непрерывный блок памяти. Это уменьшает фрагментацию кучи и позволяет проще и быстрее размещать новые объекты.

![image](https://github.com/artemaverin/summary/assets/97846877/5d492fb8-9075-4468-a597-8587bb96425c)


Сборка мусора происходит, когда заполнена вся область памяти. Память делится на два поколения, поэтому есть два типа сборки мусора: _minor GC_ и _major GC_. Первый происходит, когда переполняется young generation, второй, когда переполняется область из old generation.

Теперь поговорим какие стадии проходят объекты до сборки мусора.

1. Объект рождается. Во время исполнения JVM видит, что стоит оператор new. Происходит выделение памяти под объект и возврат ссылки, которая будет ссылаться на занятый участок памяти. Все объекты рождаются в eden

![image](https://github.com/artemaverin/summary/assets/97846877/a2cdacdf-071a-4fc0-837e-14323d34eb34)

2. Этап 1 выполняет до тех пор, пока не будет заполнен eden

![image](https://github.com/artemaverin/summary/assets/97846877/75718acc-1717-46fc-8d0d-b2da743c8e6a)

Когда eden заполнен происходит minor GC:

3. Объекты, у которых уже нет ссылки удаляются

![image](https://github.com/artemaverin/summary/assets/97846877/b38f1d9b-81d0-4124-a379-a7d3f4f4cd3d)

4. Объекты, у которых есть ссылки попадают в survivor space из eden. Причем survivor space делиться на две части. Между этими частями происходит перемещения объектов. В один момент времени одна из частей пуста, чтоб мочь вместить объекты пришедшие из eden.

![image](https://github.com/artemaverin/summary/assets/97846877/6fae5c49-d684-4279-a060-5945ed94a4de)

5. Объекты, которые уже были в одной части survivor space, перемещаются в другую, при этом растет их «возраст» (age). Сам процесс, перемещения объектов из различных частей survivor space и увеличения их возраста называется «взрослением» (aging).

![image](https://github.com/artemaverin/summary/assets/97846877/fc80edb6-89a3-4755-aa06-9f68828592da)

6. Объекты, которые достигли определенного возраста попадают в old generation. Этот процесс называется «продвижением» promotion.

![image](https://github.com/artemaverin/summary/assets/97846877/21013d1a-0778-47b3-a9ca-a3a6a1fe1aab)

Этапы 1-6 происходят до тех пока не будет заполнен old generation, причем по ходу будут происходить minor GC, для очищения young generation.

7. Когда old generation заполняется производиться major GC

8. Этапы 1-7 происходят на протяжении работы программы