https://github.com/MonYamau/Hangman  
[Лиза]

Игра в процедурном стиле, состоит из двух классов.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Игра не запускается
```java
[Введите "Н", чтобы начать новую игру]
[Введите "В", чтобы выйти]
н
Exception in thread "main" java.lang.IllegalStateException: Couldn't read the file: C:\MyReviews\PROG\HM-Лиза-\Hangman-main\src\src\main\resources\dictionary.txt
	at main.java.Game.generateWord(Game.java:85)
```

## ХОРОШО

+ 👍 Можно ввести только одиночную букву русского алфавита
+ 👍 Есть список введенных букв
+ 👍 Активно применяются константы

## ЗАМЕЧАНИЯ

**1. Нейминг**

- КОНСТАНТЫ_НУЖНО_ПИСАТЬ_СТИЛЕМ_UPPER_SNAKE
```java
private static final String[] hangmanStatus

//ПРАВИЛЬНО:
private static final String[] HANGMAN_STATUS
```

- Не вижу причин, почему сканер нельзя назвать "сканер", если он сканер
```java
private static final Scanner INPUT = new Scanner(System.in);

//ПРАВИЛЬНО:
private static final Scanner SCANNER = new Scanner(System.in);
```

- Избыточно. Мы и так понимаем, что словарь состоит из слов
```java
List<String> dictionaryWords;

//ПРАВИЛЬНО ТАК:
List<String> dictionary;

//ТАК ТОЖЕ ПРАВИЛЬНО:
List<String> words;
```

- Название не соответствует тому, что делает метод.  
Этот метод получает от юзера одну большую русскую букву
```java
char validateInputLetter()

//ПРАВИЛЬНО:
chat inputRussianUpperLetter()
```

- Название не соответствует тому, что делает метод
```java
private static void incrementErrorCount(char letter) {
  //...
  mistakeCount++;
}

//ПРАВИЛЬНО:
private static void incrementMistakeCount(char letter) {
  //...
  mistakeCount++;
}
```
*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Нарушение DRY**, магические буквы, числа, слова. Вводи константы. А если они уже есть- пользуйся 
```java
private static final String START = "Н";
private static final String EXIT = "В";
private static final String INSTRUCTIONS_SCRIPT = """
    Желаешь начать новую игру?
    [Введите "Н", чтобы начать новую игру]
    [Введите "В", чтобы выйти]
    """;

//ПРАВИЛЬНО:
private static final String START = "Н";
private static final String EXIT = "В";
private static final String INSTRUCTIONS_SCRIPT = """
    Желаешь начать новую игру?
    [Введите '%s', чтобы начать новую игру]
    [Введите '%s', чтобы выйти]
    """.formatted(START, EXIT);
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*    

**3. Форматирование**

- Используй форматирование только тогда, когда слово нужно вставить внутрь другого слова, а не сбоку
```java
System.out.printf("Количество ошибок: %d\n", mistakeCount);
System.out.printf("Привет! %s", INSTRUCTIONS_SCRIPT);

//ПРАВИЛЬНО:
System.out.println("Количество ошибок: " + mistakeCount);
System.out.print("Привет! " + INSTRUCTIONS_SCRIPT);
```

- В таких случаях принято использовать одинарные кавычки- с ними меньше мороки
```java
System.out.printf("Буквы \"%s\" нет в данном слове.\n", letter);

//ЛУЧШЕ:
System.out.printf("Буквы '%s' нет в данном слове.\n", letter);
```

- Приоритет- понятность кода.

Если распечатку трех строк здесь сделать не одной командой, а тремя отдельными, это сделает код более понятным.  
И чтобы представить, как в итоге распечатается текст, нам не придется мысленно "разворачивать" одну строку в несколько
```java
System.out.printf("Ты проиграл Х_Х\nЗагаданное слово: %s\n" + INSTRUCTIONS_SCRIPT, secretWord);

//ПРАВИЛЬНО:
System.out.println("Ты проиграл Х_Х");
System.out.println("Загаданное слово: " + secretWord);
System.out.println(INSTRUCTIONS_SCRIPT);
```

**4. Не завершай работу программы через exit**
```java
private static void processStartChoice() {
  while (true) {
    //...
      case EXIT: exit(0);
    }
  }
}
```
На это есть много причин: 
- При выходе через exit могут не закрыться некоторые ресурсы программы.
- Каждый метод имеет право закончить только свою работу(напр. через `return`), а не работу всего приложения.

Здесь достаточно сделать выйти из метода через `return`, тогда и вся программа завершит свою работу:
```java
public static void main(String[] args) {
  System.out.printf("Привет! %s", INSTRUCTIONS_SCRIPT);
  processStartChoice();
}

private static void processStartChoice() {
  while (true) {
    //...
      case EXIT: return; //выйдет в main() и там завершит работу программы
    }
  }
}
```

**5. Уместная оптимизация**

- Иногда поясняющая переменная бывает лишней и лучше сразу сделать return.  
Посмотри- IDE это подчеркивает желтым. Это она подсказывает, что можно сразу вернуть значение
```java
char letter = inputValue.charAt(0);
return letter;

//ТУТ ПРАВИЛЬНО:
return inputValue.charAt(0);
```

**Лайфхак:** если идея что-то подчеркивает желтым, значит она дает какую-то подсказку.  
Ставишь курсор на подсказку, слева появляется желтая лампочка,
клацаешь по лампочке левой кнопкой мышки и смотришь подсказки.

**6. Минимизируй область видимости переменной**

Объявляй переменную там, где ты ее используешь
```java
String inputValue = null;
while (!validate) {
  inputValue = INPUT.nextLine();
  //...
}

//ПРАВИЛЬНО:
while (!validate) {
  String inputValue = INPUT.nextLine();
  //...
}
```
*Блох, "Java. Эффективное программирование", изд.3, гл.9.1*

**7. Работа с регулярным выражением**

- С помощью подходящей регулярки можно сразу проверять строку на то, что она длиной в одну букву и эта буква- русская
```java
private static final String ACCEPTABLE_LETTERS = "[а-яёА-ЯЁ]+";

boolean isRus = Pattern.matches(ACCEPTABLE_LETTERS, inputValue);
if (inputValue.length() != 1 || !isRus) {
  //...
}

//ПРАВИЛЬНО:
private static final String ONE_RUS_LETTER_REGEX = "[а-яёА-ЯЁ]";

if (!isOneRussianLetter(inputValue)) {
  //...
}

private static boolean isOneRussianLetter(String s) {
  return  Pattern.matches(ONE_RUS_LETTER_REGEX, s); 
}
```

- Компилируй регулярные выражения. 

Если регулярное выражение используется многократно, как здесь, то нужно его откомпилировать.  
Компиляция регулярного выражения в объект `Pattern` с помощью `Pattern.compile()` позволяет повысить производительность при многократном использовании
```java
private static final String ONE_RUS_LETTER_REGEX = "[а-яёА-ЯЁ]";

private static boolean isOneRussianLetter(String s) {
  return  Pattern.matches(ONE_RUS_LETTER_REGEX, s); 
}

//ПРАВИЛЬНО:
private static final String ONE_RUS_LETTER_REGEX = "[а-яёА-ЯЁ]";
private static final Pattern ONE_RUS_LETTER_PATTERN = Pattern.compile(ONE_RUS_LETTER_REGEX);

private static boolean isOneRussianLetter(String s) {
  Matcher matcher = ONE_RUS_LETTER_PATTERN.matcher(word);
  return matcher.matches(); 
}
```
*Блох "Java. Эффективное программирование", изд.3, гл.2.6*

**8. Не используй исключения для ветвлений бизнес логики**
```java
private static char validateInputLetter()
  //...
  boolean validate = false;
  while (!validate) {
    validate = true;
    //...
    if (inputValue.length() != 1 || !isRus) {
      validate = false;
      try {
        throw new IOException();
      } catch (IOException e) {
        System.out.println(INCORRECT_INPUT_SCRIPT);
      }
    }
    inputValue = inputValue.toUpperCase();
  }

//ПРАВИЛЬНО:
while (!validate) {
  validate = true;
  //...
  if (inputValue.length() != 1 || !isRus) {
    System.out.println(INCORRECT_INPUT_SCRIPT);
    validate = false;
  }
  inputValue = inputValue.toUpperCase();
}
```
*Хорстманн "Java. Библиотека профессионала", т1., гл.11*
```java
"Обработка исключений не может заменить собой простую проверку" - Хорстманн.
```

Но, вообще, этот метод у тебя получился какой-то избыточно сложный.  
В чем состоит его задача, получить одну русскую букву?  
Тогда делаем примерно так:
```java
private static char inputRussianLetter() {
  System.out.println("Попробуй ввести букву:");  
  while(true) {
    String s = INPUT.nextLine();
    if(isOneRussianLetter(s)) {
      return s.charAt(0);  
    }
    System.out.println(INCORRECT_INPUT_SCRIPT);
  }
}
```
И да, в этом методе не надо приводить букву к большому регистру.  
Иначе метод будет делать слишком много разного.  
Если для алгоритма нужно получать букву в определенном регистре, то пусть это делает клиент этого метода.  
Тогда алгоритм программы будет более наглядным
```java
char letter = validateInputLetter();

//ПРАВИЛЬНО:
char letter = inputRussianLetter();
letter = Character.toUpperCase(letter);
```

**9. Вводи вспомогательные методы, инкапсулируй условные конструкции**

Не делай сложных условий в if'ах.  
Например, чтобы понять это условие, нужно в голове последовательно выполнить три операции:  
Понять, что происходит преобразование чара в строку.  
Понять, что ищется входждение этой строки в секретном слове.  
И напоследок выполнить операцию "не"  
```java
if (!secretWord.contains(String.valueOf(letter))) {
  //действия, если буквы нет в слове
}
```

Первые две из этих операций можно объединить в один вспомогательный метод.  
Он будет определять, что такая буква есть в слове
```java
if(!isSecretWordLetter(letter)) {
  //действия, если буквы нет в слове
}

private static boolean isSecretWordLetter(char letter) {
  String s = String.valueOf(letter);  
  return secretWord.contains(s);
}
```
*"ЧК", гл.17, G28, "Инкапсулируйте условные конструкции"*

В этом случае всё равно в условии if'а будут две операции: `!` и `isSecretWordLetter()`.  
Но такое условие понятно и нормально читается.

Кстати, может возникнуть искушение написать метод, который проверяет не наличие, а отсутствие буквы:
```java
if(isNotSecretWordLetter(letter)) {
  //действия, если буквы нет в слове
}
```

Но ты не делай так- "отрицательные" методы в коде программы читаются хуже "положительных":
```java
//❌ ЭТО ПЛОХО 
boolean isNotSecretWordLetter(char letter) {  
  //проверяет на отсутствие буквы
}  

//✔️ ЭТО ХОРОШО
boolean isSecretWordLetter(char letter) {
  //проверяет на наличие буквы
}
```

**10. Вводи вспомогательные методы, делай программу более простой и понятной**

Даже если метод состоит из одной или двух строк, но при этом делает код программы читабельней, 
то этот метод имеет право на жизнь:
```java
displayField = "_ ".repeat(secretWord.length()).trim();

//ЛУЧШЕ:
displayField = createMask(secretWord);

private static String createMask(String s) {
  return MASK_SYMBOL.repeat(s.length()).trim();
}
```

**11. Рекурсия**

В алгоритме программы есть рекурсия
```java
+-> void processStartChoice() 
|         ↓
|   void startGameRound() 
|         ↓
+---private static boolean isGameOver() { ... processStartChoice();}
```

Рекурсия должна использоваться только там, где она оптимальна алгоритмически.
Например, при обходе бинарного дерева.

В других ситуациях, где рекурсия может быть замененена циклом, она должна быть им замененена. 
Рекурсия тяжело читается и при определенных условиях может привести к переполнению стека. 
Убери рекурсию, замени ее использование на цикл while().  

Условный пример
```java
//❌ ПЛОХО:
void start() {
  printMessage("Введите команду: 0-выйти, 1-играть");  
  int command = inputCommand();

  if(command == 0) {
    return;
  } 

  if(command == 1) {
    playGame();
  } 

  start();  <-- РЕКУРСИЯ
}

//✔️ ХОРОШО:
void start() {
  while(true) {
    printMessage("Введите команду: 0-выйти, 1-играть");  
    int command = inputCommand();

    if(command == 0) {
      return;
    } 

    if(command == 1) {
      playGame();
    } 
  }  
}
```

**12. Правило одной операции**

Каждый метод должен выполнять только одну операцию.  
В этой реализации есть методы, которые выполняют более одной операции.  
Например:

```java
private static void incrementErrorCount(char letter) {
  if (!secretWord.contains(String.valueOf(letter))) {
    System.out.printf("Буквы \"%s\" нет в данном слове.\n", letter);
    mistakeCount++;
  }
}
```
Судя по названию, этот метод должен только увеличить счетчик ошибок.  
Но на самом деле, он делает несколько вещей сразу:  
Увеличивает счетчик ошибок.  
Распечатывает информацию о количестве ошибок.

Увеличить счетчик и напечатать сообщение об этом- это две разные операции.

При этом если метод называется "увеличить счетчик" то он должен делать буквально то, что обещает- просто увеличить счетчик.  
А не увеличить счетчик по какому-то условию, которое не оговорено контрактом метода:
```java
//❌ ЭТО НЕПРАВИЛЬНО 
private static void incrementErrorCount(char letter) {
  if (!secretWord.contains(String.valueOf(letter))) {
    mistakeCount++;
  }
}

//✔️ ЭТО ПРАВИЛЬНО
private static void incrementMistakeCount() {
  mistakeCount++;
}
```

И тогда правильное использование метода, соблюдающего правило одной операции, будет выглядеть вот так:
```java
//❌ ПЛОХО:
while (!isGameOver()) {
  //...  
  incrementErrorCount(letter);
}

//✔️ ХОРОШО:
while (!isGameOver()) {
  //...  
  if(isSecretWordLetter(letter)) {
    //действия, если буква есть в слове
  } else {
    incrementMistakeCount(); //или просто mistakeCount++
    printMistakeLetterMessage(letter);
  }
}
```

Этот метод тоже выполняет несколько операций сразу:
```java
private static boolean isGameOver() {
  if (mistakeCount == MAX_ERROR) {
    System.out.printf("Ты проиграл Х_Х\nЗагаданное слово: %s\n" + INSTRUCTIONS_SCRIPT, secretWord);
    processStartChoice();
  }
  if (!displayField.contains("_")) {
    System.out.printf("ТЫ ВЫИГРАЛ! ^О^\nЗагаданное слово: %s\n" + INSTRUCTIONS_SCRIPT, secretWord);
    processStartChoice();
  }
  return false;
}

//ПРАВИЛЬНО:
private static boolean isGameOver() {
  return isLose() || isWin();
}

private static boolean isWin() {
  return !displayField.contains(MASK_SYMBOL);
}

private static boolean isLose() {...}

private static void printWinMessage() {
  System.out.prinln("ТЫ ВЫИГРАЛ!");
  System.out.prinln("Загаданное слово: " + secretWord);
  System.out.prinln(INSTRUCTIONS_SCRIPT);
}
private static void printLoseMessage() {...}
```
*Мартин, "Чистый код", гл.3, "Правило одной операции"*

**13. Сложные алгоритмы**

Придумывай простые алгоритмы.  
Например, открытие буквы в маске сейчас происходит вот так:
```java
private static String secretWord;
private static String displayField;

private static void openLetter(char letter) {
  List<String> iterateWord = new ArrayList<>(Arrays.asList(secretWord.split("")));
  List<String> iterateField = new ArrayList<>(Arrays.asList(displayField.split(" ")));
  for (int i = 0; i < secretWord.length(); i++) {
    if (iterateWord.get(i).equals(String.valueOf(letter))) {
      iterateField.set(i, String.valueOf(letter));
    }
  }
  displayField = String.join(" ", iterateField);
}
```

Этот алгоритм можно сделать значительно проще:
```java
private static String secretWord;  //слово игры: "инкапсуляция"
private static StringBuilder mask; //маска игры: "и-ка--ул-ци-"

private static void openLetter(char letter) {
  for (int i = 0; i < secretWord.length(); i++) {
    if (secretWord.charAt(i) == letter) {
      mask.setCharAt(i, letter);
    }
  }
}
```
Написание простых и понятных алгоритмов это тренируемый навык, для развития которого нужно практиковаться.

**14. Чтение слова**

- Чтение слова из файла лучше вынести в отдельный класс `Dictionary`, тогда повысится читаемость кода в проекте.  
Процедурный стиль программирования дозволяет делать несколько классов в проекте.

- Чтение слов из файла не работает.  
Но за сообщение с абсолютным путем к файлу мегареспект 👍👍👍👍👍
```java
Couldn't read the file: C:\MyReviews\PROG\HM-Лиза-\Hangman-main\src\src\main\resources\dictionary.txt
```
Теперь юзер по этому абсолютному пути хотя бы сможет проверить у себя на диске наличие этого файла.

+ 👍 Хорошо, что перехватываешь проверяемое исключение и вместо него кидаешь непроверяемое.  
👍 Хорошо также, что кидаешь не базовое непроверяемое исключение RuntimeException, а более конкретизированнное
```java
try {...} catch (IOException e) {
  throw new IllegalStateException("Couldn't read the file: " + PATH.toAbsolutePath(), e);
 }
```

- Корректно завершай работу программы.

Если файл не читается и бросается исключение, то это исключение нужно перехватить и обработать.  
В данном случае- перхватить и написать юзеру соответствующее сообщение без вываливания красных эксепшенов в консоль.  
Примерно, так:
```java
[Введите "Н", чтобы начать новую игру]
[Введите "В", чтобы выйти]
н
Exception in thread "main" java.lang.IllegalStateException: Couldn't read the file: C:\MyReviews\PROG\HM-Лиза-\Hangman-main\src\src\main\resources\dictionary.txt
	at main.java.Game.generateWord(Game.java:86)

//ПРАВИЛЬНО:
[Введите "Н", чтобы начать новую игру]
[Введите "В", чтобы выйти]
н
Couldn't read the file: C:\MyReviews\PROG\HM-Лиза-\Hangman-main\src\src\main\resources\dictionary.txt
Ошибка чтения файла. Работа программы прекращена.
```

Где производить перехват исключения и распечатку сообщения?  
В данном случае, например, это можно сделать в main:
```java
public static void main(String[] args) {
  System.out.printf("Привет! %s", INSTRUCTIONS_SCRIPT);

  try(...) {
    processStartChoice();
  } catch (IllegalStateException e) {
    //распечатать сообщение о завершении работы программы
  }
}
```

- Тип исключения.

При ошибке чтения файла ты бросаешь `IllegalStateException`.  
Наверное, этот тип исключения не подходит к ситуации.  
Это исключение используют при других обстоятельствах:
```java
//IllegalStateException.java
Signals that a method has been invoked at an illegal or inappropriate time. 
In other words, the Java environment or Java application is not in an appropriate state for the requested operation.

Сигнализирует о том, что метод был вызван в недопустимое или неподходящее время. 
Другими словами, среда Java или Java-приложение находятся в неподходящем состоянии для выполнения запрошенной операции.
```

В данном случае, здесь более корректно кинуть `UncheckedIOException`:
```java
//UncheckedIOException.java
Wraps an IOException with an unchecked exception.

Оборачивает IOException в непроверяемое исключение.
```

Но это замечание такое, на уровне требований к более опытному программисту- просто мне захотелось немного подушнить.  
Я удивился, что ты выбрала именно `IllegalStateException`, а не более стандартный и универсальный(но все так же мало подходящий к ситуации) `IllegalArgumentException`, например.

**Кстати, лайфхак:** если не знаешь, в каких случаях нужно бросать конкретное исключение,
то переходи в код класса этого исключения(нажми на названии класса Ctrl + левая кнопка мышки).  
И через переводчик читай описание класса в верхнем комментарии.

**15. class HangmanRenderer**

👍 Название константы написано неправильным стилем, в остальном всё супер.

## ВЫВОД

Не работает чтение файла.  
Попробуй понять проблему и решить ее самостоятельно.  
Для этого найди другие реализации и посмотри, как там делается файловое чтение.  
Например [ТУТ](https://github.com/AlenaBormatova/hangman).  
Скачай этот проект, запусти, разберись, почему в нем файловое чтение происходит корректно.

Перепиши методы так, чтобы они соответствовали таким условиям:
+ Название метода должно объяснять, что он делает.
+ Каждый метод должен делать только одно дело- правило одной операции.

Посмотри на ютубе ролики Немчинского для новичков:
```
Правильные методы по Clean Code
Как называть переменные, методы и классы? Чистый код (Clean Code)
Принцип хорошего кода KISS 
```

Для развития навыков процедурной декомпозиции, посмотреть стрим Сергея [Крестики-нолики в процедурном стиле](https://www.youtube.com/watch?v=PPikj1qHxrA).

Подробное объяснение, как делать эту программу в процедурном и ООП стилях, есть у Сергея в расширенных материалах.

В целом, для новичка не так уж и плохо, видна творческая мысль 👍

n.156(301)  
#ревью #виселица 