https://github.com/klasavch1k/Hangman  
[Андрей]

После первого рефакторинга. Стало ок 👍

Игра написана в ООП-стиле. 

Предыдущее ревью [ТУТ](https://github.com/raketareview/hangman-review/blob/master/content/rev-hm202-andrey-klasavch1k.md).

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Определись, ты с юзером на "вы" или на "ты"
```
Некорректный пункт меню. Попробуйте снова
Ввод: 11
Некорректный пункт меню. Попробуйте снова
Ввод: 1

...
Ввод: а
ты уже называл эту букву(
```

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву русского алфавита
+ 👍 Есть список введенных букв
+ 🚀 Есть точки расширения: универсальный валидатор слов, который можно использовать для разных алфавитов 

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Этот класс ничего не фильтрует. Это валидатор
```java
public class WordFilter {
  //...
  public boolean isValid(String word) {...}
}

//ПРАВИЛЬНО:
public class WordValidator {
  //...
  public boolean isValid(String word) {...}
}
```

- Одной концепции - одно название.

Сейчас для обозначения одной и той же концепции открытой в маске буквы используются два разных слова: "open" и "reveal"
```java
public class SecretWord {
  //...
  public void openLetterInMask(char letter) {...}

  public boolean isMaskFullyRevealed() {...}
}
```

Выбери какой-то один термин.

- Не дублируй имя класса в названии методов класса
```java
GameSession.startSession();

//ПРАВИЛЬНО:
GameSession.start();
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Exceptions**

- Сообщения в исключениях делай информативными
```java
if (minLength < 1 || minLength > maxLength) {
  throw new IllegalArgumentException("Invalid word length range");
}

throw new UncheckedIOException("Failed to load word dictionary", e);

//ПРАВИЛЬНО:
private static final String INVALID_RANGE_TEMPLATE = "Invalid word length range: %d-%d";

//...
if (minLength < 1 || minLength > maxLength) {
  String message = INVALID_RANGE_TEMPLATE.formatted(minLength, maxLength);
  throw new IllegalArgumentException(message);
}

throw new UncheckedIOException("Failed to load word dictionary: " + path, e);
```

**3. Форматирование**

- Ну, камон. Тут явно не такое большое количество аргументов, чтобы писать их в столбик
```java
public WordDictionary(
    Path path,
    WordFilter wordFilter
)

//ПРАВИЛЬНО:
public WordDictionary(Path path, WordFilter wordFilter) 
```

**4. class WordFilter**

+ 👍 Всё ок, кроме названия класса.

+ 👍 Универсальный валидатор слов, это хорошо.

Можно проверять слово на принадлежность к любому языку: паттерн языковой проверки он принимает в конструктор
```java
public class WordFilter {
  private final int minLength;
  private final int maxLength;
  private final Pattern allowedLetters;

  public WordFilter(int minLength, int maxLength, Pattern allowedLetters) {...}

  public boolean isValid(String word) {...}
}

//КЛИЕНТ:
public class GameRunner {
  private static final Pattern ONLY_RUSSIAN_LETTERS_PATTERN = Pattern.compile("[а-яё]+");
  //...
    
  public static void main(String[] args) {
    WordFilter wordFilter = new WordFilter(..., ONLY_RUSSIAN_LETTERS_PATTERN);
    //...
  }
}
``` 

**5. class WordDictionary**

+ 👍 Всё супер.

**6. class ConsoleUI**

- Паттерн русских букв сейчас хранится в UI в качестве константы
```java
public class ConsoleUI {
  private static final Pattern SINGLE_RUSSIAN_LETTER_PATTERN = Pattern.compile("[А-ЯёЁа-я]");
  //...
}
```

Получается, что текущий UI у тебя всегда только русский и требует для работы только русские буквы.  
А валидатор(фильтр) слов может быть универсальным - потому что он принимает паттерн букв в свой конструктор, а не хардкодит его в своих константах.

Нужно как-то согласовать между собой этот момент.  
Вариантов я вижу три:

ЛИБО просто переименовать "ConsoleUI" в "RuConsoleUI". 
Минусы:  
🔸 слишком просто и неинтересно  
🔸 непонятно, зачем тогда сделан универсальный валидатор слов

ЛИБО `ConsoleUI` должен принимать в себя паттерн букв так же, как это делает валидатор слов.  
Минусы: всё равно класс UI останется завязан на русский язык - в нем захардкоджены русские сообщения.

Причём, сообщения прямо указывают на русский язык: 
```java
public class ConsoleUI {
  //...

  public char readLetter() {
    //...
    System.out.println("нужно ввести одну русскую букву!");
  }
}
```

ЛИБО использовать полиморфизм, который всё объяснит. Примерно так:
```java
public abstract class ConsoleUI {
  private static Pattern letterPattern;
  //...

  public ConsoleUI(Pattern letterPattern, ...) {...}

  public void printMenu(...) {...}  <-- Общий метод для всех потомков

  public char readLetter() {
    //...
    while (true) {
      String title = getReadLetterTitle();
      System.out.print(title);
      enteredLine = scanner.nextLine();
      if (letterPattern.matcher(enteredLine).matches()) {
        return enteredLine.toLowerCase().charAt(0);
      }
      String fail = getReadLetterFail()
      System.out.println(fail);
    }
  }  

  protected abstract String getReadLetterTitle();
  //...
}
```

И конкретные реализации:
```java
public class RuConsoleUI extends ConsoleUI {
  private static final Pattern LETTER_PATTERN = Pattern.compile("[А-ЯёЁа-я]");
  private static final String ENTER_MESSAGE = "Ввод: ";
  //...

  public RuConsoleUI() {
    super(LETTER_PATTERN, ...);
  }

  @Override
  protected String getReadLetterTitle() {
    return ENTER_MESSAGE;
  }

  //...
}

public class EnConsoleUI extends ConsoleUI {
  private static final Pattern LETTER_PATTERN = Pattern.compile("[A-Za-z]");
  private static final String ENTER_MESSAGE = "ENTER: ";
  //...

  public EnConsoleUI() {
    super(LETTER_PATTERN, ...);
  }

  @Override
  protected String getReadLetterTitle() {
    return ENTER_MESSAGE;
  }

  //...
}
```

Создавать эти зависимости нужно в `main` через фабрику - чтобы не было багов, когда словарь одного языка, а UI - другого 
```java
public class Main {
  private static final Language LANGUAGE = Language.Ru;

  public static void main(String[] args) {
    Factory factory = new Factory(LANGUAGE);  //абстрактная фабрика

    ConsoleUi consoleUi = factory.getConsoleUi();
    Dictionary dictionary = factory.getDictionary();  //вернет словарь с нужным языком и нужным файлом слов
    //...
  }
}

public enum Language {
  RU, EN
}
```

Обязательны ли эти сложности?  
Ну я тебе писал, что не усложняй себе задачу сверх ТЗ.  
А если всё-таки решил усложнить, то это повышает требования к качеству кода, а не понижает их.

Весь этот дополнительный код пришлось добавить из-за универсального валидатора.

- Нарушение Low Coupling.

Сейчас Меню имеет высокую связность:  
Оно распечатывает само себя через другой класс(ConsoleUI), а поэтому без этого класса не может быть использовано в других проектах, где тоже будет нужно меню
```java
public class Menu {
  //...

  public void show() {
    consoleUI.printMenu(title, menuOptions);
  }
}
```

ИЛИ принимай и распечатывай меню целиком, а не по запчастям
```java
//СЕЙЧАС ТАК:
public void printMenu(String title, List<MenuOption> menuOptions) {
  // распечатывает меню
}

//ПРАВИЛЬНО:
public void printMenu(Menu menu) {
  String title = menu.getTitle();  
  List<MenuOption> menuOptions = menu.getMenuOptions();  
  // распечатывает меню
}
```
В этом случае Меню должно быть классом-моделью, задача которой только хранить данные: заголовок, номер (ключ) пункта меню и текст пункта меню.  
Тогда Меню-модель не должна в себе иметь метод распечатки.

ИЛИ меню должно само себя распечатывать, а не юзать для этого левый класс
```java
//СЕЙЧАС ТАК:
public class Menu {
  //...

  public void show() {
    consoleUI.printMenu(title, menuOptions);
  }
}

//ПРАВИЛЬНО:
public class Menu {
  //...

  public void show() {
    System.out.println(title);
    //...
  }
}
```
В этом случае Меню остаётся представлением(view), хранит нужные данные и распечатывает их. 

Любой из этих двух подходов ок.  
Но в любом случае, код класса Меню нужно переделать. 

**7. class Menu**

+ 👍 Меню в ООП стиле, это хорошо.

- Критика класса - см. выше.

**8. public record MenuOption(String label, Runnable action)**

+ 👍 В принципе норм
```java
public record MenuOption(String label, Runnable action) {
}
```

- Я бы сделал этот рекорд внутренним классом меню, чтобы он не бултыхался отдельно. Примерно так:
```java
public class Menu {
  //...

  public record MenuOption(String label, Runnable action) {
  }
}
```

**9. class HangmanPicture**

+ 👍 Отлично.

**10. class SecretWord**

+ 👍 Превосходно.

Текст слова, маска и связанные с ними операции: открыть букву etc.

По моему мнению, подобный класс - это то, что в этом проекте отделяет ООП стиль от процедурного. 
Если человек в этом проекте выделяет такие абстракции и не наделяет их лишними ответственностями, значит имеет правильное понимание ООП-декомпозиции.

**11. class GameSession**

- Аргументация понятна
```java
/*
Я намеренно оставил консоль в конструкторе.
1) Её использует ещё и меню
2) В ConsoleUI есть поле Scanner, тот содержит Console.in
   для избежания потенциальных конфликтов, решил использовать общий объект ConsoleUI.
*/
public GameSession(SecretWord secretWord, ConsoleUI consoleUI) {...}
```

Эту ситуацию я разбирал выше:

🔹 Я не против, если конструктор этого класса будет принимать в себя `ConsoleUI` в том случае, если тот будет абстракцией(напр. абстрактным классом) для подстановки UI на разных языках.

🔹 Меню не должно юзать `ConsoleUI` для распечатки самого себя.  
Если меню хочет себя печатать, пусть использует свой собственный экземпляр Scanner'a, это было бы совершенно нормально. Потенциальных конфликтов я не вижу.

- Избыточное форматирование.

Здесь подстановочный аргумент в сообщении стоит с краю сообщения, поэтому можно использовать простую конкатенацию - это легче читается
```java
consoleUI.printMessage("Загаданное слово: %s".formatted(secretWord.getWord()));

//ПРАВИЛЬНО:
consoleUI.printMessage("Загаданное слово: " + secretWord.getWord());
```

- Если всё равно юзаешь UI класс, то логично всю распечатку туда передать
```java
consoleUI.printMessage("Ты проиграл(");
consoleUI.printMessage("Загаданное слово: %s".formatted(secretWord.getWord()));

consoleUI.printMessage("Ты победил");

//ЛУЧШЕ:
consoleUI.printLossMessage(secretWord.getWord());

consoleUI.printWinMessage();
```

Плюсы: `ConsoleUI` избавляется от текстов и оставляет себе только логику.  
И тем самым становится не привязан, например, к конкретному языку визуального интерфейса. 

**12. class GameRunner**

Содержит точку входа main.

+ 👍 Только создает и запускает Игру, в данном случае через меню "Играть или выйти", это хорошо.

*Мартин, "ЧК", гл.11, "Отделение конструирования системы от ее использования"*

## ВЫВОД

Рефакторинг программе пошёл на пользу, все самые принципиальные ошибки исправил.  
В целом реализация ок 👍  

Из-за того, что ты хорошо написал этот проект в ООП стиле, писать Симуляцию будет значительно легче.

n.203(388)  
#ревью #виселица #оопвиселица 