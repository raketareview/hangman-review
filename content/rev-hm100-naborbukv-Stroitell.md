https://github.com/Stroitell/hangman  
[АтыдьсЛЬвтыдущ]

Игра в процедурном стиле, выполнена в одном классе.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

Орфографические ошибки: "Неккоректный ответ", "Допустимые сивмолы".

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву русского языка
+ 👍 Есть список введенных букв
+ 👍 Цветной текст в консоли

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Название должно объяснять суть явления. "Проверка символа" не объясняет, что это за проверка. 

Оно проверяет, что символ принадлежит финникийскому алфавиту? Что он написан в верхнем регистре? Что он является последней буквой алфавита? 
Никогда не догадаешься, пока не заглянешь внутрь метода
```
Integer checkCharacter(String word, String character)
```
На самом деле, метод проверяет вхождение буквы `character` в слово `word`- этот функционал нужно отразить в названии метода.

- Если метод что-то проверяет, то результатом этой проверки должно быть булево значение true/false
```
Integer checkCharacter(String word, String character)

//ПРАВИЛЬНО:
boolean checkCharacter(String word, String character)
```

- В названии переменных не пиши тип данных, к которым они относится. И вообще не употребляй венгерскую ноттацию.  

Название переменной должно отвечать на вопрос что хранит переменная, а не как хранит.
Тем более, что тут название обманывает- переменная 'character' не принадлежит к типу данных Character
```
Integer checkCharacter(String word, String character)

//ПРАВИЛЬНО:
Integer checkCharacter(String word, char letter) <- ДОЛЖНО ПРОВЕРЯТЬ ВХОЖДЕНИЕ БУКВЫ(СИМВОЛА) в СЛОВО(СТРОКУ) 
```

- Добавить символ в тень- это звучит очень поэтично. По факту, метод просто открывает букву(в маске) 
```
void addCharacterToShadow(String character, StringBuilder shadow, String word)

//БЕЗ ЛИШНЕЙ ПОЭТИКИ:
void openLetter(String character, StringBuilder shadow, String word)
```

- Названия методов должны быть глаголом в повелительном наклонении
```
void gallowsArts(int hp, StringBuilder shadow, String word)

//ПРАВИЛЬНО:
void printGallows(int hp, StringBuilder shadow, String word)
```
*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*  

**2. Приоритет- читаемость кода**, распечатка многострочных строк порой в данном контексте выглядит сомнительно
```
System.out.println("""
  Сыграть ещё?
  да/нет
""");

//ПОНЯТНЕЕ:
System.out.println("Сыграть ещё?");
System.out.println("да/нет");
```

**3. Константы**

Эти данные по ходу программы не изменяются, поэтому должны быть константами
```
private static String reset = "\u001B[0m";
private static String red = "\u001B[31m";

//ПРАВИЛЬНО:
private static final String RESET = "\u001B[0m";
private static String RED = "\u001B[31m";
```

**4. Нарушение DRY**, магические буквы, числа, слова. Вводи константы
```
System.out.println("да/нет");
if (input.equals("да")) {...}
if (input.equals("нет")) {...}
System.out.println("""
  Начать игру?
  да/нет
""");
do {...} while (!input.equals("нет"));

//ПРАВИЛЬНО:
private final static String START = "да";
private final static String QUIT = "нет";

System.out.printf("%s/%s \n", START, QUIT);
if (input.equals(START)) {...}
if (input.equals(QUIT)) {...}
System.out.println("Начать игру?");
System.out.printf("%s/%s \n", START, QUIT);

do {...} while (!input.equals(QUIT));
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*  

**5. Нарушение DRY**, повторяющиеся действия выноси во вспомогательные методы
```
System.out.println(yellow + "Некорректный ответ" + reset);
System.out.println("Допустимые сивмолы: " + green + "абвгдеёжзийклмнопрстуфхцчшщъыьэюя" + reset);

//ПРАВИЛЬНО:

printlnColor("Некорректный ответ", YELLOW);
System.out.print("Допустимые сивмолы: ");
printlnColor("Некорректный ответ", GREEN);

private void printColor(String message, String color) {
  System.out.print(color);
  System.out.print(message);
  System.out.print(RESET);
}

private void printlnColor(String message, String color) {
  printColor(message, color);
  System.out.println(); 
}
```

**6. Цветная консоль**

Для работы с цветом в консольных пет проектах создай соотвествующий енам
```
public enum Color {
  RESET("\u001B[0m"),
  RED("\u001B[31m"),
  GREEN("\u001B[32m"),
  //oth's colors
  ;

  private final String code;

  Color(String code) {
    this.code = code;
  }

  public String getCode() {
    return code;
  }
}
```
Тогда работа с цветом будет проходить безопаснее:
```
private void printColor(String message, Color color) {
  System.out.print(color.getCode());
  System.out.print(message);
  System.out.print(Color.RESET.getCode());
}
```

**7. Если в блоке if есть return**(break, continue, throw, exit и т.д.), то else не пишется.  
В этом случае неважно, будет else или нет, так как программа будет работать одинаково и код без `else` будет выглядеть читабельней
```
if (input.equals("нет")) {
  break;
} else {
  System.out.println(yellow + "Некорректный ответ" + reset);
}

//ПРАВИЛЬНО:
if (input.equals("нет")) {
  break;
} 
System.out.println(yellow + "Некорректный ответ" + reset);
```

**8. Создавай вспомогательные методы**, делай программу более простой и понятной
```
while (hp > 0 & shadow.toString().contains("_")) {...}

//ПРАВИЛЬНО:
if (!isGameOver()) {...}

private boolean isGameOver() {
  return isLose() || isWin();
}

private boolean isWin() {
  return !shadow.toString().contains("_");
}
```

**9. Метод-чемпион по количеству ошибок на единицу смысла**

Этот метод определяет, что буква входит в состав слова
```
private static Integer checkCharacter(String word, String character) {
  if (word.contains(character)) {
    return 1;
  } else {
    return -1;
  }
}
```

- При прочих равных, нужно использовать примитивный тип, а не класс-обертку. 
Здесь для применения обертки нет никаких оснований
```
Integer checkCharacter(...)

//ПРАВИЛЬНО:
int checkCharacter(...)
```

- Метод что-то проверяет, а значит должен вернуть boolean. 
Результатом любого сравнения является boolean, а значит можно тело метода сократить до одной строки. 
Буква должна быть символом. Название должно объяснять, что делает метод:
```
private static boolean contains(String word, char letter) {
  return word.contains(character);
}
```

**10. Проверка соответствия буквы алфавиту сейчас- индусский код**

Сейчас проверка соотвествия буквы русскому алфавиту происходит путем поиска буквы в строке. Это очень избыточный алгоритм
```
private static String alphabet = "абвгдеёжзийклмнопрстуфхцчшщъыьэюя";

private static boolean checkValidInput(String character) {
  return !alphabet.contains(character) || !(character.length() == 1);
}

//ПРАВИЛЬНО:
private static boolean isRussianLetter(char symbol) {
  symbol = Character.toLowerCase(symbol);
  return (symbol >= 'а' && symbol <= 'я') || symbol == 'ё';
}
```
Почитай про то, как символы хранятся в таблице ASCII/юникод.

**11. Реакция на ошибки**

В проекте не предусмотрено никакой реакции на ошибку чтения файла.
Если файл по указанному адресу не будет обнаружен, то программа аварийно приостановит свою работу через вываливание непонятных для юзера букв в консоль
```
Начать игру?
да/нет
да
Допустимые сивмолы: абвгдеёжзийклмнопрстуфхцчшщъыьэюя
Exception in thread "main" java.io.FileNotFoundException: _src\dictionary.txt (Системе не удается найти указанный путь)
	at java.base/java.io.FileInputStream.open0(Native Method)
	at java.base/java.io.FileInputStream.open(FileInputStream.java:219)
```
В данном случае такой способ реагирования на ошибку неправилен, здесь должна быть другая реакция на отсутствие файла со словами.  
Нужно сказать юзеру, что файл отсутствует и поэтому работа программы будет завершена. 
И корректно завершить работу программы без вываливания красных эксепшенов в консоль.

**12. Большой божественный метод**

Большой божественный метод состоящий из необозримого количества строк- 60
```
private static void startIfYes() throws FileNotFoundException
```
Делает много всего сразу, разобраться в нем трудно, код читается плохо. 
Метод разделить на несколько маленьких, каждый из которых будет делать свое.  
*Мартин "ЧК", гл.3, "Компактность!"*
*Фаулер "Рефакторинг", гл.6, "Извлечение метода"* 

**13. Разберись с проверяемыми исключениями**

У некоторых методов в сигнатуре прописаны проверяемые исключения, хотя эти исключения не могут возникнуть в этих методах. В сигнатурах не должно быть лишнего мусора
```
private static StringBuilder makeShadow(String word) throws FileNotFoundException {
  return new StringBuilder("_".repeat(word.length()));
}

//ПРАВИЛЬНО:
private static StringBuilder makeShadow(String word) {...}
```
Почитай про непроверяемые и проверяемые исключения.

**14. Распечатка виселицы**

- Метод распечатки виселицы должен печатать только изображение виселицы. Любую другую сопроводительную информацию он распечатывать не должен
```
private static void gallowsArts(int hp, StringBuilder shadow, String word) {
  if (hp == 6) {
    System.out.println("""
      _________
      |       |
      |       
      |      
      |_________
          """);
     System.out.println(green + shadow + reset);  <- ЭТО НЕ ДОЛЖНО ТУТ ПЕЧАТАТЬСЯ
  }
  //...
}
```

- Если в аргументы метода пришло некорректное значение `hp`(hp > 6 или hp < 0), то это бак алгоритма и нужно бросать исключение, чтобы сразу же выявить этот баг и устранить его.

- Распечатка картинки виселицы через switch-case или if-elseif - индусский код.
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

public static void printPicture(int numPicture) {  
  System.out.println(PICTURES[numPicture]);
}
```
Желательно картинки и метод распечатки вынести в отдельный класс, чтобы в основном классе не смешивать логику и графику.  
Процедурный стиль дозволяет делать проекты, состоящие из нескольких классов.

**15. Передача аргументов в методы**

В проекте все переменные класса это по сути константы- они не изменяются по ходу программы. 
Однако здесь лучше и некоторые изменяемые переменные методов сделать переменными класса. 
Например, постоянно по ходу игры все время приходится работать с переменными слово и маска. 

Эти переменные стоит сделать перменными класса, тогда их не нужно будет все время передавать в методы. Пусть эти методы берут слово и маску из окружения
```
void addCharacterToShadow(String character, StringBuilder shadow, String word) {
  //открывает букву в маске
  //слово и маска передаются в аргументы
}

//ЛУЧШЕ:
private static String word;
private static StringBuilder shadow;

void addCharacterToShadow(String character) {
  //открывает букву в маске
  //слово и маска берутся из окружения
}
```

## ВЫВОД

Для начала нужно разделить божественный метод на несколько мелких. Тогда более явственно проявятся остальные проблемы, *если* они будут.  
Сейчас код читается тяжело из-за того, что проект не разделен на достаточное количество методов.

n.100(188)  
#ревью #виселица 