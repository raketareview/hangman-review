https://github.com/AlexSerSla/Hangman  
[Aleksandr Slavkin]

Проект выполнен в процедурном стиле, в одном классе. Есть много над чем поработать, особенно над неймингом.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Зачем юзера каждый раз мучить такими длинными командами? Сделай команды длиной в 1 символ
```
Введите <start> для начала новой игры, введите <exit> для выхода
```

2. Можно ввести не только русскую букву, но и буквы других алфавитов
```
Введите букву
魚
Вы ввели: Й Q 魚 
```

3. Можно ввести не одну букву, а набор букв и это будет считаться допустимым вводом
```
Введите букву
hello
Вы ввели: H 
```
## ХОРОШО

+ 👍 Игра запускается
+ 👍 Есть список введенных букв

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Метод создает словарь
```
void readDictionaryInList()

//ПРАВИЛЬНО:
void createDictionary()  //или initDictionary()
```

- В названии переменных не пиши тип данных, к которым они относится. 
И вообще не употребляй венгерскую ноттацию.  
Название переменной должно отвечать на вопрос что хранит переменная, а не как хранит
```
static List<String> dictionaryList = new ArrayList<>();
static List<Character> usedLettersList = new ArrayList<>();

//ПРАВИЛЬНО:
static List<String> dictionary = new ArrayList<>();
static List<Character> usedLetters = new ArrayList<>();
```

- Избыточный контекст
```
boolean checkWinInGame(List<Character> displayWordList)

//ПРАВИЛЬНО:
boolean isWin(List<Character> displayWordList)
```

- Состояние здесь это `lostState[numOfDraw]`, а `state` это линия, которую нужно напечатать 
```
for (String state : lostState[numOfDraw]) {
  System.out.println(state);
}

//ПРАВИЛЬНО:
for (String line : lostState[numOfDraw]) {
  System.out.println(line);
}
```

- Это не потеряное состояние, это картинки хангмана
```
static String[][] lostState = {
  {
    "┌───┐",
    "│",
    "│",
    "│",
    "╘═════"
  },
  //oth's
};

//ПРАВИЛЬНО:
static String[][] hangmanPictures = {...};
```

- Придумывай понятные, но лаконичные названия
```
boolean checkInputLetterUsed(char inputLetter)

//ЛАКОНИЧНЕЕ:
boolean isUsedLetter(char letter)
```

- Трудно представить, как символ(char) может быть в букве. Учитывая то, что буква это и есть char, а оба они,- буква и чар,- занимают слишком мало места в простанстве, чтобы в них кто-то внедрился.
Возможно, здесь имелся ввиду не предлог "в"(in), а какое-то сокращение, но тогда это сокращение тут не читается
```
char inLetter <-- БОЛЬШЕ ВОПРОСОВ ЧЕМ ОТВЕТОВ

//ПРАВИЛЬНО:
char letter
```
Понятно, что здесь я занудствую и я догадался, что ты имел ввиду "inputLetter"(ты ведь это имел ввиду, правда?). Но читая код я не хочу все время догадываться, я хочу читать понятные названия.

- По конвенции тут нужно писать `has`- имеет, содержит 
```
boolean haveLetter = checkLetterInWord(hiddenWordList, inputLetter);

//ПРАВИЛЬНО:
boolean hasLetter = checkLetterInWord(hiddenWordList, inputLetter);
```

-  Почему метод получает букву именно от юзера, программа может получать ее от чего-то другого? 
В контексте получения буквы от пользователя через клавиатурный ввод- лучше не "get", а "input" 
```
char getUserLetter()

//ЛУЧШЕ:
char inputLetter()
```

- Название обманывает. Метод не проверяет потерянного в игре
```
boolean checkLostInGame(int numOfErrors)
```

- Два метода с фактически одинаковыми названиями. Судя по названию, в игре есть две концепции: игрок(player) и пользователь(user). И получать букву можно и от одного и от другого
```
public static char getPlayerLetter()
public static char getUserLetter()
```
На самом деле в игре нет отдельно юзера и пользователя, а есть плохой нейминг методов.

- Избыточный контекст
```
char inputLetter = getPlayerLetter();

//ПРАВИЛЬНО:
char letter = getPlayerLetter();
```

- Название обманывает. Обещает распечатать "HiddenWord", то есть исходное слово. А распечатывает "displayWord", то есть текущую маску этого слова
```
void printHiddenWord(List<Character> displayWordList)
```
*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*  

**2. Нарушение инкапсуляции**. Публичным должен быть только метод `main()`. Остальные методы и поля здесь должны быть приватными.

**3. Нарушение инкапсуляции**. Всегда явно указывай область видимости и `final`, если переменная final
```
static List<String> dictionaryList = new ArrayList<>();
static List<Character> usedLettersList = new ArrayList<>();

//ПРАВИЛЬНО:
private final static List<String> dictionaryList = new ArrayList<>();
private final static List<Character> usedLettersList = new ArrayList<>();
```

**4. Нарушение DRY**, магические буквы, числа, слова. Вводи константы
```
System.out.println("Введите <start> для начала новой игры, введите <exit> для выхода");
String inputWord = scanner.nextLine().toUpperCase();
if (inputWord.equals("START")) {...}
if (inputWord.equals("EXIT")) {...}

//ПРАВИЛЬНО:
private final static String START = "start;
private final static String EXIT = "exit;

System.out.printf("Введите <%s> для начала новой игры, введите <%s> для выхода \n", START, EXIT);
String inputWord = scanner.nextLine().toLowerCase();
if (inputWord.equals(START)) {...}
if (inputWord.equals(EXIT)) {...}
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*  

**5. Еще магия**

Эти две семерки между судьбы как-то связаны? Или это просто две несвязанные между собой величины, по случайному совпадению равные семи?  
Если да- вводи константу, если нет- вводи константу
```
private static int numOfErrors = 7;

public static void drawHangman(int numOfErrors) {
  int numOfDraw = (7 - numOfErrors) - 1;
  //...
}
```

**6. Если нужно печатать или создавать строку** с более, чем одним подстановочным значением или значение вставляется внутрь сообщения, используй форматированный вывод- тогда сразу будет виден весь шаблон
```
System.out.println("Вы ввели: " + inputLetter + ". Необходимо ввести букву.");

//ПРАВИЛЬНО:
System.out.printf("Вы ввели: %s. Необходимо ввести букву. \n", inputLetter);
```

**7. Если в блоке if есть return**(break, continue, throw, exit и т.д.), то else не пишется.  
В этом случае неважно, будет else или нет, так как программа будет работать одинаково и код без `else` будет выглядеть читабельней
```
if (inputWord.equals("START")) {
  return true;
} else if (inputWord.equals("EXIT")) {
  return false;
}

//ПРАВИЛЬНО:
if (inputWord.equals("START")) {
  return true;
} 
if (inputWord.equals("EXIT")) {
  return false;
}
```

**8. Бери значения полей из окружения**

Если переменная существует в виде поля класса, то бери значение этой переменной из окружения, а не принимай во входящие аргументы метода
```
static List<Character> usedLettersList = new ArrayList<>();

public static void printUsedLetters(List<Character> usedLettersList) {
  for (char letter : usedLettersList) {
    System.out.print(letter + " ");
  }
}

//ПРАВИЛЬНО:
static List<Character> usedLettersList = new ArrayList<>();

public static void printUsedLetters() {
  for (char letter : usedLettersList) {
    System.out.print(letter + " ");
  }
}
```
То же самое касается и других методов, например, `void printHiddenWord(List<Character> displayWordList)`

**9. Избыточно**
```
public static boolean checkInputLetterUsed(char inputLetter) {
  for (char usedLetter : usedLettersList) {
    if (inputLetter == usedLetter) {
      return true;
    }
  }
  return false;
}

//ВСЕ ПРОЩЕ:
public static boolean checkInputLetterUsed(char inputLetter) {
  return usedLettersList.contains(inputLetter);
}
```

**10. В последних версиях явы добавили удобные методы**
```
return usedLettersList.get(usedLettersList.size() - 1);

//ВЕРНУТЬ ПОСЛЕДНИЙ ЭЛЕМЕНТ СЕЙЧАС СТАЛО УДОБНЕЕ:
return usedLettersList.getLast();
```

**11. Неправильная последовательность действий**

В программе сначала читается файл со словами и создается словарь, а потом происходит диалог "играть или выйти?"
```
public static void main(String[] args) {
  readDictionaryInList();  <-- СОЗДАЕТ СЛОВАРЬ
  controlGame(); <-- СПРАШИВАЕТ, А НАДО ЛИ ИГРАТЬ
}
```
Таким образом, если юзер не захочет играть, а выйдет из игры, то файл со словами создавался зря.

**12. Пользуйся классом Random**
```
int randomNumberWord = getRandomNuber(0, dictionaryList.size() - 1);

public static int getRandomNuber(int minNumber, int maxNumber) {
  maxNumber -= minNumber;
  return (int) (Math.random() * ++maxNumber) + minNumber;
}

//ПРАВИЛЬНО:
Random random = new Random();
int randomNumberWord = random.nextInt(0, dictionaryList.size() - 1);
```

**13. Избавься от падежей**

В игре реализовано правильное написание информации с учетом падежей: ошибка, ошибок, ошибки
```
Осталось 5 ошибок.
Осталось 2 ошибки.
Осталась 1 ошибка.
```
Этим занимается специальный алгоритм
```
case 6, 5:
  System.out.println("Осталось " + numOfErrors + " ошибок.");
  break;
case 4, 3, 2:
  System.out.println("Осталось " + numOfErrors + " ошибки.");
  break;
case 1:
  System.out.println("Осталась " + numOfErrors + " ошибка.");
```
Все это- лишняя трата усилий и ненужное усложнение, делающее код более громозким. 
Просто подбери формулировку, которая позволит обходиться без падежей
```
Осталось ошибок: 5
Осталось ошибок: 2
Осталось ошибок: 1
```

**14. Реакция на ошибки**

В проекте предусмотрено, что может возникнуть исключение при чтени файла со словами.  
В этом случае проект просто перехватывает проверяемое исключение и печатает сообщение об ошибке чтения файла
```
try {
  //...
} catch (IOException e) {
  System.err.println("Ошибка чтения файла: " + e.getMessage());
}
```
В данном случае такой способ реагирования на ошибку неправилен. 
Потому что если файл действительно будет отсутствовать, то программа сначала будет глючить, а потом все равно аварийно прекратит свою работу. 
И напишет в консоль непонятные для пользователя сообщения
```
Введите <start> для начала новой игры, введите <exit> для выхода
Ошибка чтения файла: _src\dictionary.txt (Системе не удается найти указанный путь)
тишина в консоли- и что дальше? это типа я ввел в консоль
Введите <start> для начала новой игры, введите <exit> для выхода
start
Exception in thread "main" java.lang.IndexOutOfBoundsException: Index 0 out of bounds for length 0
	at java.base/jdk.internal.util.Preconditions.outOfBounds(Preconditions.java:100)
	at java.base/jdk.internal.util.Preconditions.outOfBoundsCheckIndex(Preconditions.java:106)
```
Здесь должна быть другая реакция на отсутствие файла со словами.  
Нужно сказать юзеру, что файл со словами по указанному пути открыть не удалось и поэтому работа программы будет завершена. 
И корректно завершить работу программы без вываливания красных эксепшенов в консоль.

**15. Побочные эффекты**

Побочные эффекты, метод делает два действия одновременно. 
Этот метод должен только определить состояние выигрыша- что следует из контракта, заданного его названием. 
Но кроме этого метод еще распечатывает инфомационные сообщения.
```
public static boolean checkWinInGame(List<Character> displayWordList) {
  if (!(displayWordList.contains('*'))) {
    System.out.println("Вы виграли!!! Поздравляю!");
    System.out.println();
    return true;
  } else {
    return false;
  }
}
```
Метод нужно разделить на несколько, каждый из которых будет делать что-то одно.  

То же самое касается и других методов: `checkLostInGame(int numOfErrors)` etc.

*Мартин "Чистый код", гл.3, "Избавьтесь от побочных эффектов"*  
*Фаулер "Рефакторинг", гл.6, "Извлечение метода"*

**16. Рекурсия** 
```
+-> void startGameRound() { ... startGameLoop();}
|         |
|         V
|   void startGameLoop()
|         |
|         V
+---void controlGame() { ... startGameRound();}

```

Рекурсия должна использоваться только там, где она оптимальна алгоритмически.
Например при обходе бинарного дерева.

В других ситуациях, где рекурсия может быть замененена циклом, она должна быть им замененена. 
Рекурсия тяжело читается и при определенных условиях может привести к переполнению стека. 
Убери рекурсию, замени ее использование на цикл while().  

Условный пример
```
//ПЛОХО:
void start() {
  printMessage("Введите команду: 0-выйти, 1-играть");  
  int command = inputCommand();

  if(command == 0) {
    return;
  } 

  if(command == 1) {
    playGame();
  } 

  start();  <-- РЕКУРСИЯ
}

//ХОРОШО:
void start() {
  while(true) {
    printMessage("Введите команду: 0-выйти, 1-играть");  
    int command = inputCommand();

    if(command == 0) {
      return;
    } 

    if(command == 1) {
      playGame();
    } 
  }  
}
```

**17. Используй методы, а не кеширование**

Кеширование, в данном случае я под этим подразумеваю сохранение значения состояния игры во временной переменной, делает код багоопасным и сложным.
```
public static void startGameLoop() {
  while (true) {
    char inputLetter = getPlayerLetter();
    boolean haveLetter = checkLetterInWord(hiddenWordList, inputLetter);
    boolean gameOver = false;  <-- КЕШИРОВАНИЕ
    printHiddenWord(displayWordList);
    if (haveLetter) {
      gameOver = checkWinInGame(displayWordList);  <-- КЕШИРОВАНИЕ
    } else {
      numOfErrors--;
      gameOver = checkLostInGame(numOfErrors);  <-- КЕШИРОВАНИЕ
      drawHangman(numOfErrors);
    }
    if (gameOver) {  <-- ИСПОЛЬЗОВАНИЕ КЕША
      controlGame();
      break;
    }
  }
}

//ПРАВИЛЬНО:
public static void startGameLoop() {
  while (!isGameOver()) {
    char letter = getPlayerLetter();
    printHiddenWord(displayWordList);

    if (!isHiddenWordLetter(letter)) {  //эта буква есть в слове?
      numOfErrors--;
      //Напечатать "Осталось ошибок: x"
      drawHangman(numOfErrors);
    }

    if(isWin()) {
      //Напечатать сообщение "Вы выиграли!"
    } else if (isLose()) {
      //Напечатать сообщение "Вы проиграли!"
    }
  }
}

private static boolean isGameOver() {
  return isWin() || isLose();  
}
```

**18. Распечатка виселицы**

- Картинки нужно сделать константами. Константы это `final static`
```
static String[][] lostState = { 
  //картинки хангмана  
};

//ПРАВИЛЬНО:
private final static String[][] PICTURES = { 
  //картинки хангмана  
};
```

- Желательно картинки и метод распечатки вынести в отдельный класс, чтобы в основном классе не смешивать игровую логику и графику.  
Процедурный стиль дозволяет делать проекты, состоящие из нескольких классов.

## АРХИТЕКТУРА

Методы с побочными эффектами и неудачный нейминг приводят к тому, что код становится непонятный и запутанный.
Доходит до абсурда, когда вызывается метод с названием "напечатать скрытое слово", а печатет при это маску слова.  
Методы нужно рефакторить.

### Рассмотрим на примере

Есть метод, тело которого состоит всего из трех строк
```
static List<Character> usedLettersList = new ArrayList<>();

public static char getPlayerLetter() {
  usedLettersList.add(getUserLetter());
  printUsedLetters(usedLettersList);
  return usedLettersList.get(usedLettersList.size() - 1);
}
```
Для начала отрефакторим его так, чтобы было лучше понятно, что в нем происходит
```
static List<Character> usedLettersList = new ArrayList<>();

public static char getPlayerLetter() {
  char letter = getUserLetter();  
  usedLettersList.add(letter);
  printUsedLetters(usedLettersList);
  return usedLettersList.getLast();
}
```

Метод называется "вернуть букву от игрока". Но видно, что он делает не совсем это. 

Теперь нужно посмотреть на то, что метод *реально* делает и для самого себя сформулировать это.  
Получится что-то вроде: метод получает букву от пользователя, добавляет букву в список введенных, распечатывает список введенных букв и возвращает полученную от пользователя букву.  

Формулируем, оставляя только суть: метод возвращает введенную пользователем букву и выполняет действия, которые нужно выполнить при получении буквы от пользователя.

Становится понятно, что метод делает больше одного дела сразу и все, что не зявлено контрактом метода(получить букву от игрока), является побочным эффектом
```
static List<Character> usedLettersList = new ArrayList<>();

public static char getPlayerLetter() {
  char letter = getUserLetter();  
  usedLettersList.add(letter);  <-- ПОБОЧНЫЙ ЭФФЕКТ
  printUsedLetters(usedLettersList); <-- ПОБОЧНЫЙ ЭФФЕКТ
  return usedLettersList.getLast();  <-- НЕ побочный эффект
}
```

Если убрать все побочки из метода, то оказывается, что в существовании этого метода нет никакого смысла- это просто обертка над `getUserLetter()`
```
public static char getPlayerLetter() {
  char letter = getUserLetter();  
  return usedLettersList.getLast();
}
```

Проблема с кодом, который *сейчас* вызывает метод `getPlayerLetter()` состоит в том, что читая строку "получить букву от игрока", 
мы не можем понять, что в этот момент вместе с получением буквы в методе `getPlayerLetter()` происходят и другие процессы. 
Например, добавление буквы в список использованных и распечатка всех использованых букв
```
public static void startGameLoop() {
  while (true) {
    char inputLetter = getPlayerLetter();
    boolean haveLetter = checkLetterInWord(hiddenWordList, inputLetter);
    //...
  }
}

//ПРАВИЛЬНО:
public static void startGameLoop() {
  while (true) {
    char letter = getUserLetter();
    addToUsedLetters(letter);
    printUsedLetters(usedLettersList);

    boolean haveLetter = checkLetterInWord(hiddenWordList, inputLetter);
    //...
  }
}

private static void addToUsedLetters(char letter) { <-- НАЗВАНИЕ ОБЪЯСНЯЕТ, ЧТО ДЕЛАЕТ МЕТОД
  usedLettersList.add(letter);  <-- А ТЕЛО РЕАЛИЗУЕТ ЭТО
}
```

Значит метод этот вообще лишний и его нужно убрать. Вот так, само собой, отпадет проблема 

## ВЫВОД

Обратить особое внимание на нейминг. 
Начать прям с базы- с прочтения "Java Code Conventions", есть русские переводы.
Потом почитай 2-ю главу "Чистого кода". 

Посмотри несколько роликов на ютубе по наименованию переменных и методов, в том числе ролик Немчинского "Как называть переменные, методы и классы?".  
Для развития навыков процедурной декомпозиции, посмотри стрим Сергея [Крестики-нолики](https://www.youtube.com/watch?v=PPikj1qHxrA)

n.102(192)  
#ревью #виселица #падеж #рекурсия 