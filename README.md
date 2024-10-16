# Задача "Понимание JVM"

## Описание
Просмотрите код ниже и опишите (текстово или с картинками) каждую строку с точки зрения происходящего в JVM  

Не забудьте упомянуть про: 
- ClassLoader'ы, 
- области памяти (стэк (и его фреймы), хип, метаспейс)  
- сборщик мусора

**Код для исследования**
```java

public class JvmComprehension {

    public static void main(String[] args) {
        int i = 1;                      // 1
        Object o = new Object();        // 2
        Integer ii = 2;                 // 3
        printAll(o, i, ii);             // 4
        System.out.println("finished"); // 7
    }

    private static void printAll(Object o, int i, Integer ii) {
        Integer uselessVar = 700;                   // 5
        System.out.println(o.toString() + i + ii);  // 6
    }
}

```

## Решение

### ClassLoaders

Запрос на загрузку классов(JvmComprehension, Object, Integer) поступает в Application ClassLoader. 
Если класс не был загружен ранее, то запрос делегируется Platform ClassLoader, а затем Bootstrap ClassLoader.
Bootstrap ClassLoader начинает искать классы. 
Bootstrap ClassLoader загружает классы Object и Integer. 
Далее делегирует работу Platform ClassLoader.
Platform ClassLoader делегирует Application ClassLoader.
Application ClassLoader загружает класс JvmComprehension в Metaspace.
Далее происходит проверка на то, что код валиден.
После загруженные классы связываются. 
Далее на стадии инциализации вызывается статический метод printAll.

### Runtime Data Area

В Stack создается фрейм для метода main.

(1) в Stack создается пременная i и приравнивается к 1. 

(2) в Stack создается ссылка на o, в куче создается объект типа Object, ссылка присвается в переменную o.

(3) в Stack создается ссылка на ii = 2, в куче создается объект типа Integer, ссылка присвается в переменную ii.

(4) в Stack создается фрейм для метода printAll; создаются новые сслыки на Object и Integer с теми же именами, но уже во фрейме printAll.

(5) в Stack создается ссылка на uselessVar = 700, в куче создается объект типа Integer, ссылка присвается в переменную uselessVar.

(6) вызывается метод вывода на экран и для этого создается новый фрейм в Stack(в курче создается объект типа String).

(7) вызывается метод вывода на экран и для этого создается новый фрейм в Stack.

### Execution Engine

### Garbage Collection

Во время работы кода периодически происходит сборка мусора из кучи.
Недостижимые объекты удаляются (uselessVar).
