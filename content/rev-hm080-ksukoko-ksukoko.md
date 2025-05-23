https://github.com/ksukoko/Hangman.git    
[ksu koko]

Простая реализация в процедурном стиле, выполнена в одном классе. Есть баги.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Команды не работают
```
Если вы хотите начать игру, ведите Да, и Нет, если не хотите 
да
Убедитесь, что вы верно ввели команду
Если вы хотите начать игру, ведите Да, и Нет, если не хотите 
нет
Убедитесь, что вы верно ввели команду
Если вы хотите начать игру, ведите Да, и Нет, если не хотите 
```

2. При повторном вводе неправильной буквы увеличивается счетчик ошибок
```
Введите букву 
а
+---+
|   |
O   |
    |
    |
    |
=========

Загаданное слово _____________
Введите букву 
а
+---+
|   |
O   |
|   |
    |
    |
=========
```

3. Одна и та же буква в разных регистрах считается двумя разными буквами и увеличивает счетчик ошибок
```
Введите букву 
в
 +---+
 |   |
 O   |
/|\  |
     |
     |
 =========

Загаданное слово ___________в_
Введите букву 
В
 +---+
 |   |
 O   |
/|\  |
/    |
     |
 =========

Загаданное слово ___________в_
```

4. Нет списка неправильно введенных букв.
5. Можно ввести не только русскую букву, но букву любого другого алфавита, цифру и вообще все что угодно.
6. Как это ничего не ввел? Я ввел 'ддодод'
```
Введите букву 
ддодод
Вы ничего не ввели, попробуйте ещё раз ввести букву
```

7. Вешается одноногий
```
 +---+
 |   |
 O   |
/|\  |
/    |
     |
 =========

Загаданное слово _________
Введите букву 
у
Вы проиграли :( 
```

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночный символ
+ 👍 Простой понятный алгоритм

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Название переменной должно объяснять, что оно в себе хранит. Эта переменная хранит не новую игру, а команду
```
String newGame = scanner.nextLine();

if ("Нет".equals(newGame)) {
  System.out.println("Игра окончена");
}

//ПРАВИЛЬНО:
String command = scanner.nextLine();
```

- КОНСТАНТЫ_ПИШУТСЯ_СТИЛЕМ_UPPER_SNAKE
```
private static final String[] hangmanStages

//ПРАВИЛЬНО:
private static final String[] HANGMAN_STAGES
```

- Название метода должно как можно лучше объяснять, что делает метод
```
private static char getUserInput() 

//ЛУЧШЕ:
private static char inputLetter() 
```

- Отрицательные условия читаются в коде хуже положительных
```
boolean isIncorrectGuess(char userGuess) {
  //сообщает, что буква некорректная  
}

//ЛУЧШЕ:
boolean isCorrectGuess(char userGuess) {
  //сообщает, что буква КОРРЕКТНАЯ  
}
```

- Название метода должно быть глаголом
```
void game()

//ПРАВИЛЬНО:
void startGame()
```
*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*  

**2. Нарушение конвенции кода**
```
public class Hangman {
  private static void selectRandomWord() {...}

  int remainingAttempts = 6;

  private static char getUserInput() {...}
  public static void main(String[] args) {...}
  //...
}
```
Публичные методы должны находиться выше вспомогательных приватных. А метод `main()` должен быть выше всех остальных. Поля не должны находиться ниже методов

**3. Нарушение DRY**, магические буквы, числа, слова. Вводи константы 
```
System.out.println("Если вы хотите начать игру, ведите Да, и Нет, если не хотите ");
String newGame = scanner.nextLine();

if ("Нет".equals(newGame)) {
  //...  
} else if ("Да".equals(newGame)) {...}

//ПРАВИЛЬНО:
private final static String START = "да";
private final static String QUIT = "нет";

System.out.printf("Если вы хотите начать игру, ведите %s, и %s, если не хотите %n", START, QUIT);
String command = scanner.nextLine().toLowerCase();

if (command.equals(START)) {
  //...  
} else if (command.equals(QUIT)) {...}
```

Еще магическое в проекте: '_', 6.

*Фаулер, "Рефакторинг", гл.8 п."Замена магического числа символической константой"*   
*refactoring.guru "Замена магического числа символьной константой"*  

**4. Если в блоке if есть return**(break, continue, throw, exit и т.д.), то else не пишется - в этом случае неважно, будет else или нет, так как программа будет работать одинаково
```
if (letter.length() == 1) {
  return letter.charAt(0);
} else {
  System.out.println("Вы ничего не ввели, попробуйте ещё раз ввести букву");
}

//ПРАВИЛЬНО:
if (letter.length() == 1) {
  return letter.charAt(0);
} 
System.out.println("Вы ничего не ввели, попробуйте ещё раз ввести букву");
```

**5. Побочные эффекты**

Судя по названию метод должен только проверить букву на корректность
```
private static char[] hiddenWord;

private static boolean isIncorrectGuess(char userGuess) {
  boolean correct = false;
  for (int i = 0; i < randomWord.length(); i++) {
    if (randomWord.charAt(i) == userGuess) {
      hiddenWord[i] = userGuess;  <-- побочный эффект
      correct = true;
    }
  }
  return !correct;
}
```
Но кроме этого метод еще открывает букву в маске `char[] hiddenWord`.
Метод нужно разделить на несколько, каждый из которых будет выполнять только одну задачу.

Другие методы с побочными эффектами: `isWordComplete()`, `selectRandomWord()`.  
*Фаулер "Рефакторинг", г.6, п."Извлечение метода"*  

**6. Стоит разделить класс Hangman на несколько**, это улучшит читаемость кода.

Процедурный стиль позволяет делать проекты, состоящие из нескольких классов.  
Стоит вынести в отдельный класс картинки хангмана- это отделит картинки от логики класса Hangman.  
Также в отдельный класс можно вынести чтение слов из файла.

## ВЫВОД

Убрать баги.

n.80(150)  
#ревью #виселица