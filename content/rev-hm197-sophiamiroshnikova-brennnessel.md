https://github.com/brennnessell/hangman  
[Sophia Miroshnikova]

Игра в процедурном стиле. Состоит из нескольких классов.

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву русского алфавита
+ 👍 Есть список (не) введенных букв

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Если в проекте есть класс `BankOfWords`, то все переменные с именем, включающим это название, должны быть экземплярами этого класса.  

Когда разные концепции называются одним и тем же именем, это приводит к путанице
```java
public class BankOfWords {
  private static final String WORDS_FILE_PATH = "bankOfWords.txt";

  public static String getRandomWord() {
    List<String> bankOfWords;
    //...
  }
}

//ПРАВИЛЬНО:
public class BankOfWords {
  private static final String WORDS_FILE_PATH = "bankOfWords.txt";

  public static String getRandomWord() {
    List<String> words;
    //...
  }
}
```

- В названия не нужно вставлять частицы "Of", "The", "Are" и т.д.

Это только делает названия более громоздкими.  
"Of" в названиях методов можно использовать только в контексте "valueOf()"
```java
class BankOfWords

//ПРАВИЛЬНО:
class WordBank
```

- Не называй классы интерфейсами. 

Визуальный интерфейс называй "View" или "UI".  
Хотя "UI" тоже означает "User Interface", но эта аббревиатура уже сама стала именем собственным
```java
class UserInterface

//ПРАВИЛЬНО:
class Ui
```

- Буквы это не клавиатура
```java
String keyboard = "АБВГДЕЁЖЗИЙКЛМНОПРСТУФХЦЧШЩЪЫЬЭЮЯ";

//ПРАВИЛЬНО:
String availableLetters = "АБВГДЕЁЖЗИЙКЛМНОПРСТУФХЦЧШЩЪЫЬЭЮЯ";
```

- Частицы "is" и "has" должны стоять в начале названия булевых переменных
```java
boolean letterIsGot = false;

//ПРАВИЛЬНО:
boolean isGotLetter = false;
```

- Буква это не строка. 

Потому что буква всегда состоит из одного символа, а строка может состоять из ноля символов или любого другого их количества
```java
String letter

//ПРАВИЛЬНО:
char letter
```

- В таких случаях принято использовать `this`
```java
private Scanner scanner;

UserInterface(Scanner newScanner) {
  scanner = newScanner;
}

//ПРАВИЛЬНО:
private Scanner scanner;

UserInterface(Scanner scanner) {
  this.scanner = scanner;
}
```

- Не дублируй имя класса в названии метода класса
```java
public class Game {
  //...
  public void playGame(...) {...}
}

//ПРАВИЛЬНО:
public class Game {
  //...
  public void play(...) {...}
}
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Exceptions**

- Текст в Exception всегда должен быть на английском языке.
```java
//Неправильно: сообщение в исключении не на английском языке
throw new IllegalStateException("Во время открытия файла с банком слов произошла ошибка: %s".formatted(e.toString()));
```
Исключение- это не просто телеграмма, которая летит сквозь слои.

У exception особое назначение- если исключение вылетит и не будет перехвачено внутри программы, то аварийно прекратит выполнение программы.  
Тогда на экране будет распечатано сообщение эксепшена, и это сообщение должно быть понятно сисадмину в любой точке планеты.  
А значит, сообщение должно быть на английском.

Интерпретация исключения и перевод его на локальный язык должны происходить там, где это соответствует архитектуре программы.  
Или не происходить вовсе, если исключение не планируется перехватывать.

**3. class BankOfWords**

Словарь слов.

- Соблюдай требования утилитных классов. 

Это утилитный класс, он содержит только статические методы.  
Утилитные классы, как и классы констант, должны быть `final` и иметь приватный конструктор.  
Не должно быть возможности унаследоваться от утилиты или сделать ее экземпляр.

*Блох, "Java. Эффективное программирование", изд.3, гл.2.4*

- Путь к файлу класс должен получать в конструктор или в метод чтения слов.  
Так как этот класс- утилита, то путь нужно получать в метод
```java
public class BankOfWords {
  private static final String WORDS_FILE_PATH = "bankOfWords.txt";

  public static String getRandomWord() {
    Path bankOfWordsFilePath = Path.of(WORDS_FILE_PATH);
    //...
  }
}

//ПРАВИЛЬНО:
public class BankOfWords {

  public static String getRandomWord(String filepath) {
    Path bankOfWordsFilePath = Path.of(filepath);
    //...
  }
}
```

Сейчас путь к файлу захардкоден в коде класса.  
Это делает класс не универсальным: его нельзя переиспользовать для чтения других файлов и для использования в других проектах в аналогичных задачах.

- Нарушение SRP, чужая ответственность, зависимость модели от представления.

Модель(а это модель) не должна ничего печатать в консоль
```java
public static String getRandomWord() {
  //...
  System.out.println("Файл %s не содержит слов!".formatted(WORDS_FILE_PATH));  <-- МОДЕЛЬ ПЕЧАТАЕТ В КОНСОЛЬ
  //...
}
```
Класс-модель должен работать в любой визуальной среде (консоль, Swing, Android, аркадный автомат на лампочках).  
Поэтому модели не должны сами печатать информацию непосредственно на экран.  
Иначе такая модель будет работать только в одной конкретной визуальной среде, а не в любой из них.

Гугли: зависимость модели от представления.

- Тип возвращаемого исключения выбран неправильно для этой ситуации
```java
catch (IOException e) {
  throw new IllegalStateException(...);
}
```

**4. class UserInterface**

+ 👍 Отдельный класс, где находится пользовательский ввод\вывод, это хорошо.

- Нарушение конвенции кода. Константы должны стоять выше остальных полей.

- Проверка соответствия буквы алфавиту сейчас- индусский код.

Сейчас соответствие буквы русскому алфавиту происходит путем поиска буквы в строке. Это очень избыточный алгоритм
```java
keyboard = "АБВГДЕЁЖЗИЙКЛМНОПРСТУФХЦЧШЩЪЫЬЭЮЯ";

//ПРАВИЛЬНО:
private boolean isRusLetter(char symbol) {
  symbol = Character.toLowerCase(symbol);
  return (symbol >= 'а' && symbol <= 'я') || symbol == 'ё';
}
```
Почитай про то, как символы хранятся в таблице ASCII/юникод.

Не нужно сначала формировать пул всех доступных букв, а потом убирать из него использованные буквы.  
Нужно создать пустой пул(список, коллекцию) использованных букв и добавлять в него использованные буквы.

Как из двух этих вариантов юзеру проще понять какие были введены и их не нужно пробовать повторно?
```java
//ПОКАЗЫВАЕМ ДОСТУПНЫЕ БУКВЫ:
Доступные буквы: БВГДЕЁЖЗИЙКЛМНОПРСУФХЦЧШЩЪЫЬЭЮЯ

//ПОКАЗЫВАЕМ ВВЕДЕННЫЕ БУКВЫ:
Введенные буквы: А, Т
```

- В этом классе должна быть только простая логика ввода/вывода.

Этот метод забирает на себя часть общеигровой логики:
```java
public String getUserLetter() {
    while(true) {
     //Получает от юзера букву,
     //возвращает клиенту букву, если она соответствует сложным правилам игровой логики 
    }
}
```

В классе с юайем может быть только более простой метод ввода буквы, который не забирает на себя часть одеяла логики более высокого порядка.    
Например метод, который принимает ЛЮБУЮ русскую букву:
```java
public getRusLetter(char symbol) {
  while(true) {
    //принимает от юзера и возвращает клиенту ЛЮБУЮ русскую букву
  }
}
```

Дальнейшая проверка полученной от юзера русской буквы на корректность хода должна проходить в классе `Game`. 

- Используй многострочные строки
```java
draw = "┌───────────────────────┐\n" +
       "│ Ошибки: 0 / 7         │\n" +
       "├───────────────────────┤\n" +
       "│                       │\n" +
       "│ =======               │\n" +
       "└───────────────────────┘";

//ПРАВИЛЬНО:
draw = """
  ┌───────────────────────┐
  │ Ошибки: 0 / 7         │
  ├───────────────────────┤
  │                       │
  │ =======               │
  └───────────────────────┘
  """;
```
Многострочные строки(текстовые блоки) имеются в Java начиная с 13 версии.

- Печать картинки виселицы через switch-case или if-elseif - индусский код.  

Картинки нужно хранить в статическом массиве и печатать по номеру.  
Например, так
```java
  private static final String[] PICTURES = {
      """
    +----   
    |       
    |       
    |       
    |       
    ----- 
    """,
      """
     +---+   
     |   |   
     |   O   
     |       
     |       
     ----- 
     """
   ,
    // more pics
  };

  public void drawHangman(int errorCount) {
    String picture = PICTURES[errorCount];  
    System.out.println(picture);
  }
}
```

**5. class Game**

- Нарушение инкапсуляции. Всегда явно указывай модификатор доступа
```java
public class Game {

  Game() {...}
  //...
}

//ПРАВИЛЬНО:
public class Game {

  public Game() {...}
  //...
}
```

- Главные публичные методы должны стоять выше вспомогательных им приватных методов.

- Если бы мы этот класс рассматривали с точки зрения ООП, то он должен был бы принимать слово в конструктор:
```java
public class Game {
  private String secretWord;
  private String gameState;
 //...

  Game() {
    gameState = GAME_IN_PROCESS;
    secretWord = BankOfWords.getRandomWord();
  }
  //...
}

//ПРАВИЛЬНО:
public class Game {
  private String secretWord;
  private String gameState;
 //...

  public Game(String secretWord) {
    this.secretWord = secretWord;
    this.gameState = GAME_IN_PROCESS;
  }
  //...
}
```
Здесь текст слова- эта та зависимость, которую класс должен получать извне согласно `DI`.  
Потому что согласно `SRP` класс `Game` должен работать со словом. А получать это слово из конкретного источника- это чужая ответственность.

Но если рассматривать проект с точки зрения процедурного стиля, в котором он фактически написан, то и так норм.

- Опять же, если бы мы этот класс считали объектно-ориентированным, то UI он должен был бы принимать в конструктор.

Тогда UI нужно было бы принимать в конструктор, а не в метод старта игры
```java
public class Game {
  //...

  Game() {...}

  public void playGame(UserInterface userInterface) {...}
}

//ПРАВИЛЬНО:
public class Game {
  //...

  public Game(Ui ui, ...) {...}

  public void play() {...}
}
```

*Макконнелл, "Совершенный код", гл.7.5*
```
"Передавайте в метод те переменные или объекты, которые нужны ему для поддержания абстракции интерфейса" - Макконнелл
```

- Создавай вспомогательные методы, делай программу более простой и понятной.

Не нужно кешировать состояние игры в промежуточных енамах, используй вспомогательные методы.

Сейчас так:
```java
public void playGame(...) {
  while (gameState.equals(GAME_IN_PROCESS)) {

    if (secretWord.contains(currentLetter)) {
      //...
      if (convertMaskedWordToStr(maskedWord).equals(secretWord)) {
        gameState = GAME_IS_WON;
      }
    } else {
      //...
      if (errorsCount == ERRORS_LIMIT) {
          gameState = GAME_IS_LOST;
          //...
      }
    }
  }
  
  if (gameState.equals(GAME_IS_WON)) {
    //сообщение при выигрыше
  } else {
    //сообщение при проигрыше
  }
}
```

Правильно так:
```java
public void playGame(...) {
  while (!isGameOver()) {

    if (isWordLetter(currentLetter)) {...}
    //...
  }
  
  if (isWin()) {
    //сообщение при выигрыше
  } else {
    //сообщение при проигрыше
  }
}

private boolean isGameOver() {
  return isWin() || isLose();  
}

private boolean isWin() {...}

private boolean isLose() {
  return errorsCount >= ERRORS_LIMIT;    
}
```

- Переменные, которые характеризуют сущность класса, делай полями класса
```java
public class Game {
  //...
  private String secretWord;

  Game() {
    gameState = GAME_IN_PROCESS;
    secretWord = BankOfWords.getRandomWord();
  }

  private static void openGuessedLetters(String originWord, String letter, String[] demaskingWord) {
    for (int i = 0; i < originWord.length(); i++) {...}
  }
}

//ПРАВИЛЬНО:
public class Game {
  //...
  private final String word;
  private final char[] mask;
  private int errorCount;

  public Game(String word) {
    this.word = word;
    this.mask = createMask(word);
  }

  private void openLetter(char letter) {
    for (int i = 0; i < word.length(); i++) {...}
  }
}
```

- Не делай ацких условий в if'ах. 

Сложные условия выноси во вспомогательные методы или поясняющие переменные.  
Но конкретно в этом случае проблема состоит в неправильно выбранном типе входящего аргумента
```java
private static void openGuessedLetters(String letter, ...) {
  for (int i = 0; i < originWord.length(); i++) {
    if (String.valueOf(originWord.charAt(i)).equals(letter)) {  <-- Условие - ацкий ад
      demaskingWord[i] = letter;
    }
  }
}

//ПРАВИЛЬНО:
private static void openLetter(char letter, ...) {
  letter = Character.toLowerCase(letter);

  for (int i = 0; i < word.length(); i++) {
    if (letter = word.charAt(i)) {
      mask[i] = letter;
    }
  }
}
```

- Избыточно
```java
errorsCount += 1;

//ПРАВИЛЬНО:
errorsCount++;
```

**6. class Main**

Содержит точку входа main.

+ 👍 Только создает и запускает Игру, это хорошо.  

+ 👍 Диалог "Играть или выйти" находится здесь, а не в `Game`, это хорошо. 

*Мартин, "ЧК", гл.11, "Отделение конструирования системы от ее использования"*

- Нарушение DRY.

Магические буквы, числа, слова. Вводи константы 
```java
public static void main(String args[]) {
  int workMode = 1;

  while (workMode != 0) {
    System.out.println("Выберите режим работы: [1] - Игра, [0] - Выход");
    workMode = userInterface.getWorkMode();

    if (workMode == 1) {
      // играть
    } else if (workMode != 0 && workMode != 1) {...}
  }
}

//ПРАВИЛЬНО:
private static final int START = 1;
private static final int QUIT = 1;

public static void main(String args[]) {

  while (true) {
    System.out.printf("Выберите режим работы: [%d] - Игра, [%d] - Выход  \n", START, QUIT);
    int workMode = userInterface.getWorkMode();

    if (workMode == START) {
      // играть
    } else if (workMode = QUIT) {
      return; //выход   
    } else {
      // неправильный workMode    
    }
  }
}
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru, "Замена магического числа символьной константой"*

## ВЫВОД

Несмотря на то, что в проекте несколько классов, он написан в процедурном стиле.  
Программа в ООП стиле должна быть декомпозирована по правилам ООП и использовать ООП-подход для реализации типичных задач.  
Здесь не видно ООП декомпозиции.

Если планировался процедурный стиль, то получилось более-менее норм.  

Если планировался ООП стиль, то разобраться в различиях:  
Стрим Сергея [Крестики-нолики в процедурном стиле](https://www.youtube.com/watch?v=PPikj1qHxrA)  
Мой стрим [Крестики-нолики в ООП стиле](https://t.me/zhukovsd_it_chat/53243/187097)

Подробное объяснение, как делать эту программу в процедурном и ООП стилях, есть у Сергея в расширенных материалах.

n.197(378)  
#ревью #виселица 