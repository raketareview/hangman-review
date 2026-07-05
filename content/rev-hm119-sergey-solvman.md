https://github.com/solvman/hangman  
[Sergey]

Игра в процедурном стиле, выполнена в одном классе.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

Неправильная последовательность в порядке отображения введенных букв
```
Enter your guess: z
Incorrect guesses: [z]

Enter your guess: n
Incorrect guesses: [z, n]

Enter your guess: b
Incorrect guesses: [b, z, n]
```
Введенные буквы должны размещаться по алфавиту(b, n, z) либо в порядке ввода(z, n, b).

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву английского алфавита
+ 👍 Есть список введенных букв
+ 👍 Простой понятный алгоритм

## ЗАМЕЧАНИЯ

**1. Нейминг**

👍 В целом, ок.

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Нарушение DRY**, магические буквы, числа, слова. Вводи константы
```
System.out.print("Start a new round? (Y/N): ");
if (input.equals("Y")) {...}
if (input.equals("N")) {...}
System.out.println("Invalid input. Enter 'Y' for yes, and 'N' to exit.");

//ПРАВИЛЬНО:
private final static String START = "Y";
private final static String QUIT = "N";

System.out.printf("Start a new round? (%s/%s): ", START, QUIT);
if (input.equals(START)) {...}
if (input.equals(QUIT)) {...}
System.out.printf("Invalid input. Enter '%s' for yes, and '%s' to exit. \n", START, QUIT);
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*

**3. Комментарии**

Комментарии! Они тут везде. Оформлены по всем правилам и если код распечатать на принтере и повесить на стене в рамку- будет оч. красиво.  
Но при чтении кода они оч. раздражают- потому что не несут никакой пользы. 

Например, здесь для объяснения одной строки кода задействовано три строки комментария и этот комментарий все равно ничего нового не объясняет. 
Потому что мы и так из названия константы видим, что это (относительный)путь к файлу 
```
/**
 * Path to the dictionary file containing words.
 */
private static final String DICTIONARY_FILE_PATH = "data/dictionary.txt";
```
Я первым делом прошелся по коду и удалил все каменты.
Когда в проекте много каментов, это плохо- пользы от них практически нет, они только забивают пространство и мешают читать код.
В идеале, комментариев вообще не должно быть, код должен объяснять сам себя через правильный нейминг и лаконичный код.  
*Мартин, "ЧК", гл.4*

**4. Нарушение конвенции кода**. В любой ситуации выделяй тело блока скобочками, даже если тело состоит из одной строки. 
Исключение- метод equals(), там можно после if не выделять блоки скобочками
```
if (dictionary.isEmpty())
  throw new IllegalStateException("Dictionary is empty; cannot select secret word.");

//ПРАВИЛЬНО:
if (dictionary.isEmpty()) {
  throw new IllegalStateException("Dictionary is empty; cannot select secret word.");
}
```

**5. При прочих равных, нужно использовать примитивный тип, а не класс-обертку**
```
private static final Integer MAX_GUESSES = 6;

//ПРАВИЛЬНО:
private static final int MAX_GUESSES = 6;
```
Для применения класса-обертки над примитивным типом данных, должна быть какая-то причина. 
Здесь использование обертки не дает ничего большего по сравнению с примитивом.

Из кода видно, что ты всем примитивам отказываешь в праве на существование
```
private static Character getUserGuess(...)

//ПРАВИЛЬНО:
private static char getUserGuess(...)
```

Но даже сама идея подчеркивает этот код и предлагает "convert to primitive":
```
Integer incorrectGuessCount = 0;
```

**6. Нарушение инкапсуляции**. Публичным должен быть только метод `main()`. Остальные методы и поля здесь должны быть приватными.

**7. Тернарники делают код нечитаемым**

Тернарники, передаваемые в аргументы методов, делают код нечитаемым
```
output.append(correctGuessLetterSet.contains(character) ? character : "*");

//ПРАВИЛЬНО:
char symbol = correctGuessLetterSet.contains(character) ? character : "*";
output.append(symbol);
```

**8. Избыточно**
```
if (input.length() == 1 && Character.isLetter(guess) && ((guess >= 'a') && (guess <= 'z'))) {...}

//ПРАВИЛЬНО:
if (input.length() == 1 && guess >= 'a' && guess <= 'z') {...} 
```

**9. Реакция на ошибки**

+ 👍 В проекте предусмотрено, что может возникнуть исключение при чтении файла со словами.  
В этом случае программа корректно закрывается и печатает юзеру соответствующее сообщение.

- В сообщении об ошибке чтения файла нужно указывать не относительный путь к файлу, а полный. 
Юзер должен точно знать, по какому адресу ему нужно искать файл
```
Error: dictionary file not found at data/dictionary.txt

//ПРАВИЛЬНО:
Error: dictionary file not found at c:/<полный путь>/data/dictionary.txt
```

- Если же файл со словами будет существовать по указанному адресу, но будет пустой(я залез в него и грохнул все строки до единой), 
то сообщение будет обманывать:
```
Error: dictionary file not found at data/dictionary.txt
```
Потому что файл по указанному адресу есть, он найден. Просто он пустой.

- Нарушение SRP. Метод не должен завершать работу программы через exit()
```
catch (FileNotFoundException exception) {
  System.out.println("Error: dictionary file not found at " + DICTIONARY_FILE_PATH);
  System.exit(1);
}
```
Каждый метод и класс имеют право завершать только свою работу. Например, через return.  
Потому что методы и классы не должны знать логику работу более высоких слоев программы, у которых могут быть свои планы на тему того, когда и почему нужно завершать работу программы.

Кроме того, при выходе через `exit()` могут не закрыться некоторые ресурсы программы.

**10. Распечатка виселицы**

- Способ формирования картинки виселицы очень сложный и непонятный:
```
hangman.append(" |    ")
  .append(incorrectGuessCount > 1 ? "/" : "")
  .append(incorrectGuessCount > 2 ? "|" : "")
  .append(incorrectGuessCount > 3 ? "\\" : "").append("\n");
  //еще миллион строк
```
Невозможно представить, как в итоге напечатается картинка- в коде видим только ее запчасти. 

- Понятно желание соптимизировать хранение картинок и все слайды сгруппировать в максимально сжатый объем. 
Но здесь это делает код значительно более худшим для понимания. 

Для такой простой игры приоритет- читаемость кода, а не оптимизация хранения картинок в памяти.

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

**11. Никогда не переопределяй toString() у енамов**

toString() в енамах нужно использовать только для отладки, переопределять его не надо
```
private static enum GameStatus {
  IN_PROGRESS, WON, LOST;

  @Override
  public String toString() {
    switch (this) {
      case IN_PROGRESS:
        return "In progress";
        //...
    }
  }
}
```
Если в енаме нужно хранить какие-то тексты, то храни их в дополнительных полях енама:
```
private enum GameStatus {
  IN_PROGRESS("In progress"), 
  //...
  private final String message;

  GameStatus(String message) {
    this.message = message;
  }

  public String getMessage() {...}
}
```
Про особенности использования `toString()` читай тут:  
*Блох, "Java эффективное программирование" гл.3.3*  

**12. Расчет состояний игры**

Нет смысла вводить какие-то промежуточные состояния игры, рассчитывать их и потом юзать
```
while (status == GameStatus.IN_PROGRESS) {...}

GameStatus calculateGameStatus(String secretWord, Set<Character> correctGuessLetterSet, Integer incorrectGuessCount) {...}

private static enum GameStatus {
  IN_PROGRESS, WON, LOST;
  //...
}
```
Тем более, что это состояние кешируется: рассчитывается один раз за цикл и записывается в переменную. 
Потом это кешированное значение несколько раз используется по ходу алгоритма.
Это потенциально багоопасный подход, потому что нужно зорко следить, чтобы во всех ветвлениях алгоритма кешированное состояние было актуально.

Избавься от посредников и от кешировния, напрямую пользуйся соответствующими методами:
```
while (!isGameOver(...)) {...}

boolean isWon(...) {...}

boolean isLose(...) {
  return incorrectGuessCount == MAX_GUESSES;
}

boolean isGameOver(...) {
  return isWon(...) || isLose(...);
}
```

**13. Создавай вспомогательные методы**

Вспомогательные методы делают код проще и понятнее
```
private static void startNewRound(String secretWord, Scanner inputScanner) {
  //миллион строк
  if (status == GameStatus.LOST) {
    displayHangman(incorrectGuessCount);
    System.out.println(GameStatus.LOST);
    System.out.println("Secret word is " + secretWord);
    return;
  }
  //миллион строк
}

//ЛУЧШЕ:
private static void startNewRound(String secretWord, Scanner inputScanner) {
  //миллион строк
  if (isLose(...)) {
    displayLoseMessage(...);
    return;
  }
  //миллион строк
}
```

**14. Используй поля из окружения**

В методах огромные списки входящих аргументов- методы перебрасывают друг другу аргументы, иногда по очень длинному сквозному маршруту.
При прочих равных, старайся брать поля из окружения класса 
```
private static Character getUserGuess(Scanner inputScanner, Set<Character> correctGuessLetterSet, Set<Character> incorrectGuessLetterSet) {
  String input = inputScanner.nextLine().trim().toLowerCase();
  //...
}

//ЗНАЧИТЕЛЬНО ЛУЧШЕ:
private static final Scanner SCANNER = Scanner inputScanner = new Scanner(System.in);

private static Character getUserGuess(...) {
  String input = SCANNER.nextLine().trim().toLowerCase();
  //...
}
```

## ВЫВОД

Пересмотреть свое отношение к примитивным типам данным.  
Для развития навыков процедурной декомпозиции, посмотреть стрим Сергея [Крестики-нолики в процедурном стиле](https://www.youtube.com/watch?v=PPikj1qHxrA)

n.119(222)  
#ревью #виселица 