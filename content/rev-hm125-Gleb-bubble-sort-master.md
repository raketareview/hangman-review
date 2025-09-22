https://github.com/bubble-sort-master/HangMan  
[Глеб]

Игра в процедурном стиле, выполнена в двух классах.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

Не обнаружено.

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву русского алфавита
+ 👍 Есть список введенных букв

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Константы должны быть final
```
private static int WORD_MAX_LENGTH = 7;

//ПРАВИЛЬНО:
private static final int WORD_MAX_LENGTH = 7;
```

- Избыточно
```
void runNewGameLoop(ArrayList<String> dictionary)

//ПРАВИЛЬНО:
void start(ArrayList<String> dictionary)
```

- "Буква" это одиночный символ. Строка может содержать более одной буквы
```
boolean isRussianLetter(String letter)

//ПРАВИЛЬНО:
boolean isRussianLetter(char letter)
```

- "Hangman" это одно слово
```
class HangManPicture

//ПРАВИЛЬНО:
class HangmanPicture
```

- Для одной концепции должно быть одно название. Распечатка должна быть либо "print" либо "display"
```
void displayHangMan(...)
void printWordProgress(...)
```

- Однобуквенное имя переменной цикла в foreach должно быть первой буквой типа данных. 
Либо не должно быть только одной буквой- нет жесткого требования, чтобы в foreach эта переменная называлась одной буквой 
```
for (char l : word)

//ПРАВИЛЬНО ТАК:
for (char c : word)

//ИЛИ ТАК:
for (char letter : word)
```

- Этот метод не инициализирует словарь. 
Потому что инициализация происходит в отношении того объекта, который уже существует. 
А этот метод создает и возвращает словарь
```
ArrayList<String> initialiseDictionary()

//ПРАВИЛЬНО:
ArrayList<String> createDictionary()
```
*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Нарушение DRY**, магические буквы, числа, слова. Вводи константы
```
System.out.println("Чтобы начать новую игру -- введите 1");
System.out.println("Чтобы покинуть игру  -- введите 2");
case "1":  //...
case "2":  //...

//ПРАВИЛЬНО:
private final static String START = "1";
private final static String QUIT = "2";

System.out.println("Чтобы начать новую игру -- введите " + START);
System.out.println("Чтобы покинуть игру  -- введите " + QUIT);
case START:  //...
case QUIT:  //...
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*

**3. Если в блоке if есть return**(break, continue, throw, exit и т.д.), то else не пишется.  
В этом случае неважно, будет else или нет, так как программа будет работать одинаково и код без `else` будет выглядеть читабельней
```
if (mistakeCount == MAX_MISTAKES) {
  //...
  return;
} else if (isWordSolved(wordToGuess, correctGuesses)) {
  //... 
  return;
}

//ПРАВИЛЬНО:
if (mistakeCount == MAX_MISTAKES) {
  //...
  return;
} 
if (isWordSolved(wordToGuess, correctGuesses)) {
  //... 
  return;
}
```

**4. Удаляй участки кода, когда они становятся бессмысленным**
```
int a = 1213;
```

**5. Нарушение инкапсуляции**. Публичным должен быть только метод `main()`. Остальные методы и поля здесь должны быть приватными.

**6. Используй классы через их интерфейсы**
```
ArrayList<String> correctGuesses = new ArrayList<>();
ArrayList<String> wrongGuesses = new ArrayList<>();
void runNewGameLoop(ArrayList<String> dictionary)

//ПРАВИЛЬНО:
List<String> correctGuesses = new ArrayList<>();
List<String> wrongGuesses = new ArrayList<>();
void runNewGameLoop(List<String> dictionary)
```
Общее правило: ArrayList нужно использовать через List, HashMap- через Map и т.д. 
Это позволяет пользоваться преимуществами полиморфизма.

Да, бывают ситуации, когда, например, с LinkedList нужно работать именно как с LinkedList, а не с List. Но это уже нюансы.

**7. Цикл while** всегда лучше читается, когда он написан в варианте `while() {...}`, а не `do {...} while()` - тогда сразу видно условие входа в цикл и выхода из него. 
Если есть возможность, `do-while` нужно переделывать на обычный `while`. 
Вечные циклы всегда пиши только так
```
public static void runNewGameLoop(ArrayList<String> dictionary) {
  //...
  do {
    //миллион строк
  } while (true);
}

//ЛУЧШЕ:
public static void runNewGameLoop(ArrayList<String> dictionary) {
  //...
  while (true) {
    //миллион строк
  } ;
}
```

**8. Бери данные из окружения**

Если одни и те же данные используются в разных методах класса, то эти данные нужно не перекидывать туда-сюда между методами, а сделать их полями класса
```
public static boolean isAlreadyGuessedLetter(String letter, ArrayList<String> correctGuesses, ArrayList<String> wrongGuesses) {...} 
public static boolean isWordSolved(String guessedWord, ArrayList<String> correctGuesses) {...}

//ТУТ ПРАВИЛЬНО:
private static String guessedWord;
private static List<String> correctGuesses;
private static List<String> wrongGuesses;

private static boolean isAlreadyGuessedLetter(char letter) {...} 
private static boolean isWordSolved() {...}
```

**9. Последовательность выполнения действий**

Загрузка словаря словами происходит раньше диалога "Играть или выйти?".  
Поэтому, если при первом запуске игры юзер выберет "Выйти", то чтение файла и загрузка словаря словами были зря
```
ArrayList<String> dictionary = initialiseDictionary();
//диалог "Играть или выйти"
```

**10. Создавай вспомогательные методы**, делай программу более простой и понятной
```
do {
  //...
  if (mistakeCount == MAX_MISTAKES) {
    //печатает сообщения о проигрыше
    return;
  } else if (isWordSolved(wordToGuess, correctGuesses)) {
    //печатает сообщения о проигрыше
    return;
  }
} while (true);

//ПРАВИЛЬНО:
while (!isGameOver()) {
  //процесс игры
  if (isLose()) {
    printLoseMessage();
  } else if (isWin()) {
    printWinMessage();
  }
}
boolean isWin() {...}
boolean isLose() {...}

boolean isGameOver() {
  return isWin() || isLose();  
}
```

**11. Реакция на ошибки**

- Если по указанному адресу в `class Dictionary` не будет найден файл со словами, то программа аварийно вылетит и напечатает непонятные(для юзера) сообщения в консоль
```
Чтобы начать новую игру -- введите 1
Чтобы покинуть игру  -- введите 2
java.io.FileNotFoundException: dictionary.txt_ (Не удается найти указанный файл)
	at java.base/java.io.FileInputStream.open0(Native Method)
```
Здесь должна быть другая реакция на отсутствие файла со словами.  
Нужно сказать юзеру, что файл со словами по указанному пути открыть не удалось и поэтому работа программы будет завершена. 

Причем, путь нужно указать не относительный, как у тебя(`dictionary.txt_`). 
А абсолютный, чтобы юзер знал, где конкретно ему нужно искать файл: `c:/..../dictionary.txt_`.

После этого корректно завершить работу программы без вываливания красных эксепшенов в консоль.

**12. Метод должен делать что-то одно**

- Есть метод, который может выполнить одно из двух действий: получить букву и записать ее в угаданные или получить букву и записать ее в ошибочные.

Чтобы узнать, что конкретно *на этот раз* выполнил метод, он в виде String возвращает код результата 
```
String processUserGuess(String wordToGuess, ArrayList<String> correctGuesses, ArrayList<String> wrongGuesses) {
  //...
  if(...) {
    //...
    return CORRECT_GUESS; <-- КОД РЕЗУЛЬТАТА ОПЕРАЦИИ: УГАДАЛИ БУКВУ
  }
  //...
  return WRONG_GUESS; <-- КОД РЕЗУЛЬТАТА ОПЕРАЦИИ: НЕ УГАДАЛИ БУКВУ
}
```
Каждый метод должен делать что-то одно. Раздели этот метод на несколько, каждый из которых будет делать только свое.

Методы не должны возвращать коды операций: сделал то, сделал сё.  
Метод должен сделать именно то, что задано его контрактом(то есть, именем). 
Если метод по какой-то причине не может выполнить то, что он заявил своим названием, то он, опять же, должен не вернуть код ошибки, а кинуть исключение.  
*"ЧК", гл.3, "...вместо кодов ошибок"*

- В этом методе *на одном уровне абстракции* реализовано несколько действий.

Присмотрись- там в методе явно видно два отдельных смысловых блока, которые ты интуитивно разделил пустой строкой.
Вынеси этот код во вспомогательные методы 
```
void printWordProgress(...) {
  //миллион строк кода
}

//ПРАВИЛЬНО:
void printWordProgress(...) {
  System.out.println("Текущее состояние слова:");
  firstMethod();
  secondMethod();
}
```

**12. class HangManPicture**

+ 👍  Хорошо, что картинки и метод их распечатки вынесены в отдельный клас. Это разгружает основной класс с игровой логикой от графики.

- В любом switch-case должен быть default. В данном случае default должен бросать исключение с сообщением, что такого номера картинки не существует.

- Используй многострочные строки
```
//ПЛОХО:
System.out.println(
  "-----  " +
  "|   |  " +
  "|   O  " +
  "|      " +
  "|      " +
  "-------"
);

//ХОРОШО:
System.out.println(
  """
  -----   
  |   |   
  |   O   
  |       
  |       
  ------- 
  """);
```

- Печать картинки виселицы через switch-case или if-elseif - индусский код.  
Картинки нужно хранить в статическом массиве и печатать по номеру.  
Например, так
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

public static void printPicture(int numPicture) {  
  System.out.println(PICTURES[numPicture]);
}
```

## ВЫВОД

Для развития навыков процедурной декомпозиции, посмотреть стрим Сергея [Крестики-нолики в процедурном стиле](https://www.youtube.com/watch?v=PPikj1qHxrA)

n.125(233)  
#ревью #виселица 