https://github.com/dav1dbl4/Hangman  
[Давид]

Проект написан в процедурном стиле. Состоит из нескольких классов.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Нет защиты от дурака
```java
[1] Начать новую игру
[2] Выйти из приложения

Введите номер команды:
fuck
Exception in thread "main" java.util.InputMismatchException
```

2. Можно ввести буквы не только русского алфавита
```java
Неправильные буквы: []
Количество ошибок: 0/9

Введите букву:
魚

Неправильные буквы: [魚]
Количество ошибок: 1/9
```

## ХОРОШО

+ 👍 Можно ввести только одиночную букву 
+ 👍 Есть список введенных букв

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Названия полей нужно писать стилем camelCase
```java
List<String> ArrayList

//ПРАВИЛЬНО:
List<String> arrayList
```

- Название обманывает: эта переменная имеет тип List, не ArrayList
```java
List<String> ArrayList

//ПРАВИЛЬНО:
List<String> list
```

- Венгерская нотация.

В названии переменных не пиши тип данных, к которым они относятся.  
И вообще не употребляй венгерскую нотацию. 
Название переменной должно отвечать на вопрос что хранит переменная, а не как хранит
```java
List<String> ArrayList

//ПРАВИЛЬНО:
List<String> words
```

- Название методов должно быть глаголом в повелительном наклонении.  

По правилам английского языка, в предложении сначала стоит глагол
```java
String wordInit() {...}

//ПРАВИЛЬНО:
String initWord() {...}
```

Но слово "init" не подходит для процесса выбора чего-то из чего-то.  
А здесь происходит именно выбор, а не инициализация:
```java
public static String wordInit() {
  List<String> ArrayList = new ArrayList<>(List.of("Шприц", "Слово", ...));

  Random random = new Random();
  int randomIndex = random.nextInt(ArrayList.size());

  return ArrayList.get(randomIndex);
}

//ПРАВИЛЬНО:
public static String getRandomWord() {...}
```

- Это названия метода - существительное
```java
void gameResult() 
```

- Этот метод не инициализирует меню, он выполняет меню
```java
public class Menu {
  public static void initStartMenu() {...}
  //...
}
```

- Название переменной должно быть существительным. Этот сканер не нажимает для продолжения, он - просто сканер
```java
public static void pressToContinue() {
  System.out.println("Нажмите Enter для продолжения...");
  Scanner pressToContinue = new Scanner(System.in);
  pressToContinue.nextLine();
}

//ПРАВИЛЬНО:
public static void pressToContinue() {
  System.out.println("Нажмите Enter для продолжения...");
  Scanner scanner = new Scanner(System.in);
  scanner.nextLine();
}
```

- Не дублируй имя класса в названии метода класса
```java
public class Game {
  //...  
  public static void gameStart() {...}
}

//ПРАВИЛЬНО:
public class Game {
  //...  
  public static void start() {...}
}
```

- Что здесь для понимания сути переменной дает уточнение "answerWord" по сравнению с просто "word"? Ничего
```java
String answerWord;
String answerWordMask;

//ПРАВИЛЬНО:
String word;
String mask;
```

- Геттер должен что-то возвращать. Геттер, который ничего не возвращает, это нонсенс
```java
void getAnswerWord(int index) {...}
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Форматирование**

- Мысленные преобразования.

Не вставляй перевод строки в `println` при распечатке.  
Иначе при чтении кода приходится мысленно "разворачивать" одну строку в несколько.  
Если нужно распечатать несколько строк, то печатай их или многострочными строками(""") или отдельными инструкциями печати.  
Тогда при чтении кода сразу будет видно, как текст будет выглядеть на печати:
```java
System.out.println("Введено неверное значение! \nПопробуйте еще раз.\n");

//ПРАВИЛЬНО:
System.out.println("Введено неверное значение!");
System.out.println("Попробуйте еще раз.");
System.out.println();
```

Тут тоже
```java
System.out.println("Вы проиграли. Загаданное слово: " + answerWord + "\n");

//ПРАВИЛЬНО:
System.out.println("Вы проиграли. Загаданное слово: " + answerWord);
System.out.println();
```

И даже тут
```java
System.out.println("[2] Выйти из приложения\n");

//ПРАВИЛЬНО:
System.out.println("[2] Выйти из приложения");
System.out.println();
```

- Форматирование строк.

Если нужно печатать или создавать строку с более чем одним подстановочным значением или значение вставляется внутрь сообщения, 
используй форматирование - тогда сразу будет виден весь шаблон сообщения
```java
 System.out.println("Количество ошибок: " + errorCount + "/" + errorCountMax + "\n");

//ПРАВИЛЬНО:
 System.out.printf("Количество ошибок: %d/%d \n", errorCount, errorCountMax);
System.out.println();
```

**3. Используй классы через их интерфейсы**
```java
ArrayList<Character> wrongLetters;

//ПРАВИЛЬНО:
List<Character> wrongLetters;
```
Общее правило: ArrayList нужно использовать через List, HashMap через Map, HashSet через Set и т.д.  
Это позволяет пользоваться преимуществами полиморфизма.

Да, бывают ситуации, когда, например, с LinkedList нужно работать именно как с LinkedList, а не с List. 
Но это уже нюансы.  
*"Java. Эффективное программирование", изд.3, гл.9.8*
```java
"Если вы выработаете привычку использовать в качестве типов интерфейсы, ваша программа будет гораздо более гибкой" - Блох
```

**4. class WordInitialization**

- Соблюдай требования утилитных классов. 

Это утилитный класс, он содержит только статические методы.  
Утилитные классы, как и классы констант, должны быть `final` и иметь приватный конструктор.  
Не должно быть возможности унаследоваться от утилиты или сделать ее экземпляр.

*Блох, "Java. Эффективное программирование", изд.3, гл.2.4*

- Слова не читаются из файла, а захардкоджены в виде списка
```java
public class WordInitialization {
  public static String wordInit() {
    List<String> ArrayList = new ArrayList<>(List.of("Шприц", "Слово", ...));
    //...
  }
}
```

Это и так простой проект, не делай его совсем примитивным.  
Слова здесь нужно загружать из файла - для практики работы с файловым чтением.

- Тут избыточно
```java
List<String> ArrayList = new ArrayList<>(List.of("Шприц", "Слово", ...));

Random random = new Random();
int randomIndex = random.nextInt(ArrayList.size());

return ArrayList.get(randomIndex);

//ПРАВИЛЬНО:
List<String> words = List.of("Шприц", "Слово", ...);
```

- В таких случаях захардкодженные списки со значениями нужно выносить в константы
```java
public class WordInitialization {
  public static String wordInit() {
    List<String> ArrayList = new ArrayList<>(List.of("Шприц", "Слово", ...));

    Random random = new Random();
    int randomIndex = random.nextInt(ArrayList.size());

    return ArrayList.get(randomIndex);
  }
}

//ПРАВИЛЬНО:
public class WordInitialization {
  private static final List<String> WORDS = List.of("Шприц", "Слово", ...);

  public static String getRandomWord() {
    Random random = new Random();
    int index = random.nextInt(WORDS.size());

    return WORDS.get(index);
  }
}
```

Потому что храня список со словами внутри метода, ты этот заполненный данными объект (список со словами) пересоздаешь каждый раз при вызове метода.  

**5. class Menu**

- Нарушение DRY.

Магические буквы, числа, слова. Вводи константы 
```java
System.out.println("[1] Начать новую игру");
System.out.println("[2] Выйти из приложения\n");

case 1:  //...
case 2:  //...

//ПРАВИЛЬНО:
private static final int START = 1;
private static final int QUIT = 2;

System.out.printf("[%d] Начать новую игру \n", START);
System.out.printf("[%d] Выйти из приложения \n", QUIT);

case START:  //...
case QUIT:  //...
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru, "Замена магического числа символьной константой"*

- Рекурсия
```java
public static void initStartMenu() {
  //...
  initStartMenu();
}
```

Рекурсия должна использоваться только там, где она оптимальна алгоритмически.
Например, при обходе бинарного дерева.

В других ситуациях, где рекурсия может быть заменена циклом, она должна быть им заменена.  
Рекурсия тяжело читается и при определенных условиях может привести к переполнению стека.  
Убери рекурсию, замени ее использование на цикл `while()`. 

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

- Метод не должен завершать работу программы через exit()
```java
public static void initStartMenu() {
  //...
  case 2: System.exit(0);
  //...
}
```
Каждый метод и класс имеют право завершать только свою работу. Например, через return.  
Потому что методы и классы не должны знать логику работы более высоких слоев программы, у которых могут быть свои планы на тему того, когда и почему нужно завершать работу программы.

Кроме того, при выходе через `exit()` могут не закрыться некоторые ресурсы программы.

**6. class HangmanDrawing**

+ 👍 Хорошо, что картинки и метод их печати вынесены в отдельный класс. Это разгружает классы с логикой от графики.

- В любом switch-case должен быть default.  
В данном случае default должен бросать исключение с сообщением, что такого номера картинки не существует.

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

**7. class Game**

- Антипаттерн "Стрела".

Не должно быть больше 2-3 уровней вложенности.  
Если больше, это антипаттерн "Стрела", такой код очень труден для понимания
```java
while (gameOn) {
  if (checkInputLetter(answerLetter)) {
    if (!correctLetters.contains(answerLetter)) {
      for (int i = 0; i < answerWord.length(); i++) {
        if (answerWord.charAt(i) == answerLetter) {
          if (!correctLetters.contains(answerLetter))
            //наконечник стрелы
          }
        }
      }
    }
  }
}
```
"Стрела" значит, что метод делает несколько дел сразу и его нужно разделить на несколько вспомогательных.
```java
"Если вам нужно более трех уровней вложенности, вы все равно запутались, так что исправьте программу" - Линус Торвальдс
```

*"ЧК", гл.3, "Правило одной операции", "Один уровень абстракции"*

- Большой божественный метод. 

Очень большой метод `gameStart()` размером 60 строк.  
Этот метод трудночитаем.  
Божественные методы разделяй на маленькие методы, каждый из которых должен делать одно дело на одном уровне абстракции.  

Например, смотрим на божественный метод и находим в нём отдельные смысловые блоки
```java
public static void gameStart() {
  //куча кода
  boolean isCorrectLetter = false;

  if (!correctLetters.contains(answerLetter)) {
    for (int i = 0; i < answerWord.length(); i++) {
      if (answerWord.charAt(i) == answerLetter) {
        getAnswerWord(i);

        if (!correctLetters.contains(answerLetter))
          correctLetters.add(answerLetter);
          isCorrectLetter = true;
        }
      }
    }
  //ещё куча кода
}  
```

Что делает этот кусок кода?  
Он открывает букву в слове, если она там есть.  
Выносим этот код во вспомогательные методы:
```java
public static void gameStart() {
  //куча кода
  if(isImputedLetter(letter)) {
    //букву уже вводили - выполнить соответствующие действия
    continue
  }

  if(!isWordLetter(letter)) {
    //этой буквы в слове нет - выполнить соответствующие действия
    continue;
  }

  openLetter(letter);  //эта буква есть в слове - открываем её в маске

  //ещё куча кода
}  

private static boolean isWordLetter(char letter) {
  // возвращает true если буква есть в слове   
}

private static void openLetter(char letter) {
  // открывает букву в маске
  // если такой буквы в исходном СЛОВЕ нет - бросает исключение   
}
```
*"ЧК", гл.3, "Компактность", "Правило одной операции", "Один уровень абстракции"*

- Этот метод не возвращает слово, он открывает букву в слове по индексу
```java
private static void getAnswerWord(int index) {
  char letter = answerWord.charAt(index);
  char[] maskArray = answerWordMask.toCharArray();
  maskArray[index] = letter;
  answerWordMask = new String(maskArray);
}   

//ПРАВИЛЬНО:
private static void openLetterBy(int index) {...}
}  
```

- Нарушение правила одной операции 
```java

```

- Нарушение правила одной операции. 

Этот метод делает разные вещи.  
Судя по названию, он должен проверить букву (что бы это ни значило).  
На самом деле он проверяет букву, пишет на экран результаты проверки, принимает от юзера команду для продолжения работы
```java
private static boolean checkInputLetter(char answerLetter) {
  if (Character.isDigit(answerLetter) || !Character.isLetter(answerLetter)) {
    System.out.println("Ошибка ввода. Попробуйте еще раз.");
    Menu.pressToContinue();
    return false;
  }

  if (answerLetter >= 'a' && answerLetter <= 'z' || answerLetter >= 'A' && answerLetter <= 'Z') {
    System.out.println("Ошибка ввода. Попробуйте еще раз.");
    Menu.pressToContinue();
    return false;
  }

  return true;
 }
```

Метод нужно разделить на несколько, каждый из которых будет делать что-то одно.  
*Мартин, "Чистый код", гл.3, "Правило одной операции", "Один уровень абстракции"*

- Рекурсия, спагетти код
```java
+-> Menu.initStartMenu() 
|         ↓
|   Game.gameStart() 
|         ↓
+-- Game.gameResult() 


public class Game {
  //...
  private static void gameResult() {
    //...
    Menu.initStartMenu();
  }
}
```

- Избыточно
```java
if (answerLetter >= 'a' && answerLetter <= 'z' || answerLetter >= 'A' && answerLetter <= 'Z') {...}

//ПРАВИЛЬНО:
answer = Character.toLowerCase(answerLetter);
if (answerLetter >= 'a' && answerLetter <= 'z') {...}
```

## ВЫВОД

Код местами запутан и тяжело читается.  
Не видно понимания простых правил наименования переменных и методов - начни с изучения конвенции наименований, есть русские переводы:  
"Oracle Java Code Conventions", part."Naming conventions"

Для всех стилей программирования на уровне первого проекта нужно научиться делать методы, которые будут соответствовать таким требованиям:  
🔹 Маленький размер  
🔹 Выполняют одну операцию на одном уровне абстракции  
🔹 Не содержат больше трех уровней вложенности  
🔹 Не имеют побочных эффектов

Сейчас некоторые методы в проекте не соответствуют этим требованиям.

Посмотри на ютубе ролики для начинающих по принципам создания методов
```
Ролики Немчинского для новичков на ютубе:
"Правильные методы по Clean Code"
"Как называть переменные, методы и классы? Чистый код (Clean Code)"
"Принцип хорошего кода KISS"
"Принцип хорошего кода DRY"
```

Для развития навыков процедурной декомпозиции, посмотри стрим Сергея  
[Крестики-нолики в процедурном стиле](https://www.youtube.com/watch?v=PPikj1qHxrA)

Подробное объяснение, как делать эту программу в процедурном и ООП стилях, есть у Сергея в расширенных материалах.

n.199(384)  
#ревью #виселица 