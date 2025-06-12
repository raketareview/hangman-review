https://github.com/MrRobert836/Java-Backend-projects/tree/master/Hangman  
[Danila Shashkov]

Игра в процедурном стиле, выполнена в одном классе. Функционал простейший. Есть баги.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Команды не работают
```
Введите 'Да' если хотите начать новую игру. 
Введите 'Нет' если хотите выйти из игры
да
Некорректный ввод.
```

2. Игра не запускается
```
Необходимо ввести 'Да' или 'Нет'.
Да
Exception in thread "main" java.nio.file.NoSuchFileException: Nouns.txt
	at java.base/sun.nio.fs.WindowsException.translateToIOException(WindowsException.java:85)
```

3. Это буква русского алфавита
```
Введите символ: ё
Введён некорректный символ. Символ должен быть буквой русского алфавита
```

4. Одна и та же буква в разных регистрах считается двумя разными буквами и увеличивает счетчик ошибок
```
Введите символ: й
Ошибки: 1
Слово: _._._._._._.
<картинка>

Введите символ: Й
Ошибки: 2
```

5. Вешается одноногий
```
Ошибки: 5
Слово: _._._._.е._.
          ----------
          |/     |
          |     ( )
          |     _|_
          |    / | \\
          |      |
          |     / 
          |    /  
          |
        __|________
        |         |
Введите символ: ш
ПОРАЖЕНИЕ!!!
```

6. Нет списка введенных букв 

7. Я так и не увиидел целиком отгаданное мною слово "привет"
```
Введите символ: е
Ошибки: 0
Слово: п.р.и.в.е._.
          ----------
          |/     |
          |
          |
          |
          |
          |
          |
          |
        __|________
        |         |
Введите символ: т
ПОБЕДА!!!
```

## ХОРОШО

👍 Можно ввести только одиночную букву русского языка

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Все части названия должны иметь смысл. 

Здесь префикс `game` в названии не имеет никакого смысла потому что не добавляет ничего к пониманию того, что хранит эта переменная. 
Весь проект это игра, а поэтому к любой переменной можно приписать префикс `игра`, а можно не приписать
```
private static String gameWord;
HashSet<String> gameMistakes = new HashSet<>();

//ЛУЧШЕ:
private static String word;
HashSet<String> mistakes = new HashSet<>();
```

- Letter не должен быть String'ом, потому что буква это один символ, а для одного символа принято использовать char.
Это либо не должно быть String, либо не должно иметь в названии `Letter`
```
String gameWordLetter
```

- Та же ситуация
```
String enterGameLetter()

//ПРАВИЛЬНО:
char enterGameLetter()
```

- С большой буквы называются только классы
```
int IndexOfLetter = Arrays.binarySearch(alphabet, input.toLowerCase());

//ПРАВИЛЬНО:
int indexOfLetter = Arrays.binarySearch(alphabet, input.toLowerCase());
```

- Название должно лаконично объяснять, что делает метод
```
//проверяет, что строка больше 1 символа
//длинное непонятное плохое название
public static boolean checkLetterTooLong(String input) {  
  return input.length() > 1;
}

//ЛАКОНИЧНО:
//проверяет, что строка состоит из одного символа
//короткое понятное хорошее название
public static boolean isOneSymbol(String input) {  
  return input.length() == 1;
}
```

- В эту переменную может попасть не только 'yes' или 'no', а все, что угодно
```
String yesOrNo = scanner.nextLine();

//ПРАВИЛЬНО:
String command = scanner.nextLine();
```

- Названия методов должны быть глаголом в повелительном наклонении. Название метода должно быть командой выполнить какие-то действия.  
В данном случае метод инициализирует слово для игры
```
void newGameWord()

//ПРАВИЛЬНО:
void initGameWord()
```

- Название обманывает. Метод печатает не только корректные буквы, метод печатает маску слова
```
public static void printCorrectLetters(int[] indexOfGameWordLetter, String gameWord) 

//ПРАВИЛЬНО:
public static void printMask(int[] indexOfGameWordLetter, String gameWord) 
```

- UPPER_SNAKE только для констант, а это не константа. Константы это поля класса, а не метода
```
public static void Gallows(int errors) {
    final String NO_ERRORS = //....
}    
```

- Методы нельзя называть с большой буквы, название метода должно быть глаголом
```
public static void Gallows(int errors)
```
*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Нарушение инкапсуляции**. Публичным должен быть только `main()`. Остальные методы и поля здесь должны быть приватными.

**3. Создавай вспомогательные методы**, делай программу более простой и понятной
```
while (foundLetters < gameWord.length()) {
  //миллион строк кода
}

//ПРАВИЛЬНО:
while (!isGameOver()) {
  //миллион строк кода
}
```

**4. Нарушение DRY**, магические буквы, числа, слова. Вводи константы. А если они уже есть- пользуйся 
```
private static final String BEGIN_THE_GAME = "Да";
private static final String END_THE_GAME = "Нет";

System.out.println("Введите 'Да' если хотите начать новую игру. " +
"\nВведите 'Нет' если хотите выйти из игры");

//ПРАВИЛЬНО:
private static final String BEGIN_THE_GAME = "Да";
private static final String END_THE_GAME = "Нет";

System.out.printf("Введите '%s' если хотите начать новую игру. \nВведите '%s' если хотите выйти из игры \n", 
BEGIN_THE_GAME, END_THE_GAME);
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой" *  
*refactoring.guru "Замена магического числа символьной константой"*  

**5. Нарушение конвенции кода.** В любой ситуации выделяй тело блока скобочками, даже если тело состоит из одной строки. 

Это не Си, прости господи. 
Исключение- метод equals(), там можно после if не выделять блоки скобочками
```
if (indexOfGameWordLetter[i] == DEFAULT_ARRAY_VALUE)
  System.out.print("_");
else
  System.out.print(gameWord.charAt(i));

//ПРАВИЛЬНО:
if (indexOfGameWordLetter[i] == DEFAULT_ARRAY_VALUE) {
  System.out.print("_");
} else {
  System.out.print(gameWord.charAt(i));
}
```

**6. Используй классы через их интерфейсы**
```
HashSet<String> gameMistakes = new HashSet<>();

//ПРАВИЛЬНО:
Set<String> mistakes = new HashSet<>();
```

**7. Приоритет- читаемость кода**, а не экономия строчек

Теперь будет видно, что сообщение распечатается в две строки
```
if (errors == FATAL)
  System.out.println("ПОРАЖЕНИЕ!!!\n" + "Загаданное слово: " + gameWord);

//ПРАВИЛЬНО:
if (errors == FATAL) {
  System.out.println("ПОРАЖЕНИЕ!!!");
  System.out.println("Загаданное слово: " + gameWord);
}
```

**8. Непроверямые исключения** перехватывай в точке их возникновения и заменяй на проверяемые. 
Изучи различия проверяемых и непроверяемых исключений 
```
public static void newGameWord() throws IOException { <-- ГИРЛЯНДА В СИГНАТУРЕ
  Path path = Path.of("Nouns.txt");
  List<String> gameWords = Files.readAllLines(path);
  //...
}

//ПРАВИЛЬНО:
public static void newGameWord() {
  Path path = Path.of("Nouns.txt");
  List<String> gameWords = null;
  try {
    gameWords = Files.readAllLines(path);
  } catch (IOException e) {
    throw new RuntimeException(e);
  }
  //...
}
```
*"ЧК", гл.7, "...проверяемые исключения"*

**9. Метод проверки буквы на (не)принадлежность к алфавиту** 

Этот метод достоин того, чтобы отдельно обсудить всё то, что с ним не так.

- Отрицательные условия всегда читаются хуже положительных
```
public static boolean checkLetterNotInAlphabet(String input) {
  // провереят, что буква НЕ принадлежит русскому алфавиту  
}

//ПРАВИЛЬНО:
public static boolean isRussianLetter(String input) {  
  // провереят, что буква ПРИНАДЛЕЖИТ русскому алфавиту  
} 
```

- Метод проверяет символ, поэтому и принимать он должен одиночный символ
```
public static boolean checkLetterNotInAlphabet(String input)

//ПРАВИЛЬНО:
public static boolean isRussianLetter(char symbol)
```

- Алгоритм поиска (не)русской буквы- индусский код
```
public static boolean checkLetterNotInAlphabet(String input) {
  String[] alphabet = new String[]{"а", "б", "в", "г", "д", "е", "ё", "ж", "з", "и", "й", "к", "л",
    "м", "н", "о", "п", "р", "с", "т", "у", "ф", "х", "ц", "ч", "ш", "щ", "ъ", "ы", "ь", "э", "ю", "я"};

  int IndexOfLetter = Arrays.binarySearch(alphabet, input.toLowerCase());
  return IndexOfLetter < 0;
}

//ПРАВИЛЬНО:
  public static boolean isRussianLetter(char symbol) {
    symbol = Character.toLowerCase(symbol);
    return (symbol >= 'а' && symbol <= 'я') || symbol == 'ё';
  }
```
Изучи, как символы кодируются в таблице ASCII/юникод.

**10. Распечатка картинки виселицы** 

- В любом switch-case должен быть default. В данном случае default должен бросать исключение с сообщением, что такого номера картинки не существует.

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

private static void printPicture(int numPicture) {  
  String[] picture = PICTURES[numPicture];

  for(String line : picture) {
    //напечатать line
  }
}
```
Желательно картинки и метод распечатки вынести в отдельный класс, чтобы в основном классе не смешивать логику и графику.

**11. Хранение маски слова в виде массива чисел** это сложно и непонятно

Видимо, ты это и сам понимаешь, поэтому захотелось объяснить суть с помощью комментария. Но понятнее не стало 
```
//В массиве хранятся номера букв секретного слова с 1, а не с 0
indexOfGameWordLetter[i] = i + 1;
```
Лучше используй стандартный подход, при котором маска это String или StringBuilder.  
Тогда работа с маской будет проще и понятней:
```
//текст "каббала" -> маска "******"
//открыть букву 'а' -> маска "*а**а*а"
char letter = 'a';
for(int i = 0; i < text.length(), i++) {
  if(word.charAt(i) == 'letter') {
    mask.setCharAt(i, letter);  //StringBuilder
  } 
}
```

## ВЫВОД

Нужно больше практиковаться.

n.96(182)  
#ревью #виселица 