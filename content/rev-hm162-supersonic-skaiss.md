https://github.com/skaiss/hangman-game  
[supersonic]

Игра в процедурном стиле.  
Выполнена в одном классе.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Игра не запускается
```java
WANNA PLAY WITH ME? 
 1 - YES    0 - NO 
1
 
NEW GAME STARTED
Exception in thread "main" java.lang.IllegalArgumentException: bound must be positive
	at java.base/java.util.Random.nextInt(Random.java:551)
	at Hangman.getRandomWord(Hangman.java:105)
```

2. Можно ввести не одну букву, а набор букв и это будет считаться допустимым вводом
```java
type your guess, one letter:
zzzz
oops MISTAKE!
used letters: [z]
```

## ХОРОШО

+ 👍 Есть список введенных букв
+ 👍 Можно вводить буквы только определенного алфавита- английского
+ 👍 Аккуратные картинки и в целом приятный понятный UI
```
__________
| /   |
|     O
|    /П\
|     LL
===========
```

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Венгерская нотация.

В названии переменных не пиши тип данных, к которым они относится.  
И вообще не употребляй венгерскую ноттацию.  
Название переменной должно отвечать на вопрос что хранит переменная, а не как хранит
```java
List<String> listOfWords = new ArrayList<>();

//ПРАВИЛЬНО:
List<String> words = new ArrayList<>();
```

- Избыточно. 

Уточнение "мой" имеет смысл делать только тогда, когда есть противопостовления с "не мой".  
А здесь нет такого противопоставления, тут все переменные- "мои"
```java
char myGuess;

//ПРАВИЛЬНО:
char quess;
```

- Геттер должен что-то возвращать. Геттер, который ничего не возвращает- нонсенс
```java
void getUserInput(Scanner scanner) 
```

- Константы нужно писать стилем UPPER_SNAKE.

Судя по тому, что ты написала статическую финальную переменную типа StringBuilder стилем UPPER_SNAKE, 
ты придерживаешься конвенции кода Oracle, а не Google.  
В таком случае и переменную типа Random здесь нужно писать стилем UPPER_SNAKE
```java
private static final Random random = new Random();
private static final StringBuilder HIDDEN_WORD = new StringBuilder();

//ПРАВИЛЬНО:
private static final Random RANDOM = new Random();
private static final StringBuilder HIDDEN_WORD = new StringBuilder();
```

- Частицу "To" не используй в названии переменных
```java
int letterToGuess
```
Частицу "To" используй только в названии методов, которые преобразуют что-то одно во что-то другое.  
Например: `intToString()`, `longToFloat()` etc.  
Переменные, в отличии от методов, никаких преобразований проводить не могут.  

- Название методов должно быть глаголом в повелительном наклонении. 

Это название я перевожу как "угадал правильно"
```java
void guessIsRight()

//ПРАВИЛЬНО:
void глаголКоторыйВсёОбъясняет()
```
Нельзя даже предположить, что делает метод с названием "угадал правильно".  
Вернее, можно предположить, что он проверяет угадали букву или нет. Но этот метод это не проверяет.

Частицу "is" используй только в начале названий булевых методов, согласно конвенции.  
В остальных случаях НЕ ИСПОЛЬЗУЙ ВООБЩЕ, чтобы не вводить в заблуждение.

- Глаголы!
```java
void rightGuessMessage()

//ПРАВИЛЬНО:
void printRightGuessMessage()
```
*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Соблюдается инкапсуляция**

👍 В классе только метод `main()` публичный.  
Остальные поля и методы приватные, это хорошо.

**3. Exception's**

Сообщения в исключениях делай информативными, а не формальными
```java
try {
  listOfWords = Files.readAllLines(Paths.get(WORDS_FILE_PATH));
} catch (IOException e) {
  System.out.println("no such file...");
}

//ПРАВИЛЬНО:
try {...} catch (IOException e) {
  System.out.println("no such file: " + WORDS_FILE_PATH);
}
```

**4. Форматирование**

- Если нужно печатать или создавать строку с более, чем одним подстановочным значением или значение вставляется внутрь сообщения, 
используй форматирование- тогда сразу будет виден весь шаблон
```java
System.out.println("you already used this letter: " + userGuess + "!");

//ПРАВИЛЬНО:
System.out.printf("you already used this letter: %c!  %n", userGuess);
```

- Не вставляй перевод строки внутрь строк при распечатке.

Иначе при чтении кода приходиться мысленно "разворачивать" одну строку в несколько.  
Если нужно распечатать несколько строк, то печатай их или многострочными строками(""") или отдельными инструкциями печати.  
Тогда сразу будет видно, как текст будет выглядеть на печати
```java
System.out.println("WANNA PLAY WITH ME?" + " \n" + " 1 - YES    0 - NO ");

//ПРАВИЛЬНО:
System.out.println("WANNA PLAY WITH ME?");
System.out.println(" 1 - YES    0 - NO ");
```

- Не иммеет смысла использовать только одну инструкцию печати, когда все равно печать занимает несколько горизонталей
```java
System.out.println("oops MISTAKE!\n" +
        "used letters: " + USED_LETTERS + "\n" +
        "number of mistakes: " + mistakesCount + "\n" +
        "number of tries left: " + (MAX_MISTAKES - mistakesCount));

//ПРАВИЛЬНО:
System.out.println("oops MISTAKE!");
System.out.println("used letters: " + USED_LETTERS);
System.out.println("number of mistakes: " + mistakesCount);
System.out.println("number of tries left: " + (MAX_MISTAKES - mistakesCount));
```

**5. Нарушение DRY**

- Магические буквы, числа, слова. Вводи константы 
```java
System.out.println("WANNA PLAY WITH ME?" + " \n" + " 1 - YES    0 - NO ");

case "1":  //...
case "0":  //...
System.out.println("choose 1 for YES and 0 for NO");

//ПРАВИЛЬНО:
private final static String START = "1";
private final static String QUIT = "0";

System.out.println("WANNA PLAY WITH ME?");
System.out.printf(" %s - YES    %s - NO  %n", START, QUIT);

case START:  //...
case QUIT:  //...
System.out.printf("choose %s for YES and %s for NO  %n", START, QUIT);
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*

- Дублирование кода.  
Используй вспомогательные методы
```java
private static void rightGuessMessage() {
  System.out.println("guess is right!  " + "\n" +
        "used letters: " + USED_LETTERS + "\n" +
        "number of mistakes: " + mistakesCount + "\n" +
        "number of tries left: " + (MAX_MISTAKES - mistakesCount));
}

private static void mistakeMessage() {
  System.out.println("oops MISTAKE!\n" +
        "used letters: " + USED_LETTERS + "\n" +
        //то же самое
}

//ПАВИЛЬНО:
private static void printQuessedRightMessage() {
  System.out.println("guess is right!  ");
  printGameStatus();
}

private static void printMistakeMessage() {
  System.out.println("oops MISTAKE!");
  printGameStatus();
}
```

**6. Печать картинок**

+ 👍 Хранение картинок в массиве-константе это хорошо
```java
private static final String[] HANGMAN_STAGES = {
  //картинки
}
```

- Картинки вынеси в отдельный класс.

Картинки виселицы и метод их выдачи лучше вынести в отдельный класс, чтобы в основном классе не смешивать логику и графику.  
Процедурный стиль программирования позволяет делать разные классы.  
Просто тогда эти классы интерпритируются не как объекты ООП, а как модули процедурного стиля программирования.

**7. Правило одной опрерации**

Каждый метод должен выполнять только одну операцию на одном уровне абстракции.  
Этот метод выполняет несколько операций
```java
private static boolean isGameStillPlaying() {
  if (mistakesCount == MAX_MISTAKES) {
    //печатает текст при проигрыше
    return false;
  } else if (letterToGuess == 0) {
    //печатает текст при выигрыше
     return false;
  }
  return true;
}
```

Метод нужно разделить на несколько:
```java
boolean isWin() {...}
boolean isLose() {...}

boolean isGameOver() {
  return isWin() || isLose();  
}

void printWinMessage() {...}
void printLoseMessage() {...}
```
*Мартин, "Чистый код", гл.3, "Правило одной операции", "Один уровень абстракции на функцию"*

То же самое касается и других методов: `boolean isInputValid(char userGuess)` etc.

**8. Создавай вспомогательные методы, делай программу более простой и понятной**
```java
if (USED_LETTERS.contains(userGuess)) {
  //...
} else if (!Character.isLetter(userGuess) || userGuess < 'a' || userGuess > 'z') {
  //...
}

//ПРАВИЛЬНО:
if (isUsedLetters(userGuess)) {
  //...
} else if (!isEnglishLetter(userGuess)) {
  //...
}
```
Даже, если метод состоит из одной строки, но при этом делает код программы читабельней, то этот метод имеет право на существование.

**9. Не делай сложных условий в if'ах, это плохо читается.**

Здесь условие очень трудно понять, потому что в теле if'a выполняется несколько операций.  
Сложные условия объясняй через поясняющие переменные или вспомогательные методы 
```java
if (randomSecretWord.contains(String.valueOf(myGuess))) {
  guessIsRight();
}

//ПРАВИЛЬНО:
if (isSecretWordLetter(myGuess){
  guessIsRight();    
}

private static boolean isSecretWordLetter(char symbol) {
  return randomSecretWord.contains(String.valueOf(symbol))
}
```

**10. Слишком длинные конструкции**  
Здесь в одну строку сведено три инструкции, поэтому непонятно, что здесь происходит 
```java
HIDDEN_WORD.append(HIDE_SYMBOL.repeat(randomWord.length()));
```
Вводи поясняющие переменные.
*Фаулер, "Рефакторинг", гл.6, "Введение поясняющей переменной"*

## ВЫВОД

В целом, для процедурного стиля относительно неплохо.

В процедурном стиле программирования нужно научиться делать маленькие методы, 
каждый из которых будет делать только одно дело.

Для развития навыков процедурной декомпозиции, посмотреть стрим Сергея  
[Крестики-нолики в процедурном стиле](https://www.youtube.com/watch?v=PPikj1qHxrA)

Подробное объяснение, как делать эту программу в процедурном и ООП стилях, есть у Сергея в расширенных материалах.

n.162(317)  
#ревью #виселица 