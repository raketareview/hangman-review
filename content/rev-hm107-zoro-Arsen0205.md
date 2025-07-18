https://github.com/Arsen0205/Hangman  
[Zoro]

Игра в процедурном стиле, выполнена в нескольких классах. Функционал простейший.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Можно ввести не только русскую букву, но английскую букву, цифру и все что угодно
2. Можно ввести не одну букву, а набор букв и это будет считаться допустимым вводом
3. Нет списка введенных букв

4. При нажатии ентера без ввода буквы, игра вылетает- спасибо @prplhd за новый тест
```
Введите букву: 
 
Exception in thread "main" java.lang.StringIndexOutOfBoundsException: Index 0 out of bounds for length 0
	at java.base/jdk.internal.util.Preconditions$1.apply(Preconditions.java:55)
```

5. Вешается одноногий
```
 +---+
 O   |
/|\  |
/    |
    ===
```

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Простой понятный алгоритм

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Класс содержит не случайное слово, а просто слова. И не одно слово, а много
```
class WordRandom

//ЛУЧШЕ:
class Dictionary
```

- Буква это один символ, а не строка. Поэтому это либо должен быть `char`, либо не `letter`
```
String letter = scanner.nextLine().toLowerCase();
```

+ 👍 В целом, нейминг ок.

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Нарушение DRY**, магические буквы, числа, слова. Вводи константы
```
System.out.println("1. Новая игра");
System.out.println("2. Выйти из игры");
case 1:  //...
case 2:  //...
System.out.println("Пожалуйста, выберите 1 или 2.");
System.out.println("Ошибка: нужно ввести число 1 или 2.");

//ПРАВИЛЬНО:
private final static int START = 1;
private final static int QUIT = 2;

System.out.println(START + ". Новая игра");
System.out.println(QUIT + ". Выйти из игры");
case START:  //...
case QUIT:  //...
System.out.printf("Пожалуйста, выберите %d или %d. \n", START, QUIT);
System.out.printf("Ошибка: нужно ввести число %d или %d. \n", START, QUIT);
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*

**3. Тернарные операции в аргументах это зло**

Тернарники сами по себе читаюся тяжело, а когда они передаются в аргументы методов- это читается еще хуже. 
Не экономь строки, приоритет- понятность кода
```
sb.append(guessedLetters.contains(c) ? c : "_").append(" ");

//ПРАВИЛЬНО:
char symbol = guessedLetters.contains(c) ? c : MASK_SYMBOL;
sb.append(symbol).append(" ");
```

**4. class WordRandom**

- Класс хранит набор слов для игры, чтобы не хранить их в текстовом файле и не заморачиваться с файловым чтением. 
Хангман и так очень простой проект, не делай его еще проще- разберись с файловым чтением, это необходимые для работы программиста азы.

- Массив со словами лучше сделать константой.

- Иногда красивее выглядит, когда `return` выполняется сразу, без поясняющей переменной
```
int index = ThreadLocalRandom.current().nextInt(words.length);
return words[index];

//ЛУЧШЕ:
return ThreadLocalRandom.current().nextInt(words.length);
```

- Почему для выбора случайного слова применяется класс `ThreadLocalRandom`, а не его предок `Random`- совершенно неясно.
`ThreadLocalRandom` преднозначен для многопоточной среды, а в этом проекте нет намеков на многопоточность
```
int index = ThreadLocalRandom.current().nextInt(words.length);
```

**5. class HangmanGraphics**

+ 👍 Отдельный класс с картинками это хорошо.
+ 👍 Картинки в массиве это хорошо.

- Массив картинок нужно вынести из метода и сделать константой. 

Из-за того, что сейчас массив картинок находится в методе, этот объект(а массив это объект) пересоздается каждый раз, когда метод вызывается.
Если массив картинок будет константой, то этот объект будет создан только один раз.

**6. class Game**

- Большой метод `void start()`- 45 строк. Если метод больше 20 строк, то можно попробовать его сократить.

Например, смотрим на длинный метод и находим отдельные смысловые блоки
```
public void start() {
  //...
  while (true) {
    System.out.println();
    HangmanGraphics.draw(errors);
    System.out.println("Слово: " + getMaskedWord());
    System.out.print("Введите букву: ");

    String letter = scanner.nextLine().toLowerCase();
    //..
  }
}
```
Находим смысловой блок, который в начале каждого хода распечатывает справочную информацию. 
Выносим этот блок во вспомогательный метод
```
public void start() {
  //...
  while (true) {
    printTurnInfo()

    System.out.print("Введите букву: ");
    String letter = scanner.nextLine().toLowerCase();
    //..
  }
}

private void printTurnInfo() {
  System.out.println();
  HangmanGraphics.draw(errors);
  System.out.println("Слово: " + getMaskedWord());
}
```
*Мартин, "ЧК", гл.3, "Компактность!"*  
*Фаулер "Рефакторинг", гл.6, "Извлечение метода"* 

- Создавай вспомогательные методы, делай программу более простой и понятной
```
String letter = scanner.nextLine().toLowerCase();
if (word.contains(String.valueOf(letter.charAt(0)))) {...}

//ЛУЧШЕ:
char letter = inputRussianLetter(); //
if (isLetterWord(letter)) {...}

private boolean isLetterWord(char letter) {
  return word.contains(String.valueOf(letter));
}

private char inputRussianLetter() {...} //возвращает только одиночную букву русского языка
```

+ 👍 Тем не менее, метод `start()` и класс `Game` понятные и читаются легко. 

**7. class Main**

+ 👍 Тут находится диалог "играть или выйти", это хорошо- экземпляр игры создается только если выбрали "играть".

- Не используй `try-catch` для витвлений бизнес-логики. 
По возможности изолируй `try-catch` во вспомогательные методы
```
public static void main(String[] args) {
  //миллион строк кода
  while (true) {
    try {
      int choice = scanner.nextInt();
      //миллион строк кода
    } catch (InputMismatchException e) {...}
  //миллион строк кода
  }
}

//ПРАВИЛЬНО:
public static void main(String[] args) {
  //миллион строк кода
  while (true) {
    String s = scanner.nextLine();
    if(!isInteger(s)) {
      System.out.println(FAIL_MESSAGE);
      continue();  
    }
    int command = Integer.parseInt(s);
    //миллион строк кода
  }
}

private static boolean isInteger(String s) {
  try {
    Integer.parseInt(s);
    return true;
  } catch (NumberFormatException e) {
    return false;
  }
}
```
*Мартин, "ЧК", гл.3, "Изолируйте try/catch"*

## ВЫВОД

Файлового чтения нет- это не серьезно. 

n.107(201)  
#ревью #виселица 