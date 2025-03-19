https://github.com/FiSheNiR/Hangman  
[Марк]

Игра в ООП стиле. 

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

👍 Не обнаружено.

## ХОРОШО

+ 👍 Игра запускается 
+ 👍 Есть список ошибочно введенных букв
+ 👍 Можно ввести только одиночную букву русского языка

## ЗАМЕЧАНИЯ

**1. Нейминг**

- UPPER_SNAKE только для констант, а это не константа
```
 private final String FILE_PATH = "src/main/java/org/example/WordsList.txt";

//ПРАВИЛЬНО ТАК(КОНСТАНТА):
 private static final String FILE_PATH = "src/main/java/org/example/WordsList.txt";
//ИЛИ ТАК(НЕ КОНСТАНТА):
private final String filePatch = "src/main/java/org/example/WordsList.txt";
```

- В названии переменных не пиши тип данных, к которым они относится. И вообще не употребляй венгерскую ноттацию
```
List<String> wordsList

//ПРАВИЛЬНО:
List<String> words
```

- Название метода вводит в заблуждение. Обещает просто проверить вхождение буквы, а на самом деле делаем много чего, вплоть до распечатки результатов 
```
public void containLetter(String userInput, SecretWord secretWord, MaskWord maskWord)
```

- Название не объясняет, что делает метод. На самом деле метод проверяет вхождение буквы в secretWord и окрывает буквы в maskWord
```
boolean checkLetter(String userInput, SecretWord secretWord, MaskWord maskWord)
```
*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*  


**2. Нарушение DRY**, магические буквы, числа, слова. Вводи константы
```
System.out.println("Начать новую игру [н] Выйти [в]");
//...
case "Н" -> //some action
case "В" -> //some action

//ПРАВИЛЬНО:
private final static String START = "Н";
private final static String QUIT = "В";

System.out.printf("Начать новую игру [%s] Выйти [%s] %n", START, QUIT);
//...
case START -> //some action
case QUIT -> //some action
```
*Фаулер, "Рефакторинг", гл.8 п."Замена магического числа символической константой"*   
*refactoring.guru "Замена магического числа символьной константой"*  

**3. Слишком длинные конструкции**, это вообще нечитаемо
```
System.out.println(Hangman.values()[mistakeHandler.getMistakeNumber()].getImage());

//ПРАВИЛЬНО:
int mistakeNumber = mistakeHandler.getMistakeNumber();
Hangman hangman = Hangman.values()[mistakeNumber];
String hangmanImage = hangman.getImage();

System.out.println(hangmanImage);
```
Вводи поясняющие переменные.  
*Фаулер, "Рефакторинг", гл.6,п."Введение поясняющей переменной"*  

**4. Отделяй пробелом перенос строки** для лучшей читаемости
```
System.out.printf("Ошибочные буквы: %s%n", mistakeHandler.getWrongLetterList());

//ПРАВИЛЬНО:
System.out.printf("Ошибочные буквы: %s %n", mistakeHandler.getWrongLetterList());
```

**5. class WordsListFromFile**, читает слова из файла

+ 👍 Отдельный класс, который читает файл в лист слов, это хорошо.

- Класс, выполняющий функции чтения файла, не должен хранить в себе путь к файлу. Он должен получать этот путь в конструктор
```
public class WordsListFromFile {
  private final String FILE_PATH = "src/main/java/org/example/WordsList.txt";
  //...
}

//ПРАВИЛЬНО:
public class WordsListFromFile {
  private final String filePatch;

  public WordsListFromFile(String filePatch) {
    this.filePatch = filePatch;
  }
  //...
}
```
Если класс хранит путь в себе, а не получает его в конструктор, то класс становится неуниверсальным.

- Нарушение SRP. Класс должен просто прочитать файл со словами. Класс не должен по своей воле фильтровать данные, которые хранятся в файле путем проверки слов на соответствие необходимым длине и алфавиту
```
if (word.length() >= MIN_WORD_LENGTH && word.matches("[а-яА-Я]+")) {
  words.add(word);
}
```
Алгоритм должен быть таким: 
  * WordsListFromFile читает файл, имя которого передали ему в конструктор, и возвращает эти слова в виде списка
  * Клиент получает список слов, выбирает случайное слово из списка и отдает своему клиенту
  * Наконец тот код, который получает случайное слово, должен при необходимости провалидировать это слово и бросить исключение, если что-то не так

Альтернативный алгоритм: класс должен принимать в конструктор также значение длины слова и регулярное выражение для его фильтрации.

**6. class InputHandler**, валидированный ввод буквы + хранилище введенных букв

- Переменная хранит не дубликаты букв, а уникальные буквы.  
Иное в принципе тут невозможно, потому что буквы хранятся в Set, что гарантирует их уникальность в этой коллекции: `Set<String> duplicateLettersSet`

- Побочные эффекты, метод выполняет два действия сразу: реализует пользовательский ввод и ведет учет введенных букв
```
public String getUserInput() {
  //...
  duplicateLettersSet.add(userInput); <-- действие 1
  return userInput; <-- действие 2
}
```
Метод нужно разделить на два, каждый из которых будет делать что-то свое.

- Нарушение SRP, у класса две ответственности: ввод одиночной буквы выбранного алфавита и учет введенных букв.
В классе нужно оставить только ответвенность по вводу одиночной русской буквы.

**7. class MaskWord**

- Стандартные переопределения типа `toString()` пиши внизу- их там обычно ищут.

**8. class MistakeHandler**

- Уже по тому, что название у класcа очень размытое и пространное, ясно что класс не имеет четких обязанностей.  
Действия, которые сейчас выполняет класс:
  - Хранит список неправильных букв.
  - Считает ошибки.
  - Хранит значение максимального уровня ошибок.
  - Проверяет наличие буквы в слове.
  - Хранит часть игровой логики: анализирует введенную букву, пишет "Есть такая буква" или "Такой буквы нет" и добавляет букву в список ошибочных.

Таким образом, это божественный класс. 

- Побочные эффекты. Метод не только проверяет букву(на предмет вхождения в секретное слово), но и изменяет состояние `MaskWord maskWord`
```private boolean checkLetter(String userInput, SecretWord secretWord, MaskWord maskWord) {
  //...
  if (gameSecretWord[i] == inputChar) {
    maskWord.unmaskLetter(i, inputChar);  <-- 1
    found = true;
  }
  return found;  <-- 2
}
```

- Побочные эффекты. Метод делает всякое разное, плоть до распечатки текстов. Это явно часть игровой логики, которая должна быть где-то в Game
```
void containLetter(String userInput, SecretWord secretWord, MaskWord maskWord) 
```

- Нарушение SRP, чужая ответственность, зависимость модели от представления.
Модель(а это модель) не должна ничего печатать в консоль
```
System.out.println("Такой буквы нет. Количество ошибок: " + mistakeNumber);
```
Иначе модель перестает быть универсальной и становится заточенной под конкретную среду и конкретное представление себя в этой среде- в данном случае, консоль.
В других средах(напр. Андроид) модель нельзя будет использовать. 
Другое представление для модели(напр. если одну и ту же модель нужно в программе показвать по-разному) нельзя будет сделать, или придется делать через костыль.

**9. class SecretWord**

- Своим названием класс путает- приходится задумываться, чем оттличается SecretWord от MaskWord.
В данном случае класс получает список слов из WordsListFromFile и выбирает из него одно случайное.

+ 👍 В принципе, идея разделить на два класса процессы чтения слов и выдачи из них одного случайного слова это хорошая идея.  
Но для начала нужно навести порядок с неймингом. 
Например:
  + WordFileReader - читает файл со словами в лист
  + Dictionary - использует WordFileReader для получения списка слов, выдает случайное слово из этого списка

Выглядеть это должно примерно так:
```
public class WordFileReader {
  private final String filePatch;

  public WordFileReader(String filePatch) {
    this.filePatch = filePatch;
  }

  public List<String> read() {
    //прочитать файл и выдать список слов
  }
}

public class Dictionary {
  private final WordFileReader wordFileReader;
  private final List<String> words;

  public Dictionary(WordFileReader wordFileReader) {
    this.wordFileReader = wordFileReader;
    this.words = wordFileReader.read();
  }

  public String getRandomWord() {
    //выдать случайное слово из списка слов words
    //при каждом вызове метода- выдать новое случайное слово
  }
}
```
Можно возразить, что у тебя примерно так все и сделано.  
Но у тебя класс SecretWord концептульно другой- он реализует не Словарь, а контейнер секретного слова с самозагрузкой оного из файлового читателя.

Дальнейшее развитие идеи- интерфейс чтения слов и разные его реализации. Это если захочется заморочиться
```
public interface WordReader {
  List<String> read();
}

public class InMemoryWordReader implements WordReader{
  private final static List<String> WORDS = List.of("инкапсуляция", "наследование", "полиморфизм");

  List<String> read() {
     return new ArrayList<>(WORDS);
  }
}

public class WordFileReader implements WordReader{
  //читаем слова из файла
}

public class HttpWordReader implements WordReader{
  //читаем слова по HTTP
}

public class Dictionary {
  private final WordReader wordReader;
  private final List<String> words;

  public Dictionary(WordReader wordReader) {...}
  //...
}
```
Это позволит получать слова разными способами и тем самым делать разные игровые конфигурации, например
```
//1
WordReader wordReader = new InMemoryWordReader();
Dictionary dictionary = new Dictionary(wordReader);
String word = dictionary.getRandomWord();
Game game = new Game(word);
game.start();

//2
WordReader wordReader = new HttpWordReader();
Dictionary dictionary = new Dictionary(wordReader);
//и т.д.
```
Зачем все эти сложности для первого проекта? Правильно, незачем. Поэтому стандартный путь: просто сделать класс Dictionary, который читает слова из файла и выдает одно случайное из них.

**10. class OutputManager**

- Рендерер(вью) для распечатки информации. Сама идея неплохая, но непонятно, почему часть информации выводится через это вью, а часть- печатается в других классах напрямую
```
public class OutputManager {
  public void printGameState(MaskWord maskWord, MistakeHandler mistakeHandler) {...}
  private void drawHangman(MistakeHandler mistakeHandler) {...}
}
```

**11. enum Hangman**, картинки хангмана

- Идея с хранением картинок в енаме- неудачная. Намного практичнее сделать простой класс типа такого
```
public final class HangmanPicture {    
  private static final String[] PICTURES = {
    """
    -----   
    |       
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
     |       
     ------- 
     """
   ,
   // more pics
  };

  //приватный конструктор

  public static String get(int pictureNumber) {
    return PICTURES[pictureNumber];
  }
}  
```
И тогда пользоваться картинками будет немного проще
```
//С ЕНАМОМ:
String image = Hangman.values()[mistakeHandler.getMistakeNumber()].getImage();
System.out.println(image);

//С КЛАССОМ:
String picture = HangmanPicture.get(mistakeHandler.getMistakeNumber());
System.out.println(picture);
```

**12. class Game**

- Содержит часть основной игровой логики. Другая часть размазана по остальным классам, см. `MistakeHandler`

- Две ответственности в методе: проверка того, что игра идет и распечатка результатов игры
```
private boolean gameResult()
```

Я бы предложил сделать такие методы:
```
private boolean isGameOver() {
    return isWin() || isLose;
}
private boolean isWin() {...}
private boolean isLose() {...}

private boolean printWinMessage() {...}
private boolean printLoseMessage() {...}
```

**13. class GameLoop**, ведет диалог играть/не_играть 

+ 👍 Хорошо, что диалог вынесен из класса Game в отдельный класс

- "Инициализация" это одномоментное действие по установке значений. Этот же метод не инициализирует, а создает и запускает игру, при этом игра работает по своему алгоритму
```
private void initGame(WordsListFromFile wordsListFromFile)
```

**14. class Main**, содержит точку входа main

+ 👍 Только создает и запускает игру, в данном случае- через GameLoop, это хорошо.

## АРХИТЕКТУРА

Программа, безусловно, выполнена в ООП стиле. Но к декомпозиции у меня большие вопросы.  
Единые ответственности разделены на части и эти части находятся в разных классах, перетекая друг в друга.  
Например, как в связке MistakeHandler + MaskWord + SecretWord.
Это приводит к трудному для понимания спагетти коду.

Моя версия декомпозиции:

**Маскированное слово**.  

Состояния:   
+ Отгадываемое слово  
+ Маска  

Поведение:  
+ Вернуть отгадываемое слово  
+ Вернуть маску  
+ Сказать, есть ли буква в слове  
+ Открыть букву в маске, а если такой буквы в слове нет- кинуть исключение  
+ Сказать, открыта ли маска полностью(т.е. слово отгадано)  
Все это должно находиться в отдельном классе. Кроме этого в классе не должно быть других методов.  

Не обязательно, но для тренировки декомпозиции можно сделать в виде классов еще такие сущности:

**Висельник**  
Состояния: текущая стадия повешения, максимальная стадия повешения.  
Поведение: сказать текущую стадию повешения, максимальную стадию, сказать жив или мертв, увеличить стадию повешения.  

**Хранилище букв**  
Состояние: список букв  
Поведение: вернуть список букв, добавить букву(если такая буква уже есть- кинуть исключение), сказать есть ли такая буква в хранилище  

**Остальное**
Словарь, инпутер и т.д.

## ВЫВОД

Для лучшего понимания декомпозиции в ООП стиле посмотреть ролики Сергея про шахматы. Посмотреть ролики Немчинского про SOLID.

n.78(146)  
#ревью #виселица #оопвиселица