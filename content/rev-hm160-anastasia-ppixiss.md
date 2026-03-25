https://github.com/ppixiss/hangman  
[Анастасия]

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Длинные команды
```java
Чтобы начать новую игру, введите (играть); Чтобы покинуть игру, введите (выйти)
играть
```
Короткими командами пользоваться удобднее. Например: 0/1, y/n, да/нет. 

2. Можно ввести не только буквы русского алфавита
```java
Введите букву: Ѧ
Такой буквы в слове нет! Количество ошибок: 1
Использованные буквы [ѧ]

Введите букву: Ѱ
Такой буквы в слове нет! Количество ошибок: 2
Использованные буквы [ѧ, ѱ]

Введите букву: Ӿ
Такой буквы в слове нет! Количество ошибок: 3
Использованные буквы [ѧ, ѱ, ӿ]

Введите букву: Ӛ
Такой буквы в слове нет! Количество ошибок: 4
Использованные буквы [ѧ, ѱ, ӿ, ӛ]

Введите букву: Ӫ
Такой буквы в слове нет! Количество ошибок: 5
Использованные буквы [ѧ, ѱ, ӿ, ӛ, ӫ]
```

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную кириллическую букву
+ 👍 Есть список введенных букв

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Название класса должно быть существительным
```java
class HangmanPrint

//ПРАВИЛЬНО:
class HangmanPrinter
```

- Избыточный контекст
```java
public class HangmanPrint {
  public static void drawHangman(int mistakes) {...}
}

//ПРАВИЛЬНО:
public class HangmanPrinter {
  public static void draw(int mistakes) {...}
}
```

- Названия должны объяснять суть переменных
```java
public static final String YES_RESTART = "да";
public static final String NO_RESTART = "нет";

//ПРАВИЛЬНО:
public static final String START = "да";
public static final String QUIT = "нет";
```

- Венгерская нотация.

В названии переменных не пиши тип данных, к которым они относится.  
И вообще не употребляй венгерскую ноттацию.  
Название переменной должно отвечать на вопрос что хранит переменная, а не как хранит
```java
char[] charArrayWord = word.toCharArray();

//ПРАВИЛЬНО:
char[] wordLetters = word.toCharArray();
```

- Пиши понятные, но лаконичные названия.  
Название "эта буква использована ранее" не добавляет ничего нового к пониманию по сравнению с "это использованная буква"
```java
boolean isLetterUsedEarlier(char letter)

//ЛУЧШЕ:
boolean isUsedLetter(char letter)
```

- Используй стандартные названия.  
Если что-то получаешь через ввод от юзера, обычно это называют "input"
```java
char enterLetter()

//ПРАВИЛЬНО:
char inputLetter()
```

- В названия не нужно вставлять частицы "Of", "The" и т.д.

Это только делает названия более громоздкими.  
Если конечно там "Of" не используется в контексте valueOf()
```java
void printStateOfWord(char[] maskedWord) 
boolean isLetterInWord(char attemptLetter)

//ПРАВИЛЬНО:
void printWordState(char[] maskedWord) 
boolean isWordLetter(char attemptLetter)
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Нарушение инкапсуляции**

Хотя инкапсуляция это термин ООП, даже при процедурном стиле в java используются классы.  
Поэтому в классе Main все поля и методы, кроме `main()` должны быть private.

**3. Если в блоке if есть return(break, continue, throw, exit и т.д.), то else не пишется.**
 
В этом случае неважно, будет else или нет, так как программа будет работать одинаково, а код без `else` будет выглядеть читабельней
```java
if (knownLetters.contains(letter)) {
  System.out.println("Буква " + "'" + letter + "'" + " была введена ранее, введите другую букву");
  return true;
} else {
  knownLetters.add(letter);
}

//ПРАВИЛЬНО:
if (knownLetters.contains(letter)) {
  System.out.println("Буква " + "'" + letter + "'" + " была введена ранее, введите другую букву");
  return true;
} 
knownLetters.add(letter);
```

**4. Форматирование строк**

Если нужно печатать или создавать строку с более, чем одним подстановочным значением или значение вставляется внутрь сообщения, 
используй форматирование- тогда сразу будет виден весь шаблон
```java
System.out.println("Чтобы начать новую игру, введите (" + PLAY + "); Чтобы покинуть игру, введите (" + EXIT + ")");
System.out.println("Буква " + "'" + letter + "'" + " была введена ранее, введите другую букву");

//ПРАВИЛЬНО:
System.out.printf("Чтобы начать новую игру, введите '%s'; Чтобы покинуть игру, введите '%s'  %n", PLAY, EXIT);
System.out.printf("Буква '%c' была введена ранее, введите другую букву  %n", letter);
```

**5. Совмещение команды и запроса, метод делает несколько дел сразу**

Методы не должны совмещать команду и запрос, методы не должны делать несколько дел сразу
```java
public static boolean isLetterUsedEarlier(char letter) {
  if (knownLetters.contains(letter)) {
    System.out.println("Буква " + "'" + letter + "'" + " была введена ранее, введите другую букву");  <-- Выполняет команду
    return true;  <-- Отвечает на запрос
  } else {
    knownLetters.add(letter);  <-- Выполняет команду
    return false;  <-- Отвечает на запрос
  }
}
```

Метод нужно разделить на несколько.  
Один из которых будет просто говорить, была ли эта буква использована ранее:
```java
public static boolean isUsedletter(char letter) {
  return usedLetters.contains(letter));
}
```
*Мартин, "Чистый код", гл.3, "Разделение команд и запросов", "Правило одной операции"*

**6. Вводи вспомогательные методы, делай программу более простой и понятной**

- Сложные условия в if'ах тяжело читаются.  
Вводи поясняющие переменные или вспомогательные методы
```java
if (!Character.isLetter(letter) || Character.UnicodeBlock.of(letter) != Character.UnicodeBlock.CYRILLIC) {...}

//ПРАВИЛЬНО:
if (isCirillicLetter(letter)) {...}

private static boolean isCirillicLetter(char symbol) {
  return !Character.isLetter(letter) || Character.UnicodeBlock.of(letter) != Character.UnicodeBlock.CYRILLIC;
}
```

- В играх обычно нужны методы, которые определяют состояние игры
```java
while (mistakes != MAX_MISTAKES && hasHiddenLetters(maskedWord)) {...}
if (!hasHiddenLetters(maskedWord)) {...}

//ПРАВИЛЬНО:
while (!isGameOver()) {...}
if (isLose()) {...}


private static boolean isGameOver() {
  return isWin() || isLose();
}

private static boolean isLose() {
  return !hasHiddenLetters(maskedWord);
}
```
Даже, если метод состоит из одной строки, но при этом делает код программы читабельней, то этот метод имеет право на существование.

- Метод не должен завершать работу программы через exit()
```java
public static List<String> readFile() {
  //...  
  System.exit(1);
  return null;
}
```
Каждый метод и класс имеют право завершать только свою работу. Например, через return.  
Потому что методы и классы не должны знать логику работу более высоких слоев программы, 
у которых могут быть свои планы на тему того, когда и почему нужно завершать работу программы.

Кроме того, при выходе через `exit()` могут не закрыться некоторые ресурсы программы.

Возможно, если прекращать работу здесь, то это сильно упрощает код. Тогда можно оставить, как есть.  
Но нужно понимать, что с архитектурной точки зрения это хуже и это что-то вроде костыля.

**7. Лишнее преобразование списка в массив**

Список умеет делать все то же, что массив, и даже больше
```java
public static String chooseRandomWord() {
  String[] words = readFile().toArray(new String[0]);
  return words[new Random().nextInt(words.length)];
}

//ПРАВИЛЬНО:
public static String chooseRandomWord() {
  List<String> words = readFile();
  int randomIndex = new Random().nextInt(words.size());
  return words.get(randomIndex);
}
```

**8. Этот метод может брать значение этого поля из окружения**
```java
public static char[] maskedWord;

public static void printStateOfWord(char[] maskedWord) {
  for (char symbol : maskedWord) {
    //печатает maskedWord из входящих аргументов
  }  
}  

//ПРАВИЛЬНО:
public static char[] maskedWord;

public static void printMaskedWord() {
  for (char symbol : maskedWord) {
    //печатает maskedWord из окружения
  }  
}  
```

**9. Метод нужно разделить на два**
```java
void handleGameResults()

//ПРАВИЛЬНО:
void printWinMessage();
void printLoseMessage();
```

**10. class HangmanPrint**

- В любом switch-case должен быть default.  
В данном случае default должен бросать исключение с сообщением, что такого номера картинки не существует.

- Печать картинки виселицы через switch-case или if-elseif - индусский код.  

Картинки нужно хранить в статическом массиве и печатать по номеру.  
Например, так
```java
public final class HangmanPrinter {
  //закрытый конструктор

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

  public static void print(int numPicture) {
    String picture = PICTURES[numPicture];
    System.out.println(picture);
  }
}
```

## ВЫВОД

Для развития навыков процедурной декомпозиции, посмотреть стрим Сергея  
[Крестики-нолики в процедурном стиле](https://www.youtube.com/watch?v=PPikj1qHxrA)

Для процедурного стиля в целом норм.

n.160(312)  
#ревью #симуляция #виселица