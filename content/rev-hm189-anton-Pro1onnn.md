https://github.com/Pro1onnn/Project1  
[Anton]

Игра написана в процедурном стиле. Состоит из нескольких классов.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Необоснованные ограничения
```
Вводите по одной букве на кириллице в нижнем регистре за раз:
Я
Вы ввели букву НЕ на кириллице или НЕ в нижнем регистре или вы ввели НЕ букву вовсе
```

Переводить букву в нужный тебе регистр- это твоя проблема.  
Юзер должен быть волен вводить буквы в любом регистре. 

2. Команды не работают.

Я ввёл одиночные буквы на кириллице в нижнем регистре, но их не приняли:
```
ў
Вы ввели букву НЕ на кириллице или НЕ в нижнем регистре или вы ввели НЕ букву вовсе
џ
Вы ввели букву НЕ на кириллице или НЕ в нижнем регистре или вы ввели НЕ букву вовсе
њ
Вы ввели букву НЕ на кириллице или НЕ в нижнем регистре или вы ввели НЕ букву вовсе
```

3. Нет списка введенных букв 

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву русского алфавита

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Не сокращай слова без необходимости. Экономия двух букв необходимостью не является
```java
Random rand

//ПРАВИЛЬНО:
Random random
```

- Название этой переменной читается как "установить доступные символы"
```java
Set<Character> setAccessibleChars
```
Что является нонсенсом- название переменных должны быть существительными, а не глаголами.

- Не используй в названиях методов зарезервированные слова типа "return", "try", "throw" etc.  
Если что-то нужно вернуть, то это "get" или "receive"  
```java
String returnsRandomWordFromList()

//ПРАВИЛЬНО:
String getRandomWordFromList()
```

- Названия методов должны быть глаголами. Эти названия- существительные
```java
static void userWin()
void charMaskInDisplay(char[] mask)

//и все остальные методы в MessageUtil
```

- Хуже венгерской нотации только венгерская нотация, которая обманывает. 

Здесь в названии переменной имеется слово "char", но эта переменная не принадлежит к типу `char`, это массив
```java
char[] charMask

//ПРАВИЛЬНО:
char[] maskLetters
```
Не пиши в названии переменных тип данных- это венгерская нотация и это плохо.

- Если метод называется "проверить" (check), то результат этой проверки должен быть boolean.  
Если метод не возвращает boolean, значит он не выполняет проверку и не должен так называться
```java
void checkCharInWord(char[] charUser, char[] charMask)
```

- Название обманывает.  

Этот метод не проверяет букву, он получает букву от юзера
```java
char checkCharInputUser()

//ПРАВИЛЬНО:
char inputLetter()
```

- Не дублируй имя класса в названиях метода класса
```java
public class Game {

  public void gameStart() {...}
  //....
}

//ПРАВИЛЬНО:
public class Game {

  public void start() {...}
  //....
}
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Нарушение DRY**, магические буквы, числа, слова. Вводи константы. 

- Совместная магия.

Если к одной и той же магической штуке обращаются два разных класса, то делай ее константой в одном из этих двух классов и потом бери их оттуда.  
Либо перенеси ее в третий класс и из первых двух классов обращайся к этим константам- эти данные должны быть синхронизированы между собой
```java
public class MessageUtil {

  public static void printInfoAboutStartStopGame() {
    System.out.println("Нажмите " + 1 + " чтобы продолжить.");
    System.out.println("Нажмите " + 0 + " чтобы выйти.");
  }
  //...
}

public class Game {
  private static final int MAX_ERROR_COUNT = 6;
  private static final String USER_INPUT_VALUE_FOR_START = "1";
  private static final String USER_INPUT_VALUE_FOR_STOP = "0";
  //...
}
```

Тут тоже
```java
public class Words {
  
  //...
  public char[] getMaskWord() {
    return word.replaceAll("[а-яё]", "*").toCharArray();
  }
}

public class Game {
  
  //...
  public void checkCharInWord(char[] charUser, char[] charMask) {
    while (counterError < MAX_ERROR_COUNT && new String(charMask).contains("*")) {...}
  }
}
```

*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*

**3. Форматирование**

- Форматирование строк.

Если нужно печатать или создавать строку с более чем одним подстановочным значением или значение вставляется внутрь сообщения, 
используй форматирование- тогда сразу будет виден весь шаблон
```java
System.out.println("Нажмите " + 1 + " чтобы продолжить.");
System.out.println("Буква: " + result + " есть в этом слове.");  //char result

//ПРАВИЛЬНО:
System.out.printf("Нажмите %d чтобы продолжить.  \n", START);
System.out.printf("Буква: %c есть в этом слове.  \n", result);
```

**4. class Words**

- Ответственность класса.

В этом классе собраны разные ответственности- от загрузки слов из файла до проверки буквы на соответствие русскому алфавиту.  
Поэтому с точки зрения ООП этот класс не является объектом. Он является контейнером функций в стиле процедурного программирования.

Если и планировался процедурный стиль, то пойдёт.  

Если планировалось ООП, то нужно методы из этого класса разнести по другим классам в соответствии с SRP.

Например, ответственность загрузки слов из файла и выдачу случайного слова нужно перенести в класс Словаря.  
Типа такого:
```java
public class Dictionary {
  private final List<String> words;


  public Dictionary(String filepath) {
    //читает слова из файла и записывает в words
  }

  public getRandomWord() {
    //возвращает случайное слово из words
  }
}
```
Кроме этого функционала в Словаре не должно быть других методов, иначе он перестанет быть внятной объектно-ориентированной сущностью. 

- Если в проекте есть класс `Words`, то все переменные с именем, включающим это название, должны быть экземплярами этого класса.  

Когда разные концепции называются одним и тем же именем, это приводит к путанице
```java
public class Words {
  private List<String> words = new ArrayList<>();
  //...
}

//ПРАВИЛЬНО:
public class WordManager {
  private List<String> words = new ArrayList<>();
  //...
}
```

- Путь к файлу класс должен получать в конструктор. 

Иначе класс становится не универсальным.  
Допустим, в программе будет несколько разных текстовых файлов- на разных языках или по разным темам.  
Тогда вместо нескольких специализированных классов Словаря, где имя файла словаря будет жестко прописано в каждом из классов, 
можно будет использовать один универсальный класс.

- В конструкторе не должно быть реализовано много сложной логики.  
Если при создании экземпляра нужно выполнить много действий- перенеси их во вспомогательный метод
```java
public class Words {
  private List<String> words = new ArrayList<>();

  public Words() {
    String line;
    try (BufferedReader br = new BufferedReader(new FileReader(PATH_FILE))) {
      while ((line = br.readLine()) != null) {
        words.add(line);
      }
    } catch (FileNotFoundException e) {
      throw new RuntimeException(e);
    } catch (IOException e) {
      throw new RuntimeException(e);
    }
  }
  //...
}

//ПРАВИЛЬНО:
public class WordManager {
  private List<String> words = new ArrayList<>();

  public WordManager() {
    initWords();
  }
  //...
}
```

- Не бросай базовый exception. Конкретизируй ситуацию- бросай то исключение, которое подходит под этот конкретный случай
```java
try (BufferedReader br = new BufferedReader(new FileReader(PATH_FILE))) {
  //...
} catch (FileNotFoundException e) {
  throw new RuntimeException(e);
} catch (IOException e) {
  throw new RuntimeException(e);
}
```
*Хорстманн "Java. Библиотека профессионала", т.1, гл.11*
```java
"Не ограничивайтесь генерацией RuntimeException. 
Найдите подходящий подкласс или создайте собственный." - Хорстманн
```

- Избыточно
```java
public char[] getMaskWord() {
  return word.replaceAll("[а-яё]", "*").toCharArray();
}

//ПРАВИЛЬНО:
public char[] getMaskWord() {
  return "*".repeat(word.length());
}
```

**5. class MessageUtil**

Класс печатает сообщения.

+ 👍 Вынос методов печати всех сообщений в отдельный класс это хорошо.
Таким образом, общая игровая логика отделяется от представления.

+ 👍 Методы печати в этом классе хорошие- они просто печатают информацию и не содержат частей общей игровой логики.

- Соблюдай требования утилитных классов. 

Это утилитный класс, он содержит только статические методы.  
Утилитные классы должны быть `final` и иметь приватный конструктор.  
Не должно быть возможности унаследоваться от утилиты или сделать ее экземпляр.

*Блох, "Java. Эффективное программирование", изд.3, гл.2.4*

- Сложная логика формирования и печати картинок виселицы.

Метод печати виселицы `gallowsInDisplay(...)` это ацкий ад 
```java
public static void gallowsInDisplay(int errorCounter) {
  StringBuilder sb = new StringBuilder();
  sb.append("--------\n");
  sb.append("|      |\n");
  sb.append("|      ").append(errorCounter >= 1 ? "o\n" : "\n");
  //ещё куча кода
  sb.append("|     ").append(errorCounter >= 6 ? "/ \\\n" : (errorCounter >= 5 ? "  \\\n" : "\n"));
  sb.append("|\n");
  System.out.println(sb);
}
```
Этот код невозможно понять и при необходимости модифицировать.  
Например, если понадобиться вместо 6 стадий повешения сделать 5 или 7.

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

  public static void printHangmanPicture(int pictureNumber) {
    String picture = PICTURES[pictureNumber];  
    System.out.println(picture);
  }
}
```

**6. class Game**

- Ответственность класса.

Опять же, в каком стиле писалась эта программа?  
Если в процедурном, то класс пойдёт- это просто контейнер с функциями, который внутри себя может работать как угодно.

Если имеется ввиду ООП, то класс нарушает SRP.  
Тогда диалог "Играть или выйти" должен находиться не здесь, а на уровень выше- в классе Main.  
Сам класс при этом не должен внутри себя генерировать слово для игры, а должен получать его в конструктор.  
Примерно так:
```java
public class Game {
  private final String word;

  public Game(String word) {
    this.word = word;
  }
  //...
}
```

- Создавай вспомогательные методы, делай программу более простой и понятной
```java
while (counterError < MAX_ERROR_COUNT && new String(charMask).contains("*")) {
  //...
}
if (counterError == MAX_ERROR_COUNT) {
  MessageUtil.userLose(charUser);
} else {
  MessageUtil.userWin();
}

//ПРАВИЛЬНО:
while (!isGameOver()) {
  //...
}
if (isLose()) {
  MessageUtil.userLose(charUser);
} else {
  MessageUtil.userWin();
}

private boolean isGameOver() {
  return  isWin() || isLose();
}

private boolean isLose() {
  return counterError == MAX_ERROR_COUNT;
}

private boolean isWin() {...}
```
Даже если метод состоит из одной строки, но при этом делает код программы читабельнее, то этот метод имеет право на жизнь.

- Избыточно
```java
if (str.matches("[а-яё]")) {
  char ch = str.charAt(0);
  return ch;
}

//ПРАВИЛЬНО:
if (str.matches("[а-яё]")) {
  return str.charAt(0);
}
```

- Большой метод. 

Большие методы разделяй на маленькие методы, каждый из которых должен делать одно дело на одном уровне абстракции.  

Например, смотрим на большой метод и находим в нем отдельные смысловые блоки
```java
public void checkCharInWord(char[] charUser, char[] charMask) {
  //куча кода
  
  for (int i = 0; i < charUser.length; i++) {
    if (charUser[i] == charInputUser) {
      isFound = true;
      charMask[i] = charInputUser;
    }
  }
  
  if (isFound) {
    MessageUtil.correctCharInWord(charInputUser);
  } else {
    counterError++;
    //печать сообщений что буквы нет в слове
  }
  
  //куча кода
}
```

Что делает этот кусок кода? Он открывает букву в маске и выставляет флаг, если буква есть в слове.  
Выносим этот код во вспомогательные методы:
```java
public void checkCharInWord(char[] charUser, char[] charMask) {
  //куча кода

  if(isWordLetter(charInputUser)) {
    openLetter(charInputUser);
    MessageUtil.correctCharInWord(charInputUser);
  } else {
    counterError++;
    //печать сообщений что буквы нет в слове
  }

  //куча кода
}

private void openLetter(char letter) {
  for (int i = 0; i < charUser.length; i++) {
    if (charUser[i] == letter) {
      charMask[i] = letter;
    }
  } 
}
```
*"ЧК", гл.3, "Правило одной операции", "Один уровень абстракции"*

**7. class Main**, содержит точку входа main

+ 👍 Только создает и запускает Игру, это хорошо.

*Мартин, "ЧК", гл.11, "Отделение конструирования системы от ее использования"*

- Диалог "Играть или выйти" должен происходить здесь, а не в `Game`.

- Используй Dependency injection.

Формируй необходимые зависимости в мейне и инжекть их дальше. 

Это позволить делать разные Main-конфигурации, не меняя код в остальных классах.  
Например первая конфигурация:
```java
public class Main {
  public static void main(String[] args) {
    
    Dictionary dictionary = new Dictionary("words.txt");
    String word = dictionary.getRandomWord();

    Game game = new Game(word);
    game.start();
  }
}
```

Вторая конфигурация с заранее заданным словом, для тестирования работы алгоритма:
```java
public class TestMain {
  public static void main(String[] args) {

    Game game = new Game("парабола");
    game.start();
  }
}
```

Третья конфигурация- через диалог "Играть или выйти":
```java
public class Main {

  private static final String START = "s";
  private static final String QUIT = "q";

  public static void main(String[] args) {
    
    Dictionary dictionary = new Dictionary("words.txt");

    String command = inputCommand();

    if(command.equalsIgnoreCase(START)) {
    
      String word = dictionary.getRandomWord();
      Game game = new Game(word);
      game.start();

    } else if(command.equalsIgnoreCase(QUIT)) {
      return;  
    } else {
      //неправильная команда  
    }
    
  }

  private static String inputCommand() {...}
}
```

## ВЫВОД

Несмотря на то, что в проекте несколько классов, он написан в процедурном стиле.  
Программа в ООП стиле должна быть декомпозирована по правилам ООП и использовать ООП-подход для реализации типичных задач.  
Здесь не видно ООП декомпозиции.

Посмотри на ютубе ролики Немчинского для новичков:
```
"Правильные методы по Clean Code"
"Как называть переменные, методы и классы? Чистый код (Clean Code)"
"Принцип хорошего кода KISS"
"SOLID принципы: SRP", "SOLID принципы: OCP" и т.д. 
```

Сравнить различия между объектно-ориентированным и процедурным стилями:  
Стрим Сергея [Крестики-нолики в процедурном стиле](https://www.youtube.com/watch?v=PPikj1qHxrA)  
Мой стрим [Крестики-нолики в ООП стиле](https://t.me/zhukovsd_it_chat/53243/187097)

Подробное объяснение, как делать эту программу в процедурном и ООП стилях, есть у Сергея в расширенных материалах.

n.360(189)  
#ревью #виселица 