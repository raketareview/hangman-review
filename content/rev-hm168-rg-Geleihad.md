https://github.com/Geleihad/Hangman  
[распознается графически]

Игра в процедурном стиле, выполнена в одном классе.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Не используй сложных формулировок, особенно отрицательных
```java
Введите любой символ, кроме N или n, чтобы начать игру. 
Введите N или n, чтобы завершить работу.
```

Используй простые положительные условия.  
Например:
```java
Введите 'S' для начала игры или любой другой символ для выхода.
```

Еще лучше- используй стандартные решения, к которым привык юзер.  
Например:
```java
Начать игру? (Y/N): 
```

2. Нет списка введенных букв 

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву русского алфавита

## ЗАМЕЧАНИЯ

**1. Нейминг**

- В названия не нужно вставлять частицы "Of", "The", "Are" и т.д.

Это только делает названия более громоздкими.  
Если конечно там "Of" не используется в контексте valueOf()
```java
int numOfWrongLetters;

//ПРАВИЛЬНО:
int wrongLettersCount;
```

- Не сокращай названия без особой необходимости
```java
Scanner sc = new Scanner(System.in);

//ПРАВИЛЬНО:
Scanner scanner = new Scanner(System.in);
```

- Название должно объяснять, что делает метод
```java
private static boolean isInputOk(String input) {
  return input.length() == 1 && input.matches("^[А-Яа-яЁё]$");
}

//ПРАВИЛЬНО:
private static boolean isRussianLetter(String input) {
  return input.length() == 1 && input.matches("^[А-Яа-яЁё]$");
}
```

- Если метод называется "сравнить"(check), то его результат должен быть boolean.  
Если его результат не boolean, значит он не должен так называться
```java
void checkLetter(char letter)
```

- Для одной концепции используй одно название.  
Оба метода что-то печатают, поэтому оба должны называться или "print" или "show"
```java
private static void showLetter(char letter) {
  //печатает букву
}

private static void printState() {
  //печатает состояние виселицы
}
```

- Геттер должен что-то возвращать. Геттер, который ничего не возвращает- нонсенс
```java
void getWord()

//ПРАВИЛЬНО:
void initWord()
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Нарушение конвенции кода**

- Константы должны стоять выше остальных полей
```java
private static int numOfWrongLetters;
private static final int MAX_MISTAKES = 6;

//ПРАВИЛЬНО:
private static final int MAX_MISTAKES = 6;
private static int numOfWrongLetters;
```

- Скобочки.

В любой ситуации выделяй тело блока скобочками, даже если тело состоит из одной строки.  
Исключение- метод equals(), там можно после if не выделять блоки скобочками
```java
if (numOfWrongLetters < MAX_MISTAKES)
  System.out.println("Победа! Загаданное слово было: " + wordToGuess + "\n");
else System.out.println("Поражение! Загаданное слово было: " + wordToGuess + "\n");

//ПРАВИЛЬНО:
if (numOfWrongLetters < MAX_MISTAKES) {
  System.out.println("Победа! Загаданное слово было: " + wordToGuess + "\n");
} else {
  System.out.println("Поражение! Загаданное слово было: " + wordToGuess + "\n");
}
```
*"Oracle Java code conventions"* 

**3. Форматирование**

- Используй многострочные строки.
```java
System.out.println(
  "Введите любой символ, кроме N или n, чтобы начать игру. " + "\n" +
  "Введите N или n, чтобы завершить работу.");

//ПРАВИЛЬНО:
System.out.println(
    """
       Введите любой символ, кроме N или n, чтобы начать игру. 
       Введите N или n, чтобы завершить работу.
       """);
```
Многострочные строки(текстовые блоки) имеются в java начиная с 13 версии.

- Каждую строку распечатывай отдельной инструкцией.

Если хочется большей универсальности и совместимости с более старыми версиями явы, то пиши вывод каждой строки отдельной инструкцией.  
Все равно сейчас код написан хоть и одной инструкцией, но в две строки
```java
System.out.println(
  "Введите любой символ, кроме N или n, чтобы начать игру. " + "\n" +
  "Введите N или n, чтобы завершить работу.");

//ПРАВИЛЬНО:
System.out.println("Введите любой символ, кроме N или n, чтобы начать игру.");
System.out.println("Введите N или n, чтобы завершить работу.");
```

- Форматированный вывод.

Если нужно печатать или создавать строку с более, чем одним подстановочным значением или значение вставляется внутрь сообщения, 
используй форматирование- тогда сразу будет виден весь шаблон
```java
System.out.println(state + " Кол-во ошибок: " + numOfWrongLetters + "/" + MAX_MISTAKES);

//ПРАВИЛЬНО:
System.out.printf("%s Кол-во ошибок: %d/%d  %n", state, numOfWrongLetters, MAX_MISTAKES);
```

**4. Нарушение DRY**, магические буквы, числа, слова. Вводи константы
```java
while (!userInput.equalsIgnoreCase("N")) {...}
System.out.println(
  "Введите любой символ, кроме N или n, чтобы начать игру. " + "\n" +
  "Введите N или n, чтобы завершить работу.");

//ПРАВИЛЬНО:
private static final String QUIT = "N";

while (!userInput.equalsIgnoreCase(QUIT)) {...}

System.out.printf("Введите любой символ, кроме '%s', чтобы начать игру.", QUIT);
System.out.printf("Введите '%s', чтобы завершить работу.", QUIT);
```

Тут тоже:
```java
state = new StringBuilder("*".repeat(wordToGuess.length()));
while ((numOfWrongLetters < MAX_MISTAKES) && (state.indexOf("*") != -1)) {...}

//ПРАВИЛЬНО:
state = new StringBuilder(MASK_SYMBOL.repeat(wordToGuess.length()));
..
```

*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*

**5. Регулярные выражения**

- Компилируй регулярные выражения. 

Если регулярное выражение используется многократно, как здесь, то нужно его откомпилировать.  
Компиляция регулярного выражения в объект `Pattern` с помощью `Pattern.compile()` позволяет повысить производительность при многократном использовании
```java
private static boolean isInputOk(String input) {
  return input.length() == 1 && input.matches("^[А-Яа-яЁё]$");
}

//ПРАВИЛЬНО:
private static final String REGEX = "^[А-Яа-яЁё]$";
private static final Pattern PATTERN = Pattern.compile(REGEX);

private static boolean isInputOk(String input) {
  Matcher matcher = PATTERN.matcher(input);
  return input.length() == 1 && matcher.matches();
}
```

```java
"Хотя String.matches — простейший способ проверки, соответствует ли строка регулярному выражению, 
он не подходит для многократного использования в ситуациях, критичных в смысле производительности." - Блох
```
Здесь, конечно, нет критической ситуации с производительностью.  
Но здесь нет и необходимости просто так использовать неоптимальный алгоритм при работе с регулярными выражениями.  
*Блох "Java. Эффективное программирование", изд.3, гл.2.6*

- Регулярное выражение "^[А-Яа-яЁё]$" уже проверяет, состоит ли строка из одного символа.  
Поэтому проверка на длину строки избыточна:
```java
private static final String REGEX = "^[А-Яа-яЁё]$";
private static final Pattern PATTERN = Pattern.compile(REGEX);

private static boolean isInputOk(String input) {
  Matcher matcher = PATTERN.matcher(input);
  return matcher.matches();
}
```

**6. Печать картинки виселицы через switch-case или if-elseif - индусский код.** 

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
    picture = PICTURES[pictureNumber];  
    System.out.println(picture);
  }
}
```
Желательно картинки и метод распечатки вынести в отдельный класс, чтобы в основном классе не смешивать логику и графику.  
Процедурный стиль программирования позволяет делать в проекте несколько классов, когда они выполняют роль контейнеров для функций.

**7. Создавай вспомогательные методы, делай программу более простой и понятной**
```java
while ((numOfWrongLetters < MAX_MISTAKES) && (state.indexOf("*") != -1)) {
 
if (numOfWrongLetters < MAX_MISTAKES)
  System.out.println("Победа! Загаданное слово было: " + wordToGuess + "\n");
    else System.out.println("Поражение! Загаданное слово было: " + wordToGuess + "\n");

//ПРАВИЛЬНО:
while (!isGameOver()) {
 
if (isWin()) {
  System.out.println("Победа! Загаданное слово было: " + wordToGuess + "\n");
} else {
  System.out.println("Поражение! Загаданное слово было: " + wordToGuess + "\n");
}

private static boolean isWin() {
  return state.indexOf(MASK_SYMBOL) == -1;
}

private static boolean isLose() {...}

private static boolean isGameOver() {
  return isWin() || isLose();  
}
```
Даже если метод состоит из одной строки, но при этом делает код программы читабельнее, то этот метод имеет право на существование.

**8. Разбиение программы на методы**

👍 Методы в программе довольно хорошие.

Они маленькие, не нарушают правила одной операции, побочных эффектов не замечено.  
Код читается легко. 

## ВЫВОД

Для процедурного стиля в целом ок.

n.168(325)  
#ревью #виселица 