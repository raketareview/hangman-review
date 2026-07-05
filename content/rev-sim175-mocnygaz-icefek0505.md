https://github.com/icefek0505/Hangman  
[mocny gaz]

Игра в ООП стиле.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

**1. Не нужно мучить юзера длинными командами "Старт"/"Стоп"**
```java
=  Please type 'Старт' if you want to continue 
 'Выход' to close the application            =
==============================================
```
Достаточно 0/1 или S/Q.

**2. Игра не запускается**
```java
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: Index 0 out of bounds for length 0
	at entity.WordPicker.pickWordFrom(WordPicker.java:16)
	at entity.WordPicker.pickWord(WordPicker.java:10)
```

**3. Ввожу буквы, а они не вводятся**
```java
================================
    YOU HAVE ALREADY ENTER:

================================

================================
=    ENTER YOUR LETTER HERE:   =
Q
W
E
```
Они не вводятся потому, что нужно вводить только русские буквы?  
Я, как юзер, об этом никогда не догадаюсь, потому что весь UI на английском.  
И при неправильном вводе мне не сообщают причину, по которой введенные буквы игнорируются.

**4. Визуальный интерфейс перегружен и непонятен, весь в шпалах. Где, шо, куда?**
```java
================================
=   Game session was started   =
================================

================================
   ┌──────────┐
   │          │
   │           
   │           
   │           
   │           
   │           
───┴───────────
 п_______
================================

================================
    YOU HAVE ALREADY ENTER:

================================

================================
=    ENTER YOUR LETTER HERE:   =
```

**5. Неправильная последовательность в порядке отображения введенных букв.**

Букву 'ц' я ввел второй, но в распечатке она стала первой.  
При этом введенная третьей буква 'к' в распечатке стала на третью позицию
```java
й
YOU HAVE ALREADY ENTER:
 й,

ц
YOU HAVE ALREADY ENTER:
 ц, й,

к
YOU HAVE ALREADY ENTER:
 ц, й, к,
```
Введенные буквы должны размещаться по алфавиту(й, к, ц) либо в порядке ввода(й, ц, к).

**6. Картинки.**

Хангман, я тебя повесил!
```java
================================
   ┌──────────┐
   │          │
   │        \( )/
   │         \│/
   │          │
   │         ╱ ╲
   │        ╱   ╲
───┴───────────
```

Фиг тебе! У меня ещё есть ГЛАЗИК!
```java
================================
   ┌──────────┐
   │          │
   │        \(x)/
   │         \│/
   │          │
   │         ╱ ╲
   │        ╱   ╲
───┴───────────
```

## ХОРОШО

+ 👍 Можно ввести только одиночную букву русского алфавита
+ 👍 Есть список введенных букв
+ 🚀 Есть подсказка: на старте открывает одну букву

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Давай названия по делу.

Класс "Судья"? Поэтично, но бессмысленно
```java
class Judge
```
Внутри класса никакой судебной деятельности не обнаружено.

- Придерживайся единообразия
```java
public void updateMaskedWord(char letter) {
   //открывает конкретную букву 
}

public void openRandomLetter() {
  //открывает случайную букву 
}

//ПРАВИЛЬНО:
public void openLetter(char letter) 

public void openRandomLetter() 
```

- Старайся придерживаться стандартных названий
```java
public class Judge {
  //...  
  public boolean isLetterInWord(char letter) {...}
}

//ПРАВИЛЬНО:
public class Judge {
  //...  
  public boolean contains(char letter) {...}
}
```
По аналогии с одноименным методом в `String`, `List` etc. 

- Чем меньше область видимости переменной, тем короче делай названия.  

Область видимости этой переменной 10 строк, на таком расстоянии мы не забудем, что это случайная буква
```java
char randomLetterFromWord = chooseRandomLetterFromWord();

//ПРАВИЛЬНО:
char letter = chooseRandomLetterFromWord();

//так тоже можно:
char randomLetter = chooseRandomLetterFromWord();
```

- Венгерская нотация.

В названии переменных не пиши тип данных, к которым они относится.  
И вообще не употребляй венгерскую нотацию.  
Название переменной должно отвечать на вопрос что хранит переменная, а не как хранит
```java
StringBuilder maskedWordBuilder = new StringBuilder(maskedWord);

//ПРАВИЛЬНО:
StringBuilder maskedWordLetters = new StringBuilder(maskedWord);  //поле для временного хранения букв из maskedWord
```

- Константы нужно писать стилем UPPER_CASE
```java
private static final Path filePath = Paths.get("src", "main", "resources", "Nouns.txt");

//ПРАВИЛЬНО:
private static final Path FILE_PATH = Paths.get("src", "main", "resources", "Nouns.txt");
```

- UPPER_SNAKE только для констант. Константы это поля классов, а не методов
```java
public class Player implements Resetable {
  //...
  private boolean isCyrillic(char letter) {
    char FIRST_RUSSIAN_LETTER = 'а';
    char LAST_RUSSIAN_LETTER = 'я';
    return letter >= FIRST_RUSSIAN_LETTER && letter <= LAST_RUSSIAN_LETTER || letter == 'ё';
  }
}

//ПРАВИЛЬНО:
public class Player implements Resetable {
  private static final char FIRST_RUSSIAN_LETTER = 'а';
  private static final char LAST_RUSSIAN_LETTER = 'я';

  private boolean isCyrillic(char letter) {
    return letter >= FIRST_RUSSIAN_LETTER && letter <= LAST_RUSSIAN_LETTER || letter == 'ё';
  }
}
```

- Название обманывает.

Этот метод определяет не кириллическую, а букву современного русского алфавита 
```java
boolean isCyrillic(char letter) 

//ПРАВИЛЬНО:
boolean isRusLetter(char letter) 
```

Потому что есть кириллические нерусские буквы, например:
```
Љ, Ў, Џ
```

- Давай методам простые понятные названия, которые объясняют, что эти методы делают
```java
char makeAssumption() {
  //получает русскую букву от юзера    
}

//ПРАВИЛЬНО:
char inputRusLetter() {
  //получает русскую букву от юзера    
}
```

- Я два раза понял, что через этот метод я что-то получаю. Но что именно я получаю?
```java
String getInput()

//ПРАВИЛЬНО:
String inputОбъяснениеЧтоИменно()
```

- Аббревиатуры в названиях лучше писать так же, как и другие слова
```java
class HangmanUI

//ЛУЧШЕ:
class HangmanUi
```
Например, какое название понятнее: HTTPURL или HttpUrl?  
*Блох "Java. Эффективное программирование", изд.3, гл.9.12.*

- Название может путать.

Методы класса не должны своим названием быть похожи на конструктор, если это не конструктор.  
Здесь `pickWord` называется как `WordPicker`, только наоборот
```java
public class WordPicker {
  private String word;

  public void pickWord() {
    word = pickWordFrom(WordsExtracter.getExtracted());
  }
  //...
}

//ПРАВИЛЬНО:
public class WordPicker {
  private String word;

  public void initWord() {
    word = readWord(WordsExtracter.getExtracted());
  }
  //...
}
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Используй классы через их интерфейсы**
```java
HashSet<Character> assumptions = new HashSet<>();

//ПРАВИЛЬНО:
Set<Character> assumptions = new HashSet<>();
```
Общее правило: ArrayList нужно использовать через List, HashMap через Map, HashSet через Set и т.д.  
Это позволяет пользоваться преимуществами полиморфизма.

Да, бывают ситуации, когда, например, с LinkedList нужно работать именно как с LinkedList, а не с List. 
Но это уже нюансы.  
*"Java. Эффективное программирование", изд.3, гл.9.8*
```java
"Если вы выработаете привычку использовать в качестве типов интерфейсы, ваша программа будет гораздо более гибкой" - Блох.
```

**3. Бесполезные методы-посредники**

В проекте часто используются методы, которые не делают ничего полезного, а просто вызывают другие методы.  
Например:
```java
public abstract class WordsExtracter {
  private static final Path filePath = Paths.get("src", "main", "resources", "Nouns.txt");

  public static String[] getExtracted() {
    return extract();
  }

  private static String[] extract() {
    //10 строк кода
  }
}

//ПРАВИЛЬНО:
public abstract class WordsExtracter {
  private static final Path filePath = Paths.get("src", "main", "resources", "Nouns.txt");

  public static String[] getExtracted() {
    //10 строк кода
  }
}
```

**4. Нарушение DRY**, магические буквы, числа, слова. Вводи константы. 

Если к одной и той же магической штуке обращаются два разных класса, то делай ее константой в одном из этих двух классов.  
Либо перенеси ее в третий класс и из первых двух классов обращайся к этим константам- эти данные должны быть синхронизированы между собой
```java
public class HangmanUI {
  //...

  public void drawRestartSuggestion() {
    System.out.println("=  Please type 'Старт' if you want to continue \n 'Выход'" +  <-- ОБЩИЕ МАГИЧЕСКИЕ СТРОКИ
        " to close the application            =");
  }
}

public class Launcher {

  public static void main(String[] args){
    while(!playerInput.equals("старт")){...}  <-- ОБЩИЕ МАГИЧЕСКИЕ СТРОКИ
    if(playerInput.equals("выход")){}  <-- ОБЩИЕ МАГИЧЕСКИЕ СТРОКИ
  }
}
```

**5. class Judge**

+ 👍 Больше всего этот класс напоминает сущность "Слово". Само по себе это хорошо. 

Класс содержит текст слова, его маску и методы для работы с ними.  
Так что оценивать этот класс буду с точки зрения сущности Слово. 

- Название класса максимально не соответствует его внутренней сути.

Посмотри, ты в названиях полей класса даже сам уточняешь, что имеешь ввиду:
```java
public class Judge {
  private int wordLength;
  private String originalWord;
  private String maskedWord;
  //...
}

//ПРАВИЛЬНО:
public class Word {
  private String text;
  private String mask;    
  private int length;
  //...
}
```

- Не используй сеттеры для НАЧАЛЬНОЙ инициализации значений, используй конструктор:
```java
public class Judge {
  //...
  private String originalWord

  public String getMaskedWord() {
    return maskedWord;
  }

  public void setOriginalWord(String originalWord) {...}  <-- Удали этот метод из класса
  //...
}

//ПРАВИЛЬНО:
public class Word {
  //...
  private String text;

  public Word(String text) {
    this.text = text;
  }
  //...
}
```

Тогда не придется в названиях постоянно уточнять, что имеется ввиду:
```java
public class Judge {
  //...
  private char chooseRandomLetterFromWord() {...}
}

//ПРАВИЛЬНО:
public class Word {
  //...
  private char chooseRandomLetter() {...}
}

```

- Не храни поле, если оно дублирует данные, которые можно получить через вызов метода
```java
public class Judge {
  private String originalWord;
  //...

  public void updateMaskedWord(char letter) {
    for (int i = 0; i < wordLength; i++) {...}
  }
}

//ПРАВИЛЬНО:
public class Judge {
  private String originalWord;
  //...

  public void updateMaskedWord(char letter) {
    for (int i = 0; i < length(); i++) {...}
  }

  public int length() {
    return originalWord.length();
  }
}
```

- Если пытаются открыть букву, которой нет в слове, то нужно бросать исключение
```java
public void updateMaskedWord(char letter)
```
Потому что попытка открыть несуществующую букву это баг алгоритма- корректно работающий алгоритм не будет пытаться открыть несуществующую букву.  
Если в алгоритме есть этот баг, то его нужно как можно быстрее обнаружить и исправить.

- Избыточно.

Почему бы сразу не использовать `StringBuilder` вместо `String`
```java
private String maskedWord;

public void updateMaskedWord(char letter) {
  StringBuilder maskedWordBuilder = new StringBuilder(maskedWord);
  for (int i = 0; i < wordLength; i++) {
    if (originalWord.charAt(i) == letter) {
      maskedWordBuilder.setCharAt(i, letter);
    }
    maskedWord = maskedWordBuilder.toString();
  }
}

//ПРАВИЛЬНО:
private final StringBuilder mask;  <-- инициализируется в конструкторе

public void updateMaskedWord(char letter) {
  for (int i = 0; i < wordLength; i++) {
    if (originalWord.charAt(i) == letter) {
      mask.setCharAt(i, letter);
    }
  }
}
```

- Используй класс `Random`, он просто удобнее
```java
return originalWord.charAt((int) (Math.random() * wordLength));

//ПРАВИЛЬНО:
Random random = new Random();
return originalWord.charAt(random.nextInt(wordLength));
```

- Нарушение DRY. Дублирование кода в классах
```java
public void openRandomLetter() {
  char randomLetterFromWord = chooseRandomLetterFromWord();
  //то же самое
}

public void updateMaskedWord(char letter) {
  //то же самое
}

//ПРАВИЛЬНО:
public void openRandomLetter() {
  char letter = chooseRandomLetterFromWord();
  openLetter(letter);
}

public void openLetter(char letter) {...}
```

- В классе не хватает методов.

Тут нужно еще:
🔸Геттер `originalWord`  
🔸Метод, который определяет, что слово полностью открыто

- Дискуссионно.

С точки зрения SRP является дискуссионным, должен ли в этом классе быть метод открытия случайной буквы.  
Или этот функционал относится не к ответственности хранения букв, а к ответственности общей игровой логики
```java
void openRandomLetter()
```
Я считаю, что этот метод нарушает SRP этого класса.  
Но если так проще, то пусть будет.

+ 👍 В целом, кроме плохого нейминга и пары неправильных моментов, класс получился на удивление неплохим.

**6. interface Resetable**

- Просто удали этот интерфейс, оно не решает никаких задач
```java
public interface Resetable {
  void reset();
}
```

**7. class Player implements Resetable**

- Если это класс игрока, то как минимум у него должно быть поле `String name`.

- Класс "Игрок" здесь не нужен.

Класс с именем "Игрок" имеет смысл в многопользовательских играх, где он хранит данные для идентификации пользователя.
Например, в игре крестики-нолики Игрок хранит значок для хода(X, 0).

В однопользовательских играх Игрок может существовать, если нужно хранить его персональные данные, необходимые для процесса игры. 
Например, количество денег при игре в казино.
Что касается хангмана, то в одном из прошлых проектов был класс `Player` и он там был оправдан, 
потому что в нем только считалась статистика выигрышей/проигрышей игрока: https://t.me/zhukovsd_it_chat/118068

В данной реализации класс `Player` лишний.

Методы из этого класса нужно перенести в те классы, частью чьих ответственностей эти методы являются.

- Старайся писать просто и понятно. 

Тут среди прочего у тебя рекурсия.  
Рекурсия должна использоваться только в редких случаях, там, где это алгоритмически оправдано.  
Например, при обходе бинарного дерева или типа того.

В остальных случаях рекурсии быть не должно 
```java
public char makeAssumption() {
  boolean isValid = false;
  String speech = "";
  while (!isValid) {
    speech = getInput();
    while (speech.length() != 1) {
      speech = getInput();
    }
    if (isCyrillic(speech.charAt(0))) {
      isValid = true;
    }
  }
  return speech.charAt(0);
}

//ПРАВИЛЬНО:
public char makeAssumption() {
  while (true) {
    String line = getInput();
    
    if (line.length() != 1) {
      continue;
    }
    
    char symbol = line.charAt(0);
    if (isCyrillic(symbol)) {
      return symbol;
    }
  }
}
```

- Здесь не нужен метод `reset()`.

Вместо ресетов просто пересоздавай объекты.  
Не бойся, процессорное время потраченное на создание объекта вместо его ресета не приведет к зависанию компьютера 
```java
public static void main(String[] args){
  Player player = new Player();

  while(true){
    //...
    player.reset();
  }
}

//ПРАВИЛЬНО:
public static void main(String[] args){
  while(true){
    Player player = new Player();
    //...
  }
}
```

Имеется в виду конкретно этот кейс- в этом классе перезагрузка и пересоздание объекта делают одно и то же.  
Может быть в каких-то других ситуациях такой метод был бы оправдан. 

**8. abstract class WordsExtracter**, файловое чтение

- Соблюдай требования утилитных классов. 

Это утилитный класс, он содержит только статические методы.  
Утилитные классы должны быть `final` и иметь приватный конструктор.  
Не должно быть возможности унаследоваться от утилиты или сделать ее экземпляр.

Абстрактный класс, в котором только статические методы, это НОНСЕНС.  
Потому что если класс абстрактный, то от него планируется наследование.  
А наследование от утилиты это Антипаттерн
```java
public abstract class WordsExtracter {
  //... 
  public static String[] getExtracted() {...}

  private static String[] extract() {...}
}

//ПРАВИЛЬНО:
public final class WordsExtracter {
  //...
  private WordsExtracter() {}
     
  public static String[] getExtracted() {...}

  private static String[] extract() {...}
}
```
*Блох, "Java. Эффективное программирование", изд.3, гл.2.4*

- Используй простые стандартные названия, не используй для классов названия во множественном числе.

Название класса должно как можно точнее объяснять, что делает этот класс.

Что по сути своей делает этот класс? Он просто читает ЛЮБОЙ текстовый файл.  
Идеальным названием для него было бы `FileReader`, но такой класс в core уже есть, поэтому: 
```java
abstract class WordsExtracter {
  //... 
  public static String[] getExtracted() {
    return extract();
  }

  private static String[] extract() {
    //читает и возвращает слова из файла
  }
}

//ПРАВИЛЬНО:
class TextFileReader {
  //...
  public static List<String> read(...) {
    //читает и возвращает слова из файла
  }    
}
```

- Для возврата группы слов используй `List`, а не массив- для клиента листы удобнее 
```java
private static String[] extract() {
  //читает и возвращает слова из файла
}

//ПРАВИЛЬНО:
public static List<String> read(...) {
  //читает и возвращает слова из файла
}   
```

- Путь к файлу этот класс должен принимать в метод чтения.

Сейчас в классе жестко прописан путь к файлу- это делает класс файлового чтения не универсальным.  
Хотя класс, который читает текстовые файлы, может быть полезен в разных проектах.

При создании классов думай о возможности их переиспользования в аналогичных задачах.  
Поэтому:
```java
public abstract class WordsExtracter {
  //... 
  public static String[] getExtracted() {...}
}

//ПРАВИЛЬНО:
public final class TextFileReader {
  //...
  public static List<String> read(String filepath) {...}  //или: read(Path path)
}
```

**9. class WordPicker**, словарь

- Словарь он и в Африке словарь
```java
class WordPicker

//ПРАВИЛЬНО:
class Dictionary
```

- Нарушение SRP.

Нет смысла хранить выбранное слово в памяти.  
Задача словаря- просто по запросу выдать слово
```java
public class WordPicker {
  private String word;

  public String getWord() {
    return word;
  }

  public void pickWord() {
    word = pickWordFrom(WordsExtracter.getExtracted());
  }

  private String pickWordFrom(String[] words) {
    //4 строки кода
  }
}

//ПРАВИЛЬНО:
public class Dictionary {
  private final String filepath;
     
  public class Dictionary(String filepath) {...}

  public void get() {
    List<String> text = TextFileReader.read(filepath);
    //вернуть случайное слово из text
  }
}
```

- Реакция на ошибки.

Если по указанному адресу в `class Dictionary` не будет найден файл со словами, то программа напишет соответствующее сообщение
```java
try (BufferedReader br = new BufferedReader(new FileReader(filePath.toFile()))) {
  //...
} catch (IOException e) {
  System.out.println(e.getMessage());
}
```

Но проблема в том, что после этого программа всё равно аварийно вылетит с исключением
```java
src\main\resources\Nouns.txt (Системе не удается найти указанный путь)
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: Index 0 out of bounds for length 0
	at entity.WordPicker.pickWordFrom(WordPicker.java:16)
	at entity.WordPicker.pickWord(WordPicker.java:10)
```
Здесь должна быть другая реакция на отсутствие файла со словами.  
Нужно сказать юзеру, что файл со словами по указанному пути открыть не удалось и поэтому работа программы будет завершена. 

Причем, путь нужно указать не относительный, как у тебя: `src\main\resources\Nouns.txt1`.  
А абсолютный, чтобы юзер знал, где конкретно ему нужно искать файл: `c:/..../Nouns.txt1`.

После этого корректно завершить работу программы.

**10. class HangmanUI**

+ 👍 Отдельный класс для распечатки сообщений это хорошо. Что-то типа view.

**11. Печать картинки виселицы** через switch-case или if-elseif - индусский код. (1)

Картинки нужно хранить в статическом массиве и печатать по номеру, например, так
```java
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

public static void drawHangman(int pictureNumber) {
    String picture = PICTURES[pictureNumber];  
    System.out.println(picture);
}
```

- Форматирование строк.

Если нужно печатать или создавать строку с более, чем одним подстановочным значением или значение вставляется внутрь сообщения, 
используй форматирование- тогда сразу будет виден весь шаблон
```java
System.out.println("=    you have " + (5 - mistakes) + " lives left    =");

//ПРАВИЛЬНО:
System.out.printf(""=    you have %d lives left    =   %n", (5 - mistakes));
```

- При распечатке все тексты вписываются в рамку
```java
public void drawPlayerWon() {
  System.out.println("================================");
  System.out.println("=    !!YOU WON THIS GAME!!     =");
  System.out.println("================================");
}

public void drawPlayerWasWrong(int mistakes) {
  System.out.println("================================");
  System.out.println("=I'M SORRY! YOU DID A MISTAKE..=");
  System.out.println("=    you have " + (5 - mistakes) + " lives left    =");
  System.out.println("================================");
}
```

Ок, но тогда ты должен такую распечатку не подстраивать каждый раз вручную, а сделать метод, который будет печатать текст в рамке.  
Например:
```java
public void drawPlayerWon() {
  drawWithBox(BOX_WIDTH, "!!YOU WON THIS GAME!!");
}

public void drawPlayerWasWrong(int mistakes) {
  String message = "you have %d lives left    =".formatted(5 - mistakes);

  drawWithBox(BOX_WIDTH, 
    "I'M SORRY! YOU DID A MISTAKE..", 
     message
  );
}

private void drawWithBox(int width, String... lines ) {  //lines это массив строк в виде varargs
  //Распечатает в рамке текст из массива строк lines 
  //Ширина рамки = width
}
```

**12. class Launcher**, содержит точку входа main.

- Нарушение SRP.

Main должен только сконфигурировать зависимости и запустить программу.  
Управлять работой программы этот класс не должен.  
В Main'e возможен только диалог "Играть или выйти"- это все еще относится к ответственности запуска системы.

В простейшем случае для однократной игры Main должен выглядеть примерно так:
```java
public class FirstMain {

  public static void main(String[] args){
    Word word = new Word("парабола");
    Game game = new Game(word);
    game.start();
  }
}
```

Для многократной игры как-то так:
```java
public class SecondMain {
  //...

  public static void main(String[] args){
    Dictionary dictionary = new Dictionary(FILE_PATH);
    
    while(true) {
      String command = inputCommand();

      if(isStart(command)) {
        String text = dictionary.get();
        Game game = new Game(text); 

        Game game = new Game(word);
        game.start();
      }   
    }
    //...
  }

  private static String inputCommand() {
    //получение команды от юзера: старт/стоп
  }

  private static boolean isStart(String command) {
    return START.equalsIgnoreCase(command);
  }

  //...
}
```
*Мартин, "ЧК", гл.11, "Отделение конструирования системы от ее использования"*

- Большой божественный метод `void main() `на 70 строк.

Обсуждать его не имеет смысла- он абсолютно ужасен.  
Здесь тебя как будто подменили, этот код нечитаем.

Метод нужно разделить на несколько, которые будут соответствовать этим критериям:
🔹 Маленький размер  
🔹 Выполняют одну операцию на одном уровне абстракции  
🔹 Не совмещают команду и запрос  
🔹 Не содержат больше трех уровней вложенности

## ВЫВОД

Несмотря на то, что игра не запускается и в коде есть простые ошибки, проект мне понравился 👍  
Самое главное, что ты в целом правильно понимаешь, по каким принципам нужно делить ООП программу на классы.  

Конечно, не всегда выходит хорошо, но общее направление мысли верно.  
Этот проект можно считать написанным в ООП стиле.

Примеры хороших классов, которые в целом соответствуют SOLID:
```java
class Judge
class HangmanUI
```

Пример плохих классов, которые нарушают SOLID:
```java
class Player implements Resetable
```

Пример ада:
```java
class Launcher
```

Посмотри на ютубе видео Немчинского про SOLID- по одному ролику на каждый принцип.

Подробное объяснение, как делать эту программу в процедурном и ООП стилях, есть у Сергея в расширенных материалах.

n.175(341)  
#ревью #виселица #оопвиселица #игранезапускается 