https://github.com/fynkoR/hangman  
[распознаётся графически]

Игра в процедурном стиле, состоит из одного класса.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Команды не работают
```
Please, enter <n> - new game or <e> - exit
N
Please, enter <n> - new game or <e> - exit
E
Please, enter <n> - new game or <e> - exit
```

2. Можно ввести не одну букву, а набор букв и это будет считаться допустимым вводом
```java
Error = 1
<картинка>

Enter letter of guess: 
йцууцццц
This letter is not in the word
Error = 2
```

3. Во время игры нет списка введенных букв 

4. Два раза печатается одно и то же сообщение
```java
You lose !
This word: учёность
Used letter's: [у, ф, е, ц, й, к, ы, н]
Please, enter <n> - new game or <e> - exit
Please, enter <n> - new game or <e> - exit
```

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только буквы русского алфавита

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Н вбрсв глсн бкв
```java
public rndWord(...) {...}

//ПРАВИЛЬНО:
public getRandomWord(...) {...}
```

- Хуже венгерской нотации только венгерская нотация, которая обманывает.

Здесь переменная с именем "массив" не является массивом, она является ArrayList'ом.  
Но в любом случае венгерская нотация это плохо. Название переменной должно объяснять, что она хранит, а не как хранит
```java
ArrayList<String> array

//ПРАВИЛЬНО:
ArrayList<String> words
```

- Название обманывает.

Метод преобразует файл не в массив, а в ArrayList. И не преобразует файл, а читает его
```java
ArrayList<String> fileToArray(String fileName) {...}

//ПРАВИЛЬНО:
ArrayList<String> readFile(String fileName) {...}
```

Если рассматривать ситуацию в вакууме, то метод с названием `fileToArray(...)` действительно может существовать.  
Но для этого он должен выглядеть примерно так:
```java
String[] fileToArray(File file) {...}
```
То есть, в этом гипотетическом примере метод буквально берёт file и конвертирует("To") его в array.

- Название метода должно быть глаголом в повелительном наклонении. Сейчас название- существительное
```java
char validationLetter(Scanner scanner) {...}

//ПРАВИЛЬНО:
char validateLetter(Scanner scanner) {...}
```

- Название метода не соответствует тому, что он делает.

Метод не валидирует букву, он получает букву от юзера
```java
char validationLetter(Scanner scanner) {...}

//ПРАВИЛЬНО:
char inputLetter(Scanner scanner) {...}
```

- Загадочное название метода
```java
List<List<String>> startArrayList()
```
Название "начать аррай лист" слишком эфемерное- под этим выражением можно понимать всё, что угодно.  
Начинать-то аррайлист он может и начинает, но возвращает почему-то не аррайлист, а аррайлист аррайлистов.

Короче, название метода нужно переделать так, чтобы из названия было понятно, что этот метод делает. 

- Название должно объяснять, что делает метод.

Суть этого метода не в том, что он показывает аррайлист(хотя он распечатывает не аррайлист, а аррайлист аррайлистов).  
Его суть в том, что он показывает картинку виселицы 
```java
void showArrayList(List<List<String>> arr) {...}

//ПРАВИЛЬНО:
void showHangmanPicture(List<List<String>> arr) {...}
```
А `ArrayList` здесь это просто способ хранения и передачи картинки в метод, не более того. 

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Нарушение конвенции кода**

- Метод-точка входа `main()` должен стоять первым среди методов.

*"Oracle Java Code Conventions"* 

**3. Область видимости методов**

Все методы, кроме main(), *в этом классе* должны быть private.

Сторонние классы не должны иметь возможность дергать за публичные методы, которые являются подробностями внутреннего устройства чужого класса.  
Подробность внутреннего устройства- это методы, которые 
Иначе, через вызовы этих методов, сторонний класс может нарушить всю логику работы программы. 

**4. Нарушение DRY**, магические буквы, числа, слова. Вводи константы 
```java
while(!(answer.equals("n") || answer.equals("e"))) {...}
System.out.println("Please, enter <n> - new game or <e> - exit");
if(answer.equals("n")) {...}

//ПРАВИЛЬНО:
private static final String START = "n";
private static final String EXIT = "e";

while(!(answer.equals(START) || answer.equals(EXIT))) {...}
System.out.printf("Please, enter <%s> - new game or <%s> - exit  \n", START, EXIT);
if(answer.equals(START)) {...}
```

Другая магия: 6, "*", 'а', 'я' etc.

*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*

**5. Используй классы через их интерфейсы**
```java
ArrayList<String> array = fileToArray(fileName);
ArrayList<String> fileToArray(String fileName)

//ПРАВИЛЬНО:
List<String> array = fileToArray(fileName);
List<String> fileToArray(String fileName)
```
Общее правило: ArrayList нужно использовать через List, HashMap через Map, HashSet через Set и т.д.  
Это позволяет пользоваться преимуществами полиморфизма.

Да, бывают ситуации, когда, например, с LinkedList нужно работать именно как с LinkedList, а не с List. 
Но это уже нюансы.  
*"Java. Эффективное программирование", изд.3, гл.9.8*
```java
"Если вы выработаете привычку использовать в качестве типов интерфейсы, ваша программа будет гораздо более гибкой" - Блох.
```

**6. Если в блоке if есть return(break, continue, throw, exit и т.д.), то else не пишется**

В этом случае неважно, будет else или нет, так как программа будет работать одинаково, а код без else будет выглядеть читабельней
```java
if (answer.equals("n")) {
  System.out.println("New game !");
  return true;
} else {
  System.out.println("Okay, bye !");
  return false;
}

//ПРАВИЛЬНО:
if (answer.equals("n")) {
  System.out.println("New game !");
  return true;
} 
System.out.println("Okay, bye !");
return false;
```

**7. Избыточно**

- (a)
```java
public static String maskWord(String word) {
  String mask = "";
  for (int i = 0; i < word.length(); i++) {
    mask = mask.concat("*");
  }
  return mask;
}

//ПРАВИЛЬНО:
private static final String MASK_SYMBOL = "*";

public static String maskWord(String word) {
  return MASK_SYMBOL.repeat(word.length());
}
```

- (b)
```java
while (!check) {
  //...
  if ((letter >= 'а' && letter <= 'я') || letter == 'ё') {
    check = true;
  } else {
    System.out.println("Letter must be lowercase, from 'a' to 'я'.");
  }
}
return letter;

//ПРАВИЛЬНО:
while (true) {
  //...
  if ((letter >= FIRST_LETTER && letter <= LAST_LETTER) || letter == SPECIAL_LETTER) {
    return letter;
  } 
  System.out.printf("Letter must be lowercase, from '%c' to '%c'.  \n", FIRST_LETTER, LAST_LETTER);
}
```

**8. Создавай вспомогательные методы, делай программу более простой и понятной**
```java
while (!(answer.equals("n") || answer.equals("e"))) {
  if (answer.equals("n")) {...}
  //...    
}

//ПРАВИЛЬНО:
while (isCommand(answer)) {
  if (isStartCommand(answer)) {...}
  //... 
}

private static boolean isCommand(String answer) {
  return isStartCommand(answer) || isExitCommand(answer);
}

private static boolean isStartCommand(String answer) {
  return answer.equalsIgnoreCase(START);
}
```
Даже если метод состоит из одной строки, но при этом делает код программы читабельнее, то этот метод имеет право на жизнь.

**9. Никогда не возвращай null**
```java
public static ArrayList<String> fileToArray(String fileName) {
  ArrayList<String> array = null;
  try (BufferedReader br = new BufferedReader(new FileReader(fileName))) {
    array = new ArrayList<>();
    //...
  } catch (IOException ex) {
    System.out.println(ex.getMessage());
  }
  return array;  <-- Если сработает catch, то вернет null
}

//ПРАВИЛЬНО:
public static ArrayList<String> fileToArray(String fileName) {
  List<String> words = new ArrayList<>();
  try (BufferedReader br = new BufferedReader(new FileReader(fileName))) {
    array = new ArrayList<>();
    //...
  } catch (IOException ex) {
    System.out.println(ex.getMessage());
  }
  return words;  <-- Если сработает catch, то вернет пустой ArrayList
}
```
Возврат null повышает риск возникновения NullPointerException в программе.  
*Мартин, "Чистый код", гл.7.7-8*  
*Ютуб, Немчинский "Почему нельзя возвращать NULL?"*  

**10. Нарушение правила одной операции**

- Нарушение правила одной операции. 

Этот метод делает какие-то две разные вещи
```java
public static char validationLetter(Scanner scanner) {
  char letter = 0;
  boolean check = false;
  while (!check) {
      System.out.println("Enter letter of guess: ");
      letter = scanner.next().charAt(0);
      if ((letter >= 'а' && letter <= 'я') || letter == 'ё') {
        check = true;
      } else {
        System.out.println("Letter must be lowercase, from 'a' to 'я'.");
      }
    }
    return letter;
  }
```
Метод нужно разделить на несколько, каждый из которых будет делать что-то одно.  
*Мартин, "Чистый код", гл.3, "Правило одной операции", "Один уровень абстракции"*

**11. Печать картинок**

- В любом switch-case должен быть default.  
В данном случае default должен бросать исключение с сообщением, что такого номера картинки не существует.

- Нечитаемый, неподдерживаемый, непрозрачный, не интуитивно понятный, увеличивающий на единицу wtf-счётчик, багоопасный способ формирования картинок
```java
public static void draw(List<List<String>> array, int error) {
  switch (error) {
    case 0:
      showArrayList(array);
      break;
    case 1:
      array.get(2).set(4, "O");
      showArrayList(array);
      break;
      //...
  }
}
```
Пиши код так, чтобы его было легко читать, понять и изменять.

- Печать картинки виселицы через switch-case или if-elseif - индусский код.  

Картинки нужно хранить в статическом массиве и печатать по номеру.  
Например, так
```java
public class HangmanRenderer {
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

  public static void render(int pictureNumber) {
    String picture = PICTURES[pictureNumber];  
    System.out.println(picture);
  }
}
```
Желательно картинки и метод распечатки вынести в отдельный класс, чтобы в основном классе не смешивать логику и графику.  
Процедурный стиль программирования позволяет делать в проекте несколько классов, когда они выполняют роль контейнеров для функций.

**12. Вводи вспомогательные методы**

Мы должны понимать, что тут происходит
```java
public static void doGame(String word, Scanner in, List<List<String>> array) {
  //..  
  while ((error < 6) && !(word.equals(maskBuilder.toString()))) {
    //...
  }
  
  if (word.equals(maskBuilder.toString())) {
      System.out.println("You win !");
    } else {...}
}

//ПРАВИЛЬНО:
private static String word;
private static StringBuilder mask;

public static void doGame(...) {
  //..  
  while (!isGameOver()) {
    //...
  }
  
  if (isWin()) {
    System.out.println("You win !");
  } else if isLose() {...}
}

private static boolean isGameOver() {
  return isWin() || isLose();  
}

private static boolean isWin() {
  return word.equals(maskBuilder.toString());    
}

private static boolean isLose() {...}
```

**13. Большой божественный метод.**

Божественные методы разделяй на маленькие методы, каждый из которых должен делать одно дело на одном уровне абстракции.  

Например, смотрим на божественный метод и находим в нем отдельные смысловые блоки
```java
public static void doGame(String word, Scanner in, List<List<String>> array) {
  //куча кода

  if (usedChars.add(letter)) { // true = добавилась, false = уже была
    boolean change = false;
    for (int i = 0; i < word.length(); i++) {
      if (word.charAt(i) == letter) {
       maskBuilder.setCharAt(i, letter);
        change = true;
    }
  }
 
  if (!change) {
     error++;
     System.out.println("This letter is not in the word");
  }

  //ещё куча кода
}
```

Что делает этот кусок кода? 

Здесь переплетено несколько дел: открывает букву в слове и одновременно выставляет флаг, если такая буква есть в слове.

Выносим этот код во вспомогательный метод:
```java
private static String word;
private static StringBuilder mask;

public static void doGame(String word, Scanner in, List<List<String>> array) {
  //куча кода

  if(isWordLetter(letter)) {
    openLetter(letter);
  } else {
    //действия, если буквы нет в слове
  }

  //ещё куча кода
}

private static isWordLetter(char letter) {
  //возвращает true если letter есть в слове    
}

private static void openLetter(char letter) {
  for (int i = 0; i < word.length(); i++) {
    if (word.charAt(i) == letter) {
      mask.setCharAt(i, letter);
    }
  }
}
```
*"ЧК", гл.3, "Правило одной операции", "Один уровень абстракции"*

## ВЫВОД

Для процедурного стиля более-менее норм.

Посмотри на ютубе ролики Немчинского для новичков:
```
"Правильные методы по Clean Code"
"Как называть переменные, методы и классы? Чистый код (Clean Code)"
"Принцип хорошего кода KISS"
```

Подробное объяснение, как делать эту программу в процедурном и ООП стилях, есть у Сергея в расширенных материалах

n.186(357)  
#ревью #виселица 