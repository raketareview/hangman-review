https://github.com/rasulovtolib/hangman.git  
[Толиб Росулов]

Игра в процедурном стиле, выполнена в двух классах.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Игра не запускается
```java
Press: N to new game
Press: E to exit
n
Exception in thread "main" java.io.IOException: File not found
	at Main.readFileWords(Main.java:100)
	at Main.main(Main.java:22)
```

2. Можно ввести не только русскую букву, но и буквы других алфавитов
```java
Guessed wrong letters: [Ц, 魚, Q]
```

Хотя планируется получать только русские буквы:
```java
Please enter Cyrillic alphabet
```

3. Можно ввести не одну букву, а набор букв и это будет считаться допустимым вводом
```java
Enter the letter: 
dfdfgdfg

Guessed wrong letters: [Z, N, B, D]
```

4. Соблюдай грамматику
```java
Please enter Cyrillic alphabet

//ПРАВИЛЬНО:
Please enter a Cyrillic letter
```

## ХОРОШО

+ 👍 Есть список введенных букв

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Название должно объяснять, что делает метод.  

Название "применить угаданную букву" ничего не объясняет. Куда её применить и как?  
Название просто указывает на какие-то действия с буквой, а должно описывать конкретное действие.  
Этот метод открывает букву в слове(маске) 
```java
boolean applyGuessedLetter(char[] secretWord, char guessedLetter, char[] secretWordMask) 

//ПРАВИЛЬНО:
boolean openLetter(char[] secretWord, char guessedLetter, char[] secretWordMask) 
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Нарушение DRY**, магические буквы, числа, слова. Вводи константы
```java
System.out.println("Press: N to new game");
System.out.println("Press: E to exit");
if (input.equals("E")) {...}

//ПРАВИЛЬНО:
private static final String START = "N";
private static final String QUIT = "E";

System.out.printf("Press: %s to new game  %n", START);
System.out.printf("Press: %s to exit  %s", QUIT);
if (input.equals(QUIT)) {...}
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*

- Придерживайся единообразия.

Или "Printer" и "print", или "Renderer" и "render"
```java
class HangmanRenderer {
  public static void printHangman(int attempts) {...}
}

//ПРАВИЛЬНО:
class HangmanRenderer {
  public static void render(int attempts) {...}
}
```

**3. Исключения**

- Сообщения в исключениях делай не формальными, а информативными.

В сообщении нужно указать, что не найден конкретный файл
```java
throw new IOException("File not found");

//ПРАВИЛЬНО:
throw new IOException("File not found: " + абсолютныйПутьКфайлу);
```

- Проверяемые исключения перехватывай в точке их возникновения и заменяй на непроверяемые. 

Проброс исключений в сигнатуре методов захламляет код.  
Поэтому заменяй проверяемые исключения на непроверяемые  
```java
private static List<String> readFileWords(String file) throws IOException {
  //...
  try (BufferedReader reader = new BufferedReader(new FileReader(file))) {...}
  catch (IOException e) {
    throw new IOException("File not found");
  }
  return words;
}

//ПРАВИЛЬНО:
private static List<String> readFileWords(String file) {
  //...
  try (BufferedReader reader = new BufferedReader(new FileReader(file))) {...}
   catch (IOException e) {
    throw new КакоетоНепроверяемоеИсключение(...);
  }
  return words;
}
```
Изучи различия проверяемых и непроверяемых исключений  
*"ЧК", гл.7, "...проверяемые исключения"*

- Не бросай исключения в космос.

Метод main - последняя точка в программе, после которой исключение аварийно закрывает работу программы.  
Если ты знаешь, что в программе могут возникнуть исключения, то обработай их внутри программы,
а не бросай дальше в космос
```java
public static void main(String[] args) throws Exception {...}  <-- Исключение летит в космос

//ПРАВИЛЬНО:
public static void main(String[] args) {...}  <-- Где-то внутри программы происходит перехват и обработка исключений
```

**4. Печать картинок**

+ 👍 Хорошо, что картинки и метод их печати вынесены в отдельный класс HangmanRenderer.  
Это разгружает классы с логикой от графики.

- Неправильная реакция на ошибки.

Есть три основных реакции на ошибки: игнорирование, исправление, падение.  
Здесь выбрано игнорирование- при получении некорректного номера картинки, метод просто ничего не делает
```java
public static void printHangman(int attempts) {
  if (attempts >= 0 && attempts <= HANGMAN_STATES.length) {
    System.out.println(HANGMAN_STATES[attempts]);
  }
}
```

Правильная реакция должна быть другой, нужно сделать падение:
```java
public static void printHangman(int attempts) {
  System.out.println(HANGMAN_STATES[attempts]);  <-- КИНЕТ ИСКЛЮЧЕНИЕ, ЕСЛИ КАРТИНКИ С ТАКИМ НОМЕРОМ НЕТ В МАССИВЕ
}
```
Потому что запрос картинки с неправильным номером означает, что в алгоритме игры есть баг.  
Правильно работающий алгоритм не будет пытаться распечатать картинку с несуществующим номером.

Этот баг не нужно маскировать- это приведет только к тому, что игра будет происходить некорректно.  
Баг нужно максимально быстро выявить и устранить.

**5. Нарушение принципа разделения команд и запросов**

Этот метод совмещает команду и запрос.  
Команда: произвести некие действия.  
Запрос: сказать, что произошло в результате произведенных действий
```java
private static boolean applyGuessedLetter(char[] secretWord, char guessedLetter, char[] secretWordMask) {
  //Окрывает букву в слове- команда
  //Сообщает, была ли открыта буква в слове- запрос  
}
```
Метод должен или выполнять команду, или отвечать на запрос.

*Мартин, "Чистый код", гл.3, "Разделение команд и запросов"* 
```java
"Либо функция изменяет состояние объекта, либо возвращает информацию об этом объекте. 
Совмещение двух операций часто создает путаницу." - Мартин.
```

**6. Если в блоке if есть return(break, continue, throw, exit и т.д.), то else не пишется**
```java
if (input.equals("E")) {
  break;
} else {
  System.out.println("Incorrect input");
}

//ПРАВИЛЬНО:
if (input.equals("E")) {
  break;
} 
System.out.println("Incorrect input");
```

**7. Не делай сложных условий в if'ах, это плохо читается**

Здесь условие очень трудно понять, сложные условия объясняй через поясняющие переменные или вспомогательные методы 
```java
if ((!found) && !guessedWrongLetters.contains(guessedLetter)) {...}
```

Поясняющая переменная:
```java
boolean isНазваниеКотороеВсеОбъясняет = !found && !guessedWrongLetters.contains(guessedLetter);
if (isНазваниеКотороеВсеОбъясняет) {...}
```

Вспомогательный метод:
```java
if (isНазваниеКотороеВсеОбъясняет(....)) {...}

private static boolean isНазваниеКотороеВсеОбъясняет(....) {...}
```
*Фаулер, "Рефакторинг", гл.6, "Введение поясняющей переменной"*

**8. Используй стандартные решения** 

Это игра, а в каждой игре нужно постоянно проверять состояние на проигрыш и выигрыш.  
Для этого делай вспомогательные методы.  
Например, так:
```java
while (!gameOver) {
  //процесс игры

  if (Arrays.equals(secretWordMask, secretWord)) {
    //выиграл
  }
  if (attemptsLeft == 0) {
    //проиграл
  }
}

//ЛУЧШЕ:
private static char[] secretWordMask;
private static char[] secretWord;
private static int attemptsLeft;
//...

while (!gameOver) {
  //процесс игры

  if (isWin()) {
    //выиграл
  }
  if (isLose()) {
    //проиграл
  }
}

//...
private static boolean isLose() {
  return attemptsLeft == 0;
}
```

**9. Божественный метод**

Ацки длинный метод main, который делает много разного- 70 строк.
Такие методы почти нечитаемы, а этот особенно- в нем сложный код.  
Делай маленькие методы, каждый из которых должен делать одно дело на одном уровне абстракции.

*"ЧК", гл.3, "Правило одной операции", "Один уровень абстракции"*

**10. Стрела**

Не должно быть больше 2-3 уровней вложенности.  
Если больше, это антипаттерн "Стрела", такой код очень труден для понимания
```java
while (true) {
  if (input.equals("N")) {
    while (!gameOver) {
      if ((!found) && !guessedWrongLetters.contains(guessedLetter)) {
        //наконечник стрелы
      }
    }
  }
}
```
Стрела значит, что метод делает несколько дел сразу и его нужно разделить на несколько вспомогательных.
```java
"Если вам нужно более трех уровней вложенности, вы все равно запутались, так что исправьте программу" - Линус Торвальдс.
```

## ВЫВОД

В процедурном стиле нужно научиться делать методы, которые будут соответствовать таким требованиям:
+ Маленький размер
+ Выполняют одну операцию на одном уровне абстракции
+ Не совмещают команду и запрос
+ Не содержат больше трех уровней вложенности

Пример хороших методов в проекте: readLetter(), printMask().  
Пример плохого метода: main().

Про методы читай третью главу "Чистого кода".

Для развития навыков процедурной декомпозиции, посмотреть стрим Сергея  
[Крестики-нолики в процедурном стиле](https://www.youtube.com/watch?v=PPikj1qHxrA)

Посмотри на ютубе ролики Немчинского для новичков:
```
Правильные методы по Clean Code
Как называть переменные, методы и классы? Чистый код (Clean Code)
```

Подробное объяснение, как делать эту программу в процедурном и ООП стилях, есть у Сергея в расширенных материалах.

n.167(324)  
#ревью #виселица 