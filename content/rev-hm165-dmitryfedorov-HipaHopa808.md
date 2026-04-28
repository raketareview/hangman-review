https://github.com/HipaHopa808/HangGame  
[Дмитрий Федоров]

Игра в процедурном стиле, выполнена в нескольких классах.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Нет защиты от дурака
```java
"Виселица"
1 - начать новую игру;
2 - выйти из игры;

ййй
Exception in thread "main" java.lang.NumberFormatException: For input string: "ййй"
	at java.base/java.lang.NumberFormatException.forInputString(NumberFormatException.java:67)
```

2. Игра не запускается
```java
"Виселица"
1 - начать новую игру;
2 - выйти из игры;

1
Файл со словами отсутствует! Игра завершена
"Виселица"
```

3. Можно ввести не только русскую букву, но и буквы других алфавитов
```
Ошибки:й,w,魚
```

4. При проигрыше не показывает слово, которое было загадано
```java
[[], а, р, а, в, а, []]
Ошибки:ф,ы,п,о,ь,т
Для выхода в главное меню введите 1
```

## ХОРОШО

+ 👍 Можно ввести только одиночный символ
+ 👍 Есть список введенных букв
+ 👍 Есть подсказка- две открытых буквы в слове начале игры

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Не дублируй имя класса в названии полей и методов класса
```java
public class MainMenu {
  private final String mainMenuText = //...

  public boolean startMainMenu() {....}
}

//ПРАВИЛЬНО:
public class MainMenu {
  private final String text = //...

  public boolean start() {....}
}
```

- "Геттерами" называют методы, которые возвращают какое-то значение.  
Класс не может быть геттером, потому что класс это не метод
```java
class WordGetter

//ПРАВИЛЬНО:
class Dictionary
```

- Геттер должен что-то возвращать. Геттер, который ничего не возвращает- нонсенс
```java
void getNewWord()
```

- Если метод называется "сравнить"(check), то его результат должен быть boolean.  
Если его результат не boolean, значит он не должен так называться
```java
void checkUserInput()
```

- Хуже венгерской ноттации только венгерская нотация, которая обманывает.  
Эта переменная не принадлежит к типу Character, она принадлежит к типу String
```java
String character
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Если в блоке if есть return(break, continue, throw, exit и т.д.), то else не пишется.**
 
В этом случае неважно, будет else или нет, так как программа будет работать одинаково, а код без `else` будет выглядеть читабельней
```java
if (userChoice == 1) {
  return true;
} else if (userChoice == 2) {
  return false;
} else {
  System.out.println("Введите корректный пункт меню!");
}

//ПРАВИЛЬНО:
if (userChoice == 1) {
  return true;
} 
if (userChoice == 2) {
  return false;
}
System.out.println("Введите корректный пункт меню!");
```

**3. Нарушение DRY**

- Магические буквы, числа, слова. Вводи константы
```java
private final String mainMenuText = """
      "Виселица"
      1 - начать новую игру;
      2 - выйти из игры;
      """;
if (userChoice == 1) {...}
if (userChoice == 2) {...}

//ПРАВИЛЬНО:
private final static int START = 1;
private final static int QUIT = 2;

private final String mainMenuText = """
      "Виселица"
      %d - начать новую игру;
      %d - выйти из игры;
      """.formatted(START, QUIT);

if (userChoice == START) {...}
if (userChoice == QUIT) {...}
```

- Совместная магия.

Если к одной и той же магической штуке обращаются два разных класса, то делай ее константой в одном из этих двух классов.  
Либо перенеси ее в третий класс и из первых двух классов обращайся к этим константам- эти данные должны быть синхронизированы между собой
```java
public class GameScreen {
  public boolean startGameScreen() {
    //...
    System.out.println("Для выхода в главное меню введите 1");  <-- СОВМЕСТНАЯ МАГИЯ
  }
}

public class MainMenu {
  public boolean startMainMenu() {
    if (userChoice == 1) {  <-- СОВМЕСТНАЯ МАГИЯ
      //...
    }
  }
}

public class GameScreen {
  public boolean startGameScreen() {
    //...
    System.out.println("Для выхода в главное меню введите 1");  <-- СОВМЕСТНАЯ МАГИЯ
  }
}
```

*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*

**4. Там, где уместно, используй вечный цикл**
```java
boolean startMainMenu() {
  boolean flag = true;
  while (flag) {
   //...  
    if (userChoice == 1) {
      flag = false;
      return true;
    } else if (userChoice == 2) {
      flag = false;
      return false;
    } 
    return false;
  }
}

//ПРАВИЛЬНО:
boolean startMainMenu() {
  while (true) {
   //...  
    if (userChoice == START) {
      return true;
    } else if (userChoice == QUIT) {
      return false;
    } 
    return false;
  }
}
```

**5. Создание объекта и использование объекта это две разные опреации**
```java
gameState = new MainMenu().startMainMenu();

//ПРАВИЛЬНО:
gameState = new MainMenu();
gameState.startMainMenu();
```

**6. Исключения**

- Не бросай базовый эксепшен. Конкретизируй ситуацию- бросай то исключение, которое подходит под этот конкретный случай
```java
try {
  //... 
} catch (IOException e) {
  throw new RuntimeException(e);
}
```
*Хорстманн "Java. Библиотека профессионала", т1., гл.11*
```java
"Не ограничивайтесь генерацией RuntimeException. 
Найдите подходящий подкласс или создайте собственный." - Хорстманн
```

- Не используй try-catch для организации ветвлений в бизнес логике.

Участки с try-catch изолируй во вспомогательных методах
```java
private void checkUserInput() {
  Scanner input = new Scanner(System.in);
  String userInput = input.nextLine();
  try {
    int userChoice = Integer.parseInt(userInput);
    if (userChoice == 1) {
      gameState = false;
    }
  } catch (NumberFormatException ex) {
    //сложная бизнес логика
  }
}

//ПРАВИЛЬНО:
private void нормальноеНазвание() {
  Scanner input = new Scanner(System.in);
  String userInput = input.nextLine();
  if(isNumber(userInput)) {
    int number = Integer.parseInt(userInput);
    //действия, если юзер ввел цифру
  } else {
    //действия, если юзер ввел не цифру
  }
}

private boolean isNumber(String s) {
  try {
    Integer.parseInt(userInput);
    return true;
  } catch (NumberFormatException ex) {
    return false;
  }
}
```
*"ЧК", гл.3*  
*Хорстманн "Java. Библиотека профессионала", т1., гл.11*
```java
"Обработка исключений не может заменить собой простую проверку" - Хорстманн
```

**7. class WordGetter**

- Проверямые исключения перехватывай в точке их возникновения и заменяй на непроверяемые. 

Проброс исключений в сигнатуре методов захламляет код.  
Поэтому заменяй проверяемые исключения на непроверяемые  
```java
public WordGetter(Path dictPath) throws IOException {
  words = Files.readAllLines(dictPath);
}

//ПРАВИЛЬНО:
public WordGetter(Path dictPath) {
   try {
    words = Files.readAllLines(dictPath);
  } catch (IOException e) {
    throw new НепроверяемоеИсключение(e);
  }
}
```
Изучи различия проверяемых и непроверяемых исключений  
*"ЧК", гл.7, "...проверяемые исключения"*

- В таких случаях можно сразу возвращать значение
```java
String word = words.get(random.nextInt(0, words.size()));
return word;

//ПРАВИЛЬНО:
return words.get(random.nextInt(0, words.size()));
```
**Лайфхак:** если идея что-то подчеркивает желтым(здесь- слово "word"), значи она дает какую-то подсказку.  
Ставишь курсор на подсказку, слева появляется желтая лампочка,
клацаешь по лампочке левой кнопкой мышки и смотришь подсказки.

**8. enum Hangman**

+ 👍 Картинки в отдельном классе это хорошо.  
Это разгружает классы с логикой от графики.

- Некорректное использование енама.

Этот енам в проекте не используется как енам.  
То есть, к его константам не обращаются как к константам.  
Этот енам используется как эрзац-массив.  
Если так, то и сделай его массивом:
```java
public enum Hangman {
  FIRST_STATE("""
        +---+
        |   |
            |
            |
            |
      ========="""),

  SECOND_STATE("""
        +---+
        |   |
        O   |
            |
            |
      ========="""),

  //oths pics

  private String asciiHangman;

  Hangman(String asciiHangman) {
    this.asciiHangman = asciiHangman;
  }

  public String getAsciiHangman() {
    return asciiHangman;
  }
}

//ПРАВИЛЬНО:
public final class HangmanPicture {
  public static final PICTURES = {
    """
        +---+
        |   |
            |
            |
            |
      =========""",
     """
        +---+
        |   |
        O   |
            |
            |
      =========""",
     //oths pics
  };

  //приватный корнструктор
}
```

Тогда и использование картинок будет проще и логичнее:
```java
//БЫЛО:
System.out.println(Hangman.values()[userMarks.size()].getAsciiHangman());

//СТАНЕТ:
System.out.println(Hangman.PICTURES[userMarks.size()]);
```

Но вообще, если бы эта реализация претендует на ООП, то нужно картинки и метод их распечатки вынести в отдельный класс.  
Потому что с точки зрения ООП это составляет единую сущность:
```java
public class HangmanRenderer {
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

  public void render(int pictureNumber) {
    String picture = PICTURES[pictureNumber];  
    System.out.println(picture);
  }
}
```

**9. class GameScreen**

- Нейминг. 

Название не соответствует сути класса.  
Кроме работы с представлением, на что указывает "Screen", класс выполняет бизнес логику, 
которая не зависит от представления(или по крайней мере *не должна* от него зависеть).  
Например: проверяет символ, проверяет состояние игры etc. 

- Нечитаемые конструкции.

Приоритет должен состоять в написании кода, который легко и просто читается, как открытая книга.  
Не экономь строчки в ущерб читаемости.

Вводи вспомогательные  методы:
```java
if (word.contains(character.toLowerCase())) {...}

if (!(wordCurrentState.contains("[]"))) {...}

//ПРАВИЛЬНО:
if (isWordLetter(character)) {...}

if (isНазваниеКотороеВсёОбъясняет()) {...}
```

Ввроди поясняющие переменные:
```java
System.out.println(Hangman.values()[userMarks.size()].getAsciiHangman());

//ПРАВИЛЬНО:
Hangman hangman = Hangman.values()[userMarks.size()];
String picture = hangman.getAsciiHangman();
System.out.println(picture);
```

- Нарушение правила одной операции. 

Этот метод делает какие-то две разные вещи: проверяет состояние игры и печатает сообщение о текущем состоянии игры  
```java
private boolean checkGameState()
```

Этот метод не делает то, что обещает его конракт- он не проверяет символ(в слове) 
```java
void checkChar(String character)
```
Этот метод делает несколько разных вещей на одном уровне абстракции:  
Выполняет одни действия в случае правильно введенной буквы.  
Выполняет другие действия в случае неправильно введенной буквы.

Методы нужно разделить на несколько, каждый из которых будет делать что-то одно.  
*Мартин, "Чистый код", гл.3, "Правило одной операции", "Один уровень абстракции"*

- В целом, код в классе излишне сложный, заумный и запутанный.  

## АРХИТЕКТУРА

Несмотря на то, что в проекте несколько классов, он написан в процедурном стиле.  
Программа в ООП стиле должна быть декомпозирована по правилам ООП и использовать ООП подход для реализации типичных задач.  
Здесь не видно ООП декомпозиции- классы в программе не являются объектами в понимании ООП, а больше похожи на модули процедурного процедурного стиля.

## ВЫВОД

При процедурном стиле, нужно научиться писать простые понятные методы без побочных эффектов и
которые будут соблюдать правило правило одной операции.

Для развития навыков процедурной декомпозиции, посмотреть стрим Сергея  
[Крестики-нолики в процедурном стиле](https://www.youtube.com/watch?v=PPikj1qHxrA)

Сравнить различия между ООП и процедурным стилями:  
Стрим Сергея [Крестики-нолики в процедурном стиле](https://www.youtube.com/watch?v=PPikj1qHxrA)  
Мой стрим [Крестики-нолики в ООП стиле](https://t.me/zhukovsd_it_chat/53243/187097)

Подробное объяснение, как делать эту программу в процедурном и ООП стилях, есть у Сергея в расширенных материалах.

n.165(320)  
#ревью #виселица 