https://github.com/PancoManco/hangman_firstProject.git  
[FADISLABR]

Игра в процедурном стиле, выполнена в нескольких классах.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

Не обнаружено.

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву английского языка
+ 👍 Есть список введенных букв

## ЗАМЕЧАНИЯ

**1. Нейминг**

- В названии переменных не пиши тип данных, к которым они относится. И вообще не употребляй венгерскую ноттацию
```
List<String> wordList = new ArrayList<>();

//ПРАВИЛЬНО:
List<String> words = new ArrayList<>();
```

- Для одной концепции должно быть одно название. Оба этих метода делают одно- распечатывают информацию
```
public void showGameStatus(String word, int mistakes)
private void printHiddenWord(List<Character> hiddenWord)
```

- Названия методов должны быть глаголом в повелительном наклонении. Название метода это команда выполнить какие-то действия, типа "беги", "стой", "садись в трамвай"
```
void statusGame(int misses)

//ПРАВИЛЬНО:
void printHangman(int misses)
```

- Название должно как можно лучше объяснять суть явления. Здесь хранятся не просто символы, а буквы
```
Set<Character> incorrectChars

//ПРАВИЛЬНО:
Set<Character> incorrectLetters
```

- Название должно как можно лучше объяснять суть явления. Этот метод создает маску слова
```
List<Character> getListWithUnderline(String randomWord)

//ПРАВИЛЬНО:
List<Character> createMask(String randomWord)
```

- Метод, который что-то проверяет, должен возвращать результат проверки в виде `boolean`
```
void checkUserGuess(char guess)
```

- Классы пишутся с большой буквы, название должно быть существительным
```
class launch

//ПРАВИЛЬНО:
class Launcher
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*  

**2. Нарушение DRY**, магические буквы, числа, слова. Вводи константы
```
System.out.println("Выберите один из двух вариантов :  1 - начать новую игру : 0 - выйти из приложения ");
System.out.println("Введите 0 или 1 : ");
case "0":  //...
case "1":  //...

//ПРАВИЛЬНО:
private final static int START = 1;
private final static int QUIT = 0;

System.out.printf("Выберите один из двух вариантов :  %d - начать новую игру : %d - выйти из приложения %n", START, QUIT);
System.out.printf("Введите %d или %d : ", START, QUIT);
case QUIT:  //...
case START:  //...
```
*Фаулер, "Рефакторинг", гл.8 п."Замена магического числа символической константой"*   
*refactoring.guru "Замена магического числа символьной константой"*  

**3. Нарушение DRY**, дублирование констант

Одно и то же значение в разных классах хранится в разных константах. Константы в проекте не должны дублироваться
```
public class HangmanGame {
  private static final int DEAD_STATE = 6;
  //...
}

public class StatusGame {
  private static final int DEAD_STATE = 6;
  //...
}
```

**4. Нарушение DRY**, магические буквы, числа, слова. Вводи константы, а если они уже есть- используй.

Это магическое число уже существует в проекте в виде двух констант
```
while (miss != 6 && (underLineWord.contains('_')))
```

**5. Создавай вспомогательные методы**, делай программу более простой и понятной
```
while (miss != 6 && (underLineWord.contains('_'))) {...}

//ПРАВИЛЬНО:
while (isНазваниеКотороеВсеОбъясняет(/* args or empty */)) {...}

private boolean isНазваниеКотороеВсеОбъясняет(/* args or empty */) {
  return miss != DEAD_STATE && (underLineWord.contains('_'));
}
```

**6. class WordReader**

- Имя файла класс должен получать в конструктор. Иначе класс становится неуниверсальным.
Допустим, в программе будет несколько разных текстовых файлов- на разных языках или по разным темам. 
Тогда вместо нескольких специализированных классов Словаря, где имя файла словаря будет жестко прописано в каждом из классов, можно будет использовать один универсальный класс словарь.

- Игнорирование ошибок- плохо. Если произойдет ошибка при чтении  файла, то эта ошибка будет перехвачена и проигнорирована. Но исключение по связанной с этим причиной все равно произойдет позже и оно аварийно завершит выполнение программы
```
try {  
  // тут может вылетить IOException
  // тут wordList заполняется данными
} catch (IOException e) {  
  System.err.println("Ошибка чтения файла: " + e.getMessage());  <-- ИГНОРИРОВАНИЕ ИСКЛЮЧЕНИЯ
}
String randomWord = wordList.get(random.nextInt(0, wordList.size())); <-- ПРИ ИГНОРИРОВАНИИ IOException ТУТ ВЫЛЕТИТ IndexOutOfBoundsException ПОТОМУ ЧТО wordList НЕ БЫЛ ЗАПОЛНЕН В try
```
В случае, если файл со словами не был прочитан, нужно вывести соответствующее сообщение и корректно закрыть программу без вываливания красных эксепшенов в консоль.

**7. class StatusGame**, распечатывает информацию игры

- Вот это совсем не читается. Нет задачи экономить строчки, главная задача- писать понятный код, который при необходимости можно легко изменить
```
System.out.println("  _____\n" + "  |  |\n" + "  |\n" + "  |\n" + "  |\n" + "__|____");

//ПРАВИЛЬНО:
System.out.println("-----");
System.out.println("|   |");
System.out.println("|   O");
System.out.println("|    ");
System.out.println("|    ");
System.out.println("-----");
```

- В любом switch-case должен быть default, в данном случае default должен бросать исключение с сообщением, что такого номера картинки не существует.

- Распечатка картинки виселицы через switch-case или if-elseif - индусский код.
Картинки нужно хранить в статическом массиве и печатать по номеру, например, так
```
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

public void printHangman(int numPicture) {  
  System.out.println(PICTURES[numPicture]);
}
```

- Судя по названию, метод должен только показывать состояние игры, а не определять это состояние.  
Этот класс только распечатывает информацию, он не должен содержать в себе правила игры и определять выигрыш или проигрыш
```
public void showGameStatus(String word, int mistakes) {
  statusGame(mistakes);
  if (mistakes < DEAD_STATE) {  <-- ОПРЕДЕЛЯЕТ СТАТУС
    System.out.println(GUESSED_WORD + word);
    System.out.println(WON_MESSAGE);
  } else if (mistakes == DEAD_STATE) {  <-- ОПРЕДЕЛЯЕТ СТАТУС
    System.out.println(GUESSED_WORD + word);
    System.out.println(LOOSE_MESSAGE);
  }
}

//ПРАВИЛЬНО:
public void showWinMessage(/* args */)
public void showLoseMessage(/* args */)
```

**8. class HangmanGame**, основная игровая логика

- Побочный эффект.
Судя по названию, метод должен только проверить символ
```
void checkUserGuess(char guess)
```
Но кроме этого метод еще производит действия, которые нужно выполнить при вводе правильной или неправильной буквы.

Метод нужно разделить на несколько, каждый из которых будет выполнять только одну задачу.  
*Фаулер "Рефакторинг", г.6, п."Извлечение метода"*  

- Избыточно. При вызове этого метода каждый раз будет создаваться и компилироваться паттерн. Поля `regex` и `pattern` должны быть полями класса, а не метода- тогда они создадутся один раз
```
public boolean isEnglishLetter(Character guess) {
  String regex = "^[A-Za-z]+$";
  Pattern pattern = Pattern.compile(regex);
  return pattern.matcher(guess.toString()).matches();
}
```

- Магический символ: `'_'`

**9. class launch**, содержит точку входа main

+ 👍 Только создает и запускает игру, это хорошо.

+ 👍 Хорошо, что именно здесь происходит диалог "играть или выйти"

- Плохо то, что экземпляр игры создается до диалога с пользователем. Поэтому, если юзер не захочет играть, то экземпляр игры был создан зря
```
public static void main(String[] args) {
  HangmanGame hangmanGame = new HangmanGame();  <-- СНАЧАЛА СОЗДАЛИ ИГРУ
  //...    
  System.out.println("Выберите один из двух вариантов :  1 - начать новую игру : 0 - выйти из приложения "); <-- ПОТОМ СПРОСИЛИ А НАДО ЛИ
  //
}
```

## ВЫВОД

Для процедурного стиля неплохо.

n.88(171)  
#ревью #виселица 