https://github.com/ggrimnir/hangman  
[Grimnir]

Проект выполнен в ООП стиле.  
Архитектура MVC.  

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

- Противоречащая друг другу информация.  
Можно ввести команды не от 1 до 3, а от 1 до 2
```java
Invalid menu point input: must be chosen number from 1 to 3
Menu: 
1. Start new game 
2. Exit 
```

- Вводящий в заблуждение UI.  

Весь интерфейс на английском, но внезапно буквы требует вводить русские
```java
Please, enter a letter:
ы
Invalid letter used
Mistakes: 1 of 6
  +---+
  O   |
      |
      |
     ===
Word: *******
____________
Used letters: [ы]
Please, enter a letter:
s
Invalid letter request input: must be entered one Russian letter only
```

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву русского алфавита
+ 👍 Есть список введенных букв
+ 🚀 Архитектура MVC

## ЗАМЕЧАНИЯ

Судя по структуре проекта, была попытка написать проект в архитектуре MVC.  
Поэтому критика будет с позиций этой архитектуры. 

**1. Нейминг**

- Если в проекте есть класс `Word`, то все переменные с именем, включающим это название, должны быть экземплярами этого класса.  
Если разные концепции называются одним и тем же именем, это приводит к путанице
```java
public class Word {
  private final String word;  <-- Это поле "word" имеет тип String, а не Word
  //...
}

//ПРАВИЛЬНО:
public class Word {
  private final String text;
  //...
}
```

- Инициализация это когда устанавливаешь данные в поле, которое *уже* есть.  
Если ты что-то создаешь и возвращаешь, то это не инициализация
```java
public class MaskHandler {
  private final StringBuilder mask;

  public MaskHandler(Word word) {
    mask = initNewGameMask(word);
    extractLettersIndexes(word);
  }

  public StringBuilder initNewGameMask(Word word) {
    return new StringBuilder("*".repeat(word.getLength()));
  }
}

//ПРАВИЛЬНО ТАК:
public class MaskHandler {
  private final StringBuilder mask;

  public MaskHandler(Word word) {
    initMask(word);
    extractLettersIndexes(word);
  }

  public StringBuilder initMask(Word word) {
    this.mask = new StringBuilder("*".repeat(word.getLength()));
  }
}

//ТАК ТОЖЕ ПРАВИЛЬНО:
public class MaskHandler {
  private final StringBuilder mask;

  public MaskHandler(Word word) {
    mask = createMask(word);
    extractLettersIndexes(word);
  }

  public StringBuilder createMask(Word word) {
    return new StringBuilder("*".repeat(word.getLength()));
  }
}
```

- Тавтология. И так понятно, что действия внутри класса "Маска" выполняются именно в маске
```java
public class MaskHandler {
  public StringBuilder openLettersInTheMask(char letter) {...}
  //...
}

//ПРАВИЛЬНО:
public class MaskHandler {
  public StringBuilder openLetter(char letter) {...}
  //...
}
```

- Если метод называется "сравнить"(check), то его результат должен быть boolean.  
Если его результат не boolean, значит он не должен так называться
```java
void checkGuess(char letter)
```

- В названия не нужно вставлять частицы "For", "The", "Are" и т.д.

Это только делает названия более громоздкими.  
```java
void showInfoForValidGuess()

//ПРАВИЛЬНО:
void showValidGuessInfo()
```

- Частица "To" используется в методах конвертации: `toList()`, `toString()`, `toInt()`, `meterToFeet()`.   
В остальных случаях воздерживайся от этого
```java
void addToUsedLetters(char letter) {
  usedLetters.add(letter);
}

//ПРАВИЛЬНО:
void addUsedLetter(char letter) {
  usedLetters.add(letter);
}
```

- Избыточное уточнение. Все сообщения, которые здесь печатаются, печатаются для игрока
```java
public void printStatisticsMessageForPlayer(int count, int max) {
  System.out.printf("Mistakes: %d of %d%n", count, max);
}

//ПРАВИЛЬНО:
public void printStatistic(int count, int max) {
  System.out.printf("Mistakes: %d of %d%n", count, max);
}
```

- Избыточное, вводящее в заблуждение уточнение.

Если есть актуальная маска, то есть и неактуальная?(Нет)  
Почему `maskHandler` это просто `maskHandler`, а не `actualMaskHandler`?
```java
private final MaskHandler MaskHandler;
private StringBuilder actualMask;

//ПРАВИЛЬНО:
private final MaskHandler MaskHandler;
private StringBuilder mask;
```

- В названии метода глагол должен стоять первым.  
Название обманывает- этот метод не отправляет запрос, он получает букву
```java
char letterRequest() {
  //...
  return input.charAt(0);  
}

//ПРАВИЛЬНО:
char inputLetter() {
  //...
  return input.charAt(0);  
}
```

- Венгерская нотация.

В названии переменных не пиши тип данных, к которым они относится.  
И вообще не употребляй венгерскую нотацию.  
Название переменной должно отвечать на вопрос что хранит переменная, а не как хранит
```java
List<String> dictionaryList

//ПРАВИЛЬНО:
List<String> words
```

- Используй стандартные названия. 

Если нужно что-то откуда-то получить/прочитать, используй стандартные названия: `get`, `read` etc.
```java
public class DictionaryChooser implements Chooser {
  public String choose() {...}
}

//ЛУЧШЕ:
public class FileDictionary implements Dictionary {
  public String get() {...}
}
```

- Используй стандартные/привычные названия. 

Если нужно обозначить границы, просто пиши "min", "max"
```java
int getRandomNum(int border)

//ПРАВИЛЬНО:
int getRandomNum(int max)
```

Или используй названия по аналогии cо стандартными библиотеками.  
Например:
```java
int getRandomNum(int bound)

//ПО АНАЛОГИИ С:
public class Random implements RandomGenerator, java.io.Serializable {
  public int nextInt(int bound) {...}
}
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Нарушение конвенции кода**

- Скобочки.

В любой ситуации выделяй тело блока скобочками, даже если тело состоит из одной строки.  
Исключение- метод equals(), там можно после if не выделять блоки скобочками
```java
if (menuPoint > MAIN_MENU_MAX_POINT || menuPoint < MAIN_MENU_MIN_POINT)
  throw new ConsoleMenuInputException("Invalid menu point input: must be chosen number from 1 to 3");

//ПРАВИЛЬНО:
if (menuPoint > MAIN_MENU_MAX_POINT || menuPoint < MAIN_MENU_MIN_POINT) {
  throw new ConsoleMenuInputException("Invalid menu point input: must be chosen number from 1 to 3");
}
```

- Константы должны стоять выше остальных полей
```java
public class Console {
  private final Scanner scanner;
  private static final int MAIN_MENU_MAX_POINT = 2;
  //...
}

//ПРАВИЛЬНО:
public class Console {
  private static final int MAIN_MENU_MAX_POINT = 2;
  private final Scanner scanner;
  //...
}
```

*"Oracle Java code conventions"* 

**3. Нарушение DRY**

- Магические буквы, числа, слова. Вводи константы
```java
System.out.println("""
    Menu:\s
    1. Start new game\s
    2. Exit\s
    """);

System.out.println("One more game? \n" +
    "1. Yes! \n" +
    "2. No... \n");

if (menuPoint > MAIN_MENU_MAX_POINT || menuPoint < MAIN_MENU_MIN_POINT)
  throw new ConsoleMenuInputException("Invalid menu point input: must be chosen number from 1 to 3");

//ПРАВИЛЬНО:
private static final int START = 1;
private static final int EXIT = 2;

private static final String INVALID_MENU_INPUT_MESSAGE = "Invalid menu point input: must be chosen number from %d to %d".formatted(START, EXIT);

System.out.println("Menu");
System.out.printlf("%d. Start new game  %n", START);
System.out.printlf("%d. Exit  %n", EXIT);

System.out.println("One more game?");
System.out.printlf("%d. Start new game  %n", START);
System.out.printlf("%d. Exit  %n", EXIT);

if (menuPoint > MAIN_MENU_MAX_POINT || menuPoint < MAIN_MENU_MIN_POINT) {
 throw new ConsoleMenuInputException(INVALID_MENU_INPUT_MESSAGE);
}
```

- Общая магия в разных классах.

Если к одной и той же магической штуке обращаются два разных класса, то делай ее константой в одном из этих двух классов.  
Либо перенеси ее в третий класс и из первых двух классов обращайся к этим константам- эти данные должны быть синхронизированы между собой
```java
public class Game {
 
  public void checkGameStatus() {
    if (actualMask.indexOf("*") == -1) {...}  <-- ОБЩАЯ МАГИЯ
  }
  //...
}

public class MaskHandler {

  public StringBuilder initNewGameMask(Word word) {
    return new StringBuilder("*".repeat(word.getLength()));  <-- ОБЩАЯ МАГИЯ
  }
  //...
}
```

А здесь общая магия не только в разных классах, но и в разных слоях
```java
public class GameProcess {  <-- МОДЕЛЬ        
  //...

  public void execute() {
    try {
      switch (console.mainMenuStart()) {
        case 1 -> {...}  <-- ОБЩАЯ МАГИЯ
        }
        case 2 -> {...}  <-- ОБЩАЯ МАГИЯ
    } catch (ConsoleMenuInputException e) {...}
  }
  //...
}

public class Console {  <-- ВЬЮ

  public int mainMenuStart() {
    System.out.println("""
        Menu:\s
        1. Start new game\s  <-- ОБЩАЯ МАГИЯ
        2. Exit\s            <-- ОБЩАЯ МАГИЯ
        """);
    //...
  }
  //...
}
```

- Если уже есть константы, то используй их
```java
public class Console {
  private static final int MAIN_MENU_MAX_POINT = 2;
  private static final int MAIN_MENU_MIN_POINT = 1;
  //...

  private void validateMenuPointInput(String input) {
    //...
    if (menuPoint > MAIN_MENU_MAX_POINT || menuPoint < MAIN_MENU_MIN_POINT)
      throw new ConsoleMenuInputException("Invalid menu point input: must be chosen number from 1 to 3");
    }
  }
}

//ПРАВИЛЬНО:
private static final int MAIN_MENU_MAX_POINT = 2;
private static final int MAIN_MENU_MIN_POINT = 1;
private static final String MENU_INPUT_EXCEPTION = "Invalid menu point input: must be chosen number from %d to %d".formatted(MAIN_MENU_MIN_POINT, MAIN_MENU_MAX_POINT);

private void validateMenuPointInput(String input) {
  //...
  if (menuPoint > MAIN_MENU_MAX_POINT || menuPoint < MAIN_MENU_MIN_POINT) {
    throw new ConsoleMenuInputException(MENU_INPUT_EXCEPTION);  
  }
}
```

*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*

**4. Форматирование строк**

- Мысленные преобразования.

Не вставляй перевод строки в середину строк при распечатке.  
Иначе при чтении кода приходиться мысленно "разворачивать" одну строку в несколько.  
Если нужно распечатать несколько строк, то печатай их или многострочными строками(""") или отдельными инструкциями печати.  
Тогда сразу будет видно, как текст будет выглядеть на печати:
```java
System.out.println("Congratulations! You win!\n____________");

//ПРАВИЛЬНО:
System.out.println("Congratulations! You win!");
System.out.println("____________");
```

- Не экономь строки в ущерб читаемости кода
```java
System.out.println("Word: " + mask + "\n____________");

//ПРАВИЛЬНО:
System.out.println("Word: " + mask);
System.out.println("____________");
```

- Для лучшей читаемости, конечный перевод строки отделяй от основного шаблона на 1-2 пробела
```java
System.out.printf("Mistakes: %d of %d%n", count, max);

//ЛУЧШЕ:
System.out.printf("Mistakes: %d of %d  %n", count, max);
```

**5. Рекурсия**

В коде часто используется рекурсия
```java
private void showReplayRequest() {
  try {
    //...
  } catch (ConsoleMenuInputException e) {
    showReplayRequest(); <-- РЕКУРСИЯ
  }
}

public void execute() {
  try {
      //...
  } catch (ConsoleMenuInputException e) {
    System.out.println(e.getMessage());
    execute(); <-- РЕКУРСИЯ
  }
}
```

Рекурсия должна использоваться только там, где она оптимальна алгоритмически.
Например, при обходе бинарного дерева.

В других ситуациях, где рекурсия может быть заменена циклом, она должна быть им заменена.  
Рекурсия тяжело читается и при определенных условиях может привести к переполнению стека.  
Убери рекурсию, замени ее использование на цикл `while()`. 

Условный пример
```java
//❌ ПЛОХО:
void start() {
  printMessage("Введите команду: 0-выйти, 1-играть");  
  int command = inputCommand();

  if(command == 0) {
    return;
  } 

  if(command == 1) {
    playGame();
  } 

  start();  <-- РЕКУРСИЯ
}

//✔️ ХОРОШО:
void start() {
  while(true) {
    printMessage("Введите команду: 0-выйти, 1-играть");  
    int command = inputCommand();

    if(command == 0) {
      return;
    } 

    if(command == 1) {
      playGame();
    } 
  }  
}
```

**6. Использование исключений**

- Не используй исключения для организации ветвлений бизнес логики.

У тебя в программе исключения часто используются неправильно.  
Ты ими заменяешь if-else и на основе исключений делаешь ветвление логики.  
А исключения на то и исключения, чтобы использовать их в ИСКЛЮЧИТЕЛЬНЫХ СИТУАЦИЯХ.

**Например, банальная задача:**  
Получить букву от юзера и в зависимости от этой буквы(использовалась ли она ранее и т.д.) выполнить те или иные действия.

Ты эту задачу решаешь через исключения:
```java
public class GameProcess {
 //...
 
  private void processOneTurn() {
    try {
      char letter = console.letterRequest();
      game.checkGuess(letter);
      showInfoForValidGuess();
    } catch (GameLetterUsedException | ConsoleLetterRequestInputException e) {
      console.printExceptionMessage(e);
    } catch (GameLetterInvalidException e) {
      console.printExceptionMessage(e);
      showInfoForInvalidGuess();
    }
    showUsedLetters();
  }
}
```

То есть, если буква уже была использована ранее- бросается исключение; если это буква неправильного алфавита, бросается исключение и т.д.  
Такой код вообще нечитабелен, понять из него алгоритм работы программы очень трудно. 

Вот тоже самое, но через обычное ветвление if-else:
```java
public class GameProcess {

  private void processOneTurn() {
    char letter = console.letterRequest();
    Game.MoveResult moveResult = game.makeMove(letter); 

    if(isPreviousUsedLetter(moveResult)) {
      showPreviousUsedLetter();
    } else if (isБукваНеправильногоАлфавита(moveResult)) {
      showInfoForInvalidGuess();
    } else if (isOk(moveResult)){
      showInfoForValidGuess();
    } else {
      throw new IllegalArgumentException("illegal moveResult: " + moveResult);
    }
  }

  private boolean isPreviousUsedLetter(Game.MoveResult moveResult) {  
    return moveResult == game.GameMoveResult.PREVIOUS_USED_LETTER;  //эта буква была введена ранее
  }

  //...
}

public class Game {
  //...
  public MoveResult makeMove(char letter) {...} 

  //результат выполнения хода
  public enam MoveResult {  
    PREVIOUS_USED_LETTER, 
    ...,
    OK  
  }
}
```

Да, этот гипотетический метод `MoveResult makeMove(char letter)` совмещает в себе команду и запрос.  
В данном случае это сделано для упрощения кода при рассматривании взаимодействия движка игры(Game) и контроллера движка(GameProcess).

**Еще одна простая задача:**  
Получить номер пункта меню и выполнить соответствующие действия.  

Тут банальную задачу ввода правильного/неправильного пункта меню ты тоже решаешь через обработку исключений 
```java
public class GameProcess {
 //...

  private void showReplayRequest() {
    try {
      int response = console.replayRequest();
      switch (response) {
        case 1 -> {
          game = new Game();
          startNewGame();
        }
        case 2 -> console.exit();
      }
    } catch (ConsoleMenuInputException e) {
      System.out.println(e.getMessage());
      showReplayRequest();
    }
  }
}
```

Чтобы избавиться от смешивания сложной бизнес логики с обработкой ошибок, переделаем метод ввода команд таким образом, 
чтобы он физически не мог вернуть неправильную команду. Для вью это будет корректное поведение.  
Например:
```java
public class Console {
  //...

  public Command inputCommand() {
    while(true) {  //Долбим юзера пока он не введет то, что мы от него требуем
      System.out.printf("%d. Start new game  %n", Command.START.ordinal());
      System.out.printf("%d. Exit  %n", Command.QUIT.ordinal());

      String key = scanner.nextLine().toUpperCase();

      if(isCorrectCommandKey(key)) {
        int index = Integer.parseInt(key);
        return Command.values()[index];
      }
      System.out.printf("Invalid menu point input: must be chosen number from %d to %d  %n", 0, Command.values().length - 1);
    }
  }

  private boolean isCorrectCommandKey(String key) {
    try {
      int index = Integer.parseInt(key);
      return index >= 0 && index < Command.values().length;
    } catch (NumberFormatException e) {
      return false;
    }
  }

  public enum Command {
    START, QUIT;
  }
}
```

Тогда на уровне более высокой логики получение команды меню будет выглядеть так:
```java
public class GameProcess {
 //...

  private void showReplayRequest() {
    Console.Command command = console.inputCommand();
    switch (command) {
      case START -> {
        game = new Game();
        startNewGame();
      }
      case QUIT -> console.exit();
    }
  }
}
```

Как видим, полностью избиваться от обработки исключений не получилось.  
Но полностью избавиться от исключений и невозможно.  
Можно только изолировать блоки с try-catch от сложной бизнес логики в отдельные более простые методы.

*"ЧК", гл.3*  
```java
"Блоки try/catch выглядят уродливо... они смешивают обработку ошибок с нормальной обработкой" - Мартин
```

*Хорстманн "Java. Библиотека профессионала", т1., гл.11*
```java
"Обработка исключений не может заменить собой простую проверку" - Хорстманн
```

- Не используй исключения как телеграмму передачи сообщений внутри программы.

Исключение это не телеграмма.  
Исключения не нужно использовать для обмена сообщениями между частями программы.  
Назначение исключения- распечатать сообщение тогда, когда программа аварийно остановит свою работу.  

Единственное назначение message в exception- быть распечатанным ТОЛЬКО тогда, когда исключение не было перехвачено и обработано внутри программы.

Ты используешь исключения в качестве средства передачи сообщениями внутри программы
```java
private void processOneTurn() {
  try {
    //...
  } catch (GameLetterUsedException | ConsoleLetterRequestInputException e) {
    console.printExceptionMessage(e);  <-- РАСПЕЧАТКА MESSAGE ИСКЛЮЧЕНИЯ ВО ВРЕМЯ ШТАТНОЙ РАБОТЫ ПРОГРАММЫ
  }
}
```

В качестве мысленного эксперимента представь, что в программе у тебя можно выбирать разные языки интерфейса- русский, английский, монгольский.  
Тогда если использовать `exception` как телеграмму, то те модели, которые бросают исключения в качестве телеграммы, 
должны будут учитывать текущий язык интерфейса и записывать в исключения message's на конкретном языке.  
То есть, эти модели зависят от представления.

Поэтому правильно распечатывать инфу в случае перехвата исключений нужно так:
```java
private void processOneTurn() {
  try {
    //...
  } catch (GameLetterUsedException e) {
    console.printExceptionMessage("бла-бла-бла сообщение");
  } catch (ConsoleLetterRequestInputException e) {
    console.printExceptionMessage("бла-бла-бла другое сообщение");
  }
}
```

- Делай информативные сообщения.

Из-за того, что ты используешь исключения как телеграммы внутри программы,  
то и текст сообщений в исключениях ты подгоняешь под требования показа текста юзеру во время *штатной* работы программы.  

На самом деле текст сообщений в исключениях должен как можно лучше объяснить причину при аварийной остановке программы.  
Для этого message должен быть достаточно информативен.  

Например, если исключение вылетело из-за какой-то буквы, то нужно в сообщении указать эту букву
```java
if (!isLetterValid(letter)) {
  throw new GameLetterInvalidException("Invalid letter used");
}

//ПРАВИЛЬНО:
if (!isLetterValid(letter)) {
  throw new GameLetterInvalidException("Invalid letter used: " + letter);
}
```

### МОДЕЛИ

В архитектуре MVC модели должны хранить данные и алгоритмы бизнес-логики, которые не взаимодействуют с представлением. 

**7. class Word**

- Позиционирование.

В идеале, в ООП архитектуре каждый класс должен из себя представлять какую-то внятную сущность.  

Какую сущность из себя представляет этот класс "Word"?  

Фактически, это Строка, которая сам себя инициализирует данными из источника слов(словаря).  
Если убрать самоинициализацию, останется просто аналог String c аналогичными методами:  
`List<Character> extractAllLetters()` у класса Word и `char[] toCharArray()` в классе String etc.

Но класс не должен сам себя инициализировать данными, это нарушение DI
```java
public class Word {
  private final Chooser chooser;
  private final String word;
  //...

  public Word() {
    chooser = new DictionaryChooser();
    word = chooser.choose();
    //...
  }
}

//ПРАВИЛЬНО:
public class Word {
  private final String text;
  //...

  public Word(String text) {
    this.text = text;
    //...
  }
}
```

- Спагетти код, нарушение Low Coupling.

По той же причине между классами получаются запутанные связи- кастомный класс `Word` зависит от кастомного класса `DictionaryChooser`.

- Шум в комментариях.

Не пиши в комментариях очевидное- мы все пониманием концепцию перегрузки методов
```java
public Word() {...}

// добавляем гибкость, на случай, если будет другой источник словаря, например
public Word(Chooser chooser) {...}
```
В идеале, комментариев вообще не должно быть(кроме `TODO`), код должен объяснять сам себя через правильный нейминг и лаконичный код.  
*Мартин, "ЧК", гл.4"*

- Неправильная перегрузка конструктора
```java
public Word() {
  chooser = new DictionaryChooser();
  word = chooser.choose();
  letters = extractAllLetters();
  length = word.length();
}

public Word(Chooser chooser) {
  this.chooser = chooser;
  word = chooser.choose();
  letters = extractAllLetters();
  length = word.length();
}

//ПРАВИЛЬНО:
public Word(Chooser chooser) {
  this.chooser = chooser;
  word = chooser.choose();
  letters = extractAllLetters();
  length = word.length();
}

public Word() {
  this(new DictionaryChooser());
}
```
*Эккель "Философия Java", гл.5 "Вызов конструктора из конструктора"*

- Не кешируй значения в полях, если можно просто вызвать методы
```java
public class Word {
  private final String word;
  private final int length;
  //...

  public Word() {
    //...
    word = chooser.choose();
    length = word.length();
  }

  public int getLength() {
    return length;
  }
  //...
}

//ПРАВИЛЬНО:
public class Word {
  private final String word;
  //...

  public Word() {
    //...
    word = chooser.choose();
  }

  public int getLength() {
    return word.length();
  }
  //...
}
```

- Неправильный toString(). 

toString() должен быть стандартным(как делает IDE по Alt+Ins) и использоваться только для отладки.  
toString() должен содержать значение всех значимых полей класса.  
toString() не должен использоваться для представления, за исключением предельно простых классов, вроде PhoneNumber
```java
public String toString() {
  return word;
}

//ПРАВИЛЬНО:
public String toString() {
    return "Word{" +
        "chooser=" + chooser +
        ", word='" + word + '\'' +
        ", letters=" + letters +
        ", length=" + length +
        '}';
  }
```
*Блох, "Java. Эффективное программирование", изд.3, гл.3.3*  

- Класс неправильно декомпозирован.

Итак, сейчас класс `Word` это просто `String`, который инициализирует сам себя.  
Это неправильная декомпозиция.

В "Виселице" действительно есть сущность "Слово", но состояния и поведение у этой сущности должны быть другими.  
Пример правильной ООП декомпозиции"Слова"- у Сергея в эталонном проекте.

**8. class MaskHandler**

- Названия типа "Handler", "Manager", "Helper" etc.

**Лайфхак:**  
Если класс называется "Handler", "Manager", "Helper", то скорее всего, с ним что-то не так.  

Либо слово "Handler" просто слово-паразит, которое можно выкинуть и смысл класса не поменяется.

Либо класс делает много разного, из-за чего в названии нельзя внятно сформулировать, что он делает.  
И тогда класс совмещает в себе несколько разных обязанностей.

Либо класс нельзя нормально назвать, потому что он является просто контейнером функций в процедурном стиле программирования.

Как и всегда, это правило не абсолютное, но здесь сейчас так.  
В данном случае "Handler" это слово слово-паразит. Если оставить просто `class Mask`, это не ухудшит, а намного улучшит понимание того, что из себя представляет этот класс.

- Нарушение инкапсуляции.

Публичными должны быть только те методы, которые предназначены для использования клиентами.  
Методов, предназначенные для использования потомками, должны быть `protected`.  
Остальные методы в классах должны быть `private`
```java
public MaskHandler(Word word) {
  mask = initNewGameMask(word);
  extractLettersIndexes(word);
}

public StringBuilder initNewGameMask(Word word) {...}

public void extractLettersIndexes(Word word) {...}

//ПРАВИЛЬНО:
public MaskHandler(Word word) {
  mask = initNewGameMask(word);
  extractLettersIndexes(word);
}

private StringBuilder initNewGameMask(Word word) {...}

private void extractLettersIndexes(Word word) {...}
```

- Запутанный алгоритм с использованием сета индексов букв
```java
private final Map<Character, List<Integer>> lettersIndexes = new HashMap<>();
```
Придумай более простой алгоритм работы с буквами маски, без сета индексов.  
Иначе выглядит это как полное адище начиная с создания такого сета:
```java
public void extractLettersIndexes(Word word) {
  for (int i = 0; i < word.getLength(); i++) {
    char curr = word.getWord().charAt(i);
    int index = i;
    lettersIndexes.compute(curr, (key, val) ->
    {
      if (val == null) {
        val = new ArrayList<>();
        val.add(index);
     } else {
        val.add(index);
      }
      return val;
    });
  }
}
```

И кончая открытием букв в маске
```java
public StringBuilder openLettersInTheMask(char letter) {
  List<Integer> indexes = lettersIndexes.getOrDefault(letter, Collections.emptyList());
  for (int i : indexes) {
    mask.setCharAt(i, letter);
  }
  return mask;
}
```

Задачка на алгоритмическое мышление:  
Сделать метод открывания букв в маске, но без использования в классе поля сета индексов букв. 

- Побочный эффект. 

Нельзя в методе устанавливать поле класса и возвращать эти же данные через return. 
В этом случае инициализация поля класса будет происходить неявно для клиентского кода. 
То есть, это будет побочный эффект
```java
private final StringBuilder mask;  <-- ПОЛЕ КЛАССА

public StringBuilder openLettersInTheMask(char letter) {
  List<Integer> indexes = lettersIndexes.getOrDefault(letter, Collections.emptyList());
  for (int i : indexes) {
    mask.setCharAt(i, letter); <-- ИЗМЕНИЛ СОСТОЯНИЕ ПОЛЯ КЛАССА
  }
  return mask;  <-- ВЕРНУЛ ЭТО ПОЛЕ
}

//ПРАВИЛЬНО ТАК:
private final void StringBuilder mask;

public void openLettersInTheMask(char letter) {
  List<Integer> indexes = lettersIndexes.getOrDefault(letter, Collections.emptyList());
  for (int i : indexes) {
    mask.setCharAt(i, letter);
  }
}
```
*Мартин "Чистый код", гл.3, "Избавьтесь от побочных эффектов"*  


- Декомпозиция класса.

Считать маску отдельной сущностью лично мне кажется сомнительным.  
Я думаю, что маска это часть другой сущности, которая здесь искусственно разделена на несколько других.

Но даже если считать маску отдельной сущностью, то частью это сущности нужно считать и поведение, которое определяет, открыта ли маска полностью.  
Сейчас это поведение живет где-то вне маски.  

Причем, живёт так мутно, что не сразу его и проследишь
```java
public class Game {
  private final MaskHandler maskHandler;
  private StringBuilder actualMask;
  //...

  public Game() {
    maskHandler = new MaskHandler(word);
    actualMask = maskHandler.getMask();
    //...
  }

  public void checkGameStatus() {
    if (actualMask.indexOf("*") == -1) {  <-- ВОТ ОНО
      changeGameStatusToWon();
    } else if (getMistakesCount() == MAX_MISTAKES_COUNT) {
      changeGameStatusToLost();
    }
  }
}
```

**9. class Game**

+ 👍 Этот класс- движок игры, движок это хорошо.

Движок это вся игровая логика без взаимодействия с представлением.  
Игровая логика с взаимодействием с представлением в таких случаях выносится в отдельный контроллер.  
Здесь таки и сделано: движок это `class Game`, а контроллер- `class GameProcess`.

- Модель не должна работать с представлением
```java
public class Game {
  //...

  public String getHangmanStage() {
    return HangmanAsciiArt.getStage(mistakesCount);  <-- ПРЕДСТАВЛЕНИЕ
  }
}
```
Модель не должна ничего знать ни про представление, ни про контроллеры.  
Под "знанием" понимается хранение, вызов методов и любой другое использование моделью классов не_моделей.

Представление(Вью) может знать про модель только то, что нужно знать для отрисовки модели.  
Контроллер может(должен) знать всё про всех.

В MVC модель может дергать представление, но только косвенно- через использование паттерна `Callback`.  
Это называется "MVC с активной моделью".  
Но в этом проекте модели пассивные.

Избавить`Game` зависиморсти от представления можно так:
```java
public class GameProcess {
  //...

  private void showInfoForInvalidGuess() {
    console.printHangmanStage(game.getHangmanStage()); <-- ВЫТЯГИВАЕТ ПРЕДСТАВЛЕНИЕ ИЗ МОДЕЛИ
    //...
  }
}

public class Console {
  //...

  public void printHangmanStage(String stage) {
    System.out.println(stage);
  }
}

public class Game {
  //...

  public String getHangmanStage() {
    return HangmanAsciiArt.getStage(mistakesCount);  <-- ЗАВИСИМОСТЬ ОТ ПРЕДСТАВЛЕНИЯ В МОДЕЛИ GAME
  }
}

//ПРАВИЛЬНО:
public class GameProcess {
  //...

  private void showInfoForInvalidGuess() {
    int mistakesCount = game.getMistakesCount();
    console.printHangmanStage(mistakesCount);
    //...
  }
}

public class Console {
  //...

  public void printHangmanStage(int mistakesCount) {
    String stage = HangmanAsciiArt.getStage(mistakesCount);
    System.out.println(stage);
  }
}
```

В качестве мысленного эксперимента представь, что ты делаешь версию этой игры на Swing или Андроид.  
Для этого **все модели**, которые ты используешь в консольной версии, должны без всяких изменений использоваться и в Swing/Андроиде.  

Но нужно ли модели `Game` для этих визуальных сред хранить в себе зависимость от псевдографики класса `HangmanAsciiArt`?  
Очевидно, что нет.  
Скорее всего, там мы будем виселицу показывать пиксельными картинками, а не убогой псевдографикой ASCII.

**10. Пакет util**

В этом пакете нет утилит.

Утилитными являются классы, которые содержат только статические методы и не содержат изменяемых полей.  
Классы в этом пакете- модели.

*Блох, "Java. Эффективное программирование", изд.3, гл.2.4*

**11. interface Chooser**, интерфейс словаря

👍 Интерфейс словаря это хорошо.  
Можно делать разные его реализации, которые будут получать слова из разных источников(файл, http, из памяти etc)
```java
public interface Chooser {
  String choose();
}
```

**12. class DictionaryChooser implements Chooser**

Файловая реализация словаря. Словарь это модель.

+ 👍 В случае отсутствия файла словаря, программа корректно закрывается без вываливания красных иксепшенов в консоль.  
Это хорошо
```java
Fatal error: dictionary not found or empty! 
Check file src/main/resources/dictionary: it must exist and contain words longer than 5 letters
```

- Путь к файлу класс должен получать в конструктор. 

Иначе класс становится неуниверсальным.  
Допустим, в программе будет несколько разных текстовых файлов- на разных языках или по разным темам.  
Тогда вместо нескольких специализированных классов Словаря, где имя файла словаря будет жестко прописано в каждом из классов, 
можно будет использовать один универсальный класс Словарь.

- Магическое число: 5.

### ПРЕДСТАВЛЕНИЕ (VIEW)

В архитектуре MVC вью должны распечатывать данные, в том числе модели.  
Также вью должны принимать ввод от юзера.  
Вью не должны ничего знать про контроллеры.

**13. class HangmanAsciiArt**

- Используй многострочные строки
```java
private static final String[] STAGES = {
    "  +---+\n" +
    "  O   |\n" +
    "      |\n" +
    "      |\n" +
    "     ===",
    //....
}

//ЛУЧШЕ:
private static final String[] STAGES = {
    """  
       +---+
       O   | 
           |
           |
          ===""",
    //....
}
```
Такая запись switch-case появилась начиная с java 13.

**14. class Console**

+ 👍 Отдельный класс, через который происходит весь пользовательский ввод/вывод это хорошо.

- Нарушение DRY, дублирование кода.  
Вводи вспомогательные методы
```java
public int mainMenuStart() {
  System.out.println("""
    Menu:\s
    1. Start new game\s
    2. Exit\s
    """);
  String input = scanner.nextLine();
  validateMenuPointInput(input);
  return Integer.parseInt(input);
}

public int replayRequest() {
  System.out.println("One more game? \n" +
    "1. Yes! \n" +
    "2. No... \n");
  String input = scanner.nextLine();
  validateMenuPointInput(input);
  return Integer.parseInt(input);
}

//ПРАВИЛЬНО:
private static final String MAIN_MENU_START_MESSAGE = """
  Menu:
  1. Yes! 
  2. No...""";

private static final String REPLAY_REQUEST_MESSAGE = """
  One more game? 
  1. Yes! 
  2. No...""";  

public int mainMenuStart() {
  return someMethod(MAIN_MENU_START_MESSAGE);
}

public int replayRequest() {
  return someMethod(REPLAY_REQUEST_MESSAGE);
}

private int someMethod(String message) {
  System.out.println(message);
  String input = scanner.nextLine();
  validateMenuPointInput(input);
  return Integer.parseInt(input);
} 
```

### КОНТРОЛЛЕРЫ 

Контроллеры знают всё про слои view и модель.  
Задача контроллеров состоит в том, чтобы связывать модели и вью в единую программу.

**15. class GameProcess**

+ 👍 Единственный контроллер в программе и в пакете "controller".  
И это действительно контроллер.

+ 👍 Что еще лучше, это тонкий контроллер.

Здесь находится только та логика игры, которая работает с представлением.  
Логика игры, которая не работает с представлением, находится в движке игры, в классе `Game`.  
Связка движок_игры + контроллер_игры это хорошо.

- Нарушение DRY.

Дублирование кода. Подумай, как можно вынести общий код отсюда во вспомогательный метод
```java
public void execute() {
  try {
    switch (console.mainMenuStart()) {
      case 1 -> {
        console.printNewGameMessage();
        startNewGame();
      }
      case 2 -> console.exit();
    }
  } catch (ConsoleMenuInputException e) {
    System.out.println(e.getMessage());
    execute();
  }
}

private void showReplayRequest() {
  try {
    int response = console.replayRequest();
    switch (response) {
      case 1 -> {
        game = new Game();
        startNewGame();
      }
      case 2 -> console.exit();
    }
  } catch (ConsoleMenuInputException e) {
    System.out.println(e.getMessage());
   showReplayRequest();
  }
}
```

- Функционал ок, реализация не ок.

Функционал класса в принципе ок, претензии к коду:
Exception'ы используются для реализации бизнес логики вместо `if-else`, капец это напрягает.

### MAIN

Main не относится ни к одному слою MVC, это точка входа в программу.  
Он предназначен для начального конфигурирования программы и её последующего запуска.

**16. class App**

- Этот main не соответствует идее MVC- в нём слишком мало DI
```java
public static void main(String[] args) {
  try {
    GameProcess gameProcess = new GameProcess();
    gameProcess.execute();
  } catch (DictionaryEmptyException e) {...}
}
```

MVC программа должна обеспечивать более гибкое использование моделей и представлений.  
Например так:
```java
public static void main(String[] args) {
  try {
    //вью
    View view = new ConsoleView();

    //модели
    Dictionary dictionary = new FileDictionary("ru_words.txt");
    Game game = new Game(dictionary);

    //контроллеры 
    GameProcess gameProcess = new GameProcess(view, game);
    gameProcess.execute();

  } catch (DictionaryEmptyException e) {...}
}
```

### АРХИТЕКТУРА

**17. Неправильное использование интерфейсов**

В проекте есть интерфейс словаря и это действительно *потенциально* полезная штука.  
Проблема только в том, что в рамках твоей архитектуры этот интерфейс совершенно бесполезен.

В программе ты используешь Словарь не как интерфейс, а как конкретную его реализацию
```java
public class Game {
  //...

  public Game() {
    word = new Word();
    //...
  }
  //...
}

public class Word {
  //...

  public Word() {
    chooser = new DictionaryChooser();  
    //...
  }
}
```
С тем же успехом можно было не выделять интерфейс словаря и использовать только класс `DictionaryChooser`.  

Чтобы в интерфейсах появился смысл, должна быть возможность инжектить их прямо с самого верхнего этажа.  
На примере словаря выглядеть это должно примерно так:
```java
public class FirstMain {
  public static void main(String[] args) {

    Dictionary dictionary = new FileDictionary("ru_words.txt");
    Game game = new Game(dictionary);
    GameController controller = new GameController(game);

    controller.execute();
  }
}

public interface Dictionary {
  String get();
}

public class FileDictionary implements WordDictionary{
  //...

  public FileDictionary(String filename) {...}

  @Override
  public String get() {...}
}
```

В этом случае можно будет легко собирать разные конфигурации, не меняя код в уже сделанных контроллерах и моделях
```java
public class SecondMain {
  public static void main(String[] args) {

    Dictionary dictionary = new Dictionary() {
      private final List<String> words = List.of("инкапсуляция", "наследование", "полиморфизм");
      
      @Override
      public String get() {
        Random random = new Random();
        int index = random.nextInt(words.size());
        return words.get(index);
      }
    };

    Game game = new Game(dictionary);
    GameController controller = new GameController(game);

    controller.execute();
  }
}
```
Здесь нет ничего сверхъестественного, просто обычный полиморфизм и Dependency injection. 

В проекте может быть несколько разных конфигураций и, соответственно, разных Main'ов.

**18. Использование слоя View**

Из-за того, что вью в твоей версии MVC не используется через полиморфизм, он теряет 90% своих возможностей.

Идея та же- создать интерфейс view, через который делать весь юзерский ввод/вывод:

Примерно, так:
```java
public interface View {
  void showHangman(int state);

  void showWin();

  void showLose();
}
```

Потом сделать консольную реализацию:
```java
public class ConsoleView implements View{
  private static final String[] PICTURES = {
      //картинки хангмана
  };

  @Override
  public void showHangman(int state) {
    String picture = PICTURES[state];
    System.out.println(picture);
  }

  @Override
  public void showWin() {
    System.out.println("ВЫ ВЫИГРАЛИ!");
  }

  @Override
  public void showLose() {
    System.out.println("Вы проиграли :(");
  }
}
```

Вью тоже должны инжектиться прямо с самого верха:
```java
public class ThirdMain {
  public static void main(String[] args) {
    View view = new ConsoleView();

    Dictionary dictionary = new FileDictionary("ru_words.txt");
    Game game = new Game(dictionary);
    GameController controller = new GameController(view, game);

    controller.execute();
  }
}
```

Что это нам даёт?  
Дает возможность делать конфигурации с разным представлениями, не меняя код в моделях и контроллерах,
а только добавляя в проект новые вью.  
Например, вью цветной распечатки в консоли:
```java
public class ColorConsoleView extends ConsoleView {

  public static final String RESET_COLOR = "\u001B[0m";
  public static final String RED = "\u001B[31m";
  public static final String GREEN = "\u001B[32m";

  @Override
  public void showWin() {
    setColor(GREEN);
    super.showWin();
    resetColor();
  }

  @Override
  public void showLose() {
    setColor(RED);
    super.showLose();
    resetColor();
  }

  private void setColor(String color) {
    System.out.print(color);
  }

  private void resetColor() {
    System.out.print(RESET_COLOR);
  }
}
```

Использование:
```java
public class ColoredMain {
  public static void main(String[] args) {
    View view = new ColorConsoleView();

    Dictionary dictionary = new FileDictionary("ru_words.txt");
    Game game = new Game(dictionary);
    GameController controller = new GameController(view, game);

    controller.execute();
  }
}
```

**View это не обязательно вывод текста только на экран.**

Вью это любой способ взаимодействия с юзером: показывания ему информации и получения от него команд.

Допустим, у нас есть аркадный автомат для игры Hangman:  
![pic](https://github.com/raketareview/hangman-review/blob/master/content/resources/rev-hm169/arcade_machine_hangman.png) 

Лампочки и кнопки здесь являются интерфейсом взаимодействия с игроком.

В этом гипотетическом аркадном аппарате нет мозгов, а есть только модуль дискретного ввода/вывода, который принимает команды от ПК.  
И по этим командам модуль включает/выключает лампочки.  

ФОРМАТ КОМАНДЫ:  
Длина команды: 2 байта.  
1 байт: номер команды   
2 байт: адрес лампы(или кнопки)  

Тогда для управления таким аппаратам через ПК нужно сделать специальную реализацию View: 
```java
public class ArcadeMachineView implements View {
  private final static int PORT_ADDRESS = 1;

  private final static byte COMMAND_LED_ON = 1;
  private final static byte COMMAND_LED_OFF = 2;

  private final static byte LED_WIN = 1;
  private final static byte LED_LOSE = 2;

  private final static byte LED_FIRST_HANGMAN_PICTURE = 3;

  private final static int HANGMAN_STATES = 5;

  //COM-порт компьютера
  private final ComPort comPort;

  public ArcadeMachineView() {
    //подключаемся к аркадному автомату через com-порт
    this.comPort = new ComPort(PORT_ADDRESS);
  }

  @Override
  public void showHangman(int stateNumber) {
    //выключаем лампы всех картинок
    for(int i = 0; i < HANGMAN_STATES; i++) {
      byte ledOffAddress = (byte) (LED_FIRST_HANGMAN_PICTURE + i);
      outCommand(COMMAND_LED_OFF, ledOffAddress);
    }

    //включаем нужную картинку
    byte ledOnAddress = (byte) (LED_FIRST_HANGMAN_PICTURE + stateNumber);
    outCommand(COMMAND_LED_ON, ledOnAddress);
  }

  @Override
  public void showWin() {
    outCommand(COMMAND_LED_ON, LED_WIN);
  }

  @Override
  public void showLose() {
    outCommand(COMMAND_LED_ON, LED_LOSE);
  }

  private void outCommand(byte type, byte address) {
    comPort.outByte(type);
    comPort.outByte(address);
  }
}
```

И дальше управлять аркадным автоматом можно с помощью той же MVC-шной программы, просто подставив нужный вью:
```java
public class ArcadeMachineMain {
  public static void main(String[] args) {
    View view = new ArcadeMachineView();

    Dictionary dictionary = new FileDictionary("ru_words.txt");
    Game game = new Game(dictionary);
    GameController controller = new GameController(view, game);

    controller.execute();
  }
}
```

## ВЫВОД

исключения вместо if-else
Это не mvc и не ооп, это основы процедурное программирование.

Спагетти код

n.169(328)  
#ревью #виселица #оопвиселица #mvc #архитектура 