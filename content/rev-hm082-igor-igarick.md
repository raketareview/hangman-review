https://github.com/igarick/Hangman.git  
[Igor]

Простая реализация в процедурном стиле, выполнена в одном классе.  
Есть над чем поработать.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

Не обнаружено.

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву русского алфавита
+ 👍 Есть список введенных букв

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Название метода должно объяснять, что делает метод. Этот метод не проверяет начало или конец, этот метод возвращает валидную команду, которую вводит юзер
```
String checkStartOrEnd();

//ПРАВИЛЬНО:
String inputCommand();
```

- Этот метод не проверяет букву, он получает букву от юзера
```
String checkIsLetter()

//ПРАВИЛЬНО:
String inputLetter()
```

- Название переменной должно объяснять, что хранит переменная. В эту строку юзер может ввести не только начало или конец, а все, что угодно, например слово "инвентаризация"
```
String startOrEnd = scanner.next().toUpperCase();

//ПРАВИЛЬНО:
String command = scanner.next().toUpperCase();
```

- Слова-паразиты. Любая переменная, которую ты добавляешь в программу и инициализируешь, будет "new", поэтому не нужно давать названия в стиле `newПеременная`
```
String newWord = getNewWord();

//ПРАВИЛЬНО:
String word = getRandomWord();
```

- Двойной удар
```
String newWordNew;
```

- Ne pishy translitom! Esli ne znaesh perevod, ispolzuy gugl-perevodchik
```
printImageVisel(boolean checkLetterInWord)

////ПРАВИЛЬНО:
printImageGallows(boolean checkLetterInWord)
```

- Название метода вводит в заблуждение. Обещает напечатать, что буквы нетути. Но может это напечатать, а может и не напечатать- все это зависит от управляющего флага
```
public static void printNoLetter(boolean letterInWord) {
  if (!letterInWord) System.out.println("Такой буквы нет!");
}
```

- Да, но почему "x"? В таких случаях принято использовать первую букву названия класса
```
for (String x : openWord) {
  System.out.print(x);
}

//ПРАВИЛЬНО:
for (String s : openWord) {
  System.out.print(s);
}
```
Исключение касается разве что Integer'a:
```
for(int d : values) {
    System.out.print(d);
}
```
*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*  

**2. Нарушение инкапсуляции**, публичным должен быть только методод main.

**3. Избыточно**, для распечатки пустой строки пиши просто println без аргументов
```
System.out.println("");  <-- печатает пустую строку

//ПРАВИЛЬНО:
System.out.println();  <-- печатает пустую строку
```

**4. Если в классе только статические методы и поля**, то делай класс final, а его конструктор- static.  
Вот так
```
public final class Main {
  //...
  private Main() {
  }
  //...
}
```
Тем самым нельзя будет создать экземпляр этого класса или унаследоваться от него. 

**5. Нарушение DRY**, магические буквы, числа, слова. Вводи константы 
```
System.out.println("Введите одну букву от А до Я");
System.out.println("Вы уже вводили эту букву. Введите одну букву от А до Я");
if (startOrEnd.equals("В")){...}
if (!((startOrEnd.length() == 1 && startOrEnd.matches("[НВ]")))) {
  System.out.println("Введите букву Н или В");
}
//и еще по коду миллион строк с буквами Н, В, А, Я 

//ПРАВИЛЬНО:
private final static String START = "Н";
private final static String QUIT = "В";
private final static String FIRST_LETTER = "А";
private final static String LAST_LETTER = "Я";

private final static String COMMAND_REGEX = "[%s%s]".formatted(START, QUIT);

//...
System.out.printf("Введите одну букву от %s до %s %n", FIRST_LETTER, LAST_LETTER);
System.out.println("Вы уже вводили эту букву. Введите одну букву от %s до %s %n", FIRST_LETTER, LAST_LETTER);
if (startOrEnd.equals(QUIT)){...}
if (!((startOrEnd.length() == 1 && startOrEnd.matches(COMMAND_REGEX)))) {
  System.out.printf("Введите букву %s или %s %n", START, QUIT);
}
```
Другие магические штуки в проекте: "*", "words", 6.  
*Фаулер, "Рефакторинг", гл.8 п."Замена магического числа символической константой"*   
*refactoring.guru "Замена магического числа символьной константой"*  

**6. Цикл while** всегда лучше читается, когда он написан в варианте `while() {...}`, а не `do {...} while()` - тогда сразу видно условие входа в цикл и выхода из него. 
Если есть возможность, `do-while` нужно переделывать на обычный `while`.  
Вечный цикл пиши только и исключительно в варианте `while(){}`
```
//ТАК ПЛОХО:
do {
  //...
  //...
  //...
  //...
} while (true);

//ТАК ХОРОШО:
while (true) {
  //...
  //...
  //...
  //...
}
```

**7. Нарушение конвенции кода.** В любой ситуации выделяй тело блока скобочками, даже если тело состоит из одной строки. Исключение- метод equals()
```
if (startOrEnd.equals("В")) return;
if (!letterInWord) System.out.println("Такой буквы нет!");

//ПРАВИЛЬНО:
if (startOrEnd.equals("В")) {
  return;
}    
if (!letterInWord) {
  System.out.println("Такой буквы нет!");
}  
```

**8. Используй непроверяемые исключения**. 

Не прописывай гирлянды проверяемых исключений в сигнатуре метода. Вместо этого перехватывай проверяемые исключения и кидай наверх непроверяемые
```
public static String getNewWord() throws FileNotFoundException { <-- проверяемое исключение в сигнатуре
  File file = new File("words");
  Scanner scanner = new Scanner(file);  <-- тут может вылетить проверяемое исключение
  //...
}  

//ПРАВИЛЬНО:
public static String getNewWord() { <-- в сигнатуре нет проброса исключений
  File file = new File("words");
  Scanner scanner = null;
  try {
    scanner = new Scanner(file);
  } catch (FileNotFoundException e) {  <-- перехват провер.искл.
    throw new RuntimeException(e);  <-- бросание непровер.искл.
  }
  //...
}  
```
Изучи отличия проверяемых и непроверяемых исключений.  
*"ЧК", гл.7, п."...проверяемые исключения"*  

**9. Комментарии**  
Коментарии в основном не несут полезной нагрузки, а констатируют очевидное
```
//такой буквы нет
public static void printNoLetter(boolean letterInWord) {...}
```
Когда в проекте много каментов, это плохо- пользы от них практически нет, они только забивают пространство и мешают читать код.
В идеале, комментариев вообще не должно быть, код должен объяснять сам себя через правильный нейминг и лаконичный код.  
*Мартин, "ЧК", гл.4*  

**10. Создавай вспомогательные методы**, делай программу более простой и понятной
```
String startOrEnd = //...
if (!((startOrEnd.length() == 1 && startOrEnd.matches("[НВ]")))) {
  System.out.println("Введите букву Н или В");
}

//ПРАВИЛЬНО:
String command = //...
if(!isValidCommand(command)) {
  System.out.printf("Введите букву %s или %s %n", START, QUIT);
}

//...
private static boolean isValidCommand(String command) {
  return command.length() == 1 && command.matches(COMMAND_REGEX);
}
```

**11. Метод выполняет два действия одновременно:** добавляет букву в перечень введенных и распечатывает это
```
public static void addLetter(String newLetter) {
  int length = enteredLetters.length();
  if (length == 0) {
    enteredLetters.append(newLetter);
    System.out.println("Вы ввели буквы: " + enteredLetters);
    System.out.println("");
  } else {
    enteredLetters.append(", ");
    enteredLetters.append(newLetter);
    System.out.println("Вы ввели буквы: " + enteredLetters);
    System.out.println("");
  }
}
```
Методы должны делать только одно дело, то есть иметь только одну ответственность(SRP), примерно так
```
private static void addLetter(String letter) {
  int length = enteredLetters.length();
  if (length > 0) {
    enteredLetters.append(", ");
  } 
  enteredLetters.append(letter);
}

private static void printEnteredLetters() {
  System.out.printf("Вы ввели буквы: %s %n%n", enteredLetters);
}
```
Те же проблемы: `void maskWord(String newWord)`.

**12. Совершенно ацкий метод** распечатки виселицы

- Нарушение DRY, сплошное дублирование кода
```
public static void printImageVisel(boolean checkLetterInWord) {
  if (!checkLetterInWord && sumMistakes == 1) {
    char[][] missOne = {{'-', '-', '-', '-', '-', ' '}, {'|', '/', ' ', ' ', '|', ' '}, {'|', ' ', ' ', ' ', 'O', ' '}, {'|', ' ', ' ', ' ', ' ', ' '}, {'|', ' ', ' ', ' ', ' ', ' '}, {'|', ' ', ' ', ' ', ' ', ' '}, {'|', ' ', ' ', ' ', ' ', ' '}, {'|', ' ', ' ', ' ', ' ', ' '}};
    for (int i = 0; i < 8; i++) {
      for (int j = 0; j < 6; j++) {
        System.out.print(missOne[i][j]);
      }
      System.out.println("");
    }
      System.out.println("У вас осталось попыток: " + (6 - sumMistakes));
  } else if (!checkLetterInWord && sumMistakes == 2) {
   //ДУБЛИРОВАНИЕ;
  } else if (!checkLetterInWord && sumMistakes == 3) {
   //ДУБЛИРОВАНИЕ;
  }
  //И ЕЩЕ МИЛЛИОН СТРОК ДУБЛИРОВАНИЯ
```

- Сигнатура метода запутанная, непонятно как этим методом пользоваться
```
void printImageVisel(boolean checkLetterInWord) <-- чиво?

//ПРАВИЛЬНО:
void printImageVisel(int numPicture) <-- распечатает картинку под номером "numPicture"
```

- Инициализация картинок в самом методе- избыточно. Каждый раз при вызове метода будет заново создаваться набор картинок. Картинки нужно вынести в константу. 

- Хранение картинок в массиве `char[][]` это сложно, избыточно и нечитаемо. 
Храни картинки либо в двумерном массиве строк `String[][]`, либо в массиве многострочных строк (java 13 or higher)
```
String s = """
    это
    многострочный
    стринг
  """;  
```

- Распечатка картинки виселицы через switch-case или if-elseif - индусский код.  
Картинки нужно хранить в статическом массиве и печатать по номеру, например, так
```
private static final String[][] PICTURES = {
  {
    "-----   ",
    "|       ",
    "|       ",
    "|       ",
    "|       ",
    "------- ",
   },
   {
     "-----   ",
     "|   |   ",
     "|   O   ",
     "|       ",
     "|       ",
     "------- ",
   },
   // more pics
  };

public void printPicture(int numPicture) {  
  String[] picture = PICTURES[numPicture];

  for(String line : picture) {
    //напечатать line
  }
}
```
Желательно картинки и метод распечатки вынести в отдельный класс, чтобы в основном классе не смешивать логику и графику.  
Процедурный стиль позволяет делать проекты из нескольких классов.

**13. Чтение слов из файла** тоже можно вынести в отдельный класс. 

**14. Пиши код так, чтобы он читался как открытая книга**, для этого вводи вспомогательные методы.

Например, смотрим на большой кусок кода, выявляем смысловые блоки
```
public static void gameLoop(String newWord) {
  do {
   //...
   if (checkState) {
     resetLettersAndMistakes();
     System.out.println("Вы выйграли");
     System.out.println("");
     return;
   }
   if (sumMistakes == 6) {
     resetLettersAndMistakes();
     System.out.println("Увы, мой друг, Вы програли");
     System.out.println("Загаданное слово: " + newWordNew);
     System.out.println("");
     return;
   }
  } while (true);
}  
```

И выносим их во вспомогательные методы
```
public static void gameLoop(String newWord) {
  while(!isGameOver()) {
    //...
    if (isLose()) {
      printLoseMessage();  
    } else if (isWin()) {
      printWinMessage();  
    }
  }
  resetLettersAndMistakes();
}  

//...
private static boolean isGameOver() {
  return isLose() || isWin();  
}
```

**15. Странная логика** построения и использования некоторых методов.

Например, есть задача: в случае ввода неправильной буквы, напечатать сообщение, что буква неправильная.  
Здесь для этого создается метод, который печатает это сообщение в зависимости от управляющего флага.  
Используется это в коде так
```
public static void gameLoop(String newWord) {
  //...  
  boolean check = checkLetterInWord(newLetter, newWord);
  printNoLetter(check);
  //...
}

public static void printNoLetter(boolean letterInWord) {
  if (!letterInWord) System.out.println("Такой буквы нет!");
}
```
То есть в любом случае сработает метод "напечатать", который внутри себя еще будет решать, печатать ему или нет. Это сложно и непонятно.

Правильно и просто будет вызывать метод печати только тогда, когда действительно надо печатать:
```
public static void gameLoop(String newWord) {
  //...  
  boolean isWordLetter = checkLetterInWord(newLetter, newWord);
  if(!isWordLetter) {
    printNoLetter();
  }
  //...
}

public static void printNoLetter() {
  System.out.println("Такой буквы нет!");
}
```

То же самое касается `countNumberMistakes(boolean isMistake)`, `printImageVisel(boolean checkLetterInWord)` и др.

**16. Почему буквы тут это строки?**

В проекте везде для хранения одиночной буквы используется String
```
String newLetter = checkIsLetter();
```
Обычно под буквой понимается одиночный символ, а для его хранения принято использовать тип `char`.
В чаре можно хранить даже иероглифы
```
public static void main(String[] args) {
  char c = '强';
  System.out.println(c);
}
```
Поэтому одиночные буквы храни в переменных типа `char`.

## ВЫВОД

Для новичка не так плохо, как могло бы быть. Просто нужно тренироваться.  
Посмотри ролики Немчинского про DRY, KISS, наименование переменных и другие его видео про основы.

n.82(153)  
#ревью #виселица