https://github.com/hi-iamdeveloper/Hangman_game-zhukovsd-Project1  
[Иван]

Игра написана в смешанном ООП - процедурном стиле.  

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Из пяти слов?
```
Игра запущена! Слово состоит из 5
```

2. Можно ввести не только русскую букву, но и буквы других алфавитов  
3. Нет списка введенных букв  

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву

## ЗАМЕЧАНИЯ

**1. Нейминг**

- UPPER_SNAKE только для констант, а это не константа
```java
private final String PATH;

//ПРАВИЛЬНО ТАК:
private final String path;

//ИЛИ ТАК:
private static final String PATH;
```

- В сигнатуре методов не бывает констант
```java
public WordProvider(String PATH)

//ПРАВИЛЬНО:
public WordProvider(String path)
```

- Этот метод не инициализирует провайдер, он его создает
```java
WordProvider provider = wordProviderInitialize("src/words.txt");

//ПРАВИЛЬНО:
WordProvider provider = createProviderInitialize("src/words.txt");
```

Вот метод, который инициализирует провайдер:
```java
WordProvider provider = new WordProvider("src/words.txt");
wordProviderInitialize(provider);
```

- Название "парсированный ввод" ни о чём не говорит.  
Задача этой переменной- хранить хранить полученную команду
```java
int parsedInput = Integer.parseInt(input);

//ПРАВИЛЬНО:
int command = Integer.parseInt(input);
```

- Запутал на ровном месте
```java
this.PATH = PATH;
Path path = Path.of(PATH);

//ПРАВИЛЬНО:
this.filename = filename;
Path path = Path.of(filename);
```

- Для определения состояния в названиях методов используются приставки is-, has-
```java
boolean letterUsed(Set<Character> letters, char letter)

//ПРАВИЛЬНО:
boolean isUsedLetter(Set<Character> letters, char letter)
```

- Одинаковые концепции называй одинаково
```java
void printMenu() {
  //печатает меню
}

void firstGreeting() {
  //печатает приветствие
}

//ПРАВИЛЬНО:
void printMenu() {
  //печатает меню
}

void printGreeting() {
  //печатает приветствие
}
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Объявляй переменные там, где они используются**

Минимизируй область видимости локальных переменных
```java
int parsedInput;
try {
  parsedInput = Integer.parseInt(input);
  //дальше parsedInput используется только внутри блока try
}

//ПРАВИЛЬНО:
try {
  int parsedInput = Integer.parseInt(input);
  //дальше parsedInput используется только внутри блока try
}
```
*Блох, "Java. Эффективное программирование", изд.3, гл.9.1*  

**3. Нарушение DRY**

Магические буквы, числа, слова. Вводи константы 
```java
System.out.println("1 - начать игру");
System.out.println("2 - выйти");

case 1:  //...
case 2:  //...

//ПРАВИЛЬНО:
private static final int START = 1;
private static final int QUIT = 2;
private static final 

System.out.println(START + " - начать игру");
System.out.println(QUIT + " - выйти");

case START:  //...
case QUIT:  //...
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru, "Замена магического числа символьной константой"*

**4. Exceptions**

- Текст в Exception всегда должен быть на английском языке.
```java
throw new IllegalStateException("Слова в файле отсутсвуют");  <-- Неправильно: сообщение в исключении не на английском языке
```
Исключение это не просто телеграмма, которая летит сквозь слои.

У exception особое назначение- если исключение вылетит и не будет перехвачено внутри программы, то аварийно прекратит выполнение программы.  
Тогда на экране будет распечатано сообщение эксепшена, и это сообщение должно быть понятно сисадмину в любой точке планеты.  
А значит, сообщение должно быть на английском.

Интерпретация исключения и перевод его на локальный язык должны происходить там, где это соответствует архитектуре программы.  
Или не происходить вовсе, если исключение не планируется перехватывать.

+ 👍 Респект за перехват проверяемого `IOException` и возврата вместо него непроверяемого `UncheckedIOException`
```java
catch (IOException e) {
  throw new UncheckedIOException("Не удалось загрузить слова: " + PATH, e);
}
```
Но текст в исключении всё равно должен быть на английском языке.

- Не смешивай бизнес логику и обработку ошибок.

Не используй исключения для организации ветвлений бизнес логики вместо `if-else if`.  
Обработку ошибок изолируй в отдельных методах
```java
String input = scanner.nextLine();
try {
  int parsedInput = Integer.parseInt(input);
  //бизнес логика
} catch (NumberFormatException e) {
  System.out.println("Нужно вводить цифры!");
}

//ПРАВИЛЬНО:
String input = scanner.nextLine();
if(isNumber(input)) {
   int command = Integer.parseInt(input);  

  //бизнес логика     
} else {
  //ввели не цифру  
}

private boolean isNumber(String s) {
  try() {
    Integer.parseInt(s);
    return true;
  } catch (NumberFormatException e) {
    return false; 
  }   
}
```
*"ЧК", гл.3, "Изолируйте блоки try/catch"*

**5. class WordProvider**

Словарь (провайдер слов).

- Всегда явно указывай уровень доступа.  

Потому что неясно- то ли ты забыл его указать(а однажды забудешь), то ли оно специально задумано как default
```java
public class WordProvider {
  List<String> words;
  //...
}

//ПРАВИЛЬНО:
public class WordProvider {
  private final List<String> words;
  //...
}
```

+ 👍 Словарь принимает в конструктор путь к файлу, а не хардкодит внутри себя, это хорошо.  
Но нейминг нужно поправить
```java
public class WordProvider {
  public WordProvider(String PATH) {...}
}

//ПРАВИЛЬНО:
public class WordProvider {
  public WordProvider(String filename) {...}
}
```

- Реакция на ошибки. 

Если по указанному адресу в не будет найден файл со словами, то программа аварийно вылетит через Exception и напечатает непонятные для юзера сообщения в консоль
```java
C:\Users\alex\.jdks\openjdk-26.0.1\bin\java.exe "-javaagent:C:\Prog\IntelliJ IDEA 2026.1\lib\idea_rt.jar=12676" -Dfile.encoding=UTF-8 -Dsun.stdout.encoding=UTF-8 -Dsun.stderr.encoding=UTF-8 -classpath C:\MyReviews\PROG\Hm-Иван-\Hangman_game-zhukovsd-Project1-master\out\production\Hangman_game-zhukovsd-Project1 Main
Exception in thread "main" java.io.UncheckedIOException: Не удалось загрузить слова: src/words.txt
	at WordProvider.<init>(WordProvider.java:25)
	at Main.wordProviderInitialize(Main.java:53)
```
Здесь должна быть другая реакция на отсутствие файла со словами.  
Нужно сказать юзеру, что файл со словами по указанному пути открыть не удалось и поэтому работа программы будет завершена. 

После этого нужно корректно, а не аварийно, завершить работу программы.

- Приоритет- читаемость кода, а не экономия строчек
```java
return words.get(random.nextInt(words.size()));

//ПРАВИЛЬНО:
int index = random.nextInt(words.size());
return words.get(index);
```

**6. enum HangmanState**

Хранилище картинок виселицы.

- Не переопределяй `toString()` у enum.

Хотя прямого запрета на переименование `toString()` у енамов я в литературе не встречал, но этот метод в классах принято использовать только для отладки.  
В этом енаме переопределенный `toString()` возвращает картинку, а значит предназначен не для отладки, а для использования в представлении.  
Я бы советовал у енамов не переопределять `toString()`, он в енамах и так по умолчанию хорош для задач отладки.

Если возникла потребность переопределить `toString()` у енама, значит ты делаешь что-то не так с этим классом.

Про особенности использования метода `toString()` почитай тут:  
*Блох, "Java. Эффективное программирование", изд.3, гл.3.3*.

- Неправильное использование енамов.

Енамы нужно использовать как "константы на стероидах", в них не должно быть сложной логики.  
[Документация](https://docs.oracle.com/javase/8/docs/technotes/guides/language/enums.html) на enum сообщает:
```java
Итак, когда следует использовать перечисления (enums)? Всякий раз, когда вам нужен фиксированный набор констант.
```

От этого енама нам нужен не фиксированный набор констант, а получение картинки по номеру ошибки
```java
public enum HangmanState {
    START(
            """
             -----
             |   |
             |
            _|_
            """
    ),
    HEAD(
            """
             -----
             |   |
             |   0
            _|_
            """
    ),
    BODY(...),
    LEFT_ARM(...),
    BOTH_ARMS(...),
    LEFT_LEG(...),
    DEAD(...);
    

    public static HangmanState fromTries(int tries) {  <-- Вот всё, что нам нужно от этого енама
        return switch (tries) {
            case 0 -> START;
            case 1 -> HEAD;
            //...
        };
    }
}
```

Поэтому нам нужен класс с развитой, хоть и простой, логикой, а не набор констант:
```java
public class PictureStorage {
  private static final String[] PICTURES = {
      """
    -----   
    |       
    |       
    |       
    ------- 
    """,
      """
     -----   
     |   |   
     |   O   
     |       
     ------- 
     """
      ,
      // more pics
  };
  
  public String get(int num) {
    return PICTURES[num];
  }
}
```

- Маскировка бага. 

Попытка распечатать картинку с некорректным номером является багом.  
Его нужно выявить и устранить, а не маскировать.  
Для этого лучший вариант здесь- сделать быстрое падение
```java
public static HangmanState fromTries(int tries) {
  return switch (tries) {
    case 0 -> START;
    case 1 -> HEAD;
    //...
    default -> DEAD; // 6 и больше
  };
}

//ПРАВИЛЬНО:
public static HangmanState fromTries(int tries) {
  return switch (tries) {
    case 0 -> START;
    case 1 -> HEAD;
    //...
    default -> //бросить исключение
  };
}
```

**7. class HangmanGame**

- Тотальное нарушение инкапсуляции.

Публичными должны быть только те методы, которые предназначены для использования клиентами.  
Методы и поля, предназначенные для использования потомками, должны быть `protected`.  
Остальные методы и поля в классах должны быть `private`.

Придерживайся правила минимального открытого интерфейса.  
*Вайсфельд "Объектно-ориентированный подход", гл.5, "Минимальный открытый интерфейс"*

- Нарушение правила одной операции.

Если в названии метода хочется написать "And", "Or", "If" и тому подобное, значит метод делает много разных операция

```java
char inputAndletterCheck(Scanner scanner, Set<Character> usedLetters) 
```

Метод нужно разделить на несколько, каждый из которых будет делать что-то одно.  

Например, из этого метода нужно выделить метод, который только получает русскую букву.  
ЛЮБУЮ русскую букву, а не только ту русскую букву, которую ещё не вводили:
```java
private char inputRusLetter() {
  while(true) {
    //получает от юзера русскую букву и возвращает её
  }
}
```
*Мартин, "Чистый код", гл.3, "Правило одной операции", "Один уровень абстракции"*

- Поля класса.

Определи те состояния, которые определяют сущность этого класса и сделай их полями
```java
public class HangmanGame {
  final static int ATTEMPTS = 6;

  void startGame(Scanner scanner, String word) {
    Set<Character> usedLetters = new HashSet<>();
    //...
  }

  char inputAndletterCheck(Scanner scanner, Set<Character> usedLetters) {...}
  //...
}

//ПРАВИЛЬНО:
public class HangmanGame {
  private final static int ATTEMPTS = 6;

  private final Scanner scanner = new Scanner(System.in);
  private final Set<Character> usedLetters  = new HashSet<>();

  private final String word;
  private final har[] mask;
  //...

  void startGame(String word) {
    this.word = word;
    this.mask = createMask();
    //...
  }

  char inputAndletterCheck() {...}
  //...
}
```

- Нарушение правила одной операции.

Первая операция: выяснить, была ли эта буква использована ранее.  
Вторая операция: напечатать сообщение об этом
Третья операция: добавить букву в список использованных
```java
boolean letterUsed(Set<Character> letters, char letter) {
  if (letters.contains(letter)) {
    System.out.println("Эта буква уже есть!");  <-- ВТОРАЯ ОПЕРАЦИЯ
    return false;  <-- ПЕРВАЯ(ОСНОВНАЯ) ОПЕРАЦИЯ
  } else {
    letters.add(letter);  <-- ТРЕТЬЯ ОПЕРАЦИЯ
    return true;  <-- ПЕРВАЯ(ОСНОВНАЯ) ОПЕРАЦИЯ
  }
}
```

- Побочные эффекты.

Название метода (его контракт) обещает только проверить букву, что бы это ни значило.  
Но кроме проверки буквы, метод делает много разного, в том числе изменяет состояние пришедших в него объектов
```java
boolean checkLetter(char letter, String word, char[] hiddenLetters) 
```
Эти неявные изменения являются побочным эффектом.

*Мартин "Чистый код", гл.3, "Избавьтесь от побочных эффектов"*  
*Фаулер "Рефакторинг", гл.6, "Извлечение метода"* 

- Максимально непонятный нейминг
```java
char[] initialize(int length) {

  char[] temp = new char[length];

  for (int i = 0; i < temp.length; i++) {
    temp[i] = '_';
  }

  return temp;
}

//ПРАВИЛЬНО:
char[] createMask(int length) {
  char[] mask = new char[length];

  for (int i = 0; i < mask.length; i++) {
    mask[i] = MASK_SYMBOL;
  }

  return mask;
}
```

+ 👍 Хороший метод
```java
boolean isWin(char[] hiddenLetters) {
  //определяет состояние выигрыша
}
```

Но нужно сделать и остальные методы из той же концепции:
```java
boolean isGameOver(...) {
  return isLose(...) || isWin(...);    
}

boolean isLose(...) {...}
```

**8. class Main**

- Бесполезный метод-посредник
```java
WordProvider provider = wordProviderInitialize("src/words.txt");

WordProvider wordProviderInitialize(String path) {
  return new WordProvider(path);
}

//ПРАВИЛЬНО:
WordProvider provider = new WordProvider("src/words.txt");
```

## АРХИТЕКТУРА

Эта реализация имеет признаки и процедурного и объектно-ориентированного стилей.

Признаки ООП:  
🔹 Программа разделена на классы, каждый из которых описывает отдельную сущность: общая игровая логика, словарь и т.д.  
🔹 Отдельный класс с точкой входа main, который тут используется правильно- ведет диалог "играть или выйти" и запускает игру.

Признаки процедурного стиля:  
🔸 Не выявлены и не сделаны в виде классов менее очевидные, чем "Словарь", сущности. Например, "Секретное слово". 
Значит нет системного понимания объектно-ориентированной декомпозиции.

🔸 Те классы, на которые сейчас поделена программа, могут быть и в процедурной программе- процедурный стиль сам по себе не отрицает возможность создания нескольких классов.  
🔸 Тотальное игнорирование инкапсуляции.  
🔸 Процедурные приёмы: перекидывание данных между методами вместо выделение их в поля класса - `class HangmanGame`.  

## ВЫВОД

Для всех стилей программирования на уровне первого проекта нужно научиться делать методы, которые будут соответствовать таким требованиям:  
🔹 Маленький размер  
🔹 Выполняют одну операцию на одном уровне абстракции  
🔹 Не совмещают команду и запрос  
🔹 Не содержат больше трех уровней вложенности  
🔹 Не имеют побочных эффектов

Сейчас не все методы в программе соответствуют этим требованиям.

Посмотри на ютубе ролики Немчинского для новичков:
```
"Правильные методы по Clean Code"
"Как называть переменные, методы и классы? Чистый код (Clean Code)"
"Принцип хорошего кода KISS"
```

Сравнить различия между ООП и процедурным стилями:  
Стрим Сергея [Крестики-нолики в процедурном стиле](https://www.youtube.com/watch?v=PPikj1qHxrA)  
Мой стрим [Крестики-нолики в ООП стиле](https://t.me/zhukovsd_it_chat/53243/187097)

Подробное объяснение, как делать эту программу в процедурном и ООП стилях, есть у Сергея в расширенных материалах.

n.195(373)  
#ревью #виселица 