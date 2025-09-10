https://github.com/murlov/hangman  
[Тимур]

Игра в ООП стиле. Есть над чем поработать.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

Не обнаружено.

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву русского алфавита
+ 👍 Есть список введенных букв

## ЗАМЕЧАНИЯ

**1. Нейминг**

- КОНСТАНТЫ_НУЖНО_ПИСАТЬ_СТИЛЕМ_UPPER_SNAKE
```
public final static String alphabet = "абвгдеёжзийклмнопрстуфхцчшщъыьэюя";

//ПРАВИЛЬНО:
public final static String ALPHABET = "абвгдеёжзийклмнопрстуфхцчшщъыьэюя";
```

- Геттер должен называться так же, как возвращаемое им поле
```
public String getName() {
  return nameOfWord;
}

//ПРАВИЛЬНО:
public String getName() {
  return name;
}
```

- Делай понятные, но лаконичные названия
```
public class MysteryWord {
 private String nameOfWord;
 private boolean wordWasUsed;
 private StringBuilder hiddenVersionOfWord;
 //...
}

//ЛУЧШЕ:
public class MysteryWord {
 private String text;
 private boolean isUsed;
 private StringBuilder mask;
 //...
}
```

- Название должно как можно лучше объяснять суть явления
```
void updateHiddenVersion(char letter) {...}

//ЛУЧШЕ:
void openLetter(char letter) {...}
```

- Не пиши в названии классов слово "Class". И тем более не пиши в названии класса слово "Interface"- класс с названием "интерфейс" это нонсенс
```
class GameInterface
```
Если нужно обозначить в названии класса его принадлежность к "визуальному интерфейсу пользователя", используй в названии слова "Ui" или "View".

- Избыточный контекст
```
Game.startGame();

//ЛУЧШЕ:
Game.start();
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Когда нет разницы, используй постфиксную форму инкремента/декремента(i++, i--)**, а не префиксную(++i).
Иначе при рефакторинге можно надолго зависнуть вспоминая отличия постинкремена и преинкремена. 
Используй преинкремент(++n) только тогда, когда нужен именно преинкремент. 
А лучше вообще никогда не использовать преинкремент
```
++fromIndex;
--remainingAttempts;

//ПРАВИЛЬНО:
fromIndex++;
remainingAttempts--;
```

**3. Текст в exception** всегда должен быть на английском языке
```
throw new IllegalArgumentException("Введите, пожалуйста, одну букву из русского алфавита!");
```
Исключение это не просто телеграмма, которая летит сквозь слои. 
У exception особое назначение- если исключение вылетит и не будет перехвачено внутри программы, то аварийно прекратит выполнение программы.
Тогда на экране будет распечатано сообщение эксепшена, и это сообщение должно быть понятно сисадмину в любой точке планеты. 
А значит, сообщение должно быть на английском. 
Интерпритация исключения и перевод его на локальный язык должно происходить там, где это соответствует архитектуре программы или не происходить вовсе, если исключение не планируется перехватывать

**4. При прочих равных, нужно использовать примитивный тип, а не класс-обертку** 
```
Character getLetter(StringBuilder usedLetters)
void addLetter(StringBuilder usedLetters, Character letter)

//ПРАВИЛЬНО:
char getLetter(StringBuilder usedLetters)
void addLetter(StringBuilder usedLetters, char letter)
```
Для применения класса-обертки над примитивным типом данных, должна быть какая-то причина. 
Здесь использование обертки не дает ничего большего по сравнению с примитивом.

**5. Нарушение DRY**, магические буквы, числа, слова. Вводи константы
```
System.out.println("Меню:\n1.Старт\n2.Выход");
while (number != 1 && number != 2) {...}
if (number != 1 && number != 2) {...}

//ПРАВИЛЬНО:
private final static int START = 1;
private final static int QUIT = 2;

System.out.println("Меню:");
System.out.println(START + ".Старт");
System.out.println(QUIT + ".Выход");

while (number != START && number != QUIT) {...}
if (number != START && number != QUIT) {...}
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*

**6. Нарушение DRY**, магические буквы, числа, слова. Вводи константы. 

Если к одной и той же магической штуке обращаются два разных класса, то делай ее константой в одном из этих двух классов. 
Либо перенеси ее в третий класс- эти данные должны быть синхронизированы между собой
```
public class Game {
  public void startGame() throws FileNotFoundException {
    //...
    while (choice != 2)   <-- 2 это СТАРТ_ИГРЫ
    //...
  }
}

public class GameInterface {
  public int getUserChoice() {
    //...
    while (number != 1 && number != 2)   <-- 2 это СТАРТ_ИГРЫ
    //...
  }
}
```

**7. Реакция на ошибки**

+ 👍 В случае ошибки чтения файла со словами, программа корректно прекращает свою работу 
```
Файл не найден!

Process finished with exit code 0
```

- Сообщение ниочём. Какой файл не найден и что с этим делать?
```
Файл не найден!
```
Нужно сказать, что отсутствует файл со словами по (абсолютному)адресу `c:/..../название_файла.txt` и программа поэтому прекращает свою работу.

**8. class Dictionary**

- Нарушение инкапсуляции. Всегда явно указывай область видимости полей и методов.

- Проброс исключения в сигнатуре это мусор
```
void readWords(String path) throws FileNotFoundException 
```
Заменяй проверяемые исключения на непроверяемые- это избавит от необходимости прописывать исключения в сигнатуре методо. 
Изучи отличия проверяемых исключений от непроверяемых.  
*"ЧК", гл.7, "...проверяемые исключения"*

- Слишком избыточно и бессмысленно считывать строки из файла, а потом делать из каждой строки объект `MysteryWord` и загружать этот объект в список.
На это тратятся лишние ресурсы, а пользы никакой нет.  
Лучше считать строки в список, а переводить строку в объект `MysteryWord` только при вызове соответствующего метода
```
public class Dictionary {

  List<MysteryWord> words = new ArrayList<>();

  void readWords(String path) throws FileNotFoundException {
    //...
    String word = scanner.nextLine();
    words.add(new MysteryWord(word));
    //...
  }

  MysteryWord getRandomWord() {
    //...
    word = words.get(random.nextInt(words.size()));
    return word;
  }
}

//ПРАВИЛЬНО:
public class Dictionary {

  List<String> lines = new ArrayList<>();

  void readWords(String path) throws FileNotFoundException {
    //...
    String line = scanner.nextLine();
    lines.add(line);
    //...
  }

  MysteryWord getRandomWord() {
    //...
    String text = lines.get(random.nextInt(lines.size()));
    return new MysteryWord(text);
  }
}
```

- Избыточно сложная логика: ищем случайное слово среди неиспользованных слов и возвращаем его
```
MysteryWord getRandomWord() {
  //...
  do {
    word = words.get(random.nextInt(words.size()));
  } while (word.wasUsed());

  return word;
}
```
Теоретически это еще может привести к тому, что однажды программа просто зависнет- она будет получайть случайные номера слов и все эти слова будут "wasUsed". 
Просто удаляй использованные слова из словаря.  
Например, так:
```
public boolean isEmpty() {
  return words.isEmpty();
}

public MysteryWord takeRandom() {
  if(isEmpty()) {
    //бросить исключение- слова в словаре закончились
  }
  int index = random.nextInt(words.size());
  return words.remove(index);
}
```

**9. class MysteryWord**

+ 👍 Хорошо, что выявил эту сущность, которая содержит в себе текст слова, его маску и методы для работы с ними. 
Это означает, что ты уже понимаешь принципы ООП декомпозиции.

- Нарушение SRP. Экземпляр хранит информацию о том, был ли он использован(очевидно какими-то сторонними классами в интересах какого-то процесса)
```
private boolean wordWasUsed
```
Эта информация не имеет никакого отношения к единой ответстсвенности класса по хранению в себе текста-маски и отгадыванию слова. 
Информация о том, был ли использован экземпляр слова, относится к общей игровой логике.

Вместо этого флага можно немного изменить алгоритм игры. Например, если слово было использовано, его просто удалить из словаря.

- Устанавливать маску слова нужно не через вызов специального метода, а автоматически- в конструкторе `MysteryWord`. 
Иначе правила пользования классом `MysteryWord` выглядят слишком сложными и неоднозначными
```
public class Game {
  //...
  mysteryWord.setHiddenVersion();
  //...
}

public class MysteryWord {
  //...  
  public void setHiddenVersion() {
    //...
    this.hiddenVersionOfWord = new StringBuilder("_".repeat(nameOfWord.length()));
    //...
  }
}

//ПРАВИЛЬНО:
public class MysteryWord {
  public MysteryWord(String nameOfWord) {
    //...
    this.hiddenVersionOfWord = createHiddenVersion(nameOfWord);
  }
  private StringBuilder createHiddenVersion(String s) {...}
}
```

- Магическая строка: "_"

- При попытки открыть букву, которой нет в слове, нужно бросать исключение
```
void updateHiddenVersion(char letter)
```
Потому что если в алгоритме будет баг и программа попытается открыть несуществующую букву, эта операция просто проигнорируется. 
В итоге мы не сразу поймем, что программа багует.

- Избыточно. Условие в `while(...)` не делает операцию по открытию буквы быстрее и оптимальнее, а наоборот- делает ее в разы дольше.
```
while (nameOfWord.indexOf(letter, fromIndex) >= 0) {
  index = nameOfWord.indexOf(letter, fromIndex);
  hiddenVersionOfWord.setCharAt(index, letter);
  ++fromIndex;
}
```
Потому что `indexOf(...)` под капотом тоже перебирает в цикле все буквы строки `nameOfWord`. 
И выходит, что если в слове три раза встречается одна и та же буква, то цикл `while` через условие с использованием `indexOf(...)` пройдется по буквам в слове 4 раза. 
3 неполных раза до ближайшей отгадываемой буквы и один полный- от начала до конца, чтобы убедиться, что таких букв больше нет.

И ладно бы, это делало алгоритм проще и понятнее. Но проще и оптимальнее будет обычный цикл:
```
for (int i = 0; i < nameOfWord.length(), i++) {
  if(nameOfWord.charAt[i] == letter) {
    hiddenVersionOfWord.setCharAt(i, letter);
  }  
}
```

**10. class GameInterface**

+ 👍 Это view-класс, через который идет все общение с юзером и распечатка информации. В данном случае- общение и распечатка через консоль.

- В таких случаях используй многострочные строки
```
System.out.println(
    "  +---+\n" +
    "  |   |\n" +
    "  O   |\n" +
    " /|\\  |\n" +
    " / \\  |\n" +
    "      |\n" +
    "=======\n"
    );

//ПРАВИЛЬНО:
System.out.println(
    """  
      +---+
      |   |
      O   |
     /|\\  |
     / \\  |
           |
     =======
    """);
```
Многострочные строки имеются начиная с java 15.

- При распечатке виселицы, в случае попытке распечатать некорректного номера картинки, нужно бросать исключение.
Потому что попытка напечатать несуществующую картинку означает, что алгоритм где-то работает неправильно. 
Эту ситуацию нужно максимально быстро выявить и устранить
```
default:
  System.out.println("Ошибка! Не удалось вывести рисунок виселицы!");
  break;

//ПРАВИЛЬНО:
default:
  //бросить исключение
```

- Печать картинки виселицы через switch-case или if-elseif - индусский код.
Картинки нужно хранить в статическом массиве и печатать по номеру, например, так
```
private static final String[] PICTURES = {
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

public void printGallows(int numPicture) {  
  System.out.println(PICTURES[numPicture]);
}
```

- Не используй try-catch для организации ветвлений логики, оно придумано не для этого
```
while (letter == '0') {
  try {
    buffer = scanner.nextLine();
    letter = Character.toLowerCase(buffer.charAt(0));
    //...
    if (Game.alphabet.indexOf(letter) == -1) {
      throw new IllegalArgumentException("Такой буквы нет в алфавите, попробуйте ещё раз!");
    } 
    //...
  } catch (IllegalArgumentException e) {
    System.out.println(e.getMessage());
    letter = '0';
  } 
  //...
  return letter;
}

//ПРАВИЛЬНО:
while (true) {
  String buffer = scanner.nextLine();
  char letter = Character.toLowerCase(buffer.charAt(0));
  //...
  if (Game.alphabet.indexOf(letter) == -1) {
    System.out.println("Такой буквы нет в алфавите, попробуйте ещё раз!");
    continue();
  } 
  //...
  return letter;
}
```

- Циклическая зависимость. Класс `Game` использует класс `GameInterface` а тот в свою очередь использует класс `Game`
```
public class Game {
  //...
  letter = gameInterface.getLetter(usedLetters);   //Game --> GameInterface
  //...
}

public class GameInterface {
  //...
  if (Game.alphabet.indexOf(letter) == -1) {...}  //GameInterface --> Game  
  //...
}
```
View-класс не должен ничего знать про использующий его контроллер. 
В данном случае алфавит нужно передавать или в конструктор `GameInterface`, или в аргументы `getLetter()`.

- Создавай вспомогательные методы, делай программу проще и понятнее
```
if (Game.alphabet.indexOf(letter) == -1) {...}

//ПРАВИЛЬНО:
if(!isAlphabetLetter(letter)) {...}

private boolean isAlphabetLetter(char letter) {
  return Game.alphabet.indexOf(letter) >= 0;
}
```

- Для определения буквы русского языка, используется строка с перечислением всех букв алфавита, это капец индусский код
```
class Game {
  public final static String alphabet = "абвгдеёжзийклмнопрстуфхцчшщъыьэюя";
  //...
}

if (Game.alphabet.indexOf(letter) == -1) {
  //Такой буквы нет в алфавите
}
```

Сделай метод, который будет определять русскую букву по таблице символов. 
За одно изучи, как буквы хранятся в таблице ASCII/Unicode
```
private boolean isRussianLetter(char letter) {
  letter = Character.toLowerCase(letter);
  return letter >= 'а' && letter <= 'я' || letter == 'ё';
}
```

**11. class Game**

- Большой божественный метод 

Большой божественный метод состоящий из необозримого количества строк- 52
```
void startGame()
```
Делает много всего сразу, разобраться в нем трудно, код читается плохо. 
Метод разделить на несколько маленьких, каждый из которых будет делать свое.  
*Мартин "ЧК", гл.3, "Компактность!"*  
*Фаулер "Рефакторинг", гл.6, "Извлечение метода"* 

- При прочих равных, бери данные из окружения класса, а не перекидывай их между методами. 
Здесь `StringBuilder usedLetters` нужно сделать полем класса
```
private void addLetter(StringBuilder usedLetters, Character letter) {
  int firstSize = usedLetters.length();
  //...
}

//ПРАВИЛЬНО:
private final StringBuilder usedLetters = new StringBuilder();

private void addLetter(Character letter) {
  int firstSize = usedLetters.length();
  //...  
} 
```

- Код в классе читается тяжело еще и из-за длинных сложных условий в if'ах. Просто приведу одно из этих адовых условий: 
```
if (Game.alphabet.indexOf(usedLetters.charAt(i - 1)) < Game.alphabet.indexOf(letter) && Game.alphabet.indexOf(usedLetters.charAt(i)) > Game.alphabet.indexOf(letter)) {...}
```
Что здесь происходит, полностью понять никогда не удастся. 
Вводи вспомогательные методы, которые своим названием будут объяснять, что проверяет это условие.  
Примерно так:
```
if(isНазваниеКотороеВсеОбъясняет(...))  {...}

private boolean isНазваниеКотороеВсеОбъясняет(...) {
  return Game.alphabet.indexOf(usedLetters.charAt(i - 1)) < Game.alphabet.indexOf(letter) && Game.alphabet.indexOf(usedLetters.charAt(i)) > Game.alphabet.indexOf(letter);
}
```

- Сделай вспомогательные методы для определения текущего состояния игры:
```
private boolean isWin() {...}
private boolean isLose() {...}

private boolean isGameOver() {
  return isWin() || isLose();
}
```

- Если у тебя есть вью-класс, через который идет вся распечатка, то через этот класс должна идти ВСЯ распречатка
```
System.out.println("Файл не найден!");

//ПРАВИЛЬНО:
gameInterface.printFileNotFound(<полный путь к файлу>);
```

**12. class Main**, содержит точку входа main

+ 👍 Только создает и запускает Game, это хорошо.

- Не пробрасывай исключения из main в космос. 
Если ты знаешь, что может возникнуть исключение, его нужно поймать и обработать
```
public static void main (String[] args) throws FileNotFoundException
```

## ВЫВОД

Реализация неровная, где-то хорошо, где-то не очень. В целом, норм. 

n.120(224)  
#ревью #виселица #оопвиселица 