https://github.com/timk01/Hangman  
[Timur M.]

Игра в ООП стиле.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Неправильная последовательность в порядке отображения введенных букв. 

Букву 'ц' я ввел второй, но в распечатке она стала первой. 
При этом введенная третьей буква 'к' в распечатке стала на третью позицию
```java
Нужно загадать одну русскую букву
й

Ошибки (1): [й]
Нужно загадать одну русскую букву
ц

Ошибки (2): [ц, й]
Нужно загадать одну русскую букву
к

Ошибки (3): [ц, й, к]
```
Введенные буквы должны размещаться по алфавиту(й, к, ц) либо в порядке ввода(й, ц, к).

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву русского алфавита
+ 👍 Есть список введенных букв
+ 👍 Перед игрой распечатываются правила
+ 👍 Простой, понятный интерфейс, аккуратные картинки
```
  +---+
  |   |
  O   |
 /+\  |
 / \  |
      |
=========
```

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Если в проекте есть класс `Dictionary`, то все переменные с именем, включающим это название, должны быть экземплярами этого класса.
Если разные концепции называются одним и тем же именем, это приводит к путанице
```java
public class Dictionary {
  private final List<String> dictionary = new ArrayList<>();  <-- это не экземпляр класса Dictionary
  //...
}

//ПРАВИЛЬНО:
public class Dictionary {
  private final List<String> words = new ArrayList<>();  
  //...
}
```

- Это опечатка или специально написанный неопределенный артикль?
```java
public String getARandomWord()

//ПАРВИЛЬНО:
public String getRandomWord()
```
В любом случае, в названиях не нужно использовать артикли "A", "Are", "The", частицы "Of" и т.д.  
Если, конечно, "Of" не используется в контексте "valueOf()".

- Это не константа. Константы должны быть private final
```java
private final List<String> HANGMAN_VIEW = new ArrayList<>();

//ПРАВИЛЬНО:
private final List<String> pictures = new ArrayList<>();
```

- Этот метод не инициализирует hangman stages, он инициализирует hangman view
```java
private final List<String> HANGMAN_VIEW = new ArrayList<>();

private void initHangmanStages() {
  //заполняет HANGMAN_VIEW строками(картинками виселицы)
}

//ПРАВИЛЬНО:
private final List<String> pictures = new ArrayList<>();

private void initPictures() {
  //заполняет pictures картинками
}
```


- Все инпутеры-реализации интерфейса `Input` предназначены для юзера.  
Поэтому уточнение "User" не объясняет сути этой имплементации.  
Этот инпутер предназначен для консольного ввода
```java
class UserInput implements Input

//ПРАВИЛЬНО:
class ConsoleInput implements Input
```

- Название переменной должно объяснять её суть
```java
public class Validator {
  private static final char LOWER_A = 'а';
  private static final char LOWER_YA = 'я';
  private static final char LOWER_YO = 'ё';
  //...
}

//ПРАВИЛЬНО:
public class Validator {
  private static final char FIRST_LETTER = 'а';
  private static final char LAST_LETTER = 'я';
  private static final char SPECIAL_LETTER = 'ё';
  //...
}
```
*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Форматирование строк**

Если нужно печатать или создавать строку с более, чем одним подстановочным значением или значение вставляется внутрь сообщения, используй форматирование- тогда сразу будет виден весь шаблон
```java
System.out.print("Ошибки (" + errors + "): ");

//ПРАВИЛЬНО:
System.out.printf("Ошибки (%d): ", errors);
```

**3. Если в блоке if есть return(break, continue, throw, exit и т.д.), то else не пишется.**
```java
if (NO.equals(input)) {
  return false;
} else {
  System.out.printf("Некорректный ввод. Пожалуйста, введите '%s' или '%s'.%n", YES, NO);
}

//ПРАИВЛЬНО:
if (NO.equals(input)) {
  return false;
} 
System.out.printf("Некорректный ввод. Пожалуйста, введите '%s' или '%s'.%n", YES, NO);
```

**4. Объявляй переменные там, где они используются**

Минимизируй область видимости локальных переменных
```java
String input;
do {
  input = scanner.nextLine().trim().toLowerCase();
  //...
} while (true);

//ПРАВИЛЬНО:
do {
  String input = scanner.nextLine().trim().toLowerCase();
  //...
} while (true);
```
*Блох, "Java. Эффективное программирование", изд.3, гл.9.1*  

**5. class Dictionary**

- Неудобство пользования.

Конструктор принимает кроме пути к файлу максимальное количество слов, которое нужно прочитать из файла.  
Само по себе это неплохо
```java
public class Dictionary {
  //...  
  public Dictionary(String path, int maxWords) {
    this.path = path;
    this.maxWords = maxWords;  <-- макс. количество загружаемых слов из файла
  }
  //...
}
```
Но теперь для того, чтобы просто прочитать все слова из файла, нужно делать это не совсем корректным способом.

Допустим, я не знаю, сколько слов в текстовом файле, 67 или 533. Но хочу их все прочитать в словарь.  
Тогда, чтобы прочитать их всех наверняка, мне нужно подставить какую-то заведомо большую цифру.  
Например:
```java
Dictionary dictionary = new Dictionary(FILE_PATH, Integer.MAX_VALUE);
```

Чтобы пользоваться твоим классом более корректно, сделай перегруженные конструкторы:
```java
public class Dictionary {
  //...  
  public Dictionary(String path, int maxWords) {
    //чтение файла с ограничением по количеству слов
  }

  public Dictionary(String path) {
    //чтение файла без ограничения по количеству слов
  }
  //...
}
```

- Намерения неясны.

В классе есть специальный публичный метод для загрузки слов
```java
public class Dictionary {
  //...  
  public void loadWords() 
}
```

Наличие отдельного метода для загрузки слов мне непонятно.  
Если слова нужно отдельно загружать через вызов дополнительного метода, то это усложняет правила пользования классом
```java
//Сейчас так: cоздание словаря и загрузка в него слов- 2 операции
Dictionary dictionary = new Dictionary(path, maxWords);
dictionary.loadWords();

//...
public class Dictionary {
  //...
  public Dictionary(String path, int maxWords) {...}
  public void loadWords() {...} 
}
```

Альтернативный вариант:
```java
//Создание словаря и загрузка в него слов- 1 операция
Dictionary dictionary = new Dictionary(path, maxWords);  <-- слова грузятся при вызове конструктора

//...
public class Dictionary {
  //...
  public Dictionary(String path, int maxWords) {
    //...
    loadWords(path, maxWords);
  }
  private void loadWords(String path, int maxWords) {...} 
}
```

+ 🚀 Мегалайк за тип используемого исключения.   
Внимание к нюансам это всегда хорошо
```java
try (BufferedReader buff = new BufferedReader(new FileReader(path))) {
  //...  
} catch (IOException e) {
  throw new UncheckedIOException("Не найден файл dictionary (словаря) по указанному пути", e);  <-- возможно, лучший тип исключения для этого случая
}
```

- Делай информативные сообщения в исключениях
```java
throw new UncheckedIOException("Не найден файл dictionary (словаря) по указанному пути", e);

//ПРАВИЛЬНО:
throw new UncheckedIOException("Не найден файл словаря : " + path, e);
```

- Зачем нужен этот метод-посредник?
```java
public String getARandomWord() {
  return pickRandomWord();
}

private String pickRandomWord() {
  //5 строк какого-то кода
}

//ПРАВИЛЬНО:
public String getRandomWord() {
  //5 строк какого-то кода
}
```

- Текст в exception всегда должен быть на английском языке
```java
throw new IllegalStateException("Словарь пуст");
```
Исключение это не просто телеграмма, которая летит сквозь слои.

У exception особое назначение- если исключение вылетит и не будет перехвачено внутри программы, то аварийно прекратит выполнение программы.  
Тогда на экране будет распечатано сообщение эксепшена, и это сообщение должно быть понятно сисадмину в любой точке планеты.  
А значит, сообщение должно быть на английском.

Интерпритация исключения и перевод его на локальный язык должно происходить там, где это соответствует архитектуре программы.  
Или не происходить вовсе, если исключение не планируется перехватывать.

**6. class View**

+ 👍 Отдельный класс представление(view), это хорошо.

Через этот вью происходит распечатывание всей информации для юзера.  
Благодаря этому, в программе данные отделяются от их представления.

- Картинки виселицы храни в константе.

Картинки виселицы для всех экземпляров класса `View` будут одинаковы, 
поэтому нужно хранить их в константе
```java
private final List<String> HANGMAN_VIEW = new ArrayList<>();  <-- Это не константа

private void initHangmanStages() {
  //заполняет HANGMAN_VIEW строками(картинками виселицы)
}

//ПРАВИЛЬНО:
private static final List<String> PICTURES = getPictures();  <-- Это константа

private static void getPictures() {
  //возвращает список картинок виселицы
}
```

- Для распечатки каждого результата нужен свой метод
```java
public void printRoundResult(String word, boolean isWin) {
  String result = isWin ? "выиграли" : "проиграли";
  System.out.printf("Вы %s. Было загадано слово: %s%n", result, word);
}

//ПРАВИЛЬНО:
public void printWinMessage(String word) {...}
public void printLoseMessage(String word) {...}
```
*"ЧК", гл17, F3, "Флаги в аргументах"*

**7. class UserInput implements Input**

+ 👍 Часть слоя представления- получает данные от юзера.

Как и предыдущий класс `View`, он обеспечивает отделение данных от представления.  
Оба эти класса создают в программе слой представления(view).  

Но чтобы совсем было хорошо, у View тоже должен быть интерфейс.  
Так же, как у класса `UserInput` есть интерфейс `Input`.

**8. class WordUtils**

- Это утилитный класс, он содержит только статические методы.  
Утилитные классы должны быть `final` и иметь приватный конструктор.  
Не должно быть возможности унаследоваться от утилиты или сделать ее экземпляр.  
*Блох, "Java. Эффективное программирование", изд.3, гл.2.4*

- Магические строки
```java
sb.append("_ ");
```

- Трудно понять, что здесь происходит.  
Вводи поясняющие переменные
```java
return buildRevealedWord(word, goodLetters).replace(" ", "").equals(word);

//ПРАВИЛЬНО:
StringBuilder sb = buildRevealedWord(word, goodLetters);
String revealedWord = sb.toString().replace(" ", "");
return revealedWord.equals(word);
```

**9. class Round**

- Неправильное использование View
```java
view.printMessage("Вы уже называли эту букву. Попробуйте другую.");
view.printMessage("Загаданная буква: " + guessedChar);

//ПРАВИЛЬНО:
view.printReenteringLetterMessage();
view.printInputtedLetterMessage(guessedChar);
```
Если во вью есть отдельные методы для распечатки отдельных сообщений, то сделай такие методы для всех сообщений.  
Вью должно быть либо таким:
```java
public class View {
  //Самый простой вью-принтер

  //только один метод- распечатка сообщения
  public void out(String message) {
    System.out.println(message);
  }
}
```

Либо таким:
```java
public class View {
  
  public void printGreetings() {
    System.out.println("Добро пожаловать в игру 'Виселица'!");
  }

  public void printWrongLetter(Char letter) {
    System.out.println("Такой буквы нет в слове: " + letter);
  }
  //Все остальные стопитсот методов на каждый тип сообщения 
}
```

Преимущество последнего вью в том, что можно делать разные реализации с тем же интерфейсом.  
Или теми же переопределенными публичными методами класса-предка.  

В случае общего интерфейса, это будет примерно так:
```java
public interface View {
  void printGreetings();

  void printWrongLetter(Char letter);
  //Все остальные стопитсот методов на каждый тип сообщения 
}

public class ConsoleView implements View{
  //Печать в консоль

  public void printGreetings() {
    System.out.println("Добро пожаловать в игру 'Виселица'!");
  }

  public void printWrongLetter(Char letter) {
    System.out.println("Такой буквы нет в слове: " + letter);
  }
  //...
}
```

Это открывает интересные возможности:
```java
public class ArcadeMaсhineView implements View{
  //вывод информации в аркадный автомат
  //через порт RS-232

  public void printGreetings() {
    //Включить лампочку "Игра началась"
  }

  public void printWrongLetter(Char letter) {
    //Включить красную лампочку над буквой 'letter'
  }
  //...
}
```

Ну, или банальное:
```java
public class RuConsoleView implements View{
  //Печать в консоль
  public void printGreetings() {
    System.out.println("Добро пожаловать в игру 'Виселица'!");
  }

  public void printWrongLetter(Char letter) {
    System.out.println("Такой буквы нет в слове: " + letter);
  }
  //...
}

public class EnConsoleView implements View{
  //Печать в консоль
  public void printGreetings() {
    System.out.println("Welcome to the game 'Hangman'!");
  }

  public void printWrongLetter(Char letter) {
    System.out.println("There is no such letter in the word:" + letter);
  }
  //...
}
```

- Совмещение команды и запроса, метод делает два дела сразу.

Методы не должны совмещать команду и запрос, методы не должны делать два дела сразу
```java
private boolean finishRound(String word, Set<Character> goodLetters, int roundErrors) {
  boolean roundResult = isWin(word, goodLetters);
  if (!roundResult) {
    view.printStage(roundErrors);  <-- Выполняет команду
  }
  view.printRoundResult(word, roundResult);  <-- Выполняет команду
  return roundResult;  <-- Отвечает на запрос
}
```
*Мартин, "Чистый код", гл.3, "Разделение команд и запросов", "Правило одной операции"*

- Вводи вспомогательные методы, делай код понятнее
```java
while (roundErrors < maxErrors && !isWordFullyGuessed(word, goodLetters)) {...}

//ПРАВИЛЬНО:
while (!isGameOver()) {...}

private boolean isGameOver() {
  return isWin() || isLose();
}
```

**10. class Game**, содержит точку входа Main 

+ 👍 Только создает и запускает игру, это хорошо.
+ 👍 Диалог "Играть или выйти" происходит тут, это хорошо.

## АРХИТЕКТУРА

В основном, игра написана в ООП стиле.

Но декомпозированы только совсем очевидные сущности типа Словаря.

На мой взгляд, правильное понимание декомпозиции в этом проекте показывает выявление сущности "Слово" и реализация ее в виде класса.  
В этом классе должны быть объединены состояния(текст слова и его маска) и поведение(открыть букву etc.)  
Сейчас эта сущность разделена на несколько классов, в том числе на утилиту `WordUtils`- то есть, чисто процедурный подход.

Объектно-ориентированная роль слоя вью тоже не совсем понята- класс `View` здесь частично вью, частично принтер.

Но отделение данных от представления через создание слоя вью(ввод/вывод информации через консоль) это с точки зрения ООП хорошо и прогрессивно.

## ВЫВОД

В целом, норм.

n.158(310)  
#ревью #виселица 