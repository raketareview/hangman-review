https://github.com/sutyaginev/hangman  
[Евгений]

Игра в процедурном стиле, выполнена в одном классе.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

Не обнаружено.

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву русского языка
+ 👍 Есть список введенных букв
+ 👍 Аккуратный UI
+ 👍 Широко применяется форматированный вывод

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Метод не пишет слова, метод читает слова
```
words = writeWordsToList();

//ПРАВИЛЬНО:
words = readWords();
```

- В названии переменных не пиши тип данных, к которым они относится. И вообще не употребляй венгерскую ноттацию.  
Название переменной должно отвечать на вопрос что хранит переменная, а не как хранит
```
String inputString

//ПРАВИЛЬНО:
String input
```

- Этот метод не проверяет состояние игры, он создает состояние игры
```
String checkGameState(String word, int errorsCount, int guessCount) 

//ПРАВИЛЬНО:
String createGameState(String word, int errorsCount, int guessCount) 
```

- Название должно как можно лучше объяснять, что делает метод. "Заполнить зашифрованное слово"- вызывает больше вопросов, чем дает ответов
```
void fillEncryptedWord(String word, StringBuilder encryptedWord, char letter)

//ЛУЧШЕ:
void открытьБуквуВмаске(String word, StringBuilder encryptedWord, char letter)
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Нарушение инкапсуляции**

Нет никаких оснований, чтобы эти константы были публичными. В этой реализации публичным должен быть только метод `main()`
```
public static final int MAX_ERRORS_COUNT = 6;
public static final Scanner SCANNER = new Scanner(System.in);
```

**4. Нарушение DRY**, магические буквы, числа, слова. Вводи константы
```
System.out.print("\n1. Новая игра\n2. Выход\nВыберите вариант: ");
case "1":  //...
case "2":  //...
System.out.println("Вы должны ввести число 1 или 2.");


//ПРАВИЛЬНО:
private final static String START = "1";
private final static String QUIT = "2";

System.out.printf("\n%s. Новая игра\n%s. Выход\nВыберите вариант: ", START, QUIT);
case START:  //...
case QUIT:  //...
System.out.printf("Вы должны ввести число %s или %s. \n", START, QUIT);
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*   
*refactoring.guru "Замена магического числа символьной константой"*

**5. При форматированном выводе** перенос строки отделяй пробелом. Результат будет тот же, но шаблон будет лучше читаться
```
System.out.printf("Слово: [%s]%n", encryptedWord.toString());

//ПРАВИЛЬНО:
System.out.printf("Слово: [%s] %n", encryptedWord.toString());
```

**6. Приоритет- читаемость кода**, а не экономия строчек

Теперь будет видно, что сообщение распечатается в несколько строк
```
System.out.print("\n1. Новая игра\n2. Выход\nВыберите вариант: ");

//ПРАВИЛЬНО:
System.out.println("1. Новая игра");
System.out.println("2. Выход");
System.out.print("Выберите вариант: ");
```

**7. Реакция на ошибки**

В проекте предусмотрено, что может возникнуть исключение при чтени файла со словами.  
В этом случае проект просто информирует юзера о том, что файл не найден
```
try (Scanner scanner = new Scanner(Paths.get("src/words.txt"))) {
  while (scanner.hasNext()) {
    words.add(scanner.nextLine());
  }
} catch (IOException e) {
   System.out.println("File not found" + e.getMessage());
}
```
В данном случае такой способ реагирования на ошибку неправилен. Потому что если файл действительно будет отсутствовать, то все равно вылетит исключение, хотя и по другой причине
```
File not found_src\words.txt
Exception in thread "main" java.lang.IllegalArgumentException: bound must be positive
	at java.base/java.util.Random.nextInt(Random.java:551)
	at Hangman.chooseRandomWord(Hangman.java:172)
```
Здесь должна быть другая реакция на отсутствие файла со словами.  
Нужно сказать юзеру, что файл отсутствует и поэтому работа программы будет завершена. И соответственно завершить работу программы.

**8. Создавай вспомогательные методы**, делай программу более простой и понятной
```
if (!inputString.matches("[а-яё]")) {
  System.out.println("Необходимо ввести одну букву русского алфавита.");
  continue;
}

//ПРАВИЛЬНО:
if (!isRussianLetter(inputString)) {
  System.out.println("Необходимо ввести одну букву русского алфавита.");
  continue;
}

private static boolean isRussianLetter(...) {...}
```

**9. Распечатка виселицы**

- В любом switch-case должен быть default. В данном случае default должен бросать исключение с сообщением, что такого номера картинки не существует.

- Выбранный способ распечатки- непонятен и неочевиден. Как будет выглядеть результат распечатки изображения, мысленно представить невозможно
```
private static char[][] createBoard() {
  return new char[][]{
    "---- ".toCharArray(),
    "|  | ".toCharArray(),
    "|    ".toCharArray(),
    "|    ".toCharArray(),
    "|    ".toCharArray(),
    "|    ".toCharArray()
  };
} 

private static void fillBoardWithManikin(char[][] board, int errorsCount) {
  switch (errorsCount) {
    case 1:
      board[2][3] = 'o';
      break;
    case 2:
      board[3][3] = '|';
      break;
   //еще миллион строк
  }
}
```
Понятно желание соптимизировать хранение картинок и все слайды сгруппировать в максимально сжатый объем. 
Но здесь это делает код значительно более худшим для понимания. И это касается не только картинки, но и способа их распечатки через формирование картинки в массиве чаров.

Для такой простой игры приоритет- читаемость кода, а не оптимизация хранения картинок.

Поэтому каждый слайд нужно хранить в виде отдельной картинки.  
Например, в виде массива
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
```
Желательно картинки и метод распечатки вынести в отдельный класс, чтобы в основном классе не смешивать игровую логику и графику.  
Процедурный стиль дозволяет делать проекты, состоящие из нескольких классов.

**10. Вместо создания промежуточных переменных состояний игры**, сделай методы, которые будут определять состояния игры
```
private static String checkGameState(String word, int errorsCount, int guessCount) {
  if (errorsCount >= MAX_ERRORS_COUNT) {
    return GAME_STATE_LOSS;
  }

  if (guessCount == word.length()) {
    return GAME_STATE_WIN;
  }
  return GAME_STATE_NOT_FINISHED;
}

//ЛУЧШЕ:
private static boolean isGameOver(...) {
  return isWin(...) || isLose(...);  
} 

private static boolean isWin(...) {
  return guessCount == word.length();  
} 

private static boolean isLose(...) {...} 
```

И тогда вместо
```
if (!Objects.equals(gameState, GAME_STATE_NOT_FINISHED)) {
   System.out.println(gameState);
   System.out.printf("Загаданное слово: %s%n", word);
   return;
}
```
Будет
```
if (isLose(...)) {
   printLoseMessage();
   return;
} 
if (isWin(...)) {
   printWinMessage();
   return;
}
```

## ВЫВОД

В целом, для процедурного стиля приемлемо.

n.97(183)  
#ревью ##виселица 