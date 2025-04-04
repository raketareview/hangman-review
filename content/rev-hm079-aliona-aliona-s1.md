https://github.com/aliona-s1/hangman-oop  
[Aliona]

Программа в ООП стиле. Вторая реализация автора, первая была в процедурном стиле.  
Сделано хорошо, можно рекомендовать в качестве одной из образцовых.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

Не обнаружено.

## ХОРОШО

+ 👍 Игра запускается 
+ 👍 Есть список введенных букв 
+ 👍 Можно ввести только одиночную букву русского алфавита 
+ 👍 Простой понятный алгоритм

## ЗАМЕЧАНИЯ

**1. Нейминг**

+ 👍 Все ок.

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*  

**2. Текст в exception** всегда должен быть на английском языке
```
throw new RuntimeException("Файл не найден.");
```
Исключение это не просто телеграмма, которая летит сквозь слои. 
У exception особое назначение- если исключение вылетит и не будет перехвачено внутри программы, то аварийно прекратит выполнение программы.
Тогда на экране будет распечатано сообщение эксепшена, и это сообщение должно быть понятно сисадмину в любой точке планеты. 
А значит, сообщение должно быть на английском. 
Интерпритация исключения и перевод его на локальный язык должно происходить там, где это соответствует архитектуре программы или не происходить вовсе, если исключение не планируется перехватывать

**3. class Dictionary**

+ 👍 Если не считать exception'ов с русскими текстами, класс ок. 

**4. class MaskedWord**

+ 👍 Хорошо, что выявила сущность "Маскированное слово" и вынесла его в отдельный класс.

- При обновлении маски, если буквы нет в слове, нужно бросать исключение
```
public void updateMask(String letter)
```

+ 👍 Класс хороший, делает только то, что должен и не нарушает SRP
```
public class MaskedWord {
  private static final String MASKING_SYMBOL = "*";
  private final String secretWord;
  private String mask;

  public MaskedWord(String secretWord) {...}

  public String getSecretWord() {...}

  public String getMask() {...}

  public boolean isLetterInWord(String letter) {...}

  public void updateMask(String letter) {...}

  public boolean isSecretWordGuessed() {...}
}
```

**5. class Renderer**, класс для распечатки игровой информации: картинки хангмана, маска слова и т.д.

+ 👍 Отдельный класс для распечатки информации это хорошо. Своего рода view.

- При форматированном выводе для распечатки интов должен использоваться шаблон `%d`. Я удивлен, почему у тебя здесь инты распечатываются через `%s` и при этом не вылетает исключение.  
Видимо, в новых версиях явы они уже не так строго относятся к таким вещам.  
Но все равно нужно шаблоны применять осмысленно и помнить, что для вывода разных типов данных предусмотрены разные шаблоны: `%d` для интов, `%s` для строк
```
public void printGameInfo(int mistakes, int maxMistakes, List<String> inputtedLetters) {
  System.out.printf("Ошибок: %s из %s", mistakes, maxMistakes);
}

//ПРАВИЛЬНО:
public void printGameInfo(int mistakes, int maxMistakes, List<String> inputtedLetters) {
  System.out.printf("Ошибок: %d из %d", mistakes, maxMistakes);
}
```

- Этот метод нужно разделить на два, иначе вью слишком много на себя берет- не только распечатывает результаты, но и опредляет их
```
public void printResult(boolean isWin, String secretWord) {...}

//ПРАВИЛЬНО:
public void printWin(String secretWord) {...}
public void printLose(String secretWord) {...}
```

**6. class Game**, основная игровая логика

+ 👍 Принимает в конструктор достаточное количество зависимостей, это хорошо.  Из-за этого игра потенциально может использовать разные Рендереры и слова, полученные из разных источников
```
public Game(Renderer renderer, MaskedWord maskedWord, Scanner scanner) {...}
```

- Если есть метод `isWin()` то логично было бы иметь еще метод `isLose()`
```
private boolean isWin() {
  return maskedWord.isSecretWordGuessed();
}

private boolean isFinished() {
  return mistakes == MAX_MISTAKES || isWin();
}

//ЛУЧШЕ:
private boolean isWin() {
  return maskedWord.isSecretWordGuessed();
}

private boolean isLose() {
  return mistakes == MAX_MISTAKES;
}

private boolean isFinished() {
  return isLose() || isWin();
}
```

- Раз есть отдельный класс `Renderer`, который печатает не только картинку хангмана, но и всякую разную информацию общего назначения, 
то логично было бы печатать через Рендерер вообще *ВСЮ* информацию, а не так как сейчас: 
часть печатается через Рендерер, а часть печатается просто в консоль в классе Game
```
public void start() {
  System.out.println("\nНачинаем игру!");
  runGameLoop();
}

//ЛУЧШЕ:
public void start() {
  renderer.printStartMessage();
  runGameLoop();
}
```
Почему? Ну так у тебя есть Рендерер, который инжектится в Game. 
Game получает в себя Рендерер и печатает через него инфо общего назначения, при этом Game даже не знает, куда конкретно эта инфо печатается- в консоль, в логи или еще куда-то.

Допустим, ты сделаешь наследника Рендерера, который переопределит методы и будет не печатать в консоль, а выводить инфо на табло игрового автомата через интерфейс RS-485. 
Ты этого наследника заинжектишь в Game в качестве `Renderer renderer` и Game будет так же корректно печатать инфо, но уже не в консоль, а в другое устройство. 
Но в этом случае часть информации пользователь игрового автомата увидит на табло автомата, а часть нет. 
Ибо часть инфо напечается в консоль, а физически эта консоль будет находиться в другом помещении, нежели этот игровой автомат.

**7. class Main**, содержит точку входа main

+ 👍 Только создает и запускает игру, это хорошо.
+ 👍 Диалог с пользователем "играть/не_играть" находится в этом классе, а не в Game, это хорошо.

## ВЫВОД

Всё ок. Классов немного, но все они созданы по делу, декомпозиция хорошая, выполнена в ООП стиле.  

n.79(149)  
#ревью #виселица #оопвиселица