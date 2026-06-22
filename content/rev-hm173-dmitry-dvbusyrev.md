https://github.com/dvbusyrev/hangman_TDD  
[Дмитрий]

Игра в процедурном стиле, выполнена в нескольких классах.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

**1. Совершенно ацкие пустоты**
```java
Word: s - - - - - - -

q w e r t y u i o p
 _ _ _ _ g h j k l
  z x c v b n m

***
>g















***
Topic: animals
```

**2. Нарушение концепции игры.**

Это игра "Хангман", но здесь не рисуются картинки с виселицей.  
Это нарушает всю концепцию игры- потому что весь её прикол состоит в драматизме ситуации, когда каждая новая ошибка приближает смерть несчастного, 
которого каждый игрок подсознательно ассоциирует с самим собой.   

НУЖНО РИСОВАТЬ КАРТИНКИ С ЧЕЛОВЕЧКОМ, КОТОРОГО ПЛАВНО ВЕШАЮТ.  

**3. При проигрыше не показал, какое слово было загадано**
```java
Topic: animals

Difficulty: easy

0 tries left

Word: s q - - r r e l

_ _ _ _ _ _ u i o p
 _ _ _ _ _ _ _ _ _
  z _ _ _ b _ _

***
Game failed
```
А может быть я отгадал слово, а твоя игра неправильно работает- откуда я знаю?

**4. Визуальный интерфейс.**

Извини, но интерфейс некрасивый и неудобный.  
Играть просто неинтересно.

Когда делаешь программу, ставь себя на место юзера и думай, будет ли ему удобно, приятно и интересно пользоваться твоим продуктом.

Да, это тренировочная программа. Но не существует волшебного тумблера "выйду на работу- буду делать нормально".  
Ты либо всегда делаешь нормально, либо никогда не делаешь. 

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву английского алфавита
+ 👍 Есть список введенных букв
+ 🚀 Несколько уровней сложности и тем словаря
+ 🚀 Тесты

## ЗАМЕЧАНИЯ

**1.1. Нейминг**

- Если метод называется "сравнить"(check), то его результат должен быть boolean.  

Если его результат не boolean, значит он не должен так называться.  
В данном случае, этот метод валидирует
```java
private void checkListNotEmpty() {
  if (difficulties.isEmpty()) {
    throw new EmptyListAccessException();
  }
}

//ПРАВИЛЬНО:
private void validate(List<Difficulty> difficulties) {
  if (difficulties.isEmpty()) {
    throw new EmptyListAccessException("blablabla");
  }
}
```

- Почему метод принимает именно форматированную букву?

Если он принимает букву, он должен принимать букву.  
Если с этой буквой что-то предварительно делали, то метод это волновать не должно- он должен что-то делать с *любой* буквой, которую в него кидают
```java
void checkGuess(String formattedLetter)

//ПРАВИЛЬНО:
void checkGuess(char letter)
```

- Название вводит в заблуждение. 

🔹 От метода с названием "input" мы ожидаем получение информации (от кого-то  к нам), а не её передачу (от нас кому-то).  
🔹 Метод может называться так же, как поле класса, только в редких случаях. И только если метод является геттером этого поля, а здесь эти названия похожи до уровня смешения 
```java
private final List<String> inputLetters = new ArrayList<>();

void inputLetter(String letter) {
  //добавляет letter в список введенных
}

//ПРАВИЛЬНО:
void addInputLetter(String letter) {
  //добавляет letter в список введенных
}
```
  
*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**1.2. Нарушение конвенции кода**

- Скобочки.

В любой ситуации выделяй тело блока скобочками, даже если тело состоит из одной строки.  
Исключение- метод equals(), там можно после if не выделять блоки скобочками
```java
if (isFailed()) return GameResult.FAILED;    
if (isWon()) return GameResult.WON;

//ПРАВИЛЬНО:
if (isFailed()) {
  return GameResult.FAILED; 
}   
if (isWon()) {
  return GameResult.WON;
}
```
*"Oracle Java code conventions"* 

**2. Форматирование**

- Мысленные преобразования.

Не вставляй перевод строки в середину строк при распечатке.  
Иначе при чтении кода приходится мысленно "разворачивать" одну строку в несколько.  
Если нужно распечатать несколько строк, то печатай их или многострочными строками(""") или отдельными инструкциями печати.  
Тогда сразу будет видно, как текст будет выглядеть на печати:
```java
System.out.print(triesLeft + " tries left" + "\n\n");

//ПРАВИЛЬНО:
System.out.println(triesLeft + " tries left");
System.out.println();
```

Вот это вообще ацкий ад, мне нужно приложить значительные ментальные усилия, чтобы в воображении развернуть это в 2D:
```java
consoleDisplay.print("***" + "\n" + "Topic: " + game.getTopic() + "\n\n");

//ПРАВИЛЬНО ТАК:
consoleDisplay.println("***");
consoleDisplay.println("Topic: " + game.getTopic());
consoleDisplay.println("");
consoleDisplay.println("");
```

- Напечатать одну строку и перевести каретку на новую- команда `println(...)`
```java
System.out.print(difficultyStringOutput + "***" + "\n");
System.out.print(inputLettersLeft + "\n");

//ПРАВИЛЬНО:
System.out.println(difficultyStringOutput + "***");
System.out.println(inputLettersLeft);
```

**3. Нарушение DRY**, магические буквы, числа, слова. Вводи константы
```java
private static final String MENU =
  """
     0. Start new game
     1. Close menu
     Press 0 or 1
     """;

do {...} while (!input.equals("0") && !input.equals("1"));

//ПРАВИЛЬНО:
private static final String START = "0";
private static final QUIT = "1";

private static final String MENU =
  """
     %s. Start new game
     %s. Close menu
     Press %s or %s
     """.formatted(START, QUIT, START, QUIT);

do {...} while (!isCommand(input));

private boolean isCommand(String value) {
  return value.equals(START) || value.equals(QUIT);
}
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*

**4. Exceptions**

- Делай информативные сообщения.  

Исключения должны всегда содержать в себе сообщение на английском языке, которые описывают причину их возникновения
```java
if (words == null || words.isEmpty() || topic == null || topic.isBlank()) {
  throw new IllegalArgumentException();
}

//ПРАВИЛЬНО:
if (words == null || words.isEmpty() || topic == null || topic.isBlank()) {
  throw new IllegalArgumentException("Illegal word: " + word);
}
```

- Не бросай базовый эксепшен. Конкретизируй ситуацию- бросай то исключение, которое подходит под этот конкретный случай
```java
try (BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(inputStream))) 
{...} catch (IOException e) {
  throw new RuntimeException(e);
}
```
*Хорстманн "Java. Библиотека профессионала", т1., гл.11*
```java
"Не ограничивайтесь генерацией RuntimeException. 
Найдите подходящий подкласс или создайте собственный." - Хорстманн
```

- Проверяемые исключения перехватывай в точке их возникновения и заменяй на непроверяемые. 

Проброс исключений в сигнатуре методов захламляет код.  
Поэтому заменяй проверяемые исключения на непроверяемые  
```java
private Optional<WordsGroup> readWordsWithTopic(BufferedReader bufferedReader) throws IOException {
  try (BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(inputStream))) 
  {...} catch (IOException e) {
    throw new RuntimeException(e);
  }
}

//ПРАВИЛЬНО:
private Optional<WordsGroup> readWordsWithTopic(BufferedReader bufferedReader) {
  try (BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(inputStream))) 
  {...} catch (IOException e) {
    throw new КакоетоНепроверяемоеИсключение(e);
  }
}
```
Изучи различия проверяемых и непроверяемых исключений  
*"ЧК", гл.7, "...проверяемые исключения"*

**5. record WordsFileReader(String filePath)**

- Неправильное использование record'a.

Тип record сделан для того, чтобы в этих классах хранили простые данные, желательно вообще без поведения.  
В крайнем случае- с минимальным поведением.

Здесь record имеет сложное поведение
```java
public record WordsFileReader(String filePath)  {
  public List<String> readTopics()  {...}

  public WordRepository readAllWordsByTopic()  {...}

  private Optional<WordsGroup> readWordsWithTopic(BufferedReader bufferedReader) throws IOException  {...}

  private String formatLine(String line) {...}

  private boolean containsNotOnlyLetters(String line) {...}
}

//ПРАВИЛЬНО:
public class WordsFileReader {
  //...

  public WordsFileReader(String filePath) {...}

  public List<String> readTopics()  {...}

  public WordRepository readAllWordsByTopic()  {...}

  private Optional<WordsGroup> readWordsWithTopic(BufferedReader bufferedReader) throws IOException  {...}

  private String formatLine(String line) {...}

  private boolean containsNotOnlyLetters(String line) {...}
}
```

- Публичные методы должны стоять выше вспомогательных приватных.  
Такая банальная ошибка, как неправильное размещение методов в классах, ухудшает читабельность В РАЗЫ.

- Компилируй регулярные выражения. 

Если регулярное выражение используется многократно, как здесь, то нужно его откомпилировать.  
Компиляция регулярного выражения в объект `Pattern` с помощью `Pattern.compile()` позволяет повысить производительность при многократном использовании
```java
private boolean containsNotOnlyLetters(String line) {
  return !line.matches("[a-zA-Z]+");
}

//ПРАВИЛЬНО:
private static final String REGEX = "[a-zA-Z]+";
private static final Pattern PATTERN = Pattern.compile(REGEX);

private boolean containsNotOnlyLetters(String line) {
  Matcher matcher = PATTERN.matcher(line);
  return !matcher.matches();
}
```

```java
"Хотя String.matches — простейший способ проверки, соответствует ли строка регулярному выражению, 
он не подходит для многократного использования в ситуациях, критичных в смысле производительности." - Блох
```

Учитывая, что регулярка вызывается при чтении каждого слова из файла, её неоптимальное использование тормозит процесс.  

*Блох "Java. Эффективное программирование", изд.3, гл.2.6*

- Отрицательные условия в названиях методов плохо читаются, меняй их на положительные
```java
if (containsNotOnlyLetters(lineFormatted)) {...}

private boolean containsNotOnlyLetters(String line) {
  return !line.matches("[a-zA-Z]+");
}

//ПРАВИЛЬНО:
if (!containsOneLetter(lineFormatted)) {...}

private boolean containsOneLetter(String line) {
  return line.matches("[a-zA-Z]+");
}
```
*"ЧК", гл.17, G29*

- Нарушение DRY, куча прям о или опосредованно дублированного кода.  
Например, файловое чтение в разных методах.

- Магические строки: "*", ".txt".

- Позиционирование класса.

**В программе написанной в ООП стиле каждый класс должен представлять собой какую-то внятную сущность.**

Тут неясно, шо это такое.

У этого класса есть два публичных метода, которые возвращают что-то из разных концепций
```java
public List<String> readTopics()  {...}
public WordRepository readAllWordsByTopic()  {...}
```

Поэтому с точки зрения ООП, этот класс не объект, это контейнер с функциями в стиле процедурного программирования.

Сформулируй для себя, в чем состоит единая ответственность класса и переделай класс так, чтобы он соответствовал своему SRP.  

Например, от класса с названием `WordsFileReader` лично я ожидаю, что он просто прочитает слова из файла и будет выглядеть примерно так:
```java
public class WordsFileReader {
  private final String filePath;

  public WordsFileReader(String filePath) {...}

  public List<String> read();
}
```

**6. class DifficultyRepository**

- Нарушение SRP.

Если класс позиционируется, как репозиторий сложностей, то он и должен хранить(добавлять, возвращать) сложности.  
Определять, как эти сложности будут показаны юзеру- не его забота
```java
public String getStringOutput() {
  checkListNotEmpty();

  StringBuilder output = new StringBuilder();
  for (int i = 0; i < difficulties.size(); i++) {
    output.append(
        String.format(
            "%d. %s, %d hearts\n",
            i,
            difficulties.get(i).name(),
            difficulties.get(i).heartCount()
        )
    );
  }
  return output.toString();
}
```

Модель не должна зависеть от представления.  
Репозиторий это модель. Он хранит чистые данные- сложности.  
То, как сложности будут показаны юзеру- это представление.

ТО ЖЕ САМОЕ КАСАЕТСЯ `class TopicRepository`.

**7. Идеологически одинаковые классы DifficultyRepository и WordRepository**

По сути, эти классы делают одно- хранят КАКИЕ-ТО ШТУКИ.  
И должны обеспечивать одинаковый функционал.  
Например: сказать размер репозитория, вернуть штуку по ее номеру или вернуть случайную штуку.

Для этого эти классы должны быть родственниками через общего предка, где будет находиться общий функционал. 
Например, так:
```java
public abstract class Repository<T> {
  private final List<T> values;
  //...

  public void add(T value) {...}

  public T getBy(int index) {...}

  public int size() {
    return values.size();
  }

  public boolean isEmpty() {
    return values.isEmpty();
  }

  //...
}

public class WordRepository extends Repository<String> {...}
```

При этом репозитории должны быть именно репозиториями.  
То есть хранить данные и обеспечивать к ним только базовый доступ: добавить, прочитать etc.

Любое другое навешивание лишних ответственностей на репозиторий размывает его SRP и делает класс процедурным контейнером для функций.  

**8. class ConsoleDisplay**

+ 👍 Отдельный класс для пользовательского ввода-вывода, это хорошо. Что-то типа Вью.

**9.  class Game**

- Избыточно. 

При создании объекты по умолчанию инициализируются значением `null`, интежеры- `0` и т.д.
```java
public class Game {
  private String topic = null;
  private WordRepository words = null;
  private int mistakes = 0;
  //...
}

//ПРАВИЛЬНО:
public class Game {
  private String topic;
  private WordRepository words;
  private int mistakes;
  //...
}
```

Если вдруг привычка явно инициализировать переменные у тебя идёт из Си, то избавляйся от нее.

- Буква эта одиночный символ, буквы должны быть char'ами
```java
private final List<String> guessedLetters = new ArrayList<>();

//ПРАВИЛЬНО:
private final List<Character> guessedLetters = new ArrayList<>();
```

- Класс должен принимать в себя необходимые зависимости.

Если этот класс это что-то типа движка игры, то он должен принимать необходимые зависимости в конструктор.

Например, секретное слово он должен получать в конструктор, а не брать из репозитория
```java
//СЕЙЧАС ТАК:
public class Game {
  private WordRepository words = null;
  private String secretWord = null;
  private final List<String> guessedLetters = new ArrayList<>();
  private int mistakes = 0;
  //...

  //нет конструктора

  //oths meths  
}

//ПРАВИЛЬНО ТАК:
public class Game {
  private final String secretWord;
  private final List<String> guessedLetters = new ArrayList<>();
  private int mistakes;
  //...

  public class Game(String secretWord, ...) {...}

  //oths meths
}
```

Этот класс должен работать с любым словом, вне зависимости от того, откуда это слово взяли.  
Когда этот класс лезет в репозиторий и достает из него слово, то тем самым он нарушает свое SRP.  

На практике это приводит к тому, что класс становится неуниверсальным и обрастает высокой связностью(нарушает Low Coupling).

+ 👍 В целом, методы в классе маленькие и компактные.

**10. class GameSession**, содержит точку входа main.

- Если класс содержит точку входа main, то этот метод должен стоять первым среди всех.

- Нарушение SRP.

Main должен только сконфигурировать зависимости и запустить программу.  
Управлять работой программы этот класс не должен.  

Выглядеть такой класс должен примерно так:
```java
public class Main {
  public static void main(String[] args) {
    Difficulty difficulty = getDifficulty();
    WordsGroup wordsGroup = getWordsGroup();
    //...
    GameSession gameSession(wordsGroup, difficulty, ...);
    gameSession.start();
  }  
}
```
*Мартин, "ЧК", гл.11, "Отделение конструирования системы от ее использования"*

Определи сущности, которые сейчас сидят в этом классе и вынеси их в отдельные классы.  
Например, сущность, которая управляет движком игры.  
То есть, игровой контроллер.

## АРХИТЕКТУРА

Несмотря на то, что в проекте несколько классов он написан в процедурном стиле. 
Программа в ООП стиле должна быть декомпозирована по правилам ООП и использовать ООП подход для реализации типичных задач.  
Здесь не видно ООП декомпозиции.

Не выявлены и не сделаны в виде классов менее очевидные, чем "Файловое чтение", сущности вроде "Слова".  
Другие классы, в том числе `WordsFileReader` (Файловое чтение) скорее напоминают процедурные модули.

**Есть, конечно, элементы ООП подхода, но на фоне остального процедурного кода они не принципиальны.**

Из ООП признаков я бы выделил разве что движок игры, или по крайней мере, мне он кажется похожим на движок- класс `Game`.  
Этот класс не зависит от представления и содержит общую игровую логику, это хорошо.

Но если этот класс сознательно задумывался как движок, он должен содержать всю игровую логику, которая не зависит от представления.  
Часть которой сейчас находится в похожем на контроллер классе `GameSession`.

`GameSession`, в свою очередь, должен быть тонким контроллером и не содержать в себе точку входа.

Если хочется написать эту игру в ООП стиле, убери из нее уровень сложности и темы слов.  
Потому что не умея строить хорошую ООП архитектуру, лишние сложности делают код еще хуже.

**Приоритет должен состоять в создании хорошей архитектуры, а не в усложнении функционала.**

Это касается не только этого проекта, но и всех остальных.  
Базовый функционал, но в хорошем ООП стиле ппо сравнению с усложнённой плохой программой это как одноэтажный кирпичный дом вместо трехэтажного сарая.  

## ВЫВОД

Нужно глубже вникнуть в суть ООП.

Сравнить различия между ООП и процедурным стилями:  
Стрим Сергея [Крестики-нолики в процедурном стиле](https://www.youtube.com/watch?v=PPikj1qHxrA)  
Мой стрим [Крестики-нолики в ООП стиле](https://t.me/zhukovsd_it_chat/53243/187097)

Подробное объяснение, как делать эту программу в процедурном и ООП стилях, есть у Сергея в расширенных материалах.

n.173(335)  
#ревью #виселица 