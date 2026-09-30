https://github.com/ShatrovNA1/Hangman  
[Nikita Shatrov]

Игра в процедурном стиле, состоит из одного класса.  
По коду в целом ок, жаль игра не работает.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Не принимает русские буквы
```
Введите букву: ё
Введите одну букву.
Количество ошибок: 0 из 6

Введите букву: я
Введите одну букву.
Количество ошибок: 0 из 6

Введите букву: щ
Введите одну букву.
Количество ошибок: 0 из 6

Введите букву: ю
Введите одну букву.
Количество ошибок: 0 из 6
```

Понятно, что это связано с работой клавиатурного ввода через `IO.readln()`.  
Но букву 'ё' не получится ввести уже по причине недостатков алгоритма. 

2. Нет списка введенных букв 

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву
+ 👍 Простой понятный алгоритм

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Константы это только поля классов, а не методов.  
Стилем UPPER_SNAKE можно писать только названия констант, а не просто `final` полей
```java
void startGame(List<String> allWords) {
  final int MAX_ATTEMPTS = 6;
  while (attempts < MAX_ATTEMPTS) {...}
}

//ПРАВИЛЬНО:
private static final int MAX_ATTEMPTS = 6;

void startGame(List<String> allWords) {
  while (attempts < MAX_ATTEMPTS) {...}
}
```

- Название обманывает.

Этот метод не печатает `word`, он печатает что-то иное
```java
void printWord(String word, Set<Character> guessedLetters) {
  for (char c : word.toCharArray()) {
    if (guessedLetters.contains(c)) {
      IO.print(c + " ");
    } else {
      IO.print("_ ");
    }
  }
  IO.println();
}
```

Потому что если бы он печатал `word`, то выглядел бы так
```java
void printWord(String word) {
  IO.println(word);
}
```

+ 👍 В целом нейминг ок.

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Расположение методов в классе**

Метод `main()` должен стоять выше всех остальных методов в классе- это точка входа и мы сразу должны видеть, что в этой точке происходит.

**3. Форматирование**

- Форматирование строк.

Если нужно печатать или создавать строку с более чем одним подстановочным значением или значение вставляется внутрь сообщения, 
используй форматирование- тогда сразу будет виден весь шаблон
```java
IO.println("Количество ошибок: " + attempts + " из " + MAX_ATTEMPTS);

//ПРАВИЛЬНО:
IO.printf("Количество ошибок: %d из %d  \n", attempts, MAX_ATTEMPTS);
```

**4. Нарушение DRY**

Магические буквы, числа, слова. Вводи константы 
```java
IO.println("1) Новая игра");
IO.println("2) Выход");

case "1":  //...
case "2":  //...

//ПРАВИЛЬНО:
private static final String START = "1";
private static final String QUIT = "2";

IO.println(START + ") Новая игра");
IO.println(QUIT + ") Выход");

case START:  //...
case QUIT:  //...
```

Другие магические штуки в классе: 
```java
6, "_ "
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru, "Замена магического числа символьной константой"*

**5. Exceptions**

- Перехватывай конкретное исключение.

Всегда перехватывай не базовое, а конкретное исключение, которое может сгенерировать код.  
В данном случае `new FileReader(...)` может бросить не какое угодно исключение, а конкретное- `FileNotFoundException`
```java
try (FileReader fileReader = new FileReader("dictionary.txt")) {
  return fileReader.readAllLines();
} catch (Exception _) {...}

//ПРАВИЛЬНО:
try (FileReader fileReader = new FileReader("dictionary.txt")) {
  return fileReader.readAllLines();
} catch (FileNotFoundException _) {...}
```

Перехват всех подряд исключений вместо конкретных приведёт к тому, что однажды код выкинет исключение, которого ты в этом месте не ожидаешь.  
Потом ты ошибочно примешь это исключение за то, которое ожидаешь и неправильно поймёшь причину возникшего бага. 

Да, там ещё есть метод, который может бросить `IOException`
```java
fileReader.readAllLines();
```

Но так как `FileNotFoundException` наследуется от `IOException`, то их оба можно ловить на уровне `IOException`
```java
try (FileReader fileReader = new FileReader("dictionary.txt")) {
  return fileReader.readAllLines();
} catch (IOException _) {...}
```

**6. Печать картинок**

- Картинки виселицы должны быть массивом-константой.

Массив картинок не должен быть переменной метода.  
Потому что в этом случае этот объект(а массив это объект) будет пересоздаваться каждый раз при вызове метода. 

Картинки должны храниться в константе
```java
void printHangman(int attempts) {
  String[] hangman = { /*картинки*/ };
  //...
  IO.println(hangman[Math.min(attempts, 6)]);
}

//ПРАВИЛЬНО:
private static final String[] PICTURES = { /*картинки*/ };

void printHangman(int attempts) {
  String[] picture = PICTURES[Math.min(attempts, 6)];
  IO.println(picture);
}
```

**7. Создавай вспомогательные методы, делай программу более простой и понятной**
```java
void startGame(List<String> allWords) {
  //...

  while (attempts < MAX_ATTEMPTS) {
    //...
    if (letter < 'а' || letter > 'я') {
      IO.println("Введите букву русского алфавита не заглавную");
      continue;
    }
    //...

    if (isWordGuessed(word, guessedLetters)) {
      IO.println();
      IO.println("ПОБЕДА!");
      IO.println("Слово: " + word);
      return;
    }
  }
}

//ПРАВИЛЬНО:
void startGame(List<String> allWords) {
  //...

  while (!isGameOver()) {
    //...
    if (isRusLetter(letter)) {
      IO.println("Введите букву русского алфавита не заглавную");
      continue;
    }
    //...

    if (isWin(...)) {
      printWinMessage(word);
      return;
    }
  }
}

private static boolean isGameOver(...) {
  return isWin(...) || isLose(...);  
}

private static boolean isWin(...) {...}

private static boolean isLose(...) {...}
```
Даже если метод состоит из одной строки, но при этом делает код программы читабельнее, то этот метод имеет право на жизнь.

**8. Большой божественный метод** 

Божественные методы разделяй на маленькие методы, каждый из которых должен делать одно дело на одном уровне абстракции.  

Например, смотрим на божественный метод и находим в нём отдельные смысловые блоки
```java
void startGame(List<String> allWords) {
  //много кода до

  IO.print("Введите букву: ");
  String input = IO.readln().trim().toLowerCase();
  if (input.length() != 1 || !Character.isLetter(input.charAt(0))) {
    IO.println("Введите одну букву.");
    continue;
  }

  char letter = input.charAt(0);
  
  //много кода после
}
```

Что делает этот кусок кода? Он получает от юзера (любые) буквы.  
Выносим этот код во вспомогательный метод:
```java
void startGame(List<String> allWords) {
  //много кода до

  char letter = inputLetter();
  
  //много кода после
}

private static char inputLetter() {
  IO.print("Введите букву: ");

  while(true) {
    String input = IO.readln().trim().toLowerCase();

    if (input.length() == 1) {
      char c = input.charAt(0);

      if(Character.isLetter(c) {
         return c;
      }    
    }

    IO.println("Введите одну букву.");
  }
}
```
*"ЧК", гл.3, "Компактность", "Правило одной операции", "Один уровень абстракции"*

**9. Антипаттерн "Стрела"**

Не должно быть больше 2-3 уровней вложенности.  
Если больше, это антипаттерн "Стрела", такой код очень труден для понимания
```java
while (gameState != State.EXIT) {
  switch (gameState) {
    case MENU -> {
      switch (input) {
        case "1":
        //наконечник стрелы
      }
    }
  }
}
```
"Стрела" значит, что метод делает несколько дел сразу и его нужно разделить на несколько вспомогательных.
```java
"Если вам нужно более трех уровней вложенности, вы все равно запутались, так что исправьте программу" - Линус Торвальдс.
```

 **10. Нарушение правила одной операции**

Этот метод делает две разные вещи: читает слова из файла и печатает сообщение если что-то пошло не так 
```java
List<String> getAllWords() {
  try (FileReader fileReader = new FileReader("dictionary.txt")) {
    return fileReader.readAllLines();  <-- ПЕРВАЯ ОПЕРАЦИЯ
  } catch (Exception _) {
    System.out.println("Проблема с загрузкой словаря из файла");  <-- ВТОРАЯ ОПЕРАЦИЯ
    return Collections.emptyList();  <-- ПЕРВАЯ ОПЕРАЦИЯ
  }
}
```

Из этого метода нужно вынести распечатку и использовать метод вот так:
```java
//БЫЛО:
List<String> allWords = getAllWords();

if (allWords.isEmpty()) {
  return;
}

//СТАНЕТ:
List<String> allWords = getAllWords();

if (allWords.isEmpty()) {
  System.out.println("Проблема с загрузкой словаря из файла");  
  return;
}
```  
*Мартин, "Чистый код", гл.3, "Правило одной операции", "Один уровень абстракции"*

## ВЫВОД

Хороший чистый процедурный код. Алгоритмы в программе простые и понятные, код читается легко.
Виден опыт процедурного программирования.

Подробное объяснение, как делать эту программу в процедурном и ООП стилях, есть у Сергея в расширенных материалах.

n.193(369)  
#ревью #виселица 