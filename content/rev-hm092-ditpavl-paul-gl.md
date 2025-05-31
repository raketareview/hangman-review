https://github.com/paul-gl/hangman-game    
[Dit Pavl]

Игра в ООП стиле. Есть расширенный функционал.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

Не обнаружено.

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву выбранного языка
+ 👍 Есть список введенных букв
+ 👍 Можно использовать подсказки
+ 🚀 Языковая локализация: можно выбрать английский или русский язык интерфейса и словаря
+ 👍 Прикольный логотип с псевдографикой как в консольных играх 90-х 

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Если в проекте есть класс Word то все переменные с именем, включающим это название, должны быть экземплярами этого класса
```
public class Word {
  private final String actualWord;
  private final StringBuilder hiddenWord;
  //...
}

//ПРАВИЛЬНО:
public class Word {
  private final String text;
  private final StringBuilder mask;
  //...
}
```

- Не сокращай без нужды. Здесь нужды нет
```
GallowsStage(String rep) <-- repository? report? republic?

//ПРАВИЛЬНО:
GallowsStage(String representation) 
```

- В названии переменных не пиши тип данных, к которым они относится. И вообще не употребляй венгерскую ноттацию
```
Map<Character, List<Integer>> lettersMap

//ПРАВИЛЬНО:
Map<Character, List<Integer>> letters
```

- Название обманывает. Этот метод не инициализирует(внутри себя), он создает и отдает некие данные
```
public class LogoInitializer implements Initializer<String> {
  public String initialize() {
    String logo = getLogoFromFile();
    //...
    return logo;
  }
}

//ПРАВИЛЬНО: get() или create()
```

- Стандартным явлениям давай стандартные имена. "Инициализаторы" в проекте это фактически фабрики
```
public interface Initializer<T> {
  T initialize();
}

//ПРАВИЛЬНО:
public interface Factory<T> {
  T get();
}
```
Если назвать инициализаторы фабриками, то многое в проекте станет более понятным. 

- Название обманывает. Это не валидатор, это инпутер
```
class InputValidator<T>

//ПРАВИЛЬНО:
class Input<T>
```

- В названии переменных не пиши тип данных, к которым они относится. И вообще не употребляй венгерскую ноттацию
```
char inputChar = Character.toUpperCase(input.charAt(0));
char hintChar

//ПРАВИЛЬНО:
char symbol = Character.toUpperCase(input.charAt(0));
char hint
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*  

**2. Нарушение конвенции кода**

Везде переменные расположены абы как: константы ниже полей классов или вперемешку с ними.

**3. Если в блоке if есть return**(break, continue, throw, exit и т.д.), то else не пишется - в этом случае неважно, будет else или нет, так как программа будет работать одинаково и код выглядит читабельней
```
if (wordOfRound.isRevealed()) {
  return GameLoopState.PLAYER_WON;
} else if (remainedLives <= 0) {
  return GameLoopState.PLAYER_LOST;
}

//ПРАВИЛЬНО:
if (wordOfRound.isRevealed()) {
  return GameLoopState.PLAYER_WON;
} 
if (remainedLives <= 0) {
  return GameLoopState.PLAYER_LOST;
}
```

**4. Создавай вспомогательные методы**, делай программу более простой и понятной
```
while (loopState.equals(GameLoopState.IN_PROGRESS)) {...}

//ПРАВИЛЬНО:
while (isНазваниеКотороеВсеОбъясняет()) {...}

private boolean isНазваниеКотороеВсеОбъясняет() {
  return loopState.equals(GameLoopState.IN_PROGRESS);
}
```

**5. Если нужно печатать или создавать строку** с более, чем одним подстановочным значением или значение вставляется внутрь сообщения, используй форматированный вывод- тогда сразу будет виден весь шаблон
```
System.out.println("Could not locate file \"" + LOGO_PATH + "\"");

//ПРАВИЛЬНО:
System.out.printf("Could not locate file \"%s\" %n", LOGO_PATH);
```
Хотя конкретно в этом случае лучше было бы просто сделать вот так
```
System.out.println("Could not locate file: " + LOGO_PATH);
```

**5. class Word**

+ 👍 Хорошо, что выявил сущность "Секретное слово" и сделал его классом. 

- Нарушение SRP. Хранение категории, к которому относится слово, это не ответственность самого слова. Можно об этом конечно подискутировать, но мое мнение таково.
```
private final String category;
```

- Сложная система внутреннего устройства.

Здесь текст загаданного слова переводится в мапу, где ключом является буква, а значенем список цифр-позиций расположения буквы в слове.  
Все это приводит к ненужным сложностям. 
Намного проще использовать стандартный подход, при котором для работы с буквами применяется последовательный перебор букв в слове.  

Возможно, при использовании мапы алгоритм будет работать немного быстрее. Но для такой простой задачи это не играет никакой роли, зато алгоритм становится хуже читабелен в разы 
```
//СЕЙЧАС- ПРИ ИСПОЛЬЗОВАНИИ МАПЫ 
public void revealLetter(char letter) {
  List<Integer> indexes = hiddenLetters.get(letter);  <-- HashMap
  if (indexes != null) {
    for (int idx : indexes) {
      hiddenWord.setCharAt(idx, letter);
    }
    hiddenLetters.remove(letter);
    uniqueHiddenLetters = getUniqueHiddenLetters();
  }
}

private Map<Character, List<Integer>> getLettersMap() {
  //метод перевода текста в мапу, 16 строк
}

//СТАНДАРТНЫЙ ПОДХОД
public void openLetter(char letter) {
  //перевести letter в нужный регистр  
  for(int i = 0; i < actualWord.length(); i++) {
    if(actualWord.charAt(i) == letter) {  //actualWord тут исходный текст слова
      hiddenWord.setCharAt(i, letter);    //hiddenWord тут маска слова, StringBuilder
    }
  }
}
```

- При открытии буквы в `revealLetter(char letter)`, если такой буквы нет- нужно кидать исключение. 
Букву нужно открывать только в том случае, если перед открытием соответствующим методом проверили, что такая буква там есть.  

- toString() должен быть стандартным(как делает IDE по Alt+Ins) и использоваться только для отладки. 
toString() должен содержать значение всех значимых полей класса.  
toString() не должен использоваться для представления, за исключением предельно простых классов, вроде PhoneNumber.  
Здесь значимые поля это не только `String actualWord`
```
@Override
public String toString() {
  return actualWord;
}

//ПРАВИЛЬНО:
@Override
public String toString() {
  return "Word{" +
    "category='" + category + '\'' +
    ", actualWord='" + actualWord + '\'' +
    ", hiddenWord=" + hiddenWord +
    ", hiddenLetters=" + hiddenLetters +
    ", uniqueHiddenLetters=" + uniqueHiddenLetters +
  '}';
}
```
*Блох, "Java эффективное программирование" гл.3.3* 

**6. Пакет console**

В этом пакете находятся классы не только для работы в консоли.  
Кроме этого здесь находятся нексолько интерфейсов, для которых можно сделать и не консольные реализации: `Reader`, `MessagePrinter` и `LocalizedMessagePrinter`.  
Эти интефейсы не должны находиться в пакете "консоль".

**7. Интерфейсы `Reader`, `MessagePrinter` и `LocalizedMessagePrinter`**

+ 👍 Интерфейсы, формирующие слой `View`, что позволяет делать программу с использованием разных сред ввода-вывода. В данном случае имеется одна среда ввода-вывода, это консоль.

- Но здесь все потенциальные возможности использования интерфейсов обнуляются из-за того, что применяются они неправильно. Но об этом ниже.

**8. class LocalizedMessageProvider**

Нарушение SRP. Метод не должен завершать работу программы через exit()
```
public LocalizedMessageProvider(Language language) {
  try {
    //...
  } catch (RuntimeException e) {
    System.out.println("Error: " + e.getMessage());
    System.exit(1);
  }
}
```
Каждый метод и класс имеют право завершать только свою работу. Например, через return.  
Потому что методы и классы не должны знать логику работы более высоких слоев программы, у которых могут быть свои планы на тему того, когда и почему нужно завершать работу программы в целом.

Возможно, в данном случае здесь прекращать работу это проще. Но с архитектурной точки зрения- хуже.

**9. class ConsoleReader implements Reader**

Из-за того, что в этом классе есть метод `close()`, который отсутствует в интерфейсе `Reader`, то пользоваться классом через его интерфейс нельзя- иначе не получится вызвать этот метод
```
public class ConsoleReader implements Reader {
  //...

  @Override
  public String read() {
    return scanner.nextLine();
  }

  public void close() {
    this.scanner.close();
  }
}
```
Поэтому сейчас ридер инициализируется так
```
ConsoleReader reader = new ConsoleReader();
```
А должен использоваться через интерфейс и инициализироваться так
```
Reader reader = new ConsoleReader();
```
При использовании через `ConsoleReader reader`, как я и писал выше, обнуляются абсолютно все потенциальные преимущества, которые дает использование интерфейса `Reader`. 

**10. class ConsolePrinter implements MessagePrinter, LocalizedMessagePrinter**

Нарушение Low Coupling. Класс не должен знать про `Language` и не должен его получать в конструктор. Он должен получать в конструктор `LocalizedMessageProvider`
```
public ConsolePrinter(Language gameLanguage) {
  provider = new LocalizedMessageProvider(gameLanguage);
}

//ПРАВИЛЬНО:
public ConsolePrinter(LocalizedMessageProvider provider) {
  this.provider = provider;
}
```

**11. Пакет utils**

Пакет `utils` не содержит содержит не одного класса, который можно считать утилитным. Тем более утилитой не могут быть интерфейсы. Гугли, что в java понимается под утилитным классом.

**12. interface Initializer<T>**

Фактически, это интерфейс фабрики.

**13. class WordPoolInitializer implements Initializer<WordPool>**

- Нарушение SRP. Класс создает "хранилище слов" из файла либо из памяти
```
public class WordPoolInitializer implements Initializer<WordPool> {
  //...
  private Map<String, List<String>> getWordsStorageFromFile() {...} <- СЛОВА ИЗ ФАЙЛА
  private Map<String, List<String>> getDefaultWordStorage() {...} <- СЛОВА ИЗ ПАМЯТИ
}
```
Должно быть два родственных класса- один читает слова из файла, другой- из памяти.

Это же касается и класса `LogoInitializer`.

- В классе не должно быть логики "сначала загружу слова из файла, если не смогу этого сделать- загружу дефолтные слова".  
Игра может использовать слова из разных источников, для этого нужно делать разные игровые конфигурации(Main'ы). 
Можно в том числе сделать специальный `Main`, где будет реализована логика "сначала загружу слова из файла и т.д.". Но эта логика не должна здесь реализовываться на уровне `WordPoolInitializer`.

- Если в проекте ты ввел специальные интерфейсы для распечатки информации(`Printer` etc), то всю инфу ты должен распечатывать через них.  
Иначе какой тогда в этих интерфейсах смысл кроме того, что они просто делают программу больше и сложнее?
```
public WordPool initialize() {
  //...
  System.out.println("Loading default storage");  <-- НУЖНО ПЕЧАТАТЬ ЧЕРЕЗ Printer
}
```

**14. enum GallowsStage**, картинки хангмана и метод их распечатки

Использование енама не по назначению- здесь это что-то типа недокласса. Енам должен просто хранить константы, иногда довольно сложные, но не содержать в себе прикладную логику.  
Переделай этот енам на простой класс и назови его `class GallowsRenderer`, картинки храни в приватном массиве.

**15. class GameLoop**, основная игровая логика

- Класс должен работать с принтером и ридером не как с классами, а как с интерфейсами
```
private final ConsolePrinter printer;
//...

public GameLoop(ConsolePrinter printer, ConsoleReader reader, Language language) {
  this.printer = printer;
  //...
}

//ПРАВИЛЬНО:
private final Printer printer;
//...

public GameLoop(Printer printer, Reader reader, Language language) {
  this.printer = printer;
  //...
}
```
Иначе абсолютно бессмысленным становится существование этих интерфейсов.  
А если класс будет работать с интерфейсами, а не реализациями, то это наоборот сулит головокружительные ООП перспективы.  
Тогда можно будет создавать игровые конфигурации, не меняя при этом код класса `GameLoop`.  

Например, в одной конфигурации, принтер будет консольным черно-белым, в другой конфигурации- консольным цветным, в третьей конфигурации- консольным цветным распечатывающим текст псевдографикой, а в четвертой принтер будет печтать информацию куда-то по http.

- Использование енамов состояний ничего хорошего не добавляет программе. 

Это только делает ее больше размером и сложнее, алгорим становится запутанее.
Лучше сделай методы, которые будут определять выигрыш и проигрыш
```
while (loopState.equals(GameLoopState.IN_PROGRESS)) {
  //...
  if (wordOfRound.isRevealed()) {
    loopState = GameLoopState.PLAYER_WON;
    break;
  }
}
//...

enum GameLoopState {NOT_STARTED, IN_PROGRESS, PLAYER_WON, PLAYER_LOST}

//ЛУЧШЕ:
while(!isGameOver()) {
  //...
  if (isWin()) {
    printWinMessage();
  } else if (isLose()) {
    printLoseMessage();
  }
}

private boolean isWin() {
  return wordOfRound.isRevealed();
}
```

**16. class Main**, содержит точку входа main

👍 Только создает и запускает игру, это хорошо.

## АРХИТЕКТУРА

ООП программа должна состоить из классов, которые декомпозированы по правилам ООП и использовать ООП подходы при реализации типичных задач.  
С декомпозицией базовых сущностей тут все более-менее норм- например, класс `Word`.

С ООП подходами при реализации типичных задач все обстоит значительно хуже.  

Иногда здесь осмысленное ООП подменяется карго культом.  
Например, бессмысленные в текущей реализации интерфейсы, которые не инжектятся сверху вниз, а инициализируются в самих классах.  
При этом классы с такими интерфейсами работатют не как с абстракциями, а как с реализациями.

В проекте часто встречаются не ООП, а процедурные подходы. Например, классы-инициализаторы сделаны в процедурном стиле.

**Альтернативный ООП подход рассмотрим на примере Инициализатора пула слов.**

Проблема: нам нужен набор слов для игры, здесь это сгрупировано в некий Пул слов- `class WordPool`. Этот набор слов можно получить или из файла или из памяти.  
Решение: сделать разные фабрики, одна будет считывать слова из файла, вторая- из памяти.

+ Итак, первым делом даем нормальное название инициализатору. Название будет объяснять, что это черт побери такое
```
//БЫЛО
public interface Initializer<T> {
  T initialize();
}

//СТАЛО
public interface Factory<T> {
  T get();
}
```

+ Создаем разные фабрики слов. 
Каждая фабрика будет получать слова из одного конкретного источника. Набор слов зависит от выбранного языка, поэтому фабрики в конструктор должны принимать язык
```
class FileWordPoolFactory implements Factory<WordPool> {
  //...
  public FileWordPoolFactory(language language) {
    //читаем слова из файла
    //если что-то не так- бросаем исключение
  }

  @Override
  public WordPool get() {
    //выдаем пул слов
  } 
}

class InMemoryWordPoolFactory implements Factory<WordPool> {
  //...
  public InMemoryWordPoolFactory(language language) {
    //пустой конструктор
  }

  @Override
  public WordPool get() {
    //выдаем пул слов конкретного языка
    //слова хранятся в константах класса
  } 
}
```

+ Фабрики слов нужно инициализировать в самом верху, например в слое `main`. 

Тем самым можно делать разные игровые конфигурации.  
Например(упрощая):

Первая конфигурация, английские слова из файла
```
public class Main1 {
  private final static Language LANGUAGE = Language.ENGLISH;

  public static void main(String[] args) {
    Factory<WordPool> wordPoolFactory = new FileWordPoolFactory(LANGUAGE);
    WordPool woordPool = wordPoolFactory.get();
    //...
    Game game = new Game(wordPool, ...);
    game.start();
  }
}
```
Вторая конфигурация, русские слова из памяти
```
public class Main2 {
  private final static Language LANGUAGE = Language.RUSSIAN;

  public static void main(String[] args) {
    Factory<WordPool> wordPoolFactory = new InMemoryWordPoolFactory(LANGUAGE);
    WoordPool wordPool = wordPoolFactory.get();
    //...
    Game game = new Game(wordPool, ...);
    game.start();
  }
}
```
Третья конфигурация, язык выбирает юзер, слова грузятся из файла, если ошибка чтения- слова грузятся из памяти
```
public class Main3 {
  public static void main(String[] args) {
    Language language = inputLanguage();
    Factory<WordPool> wordPoolFactory = getWordPoolFactory(Language language);
    WoordPool wordPool = wordPoolFactory.get();
    //...
    Game game = new Game(wordPool, ...);
    game.start();
  }

  private static Language inputLanguage() {
    //юзер выбирает язык
  }

  private static Factory<WordPool> getWordPoolFactory(Language language) {
    try {
      return new FileWordPoolFactory(language);
    }
    catch(Исключение e) {
      return new InMemoryWordPoolFactory(language);
    }
  }
}
```
Ну и моя любимая в таких случаях тестовая конфигурация для отладки- отгадывание заранее известного слова
```
public class MainTest {
  private final static Language LANGUAGE = Language.RUSSIAN;

  public static void main(String[] args) {
    Factory<WordPool> wordPoolFactory = new Factory<WordPool> {
      @Override
      public WordPool get() {
        Map<String, List<String>> map = Map.of("ПРИВЕТ", List.of("ПРИВЕТ"));
        return new WordPool(map);
      }
    }
    WoordPool wordPool = wordPoolFactory.get();  
    // или просто 
    // WoordPool wordPool = new WordPool(Map.of("ПРИВЕТ", List.of("ПРИВЕТ")));
    //...
    Game game = new Game(wordPool, ...);
    game.start();
  }
}
```
В проекте может быть сколько угодно конфигураций-майнов.

## ВЫВОД

Нужно разобраться, зачем нужны интерфейсы и как их необходимо использовать в проектах чтобы они имели смысл, а не были просто для красоты.  
Для лучшего понимания декомпозиции ООП посмотреть ролики Сергея про шахматы. Посмотреть ролики Немчинского про SOLID.

n.92(177)  
#ревью #виселица #оопвиселица #архитектура #локализация #фабрика 