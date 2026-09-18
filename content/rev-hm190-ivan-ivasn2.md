https://github.com/ivasn2/hangman  
[Иван]

Игра написана в смешанном ООП-процедурном стиле, состоит из нескольких классов.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Нет списка введенных букв 

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву русского алфавита

## ЗАМЕЧАНИЯ

**1. Нейминг**

+ 👍 В целом ок.

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Используй классы через их интерфейсы**
```java
ArrayList<String> words = new ArrayList<>();

//ПРАВИЛЬНО:
List<String> words = new ArrayList<>();
```
Общее правило: ArrayList нужно использовать через List, HashMap через Map, HashSet через Set и т.д.  
Это позволяет пользоваться преимуществами полиморфизма.

Да, бывают ситуации, когда, например, с LinkedList нужно работать именно как с LinkedList, а не с List. 
Но это уже нюансы.  
*"Java. Эффективное программирование", изд.3, гл.9.8*
```java
"Если вы выработаете привычку использовать в качестве типов интерфейсы, ваша программа будет гораздо более гибкой" - Блох.
```

**3. class WordLoader**

Читает слова из файла.

+ 👍 Класс только читает файл и возвращает список слов из него.  
Он не содержит в себе лишних ответственностей.

- Никогда не возвращай null
```java
public static ArrayList<String> wordLoader(String fileName) {
  //...
  return null;
 //...
}
```
Возврат null повышает риск возникновения NullPointerException в программе.

*Мартин, "Чистый код", гл.7.7-8*  
*Ютуб, Немчинский "Почему нельзя возвращать NULL?"*

- Соблюдай требования утилитных классов. 

Это утилитный класс, он содержит только статические методы.  
Утилитные классы должны быть `final` и иметь приватный конструктор.  
Не должно быть возможности унаследоваться от утилиты или сделать ее экземпляр.

*Блох, "Java. Эффективное программирование", изд.3, гл.2.4*

+ 👍 При ошибке чтения файла программа корректно завершает свою работу без вываливания красных эксепшенов в консоль
```java
Ошибка при чтении файла: words.txt_fuck (Не удается найти указанный файл)
Ошибка при чтении файла
```

- Модель не должна ничего печатать в консоль.

Если мы рассматриваем классы с точки зрения ООП, то класс файлового чтения это модель.  
Классы-модели не должны ничего печатать в консоль или куда-то еще.  
Потому что модели должны одинаково работать в любой визуальной среде(консоль, Windows UI, Android, аркадный автомат с лампочками).

При ошибке чтения файла, метод должен просто кинуть непроверяемое исключение 
```java
public static ArrayList<String> wordLoader(String fileName) {
  try (BufferedReader br = new BufferedReader(new FileReader(fileName))) {
    //читает файл в список
  } catch (IOException e) {
    System.out.println("Ошибка при чтении файла: " + e.getMessage());
    return null;
  }
  return words;
}

//ПРАВИЛЬНО:
public static ArrayList<String> wordLoader(String fileName) {
  try (BufferedReader br = new BufferedReader(new FileReader(fileName))) {
    //читает файл в список
  } catch (IOException e) {
    throw new ПодходящийRuntimeException("file read error: " + fileName);  
  }
  return words;
}
```

**4. class InputValidator**

+ 👍 Класс только получает от юзера русскую букву.  
Он не содержит в себе лишних ответственностей.

- Нейминг.

Это не валидатор- он ничего не валидирует.  
Это инпутер- он принимает от юзера русскую букву
```java
public class InputValidator {
  public static String validateInput(Scanner input, String errorMessage, boolean requireRussian) {...}
}

//ПРАВИЛЬНО:
public class RusLetterInput {
  public static String get(Scanner scanner, String errorMessage, boolean requireRussian) {...}
}
```

- Не используй аргументы-флаги
```java
public static String validateInput(..., boolean requireRussian) { 
  //...  
  if (requireRussian && !value.matches("[А-Яа-яЁё]")) {  // requireRussian - флаг того, что нужно получить рус. букву
    System.out.println("Введите русскую букву");
    value = input.nextLine().toLowerCase();
  }
  //...
}
```
*Мартин "ЧК", гл.3, "Аргументы-флаги"*
```
"Аргументы-флаги уродливы... функция выполняет более одной операции" - Мартин.
```

Если предполагаешь на будущее, что в игре могут понадобиться другие языки и для букв на этих языках нужно организовать дополнительный ввод,
то используй ООП-подход - на каждый язык делай отдельный инпутер букв:
```java
public interface LetterInput {
  String get(Scanner scanner, String errorMessage);
}

public class RusLetterInput implements LetterInput {

  @Override  
  public String get(Scanner scanner, String errorMessage) {
    //Возвращает русскую букву
  }
}

public class TibetanLetterInput implements LetterInput {

  @Override
  public String get(Scanner scanner, String errorMessage) {
    //Возвращает тибетскую букву
  }
}
```

- Компилируй регулярные выражения. 

Если регулярное выражение используется многократно, как здесь, то нужно его откомпилировать.  
Компиляция регулярного выражения в объект `Pattern` с помощью `Pattern.compile()` позволяет повысить производительность при многократном использовании
```java
if (requireRussian && !value.matches("[А-Яа-яЁё]")) {...}

//ПРАВИЛЬНО:
private static final String REGEX = "[А-Яа-яЁё]";
private final Pattern pattern = Pattern.compile(REGEX);

//...
Matcher matcher = pattern.matcher(value);
if (matcher.matches()) {...}
```

```java
"Хотя String.matches — простейший способ проверки, соответствует ли строка регулярному выражению, 
он не подходит для многократного использования в ситуациях, критичных в смысле производительности." - Блох
```
Здесь, конечно, нет критической ситуации с производительностью.  
Но здесь нет и необходимости просто так использовать неоптимальный алгоритм при работе с регулярными выражениями.  
*Блох "Java. Эффективное программирование", изд.3, гл.2.6*

**5. class HangmanRenderer**

+ 👍 Хорошо, что картинки вынесены в отдельный класс. Это разгружает классы с логикой от графики.

- Нейминг.

Этот класс не рендерер- потому что он ничего не печатает. Это что-то типа `PictureStorage`:
```java
public class HangmanRenderer {
  private static final String[][] HANGMAN_STAGES = { /* картинки */};

  public static String drawHangman(int mistakes) {  <-- Этот метод ничего не печатает
    return String.join("\n", HANGMAN_STAGES[mistakes]);
  }
}

//ПРАВИЛЬНО:
public class PictureStorage {
  private static final String[][] HANGMAN_STAGES = { /* картинки */};

  public static String get(int mistakes) {  <-- Этот метод ничего не печатает
    return String.join("\n", HANGMAN_STAGES[mistakes]);
  }
}
```

- Избыточно.

Просто используй многострочные строки
```java
private static final String[][] HANGMAN_STAGES = {
  {
    " ___   ",
    "|   |   ",
    "|   O   ",
    "|  (|)   ",
    "|  //    ",
     "======== ",
  },
  //oths pic
};

public static String drawHangman(int mistakes) {
  return String.join("\n", HANGMAN_STAGES[mistakes]);
}

//ПРАВИЛЬНО:
private static final String[] HANGMAN_STAGES = {
    """
   ___   
  |   |   
  |   O   
  |  (|)   
  |  //    
  ======== 
  """,
  //oths pic
};

public static String drawHangman(int mistakes) {
  return HANGMAN_STAGES[mistakes];
}
```

- Лучше сделать этот класс не хранилищем картинок, а рендерером.  
То есть, чтобы он не выдавал картинки, а печатал их
```java
public class HangmanRenderer {
  private static final String[][] HANGMAN_STAGES = {
    {
      " ___   ",
      "|   |   ",
      "|   O   ",
      "|  (|)   ",
      "|  //    ",
       "======== ",
    },
    //oths pic
  };

  public static String drawHangman(int mistakes) {
    return String.join("\n", HANGMAN_STAGES[mistakes]);
  }
}

//ЛУЧШЕ:
public class HangmanRenderer {

  private static final String[] HANGMAN_STAGES = {
    """
   ___   
  |   |   
  |   O   
  |  (|)   
  |  //    
  ======== 
  """,
  //oths pic
  };

  public static void render(int mistakes) {
    String stage = HANGMAN_STAGES[mistakes];
    System.out.println(stage);
  }
}
```

**6. class HangmanGame**

- Возврат текстов из этого метода выглядит оч.странно
```java
public String processLetter(String letter) {
  if (guessedLetters.contains(letter.charAt(0))) {
    return "Вы уже вводили эту букву\n---------------------------";
  } else if (word.contains(letter)) {
    guessedLetters.add(letter.charAt(0));
  } else if (incorrectLetters.contains(letter.charAt(0))) {
    return "Вы уже вводили эту букву\n---------------------------";
  } else if(...) {...}

  return "";
}
```

Если имеется ввиду, что класс `HangmanGame` это что-то типа движка игры, который должен содержать 
только общую игровую логику и ничего не должен распечатывать на экран, то тогда тут нужно сделать иначе.  

Нужно результат выполнения хода сообщать в виде чистых данных, а не в виде текстовых сообщений для юзера.  
Например, так:
```java
public Result processLetter(String letter) {
  if (guessedLetters.contains(letter.charAt(0))) {
    return Result.LETTER_ALREADY_ENTERED;

  } else if (word.contains(letter)) {
    guessedLetters.add(letter.charAt(0));

  } else if (incorrectLetters.contains(letter.charAt(0))) {
    return Result.LETTER_ALREADY_ENTERED;

  } else if(...) {...}

  return Result.OK;
}

public enum Result {
  OK, LETTER_ALREADY_ENTERED, ...;    
}

letter has already been entered
```

**7. class Main**

Содержит точку входа main.

- Нарушение SRP.

Main должен только сконфигурировать зависимости и запустить программу.  
Управлять работой программы этот класс не должен.  
В Main'e возможен только диалог "Играть или выйти"- это все еще относится к ответственности конструирования и запуска системы.

Если имеется ввиду, что должен быть отдельный класс-движок `HangmanGame` и какой-то класс, который будем управлять этим движком, 
то логику управления движком нужно вынести в отдельный класс-контроллер. 
А в Майне оставить только конструирование и запуск системы.  
Примерно так:
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
      GameController controller = new GameController(game);
      controller.start();

    } else if(command.equalsIgnoreCase(QUIT)) {
      return;  
    } else {
      //неправильная команда  
    }
    
  }

  private static String inputCommand() {...}
}
```

*Мартин, "ЧК", гл.11, "Отделение конструирования системы от ее использования"*

- Нарушение SRP. Метод не должен завершать работу программы через exit()
```java
public static void main(String[] args) {
  //...
  System.exit(1);
  //...
}
```
Каждый метод и класс имеют право завершать только свою работу. Например, через return.  
Потому что методы и классы не должны знать логику работы более высоких слоев программы, у которых могут быть свои планы на тему того, когда и почему нужно завершать работу программы.

Кроме того, при выходе через `exit()` могут не закрыться некоторые ресурсы программы.

- Нарушение DRY.

Магические буквы, числа, слова. Вводи константы 
```java
System.out.println("[N]ew game or [E]xit ?");
String userAnswer = InputValidator.validateInput(input, "Введите n или e", false);
if (userAnswer.equalsIgnoreCase("N")) {...}

//ПРАВИЛЬНО:
private static final String START = "N";
private static final String EXIT = "E";

private static final String INPUT_MESSAGE = "Введите '%c' или '%c'".formatted(START, EXIT);

System.out.printf("[%s]ew game or [%s]xit ?  \n", START, EXIT);
String userAnswer = InputValidator.validateInput(input, INPUT_MESSAGE);
if (userAnswer.equalsIgnoreCase(START)) {...}
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru, "Замена магического числа символьной константой"*

## ВЫВОД

В целом норм.

Если планировался объектно-ориентированный стиль, то программа в ООП стиле должна быть декомпозирована по правилам ООП и использовать ООП-подход для реализации типичных задач.  
Эта реализация имеет признаки и процедурного и объектно-ориентированного стилей.

Признаки ООП:  
🔹 Программа разделена на классы, большинство из которых описывают отдельную сущность: движок игры, загрузчик слов и т.д.  

Признаки процедурного стиля:  
🔸 Не выявлены и не сделаны в виде классов менее очевидные, чем файловый загрузчик слов, сущности. Например, "Секретное слово". 
Значит нет системного понимания объектно-ориентированной декомпозиции.

🔸 Процедурные подходы при создании классов. Например, флаг в инпутере вместо полиморфизма.  
🔸 Main, который не просто собирает и запускает игру, а управляет процессом игры.

Посмотреть ролики Немчинского про SOLID- по одному ролику на каждый принцип.

Подробное объяснение, как делать эту программу в процедурном и ООП стилях, есть у Сергея в расширенных материалах.

n.190(362)  
#ревью #виселица 