https://github.com/ferty460/hangman  
[жесть, конечно]

```
Я воспользовался чатом GPT в качестве ревьюера в ходе написания кода. 
```
Звучит интригующе.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Враждебный интерфейс. Пользователь волен вводить буквы так, как ему удобно. Задача программиста- написать алгоритм, который сам переведет введенную букву в нужный регистр
```
Введите букву:
А
Ошибка: Только кириллица маленькими буквами
```

2. Почему две ноги засчитались одной ошибкой? Одна нога должна быть одной ошибкой, вторая нога- второй ошибкой
```
________
|      |
|      0
|     /|\
|
|
********
Количество попыток: 1
Использованные буквы: [у, к, е, н, г, а, ш]
Введите букву:
щ
К сожалению, такой буквы нет.

________
|      |
|      0
|     /|\
|     / \
|
********
```


## ХОРОШО

+ 👍 Игра запускается
+ 👍 Обширное меню
+ 🚀 Дополнительный функционал- ведение статистики, хранится в файле
+ 🚀 Дополнительный функционал- 3 уровня сложности


## ЗАМЕЧАНИЯ

**1. Нейминг**

К неймингу особых замечаний нет.

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*  

**2. Нарушение DRY**, магические буквы, числа, слова. Вводи константы.

Если к одной и той же магической штуке обращаются два разных класса, то делай ее константой в одном из этих двух классов, либо перенеси ее в третий класс
```
public enum Difficulty {
  EASY("Легкий", "Слова длиной от 5 до 6 символов"),
  MEDIUM("Средний", "Слова длиной от 7 до 9 символов"),
  HARD("Сложный", "Слова длиной более 10 символов");
  //...
}

public class FileWordProvider implements WordSource {
  //..
  case EASY -> word.length() >= 5 && word.length() <= 6;
  case MEDIUM -> word.length() >= 7 && word.length() <= 9;
  case HARD -> word.length() >= 10;
  //...
}
```

*Фаулер, "Рефакторинг", гл.8 п."Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*  

**3. Нарушение DRY**, магические буквы, числа, слова- тотально везде. Вводи константы
```
case "1" -> settings.setDifficulty(Difficulty.EASY);
case "2" -> settings.setDifficulty(Difficulty.MEDIUM);
case "3" -> settings.setDifficulty(Difficulty.HARD);
case "4" -> showActionMenu(settings);
```

**4. Текст в exception** всегда должен быть на английском языке. 
```
throw new IllegalArgumentException("Только кириллица маленькими буквами");
```

Исключение это не просто телеграмма, которая летит сквозь слои. 
У exception особое назначение- если исключение вылетит и не будет перехвачено внутри программы, то аварийно прекратит выполнение программы.
Тогда на экране будет распечатано сообщение эксепшена, и это сообщение должно быть понятно сисадмину в любой точке планеты. 

А значит, сообщение должно быть на английском. 
Интерпритация исключения и перевод его на локальный язык должно происходить там, где это соответствует архитектуре программы или не происходить вовсе, если исключение не планируется перехватывать

**5. class Settings**, класс настроек. Про такие классы я писал тут: https://t.me/zhukovsd_it_chat/53243/176984

**6. class SettingsConsoleUI**, меню для работы с настройками

- Меню в процедурном стиле, дублирование кода. Про меню в ООП стиле я писал тут: https://t.me/zhukovsd_it_chat/53243/114908

**7. Если нужно печатать текст** с более, чем одним подстановочным значением или значение вставляется внутрь сообщения, используй форматированный вывод- тогда сразу будет виден весь шаблон
```
System.out.println("\nВаш уровень сложности: " + currentDifficulty.getName() + 
    " (" + currentDifficulty.getDescription() + ")");

//ПРАВИЛЬНО:
System.out.println("\nВаш уровень сложности: %s (%s) %n", currentDifficulty.getName(), currentDifficulty.getDescription());
```

**8. Не засовывай тернарник в аргументы метода**, это читается ужасно. Не экономь строки за счет ухудшения читаемости кода
```
System.out.println("Ваш словарь: " + (isDefaultDictionary ? "по умолчанию" : dictionaryFilePath) + "\n");

//ПРАВИЛЬНО:
String dictionaryType = isDefaultDictionary ? "по умолчанию" : dictionaryFilePath;
System.out.printf("Ваш словарь: %s %n%n", dictionaryType);
``` 

**9. interface WordSource**

+ 👍 Интерфейс для получение слова это хорошо, теперь можно делать разные реализации и получать слова из файла, по сети, просто фиксированные слова из памяти.

**10.  FileWordProvider implements WordSource**

- А так все хорошо начиналось. Этот класс должен только читать слова из файла и выдать одно случайное из них.
Сейчас этот класс выполняет две ответственности, даже больше: читает слова из файла, читает слова из памяти, определяет когда откуда нужно читать, хранит в себе параметры уровней сложности(длину слов для чтения)
```
private void loadDictionary() {
  try {
    dictionary = Files.readAllLines(dictionaryFilePath);   <-- СЛОВА ИЗ ФАЙЛА
  } catch (IOException e) {
    dictionary = List.of("акула", "рюкзак", "тетрадь" ...); <-- СЛОВА ИЗ ПАМЯТИ
    throw new RuntimeException("Ошибка чтения словаря: " + e.getMessage());
  }
  dictionary = filterDictionaryByDifficult(dictionary, difficulty);
}
```

Класс нужно разделить на два: один для выдачи слова из файла, другой для выдачи слова из памяти- пять фиксированных слов. 
Определять, когда кого из них нужно юзать, должен вызывающий код- создаешь экземпляр FileWordProvider, если он вылетает- создаешь экземпляр InMemoryWordProvider.

- Знать характеристики уровней сложности игры- вообще не задача класса, который откуда-то читает слова и возвращает случайное из них
```
case EASY -> word.length() >= 5 && word.length() <= 6;
case MEDIUM -> word.length() >= 7 && word.length() <= 9;
case HARD -> word.length() >= 10;
```
В данном случае значение длины слова уровня сложности должно храниться в енаме Difficulty.

**11. interface Validator<T>**

+ 👍 Простой универсальный вылидатор, это хорошо
```
public interface Validator<T> {
  void validate(T input);
}
```

**12. class LetterValidatorImpl implements LetterValidator**

- Та же проблема с двумя ответственностями. Два вида валидации в одном классе
```
public class LetterValidatorImpl implements LetterValidator {

  @Override
  public void validate(String letter) {
    //Валидация: это одна маленькая(в нижнем решистре) русская буква 
  }

  @Override
  public void checkLetterNotUsed(char letter, List<Character> usedLetters) {
    //Валидация: буква уже использовалась и находится в списке введенных
  }
}
```

Ты зачем вводил интерфейс валидатора, да еще целых два? Наверное, чтобы делать разные реализации валидации, объединенные общим интерфейсом с целью их использования через полиморфизм.  
Вот и сделай два валидтора: один валидирует что строка эта буква, второй- что буква еще не вводилась.

**13. enum Difficulty**, уровни сложности

- Нарушение DRY, дублирование текста
```
"Слова длиной от 5 до 6 символов"
"Слова длиной от 7 до 9 символов"
```

- Нарушение SRP. Этот енам вообще не должен хранить такие тексты. Они относятся не к хранению данных, необходимых для организации уровней сложности.  
Эти тексты являются информационными сообщениями, объясняющими характеристики уровней сложности
```
"Слова длиной от 5 до 6 символов"
"Слова длиной от 7 до 9 символов"
```

- Нарушение SRP. Длина слов для каждого уровня это тоже часть ответственности этого енама, но находится эта часть сейчас в Файловом Провайдере Слов.  
Все должно быть ровно наоборот: енам должен хранить длину слов каждого уровня, а словопровайдер должен читать эти данные из енама уровня.

**14. class HiddenWord**

+ 👍 Хорошо, что выявил сущность "скрытое слово" и сделал его классом. Класс выглядит почти идеальным.

- Делай код более понятным, вводи поясняющие переменные
```
this.revealedWord = new StringBuilder("*".repeat(word.length()));

//ПРАВИЛЬНО:
String mask = "*".repeat(word.length());
this.revealedWord = new StringBuilder(mask);
```

- Магическое: '*'

- Две ответственности: проверить наличие буквы в слове и открыть букву в слове 
```
public boolean guessLetter(char letter) {
  boolean isLetterFound = false;
  for (int i = 0; i < word.length(); i++) {
    //открывает букву если она есть
  }
  return isLetterFound; <-- сообщает есть ли в слове буква
}

//ПРАВИЛЬНО:
public boolean containsLetter(char letter);
public void openLetter(char letter);  //бросить исключение если пытаются открыть букву отсутствующую в слове 
```

**15. interface GameOutput**

+ 👍 Интерфейс для показа информации юзеру. Фактически view. Прекрасно, можно сделать разные реализации- печатать в консоль, выводить инфо на табло игрового автомата и т.д.

**ConsolePrinter implements GameOutput**, выводит инфо в консоль

- Нарушение SRP. Принтер берет на себя ответственность по определению результатов игры. Принтер не должен знать правила игровой логики, он же принтер
```
public void printGameResult(HiddenWord hiddenWord) {
  String word = hiddenWord.getWord();
  if (hiddenWord.isWordGuessed()) {  <-- определяет результаты
    System.out.println("Вы угадали слово: " + word + "\n");  <-- печатает результаты
  } else {
    System.out.println(HangmanStage.STAGE_6.getHangman());  <-- знает, что хангман умирает на 6 стадии повешения
    System.out.println("К сожалению, вы проиграли. \nЗагаданное слово: " + word + "\n");
  }
}

//ПРАВИЛЬНО- ПРОСТО ПЕЧАТАТЬ, ЧТО СКАЖУТ:
public void printWinMessage();   <-- печатает результаты
public void printLoseMessage();  <-- печатает результаты
```

- Нарушение SRP. (1) Почему принтер решает, какой словарь дефолтный, а какой нет? (2) Определение типа словарей по имени пути к файлу- похоже на костыль
```
public void printSettingsState(Settings settings) {
  String dictionaryFilePath = settings.getDictionaryFilePath().toString();  
  //...  
  System.out.println("Ваш словарь: " +
    (isDefaultDictionary ? "по умолчанию" : dictionaryFilePath) + "\n");
}    
```

- В любом switch-case должен быть default, в данном случае default должен бросать исключение с сообщением, что такого номера картинки не существует
```
case 1 -> HangmanStage.STAGE_5.getHangman();
default -> "";
```

- Распечатка картинки висельницы через switch-case или if-elseif - индусский код. 
Картинки нужно хранить в статическом массиве и печатать по номеру, например, так
```
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

public void printPicture(int numPicture) {  
  System.out.println(PICTURES[numPicture]);
}
```

**16. class Game**

+ 👍 Так как настроек игры много, принимает в конструктор их не по отдельности, а целым объектом, это хорошо: `public Game(Settings settings)`.

- Нарушение DI. Не принимает в конструктор принтер, а создает его самостоятельно
```
public Game(Settings settings) {
  //...
  printer = new ConsolePrinter();
}

//ПРАВИЛЬНО:
public Game(Settings settings, GameOutput gameOutput) {
  //...
  this.gameOutput = gameOutput;
}
```
Тем самым лишает всякого смысла существование интерфейс `GameOutput`. 

Нужно передавать интерфейс `GameOutput` в конструктор `Game`. Тогда можно в разных майнах создавать разные игровые конфигурации- передаватьв Game разные принтеры.
Например, обычный принтер и цветной. Код Game при этом бы не менялся.

**17. class GameLauncher**, запускатор игры

- Традиционные для проекта магические цифры.

**18. class Main**, содержит точку входа main

+ 👍 Только создает и запускает Игру, в данном случае через Запускатор, это хорошо.

## АРХИТЕКТУРА

Безусловно, это ООП стиль. Но местами здесь кроме нормального ООП есть лишь его имитация, без понимания втутренней сути.  
Это реализация валидаторов, слишком умный принтер, интерфейс без возможности пользоваться его потенциальными выгодами и т.д.

## ВЫВОД

В целом почти хорошо. Посмотреть ролики Немчинского про SOLID.

n.74(139)  
#ревью #виселица