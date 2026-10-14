https://github.com/Akvamarinika/hangman  
[Dasha Akvamarinika]

👍 Хорошая ООП-декомпозиция. Архитектура MVC.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Непонятный зазор:
```java
Выберите действие: 1
Загаданное слово: _ _ _ _ _ _ _ _ _ _ _ _ _ 
Ваши жизни: ❤️❤️❤️❤️❤️❤️
Ранее введенные буквы: 






============
```
Если это пустая виселица, то мы должны видеть пустую виселицу.

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву русского алфавита
+ 👍 Есть список введенных букв
+ 🚀 Архитектура MVC
+ 🚀 Расширяемость:  
Благодаря UI-интерфейсам, можно добавить другие языки интерфейса и тем самым сделать языковую локализацию.  

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Если в проекте есть класс `Letter`, то все переменные с именем, включающим это название, должны быть экземплярами этого класса.  

Когда разные концепции называются одним и тем же именем, это приводит к путанице
```java
public class Letter {
  public boolean openIfMatches(char letter) {...}
}

//ПРАВИЛЬНО:
public class Letter {
  public boolean openIfMatches(char symbol) {...}
}
```

Например, вот здесь непонятно, что происходит в этой letter-вакханалии:
```java
letter -> letter.openIfMatches(letterUser)
```
Здесь `letter` это `Letter`, а `letterUser` это `char`.

- Тут тоже:
```java
public class Word {
  private final String secretWord;
  private final List<Letter> stateWord;
  //...
}

//ПРАВИЛЬНО:
public class Word {
  private final String text;
  private final List<Letter> mask;
  //...
}
```

Тем более, вот тут ты сама признаёшь, что это маска:
```java
this.stateWord = createMask();

//ПРАВИЛЬНО:
this.mask = createMask();
```

Вообще, конечно, это не "маска", это просто "letters".  
Потому что "маской" можно назвать текущее значение букв(т.е. объектов кастомного типа `Letter`) с учётом их открытости, а не список букв как таковых.  
Потому что буквы хранят как маску, так и исходный текст.

- Давай простые, понятные, лаконичные названия
```java
public class Word {
  //...

  public boolean makeGuessLetter(char letterUser) {
    //открывает(делает видимой) букву в слове
  }
}

//ПРАВИЛЬНО:
public class Word {
  //...

  public boolean openLetter(char symbol) {
    //открывает(делает видимой) букву в слове
  }
}
```

- Запутала на ровном месте
```java
private boolean alreadyNamed(char letter) {
  return usedLetters.contains(letter);
}

//ПРАВИЛЬНО:
private boolean isUsedLetter(char letter) {
  return usedLetters.contains(letter);
}
```

- Звучит как команда "посчитай жизни":
```java
int countLives;
```

Придерживайся правил английского языка, "счётчик жизней" правильно будет так:
```java
int livesCount;
```

- Пиши более естественные для англ. языка названия
```java
String chooseWordRandom()

//ПРАВИЛЬНО:
String chooseRandomWord()

//Я БЫ ЕЩЕ УЛУЧШИЛ:
String getRandomWord()
```

- Название путает.

Не называй метод "run" если он не переопределяет `Runnable.run()`- название этого метода ассоциируется с этим стандартным интерфейсом и применением потоков.  
Здесь этот метод не имеет отношения к `Runnable.run()`
```java
public class GameRunner {
  //...
  public void run() {...}
}

//ПРАВИЛЬНО:
start(), go(), execute(), perform() 
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Нарушение DRY**

Магические буквы, числа, слова. Вводи константы. 

- Совместная магия. 

Если к одной и той же магической штуке обращаются два разных класса, то делай ее константой в одном из этих двух классов.  
Либо перенеси ее в третий класс и из первых двух классов обращайся к этим константам- эти данные должны быть синхронизированы между собой
```java
public class ConsoleUI implements GameUI {
  private static final char NEW_GAME = '1';
  private static final char EXIT = '2';
  //...
}

public record RussianText() implements GameText {
  //...
 
  @Override
  public String getMenuTemplate() {
    return """
        ╔══════════════════════════╗
        ║       HANGMAN GAME       ║
        ╠══════════════════════════╣
        ║  1. Новая игра           ║
        ║  2. Выйти                ║
        ╚══════════════════════════╝
        Выберите действие:\s""";
  }
}
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru, "Замена магического числа символьной константой"*

### Пакет model

**3. class Letter**

+ 👍 Класс буквы, которая может быть закрытой или открытой. Отлично, что ты выявила эту информационную сущность и сделала её классом. 

- Нарушение SRP.

Непонятно, какое дело единой ответственности Буквы до угадывания чего-либо
```java
public boolean openIfMatches(char letter)
  //Открывает букву, если её угадали: this.value == letter
}
```
Класс просто должен хранить символ и значение его видимости: скрыт/открыт.  
По какой причине символ скрыт и по какой причине его хотят открыть- не его ума дело.

Здесь должен быть метод, который просто открывает букву, вот так: 
```java
public void open() {
  isSolved = true;
}
```
Определять условия, по которым эта буква открывается, должен не класс `Letter`, а кто-то другой.

- Как следствие предыдущего замечания- неправильный нейминг
```java
private boolean isSolved = false;

//ПРАВИЛЬНО:
private boolean isOpen;  //При создании экземпляра класса Letter, значение этого поля в нём установится = false
```

**4. class Word**

+ 👍 Снова молодец, что выявила эту сущность и в целом сделала её почти правильно.

- В классе есть два поля, в которых хранится одно и то же- текст слова.  
Выглядит, как дублирование данных:
```java
private final String secretWord;  <-- Хранит текст слова
private final List<Letter> stateWord;  <-- Хранит текст слова
```

Тут нужно определиться с ролью класса `Letter` в проекте.

Что это, составная часть класса `Word`, которую должен видеть клиент точно так же, как в реальной жизни мы видим каждое слово-понятие, состоящим из отдельных букв?  
В противовес китайцам, которые видят слово-понятие в виде одного общего рисунка.

Тогда в классе достаточно просто хранить одни буквы без исходного текста слова и выдавать эти буквы клиенту.  
А клиент должен сам из букв получить текст слова и текст маски:
```java
class Client {
  public void someMethod() {
    String text = toText(word);
    String mask = toMask(word, MASK_SYMBOL);
    //...
  }
}

private String toMask(Word word, char MASK_SYMBOL) {
  StringBuilder sb = new StringBuilder(); 
  for(Letter letter : word.getLetters()) {
    if(letter.isOpen) {
       //...  
    } else {
      sb.append(MASK_SYMBOL);
    }
  }
  return sb.toString();
}
```

Сейчас по этому принципу у тебя в программе и печатается маска слова: 
```java
public class ConsoleUI implements GameUI {

  //...  
  public void displayWordMask(List<Letter> stateWord) {
    //формирует из letters через StringBuilder маску и печатает её 
  }
}
```

В этом случае `Word` это **настолько чистая и независимая от представления модель**, что не содержит в себе даже символ маски закрытой буквы.

Если же класс `Letter` это просто удобная для внутренней организации класса `Word` сущность, то клиенты вообще не должны видеть буквы:
```java
class Word {
  private final List<Letter> letters;
  //...

  public String getMask() {
    StringBuilder sb = new StringBuilder(); 
    for(Letter letter : word.getLetters()) {...}
    return sb.toString();
  }

  public String getText() {
    StringBuilder sb = new StringBuilder(); 
    for(Letter letter : word.getLetters()) {...}
    return sb.toString();
  }
}
```

В этом случае неизбежно возникнет вопрос- а нужен ли вообще класс `Letter`?  
Нельзя ли сделать проще: 
```java
class Word {
  public static final char MASK_SYMBOL = '*';

  private final String text;
  private final StringBuilder mask;
  //....

  public String getText() {...}
  public StringBuilder getMask() {...}
}
```

Короче, нужно либо показывать клиенту `letters` и не показывать текст и маску.  
Либо показывать клиенту текст и маску, но не показывать `letters`.

- Метод обращается к полю объекта, который ещё только создаётся в конструкторе:
```java
public class Word {
  private final String secretWord;
  private final List<Letter> stateWord;

  public Word(String secretWord) {
    this.secretWord = secretWord;
    this.stateWord = createMask();
  }

  private List<Letter> createMask() {
    List<Letter> state = new ArrayList<>(secretWord.length());
    //...
    return state;
  }
  //...
}

//ПРАВИЛЬНО:
public class Word {
  private final String text;
  private final List<Letter> letters;

  public Word(String text) {
    this.text = text;
    this.letters = createLetters(text);
  }

  private List<Letter> createLetters(String text) {
    List<Letter> letters = new ArrayList<>(text.length());
    //...
    return letters;
  }
  //...
}
```

- Разделяй команды и запросы. 

Этот метод совмещает команду и запрос.  
Команда: произвести некие действия.  
Запрос: сказать, что произошло в результате произведенных действий
```java
public boolean makeGuessLetter(char letterUser) {
  //Команда: открывает букву в слове
  //Запрос: была ли открыта буква    
}
```

Метод должен или выполнять команду, или отвечать на запрос.  
Здесь метод нужно разделить на два:
```java
public void openLetter(char letter) {
  //Открывает letter в слове
  //Если такой буквы в слове нет- бросить исключение
}

public boolean contains(char letter) {
  //true если letter есть в слове    
}
```

*Мартин, "Чистый код", гл.3, "Разделение команд и запросов"* 
```java
"Либо функция изменяет состояние объекта, либо возвращает информацию об этом объекте. 
Совмещение двух операций часто создает путаницу." - Мартин.
```

Принцип разделения команд и запросов это общее правило.  
Если ты считаешь, что в данном конкретном случае совмещение команды и запроса делает код проще и понятнее, то совмещай.  
Но здесь явно не тот случай- тут всё просто и нет необходимости говнокодить.

+ 👍 Возвращаешь не оригинал, а копию, это хорошо
```java
public List<Letter> getStateWord() {
  return new ArrayList<>(stateWord);
}
```

+ 👍 С точки зрения SRP мне этот класс нравится.

В классе почти нет ничего лишнего.  
Ещё это чистая модель- класс не зависит от представления ни прямо, ни косвенно- даже символ маски не хранит.

**5. class Game**

+ 👍 Этот класс- движок игры, движок это хорошо.

Движок это вся игровая логика без взаимодействия с представлением.  
Игровая логика с взаимодействием с представлением в таких случаях выносится в отдельный контроллер.  

- Камон, это не letters
```java
private final Set<Character> usedLetters = new HashSet<>();
```
Введя в проект кастомный класс `Letter`, ты должна использовать слово "Letter" только для объектов этого класса.

- Совмещение команды и запроса
```java
private int loseLife() {
  return countLives--;
}
```

- Не много ли перестраховок?
```java
public class Word {
  private final List<Letter> stateWord;  <-- Оригинал
  //...

  public List<Letter> getStateWord() {
    return new ArrayList<>(stateWord);  <-- Возвращает копию
  }
}

public class Game {
  //...
  public List<Letter> getStateWord() {
    return new ArrayList<>(word.getStateWord());  <-- Возвращает копию копии
  }
}
```

- Кеширование состояния игры.

Нет смысла вводить какие-то промежуточные состояния игры, рассчитывать их и потом юзать
```java
private GameState state;

private void updateState() {
  if (countLives == 0) {
    state = GameState.LOSS;
  } else if (word.isSolved()) {
    state = GameState.WIN;
  }
}
```
Просто зачем? Это только делает алгоритм более громоздким.

Избавься от посредников и от кешировния, напрямую пользуйся соответствующими методами:
```java
public boolean isWin() {
  return word.isSolved();    
}

public boolean isLose() {...}

public boolean isGameOver() {
  return  isWin() || isLose();
}
```

* 👻 Тот случай, когда для простоты кода приемлемо совместить команду и запрос:
```java
public GameMoveResult guessLetter(char letter)
```

+ 👍 С точки зрения SRP мне этот класс нравится.

### Пакет provider

По сути, здесь тоже хранятся модели.

**6. interface WordProvider**

+ 👍 Интерфейс словаря это хорошо.  
Можно делать разные его реализации, которые будут получать слова из разных источников(файл, http, из памяти etc)
```java
@FunctionalInterface
public interface WordProvider {
  String chooseWordRandom();
}
```

+ 👍 Внимание к деталям- прекрасно
```java
@FunctionalInterface
```

**7. class FileWordProvider implements WordProvider**

🚀 Боже, как это прекрасно: 
```java
try (BufferedReader fileReader = Files.newBufferedReader(path, StandardCharsets.UTF_8)) {
  return fileReader.lines().toList();
} catch (IOException e) {
  String absolutePath = path.toAbsolutePath().toString();
  throw new UncheckedIOException(ERROR_READ_FILE.formatted(absolutePath), e);
}
```
И хорошо выбран тип непроверяемого исключения, и указан абсолютный путь к файлу, который не удалось прочитать.

Помнится, один персонаж в чате [скептически оценивал](https://t.me/zhukovsd_it_chat/53243/265922) саму возможность использования абсолютного пути.
А тут, оказывается, в этом совсем нет ничего сложного. 

- Что насчёт русского?

Только возникает вопрос:  
Если этот эксепшен перехватить наверху, остановить работу игры и распечатать об этом сообщение на понятном юзеру языке, то есть на русском, 
то как вытащить из месседжа этого `UncheckedIOException` путь к файлу?

Сейчас в случае проблем с файлом, юзер видит такое сообщение:
```java
Выберите действие: 1
A critical error has occurred! Failed to open file containing words at specified path 'C:\MyReviews\PROG\Hm-Dasha Akvamarinika-\hangman-main\russian-words.txt_fuck'. The program will terminate.
```

Но если остальной визуальный интерфейс на русском, то логично юзеру и это сообщение показывать на русском. 

Заранее предупреждаю: я не предлагаю текст месседжа В САМОМ `UncheckedIOException` делать на русском языке - это было бы неправильно.

### Пакет ui

**8. ConsoleUI implements GameUI**

+ 👍 Вроде всё ок.

**9. record RussianText() implements GameText**

- Неправильное использование типа данных `record`.

Тип `record` задуман как простой контейнер данных, он должен использоваться именно в этом качестве.  
У record'ов не должно быть сложного поведения: [JEP 395: Records](https://openjdk.org/jeps/395)
```
Goals
Devise an object-oriented construct that expresses a simple aggregation of values.
Help developers to focus on modeling immutable data rather than extensible behavior.
```

Если нужен класс с развитым поведением, то просто создай обычный класс.

**10. class HangmanArt**

- Неправильная реакция на ошибки. 

Есть три основных реакции на ошибки: игнорирование, исправление, падение.  
Здесь выбрано исправление- при получении некорректного номера картинки, метод подгоняет номер под ближайший корректный:
```java
public static String getArt(int countLives) {
  if (countLives < 0) {
    return STAGES[0];  <-- Возвращает картинку не запрошенного неправильного номера, а ближайшего правильного номера
  }

  if (countLives >= STAGES.length) {
    return STAGES[STAGES.length - 1];   <-- Возвращает картинку не запрошенного неправильного номера, а ближайшего правильного номера
  }

  return STAGES[countLives];
}
```

Правильная реакция должна быть другой, нужно сделать падение:
```java
public static String getArt(int countLives) {
  return STAGES[countLives];  <-- КИНЕТ ИСКЛЮЧЕНИЕ, ЕСЛИ КАРТИНКИ С ТАКИМ НОМЕРОМ НЕТ В МАССИВЕ
}
```
Потому что запрос картинки с неправильным номером означает, что в алгоритме игры есть баг.  
Правильно работающий алгоритм не будет пытаться получить картинку с несуществующим номером.

Этот баг не нужно маскировать.  
Это приведет только к тому, что игра будет происходить некорректно- картинка не будет соответствовать количеству ошибок.  
Баг нужно максимально быстро выявить и устранить.

### Пакет core

**11. class GameRunner**

+ 👍 Этот класс- движок игры, движок это хорошо.

Движок это вся игровая логика без взаимодействия с представлением.  
Игровая логика с взаимодействием с представлением в таких случаях выносится в отдельный контроллер.  
Здесь таки и сделано: движок это `class Game`, а контроллер- `class GameRunner`.

- Класс должен принимать в себя экземпляр Game, а не конструировать его внутри себя:
```java
public class GameRunner {
  private final GameUI gameUI;
  private final WordProvider wordProvider;

  public GameRunner(GameUI gameUI, WordProvider wordProvider) {...}
  //...  
}

//ПРАВИЛЬНО:
public class GameRunner {
  private final Game game;
  private final GameUI gameUI;

  public GameRunner(Game game, GameUI gameUI) {...}
  //...
}
```

## ВЫВОД

В программе хорошо выполнена ООП декомпозиция.  
Программа соответствует архитектуре MVC: классы грамотно разделены на контроллер, модели и представления.  

Благодаря использованию интерфейсов, в программе есть точки расширения.  
Например, для языковой локализации.

👍 Пример хорошей ООП реализации проекта.

n.196(376)  
#ревью #виселица #оопвиселица #mvc 