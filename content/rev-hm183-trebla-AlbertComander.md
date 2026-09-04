https://github.com/AlbertComander/hangman-game  
[Trebla]

Игра в процедурном стиле, выполнена в двух классах.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. WTF???
```java
Hello and welcome!
i = 1
i = 2
i = 3
i = 4
i = 5
```

2. Нет списка введенных букв 

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву русского алфавита

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Названия пакетов нужно писать стилем alllowercase 
```java
package Hangman;

//ПРАВИЛЬНО:
package hangman;
```

- Не используй двусмысленных названий.

Слово "show"(показать) больше подходит для процесса отображения чего-то на экране.  
Этот метод не показывает букву на экране, он открывает букву в маске слова
```java
private void showLetter(char letter, String word, StringBuilder currentWordState) {
  for (int i = 0; i < word.length(); i++) {
    if (word.charAt(i) == letter) {
      currentWordState.setCharAt(i, letter);
    }
  }
}

//ПРАВИЛЬНО:
private void openLetter(String word, StringBuilder mask, char letter) {...}
```

- Название обманывает. Метод не обновляет хангмана, он рисует картинку виселицы
```java
private void updateHangman(int remainedLives) {
  String currentState = states.get(LIVES - remainedLives);
  System.out.println("Осталось попыток: " + remainedLives);
  System.out.println(currentState);
}
```
Обновить("update") можно какие-то хранимые данные.  
Но когда имеется в виду "обновить изображение на экране", то это должно быть `print`, `render` или `show`.  

- Венгерская нотация.

В названии переменных не пиши тип данных, к которым они относятся.  
И вообще не употребляй венгерскую нотацию.  
Название переменной должно отвечать на вопрос что хранит переменная, а не как хранит
```java
private List<String> wordsList;

//ПРАВИЛЬНО:
private List<String> words;
```

- Так "hangman" или "game"?

```java
Hangman game = new Hangman();
game.startGame();
```

Приведи названия к одному стандарту.  
Или так:
```java
Hangman hangman = new Hangman();
hangman.startGame();
```

Или так:
```java
Game game = new Game();
game.start();
```

*Oracle Java Code Conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Нарушение конвенции кода**

- Константы должны стоять выше остальных полей
```java
private final Scanner scanner = new Scanner(System.in);
private static final int LIVES = 6;
private List<String> wordsList;

//ПРАВИЛЬНО:
private static final int LIVES = 6;

private final Scanner scanner = new Scanner(System.in);
private List<String> wordsList;
```

*Oracle Java Code Conventions*

**3. Нарушение DRY**

- Магические буквы, числа, слова. Вводи константы 
```java
System.out.println("1. Начать игру");
System.out.println("2. Выход");
if (selectedOption == 1) {...}

//ПРАВИЛЬНО:
private static final int START = 1;
private static final int QUIT = 2;

System.out.println(START + ". Начать игру");
System.out.println(QUIT + ". Выход");
if (selectedOption == START) {...}
```

- Дублирование кода
```java
public void startGame() {
  System.out.println("Игра виселица");
  System.out.println("Выберите действие");
  System.out.println("1. Начать игру");
  System.out.println("2. Выход");
  //...
}

private int validateOption(String option) {
  System.out.println("Выбрана недопустимая опция!");
  System.out.println("Выберите действие");
  System.out.println("1. Начать игру");
  System.out.println("2. Выход");
  //...
}

//ПРАВИЛЬНО:
public void startGame() {
  printMenu();
  //...
}

private int validateOption(String option) {
  System.out.println("Выбрана недопустимая опция!");
  printMenu();
  //...
}

private void printMenu() {
  System.out.println("Выберите действие");
  System.out.println(START + ". Начать игру");
  System.out.println(QUIT + ". Выход");
}
```

- Ещё куча магии.

Если ты захочешь заменить символ закрытой буквы, то придётся перековырять много кода.  
И если где-то забудешь его поменять, то вся логика работы программы расползётся
```java
StringBuilder currentWordState = new StringBuilder("-".repeat(word.length()));
while (remainedLives > 0 && (currentWordState.indexOf("-") != -1)) {...}
if (currentWordState.indexOf("-") == -1) {...}

//ПРАВИЛЬНО:
StringBuilder currentWordState = new StringBuilder(MASK_SYMBOL.repeat(word.length()));
while (remainedLives > 0 && (currentWordState.indexOf(MASK_SYMBOL) != -1)) {...}
if (currentWordState.indexOf(MASK_SYMBOL) == -1) {...}
```

*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*

**4. Если в блоке if есть return(break, continue, throw, exit и т.д.), то else не пишется**

В этом случае неважно, будет else или нет, так как программа будет работать одинаково, а код без else будет выглядеть читабельней
```java
if (usedLetters.contains(letter)) {
  System.out.println("Вы уже вводили эту букву, попробуйте другую!");
  continue;  <-- Если сработает continue, то инструкция else никогда не выполнится
} else {
  usedLetters.add(letter);
}

//ПРАВИЛЬНО:
if (usedLetters.contains(letter)) {
  System.out.println("Вы уже вводили эту букву, попробуйте другую!");
  continue;
} 
usedLetters.add(letter);
```

**5. Компилируй регулярные выражения**

Если регулярное выражение используется многократно, как здесь, то нужно его откомпилировать.  
Компиляция регулярного выражения в объект `Pattern` с помощью `Pattern.compile()` позволяет повысить производительность при многократном использовании
```java
private int validateOption(String option) {
  while (!option.matches("[12]")) {...}
}

//ПРАВИЛЬНО:
private static final String REGEX = "[12]";
private static final Pattern PATTERN = Pattern.compile(REGEX);

private int validateOption(String option) {
  while (true)) {
    Matcher matcher = PATTERN.matcher(option);
    if(matcher.matches()) {
      return Integer.parseInt(option);
    }
    //...
  }
}
```

```java
"Хотя String.matches — простейший способ проверки, соответствует ли строка регулярному выражению, 
он не подходит для многократного использования в ситуациях, критичных в смысле производительности." - Блох
```
Здесь, конечно, нет критической ситуации с производительностью.  
Но здесь нет и необходимости просто так использовать неоптимальный алгоритм при работе с регулярными выражениями.  
*Блох "Java. Эффективное программирование", изд.3, гл.2.6*

**6. class Hangman**

- Поля класса.

Стоит вынести в поля класса такие значения: слово, маска, текущее количество ошибок, использованные буквы etc.  
А не перебрасывать эти значения между методами
```java
public class Hangman {
  //...
  private List<String> states;

  //... 
  private void showLetter(char letter, String word, StringBuilder currentWordState) {
    for (int i = 0; i < word.length(); i++) {
      if (word.charAt(i) == letter) {
        currentWordState.setCharAt(i, letter);
      }
    }
  }
}

//ПРАВИЛЬНО:
public class Hangman {
  //...
  private List<String> states;
  private String word;
  private StringBuilder mask;
  //другие общие поля класса, которые нужны 

  //... 
  private void openLetter(char letter) {
    for (int i = 0; i < word.length(); i++) {
      if (word.charAt(i) == letter) {
        mask.setCharAt(i, letter);
      }
    }
  }
}
```

Какие значения должны быть полями класса, а какие переменными метода, это бывает не всегда очевидно.  
Иногда могут быть разные доводы в пользу первого или второго варианта.  
Общее правило такое: если много разных методов обращаются к одним и тем же данным, то эти данные нужно сделать полем класса.

- Нарушение SRP. Метод не должен завершать работу программы через exit()
```java
public void startGame() {
  //...    
  if (selectedOption == 1) {
    //...
  } else {
    System.exit(0);
  }
}
```
Каждый метод и класс имеют право завершать только свою работу. Например, через return.  
Потому что методы и классы не должны знать логику работы более высоких слоев программы, у которых могут быть свои планы на тему того, когда и почему нужно завершать работу программы.

Кроме того, при выходе через `exit()` могут не закрыться некоторые ресурсы программы.

В этом методе вместо `exit(0)` достаточно использовать простой `return`.

- Невнятный метод.

Что вообще делает этот метод?
```java
private int validateOption(String option) {
  while (!option.matches("[12]")) {
    System.out.println("Выбрана недопустимая опция!");
    System.out.println("Выберите действие");
    System.out.println("1. Начать игру");
    System.out.println("2. Выход");
    option = scanner.next();
  }
  return Integer.parseInt(option);
}
```
Он делает то, что обещает его название- валидирует опцию?  
Нет, он не валидирует опцию.

Он получает опцию от клиента, и если она не подходит, то метод заново получает эту опцию от юзера.  
Как назвать этот процесс я не знаю, но одно ясно точно- здесь совмещено несколько действий сразу.

Каждый метод должен делать одно дело на одном уровне абстракции- правило одной операции.

В данном случае нужно определиться с назначением метода. То есть с его ответственностью.  
Получение данных от юзера это одна ответственность, а валидация прилетевшего извне аргумента- другая ответственность.

Если цель получить от юзера число в заданном диапазоне, то напиши метод, который делает только это:
```java
private int inputCommand() {
  while (true) {
    System.out.println("Выберите действие");
    System.out.println(START + ". Начать игру");
    System.out.println(QUIT + ". Выход");

    String line = scanner.nextLine();

    if(isNumber(line)) {
      command = Integer.parseInt(line);

      if(command == START || command == QUIT) {
        return command;
      }
    }

    System.out.println("Выбрана недопустимая опция!");
  }
}

private static boolean isNumber(String s) {
  try {
    Integer.parseInt(s);
    return true;
  } catch(NumberFormatException e) {
    return false;
  }
}
```
*"ЧК", гл.3, "Правило одной операции", "Один уровень абстракции"*

- Объявляй переменные там, где они используются.

Минимизируй область видимости локальных переменных
```java
String input = "";
while (remainedLives > 0 && (currentWordState.indexOf("-") != -1)) {
  input = scanner.next().toLowerCase();
  //...
}

char letter = ' ';
if (input.matches("[а-яё]")) {
  letter = input.charAt(0);
  //...
}

//ПРАВИЛЬНО:
while (remainedLives > 0 && (currentWordState.indexOf("-") != -1)) {
  String input = scanner.next().toLowerCase();
  //...
}

if (input.matches("[а-яё]")) {
  char letter = input.charAt(0);
  //...
}
```
*Блох, "Java. Эффективное программирование", изд.3, гл.9.1*  

- Избыточно. Используй декремент
```java
remainedLives -= 1;

//ПРАВИЛЬНО:
remainedLives--;
```

- Создавай вспомогательные методы, делай программу более простой и понятной
```java
private void playRound(String word) {
  while (remainedLives > 0 && (currentWordState.indexOf("-") != -1)) {
    //...  
    if (input.matches("[а-яё]")) {...}
    if (word.indexOf(letter) != -1) {...}
  }
  if (remainedLives == 0) {
      System.out.println("К сожалению вы проиграли! Загаданным словом было: " + word);
  }
  if (currentWordState.indexOf("-") == -1) {
    System.out.println("Поздравляю! Вы угадали слово! Загаданным словом было: " + word);
  }
}

//ПРАВИЛЬНО:
private void playRound(String word) {
  while (!isEndRound()) {
    //...  
    if (isRusLetter(input)) {...}
    if (isWordLetter(letter)) {...}
  }

  if(isWin()) {
    printWinMessage();
  } else if(isLose()) {
    printLoseMessage();
  }
}

private boolean isWordLetter(char letter) {
  return word.indexOf(letter) != -1;    
}

private boolean isEndRound() {
  return isLose() || isWin();    
}

private boolean isWin() {
  return currentWordState.indexOf(MASK_SYMBOL) == -1;
}
```
Даже если метод состоит из одной строки, но при этом делает код программы читабельнее, то этот метод имеет право на существование.

- Несколько разных операций в методе на одном уровне абстракции.

Этот метод делает два разных дела на одном уровне абстракции.
Подробности низкого уровня выноси во вспомогательные методы.

Например, смотрим на большой метод и находим в нем отдельные смысловые блоки.  
Видим *реализацию* двух низкоуровневых операций: чтение файла и получение команды от юзера
```java
public void startGame() {
  try {
    wordsList = Files.readAllLines(Path.of("resources/words_for_game.txt"));
    String content = Files.readString(Path.of("resources/hangman_states.txt"));
    states = Arrays.stream(content.split("###")).toList();
  } catch (IOException error) {
    System.out.println("Не удалось загрузить файл");
    return;
  }
  while (true) {
    System.out.println("Игра виселица");
    System.out.println("Выберите действие");
    System.out.println("1. Начать игру");
    System.out.println("2. Выход");
    int selectedOption = validateOption(scanner.next());
    if (selectedOption == 1) {
      String word = chooseWord(wordsList);
      System.out.println("Загаданное слово: " + "_".repeat(word.length()));
      playRound(word);
    } else {
      System.out.println("До встречи!");
      System.exit(0);
    }
  }
}
```

Выносим низкоуровневые подробности во вспомогательные методы.  
Например так:
```java
public void startGame() {
  words = readWordsFromFile(WORD_PATH);
  stages = readStagesFromFile(STAGE_PATH);

  while (true) {
    int command = inputCommand();

    if(command == START) {
      String word = chooseWord(words);
      printStartRound(word);
      playRound(word);

    } else if(command == QUIT) {
      printQuit();
      return;

    } else {
      printWrongCommand();  
    }
  }  
}
```
*"ЧК", гл.3, "Правило одной операции", "Один уровень абстракции"*

## ВЫВОД

И в процедурном и в ООП стиле нужно научиться делать методы, которые будут соответствовать таким требованиям:  
🔹 Маленький размер  
🔹 Выполняют одну операцию на одном уровне абстракции  
🔹 Не совмещают команду и запрос  
🔹 Не содержат больше трех уровней вложенности  
🔹 Не имеют побочных эффектов

Пример хороших методов в проекте: `chooseWord(...)`, `showLetter(...)`.  
Пример плохих методов: в той или иной степени все остальные, но особенно `startGame()`, `validateOption(...)`.

Посмотри на ютубе ролики Немчинского по распределению кода между методами:
```
"Правильные методы по Clean Code"
"Как называть переменные, методы и классы? Чистый код (Clean Code)"
"Принцип хорошего кода KISS"
```

Подробное объяснение, как делать эту программу в процедурном и ООП стилях, есть у Сергея в расширенных материалах.

n.183(354)  
#ревью #виселица 