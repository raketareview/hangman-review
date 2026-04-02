https://github.com/abdikadyr1/Hangman  
[AMIRE]

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Неправильная последовательность в порядке отображения введенных букв
```
Write your letter:
z
Used letters: [z]

Write your letter:
n
Used letters: [z, n]

Write your letter:
b
Used letters: [b, z, n]
```
Введенные буквы должны размещаться по алфавиту(b, n, z) либо в порядке ввода(z, n, b).

2. Лишние неудобства для пользователя.  
Если программе нужны буквы определенного регистра, то она сама должна переводить буквы в этот регистр, а не создавать юзеру сложности.
```java
Write your letter:
A
Invalid input, letter should be in english and lowercase.
```

3. Можно ввести не одну букву, а набор букв и это будет считаться допустимым вводом
```
Used letters: [b, r, z, n]
Write your letter:
abracadabra
THE WORD: a****
Tries left: 2

  +---+
  |   |
  O   |
 /|\  |
      |
      |
=========

Used letters: [a, b, r, z, n]
```

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только буквы определенного алфавита
+ 👍 Есть список введенных букв

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Не сокращай названия без особой необходимости.  
Сокращение на две буквы здесь ничего не дает
```java
private Random rand = new Random();

//ПРАВИЛЬНО:
private Random random = new Random();
```

- Если метод называется "сравнить"(check), то его результат должен быть boolean. Если его результат не boolean, значит он не должен так называться
```java
void checkGameState(String word, String hiddenWord, int tries)
```
*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Нарушение конвенции кода**

- Каждая переменная должна объявляться в отдельной строке
```java
int tries = 6, miss = 0;

//ПРАВИЛЬНО:
int tries = 6;
int miss = 0;
```

**3. Нарушение DRY**, магические буквы, числа, слова. Вводи константы 
```java
System.out.println("""
  1. Start new game.
  2. Exit
  """);

if (choice == 1) {...}
if (choice == 2) {...}

//ПРАВИЛЬНО:
private final static int START = 1;
private final static int QUIT = 2; 

System.out.printf("""
  %d. Start new game.
  %d. Exit
  """, START, QUIT);

if (choice == START) {...}
if (choice == QUIT) {...}
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*  

**4. Нарушение DRY**, магические буквы, числа, слова. Вводи константы. А если они уже есть- пользуйся
```java

//ПРАВИЛЬНО:
private final static 
private final static 
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*

**5. Не делай переводы строк в println**

Если нуно распечатать несколько строк, печатай их или многострочными строками или отдельными инструкциями построчной печати
```java
System.out.println("The word was: " + word + "\n" + "\n");

//ПРАВИЛЬНО:
System.out.println("The word was: " + word);
System.out.println();
System.out.println();
```
Приоритет- простой и читаемый код.  
Если инструкцию печати приходится в голове "разворачивать" из одномерной надписи в двумерную, 
то лучше сразу напечатать несколько строк.

**6. Нарушение инкапсуляции**

Хотя "инкапсуляция" это термин ООП, но даже при процедурном стиле в java используются классы.  
В классах нужно соблюдать принцип минимального открытого интерфейса.  
В классе Main все поля и методы должны быть приватными, кроме точки входа main.  
*Вайсфельд "Объектно-ориентированный подход", гл.5, "Минимальный открытый интерфейс"*

**7. Обращайся к char'у, а не к его номеру в таблице ASCII**
```java
public static boolean isValidLetter(String letter) {
  return (letter.charAt(0) >= 97 && letter.charAt(0) <= 122);
}

//ПРАВИЛЬНО:
char symbol = letter.charAt(0);
return (symbol >= 'a' && symbol <= 'z');
```

**8. Используй StringBuilder**

Плюсование строк в цикле это расточительно- постоянно пересоздаются объекты.  
Для складывания строк в цикле применяй StringBuilder- это с точки зрения ресурсов более оптимальный процесс.  
Посмотри, idea подчеркивает здесь плюсование желтым цветом, это она предлагает заменить складывание строк использованием стрингбилдера.  
Поставь курсор на желтое подчеркивание, слева появится желтая лампочка, клацни на нее, выбери пункт "Convert..." и идея автоматически заменит плюсование на StringBuilder
```java
String hiddenWord = "";

for (int i = 0; i < wordLength; i++) {
  hiddenWord += "*";
}

//ПРАВИЛЬНО:
StringBuilder hiddenWord = new StringBuilder();

for (int i = 0; i < wordLength; i++) {
  hiddenWord.append("*");
}
```

Хотя конкретно в этом случае все можно сделать значительно проще вот так
```java
String hiddenWord = "*".repeat(wordLength);
```

**9. Используй поясняющие переменные**

```java
if (inputLetter.charAt(0) == word.charAt(i)) {
  sb.setCharAt(i, inputLetter.charAt(0));
  //...
}

if (!containsLetter && !usedLetters.contains(inputLetter.charAt(0))) {...}

usedLetters.add(inputLetter.charAt(0));

//ПРАВИЛЬНО:
char letter = inputLetter.charAt(0);

if (letter == word.charAt(i)) {
  sb.setCharAt(i, letter);
  //...
}

if (!containsLetter && !usedLetters.contains(letter)) {...}

usedLetters.add(letter);
```
*Фаулер, "Рефакторинг", гл.6, "Введение поясняющей переменной"*

**10. Создавай вспомогательные методы, делай программу более простой и понятной**
```java
if (!containsLetter && !usedLetters.contains(inputLetter.charAt(0))) {...}

//ПРАВИЛЬНО:
if (isНазваниеКотороеВсеОбъясняет(...)) {...}

private boolean isНазваниеКотороеВсеОбъясняет(...) {
  return !containsLetter && !usedLetters.contains(inputLetter.charAt(0));
}
```
Даже, если метод состоит из одной строки, но при этом делает код программы читабельней, то этот метод имеет право на существование.

**11. Большие методы и правило одной операции**

Каждый метод должен выполнять только одну операцию на рдном уровне абстракции.  
Этот метод делает много разного на одном уровне абстракции
```java
public static void startGameLoop(Scanner scanner)
```

Метод состоит из большого числа обособленных блоков кода, 
каждый из которых и есть той самой отдельной операцией на одном уровне абстракции.

Такие обособленные блоки выявляй и выноси во вспомогательные методы.

Например рассмотрим большой божественный метод, который делает много разного:
```java
public static void startGameLoop(Scanner scanner) {
  //миллион строк кода
  for (int i = 0; i < wordLength; i++) {
    if (inputLetter.charAt(0) == word.charAt(i)) {
      sb.setCharAt(i, inputLetter.charAt(0));
      hiddenWord = sb.toString();

      containsLetter = true;
    }
  }
  //ещё миллион строк кода
}
```

Что здесь происходит?  
Это мы проверяем вхождение буквы inputLetter.charAt(0) в слово и одновременно открываем его.  
Само по себе проверить наличие буквы в слове и открыть букву в слове это две разные операции.  
Они должны находиться в отдельный методах:
```java
private static String word;

public static void startGameLoop(Scanner scanner) {
  //миллион строк кода
  char letter = inputLetter.charAt(0);
  if(isWordLetter(letter)) {
    openLetter(letter);
    //другие действия если буква есть в лове 
  }
  //ещё миллион строк кода
}

private static boolean isWordLetter(char letter) {
  // true если letter есть в word
}

private static void openLetter(char letter) {
  // открывает letter в word
  // если попытались открыть букву которой нет- бросает исключение
}
```
Если методы будут соответствовать правилу одной операции, то код будет читаться как книга.  
*Мартин, "Чистый код", гл.3, "Правило одной операции", "Один уровень абстракции"*

**12. Использование исключений**

- Исключение это такой же класс, как и все.  
Не нужно к нему прописывать полный путь
```java
try {
  //...
} catch (java.util.InputMismatchException e) {...}

//ПРАВИЛЬНО:
try {
  //...
} catch (InputMismatchException e) {...}
```

- Не используй try-catch для организации ветвлений в бизнес логике.  
Участки с try-catch изолируй во вспомогательных методах
```java
while(...) {
  try {
    int choice = scanner.nextInt();
    //миллион строк бизнес логики
  } catch (java.util.InputMismatchException e) {
    System.out.println("An invalid choice, try again! ");
    scanner.nextLine();
  }
}

//ПРАВИЛЬНО:
while(...) {
  String s = scanner.nextLine();
  if(!isNumber(s)) {
    System.out.println("An invalid choice, try again! ");
    continue;
  }
  int choice = Integer.parseInt(s);
  //миллион строк бизнес логики
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
*"ЧК", гл.3*  
*Хорстманн "Java. Библиотека профессионала", т1., гл.11*
```java
"Обработка исключений не может заменить собой простую проверку" - Хорстманн
```

**13. Распечатка виселицы**

- В любом switch-case должен быть default.  
В данном случае default должен бросать исключение с сообщением, что такого номера картинки не существует.

- Печать картинки виселицы через switch-case или if-elseif - индусский код.

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

  public static void render(int numPicture) {  
    System.out.println(PICTURES[numPicture]);
  }
}
```
Желательно картинки и метод распечатки вынести в отдельный класс, чтобы в основном классе не смешивать логику и графику.  
Процедурный стиль дозволяет делать проекты, состоящие из нескольких классов.

**14. class Words**

- Слова берутся не из файла, а просто из списка в памяти.

В этом проекте нужно читать слова из файла.  
Сейчас слова не читаются из файла, они хранятся тупо в списке.  
Не делай этот простой проект еще более примитивным, сделай файловое чтение.

Все равно программист должен иметь понимание об основах работы с файлами.  
Первый проект как раз хорош для этого.

- Избыточный джененрик
```java
List<String> words = List.<String>of(...)

//ПРАВИЛЬНО:
List<String> words = List.of(...)
```
Смотри за подсказками Intellij Idea- если она что-то подчеркивает или наоборот пишет серым, как в этом случае,
значит она пытается на что-то обратить внимание.

В данном случае нужно поставить курсор на серую надпись "String",
появится лампочка левее-выше, клацнуть по ней и увидим предложение идеи по улучшению кода: "Remove type argumens".

- Никогда не делай видимость дефолтной.  

Всегда указывай конкретную область видимости.
В данном случае это должно быть private
```java
private Random rand = new Random();
List<String> words = List.<String>of(...);
int randomIndex;

//ПРАВИЛЬНО:
private Random rand = new Random();
private List<String> words = List.<String>of(...);
private int randomIndex;
```

- Переменная используется только в одном методе, делать ее полем класса не имеет смысла
```java
private String randomWord;

public String getRandomWord() {
  randomIndex = rand.nextInt(words.size());
  randomWord = words.get(randomIndex);
  return randomWord;
}

//ПРАВИЛЬНО:
public String getRandomWord() {
  randomIndex = rand.nextInt(words.size());
  return words.get(randomIndex);
}
```

## ВЫВОД

В процедурном стиле программирования нужно научиться делать маленькие методы, 
каждый из которых будет делать только одно дело.

Для развития навыков процедурной декомпозиции, посмотреть стрим Сергея  
[Крестики-нолики в процедурном стиле](https://www.youtube.com/watch?v=PPikj1qHxrA)

Подробное объяснение, как делать эту программу в процедурном и ООП стилях, есть у Сергея в расширенных материалах.

n.161(314)  
#ревью #виселица 