https://github.com/ASmolenkov/Hangman-OOP  
[Александр]

Игра написана в ООП стиле, хотя ООП здесь далеко не идеально.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. На старте показывает загаданное слово, что напрочь убивает интригу
```
Вам загадано слово: ледоруб
Вам загадано слово: ■■■■■■■
```
Идея понятна- без этой подсказки трудно делать отладку программы. Но раз мы говорим про ООП, то есть способы это сделать по-лучше, о чем поговорим ниже.

2. Одна и та же буква в разных регистрах считается двумя разными буквами и увеличивает счетчик ошибок
```
Введите букву или слово целиком
л
Вам загадано слово: ледоруб
Вам загадано слово: л■■■■■■
Колличество ошибок: 0
Вы уже использовали: [л]

<КАРТИНКА>

Введите букву или слово целиком
Л
Буквы: "Л" в слове нет.
Вам загадано слово: ледоруб
Вам загадано слово: л■■■■■■
Колличество ошибок: 1
Вы уже использовали: [л, Л]
```

3. Кроме букв, можно ввести некоторые символы, которые буквами не являются
```
Введите букву или слово целиком
;
Буквы: ";" в слове нет.
Вам загадано слово: неотвязность
Вам загадано слово: ■■■■■■■■■■■■
Колличество ошибок: 1
Вы уже использовали: [;]
```

4. Неправильная последовательность в порядке отображения введенных букв- букву 'ц' я ввел второй, но в распечатке она стала первой.
При этом введенная третьей буква 'к' в распечатке стала на третью позицию
```
Введите букву или слово целиком
й
Вы уже использовали: [й]

Введите букву или слово целиком
ц
Вы уже использовали: [ц, й]
 
Введите букву или слово целиком
к
Вы уже использовали: [ц, й, к]
 
Введите букву или слово целиком
з
Вы уже использовали: [ц, з, й, к]
```
Введенные буквы должны размещаться по алфавиту либо в порядке ввода.

5. Вешается одноногий
```
Колличество ошибок: 5
Вы уже использовали: [р, а, в, ;, ы, п]
  +---+
  |   |
  O   |
 /|\  |
 /    |
      |
=========
Введите букву или слово целиком
ф
Буквы: "ф" в слове нет.
Попытки закончились, вы проиграли! 
```

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести либо одиночную букву русского афавита, либо все слово целиком. При этом данное слово должно быть такого же размера, как и загаданное- за эту проверку отдельный 👍
+ 👍 Есть список введенных букв

## ЗАМЕЧАНИЯ

**1. Нейминг**

- UPPER_SNAKE только для констант, а это не константы
```
private final Dictionary DICTIONARY;
private final Scanner SCANNER;

public void maskingWord() {
  String CHAR_MASK = "■";  <-- ЭТО ТОЖЕ НЕ КОНСТАНТА
  //...
}
```

- Название метода должно быть глаголом в повелительном наклонении
```
void letterReplacement(...)
void beginningGame()

//ПРАВИЛЬНО:
void openLetter(...)
void beginGame()
```

- Если в проекте есть класс `SecretWord` то все переменные с именем, включающим это название, должны быть экземплярами этого класса
```
public class SecretWord {
  private String secretWord;
  //...
}

//ПРАВИЛЬНО:
public class SecretWord {
  private String text;
  //...
}
```

- Название обманывает. Метод не угадывает слово, метод создает текст секретного слова и маску слова. То есть, производит начальную инициализацию значений
```
String guessTheWord()
```

- Каждая часть названия должна иметь смысл, а не быть просто для красоты. В данном случае префикс `App` не несет никакого смысла, потому что если его откинуть, смысл названия не изменится.  
И так ясно, что находясь в приложении, это константы приложения
```
class AppConstants
```

- Шаблоны для подстановочных значений желательно подписывать как шаблоны
```
public static final String LETTTER_NOT = "Буквы: \"%s\" в слове нет.\n";

//ЛУЧШЕ:
public static final String WITHOUT_LETTER_TEMPLATE = "Буквы: \"%s\" в слове нет.\n";
```

- Избыточный контекст
```
gameLogic.startGame();
hangmanPicture.printHangman(...)

//ПРАВИЛЬНО:
gameLogic.start();
hangmanPicture.print(...)
```

- Название обманывает. Метод не устанавливает `null` в счетчик попыток, он сбрасывает счетчик ошибок
```
void setTryCountNull()

//ПРАВИЛЬНО:
void resetTryCount()
```

- Название должно объяснять, что делает метод. Из названия этого метода невозможно понять, что он делает. Он проверяет, что `Player player` это счетчик попыток?
```
boolean checkIsTryCount(Player player)
```
Вот как посторонний человек будет интерпритировать такое название:  
`check` - проверить  
`IsTryCount` - это счетчик попыток  
`Player player` - объект, в отношении которого осуществляется действие

Нет, на самом деле этот метод опредедляет, что достигнут макисмальный лимит попыток. Этот функционал и нужно отразить в названии
```
public static boolean checkIsTryCount(Player player) {
  return player.getTryCount() >= AppConstants.MAX_TRY;
}
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*  

**2. Нарушение DRY**, магические буквы, числа, слова. Вводи константы. А если они уже есть- пользуйся
```
public static final String STOP = "Стоп";
public static final String START = "Старт";
public static final String START_PROMPT = "Если хотите начать новую игру введите \"Старт\"" + "\n" + "Если хотите выйти введите \"Стоп\"";

//ПРАВИЛЬНО:
public static final String STOP = "Стоп";
public static final String START = "Старт";
public static final String START_PROMPT = "Если хотите начать новую игру введите '%s'  %nЕсли хотите выйти введите '%s'".formatted(START, STOP);
```
*Фаулер, "Рефакторинг", гл.8 п."Замена магического числа символической константой"*   
*refactoring.guru "Замена магического числа символьной константой"*  

**3. Если в блоке if есть return**(break, continue, throw, exit и т.д.), то else не пишется - в этом случае неважно, будет else или нет, так как программа будет работать одинаково и код выглядит читабельней
```
if (player.getAnswer().equalsIgnoreCase(AppConstants.STOP)) {
  System.out.println(AppConstants.THANKS);
  return false;
} else {
  System.out.println(AppConstants.INVALID_INPUT);
}

//ПРАВИЛЬНО:
if (player.getAnswer().equalsIgnoreCase(AppConstants.STOP)) {
  System.out.println(AppConstants.THANKS);
  return false;
} 
System.out.println(AppConstants.INVALID_INPUT);
```

**4. Создавай вспомогательные методы**, делай программу более простой и понятной
```
if (ChecksAnswer.checkIsTryCount(player)) {
  System.out.println(AppConstants.YOU_LOOSE);
  //...
}

//ПРАВИЛЬНО:
if (isLose)  {
  System.out.println(AppConstants.YOU_LOOSE);
  //...
}

private boolean isLose() {
  return ChecksAnswer.checkIsTryCount(player);
}
```

**5. class Dictionary**

Путь к файлу класс должен получать в конструктор. Иначе класс становится неуниверсальным.  
Допустим, в программе будет несколько разных текстовых файлов- на разных языках или по разным темам.  
Тогда вместо нескольких специализированных классов Словаря, где имя файла словаря будет жестко прописано в каждом из классов, можно будет использовать один универсальный класс словарь.

**6. class SecretWord**

- Нарушение SRP, класс совмещает несколько ответственностей: загрузка слова из словаря и операции с секретным словом: создание маски, открытие буквы etc.  
Класс не должен сам создавать словарь и вообще знать, откуда нужно брать слово. Текст слова класс должен принимать в конструктор
```
public class SecretWord {
  private final Dictionary DICTIONARY;
  //...

  public SecretWord() {
    DICTIONARY = new Dictionary();
  }
  //...
}

//ПРАВИЛЬНО:
public class SecretWord {
  private final String text;
  //...

  public SecretWord(String text) {
    this.text = text;
  }
  //...
}
```
Каждый класс должен иметь одну и только одну причину для изменения(ответственность) - принцип SRP.

- Публичным должны быть только те методы, которые используются клиенским кодом.  
Вспомогательные методы должны быть приватными- это внутренности класса, их не должны трогать извне, чтобы не поломать логику работы класса.  
Например, этот метод вспомогательный и должен быть private: `public void maskingWord()`.

- Нарушение Low Coupling. Класс не должен знать ни про какого Player'а и вызывать у него метод ввода буквы
```
public void letterReplacement(Player player) {
  for (int i = 0; i < secretWord.length(); i++) {
    if (player.getAnswer().charAt(0) == secretWord.charAt(i)) {
      wordMask.setCharAt(i, player.getAnswer().charAt(0));
    }
  }
}

//ПРАВИЛЬНО:
public void openLetter(char letter) {
  for (int i = 0; i < secretWord.length(); i++) {
    if (secretWord.charAt(i) == letter) {
      wordMask.setCharAt(i, letter);
    }
  }
}
```

- Разделяй команды и запросы. Этот метод должен или выполнить действие или выполнить запрос. Но не то и другое вместе
```
public String guessTheWord() {
  Random random = new Random();
  List<String> dictionaryListWord = DICTIONARY.getListWord();
  this.secretWord = dictionaryListWord.get(random.nextInt(dictionaryListWord.size())); <-- ВЫПОЛНЕНИЕ ДЕЙСТВИЯ
  maskingWord();     <-- ВЫПОЛНЕНИЕ ДЕЙСТВИЯ
  return secretWord; <-- ВЫПОЛНЕНИЕ ЗАПРОСА
}
```
*Мартин, "Чистый код", гл.3, п."Разделение команд и запросов"*  

**7. class Player**

- Класс с именем "Игрок" имеет смысл только в многопользовательских играх, где он хранит данные для идентификации пользователя.
Например, в игре крестики-нолики Игрок хранит значок для хода(X, 0).

В однопользовательских играх Игрок может существовать, если нужно хранить его персональные данные, необходимые для процесса игры. 
Например, количество денег при игре в казино.

Что касается хангмана, то в одном из прошлых проектов был класс Player и он там был оправдан, 
потому что в нем только считалась статистика выигрышей/проигрышей игрока: https://t.me/zhukovsd_it_chat/118068

В данной реализации класс `Player` лишний.

- В таких случаях не нужно кешировать данные. Иначе правила пользования классом становятся сложными и можно легко допустить ошибку при его использовнии
```
private String answer;

public void playerAnswer() {
  answer = SCANNER.nextLine();  <-- КЕШИРОВАНИЕ
}

public String getAnswer() {
  return answer;
}

//ПРАВИЛЬНО:
public String getAnswer() {
  return SCANNER.nextLine();
}
```

- Константный класс здесь используется неправильно- Player напрямую лезет в константы вместо того, чтобы получать необходимые данные в свой конструктор.  
Это делает класс неуниверсальными и зависимым от класса констант  
```
public void setTryCountNull() {
  this.tryCount = AppConstants.MIN_TRY;
}
```

Условный пример
```
public final class Settings {
  //приватный конструктор
  public static final int HOUSE_ROOMS = 5;
}

/ПЛОХО:
public static void main(String[] args) {
  House house = new House();
  //oth code
}

public class House {
  private final int rooms;

  public House() {
    this.rooms = Settings.HOUSE_ROOMS;
  }

  public int getRooms() {
    return rooms;
  }
}

//ХОРОШО:
public static void main(String[] args) {
  House house = new House(Settings.HOUSE_ROOMS);
  //oth code
}

public class House {
  private final int rooms;

  public House(int rooms) {
    this.rooms = rooms;
  }

  public int getRooms() {
    return rooms;
  }
}
```
Про классы констант, конфигурации и их использование я писал тут: https://t.me/zhukovsd_it_chat/53243/176984

**8. public class ChecksAnswer**

- Если класс содержит только статические методы, но класс должен быть `final` и иметь приватный конструктор. Не должно быть возможности создать экземпляр такого класса или унаследоваться от него.

- Этот метод определяет наличие буквы в `SecretWord`, это часть ответственности того класса и должен находиться в нем
```
public static boolean checkLetterInSecretWord(Player player, SecretWord secretWord)
```
То же самое касается `boolean checkFullWord(...)`.

**9. public GameLogic()**, основная игровая логика

- Нарушение инкапсуляции- все методы в классе публичные. Публичными должны быть только те методы, за которые разрешается дергать клиентам. Здесь это только public `void startGame()`

- Статические методы в ООП программе нужно применять очень осторожно.  
В данном случае нет никакой необходимости в том, чтобы эти и другие методы данного класса были статичными
```
public static boolean startMenu()
public static void beginningGame()
```
Потому что статичный метод невозможно переопределить. А я не вижу здесь причин, чтобы потомка ограничивали в возможности переопределить метод `startMenu()` или любой другой в этом классе.  
В этом классе не должно быть ни одного статичного метода.

- Нарушение DRY, дублирование кода
```
if (ChecksAnswer.checkFullWord(player, secretWord)) {
  //... 
  player.getEnteredLetters().clear();
  player.setTryCountNull();
  break;
}

if (ChecksAnswer.checkIsTryCount(player)) {
  //...        
  player.getEnteredLetters().clear();
  player.setTryCountNull();
  break;
}
```

- Этот объект здесь используется в качестве "многоразового". То есть после использования объекта, нужно сбрасывать его значения к значениям по умолчанию.  
Но сброс значений здесь не только дублируется, но и находится не там- сейчас он происходит в конце процесса игры 
```
if (ChecksAnswer.checkIsTryCount(player)) {  
  System.out.println(AppConstants.YOU_LOOSE);   <-- ДЕЙСТВИЯ ПРИ ПРОИГРЫШЕ
  //...        
  player.getEnteredLetters().clear();  <-- СБРОС ЗНАЧЕНИЙ К ИСХОДНЫМ
  player.setTryCountNull();   <-- СБРОС ЗНАЧЕНИЙ К ИСХОДНЫМ
  break;
}
```
На самом деле этот сброс, а по факту начальная инициализация раунда, должен происходить в самом начале метода `void beginningGame()`, перед началом вечного цикла.

- Большой и трудно читаемый метод `void beginningGame()` - 50 строк.  
Если метод больше 20-30 строк, то скорее всего он делает много разного(содержит в себе много ответственностей) и его нужно разделить на несколько или выделить из него вспомогательные методы.  

Например, сделай методы, которые будут определять проигрыш и выигрыш. Тогда код будет более понятным, примерно так
```
while (!isGameOver()) {
  //процесс игры
  if (isLose()) {
    printLoseMessage();
  } else if (isWin()) {
    printWinMessage();
  }
}

//...
private boolean isGameOver() {
  return isWin() || isLose();
}
```

**10. class HangmanPicture**

- Не забывай про инкапсуляцию. Всегда явно ставь область видимости переменных и методов. В данном случае, она должна быть `private`
```
String[] hangmanPicture;
```

- Картинки в массиве это ок. Но заполнение массива картинками нужно вынести из конструктора и сделать массив константой.

+ 👍 В целом, класс ок.

**11. class Hangman**, содержит точку входа main

👍 Только создает и запускает игру, это хорошо.

**12. Изящно решаем ситуацию с подсказкой**

+ Итак, мы пишем хангмана, но столкнулись с проблемой- в процессе отладки алгоритма мы должны знать текст секретного слова, чтобы можно было сознательно угадывать или не угадывать буквы.  
Выход очевиден- просто пишем подсказку каждый раз над маской слова
```
Вам загадано слово: лоббист
Вам загадано слово: ■обб■■■
```

Но потом мы забываем отключить подсказку и заливаем это в продакшн. Кому-то из юзеров понравится так играть, остальным не очень.  

+ Что на самом деле нужно было делать в этой ситуации?
Сейчас подсказка у нас печатается в классе `GameLogic` тут 
```
public static void printGameState(SecretWord secretWord, Player player) {
  System.out.println(AppConstants.WORD_SECRET + secretWord.getSecretWord());  <-- ПОДСКАЗКА
  System.out.println(AppConstants.WORD_SECRET + secretWord.getWordMask());
  //печатает остальное
}
```

+ Первым делом убераем `static` из всех методов класса.

+ Убираем подсказку из `printGameState(...)`. Теперь игра, запускаемая классом `Hangman`, работает без посказки. Это основной вариант игры, в которую будут играть юзеры.

+ Создаем отдельный тестовый Майн, который будем использовать только для отладки алгоритма и который можно даже записать в гитигнор, чтобы он не пушился в репозиторий.  
В этом Майне переопределяем метод распечатки `printGameState(...)` таким образом, чтобы перед распечаткой всех остальных сообщений, печаталась подсказка. Вся остальная логика работы класса остается неизменной
```
public class HangmanWithHint {
    
  public static void main(String[] args) {

    GameLogic gameLogic = new GameLogic() {  <-- СДЕЛАЛ АНОНИМНЫЙ КЛАСС-НАСЛЕДНИК КЛАССА GameLogic
      @Override
      public void printGameState(SecretWord secretWord, Player player) {  <-- ПЕРЕОПРЕДЕЛИЛ МЕТОД
        System.out.println(AppConstants.WORD_SECRET + secretWord.getSecretWord());  <-- НАПЕЧАТАЛ ПОДСКАЗКУ
        super.printGameState(secretWord, player);  <-- ВЫЗВАЛ ДЕЙСТВИЯ ОРИГИНАЛЬНОГО МЕТОДА ПРЕДКА
      }
    };
    gameLogic.startGame();
  }
}
```
Наследование и полиморфизм!  

## АРХИТЕКТУРА

Конечно, ООП здесь далеко до идеала.  
Но оно тут все-таки есть, что уже неплохо- далеко не все пишут в ООП стиле, когда думают, что пишут в ООП стиле.  

## ВЫВОД

Нужно глубже понять принципы, по которым в ООП программе логика делится на классы.  
Для лучшего понимания декомпозиции ООП посмотреть ролики Сергея про шахматы. Посмотреть ролики Немчинского про SOLID.

n.86(168)  
#ревью #виселица #виселицаооп #подсказка