https://github.com/WayneHays/hangman_game  
[Wayne Hays]

Большой, сложный проект, сделанный в процедурном стиле.  
Местами есть проблески ООП, но общую картину это не меняет.  
Есть расширенный функционал.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Одна и та же буква в разных регистрах считается двумя разными буквами, увеличивает счетчик ошибок и дублируется в списке ошибочно введенных букв
```
Буква: щ
  +---+
  |   |
  O   |
      |
      |
      |
=========

Слово: *********
Ошибки(1 из 6):щ
Буква: Щ
  +---+
  |   |
  O   |
  |   |
      |
      |
=========

Слово: *********
Ошибки(2 из 6):щЩ
```

2. Было бы неплохо разделить ошибочно введенные буквы запятыми и отделить их пробелом от двоеточия
```
Ошибки(5 из 6):аАлшщ
```

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву текущего алфавита
+ 👍 Есть список ошибочных букв
+ 🚀 Есть языковая локализация: русский и английский языки интерфейса и отгадываемых слов
+ 🚀 Три уровня сложности

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Название вводит в заблуждение. Это не валидарор, это ввод буквы
```
class InputValidator

//ПРАВИЛЬНО:
class LetterInput
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*  

**2. Нарушение DRY**, магические буквы, числа, слова. Вводи константы
```
public String getWantToPlayMessage() {
  return "Начать игру? Да/Нет";
}

public String getExitMessage() {
  return "Нет";
}

public String getContinueMessage() {
  return "Да";
}

//ПРАВИЛЬНО:
private final static String PLAY = "Да";
private final static String QUIT = "Нет";

public String getWantToPlayMessage() {
  return "Начать игру?  %s/%s".formatted(PLAY, QUIT);
}

public String getExitMessage() {
  return QUIT;
}

public String getContinueMessage() {
  return PLAY;
}
```
*Фаулер, "Рефакторинг", гл.8 п."Замена магического числа символической константой"*   
*refactoring.guru "Замена магического числа символьной константой"*  

**3. Нарушение DRY**, магические буквы, числа, слова. Вводи константы.  
Если к одной и той же магической штуке обращаются два разных класса, то делай ее константой в одном из этих двух классов, либо перенеси ее в третий класс
```
public class RussianLanguage extends Language {
  public String getDifficultyInputHint() {
    return "Введите E, M или H";
  }
  //...
}

public class DifficultySelector {
  return switch (upperLine) {
    case "E" -> addWordLengthBoundaries(DifficultLevel.EASY, list);
    case "M" -> addWordLengthBoundaries(DifficultLevel.MEDIUM, list);
    case "H" -> addWordLengthBoundaries(DifficultLevel.HARD, list);
    default -> false;
  };
  //...
}

//ПРАВИЛЬНО:
public class RussianLanguage extends Language {
  @Override
  public String getDifficultyInputHint() {
    return "Введите %s, %s или %s".formatted(
        DifficultySelector.EASY_KEY,
        DifficultySelector.MEDIUM_KEY,
        DifficultySelector.EHARD_KEY
    );
  }
  //...
}

public class DifficultySelector {
  public final static String EASY_KEY = "E";
  public final static String MEDIUM_KEY = "M";
  public final static String HARD_KEY = "H";

  return switch (upperLine) {
    case EASY_KEY -> addWordLengthBoundaries(DifficultLevel.EASY, list);
    case MEDIUM_KEY -> addWordLengthBoundaries(DifficultLevel.MEDIUM, list);
    case  HARD_KEY -> addWordLengthBoundaries(DifficultLevel.HARD, list);
    default -> false;
  };
  //...
}
```

**4. Создавай вспомогательные методы**, делай программу более простой и понятной
```
//миллион строк кода
System.out.println(language.getWordMistakesMessage() + gameState.getMistakesCount() + language.getWordOfMessage()
   + DigitsConstants.COUNT_MISTAKES.getValue() + language.getEndOfLine() + gameState.getWrongLetters());
//миллион строк кода

//ПРАВИЛЬНО:
//миллион строк кода
printНазваниеКотороеВсеОбъясняет(); 
//миллион строк кода

private void printНазваниеКотороеВсеОбъясняет() {
  System.out.println(language.getWordMistakesMessage() + gameState.getMistakesCount() + language.getWordOfMessage()
   + DigitsConstants.COUNT_MISTAKES.getValue() + language.getEndOfLine() + gameState.getWrongLetters());
}
```

**5. Слишком длинные конструкции**, которые трудно понять. Не старайся все засунуть в одну строку в ущерб читабельности, вводи поясняющие переменные
```
this.encryptedWord = new StringBuilder(word.replaceAll(".", StringConstants.ENCRYPTOR.getValue()));
```

**6. Пакет languages**, языковая локализация

+ 👍 Отдельный абстрактный класс для языковых ресурсов и две его реализации под конкретные языки это хорошо.
Конечно, есть стандартные простые средства локализации через использование properties, но в данном случае цель состоит в тренировке построения архитектуры, поэтому ок.

**7. abstract class Language**

- Хранение скобочки и расширения файла не относится к ответственности Языка
```
public String getEndOfLine() {  return "):"; }

public String getPostfix() { return ".txt"; }
```

**8. class RussianLanguage extends Language**

- Сделай метод, который возвращает не откусанную половину фразы + еще миллион методов с кусками этой фразы. А метод, возвращающий всю фразу в виде шаблона
```
public String getWordMistakesMessage() { return "Ошибки("; }
public String getWordOfMessage() { return " из "; }
public String getEndOfLine() { return "):"; }

//ПРАВИЛЬНО:
public String getWordMistakesMessageTemplate() {
  return "Ошибки(%d из %d): ";
}
```

И тогда вместо
```
System.out.println(language.getWordMistakesMessage() + gameState.getMistakesCount() + language.getWordOfMessage()
  + DigitsConstants.COUNT_MISTAKES.getValue() + language.getEndOfLine() + gameState.getWrongLetters());
```
Будет
```
System.out.printf(language.getWordMistakesMessageTemplate(), 
  gameState.getMistakesCount(),
  DigitsConstants.COUNT_MISTAKES.getValue());

System.out.println(gameState.getWrongLetters());
```

**9. Пакет utils**

- Пакет хранит не утилиты, а константы в виде енамов. Почитай, что такое утилитный класс применительно к ООП в яве и не называй так посторонние явления. 

- enum DigitsConstants. Если коротко, это свалка. Константы левелов перенести в DifficultLevel, количество ошибок еще куда-то, а енам аннигилировать. 

- enum StringConstants. Еще свалка, но только для строк. Енам расформировать, константы перенести в те классы, в интересах которых они используются.

- enum DifficultLevel. Нормальный енам, но должен в себе хранить значения мин-макс, а не брать их из другого енама.

**10. class Dictionary**

- Зачем принимает рандом в конструктор- совершенно непонятно
```
public Dictionary(Random random) {
  this.random = random;
}
```
Просто создавай экземпляр рандома внутри класса.

- Нарушение SRP и Low Coupling. Класс имеет миллиард зависимостей на разные классы, к которым не должен иметь никакого отношения(LanguageSelector, Language), 
самостоятельно юзает эти классы и вытягивает из них нужную информацию.  
Из-за этого код класса сложен для понимания, хотя это должен был бы быть простейший файловый читатель, понятный даже младенцу.

На самом деле класс должен принимать в себя необходимые данные для чтения слов, например, имя файла, минимальный и максимальный размер слова. Примерно так
```
public Dictionary(String filename, int minWordLength, int maxWordLength) {
  //И НЕ ДЕЛАЙ Dictionary(String filename, DifficultLevel level)
  //файловый читатель не должен знать ни про какие левелы
}
```

- Нарушение SRP. Словарь должен просто прочитать из файла слова, соответствующие заданным требованиям. Словарь не должен хранить в себе язык
```
public Language getLanguage() {
  return language;
}
```

- Нарушение SRP. Класс не просто считывает информацию из текстового файла. Но и ведет диалог с юзером- просит его ввести язык, для которого будет выбран файл для загрузки
```
language = languageSelector.validateLanguageChoice(scanner);  <-- здесь юзер через клавиатуру вводит язык 
```

**11. class DifficultySelector**, класс ведет диалог с юзером и принимает от него уровень сложности.

- Разобраться в коде сложно, после третьей попытки я бросил это занятие.

- Разделяй команды и запросы. Этот метод должен или выполнить действие или выполнить запрос. Но не то и другое вместе
```
private boolean addWordLengthBoundaries(DifficultLevel difficultLevel, List<Integer> list) {
  list.add(difficultLevel.getMinWordLength());  <-- выполняет действие(команда)
  list.add(difficultLevel.getMaxWordLength());
  return true; <-- отвечает на запрос
}
```

То же самое касается и метода `processDifficultyChoice(...)`  
*Мартин, "Чистый код", гл.3, п."Разделение команд и запросов"*  

**12. class LanguageSelector**

- Очередной селектор, очередная ацкая процедурщина. Дублирование кода, при добавлении новых языков, размер класса будет расти в прогрессии
```
if (StringConstants.RUSSIAN_LANGUAGE.getValue().equalsIgnoreCase(line)) {
  language = new RussianLanguage();
  break;
} else if (StringConstants.ENGLISH_LANGUAGE.getValue().equalsIgnoreCase(line)) {
  language = new EnglishLanguage();
  break;
}
```

**13. class Painter**, рисователь хангмана

+ 👍 Тут хорошо. Хотя лист с картинками можно было бы сделать константой.

**14. class GameState**

Класс, который не объяснишь одной фразой. Итак, что это такое?  
Это класс, который содержит в себе: счетчк ошибок; мин и макс размеры слова; перечень использованных и ошибочных букв; 
текст отгадываемого слова и его маску; методы создания маски, проверки вхождения буквы в слово, открытия буквы;
проверка на проигрыш и конец игры.

Можно было бы назвать этот класс движком игры, но это не движок игры.  
В этом виде это просто божественный класс, контейнер с идеологически слабо связанной между собой информацией. Например, минимальный размер слова и проверка на проигрыш.  
С движком игры этот класс сближает то, что в нем нет использования пользовательского ввода/вывода, то есть класс не зависит от представления.

Чтобы сделать этот класс движком игры, осталось чуть-чуть: нужно создать публичный метод `сделатьХод(char letter)`, при вызове которого будет автоматически меняться состояние игрового движка:
* Открываться буква в маске, если буква есть в слове
* Увеличиваться счетчик ошибок, если нет буквы в слове и в списке введенных
* Добавлять букву в перечень введенных и/или ошибочных

Также нужно:
* Убрать лишнее: мин и макс размеры слова, эти данные не нужны движку, а значит не должны в нем храниться
* Все методы, кроме `сделатьХод(char letter)` и геттеров, должны быть приватными

Да, нужно будет придумать, как проинформировать клиентский код о результатах хода- была открыта буква или нет.  
Для этого можно либо возвращать какой-то результат, например в виде енама(буква открыта, ошибочная буква etc). 
Это проще, но дискуссионно с точки зрения принципа разделения запросов и команд.  
Либо использовать паттерн Callback, что идеологически правильнее, но сложнее.

Зачем вообще нужен отдельный движок игры? 
Это класс, который реализует основную бизнес логику приложения, которая не связана с представлением.  
Это позволит, например, использовать один и тот же движок в разных визуальных средах: для консоли, в Андроиде, Свинге и т.д. 
В каждой из этих сред останется только натянуть визуал на этот движок. При этом логика игрового процесса останется неизменной.

Нужен ли движок в хангмане? Здесь это оверинжиниринг, но если очень хочется сделать, то почему бы и не сделать.  
Другое дело, что этот "почти движок" во многом сделан из-за плохой декомпозиции приложения, о чем более подробно напишу в конце.

CallBack луноход: https://t.me/zhukovsd_it_chat/53243/139594  
CallBack разрушение: https://t.me/zhukovsd_it_chat/53243/184749

- Дублирование кода
```
public boolean isGameEnded() {
  return mistakesCount == DigitsConstants.COUNT_MISTAKES.getValue()
    || !encryptedWord.toString().contains(StringConstants.ENCRYPTOR.getValue());
}

public boolean isGameLost() {
  return mistakesCount == DigitsConstants.COUNT_MISTAKES.getValue();
}

//ПРАВИЛЬНО:
public boolean isGameEnded() {
  return isGameLost()
    || !encryptedWord.toString().contains(StringConstants.ENCRYPTOR.getValue());
}
```

- Если есть `isGameLost()`, логично было бы сделать еще `isGameWin()`

**15. class GameManager**

Класс, который натягивает консольный визуал на "почти движок" `GameState`. Я бы назвал этот класс `GameController`.

- На мой взляд, этот класс не должен сам себя инициализировать путем получения от юзера уровня сложности
```
List<Integer> list = difficultySelector.setWordLengthBoundariesForDifficultyLevel(scanner, language);
```
Этому классу вообще не нужно знать уровень сложности. Уровень сложности влияет на Словарь, а словарь GameManager получает в конструктор.
Класс не должен спрашивать у юзера никаких инициализационных команд типа выбора языка, уровня сложности, тематики слов, способа их загрузки и тому подобного.  
*Мартин, "ЧК", гл.11,п."Отделение конструирования системы от ее использования".*

- В идеале код в GameManager должен позволять создавать такие майны, чтобы можно было делать разные игровые конфигурации: 
  * C фиксированным языком и выбираемым юзером уровнем сложности 
  * C фиксированным уровнем и выбираемым языком 
  * Все параметры конфигурируются юзером

Во всех этих случаях код GameManager должен быть неизменен, а процесс ввода пользовательских данных должен происходить на уровень выше.

**16. class Main**, содержит точку входа main

+ 👍 Только создает и запускает игру, в данном случае через `GameManager`, это хорошо. 

- Плохо то, что не все конфигурационные данные для игры вводятся пользователем в этом классе. Часть данных, например Language, пользователь вводит в классе `GameManager`.

## АРХИТЕКТУРА

Проект тяжело читается, много спагетти кода. Вызвано это тем, что проект в основном написан в процедурном стиле.  
ООП можно безаговорочно засчитать только за пакетом languages.

**A-I.** 

Несмотря на то, что в проекте много классов, он написан в процедурном стиле.
Программа в ООП стиле должна быть декомпозирована по правилам ООП и использовать ООП подход для реализации типичных задач.

В проекте много классов, но декомпозированы они с точки зрения ООП часто неправильно. Например, Словарь, который просит юзера ввести язык для чтения слов из файла.

Из невыявленных базовых сущностей самая очевидная здесь это **Маскированное слово**.  

Оно имеет состояния:
+ Секретное слово  
+ Маска  

И поведение:
+ Вернуть секретное слово  
+ Вернуть маску  
+ Сказать, есть ли буква в слове  
+ Открыть букву в маске, а если такой буквы в слове нет- кинуть исключение  
+ Сказать, открыта ли маска полностью(т.е. слово отгадано)  

Все это должно находиться в отдельном классе. Кроме этого в классе не должно быть других методов. 

Не обязательно, но для тренировки декомпозиции можно сделать в виде классов еще такие сущности:

**Висельник**  
Состояния: текущая стадия повешения, максимальная стадия повешения.  
Поведение: сказать текущую стадию повешения, максимальную стадию, сказать жив или мертв, увеличить стадию повешения.  
Не путать с Рендерером висельника.

**Хранилище букв**  
Состояние: список букв  
Поведение: вернуть список букв, добавить букву(если такая буква уже есть- кинуть исключение), сказать есть ли такая буква в хранилище.  

Возможно, если вынести эти сущности в отдельные классы, то станет меньше причин для разделения Игры на GameManager и GameState.

**A-II.**

Усложнения должны работать так:
* Сначала получить от юзера язык
* На основе языка создать ресурс с текстами на нужном языке
* Получить от юзера уровень сложности
* Создать словарь с выбранным языком и длиной слов, которые заданы уровнем сложности
* Создать валидатор букв на основе выбранного языка
* Cоздать контроллер игры и передать в него словарь, валидатор, текстовый ресурс, рисователь
* Контроллер внутри себя создает движок и юзает его

Стартовать этот процесс в моем представлении должен как-то так
```
public class Main {
  public static void main(String[] args) {

    String lngTitle = "Input language (%s, %s): ".formatted(Language.RU, Language.EN);
    String lngFailMessage = "Illegal input";
    String[] lngKeys = {Language.RU.name(), Language.EN.name()};

    String languageKey = input(lngTitle, lngFailMessage, lngKeys);
    Language language = Language.valueOf(languageKey.toUpperCase());

    MessageCenter messageCenter = MessageCenterFactory.get(language); //языковой ресурс

    //...
    GameLevel level = //...

    String filename = language.name() + "_words.txt"; //напр. "RU_words.txt"
    Dictionary dictionary = new Dictionary(filename, level.minLength, level.maxLength);
    
    GameController gameController = new gameController(dictionary, messageCenter, validator, painter);
    gameController.start();
  }

  private static String input(String title, String failMessage, String... keys) {
    Scanner scanner = new Scanner(System.in);

    while (true) {
      System.out.println(title);
      String s = scanner.nextLine();

      for(String key : keys) {
        if(key.equalsIgnoreCase(s)) {
          return s;
        }
      }
    }
  }
}

enum Language {
  RU,
  EN
}

enum GameLevel {
  EASY(0, 5),
  MEDIUM(8, 10);

  public final int minLength;
  public final int maxLength;

  public GameLevel(int minLength, int maxLength) {
    this.minLength = minLength;
    this.maxLength = maxLength;
  }

}
```

Альтернативня конфигурация с фиксированными данными, чтобы каждый раз не париться с вводом
```
public class RuMediumStarter {
  public static void main(String[] args) {

    Language language = Language.RU;

    MessageCenter messageCenter = MessageCenterFactory.get(language); //языковой ресурс

    //...
    GameLevel level = GameLevel.MEDIUM;

    String filename = language.name() + "_words.txt"; //"RU_words.txt"
    Dictionary dictionary = new Dictionary(filename, level.minLength, level.maxLength);
    
    GameController gameController = new gameController(dictionary, messageCenter, validator, painter);
    gameController.start();
  }

}
```
Более подробно про локализацию я писал тут: https://t.me/zhukovsd_it_chat/53243/119188

## ВЫВОД

Для лучшего понимания декомпозиции посмотреть ролики Сергея про шахматы. Посмотреть ролики Немчинского про SOLID.  
У Сергея есть расширенные материалы по хангману, там несколько часов видео, где он подробно объясняет мативацию при создании классов, в том числе уровни сложности в игре.  
При желании можно приобрести доступ к этим материалам, в течении недели каждого месяца они продаются с 50% скидкой.  

n.81(151)  
#ревью #виселица #движок #локализация