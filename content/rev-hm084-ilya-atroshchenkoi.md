https://github.com/atroshchenkoi/Hangman  
[Илья]

Харошая ООП реализация, есть расширенный функционал.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

Не принимает русские буквы русского алфавита
```
Введите букву русского алфавита (а-я)!
Ф
Ошибка: допустимы только буквы русского алфавита (а-я).
```

## ХОРОШО

+ 👍 Игра запускается 
+ 👍 Есть список введенных букв
+ 👍 Широко применяются стримы
+ 🚀 Есть языковая локализация UI и отгадываемых слов: ru, en

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Избыточный контекст. И так понятно, что метод чтения в интерфейсе ЧитательСлов читает слова
```
WordReader.readWords();

//ЛУЧШЕ:
WordReader.read();
```

- Избыточный контекст. Каждое слово в названии должно как-то лучше помогать пониманию сути того, что делает метод. Слово "Raw" здесь ничего не добавляет к пониманию, это слово-паразит.  
"Input" ничего не добавляет к пониманию, это слово по смыслу дублирует "read" 
```
public interface GameReader {
  String readRawInput();
}

//ЛУЧШЕ:
public interface GameReader {
  String read();
}
```

- Избыточный контекст
```
boolean isValidCharLetter(char letter);

//ЛУЧШЕ:
boolean isValidLetter(char letter);
```

- Но если в проекте есть класс Word то названия прочих классов/интерфейсов, включающие в себя это название, должны иметь отношение к классу Word. 
Возвращающие методы, которые включают в себя название "Word", должны возвращать экземпляры класса Word
```
public class Word {...}

public interface WordReader {
  List<String> readWords();  <-- метод с таким названием должен возвращать экземпляры класса Word
}

//ПРАВИЛЬНО:
public class Word {...}

public interface TextReader {
  List<String> read();
}
```

- Не называй метод "run" если он не реализует `Runnable.run()`- название этого метода ассоциируется с этим стандартным интерфейсом и применением потоков. 
Здесь этот метод не имеет отношение к `Runnable.run()`
```
GameLoop.run();

//ПРАВИЛЬНО:
GameLoop.start();  //go(), execute(), perform(), поехали() 
```
*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*  

**2. Нарушение DRY**, магические буквы, числа, слова. Вводи константы
```
public static final Set<String> SUPPORTED_CODES = Set.of("ru", "en");

//...
return switch (code) {
  case "ru" -> //...
  case "en" -> //...
}

//ПРАВИЛЬНО:
private final static LNG_RU = "ru";
private final static LNG_EN = "en";
public static final Set<String> SUPPORTED_CODES = Set.of(LNG_RU, LNG_EN);

//...
return switch (code) {
  case LNG_RU -> //...
  case LNG_EN -> //...
}
```
*Фаулер, "Рефакторинг", гл.8 п."Замена магического числа символической константой"*   
*refactoring.guru "Замена магического числа символьной константой"*  

**3. Нарушение DRY**, магические буквы, числа, слова. Вводи константы.  
Если к одной и той же магической штуке обращаются два разных класса, то делай ее константой в одном из этих двух классов, либо перенеси ее в третий класс
```
public class RussianMessageProvider implements MessageProvider {
  @Override
  public String promptMainMenu() {
    return "Введите 1, чтобы начать новую игру!\nВведите 2, чтобы выйти из приложения!";  <-- (!)
  }

  @Override
  public String errorInvalidCommand() {
    return "Ошибка: допустимы только команды 1 и 2.";  <-- (!)
  }
  //...
}

public class GameLoop {
  //...
  public void run() {
    //...
    if (command == '1') {...}  <-- (!)
    if (command == '2') {...}  <-- (!)
    //...
  }

  private Optional<Character> validateInputCommand(String input, MessageProvider messageProvider) {
    //...
    if (ch != '1' && ch != '2') {...}  <-- (!)
    //...
  }
}
```

**4. Создавай вспомогательные методы**, делай программу более простой и понятной
```
while (errorCount < MAX_ERROR_COUNT && word.getLetters().stream().anyMatch(l -> !l.isVisible())) {...}

//ПРАВИЛЬНО:
while (isНазваниеКотороеВсеОбъясняет(/*args or empty*/)) {...}

private boolean isНазваниеКотороеВсеОбъясняет(/*args or empty*/) {
  return errorCount < MAX_ERROR_COUNT && word.getLetters().stream().anyMatch(l -> !l.isVisible());
}
```

**5. Текст в exception** всегда должен быть на английском языке
```
throw new IllegalStateException("Слова не загружены!");
```
Исключение это не просто телеграмма, которая летит сквозь слои. 
У exception особое назначение- если исключение вылетит и не будет перехвачено внутри программы, то аварийно прекратит выполнение программы.
Тогда на экране будет распечатано сообщение эксепшена, и это сообщение должно быть понятно сисадмину в любой точке планеты. 
А значит, сообщение должно быть на английском. 
Интерпритация исключения и перевод его на локальный язык должно происходить там, где это соответствует архитектуре программы или не происходить вовсе, если исключение не планируется перехватывать

**6. Если в блоке if есть return**(break, continue, throw, exit и т.д.), то else не пишется - в этом случае неважно, будет else или нет, так как программа будет работать одинаково и код выглядит читабельней
```
if (words.isEmpty()) {
  throw new IllegalStateException("Не найдены подходящие слова после загрузки!");
} else {
  wordsLoaded = true;
}

//ПРАВИЛЬНО:
if (words.isEmpty()) {
  throw new IllegalStateException("Не найдены подходящие слова после загрузки!");
} 
wordsLoaded = true;
```

**7. Не пиши область видимости в интерфейсах**, эта область видимости там всегда `public`
```
public interface LanguageValidator {
  public boolean isValidCharLetter(char letter);
}

//ПРАВИЛЬНО:
public interface LanguageValidator {
  boolean isValidCharLetter(char letter);
}
```

**8. Приоритет- простота и понятность**. Не пытайся засунуть все в одну строку в ущерб читабельности
```
writer.outputMessage(languageContext.getMessageProvider().promptMainMenu());

//ПРАВИЛЬНО:
String menuMessage = languageContext.getMessageProvider().promptMainMenu();
writer.outputMessage(menuMessage);
```

**9. class Letter implements Comparable<Letter>**

👍 Альтернативный взгляд на декомпозицию. Здесь замаскированное слово имеет в своей основе сущность "Буква". 
Буква здесь это символ, который может быть скрыт или открыт. Сущность ок.

**10. class Word**

+ 👍 Еще одна сущность, которая правильно декомпозирована. Слово здесь это сущность, которая состоит из Букв.

- При попытке открыть букву, которой нет в тексте слова, нужно бросать исключение
```
public void revealLetter(Letter letter) 
```

**11. interface WordReader + class FileWordReader implements WordReader**

👍 Интерфейс ридера текста и его реализация, которая читает текст из файла, это хорошо
```
public interface WordReader {
  List<String> readWords();
}
```
Благодаря интерфейсу, можно делать разные реализации, которые могут читать слова не только из файла, но по сети или просто из памяти.  
Например, может быть такая реализация для тестирования игры
```
public class InMemoryWordReader implements WordReader {
  @Override
  public List<String> readWords() {
    return List.of("инкапсуляция", "наследование", "полиморфизм", "народность");
  }
}
```

**12. Хранение слов в файле**

Сейчас русские и английские слова хранятся в одном файле `words.txt`
```
молоко весна ...
apple water ...
```
Это делает загрузку слов конкретного языка более сложной без всякой на то необходимости.  
Слова из файла считываются все скопом, а потом фильтруются под конкретный язык с помощью валидатора.

При большом количестве слов и добавлении других языков, размер этого файла будет драматически расти, а пользоваться файлом станет невозможно.  
Потому что есть языки, которые используют одинаковый алфавит. А значит нельзя будет с помощью валидатора понять, где слово на английском языке, а где на итальянском; где на русском, а где на монгольском.
Слова под каждый язык нужно хранить в отдельном файле
```
words.txt

//ПРАВИЛЬНО:
ru_words.txt
en_words.txt
```

**13. Простые фабрики**

Фабрики- это хорошо. Простые статические фабрики, как и любой другой утилитный класс, должны быть final и иметь приватный конструктор чтобы нельзя было унаследоваться или сделать экземпляр класса.

**14. Интерфейсы ввода-вывода и их консольные реализации**

👍 Интерфейсы для ввода-вывода `GameWriter`, `GameReader` и их консольные реализации это хорошо. Фактически, это слой **view**, который позволяет работать с разными средами ввода-вывода. 

**15. class WordPool**

Фактически, это сервис для `WordReader`- читает через него слова и выдает одно случайное из них.

- Класс может проводить валидацию слов на соответствие заданному алфавиту, но класс не должен *сам отбирать* слова из `WordReader` руководствуясь валидатором- это нарушение SRP.  
Если хочется застраховать программу от ошибочных слов, должно быть так: 
  * пул слов считывает слова из ридера
  * валидирует каждое слово 
  * если слово не проходит валидацию- бросает исключение.  
  * Дальше программист должен разобраться, почему в списке слов затесалось слово, которое содержит неконвенционные символы.

- Сложные правила использования класса: сначала нужно создать экземпляр, затем вызвать загрузку слов, затем только получить слово методом `Word getRandomWord()`.  
Правильно: создать экземпляр, получить слово методом `Word getRandomWord()`
```
private boolean wordsLoaded;
private List<Word> words;

public WordPool(WordReader reader) {
  this.reader = reader;
  wordsLoaded = false;
}

public Word getRandomWord() {
  if (!wordsLoaded) {
    throw new IllegalStateException("Слова не загружены!");
  }
  return words.get(random.nextInt(words.size()));
}

//ПРАВИЛЬНО (1):
private List<Word> words;

public WordPool(WordReader reader) {
  this.words = reader.read;
}

public Word getRandomWord() {
  return words.get(random.nextInt(words.size()));
}

//ПРАВИЛЬНО (2):
public WordPool(WordReader reader) {
  this.reader = reader;
}

public Word getRandomWord() {
  if(words == null) {
    words = reader.read();
  }  
  return words.get(random.nextInt(words.size()));
}
```

**16. Валидаторы**

- Наличие двух валидационных метода в одном интерфейсе валидатора- дискуссионно. Вижу здесь нарушение SRP
```
public interface LanguageValidator {
  public boolean isValidCharLetter(char letter);

  public boolean isValidStringWord(String word);
}
```
Я бы сделал так
```
public interface Validator<T> {
  public boolean isValid(T value);
}

public class RuLetterValidator implements Validator<Character> {
    public boolean isValid(Character value) {...}
}
public class RuStringValidator implements Validator<String> {
    public boolean isValid(String value) {...}
}
```

- Нарушение DRY
```
public class RussianLanguageValidator implements LanguageValidator {
  @Override
  public boolean isValidCharLetter(char letter) {
    return ((letter >= 'а' && letter <= 'я') || letter == 'ё');
  }

  @Override
  public boolean isValidStringWord(String word) {
    return word.matches("[а-яё]+");
  }
}

public class EnglishLanguageValidator implements LanguageValidator {
  @Override
  public boolean isValidCharLetter(char letter) {
    return (letter >= 'a' && letter <= 'z');
  }

  @Override
  public boolean isValidStringWord(String word) {
    return word.matches("[a-z]+");
  }
}
```
В родственных классах выноси общий код в предка
```
public class LanguageValidatorImp implements LanguageValidator {
  private final String regex;

  public LanguageValidatorImp(String regex) {
    this.regex = regex;
  }

  @Override
  public boolean isValidCharLetter(char letter) {
    return isValidStringWord(String.valueOf(letter));
  }

  @Override
  public boolean isValidStringWord(String word) {
    return word.matches(regex);
  }
}
```

Это позволит легко делать потомков под любые языки
```
public class EnLanguageValidator extends LanguageValidatorImp {
  private final static String REGEX = "[a-z]+";

  public EnLanguageValidator() {
    super(REGEX);
  }
}

public class RuLanguageValidator extends LanguageValidatorImp {
  private final static String REGEX = "[а-яё]+";

  public RuLanguageValidator() {
    super(REGEX);
  }
}
```

Ну, или не делать отдельный валидатор для отдельного языка, а просто сделать простую фабрику
```
public final class LanguageValidatorFactory {
  private LanguageValidatorFactory() {}

  public static LanguageValidator get(Language language) {
    return switch (language) {
      case RU -> new LanguageValidatorImp("[а-яё]+");
      case EN -> new LanguageValidatorImp("[a-z]+");
      case JP -> new LanguageValidatorImp("[ぁ-ゟ]+");
      default -> throw new IllegalArgumentException("illegal language: " + language);
    };
  }
}

public enum Language {
  RU, EN, JP
}
```

Второй вариант мне видится предпочтительнее потому, что все равно придется в том или ином виде делать фабрику валидаторов вне зависимоти от того, будет один валидатор или семейство валидаторов
```
public final class LanguageValidatorFactory {
  private LanguageValidatorFactory() {}

  public static LanguageValidator get(Language language) {
    return switch (language) {
      case RU -> new RuLanguageValidator();
      case EN -> new EnLanguageValidator();
      case JP -> new JpLanguageValidator();
      default -> throw new IllegalArgumentException("illegal language: " + language);
    };
  }
}
```

**17. class LanguageContext**

Просто контейнер для двух классов
```
public class LanguageContext {
  private final LanguageValidator validator;
  private final MessageProvider messages;
  //...
}
```
От класса больше вреда: он делает структуру программы сложнее, а пользы никакой не приносит. Фактически, антипаттен "Полтергейст".  
Ниже попробуем придумать, как обойтись без него.

**18. class GameLoop**

Избыточно сложный принцип ввода команды: вводится команда, валидируется, возвращается опшинал, дальше вызывающий код анализирует этот опшинал и т.д.
```
private Optional<Character> validateInputCommand(String input, MessageProvider messageProvider)
``` 

Упрости, сделай универсальный метод для ввода команд. Этот метод может вернуть только валидную команду, а значит, ему не нужен `Optional`
```
private String inputCommand(MessageProvider messageProvider, String... keys) {
  String title = messageProvider.promptMainMenu();
  writer.outputMessage(title);
  while (true) {
    String s = reader.readRawInput();
    for(String key : keys) {
      if(s.equalsIgnoreCase(key)) {
        return s;
      }
    }
    String failMessage = messageProvider.errorInvalidCommand();
    writer.outputMessage(failMessage);
  }
}
```

**19. class Game**

- Ввод буквы вынеси в отдельный метод типа такого
```
private char inputLeter() {
    while(true) {
        //бесконичный цикл, пока не введут строку
        //длинной в 1 букву нужного алфавита
        if (...) {
            return letter;
        }
    }
}
```

И тогда вместо
```
while (inputCharOptional.isEmpty()) {
   writer.outputMessage(messageProvider.promptLetterInput());
   String input = reader.readRawInput();
   inputCharOptional = validateInput(input);
}
и т.д.
```

будет более понятно, что происходит
```
char letter = inputLeter();
if(isБукваУжеИспользована(letter)) {
  //буква уже использована  
} else if(isБукваЕстьВслове(letter)) {
  //буква есть в слове
} else {
  //буквы нет в слове  
}
```

- Множество ответственностей у метода, побочные эффекты. Метод обещает только вставить букву, но вместо этого метод вставляет букву, ведет считчик ошибок, выдает информационные сообщения  
```
private void putLetter(Letter letter) {
  enterLetters.add(letter);
  if (word.containsLetter(letter)) {
    word.revealLetter(letter);
    writer.outputMessage(messageProvider.correctLetterMessage(letter.getValue()));
  } else {
    errorCount++;
    writer.outputMessage(messageProvider.incorrectLetterMessage(letter.getValue()));
  }
}
```

- Делай вспомогательные методы, которые помогут лучше понимать, что происходит. Например
```
while (!isGameOver()) {
  //...
  if (isWin()) {
    //выиграл
  } else if (isLose()) {
    //проиграл
  }
}
//...

private boolean isGameOver() {
  return isWin() || isLose();
}
```

**20. class Main**, содержит точку входа main

👍 Только создает и запускает игру `GameLoop`, это хорошо.

## АРХИТЕКТУРА

+ 👍 Есть понимание правильной декомпозиции. Базовые сущности `Word` и `Letter` выявлены и хорошо сделанны в виде классов.  
+ 👍 Есть классы, которые можно отнести к слою `view`, а значит можно делать разные представления. Например, на разных языках.
+ 👍 Широко применяются интерфейсы, что делает программу более гибкой. Наример, можно сделать реализацию интерфейса `Ридер` и получать слова не только из файла.

Возможно, некоторые вещи я сделал бы иначе.  
Например, в программе есть разные языки. Значит первым делом нужно сделать эту сущность
```
public enum Language {
  RU, EN, JP
}
```

И дальше уже отталкиваться от этой сущности при конфигурировании приложения в майне
```
public static void main(String[] args) {
  GameWriter gameWriter = new ConsoleGameWriter();
  GameReader gameReader = new ConsoleGameReader();

  Language language = inputLanguage();
  
  String patch = FILE_DIRECTORY + getFilename(language);
  WordReader wordReader = new FileWordReader(patch);
  WordPool wordPool = new WordPool(wordReader);
  
  MessageProvider messageProvider = MessageProviderFactory.get(language);
  LanguageValidator languageValidator = LanguageValidatorFactory.get(language);
  //oth's 

  GameLoop gameLoop = new GameLoop(gameReader, gameWriter, wordPool, messageProvider, languageValidator, /*oth's args*/);
  gameLoop.run();
}

public static String getFilename(Language language) {
  return language.name().toLowerCase() + "_words.txt"; // "RU" -> "ru_words.txt";  
}
```

При большом количестве передаваемых аргументов в конструктор класса нужно рассмотреть возможность использования объектов конфигурации или билдера: *Мартин "ЧК", гл. 3, "Объекты как аргументы"*.

## ВЫВОД

В целом, ок.

n.84(159)  
#ревью #виселица #оопвиселица 