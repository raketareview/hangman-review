https://github.com/rustamizteleu/VisilicaGame  
[Rustam]

Игра в ООП стиле, но с очень простой декомпозицией.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

**1. Недружественный интерфейс**
```java
Введите букву: Й
Игра принимает только маленькие буквы кириллицы
```
Переводить буквы в нужный регистр это твои проблемы, как разработчика.  
Юзер должен иметь возможность вводить буквы любого размера.

**2. Я ввёл маленькую букву кириллицы, её не приняло**
```java
Введите букву:  ї 
Игра принимает только маленькие буквы кириллицы
```
Русские буквы и буквы кириллицы это не всегда одно и то же.

**3. Русские маленькие буквы тоже не принимает**
```java
Введите букву: ё
Игра принимает только маленькие буквы кириллицы
```

**4. Из интерфейса торчат какие-то внутренности**
```java
Введите букву: у
DEBUG: letter='у' result=WRONG attemptsLeft=3
```
Юзер не должен видеть твою отладочную информацию.

**5. Нет списка введенных букв.**

**6. Вешается одноногий**
```java
  +---+
  |   |
  O   |
 -|-  |
 (    |
      |
=========
Слово: _ _ а _ _ а
Осталось попыток: 1
Введите букву: п
DEBUG: letter='п' result=WRONG attemptsLeft=0
Неверно!

Вы проиграли! Слово было: сказка
```

## ХОРОШО

+ 👍 Игра запускается. Хотя это здесь ни о чем не говорит- слова не читаются из файла, а хранятся в памяти 
+ 👍 Можно ввести только одиночную букву русского алфавита

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Никогда не пиши названия транслитом. 

Названия должны состоять из слов английского языка.  
Эта игра называется Hangman
```java
VisilicaGame

//ПРАВИЛЬНО:
HangmanGame
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Exceptions**

- Текст в exception всегда должен быть на английском языке.
```java
throw new IllegalArgumentException("Игра принимает только маленькие буквы кириллицы");
```
Исключение это не просто телеграмма, которая летит сквозь слои.

У exception особое назначение- если исключение вылетит и не будет перехвачено внутри программы, то аварийно прекратит выполнение программы.  
Тогда на экране будет распечатано сообщение эксепшена, и это сообщение должно быть понятно сисадмину в любой точке планеты.  
А значит, сообщение должно быть на английском.

Интерпретация исключения и перевод его на локальный язык должно происходить там, где это соответствует архитектуре программы.  
Или не происходить вовсе, если исключение не планируется перехватывать.

**3. Форматирование строк**

Если нужно печатать или создавать строку с более, чем одним подстановочным значением или значение вставляется внутрь сообщения, 
используй форматирование- тогда сразу будет виден весь шаблон
```java
System.out.println("DEBUG: letter='" + letter + "' result=" + result + " attemptsLeft=" + game.getAttemptsLeft());

//ПРАВИЛЬНО:
System.out.printf("DEBUG: letter='%c' result=%s attemptsLeft=%d  %n", letter, result, game.getAttemptsLeft());
```

**4. class Display**

+ 👍 Хорошо, что картинки и метод их печати вынесены в отдельный класс. Это разгружает классы с логикой от графики.

- В любом switch-case должен быть default.  
В данном случае default должен бросать исключение с сообщением, что такого номера картинки не существует.

- Печать картинки виселицы через switch-case или if-elseif - индусский код.  
Картинки нужно хранить в статическом массиве и печатать по номеру.  
Например, так
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

  public void render(int numPicture) {
    String picture = PICTURES[numPicture];
    System.out.println(picture);
  }
}
```

**5. class WordChoice**

Это словарь слов.  Слова здесь не читаются из файла, а просто хранятся в листе
```java
public class WordChoice {
  public static String getWord() {
    List<String> list = new ArrayList<String>(Arrays.asList("телефон", ...));
    int randomIndex = (int) (Math.random() * list.size());
    return list.get(randomIndex);
  }
}
```

Это довольно слабо.  
Слова нужно хранить в текстовом файле, а в игре считывать слова из файла- это позволяет потренировать навыки работы с файлами.

**6. class GameLogic**, общая игровая логика

+ 👍 Что-то типа движка игры. Движок это хорошо.

Движок это вся игровая логика без взаимодействия с представлением.  
Игровая логика с взаимодействием с представлением в таких случаях выносится в отдельный контроллер.  

Этот класс можно считать движком. 
А его контроллером с некоторыми натяжками можно считать `class Main`. 

- Нарушение DI.

Класс должен принимать в конструктор необходимые зависимости
```java
public class GameLogic {
  private String word;
  private List<Character> guessedLetters;
  private int attemptsLeft;

  public GameLogic() {
    this.word = WordChoice.getWord();
    this.guessedLetters = new ArrayList<>();
    this.attemptsLeft = 6;
  }
  //...
}

//ПРАВИЛЬНО:
public class GameLogic {
  private static final MAX_ATTEMPTS = 6;

  private String word;
  private List<Character> guessedLetters;
  private int attemptsLeft;

  public GameLogic(String word) {
    this.word = word;
    this.guessedLetters = new ArrayList<>();
    this.attemptsLeft = MAX_ATTEMPTS;
  }
  //...
}
```

Это сделает класс более универсальным.  
Захочешь, запустишь его со словом из словаря:
```java
public class Main {

  public static void main(String[] args) {
    String word = WordChoice.getWord();

    GameLogic game = new GameLogic(word);
    game.start();
  }
}
```

А захочешь, запустишь его сразу с заданным словом
```java
public class Main {

  public static void main(String[] args) {
    GameLogic game = new GameLogic("парабола");
    game.start();
  }
}
```

+ 👍 В целом класс норм.

В нем маленькие понятные методы и нет работы с представлением.  
То есть, он не печатает информацию в консоль.

На мой взгляд, `enum GuessResult` здесь лишний и без него было бы лучше, но это не так уж принципиально.

**7. class Main**, содержит точку входа main()

- Нарушение SRP.

Main должен только сконфигурировать зависимости и запустить программу.  
Управлять работой программы этот класс не должен.  
В Main'e возможен только диалог "Играть или выйти"- это все еще относиться к ответственности конструирования и запуска системы.

В простейшем виде c движком, контроллером и однократной игрой класс `Main` должен выглядеть примерно так:
```java
public class FirstMain {

  public static void main(String[] args) {
    String word = WordChoice.getWord();

    GameLogic game = new GameLogic(word);
    GameController controller = new GameController(game);
    controller.start();
  }
}
```

С многократной игрой через диалог "Играть или выйти" класс `Main` должен выглядеть примерно так:
```java
public class SecondMain {
  //...  

  public static void main(String[] args) {

    while(true) {
      String command = inputCommand();

      if(isStart(command)) {
        String word = WordChoice.getWord();
        GameLogic game = new GameLogic(word);

        GameController controller = new GameController(game);
        controller.start();
      }   
      //...
    }
  }

  private static String inputCommand() {
    //получение команды от юзера: старт/стоп
  }

  private static boolean isStart(String command) {
    return START.equalsIgnoreCase(command);
  }
}
```

Весь остальной код, который управляет работой движка и зависит от представления(печатает в консоль), нужно из текущего `Main` вынести в новый класс Контроллер Игры.

*Мартин, "ЧК", гл.11, "Отделение конструирования системы от ее использования"*

- Большой божественный метод `void main() `.

Метод нужно разделить на несколько, которые будут соответствовать этим критериям:  
🔹 Маленький размер  
🔹 Выполняют одну операцию на одном уровне абстракции  
🔹 Не совмещают команду и запрос  
🔹 Не содержат больше трех уровней вложенности  

Подробнее про методы: 3 глава "ЧК" и ролик Немчинского "Правильные методы по Clean Code" на ютубе.

- Дублирование кода
```java
switch (result) {
  case CORRECT -> System.out.println("Верно!\n");
  case WRONG -> System.out.println("Неверно!\n");
  case ALREADY_USED -> System.out.println("Эта буква уже была введена, попробуйте другую.\n");
}

//ПРАВИЛЬНО:
switch (result) {
  case CORRECT -> System.out.println("Верно!");
  case WRONG -> System.out.println("Неверно!");
  case ALREADY_USED -> System.out.println("Эта буква уже была введена, попробуйте другую.");
}
System.out.println();
```

- Вводи вспомогательные методы для лучшей читаемости кода
```java
while (!game.isWon() && !game.isLost()) {...}

//ПРАВИЛЬНО:
while (!isGameOver()) {...}

private boolean isGameOver() {
  return game.isWon() || game.isLost();
}
```

## АРХИТЕКТУРА

Я бы сказал, что программа написана скорее в ООП стиле, чем в процедурном.  

Но декомпозиция классов здесь очень простая.  
Не выявлены и не сделаны в виде классов менее очевидные, чем Словарь и Рендерер, сущности.  
Например, нет класса "Слово".

Также в минус ООП идет то, как сделан класс Main.

## ВЫВОД

В целом неплохо 👍  
Код простой и понятный. 

Сравнить различия между объектно-ориентированным и процедурным стилями:  
Стрим Сергея [Крестики-нолики в процедурном стиле](https://www.youtube.com/watch?v=PPikj1qHxrA)  
Мой стрим [Крестики-нолики в ООП стиле](https://t.me/zhukovsd_it_chat/53243/187097)

Подробное объяснение, как делать эту программу в процедурном и ООП стилях, есть у Сергея в расширенных материалах.

n.176(342)  
#ревью #виселица #оопвиселица 