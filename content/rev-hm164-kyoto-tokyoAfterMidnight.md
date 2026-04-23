https://github.com/tokyoAfterMidnight/gallows-game  
[kyoto]

Игра в процедурном стиле, выполнена в одном классе.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Нет защиты от дурака.  
Если на этом этапе нажать ентер- программа вылетает
```java
Do you want to [P]lay or [Q]uit?

Exception in thread "main" java.lang.StringIndexOutOfBoundsException: Index 0 out of bounds for length 0
	at java.base/jdk.internal.util.Preconditions$1.apply(Preconditions.java:55)
```

2. Команды не работают
```java
Do you want to [P]lay or [Q]uit?
p
Enter 'P' to play or 'Q' to exit: 
q
Enter 'P' to play or 'Q' to exit: 
```

3. Нет списка введенных букв 

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву английского алфавита

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Если метод называется "сравнить"(check), то его результат должен быть boolean.  
Если его результат не boolean, значит он не должен так называться
```java
char checkPlayerChoice()
```

- Не используй яастицы "And" или "Or" в названии методов.

Это всегда или сигнал о том, что методы делают больше одного дела сразу и нарушают тем самым правило одной опреации.  
Или просто не получилось внятно сформулировать то, что делает этот метод
```java
void checkAndReplaceLetters()
```

Если описывать через название то, что на самом деле делает этот метод, то получится что-то вроде:
```java
void inputLetterAndOpenLetterAndIncMistakes()
```
*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Нарушение инкапсуляции**. 

Хотя проект написан в процедурном стиле, а инкапсуляция это концепция ООП, 
все равно здесь нужно придерживаться основ инкапсуляции.  
Публичным должен быть только метод `main()`.  
Остальные методы и поля здесь должны быть приватными

**3. Форматированние**

- Не вставляй перевод строки внутрь строк при распечатке.

Иначе при чтении кода приходиться мысленно "разворачивать" одну строку в несколько.  
Если нужно распечатать несколько строк, то печатай их или многострочными строками(""") или отдельными инструкциями печати.  
Тогда сразу будет видно, как текст будет выглядеть на печати:
```java
System.out.println("\nRemaining attempts: " + (MAX_MISTAKES - mistakes));

//ПРАВИЛЬНО:
System.out.println();
System.out.println("Remaining attempts: " + (MAX_MISTAKES - mistakes));
```

- Шаблоны.

Если нужно печатать или создавать строку с более, чем одним подстановочным значением или значение вставляется внутрь сообщения, 
используй форматированную печать- тогда сразу будет виден весь шаблон
```java
System.out.println("\nYou lose. The secret word was \"" + secretWord + "\"");

//ПРАВИЛЬНО:
System.out.println();
System.out.printf("You lose. The secret word was \"%s\"  %n", secretWord);
```

Хотя лично я бы в этом случае не парился с двойными кавычками.  
Потому что с ними в консолоьных программах вообще принято не париться.  
И сделал бы вот так:
```java
System.out.println("\nYou lose. The secret word was \"" + secretWord + "\"");

//ПРАВИЛЬНО:
System.out.println();
System.out.printf("You lose. The secret word was '%s'  %n", secretWord);
```

**4. Нарушение DRY**

- Магические буквы, числа, слова. Вводи константы 
```java
System.out.println("Do you want to [P]lay or [Q]uit?");
while (choice.charAt(0) != 'Q' && choice.charAt(0) != 'P') {
  System.out.println("Enter 'P' to play or 'Q' to exit: ");
}
case 'P' -> //...
case 'Q' ->  //...

//ПРАВИЛЬНО:
private final static char PLAY = 'P';
private final static char QUIT = 'Q';

System.out.printf("Do you want to [%c]lay or [%c]uit?  %n", PLAY, QUIT);
while (choice.charAt(0) != QUIT && choice.charAt(0) != PLAY) {
  System.out.printf("Enter '%c' to play or '%c' to exit: %n", PLAY, QUIT);
}
case PLAY -> //...
case QUIT ->  //...
```

- Если константы уже есть- пользуйся ими
```java
private static final char MIN_CHAR = 'a';
private static final char MAX_CHAR = 'z';

System.out.println("Use only English character (a-z, A-Z): ");

//ПРАВИЛЬНО:
System.out.printf("Use only English character (%c-%c): %n", MIN_CHAR, MAX_CHAR);
```
Не нужно уточнять, что буквы (или команды) можно вводить и в большом и в маленьком регистре-
это должно быть понятно по умолчанию. А уточнение `(a-z, A-Z)` просто создает визуальный шум.  
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*

- Дублирование текста, хоть и опосредованное.  
Оба сообщения сообщают одну и ту же информацию
```java
if (!isCharSingle(playerInput)) {
  System.out.println("Enter only ONE character (a-z, A-Z): ");
  continue;
}
char ch = playerInput.charAt(0);
if (!isCharValid(ch)) {
  System.out.println("Use only English character (a-z, A-Z): ");
}

//ПРАВИЛЬНО:
private static final String ENGLISH_LETTER_MESAGE =  "Enter only ONE character (%c-%c): ".formatted( MIN_CHAR, MAX_CHAR);

if (!isCharSingle(playerInput)) {
  System.out.println(ENGLISH_LETTER_MESAGE);
  continue;
}
char ch = playerInput.charAt(0);
if (!isCharValid(ch)) {
  System.out.println(ENGLISH_LETTER_MESAGE);
}
```

**5. Магические цифры, буквы**

Шо такое "5"? Почему это "5" а не "6" или "4"?
```java
do {...} while (word.length() < 5);
```

Объясняй магию через константы:
```java 
do {...} while (word.length() < MIN_WORD_LENGTH);
```

**6. Исключения**

- Проверямые исключения перехватывай в точке их возникновения и заменяй на непроверяемые. 

Проброс исключений в сигнатуре методов захламляет код.  
Поэтому заменяй проверяемые исключения на непроверяемые.  
Изучи различия проверяемых и непроверяемых исключений 
```java
private static void loadWords() throws IOException {
  words = Files.readAllLines(Paths.get("words.txt"));
}

//ПРАВИЛЬНО:

private static void loadWords() {
  try {
    words = Files.readAllLines(Paths.get("words.txt"));
  } catch (IOException e) {
    throw new КакоетоНепроверяемоеИсключение(e);
  }
}
```
*"ЧК", гл.7, "...проверяемые исключения"*

- Не бросай исключения в космос.

Метод main- последняя точка в программе, после которой исключение аварийно закрывает работу программы.  
Если ты знаешь, что в программе могут возникнуть исключения, то обработай их внутри программы
```java
public static void main(String[] args) throws IOException
```

В данном случае нужно:  
🔹 Перехватить исключение в main  
🔹 Написать юзеру сообщение о том, что не удалось прочитать файл со свловами  
🔹 Корректно закрыть программу без вываливаея красных иксепшенов в консоль

**7. Распечатка картинок состояния виселицы**

- Сейчас алгоритм распечатки картинок совершенно непонятный и неочевидный:
```java
String head = (mistakes >= 1) ? "           O" : "";
String body = (mistakes >= 2) ? "           │" : "";
String arms;
if (mistakes >= 4) {
  arms = "          /│\\";
} 
//дальше- еще страшнее
```
Вносить изменения в такой алгоритм тяжело.  
Даже понять из кода, что в итоге распечатестя- и то тяжело.

Понятно желание оптимизировать хранение картинок и все слайды сгруппировать в максимально сжатый объем. 
Но здесь это делает код значительно более худшим для понимания.  

Для такой простой игры приоритет- читаемость кода, а не оптимизация хранения картинок в памяти.

Поэтому каждый слайд нужно хранить в виде отдельной картинки.  
Например, в виде массива
```java
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

void print(int picNumber) {
  String picture = PICTURES[picNumber];
  System.out.println(picture);
}
```
Желательно картинки и метод распечатки вынести в отдельный класс, чтобы в основном классе не смешивать игровую логику и графику.  
Процедурный стиль позволяет делать проекты, состоящие из нескольких классов.  
В этом случае классы интерпритируются не как объекты ООП, а как процедурные модули.

**8. Побочный эффект**

Судя по названию и сигнатуре, метод должен только получить букву от юзера.  
Но кроме этого метод заносит букву в коллекцию использованных букв- это побочный эффект 
```java
public static char enterPlayerLetter() {  <-- Контракт обещает получить букву от юзера
  while (true) {
    //Принимает букву от юзера
    usedLetters.add(ch); <-- Побочный эффект
    return ch;  <-- Возвращает букву
  }
}
```
Этот метод должен только получить от юзера букву.   
*Мартин "Чистый код", гл.3, "Избавьтесь от побочных эффектов"*  
*Фаулер "Рефакторинг", гл.6, "Извлечение метода"*  

**9. Правило одной операции**

Этот метод делает несколько разных операций на *одном уровне абстракции*. 
Каждый метод должен выполнять только одну операцию
```java
void checkAndReplaceLetters()
```

Сейчас этот метод выполняет такие операции:  
🔹 Принимает буквы от юзера (побочный еффект- в методе приема добавляет букву в использованные)
🔹 Открывает букву в слове
🔹 Учеличивает счетчик ошибок

Метод нужно разделить на несколько, каждый из которых будет делать что-то одно.  
*Мартин, "Чистый код", гл.3, "Правило одной операции", "Один уровень абстракции"*

**10. Создавай вспомогательные методы, делай программу более простой и понятной**

Вот такие конструкции нечитаемы впринципе.  
Тут в одну строку  сведены четыре операции, а может быть и все восемь, смотря как считать:
```java
if (!String.valueOf(maskedWord).equals(secretWord) && mistakes != MAX_MISTAKES) {...}
```

Свои намерения объясняй через вспомогательные методы:
```java
public static void playGame() {
  while (!String.valueOf(maskedWord).equals(secretWord) && mistakes != MAX_MISTAKES) {
    //...
  }
  if (mistakes == MAX_MISTAKES) {
    //...
  } 
}

//ПРАВИЛЬНО:
public static void playGame() {
  while (!isGameOver()) {
    //...
  }
  if (isLose()) {
    //...
  } 
}

private static boolean isWin() {...}
private static boolean isLose() {...}

private static boolean isGameOver() {
  return isWin() || isLose();  
}
```
Даже если метод состоит из одной строки, но при этом делает код программы читабельней, то этот метод имеет право на существование.

## ВЫВОД

В программе в основном все методы достаточно хороши, они маленькие и понятные.  
Но несколько из них нарушают правило одной операции и имеют побочные эффекты.

Для развития навыков процедурной декомпозиции, посмотреть стрим Сергея  
[Крестики-нолики в процедурном стиле](https://www.youtube.com/watch?v=PPikj1qHxrA)

Подробное объяснение, как делать эту программу в процедурном и ООП стилях, есть у Сергея в расширенных материалах.

В целом для процедурного стиля приемлемо.

n.164(319)  
#ревью #виселица 