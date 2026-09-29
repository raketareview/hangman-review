https://github.com/MakeOren/Hangman  
[Ruslan Shafigulin]

Игра в процедурном стиле, состоит из нескольких классов.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Нет списка введенных букв 

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву русского алфавита

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Придерживайся единообразия.

Оба эти метода делают одно и то же: получают от юзера какие-то данные.
Поэтому их названия должны быть единообразными
```java
public static boolean askToStartGame() {
  //получает команду от юзера
}

public static char readGuess() {
  //получает букву от юзера
}

//ПРАВИЛЬНО:
public static boolean readCommand() {
  //получает команду от юзера
}

public static char readRusLetter() {
  //получает букву от юзера
}
```

- Особенности написания имен констант в разных конвенциях. 

Если придерживаться Oracle Java Code Conventions, то эту константу нужно писать стилем UPPER_SNAKE.  
А если конвенции Google, то так, как здесь написано- потому что здесь этот объект-константа может менять своё внутреннее состояние  
Можно писать и так и так, главное, делать это осознанно
```java
//СЕЙЧАС ТАК:
private static final Scanner scanner;

//КОНВЕНЦИЯ ORACLE:
private static final Scanner SCANNER;

//КОНВЕНЦИЯ GOOGLE:
private static final Scanner scanner;
```

- Не называй разные концепции одним именем.

Если в проекте есть класс `HangmanStage`, то все переменные с именем, включающим это название, должны быть экземплярами этого класса.  
А геттеры с таким названием должны возвращать экземпляры `HangmanStage`.

Когда разные концепции называются одним и тем же именем, это приводит к путанице
```java
private static class HangmanStage {
  //...

  private static String getHangmanStage(int countError) {  <-- Судя по названию, метод должен вернуть не String, а экземпляр HangmanStage
    return STAGES[countError];
  }
}

//ПРАВИЛЬНО:
private static class HangmanStage {
  //...

  private static String get(int countError) {  
    return STAGES[countError];
  }
}
```

- Избыточно.

Уточнение, что это не просто буква, а буква юзера, ничего не добавляет к пониманию.  
С тем же успехом переменная могла называться `userLetterFromConsoleInput`- много лишней информации
```java
void calculateHint(char userLetter) {...}

//ПРАВИЛЬНО:
void calculateHint(char letter) {...}
```

- Но возможно, предыдущее уточнение понадобилось из-за неудачного названия метода
```java
private void calculateHint(char userLetter) { <-- Слово "calculate" это что-то больше про числа и арифметику
  //открывает букву в маске-hint    
}  

//ЛУЧШЕ:
private void updateHint(char letter) {
  //открывает букву в маске-hint    
}  
```

- Венгерская нотация.

В названии переменных не пиши тип данных, к которым они относятся.  
И вообще не употребляй венгерскую нотацию. 
Название переменной должно отвечать на вопрос что хранит переменная, а не как хранит
```java
Set<Character> wrongGuessesSet;

//ПРАВИЛЬНО:
Set<Character> wrongGuesses;
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Нарушение DRY**

Магические буквы, числа, слова. Вводи константы
```java
System.out.println("Введите 1 чтобы начать новую игру");
return answer.equals("1");

//ПРАВИЛЬНО:
private static final String START = "1";

System.out.printf("Введите '%s' чтобы начать новую игру  \n", START);
return answer.equalsIgnoreCase(START);
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru, "Замена магического числа символьной константой"*

**3. Если в блоке if есть return(break, continue, throw, exit и т.д.), то else не пишется**

В этом случае неважно, будет else или нет, так как программа будет работать одинаково, а код без else будет выглядеть читабельней
```java
if (guess.matches("[а-яё]")) {
  return guess.toCharArray()[0];
} else {  <-- Если сработает return, то инструкция else никогда не выполнится
  throw new InvalidUserInputException("Invalid user input in the HangmanConsole.readGuess method");
}

//ПРАВИЛЬНО:
if (guess.matches("[а-яё]")) {
  return guess.toCharArray()[0];
} 
throw new InvalidUserInputException("Invalid user input in the HangmanConsole.readGuess method");
```

**4. class HangmanDictionary**

- Главный публичный метод должен стоять выше вспомогательного приватного метода.

- Минимальный и максимальный размер слов.

Минимальный и максимальный размер слов класс должен не хардкодить в себе.  
Он их должен тоже получать во входящие аргументы метода точно так же, как получает путь к файлу
```java
public class HangmanDictionary {
  private static final int MIN_WORD_LENGTH = 5;
  private static final int MAX_WORD_LENGTH = 9;

  public static List<String> getDictionary(String path) {...}
}

//ПРАВИЛЬНО:
public class HangmanDictionary {

  public static List<String> getDictionary(String path, int minWordLength, int maxWordLength,) {...}
}
```
Тем самым класс будет более универсальным и его можно будет переиспользовать в других проектах, где нужно считывать текстовый файл.

+ 👍 Хорошо, что здесь бросается не базовый `RuntimeException`, а специально созданный для описания этой ситуации
```java
try {
  //...
} catch (IOException e) {
  throw new LoadDictionaryException("Error loading the dictionary in the HangmanDictionary.loadDictionary method", e);
}
```

*Хорстманн "Java. Библиотека профессионала", т.1, гл.11*
```java
"Не ограничивайтесь генерацией RuntimeException. 
Найдите подходящий подкласс или создайте собственный." - Хорстманн
```

**5. class HangmanConsole**

+ 👍 В этом классе находятся методы, которые распечатывают все сообщения. 

Это хорошо, потому что тем самым в программе классы с логикой отделяются от текстовых сообщений. 

- Не используй блок статический инициализации (static initializer). Это не дает ничего, кроме сложного для чтения кода 
```java
private static final Scanner scanner;

static {
  scanner = new Scanner(System.in);
}

//ПРАВИЛЬНО:
private static final Scanner scanner = new Scanner(System.in);
```

- Не передавай в методы аргументы-флаги.

Аргумент-флаг - это булева переменная, которая передаётся в метод.  
И в зависимости от состояния которой метод работает по одному или другому алгоритму
```java
public static void printGameOver(String answer, int failedAttemptCount, boolean isWin) {  <-- АРГУМЕНТ-ФЛАГ isWin
  if (isWin) {  <-- ПЕРЕКЛЮЧЕНИЕ АЛГОРИТМА ФЛАГОМ
    //Делать одно 
  } else {
    //Делать другое
  }
}

//ПРАВИЛЬНО:
public static void printWin(...) {
  //Печатает сообщение при ВЫИГРЫШЕ    
} 

public static void printLose(...) {
  //Печатает сообщение при проигрыше    
} 
```
*Мартин "ЧК", гл.3, "Аргументы-флаги"*
```
"Аргументы-флаги уродливы... функция выполняет более одной операции" - Мартин.
```

- Компилируй регулярные выражения. 

Если регулярное выражение используется многократно, как здесь, то нужно его откомпилировать.  
Компиляция регулярного выражения в объект `Pattern` с помощью `Pattern.compile()` позволяет повысить производительность при многократном использовании
```java
public static char readGuess() {
  String guess = scanner.nextLine();
  if (guess.matches("[а-яё]")) {...}
}

//ПРАВИЛЬНО:
private static final String REGEX = "[а-яё]";
private static final Pattern PATTERN = Pattern.compile(REGEX);

public static char readGuess() {
  String guess = scanner.nextLine();
  Matcher matcher = PATTERN.matcher(guess);
  if (matcher.matches()) {...}
}
```

```java
"Хотя String.matches — простейший способ проверки, соответствует ли строка регулярному выражению, 
он не подходит для многократного использования в ситуациях, критичных в смысле производительности." - Блох
```
Здесь, конечно, нет критической ситуации с производительностью.  
Но здесь нет и необходимости просто так использовать неоптимальный алгоритм при работе с регулярными выражениями.

*Блох "Java. Эффективное программирование", изд.3, гл.2.6*

- Нарушение правила одной операции. 

Этот метод делает две разные вещи: печатает правила и получает русскую букву от юзера.  
Это две разные операции
```java
public static char readGuess() {
  printRules();  <-- ПЕРВАЯ ОПЕРАЦИЯ
  String guess = scanner.nextLine();
  guess = guess.toLowerCase();

  if (guess.matches("[а-яё]")) {
    return guess.toCharArray()[0];  <-- ВТОРАЯ ОПЕРАЦИЯ
  } else {
    throw new InvalidUserInputException("Invalid user input in the HangmanConsole.readGuess method");
  }
}
```

Нужно убрать из этого метода печать правил.  
Клиент должен отдельно вызывать печать правил и получение буквы
```java
public static void main(String[] args) {
  //...
  userAnswer = HangmanConsole.readGuess();
  //...
}

//ПРАВИЛЬНО:
public static void main(String[] args) {
  //...
  HangmanConsole.printRules();
  letter = HangmanConsole.readRusLetter();
  //...
}
```
*Мартин, "Чистый код", гл.3, "Правило одной операции", "Один уровень абстракции"*

+ 👍 В целом класс норм.

**6. class HangmanGame**

- Нарушение конвенции кода.

Константы должны стоять выше остальных полей
```java
private String answer;
private String hint;
private final static int MAX_FAILED_ATTEMPT = 6;

//ПРАВИЛЬНО:
private final static int MAX_FAILED_ATTEMPT = 6;

private String answer;
private String hint;
```

- Картинки виселицы нужно вынести в другой (не внутренний) класс.

Точно так же, как тексты, нужно вынести картинки виселицы и метод их распечатки либо в `HangmanConsole`, либо в отдельный класс-рендерер.  
Иначе я не вижу логики, почему распечатка текстовых сообщений вынесена в отдельный класс, а распечатка картинок- нет.

- Используй многострочные строки
```java
private static final String[] STAGES = {
    "  +---+\n" +
    "  |   |\n" +
    "      |\n" +
    "      |\n" +
    "      |\n" +
    "=========",
    //oth pics
}

//ЛУЧШЕ:
private static final String[] STAGES = {
    """
    +---+
    |   |
        |
        |
        |
        |
  =========
  """,
  //oth pics
}
```
Многострочные строки(текстовые блоки) имеются в java начиная с 13 версии.

- Используй вспомогательные методы вместо флагов, которые применяются для хранения промежуточных состояний 
```java
private boolean isWin;
private boolean isGameOver;

public HangmanGame(List<String> dictionary) {
  this.isWin = false;
  this.isGameOver = false;
  //...
}

private void updateWinStatus() {
  if (hint.equals(answer)) {
    isWin = true;
  }
}

private void updateGameOver() {
  if (isWin || failedAttemptCount >= MAX_FAILED_ATTEMPT) {
    isGameOver = true;
  }
}

public boolean isWin() {
  return isWin;
}

public boolean isGameOver() {
  return isGameOver;
}

//ПРАВИЛЬНО:
public boolean isGameOver() {
  return  isWin() || isLose();
}

public boolean isWin() {
  return hint.equals(answer);
}

public boolean isLose() {
  return failedAttemptCount >= MAX_FAILED_ATTEMPT;    
}
```
Преимущество: используя вспомогательные методы вместо переменных-флагов, не приходится переживать за актуальность данных, которые хранят флаги. 
Код становится проще и надежнее.

- Используй инкремент
```java
failedAttemptCount += 1;

//ПРАВИЛЬНО:
failedAttemptCount++;
```

- Не делай ацки сложных условий в if'ах- такой код не читается. Вводи вспомогательные методы
```java
if (containsLetter(userLetter) && !hint.contains(String.valueOf(userLetter))) {
  calculateHint(userLetter);
} else if (!containsWrongGuesses(userLetter) && !hint.contains(String.valueOf(userLetter))) {
  failedAttemptCount += 1;
  wrongGuessesSet.add(userLetter);
}

//ПРАВИЛЬНО:
if (isWordLetter(letter) && !isHintLetter(letter)) {
  updateHint(letter);
} else if (!isWrongLetter(letter) && !isHintLetter(letter)) {
  failedAttemptCount++;
  addWrongLetter(letter);
}
```

**7. class Hangman**

Содержит точку входа main.

- Ответственность Main-класса.

Сейчас метод `main()` не только конструирует и запускает Игру, но и управляет процессом игры. 

Если бы программа претендовала на ООП, то это была бы ошибка.  
Потому что Main-класс должен только сконфигурировать зависимости и запустить программу.  
Управлять работой программы этот класс не должен.  
В Main'e возможен только диалог "Играть или выйти"- это всё еще относится к ответственности конструирования и запуска системы.

**Но так как автор не ставил целью написать этот проект в ООП стиле, то можно оставить так, как есть.** 

В случае ООП стиля, Main-класс должен был бы выглядеть примерно так:
```java
public class Main {
  private static final String START = "S";
  //... 

  public static void main(String[] args) {
    Dictionary dictionary = new Dictionary(FILE_PATH, MIN_WORD_LENGTH, MAX_WORD_LENGTH);

    while(true) {
      String command = inputCommand();
      if(command.equals(START)) {
        String word = dictionary.getRandomWord();

        Game game = new Game(word);
        game.start();

      } else if(command.equals(EXIT)) {
        //действия при выходе
        break;
      } else {
        //сообщение о вводе неправильной команды
      }
    }
  }

  private static String inputCommand() {...}  
}
```

*Мартин, "ЧК", гл.11, "Отделение конструирования системы от ее использования"*

- Антипаттерн "Стрела".

Не должно быть больше 2-3 уровней вложенности.  
Если больше, это антипаттерн "Стрела", такой код очень труден для понимания
```java
try {
  while (running) {
    while (true) {
      if (hangmanGame.isGameOver()) {
        if (!hangmanGame.isWin()) {
          //наконечник стрелы
        }
      }
    }
  }
}
```
"Стрела" значит, что метод делает несколько дел сразу и его нужно разделить на несколько вспомогательных.
```java
"Если вам нужно более трех уровней вложенности, вы все равно запутались, так что исправьте программу" - Линус Торвальдс.
```

- Большой божественный метод.

Божественные методы разделяй на маленькие методы, каждый из которых должен делать одно дело на одном уровне абстракции.  

Например, смотрим на божественный метод и находим в нем отдельные смысловые блоки
```java
public static void main(String[] args) {
  //куча кода
  boolean running = HangmanConsole.askToStartGame();

  while (running) {
    HangmanGame hangmanGame = new HangmanGame(dictionary);

    while (true) {
      //миллион строк кода
    }
  }
  //куча кода  
}
```

Что делает кусок кода в блоке `while (true){...}` ?  
Это один раунд игры. 

Выносим этот код во вспомогательный метод:
```java
public static void main(String[] args) {
  //куча кода
  boolean running = HangmanConsole.askToStartGame();

  while (running) {
    HangmanGame hangmanGame = new HangmanGame(dictionary);
    playRound(hangmanGame);
  }
  //куча кода  
}

private static playRound(HangmanGame hangmanGame) {
  while(true) {...}
}
```
*"ЧК", гл.3, "Правило одной операции", "Один уровень абстракции"*

- Высокая вложенность кода, бизнес логика перемешана с обработкой ошибок
```java
public static void main(String[] args) {
  try {
    //40 строк кода бизнес логики, где-то тут может вылететь какое-то исключение
  } catch (Exception e) {
    e.printStackTrace(log);
  } finally {
    if (log != null) {
      log.close();
    }
  }
}

//ПРАВИЛЬНО:
public static void main(String[] args) {
  try {  
    start();
  } catch (Exception e) {  <-- ОБРАБОТКА ОШИБОК
    e.printStackTrace(log);
  } finally {
    if (log != null) {
      log.close();
    }
  }
}

private void start() {  <-- БИЗНЕС ЛОГИКА
  //40 строк кода, где-то тут может вылететь какое-то исключение    
}
```

- Перехват исключений на уровне main.

Можно на самом верху в main'e перехватывать исключения, которые не обработал на более глубоких слоях.  
Условный пример:
```java
//ПЕРЕХВАТЫВАЕМ В MAIN ИСКЛЮЧЕНИЯ, КОТОРЫЕ НЕ СМОГЛИ ПЕРЕХВАТИТЬ И ОБРАБОТАТЬ В ПРОГРАММЕ:
public class Main {
  public static void main(String[] args) {
    try{
      start();
    } catch (Exception e) {
      System.out.println("Произошла критическая ошибка: " + e.getMessage());
    }
  }
  
  private static void start() {
    //логика работы программы
  }
}
```
*"ЧК", гл.3, "Изолируйте блоки try/catch"*

Но вообще нужно перехватывать не просто все подряд исключения.  
Прежде всего нужно перехватывать те исключения, появления которых ты ожидаешь.

Например:
```java
public class Main {
  public static void main(String[] args) {
    try{
      start();
    } catch (LoadDictionaryException e) {
      String filepath = e.getFilePath();  
      System.out.println("Ошибка чтения файла: " + filepath);

    } catch (Exception e) {   //какая-то не предусмотренная ошибка
      System.out.println("Произошла критическая ошибка: " + e.getMessage());  
    }
  }
  
  private static void start() {
    //логика работы программы
  }
}
```

## ВЫВОД

Если не считать божественного метода `main()`, остальные методы в программе в большинстве своём сделаны нормально.  
Если не считать класс `Hangman`, то для процедурного стиля проект сделан в целом норм.

Подробное объяснение, как делать эту программу в процедурном и ООП стилях, есть у Сергея в расширенных материалах.

n.192(368)  
#ревью #виселица 