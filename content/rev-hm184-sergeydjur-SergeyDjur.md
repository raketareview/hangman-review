https://github.com/SergeyDjur/HangmanGame  
[Сергей Джур]

Игра в процедурном стиле, состоит из нескольких классов.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Не принимает русские буквы
```java
type a letter
Ё
only russian language supports, type a russian letter
```

2. Нет списка введенных букв 

3. Всё время распечатывается по две одинаковые картинки за раз
```
type a letter
я
---------
|    |
|    O
|   /|\
|   /
|
|________

*****
---------
|    |
|    O
|   /|\
|   /
|
|________

type a letter
```

4. Повторный ввод одной и той же неправильной буквы считается новой ошибкой и увеличивает счетчик ошибок
```java
type a letter
й
---------
|    |
|    O
|   /|
|
|
|________
<ещё такая же картинка>

type a letter
й
---------
|    |
|    O
|   /|\
|
|
|________
<ещё такая же картинка>
```

5. При повторной игре программа вылетает с иксепшеном
```java
  MAKE A CHOICE BETWEEN 1 OR 2
1

type a letter
й

<ИГРАЮ - ИГРАЮ>

you lost
word you tried to guess is баклажан
 MAKE A CHOICE BETWEEN 1 OR 2
1
********
---------
|    |
|    O
|   /|\
|   / \
|
|________

type a letter
й
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: Index 7 out of bounds for length 7
	at HangmanPrinter.printHangmanStage(HangmanPrinter.java:72)
	at WordGuesser.run(WordGuesser.java:59)
```

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву русского алфавита 

## ЗАМЕЧАНИЯ

Так как программа состоит из довольно большого количества классов (шести), то критика будет с точки зрения ООП.

**1. Нейминг**

- Придерживайся единообразия.  

Оба метода делают одно и то же- получают что-то от юзера.  
Поэтому префикс у них должен быть одинаковым.  
Уточнение "FromUser"- избыточно.
```java
public class UserMenu {

  public int askChoice() {
    //получает команду от юзера
  }

  public char getSymbolFromUser() {
    //получает букву от юзера
  }
}

//ПРАВИЛЬНО:
public class UserMenu {

  public int getCommand() {
    //получает команду от юзера
  }

  public char getLetter() {
    //получает букву от юзера
  }
}
```

- В названия не нужно вставлять частицы "Of", "The", "Are" и т.д.

Это только делает названия более громоздкими.  
Если, конечно, там "Of" не используется в контексте valueOf()
```java
public class HangmanPrinter {
  private final String[] stagesOfHangman = {...}
  //...
}

//ПРАВИЛЬНО:
private final String[] hangmanStages
```

Но здесь такие уточнения всё равно избыточны, и правильно будет так:
```java
public class HangmanPrinter {
  private final String[] stages = {...}
  //...
}
```

- Избыточно. В этом классе только один метод распечатки, поэтому уточнение в названии метода тут лишнее
```java
public class HangmanPrinter {
   public void printHangmanStage(int amountMistakes) {...};
}

//ПРАВИЛЬНО:
public class HangmanPrinter {
   public void print(int amountMistakes) {...};
}
```

- Название путает.

Не называй метод "run" если он не переопределяет `Runnable.run()`- название этого метода ассоциируется с этим стандартным интерфейсом и применением потоков.  
Здесь этот метод не имеет отношения к `Runnable.run()`
```java
public class WordGuesser {
  public void run() {...}
  //...
}

//ПРАВИЛЬНО:
start(), go(), execute(), perform()
```

- Это не константа. Константы должны быть `static final`
```java
private final int MAX_AMOUNT_MISTAKES = 6;
```

- В названии переменных не используй предлог "To". 

Предлог "To" используется в методах конвертации: `toList()`, `toString()`, `toInt()`, `meterToFeet()`.   
В названии переменных не используй "To", потому что переменная хранит данные, а не конвертирует их
```java
String wordToGuess = masker.getWord();

//ПРАВИЛЬНО:
String word = masker.getWord();
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Нарушение DRY**

- Магические буквы, числа, слова. Вводи константы 
```java
System.out.println(" MAKE A CHOICE BETWEEN 1 OR 2");
if (userInputChoice != 1 && userInputChoice != 2) {...}
System.out.println("make a choice between '1' or '2'");

//ПРАВИЛЬНО:
private static final int START = 1;
private static final int QUIT = 2;

System.out.printf(" MAKE A CHOICE BETWEEN %d OR %d  \n", START, QUIT);
if (userInputChoice != START && userInputChoice != QUIT) {...}
System.out.printf("make a choice between '%d' or '%d'  \n", START, QUIT);
```

- Совместная магия.

Если к одной и той же магической штуке обращаются два разных класса, то делай ее константой в одном из этих двух классов.  
Либо перенеси ее в третий класс и из первых двух классов обращайся к этим константам- эти данные должны быть синхронизированы между собой
```java
public class GameRunner {

  public void runHangman() {
    //...
    if (choice == 1) {...}
    if (choice == 2) {...}
    {...} while (choice == 1);
  }
  //...
}

public class UserMenu {

  public int askChoice() {
    System.out.println(" MAKE A CHOICE BETWEEN 1 OR 2");
    if (userInputChoice != 1 && userInputChoice != 2) {...}
    System.out.println("make a choice between '1' or '2'");
  }
   //...
}
```

*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*

**3. Если в блоке if есть return(break, continue, throw, exit и т.д.), то else не пишется**

В этом случае неважно, будет else или нет, так как программа будет работать одинаково, а код без else будет выглядеть читабельней
```java
if (symbol >= 'а' && symbol <= 'я' || symbol >= 'А' && symbol <= 'Я') {
  return symbol;
} else {
  System.out.println("only russian language supports, type a russian letter");
}

//ПРАВИЛЬНО:
if (symbol >= 'а' && symbol <= 'я' || symbol >= 'А' && symbol <= 'Я') {
  return symbol;
} 
System.out.println("only russian language supports, type a russian letter");
```

**4. Исключения**

- Проверяемые исключения перехватывай в точке их возникновения и заменяй на непроверяемые. 

Проброс исключений в сигнатуре методов захламляет код.  
Поэтому заменяй проверяемые исключения на непроверяемые  
```java
public String getRandomWordFromFile(String filePath) throws IOException {
  List<String> lines = Files.readAllLines(Paths.get(filePath));
  //...
}

//ПРАВИЛЬНО:
public String getRandomWordFromFile(String filePath) {
  try {
    List<String> lines = Files.readAllLines(Paths.get(filePath));
  } catch (IOException e) {
    throw new КакоетоНепроверяемоеИсключение(e);
  }
  //...
}
```
Изучи различия проверяемых и непроверяемых исключений.

*"ЧК", гл.7, "...проверяемые исключения"*

- Не нужно делать пустой конструктор только для проброса непроверяемых исключений 
```java
public class WordMasker {

  public WordMasker() throws IOException {
  }
  //...
}

//ПРАВИЛЬНО:
public class WordMasker throws IOException{

  <БЕЗ ПУСТОГО КОНСТРУКТОРА>
  //...
}
```
Но вообще, как я писал выше, проверяемые исключения перехватывай в точке их возникновения и заменяй на непроверяемые.

- Не бросай исключения в космос. 

Метод main - последняя точка в программе, после которой исключение аварийно закрывает работу программы.  
Если ты знаешь, что в программе могут возникнуть исключения, то обработай их внутри программы
```java
public class GameRunner {
 
  public static void main(String[] args) throws IOException {...}  <-- Исключение летит в космос
}
```
В крайнем случае хотя бы просто перехвати в `main()`, напечатай сообщение "Критическая ошибка, работа программы будет прекращена" и корректно закрой программу.  
Сообщение без конкретики- не самый лучший вариант.  
Но это лучше, чем если программа вылетит с красными эксепшенами и непонятными сообщениями.

**5. class UserMenu**

- Позиционирование.

В ООП программе каждый класс должен собой представлять какую-то внятную сущность.  

Рассмотрим, что делает этот класс:  
🔹Получает от юзера команду "играть" или "выйти".   
🔹Получает от юзера русскую букву.

Как видим, в этом классе две равнозначные и РАЗНЫЕ ответственности.  
Если класс называется "Меню", то в нем должно быть только меню.

Что такое меню? Это список команд.  

Класс меню можно сделать несколькими разными способами:  
Либо это класс, который просто печатает список команд с их ключами (1- Начать игру, 2- Выход).  
Либо он печатает список команд с их ключами, принимает ключ от юзера и возвращает его клиенту.  
Либо он печатает список команд с их ключами, принимает ключ от юзера и выполняет пункт меню.

Твоё меню, если убрать метод получения буквы, соответствует второму варианту
```java
public class UserMenu {
  Scanner scanner = new Scanner(System.in);


  public int askChoice() {
    System.out.println(" MAKE A CHOICE BETWEEN 1 OR 2");
    //возвращает только 1 или 2 
  }

  public char getSymbolFromUser() {...}  <-- Метод не является частью ответственности меню, убрать
}
```

Меню, то есть метод `askChoice()`, сделан в процедурном стиле.  
Но для простой программы, в которой меню из двух пунктов, это нормально, даже если остальная часть программы будет написана в ООП стиле.

Про меню в ООП стиле я писал [ТУТ](https://t.me/zhukovsd_it_chat/53243/114908)

- При прочих равных, положительные условия читаются лучше отрицательных
```java
while (true) {
  //...
  if (userInputChoice != 1 && userInputChoice != 2) {
    System.out.println("make a choice between '1' or '2'");
    continue;
  }
  return userInputChoice;
}

//ЛУЧШЕ:
while (true) {
  //...
  if (userInputChoice == 1 || userInputChoice == 2) {
    return userInputChoice;
  }
  System.out.println("make a choice between '1' or '2'");
}
```

- Избыточно
```java
if (symbol >= 'а' && symbol <= 'я' || symbol >= 'А' && symbol <= 'Я') {
  return symbol;
} 

//ПРАВИЛЬНО:
symbol = Character.toLowerCase(symbol);
if (symbol >= 'а' && symbol <= 'я') {
  return symbol;
} 
```

**6. class HangmanPrinter**

+ 👍 Кроме нейминга в этом классе почти всё ок.

- Массив картинок `stagesOfHangman` нужно сделать константой.

**7. class RandomWordPicker**

Класс, который выбирает случайное слово из списка. В принципе, почему бы и нет.

- Размер слов для их отбора класс должен принимать извне, а не хардкодить в себе
```java
public class RandomWordPicker {
  private static final int MIN_WORD_LENGTH = 4;
  private final static int MAX_WOR_LENGTH = 8;

  public String getRandomWordFromFile(String filePath) throws IOException {
    //...
    if (word.trim().toLowerCase().length() < MIN_WORD_LENGTH || word.trim().length() > MAX_WOR_LENGTH) {...}
    //...
  }
}

//ПРАВИЛЬНО ТАК:
public class RandomWordPicker {
  private final int minLength;
  private final int maxLength;

  public RandomWordPicker(int minLength, int maxLength) {...}

  public String getRandomWordFromFile(String filePath) {
    //...
    int length = word.length();
    if (length < minLength || length > maxLength) {...}
    //...
  }
}

//ТАК ТОЖЕ ПРАВИЛЬНО:
public class RandomWordPicker {

  public String getRandomWordFromFile(String filePath, int minLength, int maxLength)  {
    //...
    int length = word.length();
    if (length < minLength || length > maxLength) {...}
    //...
  }
}
```

**8. class WordMasker**

- Позиционирование.

Я не вижу, какую сущность представляет этот класс, потому что он делает разное.  
Его название не соответствует тому, что он делает.

От Маскирователя Слова мы ждем просто наличие метода, который принимает в себя слово и возвращает его маску.  
Например, получив слово "парабола", должен вернуть "********". 

Нужен ли вообще для маскировки слова отдельный класс, это дискуссионный вопрос.  

Но сейчас класс совмещает в себе несколько разных ответственностей:  
🔹Читает слово из конкретного источника  
🔹Хранит в себе это слово  
🔹Маскирует это слово  

С точки зрения ООП это класс не является Объектом, потому что он собой не представляет какую-то конкретную сущность с чёткими границами ответственности.  
Он является контейнером функций в процедурном стиле.

- Избыточно
```java
public String getMaskedWord() {
  StringBuilder wordBuilder = new StringBuilder(word.length());
  wordBuilder.append("*".repeat(word.length()));
  return wordBuilder.toString();
}

//ПРАВИЛЬНО:
public String getMaskedWord() {
  return "*".repeat(word.length()));
}
```

**9. class WordGuesser**

Класс реализует основную игровую логику.

- Нарушение инкапсуляции: поля.

Всегда явно указывай область видимости.  Область видимости полей должна быть `private`, если нет причин быть иной
```java
public class WordGuesser {
  WordMasker masker = new WordMasker();
  HangmanPrinter printer = new HangmanPrinter();
  UserMenu menu = new UserMenu();
  //...
}

//ПРАВИЛЬНО:
public class WordGuesser {
  private WordMasker masker = new WordMasker();
  private HangmanPrinter printer = new HangmanPrinter();
  private UserMenu menu = new UserMenu();
  //...
}
```

- Нарушение инкапсуляции: методы.

Публичным здесь должен быть только метод `run()`, потому что только он предназначен для использования клиентами.  
*Вайсфельд "Объектно-ориентированный подход", гл.5, "Минимальный открытый интерфейс"*

- Вводи вспомогательные методы, делай программу более простой и понятной
```java
public void run() {
  while (true) {
    //...
    if (!maskedWord.contains("*")) {
      System.out.println("you win");
      break;
    }
    if (amountMistakes == MAX_AMOUNT_MISTAKES) {
      System.out.println("you lost");
      System.out.println("word you tried to guess is " + wordToGuess);
      break;
    }
  }
}

//ЛУЧШЕ:
public void run() {
  while (!isGameOver()) {
    //...
    if (isWin()) {
      printWinMessage();
    } else if (isLose()) {
      printLoseMessage();
    }
  }
}

private boolean isGameOver() {
  return  isWin() || isLose();
}

private  boolean isLose() {
  return amountMistakes == MAX_AMOUNT_MISTAKES;  
}

private boolean isWin() {...}
```
Даже если метод состоит из одной строки, но при этом делает код программы читабельнее, то этот метод имеет право на жизнь.

- Здесь главный метод `run()`, он должен стоять сразу после конструктора, но перед всеми остальными методами.

+ 👍 В целом методы в классе нормальные: они маленькие, выполняют одну операцию на одном уровне абстракции etc.  

**10. class GameRunner**

Содержит точку входа main.

+ 👍 Только создает и запускает Игру через диалог "Играть или выйти", это хорошо.  
*Мартин, "ЧК", гл.11, "Отделение конструирования системы от ее использования"*

- Метод `main()` должен стоять выше всех остальных методов- точка входа это самое главное.

- Избыточно
```java
public class GameRunner {
  WordGuesser guesser = new WordGuesser();
  UserMenu menu = new UserMenu();

  public GameRunner() throws IOException {
  }

  public void runHangman() {

    int choice;
    do {
      choice = menu.askChoice();
      if (choice == 1) {
        guesser.run();
      }
      if (choice == 2) {
        System.out.println("game is closing");
      }
    } while (choice == 1);

    menu.scanner.close();
  }

  public static void main(String[] args) throws IOException {
    GameRunner runner = new GameRunner();
    runner.runHangman();
  }
}

//ПРАВИЛЬНО:
public class GameRunner {

  public static void main(String[] args) {
    WordGuesser guesser = new WordGuesser();
    UserMenu menu = new UserMenu();

    int start = menu.getStartCommand();
    int quit = menu.getQuitCommand();

    while(true) {
      int command = menu.getCommand();

      if(command == start) {
        guesser.run();
      } else if (command == quit) {
        System.out.println("game is closing");
        return;
      }
    }
  }
}
```

## ВЫВОД

Несмотря на то, что в проекте несколько классов, он написан в процедурном стиле.  
Программа в ООП стиле должна быть декомпозирована по правилам ООП и использовать ООП-подход для реализации типичных задач.  
Здесь не видно ООП декомпозиции.

Только разделения программы на много классов недостаточно для того, чтобы считать программу объектно-ориентированной.

Посмотреть ролики Немчинского про SOLID- по одному ролику на каждый принцип.

Сравнить различия между объектно-ориентированным и процедурным стилями:  
Стрим Сергея [Крестики-нолики в процедурном стиле](https://www.youtube.com/watch?v=PPikj1qHxrA)  
Мой стрим [Крестики-нолики в ООП стиле](https://t.me/zhukovsd_it_chat/53243/187097)

Методы в программе в основном написаны хорошо и соответствуют общим требованиям:  
🔹 Маленький размер  
🔹 Выполняют одну операцию на одном уровне абстракции  
🔹 Не совмещают команду и запрос  
🔹 Не содержат больше трех уровней вложенности  
🔹 Не имеют побочных эффектов

Подробное объяснение, как делать эту программу в процедурном и ООП стилях, есть у Сергея в расширенных материалах.

n.184(355)  
#ревью #виселица 