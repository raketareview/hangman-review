https://github.com/bakyt-dev/hangman2  
[bakyt-se]

Игра в процедурном стиле, выполнена в нескольких классах.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Команды работают неправильно. 
Если заявлено использование определенных команд, в данном случае 'y' и 'n', то любая другая команда должна считаться ошибочной
```
Welcome to HANGMAN game!

Want to start a game? (Y/N): 
youfk

Thanks for playing!
```

2. Нет списка введенных букв

## ХОРОШО
+ 👍 Прикольные заметки с UML диаграммой классов [learning-development-progress.md](https://github.com/bakyt-dev/hangman2/blob/master/src/learning-development-progress.md)  
+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву английского алфавита

## ЗАМЕЧАНИЯ

**1. Нейминг**

- В эту строку записывается команда, которая может буть одной из трех: старт игры, выход из игры или неизвестная команда 
```
String initialGame = this.scanner.nextLine();

//ПРАВИЛЬНО:
String command = this.scanner.nextLine();
```

- Вводящее в заблуждение название. "Контроллерами" принято называть классы определенного слоя в ООП программах с архитектурой MVC
```
class GameController

//ПРАВИЛЬНО:
class Game
```
Конечно, никто не может тебе запретить назвать класс контроллером в программе, где нет MVC. 
Но тогда это слово в названии не будет иметь никакого смысла.
По той же причине воздержись от названий с постфиксом "Service", если твоя программа не соответствует MVC с анемичной моделью.

- Это не конец игры, это проигрыш. Конец игры это когда проиграл *или* выиграл
```
public boolean isGameOver() {
  return getWrongGuessCount() == MAX_HANGMAN_STAGES;
}
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*  

**2. Небрежное форматирование кода**

Зачем здесь такой конский зазор в две пустые строки?
```
public class Main {
    public static void main(String[] args) {

        GameController gc = new GameController();
        gc.initialPlayerChoice();


    }
}
```

**3. Нарушение DRY**, магические буквы, числа, слова. Вводи константы. А если они уже есть- пользуйся
```
System.out.println("\nWant to start a game? (Y/N): ");

String initialGame = this.scanner.nextLine();

if (initialGame.equals("Y") || initialGame.equals("y") || initialGame.equals("Yes") || initialGame.equals("yes")) {...}

//ПРАВИЛЬНО:
private final static String SHORT_CMD_START = "Y";
private final static String SHORT_CMD_QUIT = "N";

private final static List<String> ALL_START_COMMANDS = List.of(SHORT_CMD_START, "YES");
private final static List<String> ALL_QUIT_COMMANDS = List.of(SHORT_CMD_QUIT, "NET");

System.out.printf("\nWant to start a game? (%s/%s): \n", SHORT_CMD_START, SHORT_CMD_QUIT);

String command = this.scanner.nextLine();

if (isStartCommand(command)) {...}

private final boolean isStartCommand(String command) {
  return ALL_START_COMMANDS.contains(command.toUpperCase());
}
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой" *  
*refactoring.guru "Замена магического числа символьной константой"*

**4. Нарушение DRY**, магические буквы, числа, слова. Вводи константы. 

Если к одной и той же магической штуке обращаются два разных класса, то делай ее константой в одном из этих двух классов. 
Либо перенеси ее в третий класс- эти данные должны быть синхронизированы между собой
```
public class GameController {
  private static final int MAX_WRONG_COUNT = 6;
  //...
}

public class GuessManager {
  private static final int MAX_HANGMAN_STAGES = 6;
  //...
}
```

**5. Используй классы через их интерфейсы**
```
ArrayList<String> words;
HashSet<Character> correctGuesses;
HashSet<Character> getCorrectGuesses() {...}

//ПРАВИЛЬНО:
List<String> words;
Set<Character> correctGuesses;
Set<Character> getCorrectGuesses() {...}
```
Общее правило: ArrayList нужно использовать через List, HashMap- через Map и т.д. 
Это позволяет пользоваться преимуществами полиморфизма.

Да, бывают ситуации, когда, например, с LinkedList нужно работать именно как с LinkedList, а не с List. Но это уже нюансы.

**6. Реакция на ошибки**

- Если по указанному адресу в `class WordManager` не будет найден файл со словами, то программа аварийно вылетит и напечатает непонятные для юзера сообщения в консоль
```
File not found: src\com\hangman\game\words.txt_ (Не удается найти указанный файл)

Welcome to HANGMAN game!
Want to start a game? (Y/N): 
y
Exception in thread "main" java.lang.IndexOutOfBoundsException: Index 0 out of bounds for length 0
	at java.base/jdk.internal.util.Preconditions.outOfBounds(Preconditions.java:100)
```
Здесь должна быть другая реакция на отсутствие файла со словами.  
Нужно сказать юзеру, что файл со словами по указанному пути открыть не удалось и поэтому работа программы будет завершена. 

Причем, путь нужно указать не относительный, как у тебя(`src\com\hangman\game\words.txt_`). 
А абсолютный, чтобы юзер знал, где конкретно ему нужно искать файл: `c:/..../words.txt_`.

После этого корректно завершить работу программы без вываливания красных эксепшенов в консоль.

**7. class WordManager**

- Путь к файлу класс должен получать в конструктор. Иначе класс становится неуниверсальным.

Допустим, в программе будет несколько разных текстовых файлов- на разных языках или по разным темам. 
Тогда вместо нескольких специализированных классов Словаря, где имя файла словаря будет жестко прописано в каждом из классов, можно будет использовать один универсальный класс словарь.

- Избыточно. При чтении слов из файла, они переводятся в верхний регистр
```
private static ArrayList<String> words;

public WordManager() {
  //...
  while (scanner.hasNext()) {
    words.add(scanner.next().toUpperCase());
  }
}
```
Смысла в этом- никакого. Потому что список `words` это элемент внутренненго устройства класса и к этому списку не имеют доступ клиенские классы. 
Клиенты могут только получать случайное слово через соответствующий метод. 

Читай из файла в списк слова так, как они есть. А переводи в нужный регистр только в методе возврата слова.  
Вот так:
```
public String getRandomWord() {
  //...
  return randomWord.toUpperCase();
}
```

- Побочный эффект

Нельзя в методе устанавливать поле класса и возвращать эти же данные через return. 
В этом случае инициализация поля класса будет происходить неявно для клиентского кода. 
То есть, это будет побочный эффект
```
private String currentWord;

public String getRandomWord() {
  String randomWord = words.get((int) (Math.random() * words.size()));
  currentWord = randomWord;  <-- ПОБОЧНЫЙ ЭФФЕКТ
  return randomWord;
}
```
*Мартин "Чистый код", гл.3, "Избавьтесь от побочных эффектов"*

- Избыточно
```
public boolean isWordComplete(HashSet<Character> revealedLetters) {
  int foundLetters = 0;

  for (char letter : currentWord.toCharArray()) {
    if (revealedLetters.contains(letter)) {
      foundLetters++;
    }
  }
  return foundLetters == currentWord.length();
}

//ПРАВИЛЬНО:
public boolean isWordComplete(HashSet<Character> revealedLetters) {
  for (char letter : currentWord.toCharArray()) {
    if (!revealedLetters.contains(letter)) {
      return false;
    }
  }
  return true;
}
```

- Расточительно. Если нужно в цикле объединять строки, то не плюсуй их, а используй StringBuilder
```
String result = "";

for (char letter : currentWord.toCharArray()) {
  if (revealedLetters.contains(letter)) {
    result += result + letter;
  } else {
    result = result + " _ ";
  }
}

//ПРАВИЛЬНО:
StringBuilder result = new StringBuilder();

for (char letter : currentWord.toCharArray()) {
  if (revealedLetters.contains(letter)) {
    result.append(letter);
  } else {
    result.append(" _ ");
  }
}
```
*Гугли: java почему в цикле нельзя складывать строки*

**8. class HangmanDraw**

- Вот это совсем не читается. Нет задачи экономить строчки, главная задача- писать понятный код, который при необходимости можно легко изменить
```
private static final String[] HANGMAN_STAGES = {
  "+----+\n|    |\n|     \n|     \n|     \n|     \n==========",
  "+----+\n|    |\n|    O\n|     \n|     \n|     \n==========",
  //oth's pics
};

//ПРАВИЛЬНО:
private static final String[] HANGMAN_STAGES = {
    """
    -----   
    |       
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
     |       
     ------- 
     """
   ,
   // more pics
};
```

- Если передали некорректный номер ошибки для печати- это баг алгоритма. 
Его нужно не заметать под ковер, а максимально быстро выявить и устранить
```
public void drawHangman(int wrongCount) {
  if (wrongCount >= 0 && wrongCount < HANGMAN_STAGES.length) {
    System.out.println(HANGMAN_STAGES[wrongCount]);
  }
}

//ПРАВИЛЬНО:
public void drawHangman(int wrongCount) {
  System.out.println(HANGMAN_STAGES[wrongCount]);  <-- КИНЕТ ИСКЛЮЧЕНИЕ ПРИ НЕКОРРЕКТНОМ НОМЕРЕ ОШИБКИ
}
```

**9. class GameController**

- Большой божественный метод состоящий из необозримого количества строк- 65
```
void startGame()
```
Делает много всего сразу, разобраться в нем трудно, код читается плохо. 
Метод разделить на несколько маленьких, каждый из которых будет делать свое.  
*Мартин "ЧК", гл.3, "Компактность!"*  
*Фаулер "Рефакторинг", гл.6, "Извлечение метода"* 

- Вводи вспомогательные методы, делай код проще и понятнее, например:
```
private static boolean isEnglishLetter(char c) {
  c = Character.toLowerCase(c);
  return c >= 'a' && c <= 'z';
}
```

- Сделай вспомогательные методы, которые будут будут определять состояние игры:
```
while (!isGameOver()) {
  //...
  if(isWin()) {
    //действия при выигрыше
  }  else if(isLose()) {
    //действия при проигрыше
  }
}

boolean isWin() {
  return wordManager.isWordComplete(...);
} 

boolean isLose() {...}

boolean isGameOver() {
  return  isWin() || isLose();
}
```

Да, у тебя вроде как есть метод, который типа определяет конец игры. 
Но он, как я писал выше, не определяет конец игры, а потому используется в коде очень невнятно:
```
while (!this.guessManager.isGameOver() && !this.wordManager.isWordComplete(this.guessManager.getCorrectGuesses())) {...}
```

- Нарушение DRY. Дублирование кода
```
if (initialGame.equals("Y") || initialGame.equals("y") || initialGame.equals("Yes") || initialGame.equals("yes")) {...}
if (playAgain.equals("Y") || playAgain.equals("y") || playAgain.equals("Yes") || playAgain.equals("yes")) {...}

System.out.print("\nGuess a one English letter: ");
System.out.print("\nGuess a one English letter: \n");
```
Одинаковые куски кода и *одинаковые алгоритмы* выноси во вспомогательные методы.

- Рекурсия
```
public void startGame() {
  //...
  reStartGame();
}  

private void reStartGame() {
  //...  
  startGame();
}
```
Рекурсия должна использоваться только там, где она оптимальна алгоритмически.
Например, при обходе бинарного дерева.

В других ситуациях, где рекурсия может быть замененена циклом, она должна быть им замененена. 
Рекурсия тяжело читается и при определенных условиях может привести к переполнению стека. 
Убери рекурсию, замени ее использование на цикл while().  

Условный пример:
```
//ПЛОХО:
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

//ХОРОШО:
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

- Любой закомментированный код это мусор и антипаттерн "Лодочный якорь"
```
//System.out.println(randomWord);
```

**10. Тестирвание игры с подсказкой**

В рабочем коде не должно быть закомментированных костылей.

Чуть измени GameController:
```
//БЫЛО:
public class GameController {
  //...
  public void startGame() {
    String randomWord = this.wordManager.getRandomWord();
    //System.out.println(randomWord);
    <some code>
  }
}

//СТАНЕТ:
public class GameController {
  //...
  public void startGame() {
    String randomWord = getRandomWord();
    <some code>
  }

  protected String getRandomWord() {
    return this.wordManager.getRandomWord();
  }
}
```

Для тестирования сделай отдельный майн, этот класс можешь даже не комитить. 
Там, с помощью ловкого использования полиморфизма, вставь подсказку.  
Примерно, так:
```
public class MainTest {
  public static void main(String[] args) {
    GameController gc = new GameController() {
      @Override
      protected String getRandomWord() {
        String word = super.getRandomWord();
        System.out.println("ПОДСКАЗКА: word");
        return word;
      }
    };
    gc.initialPlayerChoice();
  }
}
```

**11. class Main**, содержит точку входа main

👍 Только создает и запускает Игру, это хорошо.

## АРХИТЕКТУРА

Несмотря на то, что в проекте несколько классов, он написан в процедурном стиле.
Программа в ООП стиле должна быть декомпозирована по правилам ООП и использовать ООП подход для реализации типичных задач.  
Здесь не видно ООП декомпозиции.

Да, приятно, что 
```
watched: OOP decomposition of the game "Four in a row" from the TG community chanel
```
Но в декомпозиции еще нужно потренироваться, оно так сразу не дается.

Если интересно- пиши в личку, могу скинуть мое общее представление об ООП декомпозиции хангмана. 

## ВЫВОД

Основная игровая логика плохо читается из-за божественного метода в `GameController`.

Для развития навыков процедурной декомпозиции, посмотреть стрим Сергея [Крестики-нолики в процедурном стиле](https://www.youtube.com/watch?v=PPikj1qHxrA)

Если хочется допилить до ООП, сравнить различия между ООП и процедурным стилями:  
Стрим Сергея [Крестики-нолики в процедурном стиле](https://www.youtube.com/watch?v=PPikj1qHxrA)  
Мой стрим [Крестики-нолики в ООП стиле](https://t.me/zhukovsd_it_chat/53243/187097)

## ВЫВОД

n.123(228)  
#ревью #виселица 