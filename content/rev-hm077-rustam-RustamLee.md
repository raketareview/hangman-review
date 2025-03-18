https://github.com/RustamLee/hangman-game  
[Rustam]

Игра стремится к ООП. Реализована в нескольких классах.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Одна и та же буква в разных регистрах считается двумя разными буквами и увеличивает счетчик ошибок
```
Errors: 0
Enter one letter: e
 _______
|       |
|
|
|
|
|_________
Secret word: _e___
Errors: 0
Enter one letter: E
 _______
|       |
|       O
|
|
|
|_________
Secret word: _e___
Errors: 1
```
2. Нет списка введенных букв.
3. Нет защиты от дурака
```
-----* MENU *-----
1. Start a new game
2. Rules
3. Exit
Select one option: 
dfdf
Exception in thread "main" java.lang.NumberFormatException: For input string: "dfdf"
	at java.base/java.lang.NumberFormatException.forInputString(NumberFormatException.java:67)
```

## ХОРОШО

+ 👍  Можно ввести только одиночную букву текущего алфавита, в данном случае- английского
+ 👍  Есть меню, где можно прочитать правила
+ 👍  Простая понятная логика работы

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Хуже венгерской ноттации только венгерская ноттация, которая обманывает
```
private final HashSet<String> collection;

//ПРАВИЛЬНО:
private final HashSet<String> words;
```

- Избыточный контекст
```
Gallows.printGallows(int error)

//ПРАВИЛЬНО:
Gallows.print(int error)
```
*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*  

**2. Используй классы через их интерфейсы**
```
private final HashSet<String> collection;
public HashSet<String> deserializeJson(String fileName)

//ПРАВИЛЬНО:
private final Set<String> words;
public Set<String> deserializeJson(String fileName)
```

**3. Нарушение DRY**, магические буквы, числа, слова. Вводи константы
```
System.out.println("1. Start a new game");
System.out.println("2. Rules");
System.out.println("3. Exit");
//...
do {
  //...  
  case 1:  //some action1
  case 2:  //some action2
  case 3:  //some action3
} while (option != 3);

//ПРАВИЛЬНО:
private final static int START = 1;
private final static int RULES = 2;
private final static int EXIT = 3;

System.out.println(START + ". Start a new game");
System.out.println(RULES + ". Rules");
System.out.println(EXIT + ". Exit");
//...
do {
  //...  
  case START:  //some action1
  case RULES:  //some action2
  case EXIT:  //some action3
} while (option != EXIT);
```
*Фаулер, "Рефакторинг", гл.8 п."Замена магического числа символической константой"*   
*refactoring.guru "Замена магического числа символьной константой"*  

**4. Если в блоке if есть return**(break, continue, throw, exit и т.д.), то else не пишется - в этом случае неважно, будет else или нет, так как программа будет работать одинаково
```
if (input.isBlank()) {
  System.out.println("You didn't enter a letter, try again!");
  continue;
} else if (input.length() > 1) {...}

//ПРАВИЛЬНО:
if (input.isBlank()) {
  System.out.println("You didn't enter a letter, try again!");
  continue;
} 
if (input.length() > 1) {...}
```

**5. Во всех утилитных классах** делай закрытый конструктор
```
public final class Rule {
  public static void printRules() {...}
}  

//ПРАВИЛЬНО:
public final class Rule {
  private Rule() {} 
  public static void printRules() {...}
}  
```

**6. class InputHelper**, пользовательский валидированный ввод буквы

- Название неправильное. Класс содержит один статический метод, таким образом это не `Helper`, а `Util`: https://www.baeldung.com/java-helper-vs-utility-classes
- Неясно, почему метод возвращает обертку, а не char
```
public static Character input() {...}
```

**7. class WorldStorage**, словарь

- Никогда не возвращай null
```
public String getRandomWord() {
  if (collection.isEmpty()) {
    return null;
  }
  //...
}
```
Возврат null повышает риск возникновения NullPointerException в программе.  
*Мартин, "Чистый код", гл.7.7-8*  
*Ютуб, Немчинский "Почему нельзя возвращать NULL?"*  

**8. class Rule**, правила

- Используй многострочный String
```
public static void printRules() {
  System.out.println("RULES OF THE GAME");
  System.out.println("\n* Hangman is a game where you guess a secret word letter by letter." +
    "\n* The word is a singular noun in English." +
    "\n* Each wrong guess increases the error counter." +
    //еще миллион строк
}

//ПРАВИЛЬНО:
private static final String TEXT = """
  *RULES OF THE GAME %n
  *Hangman is a game where you guess a secret word letter by letter
  * The word is a singular noun in English.
  * Each wrong guess increases the error counter.
  <-- еще миллион строк -->
  """;

public static void printRules() {
  System.out.println(TEXT);
}
```

**9. class Menu**

- Нарушение инкапсуляции
```
Scanner scanner = new Scanner(System.in);

//ПРАВИЛЬНО:
private final Scanner scanner = new Scanner(System.in);
```

- (±) Меню в процедурном стиле. Для простой программы- почему бы и нет. Про меню в ООП стиле я писал тут: https://t.me/zhukovsd_it_chat/53243/114908

**10. class Gallows**, распечатка хангмана

- В любом switch-case должен быть default, в данном случае default должен бросать исключение с сообщением, что такого номера картинки не существует.

- Распечатка картинки виселицы через switch-case или if-elseif - индусский код.
Картинки нужно хранить в статическом массиве и печатать по номеру, например, так
```
private static final String[][] PICTURES = {
  {
    "-----   ",
    "|       ",
    "|       ",
    "|       ",
    "|       ",
    "|       ",
    "------- ",
   },
   {
     "-----   ",
     "|   |   ",
     "|   O   ",
     "|       ",
     "|       ",
     "|       ",
     "------- ",
   },
   // more pics
  };

public void print(int numPicture) {  
  String[] picture = PICTURES[numPicture];

  for(String line : picture) {
    //напечатать line
  }
}
```

**11. class Game**, основная игровая логика

+ 👍  Получает в конструктор необходимые зависимости, в данном случае- текст слова
```
public class Game {
  //...  
  public Game(String secretWord) {...}
}  
```

- Нарушение инкапсуляции. Публичным здесь должен быть только конструктор и `play()`.
- Магическое: '_', 6.

- Создавай вспомогательные методы, делай программу более простой и понятной
```
while (errorCounter < 6 && charGuessedCounter < secretWord.length()) {...}

//ПРАВИЛЬНО:
if (!isGameOver()) {...}

private boolean isGameOver() {
  return errorCounter < 6 && charGuessedCounter < secretWord.length();
}
```

- Метод выполняет два действия сразу. Разделяй команды и запросы.  
Этот метод должен или выполнить действие, то есть открыть буквы в слове.  
Или подсчитать количество таких букв в слове, то есть выполнить запрос.  
Но не то и другое вместе
```
public int checkCharacter(char character) {
  int letterCounter = 0;
  for (int i = 0; i < secretWord.length(); i++) {
    if (secretWord.charAt(i) == character && guessedWord[i] == '_') {
      guessedWord[i] = character;  <-- открытие буквы- выполнение команды
      letterCounter++;
    }
  }
  return letterCounter;  <-- возврат количества открытых букв- выполнение запроса
}
```
*Мартин, "Чистый код", гл.3, п."Разделение команд и запросов"*  

- Создай и используй методы
```
private boolean isGameOver() {
  return isWin() || isLose();
}
private boolean isWin() {...}
private boolean isLose() {...}
```

**12. class Main**, содержит точку входа main

+ 👍 Только создает и запускает игру, в данном случае через меню, это хорошо.

## АРХИТЕКТУРА

Архитектура стремится к ООП. Программа декомпозирована на ряд классов, каждый из которых придерживается SRP.  
Однако, выявлены и сделаны в виде классов только самые очевидные сущности: словарь, рендерер виселицы etc.

Не выявлена менее очевидная, но более интересная сущность **Маскированное слово**.

Оно имеет состояния:   
+ Отгадываемое слово  
+ Маска  

И поведение:  
+ Вернуть отгадываемое слово  
+ Вернуть маску  
+ Сказать, есть ли буква в слове  
+ Открыть букву в маске, а если такой буквы в слове нет- кинуть исключение  
+ Сказать, открыта ли маска полностью(т.е. слово отгадано)

Все это должно находиться в отдельном классе. Кроме этого в классе не должно быть других методов.  

## ВЫВОД

В целом, неплохо. 

n.77(145)  
#ревью #виселица