https://github.com/AmanbekAzizUulu/hangman-simple-console-game  
[распознается графически]

Игра написана в ООП стиле, MVC с анемичной моделью.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Нет списка введенных букв 

2. Надписи "GAME" в каждой строчке не несут полезной нагрузки и только перегружают визуальный интерфейс
```java
[GAME] Type in your guess [single letter]: y
[GAME]     +---+
[GAME]     |   |
[GAME]     O   |
[GAME]    /|\  |
[GAME]    /    |
[GAME]         |
[GAME] =========
[GAME] Incorrect letter
[GAME] w___
[GAME] Type in your guess [single letter]: g
```

3. Команды работают неправильно. 
Если заявлено использование двух команд, в данном случае 'y' и 'n', то любая другая команда должна считаться ошибочной
```
[GAME] The word was: wolf
[GAME] Play again? (y/n): fffuuuuuck  <-- ВВЕЛ НЕКОРРЕКТНУЮ КОМАНДУ
[GAME] Goodbye!  <-- НЕКОРРЕКТНАЯ КОМАНДА ОТРАБОТАЛА КАК КОМАНДА ВЫХОДА
```

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву английского алфавита
+ 👍 При старте программы выбирается уровень сложности и тематика словаря
+ 👍 Архитектура MVC

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Однобуквенные переменные допустимы только в циклах для обозначения счетчика циклов, либо в цикле foreach для присвоения переменной текущей итерации
```java
var w = new Word();

//ПРАВИЛЬНО:
var word = new Word();
```

- Если в проекте есть класс `Word`, то все переменные с именем, включающим это название, должны быть экземплярами этого класса.  
Если разные концепции называются одним и тем же именем, это приводит к путанице
```java
public final class GameEngine {
  public GuessResult guess(
    String word,  <-- Это String, а не Word 
    Set<Character> guessed, 
    char guess) {...}
}

//ПРАВИЛЬНО:
public final class GameEngine {
  public GuessResult guess(String text, Set<Character> guessed, char guess) {...}
}
```

- Венгерская нотация.

В названии переменных не пиши тип данных, к которым они относится.  
И вообще не употребляй венгерскую нотацию.  
Название переменной должно отвечать на вопрос что хранит переменная, а не как хранит
```java
var viewStr = getCurrentView();

//ПРАВИЛЬНО:
var currentView = getCurrentView();
```

- Используй стандартные названия
```java
public FileWordProvider(String resourcePath)

//ПРАВИЛЬНО:
public FileWordProvider(String filepath)  //или path
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Анемические модели**

Проект сделан в ООП стиле, модели- анемические.  
То есть в классах находятся только или почти только одни данные, а их поведение вынесено куда-то вовне.

Моё личное мнение- для таких консольных проектов, как "Виселица", лучше богатая модель, а не анемичная.
Проект с анемичной моделью *в таких задачах* выглядит более "процедурным"- классы становятся больше похожими 
на контейнеры с функциями в стиле процедурного программирования.

Но анемическая модель это тоже корректный паттерн программирования, поэтому почему бы и нет.

**3. enum HangmanStages**

- Неправильное использование enum.

Картинки здесь объединены в енам.  
Но енам здесь не используется как енам.  
К его константам не обращаются как к конкретным константам, а обращаются как к массиву:
```java
HangmanStages stage = selectStage(wrongAttempts);
stage.getPicture()...

private HangmanStages selectStage(int wrongAttempts) {
  HangmanStages[] values = HangmanStages.values();
  //...
  return values[index];
}
```

Поэтому либо просто храни картинки в публичной константе- это будет проще и понятнее
```java
public final class HangmanStages {
    public static final String[] STAGES = {
      //картинки хангмана    
    }; 
}
```

Либо тоже в массиве, но выдавая картинку по её номеру- так лучше
```java
public class HangmanStages {
  private static final String[] STAGES = {
    //картинки хангмана    
  }; 

  public String get(int stageIndex) {
    return STAGES[stageIndex];  
  }
}
```

Либо просто сделать рендерер, который будет распечатывать хангмана- так еще лучше
```java
public class HangmanRenderer {
  private static final String[] PICTURES = {
    //картинки хангмана 
  };
  
  public void render(int num) {
    String picture = PICTURES[num];
    System.out.println(picture);
  }
}
```

### Пакет entities

Здесь находятся сущности-модели.

**4. class Word**, сущность "Слово", анемическая модель.

+ 👍 Хорошо, что выявил сущность "Слово" и сделал его классом.

- Класс может быть преобразован в record без потери функционала.

Это очень простая анемичная модель с 4-мя полями, не вижу смысла делать ее изменяемой- 
во время работы программы, значения полей класса не изменяются.  

Поэтому лучше этот класс сделать рекордом
```java
public class Word {
  private String text;
  private String hint;
  private Category category;
  private Difficulty difficulty;
  //нет конструктора, но есть сеттеры и геттеры
}

//ЛУЧШЕ:
public record Word(String text, String hint, Category category, Difficulty difficulty) {}
```

*Блох "Java. Эффективное программирование", изд.3, гл.4.3.*
```java
"Классы должны быть неизменяемыми, если только нет очень важной причины, чтобы сделать их изменяемыми" - Блох.
"Вы всегда должны делать объекты с небольшими значениями, такими как PhoneNumber или Complex, неизменяемыми" - Блох.
```

**5. class Player**

Как и все другие модели тут, этот класс- анемичная модель.

+ 👍 Класс для ведения статистики юзера-сколько выиграл и проиграл. Норм.

- Класс- бесполезный балласт в проекте. Он не используется.

Либо ты недоделал проект, либо оставил этот класс как точку расширения.
Второе тоже плохо- лишний класс как точка расширения это нарушение KISS и YAGNI.

Я понимаю, когда в качестве точки расширения делают интерфейс и одну его имплементацию, хотя можно было сделать только один класс без интерфейса.  
В этом случае интерфейс является точкой расширения, которая позволяет дополнять проект новыми классами-имплементациями.

Но просто класс, который лежит в проекте и не используется- это зря потраченное на его создание время.

- При прочих равных, нужно использовать примитивный тип, а не класс-обертку
```java
private Integer winCount;
private Integer loseCount;

//ПРАВИЛЬНО:
private int winCount;
private int loseCount;

```
Для применения класса-обертки над примитивным типом данных, должна быть какая-то причина.  
Здесь использование обертки не дает ничего большего по сравнению с примитивом.  
*Блох "Java. Эффективное программирование", изд.3, гл.9.5*
```java
"Предпочитайте примитивные типы упакованным примитивным типам" - Блох.
```

- Неизменяемые поля нужно делать final.

По ходу игры имя плеера по идее не должно изменяться, поэтому оно должно быть final.  
Непонятно, почему ты в своих классах не делаешь конструкторов, а данные инжектишь только сеттерами
```java
public final class Player {
  private String name;
  private Integer winCount;
  private Integer loseCount;
  //геттеры и сеттеры 
}

//ПРАВИЛЬНО:
public final class Player {
  private final String name;
  private int winCount;
  private int loseCount;

  public Player(String name) {
    this(name, 0, 0);
  }

  public Player(String name, int winCount, int loseCount) {...}

  //геттеры и сеттеры 
}
```

**6. class GuessResult**

Здесь находится результат совершения хода.  
То есть, юзер (где-то) делает ход и получает результат хода в виде этого объекта.

Идея ясна, но реализация мне не нравится.  
Давай посмотрим, что хранит этот результат
```java
public final class GuessResult {
  private boolean accepted;
  private boolean alreadyGuessed;
  private boolean hit;
  private GameStatus statusAfter;
  private String message;
  //геттеры и сеттеры
}
```

Зачем это всё хранить это всё в результате, мне неясно.

Ок, забегая наперед, у нас есть Движок Игры, который совершает ход и результат этого хода может быть разным.  
И нам нужно сообщить, что же произошло в результате этого хода.

Давай прикинем, что у нас может произойти в результате совершения хода, то есть, ввода очередной буквы:  
🔸Буква уже была введена ранее, ход не совершен  
🔸Ввели новую неправильную букву  
🔸Ввели новую правильную букву  

Итого, результат совершения хода достаточно представить в виде простого енама типа такого
```java
public enum TurnResult {
  LETTER_ALREADY_USED,  //ввели использованную ранее букву
  OPEN_LETTER,  //угадали букву- открыли букву в слове
  MISS,   //не угадали букву- ввели счетчик ошибок
}
```

У тебя уже есть подобный енам `GuessOutcome`, который валяется на свалке неиспользуемых классов:
```java
public enum GuessOutcome {
  HIT,
  MISS,
  ALREADY_GUESSED,
  INVALID
}
```

В принципе, в твоем *используемом* сложном `GuessResult` есть аналоги этих значений: `accepted`, `alreadyGuessed`, `hit`.  
Разница в том, что результат хода должен быть каким-то одним из трех(или четырех) и для этого подходит енам.  
А в `GuessResult` можно выставить любые комбинации true-false в значениях `accepted`, `alreadyGuessed`, `hit`.

Ок, но почему бы в результат не засунуть `message`, `statusAfter`, это же удобно?  
Потому что эти данные НЕ ЯВЛЯЮТСЯ результатом совершения хода, или в твоей терминологии- результатом угадывания.

"Сообщение" `String message` это вообще элемент представления и его не должно быть в слое моделей.  
Сообщение(message) в том контексте, как оно тут используется, может появиться только когда-нибудь в контроллере или вью.  

`GameStatus statusAfter` это как следует из названия, это состояние движка игры- выиграл, проиграл etc.

Короче, если нужно узнать результат совершения хода- нужно спросить результат совершения хода.  
Если нужно узнать состояние движка, нужно спросить состояние движка.  
Я бы вообще не плодил лишних сущностей и убрал `GameStatus`.  
А вместо него сделал бы в движке публичные методы `isWin()`/`isLose()` и их бы юзал.

**7. enum GameStatus**

Как писал выше- считаю этот енам лишним, вместо него нужно в движке использовать публичные методы определения выиграл/проиграл. 

**8. Dead Code в пакете entities**

Классы, которые не используется, но лежат потому что жаль выбросить:
```java
enum ScreenState
enum GuessOutcome
```

### Пакет engine

Пакет, в котором хранится не только движок игры.  
Потому что движок игры это бизнес логика игры без взаимодействия с представлением.

В этом же пакете лежит как движок игры модель `class GameEngine`, так и его контроллеры- `class Game` и `class GameRound`.  
Это классы разных слоев. Контроллер движка игры не является движком игры и не может лежат ь вместе с ним в пакете `engine`.

Еще кроме контроллеров и моделей в пакете лежат чистые вью: 
```java
enum HangmanStages
class HangmanConsoleRenderer
```

**9. final class GameEngine**

+ 👍 Этот класс- движок игры, движок это хорошо.

Движок это вся игровая логика без взаимодействия с представлением.  
Игровая логика с взаимодействием с представлением в таких случаях выносится в отдельный контроллер.  

- Зависимость модели от представления.

Модель, а движок должен быть моделью, должна работать с данными, которые не зависят от представления.  
Вот здесь модель зависит от представления- движок  озабочен не данными, а то, как эти данные будут показываться юзеру
```java
public GuessResult guess(String word, Set<Character> guessed, char guess) {
  GuessResult result = new GuessResult();
  //...
  result.setMessage("Letter already guessed");  <-- Указывает как состояние будет показано юзеру 
  return result;
}
```

Движок должен вернуть результат совершения хода(угадал/не угадал/повторный ввод).  
А как этот результат будет показываться юзеру, это ответственность слоёв `Controller` и `View`.  

В идеале движок вообще не должен знать, в какой  визуальной среде он используется- консоль, Windows UI, Android или вообще аркадный автомат
![pic](https://github.com/raketareview/hangman-review/blob/master/content/resources/rev-hm172/arcade_machine_hangman.png) 

И даже на каком языке он будет использоваться: на английском("Letter already guessed") или русском("Буква уже была введена")

Про свои соображения о том, что  в движке должен возвращать метод совершения хода, я писал выше.

- Не передавай тернарники в аргументы методов.

Тернарники сами по себе тяжело читаются, поэтому упрощай их использование
```java
result.setMessage(hit ? "Correct letter" : "Incorrect letter");

//ПРАВИЛЬНО:
String message = hit ? "Correct letter" : "Incorrect letter";
result.setMessage(message);
```

- Делай код более читабельным, вводи вспомогательные методы
```java
public GuessResult guess(String word, Set<Character> guessed, char guess) {
  GuessResult result = new GuessResult();

  if (guessed.contains(guess)) {
    result.setAccepted(false);
	result.setAlreadyGuessed(true);
	result.setHit(false);
	result.setStatusAfter(GameStatus.IN_PROGRESS);
	result.setMessage("Letter already guessed");
	return result;
  }
  //много кода
}

//ЛУЧШЕ:
public GuessResult guess(String word, Set<Character> guessed, char letter) {
  if (isGuessedLetter(letter)) {
    return createAlreadyGuessedResult();
  }
  //много кода
}
```

- Данные, которые должен хранить движок.

Движок должен хранить все данные, которые касаются логики выполнения хода, которые не зависят от представления.  
Как минимум- слово и использованные/отгаданные буквы, маска слова(слово с открытыми и закрытыми буквами), текущие ошибки
```java
public final class GameEngine {
  public GuessResult guess(String word, Set<Character> guessed, char guess) {...}
}

//ПРАВИЛЬНО:
public final class GameEngine {
  private final Word word;
  private final List<Character> usedLetters;
  private int mistakeCounter;
  //...

  public GameEngine(Word word) {...}

  public GuessResult guess(char letter) {...}
}
```

То, что сейчас движок не хранит эти данные, а каждый раз принимает их откуда-то означает, что часть ответственности Движка забрал себе кто-то другой.  
Очевидно, что "другой" это контроллер. Который таким образом становится толстым контроллером, а толстый контроллер это плохо.

Да, но как разобраться в том, что именно должен хранить движок, а что именно должен хранить контроллер?

Очень просто:  
Движок это модель и он должен хранить максимум логики и данных, которые нужны для выполнения логики игры и которые при этом НЕ ЗАВИСИТ от представления.  

Слово и список использованных букв это чистые данные, они не зависят от представления.  
И в игре на андроид, и в игре на аркадном автомате, эти данные будут одинаковы.  
Разным будет только СПОСОБ ПОКАЗА этих данных юзеру, то есть представление.

Логика подсчета ошибок и сам счетчик это тоже чистые данные, которые не зависят от представления.  
Поэтому они тоже должны быть в Движке, а не в его контроллере.

- Нужно перераспределить данные между движком и его контроллерами.

В связи со всем вышесказанным, нужно перераспределить методы и поля между движком и его контроллерами.  
Всё, что касается логики игры и не зависит от представления, должно быть в Движке.  
Контроллеры должны быть тонкими и содержать только ту логику, которая связывает модели(в т.ч. Движок) и представления.

**10. Контроллеры: class Game и class GameRound**

По сути, это контроллеры- они связывают модели `GameEngine`, `Word` с представлением `ConsoleUI`.  

**Контроллеры должны быть тонкими.**

Вся логика, которая в них работает с чистыми данными и не зависит от представления, должна быть вынесена в модель-движок.
Та логика игры, которая ЗАВИСИТ ОТ ПРЕДСТАВЛЕНИЯ, должна находиться в контроллере.

Например, модель-движок должен внутри себя увеличивать счетчик ошибок.  
А контроллер движка должен при этом напечатать на экране изображение хангмана, которое соответствует счетчику ошибок.

Счетчик ошибок здесь это "чистые данные", они должны находиться в модели.  
Картинка повешенного человечка- представление этих данных, распечатывать их должен контроллер через вью.

Ответственности у контроллеров `Game` и `GameRound` частично перепутаны.

`GameRound` должен выполнять всю логику контроллера для одного раунда игры.   
`Game` должен запускать контроллер раунда и вести с юзером диалог "Играть или выйти?".  
Сейчас это не совсем так.

**11. class GameRound**

Контроллер, который работает с движком и выполняет один раунд игры. 

- Этот класс должен получать в себя движок и представление  
Примерно так
```java
public class GameRound {
  private final String secret;
  private final Set<Character> guessed;
  private int wrongAttempts;

  public GameRound(String secret) {...}
}

//ПРАВИЛЬНО:
public class GameRound {
  private final GameEngine gameEngine;

  public GameRound(ConsoleUI consoleUI, GameEngine gameEngine) {...}
}
```

- Компилируй регулярные выражения. 

Если регулярное выражение используется многократно, как здесь, то нужно его откомпилировать.  
Компиляция регулярного выражения в объект `Pattern` с помощью `Pattern.compile()` позволяет повысить производительность при многократном использовании
```java
var userStringInput = scanner.nextLine().trim().toLowerCase(Locale.ROOT);
if (!userStringInput.matches("[a-z]")) {...}

//ПРАВИЛЬНО:
private static final String REGEX = "[a-z]";
private final Pattern pattern = Pattern.compile(REGEX);

var userStringInput = scanner.nextLine().trim().toLowerCase(Locale.ROOT);
Matcher matcher = pattern.matcher(userStringInput);
if (!matcher.matches()) {...}
```

```java
"Хотя String.matches — простейший способ проверки, соответствует ли строка регулярному выражению, 
он не подходит для многократного использования в ситуациях, критичных в смысле производительности." - Блох
```
Здесь, конечно, нет критической ситуации с производительностью.  
Но здесь нет и необходимости просто так использовать неоптимальный алгоритм при работе с регулярными выражениями.  
*Блох "Java. Эффективное программирование", изд.3, гл.2.6*

- Ацкий ад. Это вообще не читается, вводи поясняющие переменные
```java
var word = wordProvider.getRandom(settings.category(), settings.difficulty()).orElseThrow(() -> new IllegalStateException("No words for chosen filters"));
```
*Фаулер, "Рефакторинг", гл.6, "Введение поясняющей переменной"*

- Распределение ответственностей между движком и его контроллером. 

Определение того, что игра находится в состоянии выиграл/проиграл это логика, которая не зависит от представления.  
Поэтому она должна находиться в контроллере, а не в его движке
```java
while (true) {
  //...
  if (gameResult.getStatusAfter() == GameStatus.WON) {
    consoleUI.printGameEmptyLine();
	consoleUI.printGameResult("You Won!");
	consoleUI.printGame("Guessed word: " + viewStr);
	return RoundResult.WON;
  }

  if (wrongAttempts >= HangmanStages.values().length - 1) {
	consoleUI.printGameEmptyLine();
	consoleUI.printGameResult("You Lost!");
	consoleUI.printGame("The word was: " + secret);
	return RoundResult.LOST;
  }
}

//ПРАВИЛЬНО:
while (!gameEngine.isGameOver()) {
  //...
  if (gameEngine.isWin()) {
    printWinMessage();
  }

  if (gameEngine.isLose()) {
    printLoseMessage();
  }
}

private void printWinMessage() {
  consoleUI.printGameEmptyLine();
  consoleUI.printGameResult("You Won!");

  Word word = gameEngine.getWord();
  consoleUI.printGame("Guessed word: " + word.getText());
}
```

- Результатом хода должна быть информация: буква уже была введена ранее, есть в слове, нет в слове.  

Контроллер должен сам знать, как печатать данные модели, а не брать представление из модели- потому что модель не должна зависеть от представления 
```java
var gameResult = engine.guess(secret, guessed, guess);
consoleUI.printGame(gameResult.getMessage());

//ПРАВИЛЬНО:
GameEngine.TurnResult result =  engine.guess(letter);
printTurnResult(result);

private void printTurnResult(GameEngine.TurnResult result) {
  String message = switch(result)  {
    case LETTER_ALREADY_GUESSED -> "Letter already guessed";
    //...
  }
  //распечатать message
}
```

- Увеличивать счетчик ошибок в случае неправильного ввода буквы это ответственность движка, а не его контроллера
```java
var isNewWrong = secret.indexOf(guess) == -1 && !guessed.contains(guess);
if (isNewWrong) {
  wrongAttempts++;
}
```

**12. class Game**

Это контроллер контроллера движка

- Задача этого контроллера должна состоять только в опросе юзера "Играть или выйти" и запуске контроллера игры.

- Прием аргументов в конструктор.

Согласно паттерна GRASP "Creator", создавать объект должен тот, кто его использует.  
Поэтому Контроллер контроллера движка должен создавать для своего использования экземпляр Контроллера движка

```java
public final class Game {
  private final GameEngine engine;
  private final ConsoleUI consoleUI;
  private final WordProvider wordProvider;

  private Game(GameEngine engine, ConsoleUI consoleUI, WordProvider wordProvider) {...}
  //...
}

//ПРАВИЛЬНО ПРИМЕРНО ТАК:
public final class Game {
  private final ConsoleUI consoleUI;
  private final WordProvider wordProvider;

  private Game(ConsoleUI consoleUI, WordProvider wordProvider) {...}
  
  public void start() {
    //диалог "играть или выйти"
    //если выбрали играть- создаем движок и играем
    Word word = wordProvider.get();
    GameEngine engine = new GameEngine(word);
    GameRound round = new GameRound(engine, consoleUI);
    round.start(); 
    //...
  }
}
```

- Не мучай читателя твоего кода такими длинными конструкциями
```java
var word = wordProvider.getRandom(settings.category(), settings.difficulty()).orElseThrow(() -> new IllegalStateException("No words for chosen filters"));
```

Код должен читаться легко, как книга.  
Для этого вместо того, чтобы совмещать много инструкций в одну, пиши каждую инструкцию в отдельной строке- при необходимости, вводи поясняющие переменные.  
Совмещать не больше 2-3 инструкций в одну строку можно, только когда это легко читается.  
Например условный пример:
```java
//ОДНА ИНСТРУКЦИЯ В ОДНОЙ СТРОКЕ, НО ТАК ХУЖЕ:
String word = dictionary.get();
return word;

//ДВЕ ИНСТРУКЦИИ В ОДНОЙ СТРОКЕ, НО ТАК ЛУЧШЕ:
return dictionary.get();
```

**### Пакет utils**

В этом пакете нет утилит.  
Утилитными являются классы, которые содержат только статические методы и не содержат изменяемых полей. 

Классы в этом пакете больше похожи на сервис.

**13. interface WordProvider**, словарь слов

+ 👍 Интерфейс словаря это хорошо.  
Можно делать разные его реализации, которые будут получать слова из разных источников(файл, http, из памяти etc).

**14. class FileWordProvider implements WordProvider**

- Работа с исключениями

Перехватывай конкретное исключение.

Всегда перехватывай не базовое, а конкретное исключение, которое может сгенерировать код.  
В данном случае метод `br.readLine()` может бросить не какое угодно исключение, а конкретное- IOException.

Не бросай базовый эксепшен. Конкретизируй ситуацию- бросай то исключение, которое подходит под этот конкретный случай

```java
try (var br = new BufferedReader(new InputStreamReader(is, StandardCharsets.UTF_8))) {
  while ((line = br.readLine()) != null) {...}
} catch (Exception e) {
  throw new RuntimeException("Failed to load words from: " + resourcePath, e);
}

//ПРАВИЛЬНО:
try (var br = new BufferedReader(new InputStreamReader(is, StandardCharsets.UTF_8))) {
  while ((line = br.readLine()) != null) {...}
} catch (IOException e) {
  throw new ПодходящееСлучаюИсключение("Failed to load words from: " + resourcePath, e);
}
```

*Хорстманн "Java. Библиотека профессионала", т1., гл.11*
```java
"Не ограничивайтесь генерацией RuntimeException. 
Найдите подходящий подкласс или создайте собственный." - Хорстманн
```

- Магическое число:
```java
if (parts.length < 4) {...}
```

- Пиши код так, чтобы коллегам было легко его читать
```java
var w = new Word();
w.setText(parts[0].trim().toLowerCase()); 
w.setHint(parts[1].trim());
w.setCategory(Category.valueOf(parts[2].trim().toUpperCase()));
w.setDifficulty(Difficulty.valueOf(parts[3].trim().toUpperCase()));

return w;

//ПРАВИЛЬНО:
String text = getParameter(parts, TEXT_POS, LOWER_CASE);
String hint = getParameter(parts, HINT_POS, LOWER_CASE);

String categoryName = getParameter(parts, CATEGORY_POS, UPPER_CASE); 
Category category = Category.valueOf(categoryName);

String difficultyName = getParameter(parts, DIFFICULTY_POS, UPPER_CASE);
Difficulty difficulty= Difficulty.valueOf(difficultyName);

var word = new Word();

word.setText(text); 
word.setHint(hint);
word.setCategory(category);
word.setDifficulty(difficulty);

return word;
```

И тогда становится ясно, что сетить тут не надо.  
Тут надо анемическую модель `Word` создавать через обычный конструктор:
```java
return new Word(text, hint, category, difficulty);
```

### Пакет ui

**15. class ConsoleUI**

+ 👍 Вью, через который происходит вся распечатка. Слой преставления(вью) это хорошо.

- не надо сюда передавать сканер 
```java
public boolean askPlayAgain(Scanner scanner) {...}

//ПРАВИЛЬНО:
public boolean askPlayAgain() {...}
```
Сканер это подробность внутреннего устройства вью.  
Поэтому сканер нужно создавать внутри самого `ConsoleUI`.  
И даже не нужно передавать `Scanner` в конструктор `ConsoleUI`. 

- Нарушение DRY, магические буквы, числа, слова. Вводи константы 
```java
public boolean askPlayAgain(Scanner scanner) {
  out.print("[GAME] Play again? (y/n): ");
  var input = scanner.nextLine().trim().toLowerCase();

  return input.equals("y") || input.equals("yes");
}

//ПРАВИЛЬНО:
private static final String START =  "yes";
private static final String QUIT =  "no"; 

public boolean askPlayAgain() {
  while(true) {
    out.printf("Play again? (%c/%c): ", formatted(START.charAt(0), QUIT.charAt(0)); 
    var input = scanner.nextLine().trim().toLowerCase();
  
    if(isStartCommand(input))) {
      return true;
    }
    if(isQuitCommand(input))) {
      return false;
    }
    //распечатать сообщение ввели некорректную команду
  }
}

private boolean isStartCommand(String value) {
  return isCommand(START, value);
}

private boolean isCommand(String command, String value) {
  String shortCommand = String.valueOf(command.charAt(0));  
  return command.equals(value) || shortCommand.equals(value);
}
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*

- Нарушение DRY, дублирование кода.

Эти методы частично делают одно и то же- получают от юзера число в заданном диапазоне
```java
public Category categorySelection(Scanner scanner) 
public Difficulty difficultySelection(Scanner scanner)
```
Общий код нужно вынести во вспомогательный метод.

+ 👍 В целом, как вью, класс норм.  
Он делает только простой ввод/вывод информации и не содержит общую игровую логику.

**16. class App** содержит точку входа main.

+ 👍 Только создает и запускает игру, это хорошо.

- Создание объекта и его использование это разные операции и должны выполняться отдельными инструкциями
```java
new Game().start();

//ПРАВИЛЬНО:
Game game = new Game();
game.start();
```

## АРХИТЕКТУРА

Программа написана в архитектуре MVC с анемичными моделями.  

Главным недостатком проекта мне видится неправильное распределение кода между движком игры и его контроллерами.  

**Движок.**

Движок должен быть моделью и содержать в себе всю логику игрового процесса, которая не зависит от представления.

**Контроллер движка.**

Контроллер движка, который связывает движок с представлением, должен быть тонким и содержать только ту логику, которая зависит от представления.  

Такой контроллер должен быть сделан таким образом, чтобы его можно было использовать напрямую.  
В простейшем виде примерно вот так:
```java
public class MainTest {

  public static void main(String[] args) {

    ConsoleUI ui = new ConsoleUI(...);
    Word word = new Word("Астролябия", ...);

    GameEngine engine = new GameEngine(word);

    GameRound round = new GameRound(ui, engine); 
    round.start();
  }
}
```

При добавлении словаря, использоваться контроллер движка должен примерно так
```java
public class MainWithDictionary {

  public static void main(String[] args) {

    ConsoleUI ui = new ConsoleUI(...);

    WordProvider wordProvider = FileWordProvider("words.txt");
    Word word = wordProvider.getRandom(Category.ANIMALS, Difficulty.EASY);

    GameEngine engine = new GameEngine(word);

    GameRound round = new GameRound(ui, engine); 
    round.start();
  }
}
```

**Контроллер контроллера движка.**

Этот класс должен просто каждый раз запускать раунды игры до тех пор, пока юзеру не надоест.  
Использоваться он должен примерно так
```java
public class Main {

  public static void main(String[] args) {

    ConsoleUI ui = new ConsoleUI(...);

    WordProvider wordProvider = FileWordProvider("words.txt");

    Game game = new Game(ui, wordProvider);
    game.start();
  }
}

public class Game {
  //...

  public Game(ConsoleUI consoleUI, WordProvider wordProvider) {...}
  
  public void start() {
    while(true) {
      //спросить будет ли юзер играть
      //если не будет- return

      //получить у юзера сложность    
      //получить у юзера тему словаря
      Word word = wordProvider.getRandom(...);
      GameEngine engine = new GameEngine(word);

      GameRound round = new GameRound(consoleUI, engine); 
      round.start();
    }
     //
  }
}
```

## ВЫВОД

Внезапно мало используешь конструкторы, отдавая предпочтения сеттерам. 
Хотя оснований для такого подхода в коде не видно. 

Более осознанно пиши архитектуру MVC.  
Думай о том, к какому слою принадлежит каждый класс и какие обязательства это на него накладывает.  
Не должно быть смешивания слоёв в одном классе. В частности, модели не должны зависеть от представления.  
Контроллеры должны быть тонкими.

Непонятно, насколько осознанно ты пишешь MVC, потому что не могу понять мотивацию некоторых твоих решений.    
Пишешь ли так, потому что изучал концепцию MVC.  
Или просто пишешь типа как в Спринг, но в коре.

Если последнее- больше почитай теории об ответственностях слоёв MVC и различиях анемической и богатой моделей. 

В целом норм.

n.172(333)  
#ревью #виселица #оопвиселица #mvc 