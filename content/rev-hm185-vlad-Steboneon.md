https://github.com/Steboneon/HangmanConsole  
[Влад]

Игра в процедурном стиле, состоит из большого количества классов.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Повторный ввод одной и той же неправильной буквы считается новой ошибкой и увеличивает счетчик ошибок

2. Нет списка введённых букв

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву определенного алфавита
+ 👍 2 словаря: английский и русский

## ЗАМЕЧАНИЯ

**0. Использование MVC в проекте**

Эта программа разбита на несколько пакетов, среди которых есть "model", "view" и "controller".  
Отсюда можно сделать вывод, что программа писалась по архитектуре MVC.

Но при ближайшем рассмотрении оказывается, что классы в этих пакетах-слоях в основном не соответствуют своим слоям.

Например, в пакете controller нет ни одного контроллера
```java
class InputControler  - модель
class LetterInput - вью
class LetterValidator - модель + вью
```

В целом программа не соответствует архитектуре MVC.  
И если где-то в слое вью действительно лежит класс-вью, то это на фоне остального выглядит не более чем совпадением, а не результатом системного понимания идей этой архитектуры.

Поэтому критиковать программу я буду не с точки зрения MVC, а с точки зрения базового ООП.

**1. Нейминг**

- "Controller" пишется с двумя "l":
```java
package controler;

//ПРАВИЛЬНО:
package controller;
```

- Название не соответствует тому, что делает класс.

Это не контроллер ввода- он ничего не вводит.  
Класс переводит строку в число и проверяет, находится ли это число в заданном диапазоне
```java
public class InputControler {
  public static boolean isNumberInRange(String input, int min, int max) {...}
}
```

- Избыточно
```java
public class LetterInput {
  public char readLetter() {...}
}

//ПРАВИЛЬНО:
public class LetterInput {
  public char read() {...}
}
```

- Название класса должно быть существительным
```java
class ReadWord

//ПРАВИЛЬНО:
class WordReader
```

- Название методов в классе, кроме конструктора, не должно быть аналогичным названию своего класса.  
Хотя бы потому, что название классов должно быть существительным, а методов- глаголом в повелительном наклонении
```java
public class ReadWord {
  //...
  public void readWord(String fileName) {...}
}

//ПРАВИЛЬНО:
public class WordReader {
  //...
  public void read(String fileName) {...}
}
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Форматирование**

- Форматирование строк.

Если нужно печатать или создавать строку с более чем одним подстановочным значением или значение вставляется внутрь сообщения, 
используй форматирование- тогда сразу будет виден весь шаблон
```java
System.out.println("В файле нет слов длиной " + wordLength + " букв!");

//ПРАВИЛЬНО:
System.out.printf("В файле нет слов длиной %d букв!  \n", wordLength);
```

- Не сворачивай 2D в 1D
```java
System.out.print("\nВведите букву: ");

//ПРАВИЛЬНО:
System.out.println();
System.out.print("Введите букву: ");
```

**3. Нарушение DRY**

- Магические буквы, числа, слова. Вводи константы 
```java
System.out.println("1. Русский");
System.out.println("2. Английский");
System.out.println("0. Назад");

if (InputControler.isNumberInRange(input, 0, 2)) {...}
if (language == 0) {...}
System.out.println("Ошибка! Введите 0, 1 или 2.");

//ПРАВИЛЬНО:
private static final int QUIT = 0;
private static final int RU_LANGUAGE = 1;
private static final int EN_LANGUAGE = 2;

System.out.println(RU_LANGUAGE + ". Русский");
System.out.println(EN_LANGUAGE + ". Английский");
System.out.println(QUIT + ". Назад");

if (InputControler.isNumberInRange(input, QUIT, EN_LANGUAGE)) {...}
if (language == 0) {...}
System.out.printf("Ошибка! Введите %d, %d или %d. \n", QUIT, RU_LANGUAGE, EN_LANGUAGE);
```

- Совместная магия.

Если к одной и той же магической штуке обращаются два разных класса, то делай ее константой в одном из этих двух классов.  
Либо перенеси ее в третий класс и из первых двух классов обращайся к этим константам- эти данные должны быть синхронизированы между собой.

Сейчас совместная магия есть в классах `StartMenu` и `SettingsMenu`: 1, 2.

*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*

**4. class InputControler**

- Нарушение SRP.

Сначала переводит строку в число, а потом проверяет это число на вхождение в заданный диапазон
```java
public class InputControler {
  public static boolean isNumberInRange(String input, int min, int max) {...}
}

//ПРАВИЛЬНО:
public class MinMaxValidator {
  public static boolean isValid(int value, int min, int max) {...}
}
```

**5. class LetterInput**

+ 👍Как инпутер буквы, вполне норм.

**6. class LetterValidator**

- Нарушение SRP.

Валидатор должен только валидировать- сказать, соответствует ли значение критериям валидации или нет.  
Валидатор- это модель, он не должен ничего печатать юзеру. Печать сообщений юзеру это ответственность слоя `view`
```java
public class LetterValidator {

  public boolean isLetterValidForLanguage(char letter, int languageChoice) {
    //...
    System.out.println("Ошибка: вы выбрали английский словарь! Пожалуйста, переключите раскладку на английский.");
    //...
    System.out.println("Ошибка: вы выбрали русский словарь! Пожалуйста, переключите раскладку на русский.");
    //...
    System.out.println("Пожалуйста, введите корректную букву алфавита.");
    //...
  }
}
```

Если вместо ответа "правильно" или "неправильно" нужно вернуть какой-то определенный результат, то можно сделать примерно так
```java
public class LetterValidator {

  public Result validate(char letter, int languageChoice) {...}

  public enum Result {
    OK, WRONG_EN_DICT, WRONG_RU_DICT, INCORRECT_LETTER;
  }
}
```

- Нарушение SRP, OCP.

Из-за аргумента-флага `languageChoice`, алгоритм в методе валидации разделяется на две операции- русская и английская валидация.  
При добавлении нового языка валидации, нужно будет изменить код в классе. А значит, класс открыт для изменений.  
Такая валидация это приём процедурного программирования 
```java
public class LetterValidator {

  public boolean isLetterValidForLanguage(char letter, int languageChoice) {
    char lower = Character.toLowerCase(letter);

    boolean isEnglish = (lower >= 'a' && lower <= 'z');
    boolean isRussian = (lower >= 'а' && lower <= 'я') || lower == 'ё';

    if (languageChoice == 2 && isRussian) {
      System.out.println("Ошибка: вы выбрали английский словарь! Пожалуйста, переключите раскладку на английский.");
      return false;
    }
    //...
  }
}
```
*Мартин "ЧК", гл.3, "Аргументы-флаги"*
```
"Аргументы-флаги уродливы... функция выполняет более одной операции" - Мартин.
```

ООП подход: создать семейство валидаторов с общим предком. 

Рассмотрим на примере простой валидации, которая возвращает бинарный ответ 
```java
public interface LetterValidator {
  boolean isValid(char letter);
}

public class RuLetterValidator implements LetterValidator{

  @Override
  public boolean isValid(char letter) {
    //если letter русская буква- вернуть true
    //иначе вернуть false
  }
}

public class EnLetterValidator implements LetterValidator{

  @Override
  public boolean isValid(char letter) {
    //если letter АНГЛИЙСКАЯ буква- вернуть true
    //иначе вернуть false
  }
}
```
Суть: при добавлении нового языка в проект, нужно просто добавить новый валидатор, а не изменять код в существующем.

**7. class ReadWord**

С точки зрения ООП, этот класс не является Объектом, потому что не представляет из себя понятной сущности с четко определенными границами.  
Это контейнер с функциями в стиле процедурного программирования, он делает разное:
```java
public class ReadWord {
  private String newWord;
  private int wordLength;

  public List<Integer> findValidIndices(String fileName) {...}

  public String fetchWordAtLine(String fileName, int targetLine) {...}

  public void readWord(String fileName)  {...}

  public String getWord() {...}

  public int getWordLength() {...}
}
```

Нужно определить все ответственности, которые сейчас содержит этот класс и разнести их по разным классам в соответствии с принципом единой ответственности (SRP).

Например, если класс называется "Чтение(читатель) слов", то его ответственность должна состоять в том, чтобы прочитать слова из файла и больше ничего:
```java
public class WordFileReader {
  public List<String> read(String filepath) {...}
}
```

Чтение слов из файла и выдачу случайного слова мы можем совместить в одном классе, например `Dictionary`.  
Тогда его единой ответственностью можно будет считать хранение и выдачу слов, а файловое чтение это для него будет просто способом загрузки слов в себя:
```java
public class Dictionary {
  private final List<String> words;

  public Dictionary(String filepath) {
    //загружает из файла filepath слова в words
  }
   

  public String getRandomWord() {
    //возвращает случайное слово из words
  }
}
```

**8. class GameInitializer**

Тоже что-то непонятное процедурное.  
Мне даже трудно сформулировать, что конкретно делает этот класс.

Суть в том, что у тебя в программе есть несколько языков. И от выбора языка зависит много разного.  
Ок, создавай эти зависимости через фабрики. Например:
```java
public enum Language {
  EN, RU;
}

public class DictionaryFactory {

  private static final EN_FILE_PATH = ....
  //...

  public static Dictionary get(Language language) {
    switch(language) {
      case EN -> new Dictionary(EN_FILE_PATH);
      case RU -> new Dictionary(RU_FILE_PATH);
    }
  }    
}
```

И тогда все эти зависимости получай на уровне `Main` и внедряй дальше.  
Например в простейшем случае без диалога "Играть или выйти" это будет примерно так:
```java
public class Main {

  public static void main(String[] args) {

    Language language = inputLanguage();

    Dictionary dictionary = DictionaryFactory.get(language);
    LetterValidator letterValidator = LetterValidator.get(language);

    String word = dictionary.getRandomWord();

    Game game = new Game(word, letterValidator);
    game.start();
  }

  private static Language Language() {
    //получает от юзера язык 
  }
}
```

**9. Остальные классы в пакете game**

Разбирать каждый из этих классов не вижу смысла- они все одинаково ужасны.  
Ни один из них не представляет из себя никакой внятной объектно-ориентированной сущности и нелегко понять причину их существования.

**10. class HangmanAscii**

+ 👍Тут всё ок.

**11. class SettingsMenu**

- Никогда не возвращай null
```java
public GameSettings showSettingsMenu() {
  //...
  if (language == -1) {
    return null;
  }
}
Возврат null повышает риск возникновения NullPointerException в программе.  
*Мартин, "Чистый код", гл.7.7-8*  
*Ютуб, Немчинский "Почему нельзя возвращать NULL?"*  
```

- Меню в процедурном стиле.

Если бы это меню было простым и состояло из нескольких пунктов, то такое процедурное меню можно было бы использовать и в ООП программе.  
Здесь это меню сложное, состоит из нескольких, поэтому для программы, претендующей на ООП, такая лютая процедурщина выглядит плохо.  

Про меню в ООП стиле я писал [ТУТ](https://t.me/zhukovsd_it_chat/53243/114908).

**12. class Main**

Содержит точку входа `main`.

+ 👍 Только создает и запускает игру, это хорошо.  
*Мартин, "ЧК", гл.11, "Отделение конструирования системы от ее использования"*

- Не используется `Dependency injection`.

Грамотное использование DI делает ООП программу более гибкой и позволяет создавать разные игровые конфигурации, не меняя код в остальных классах.
Здесь вообще не используется DI:
```java
public class Main {

  public static void main(String[] args) {
    StartMenu startMenu = new StartMenu();
    startMenu.startApp();
  }
}
```

Использование DI позволяет делать разные игровые конфигурации, просто создавая новые Main-файлы.  
Например:

🔹 Простая игра с английскими словами
```java
public class FirstMain {

  public static void main(String[] args) {
    Dictionary dictionary = new Dictionary("en_words.txt");
    String word = dictionary.getRandomWord();

    Game game = new Game(word);
    game.start();
  }
}
```

🔹Игра с русскими словами и диалогом "Играть или выйти"
```java
public class SecondMain {

  private  final static String QUIT = "0"; 
  private  final static String START = "1"; 

  public static void main(String[] args) {
    Dictionary dictionary = new Dictionary("ru_words.txt");
    
    while(true) {
       String command = inputCommand();

       if(command.equals(QUIT)) {
         return;
       } else if(command.equals(START)) {
        String word = dictionary.getRandomWord();
        Game game = new Game(word);
        game.start();
       } else {
        //ввели неизвестную команду
       }
    }   
  }

  private static String inputCommand() {
    //Диалог с юзером: Играть или выйти?
    //возвращает команду от юзера
  }
}
```

## АРХИТЕКТУРА

Несмотря на то, что в проекте несколько классов, он написан в процедурном стиле.  
Программа в ООП стиле должна быть декомпозирована по правилам ООП и использовать ООП-подход для реализации типичных задач.  
Здесь не видно ООП декомпозиции.

Фактически, проект написан в процедурном стиле- классы здесь в большинстве своём являются просто контейнерами функций.

Тем более, здесь нет архитектуры MVC.

В MVC программе все классы, кроме Main-классов, должны принадлежать к одному из трёх слоёв: `model`, `view` или `controller`.  
Если используется MVC с анемичной моделью, то четыре слоя: `model`, `view`, `controller`, `service`.  
Но анемичная модель- не твой случай.

Поэтому ты должен, *по крайней мере в этой реализации*, перенести классы из других пакетов в эти три базовых слоя MVC.

Main это особый класс, он находится вне слоев MVC, поэтому должен находиться отдельно от них- либо просто в корне проекта, либо в своем отдельном пакете. 

Пример простой MVC-декомпозиции для Виселицы:

### МОДЕЛИ (MODEL)

В архитектуре MVC модели должны хранить данные и алгоритмы бизнес-логики, которые не взаимодействуют с представлением. 

Здесь это может быть класс `Dictionary`- он просто хранит слова, его работа не зависит от представления.  
Он будет одинаково работать и в консольной программа, и в программе в Windows UI
```java
public class Dictionary {
  private final List<String> words;

  public Dictionary(String filepath) {
    //загружает из файла filepath слова в words
  }
   

  public String getRandomWord() {
    //возвращает случайное слово из words
  }
}
```

### ПРЕДСТАВЛЕНИЕ (VIEW)

В архитектуре MVC вью должны распечатывать данные, в том числе модели.  
Также вью должны принимать ввод от юзера.  
Вью не должны ничего знать про контроллеры.

Здесь вью должны печатать текстовые сообщения, печатать картинки, принимать ввод от юзера.  
В идеале вью должны быть универсальными и не зависеть от конкретной визуальной среды, поэтому должны делаться через интерфейсы.  

Например:

+ Ввод от юзера
```java
public interface UserReader {
  String read();    
}

public class ConsoleUserReader implements UserReader() {
  @Override 
  public String read() {
    //получение данных от юзера через консольный класс Scanner
  }    
}
```

+ Печать текста и картинок юзеру
```java
public interface Printer {
  void printHangman(int num);    
  void printHello();    
  void printBye();
  //...    
}

public class ConsolePrinter implements Printer() {
  @Override 
  public void printHangman(int num) {
    //печатает в консоль картинку по номеру
  } 

  @Override 
  public void printHello() {
    System.out.println("ПРИВЕТ!")
  }   

  @Override 
  public void printBye() {
    System.out.println("ПОКА!")
  }   
  //...  
}
```

### КОНТРОЛЛЕРЫ (CONTROLLER)

Контроллеры должны знать всё про слои view и model.  
Задача контроллеров состоит в том, чтобы связывать модели и вью в единую программу.  
Контроллер должен быть тонким.

Здесь контроллером может быть, например, класс Игра.  
Он будет реализовывать основную игровую логику и для этого использовать модели и представления
```java
public class Game {
  private final String word;
  private final UserReader reader;
  private final Printer printer;
  //...

  public Game(String word, UserReader reader, Printer printer) {...}

  public void start() {
    //игровая логика: принимает данные от юзера, печатает данные для юзера
  }
  
  //oths methods
}
```

И тогда в Main-классе такая MVC-сборка будет выглядеть так:
```java
public class MvcMain {

  public static void main(String[] args) {
    Dictionary dictionary = new Dictionary("ru_words.txt");
    Printer printer = new ConsolePrinter();
    UserReader reader = new ConsoleUserReader();

    String word = dictionary.getRandomWord();

    Game game = new Game(word, printer, reader);
    game.start();
  }
}
```

Но здесь контроллер `Game` будет толстым, потому что будет содержать всю игровую логику.  
И ту, которая зависит от представления, и ту, которая не зависит от представления.  

Толстый контроллер это плохо, он должен быть тонким.  
Поэтому в контроллере нужно оставить только ту игровую логику, которая зависит от представления.  
А ту, которая не зависит(увеличение счетчика ошибок, определение проигрыша/выигрыша etc), нужно вынести в модель-движок игры
```java
public class MvcMain {

  public static void main(String[] args) {
    Dictionary dictionary = new Dictionary("ru_words.txt");
    Printer printer = new ConsolePrinter();
    UserReader reader = new ConsoleUserReader();

    String word = dictionary.getRandomWord();
     
    GameEngine engine = new GameEngine(word);
    GameController controller = new GameController(engine, printer, reader);
    controller.start();
  }
}
```

## ВЫВОД

Не освоив ООП на базовом уровне, нельзя переходить к архитектуре MVC.  
Тебе нужно ещё как следует поработать с ООП на базовом уровне. 

Посмотреть ролики Немчинского про SOLID- по одному ролику на каждый принцип.

Сравнить различия между объектно-ориентированным и процедурным стилями:  
Стрим Сергея [Крестики-нолики в процедурном стиле](https://www.youtube.com/watch?v=PPikj1qHxrA)  
Мой стрим [Крестики-нолики в ООП стиле](https://t.me/zhukovsd_it_chat/53243/187097)

Подробное объяснение, как делать эту программу в процедурном и ООП стилях, есть у Сергея в расширенных материалах.

n.185(356)  
#ревью #виселица #mvc 