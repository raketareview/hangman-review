https://github.com/Purelesss/hangman  
[Pureless]

Игра в процедурном стиле, выполнена в нескольких классах.

```
сделал проект Виселица в ООП-стиле
```
Это заблуждение.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Игра не запускается
```
1. Начать новую игру
2. Выйти из приложения
1
Exception in thread "main" java.lang.NullPointerException: Cannot invoke "String.length()" because "inGameWord" is null
	at com.GameLogic.createMaskedWord(GameLogic.java:30)
```

2. Нет списка введенных букв 

## ХОРОШО

+ 👍 Можно ввести только одиночную букву русского языка
+ 👍 Простой понятный алгоритм

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Избыточный контекст
```
private final String inGameWord;
String getRandomInGameWord()

//ПРАВИЛЬНО:
private final String word;
String getRandomWord()
```

- При прочих равных давай лаконичные названия. 
Подробности в названии о том, что эта буква была получена путем ввода от юзера- здесь лишние. 
Ежу понятно, что от юзера
```
String userInputtedLetter = UI.inputLetter();

//ЛУЧШЕ:
String letter = UI.inputLetter();
```

- Название обманывает. Метод не определяет то, что юзер интерфейс находится в игре. Метод выполняет один раунд игры
```
void inGame(UserInterface UI)
```

- С большой буквы пишутся только классы, а не их экземпляры
```
UserInterface UI = new UserInterface();

//ПРАВИЛЬНО:
UserInterface ui = new UserInterface();
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*  

**2. Никогда не возвращай null**
```
private String getRandomInGameWord() {
  try {
    //...
  } catch (IOException exception) {
    return null;
  }
}
```
Возврат null повышает риск возникновения NullPointerException в программе.  
*Мартин, "Чистый код", гл.7.7-8*  
*Ютуб, Немчинский "Почему нельзя возвращать NULL?"* 


**3. Нарушение DRY**, магические буквы, числа, слова. Вводи константы
```
System.out.println("1. Начать новую игру");
System.out.println("2. Выйти из приложения");
if (userChoose.equals("1") || userChoose.equals("2")) {...}

//ПРАВИЛЬНО:
private final static String START = "1";
private final static String QUIT = "2";

System.out.println(START + ". Начать новую игру");
System.out.println(QUIT + ". Выйти из приложения");
if (userChoose.equals(START) || userChoose.equals(QUIT)) {...}
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*  

**4. Нарушение DRY**, магические буквы, числа, слова. Вводи константы.

Если к одной и той же магической штуке обращаются два разных класса, то делай ее константой в одном из этих двух классов, либо перенеси ее в третий класс- эти данные должны быть синхронизированы между собой
```
public class Main {
  static void startGame() {
    //...  
    case 1 -> inGame(UI);
    case 2 -> System.exit(0);
    //...  
  }
}

public class UserInterface {
  int mainMenu() {
    //...
    System.out.println("1. Начать новую игру");
    System.out.println("2. Выйти из приложения");
    if (userChoose.equals("1") || userChoose.equals("2")) {...}
    //...      
  }
}
```

**5. Если в блоке if есть return**(break, continue, throw, exit и т.д.), то else не пишется.  
В этом случае неважно, будет else или нет, так как программа будет работать одинаково и код без `else` будет выглядеть читабельней
```
if (userInputLetter.matches("[а-яА-ЯёЁ]")) {
  return userInputLetter;
} else {
  printError("Некорректный ввод, попробуйте еще раз");
}

//ПРАВИЛЬНО:
if (userInputLetter.matches("[а-яА-ЯёЁ]")) {
  return userInputLetter;
} 
printError("Некорректный ввод, попробуйте еще раз");
```

**6. Реакция на ошибки**

В проекте предусмотрено, что может возникнуть исключение при чтени файла со словами.  
В этом случае проект просто игнорирует исключение и нигде выше не обрабатывает сложившуюся ситуацию
```
private String getRandomInGameWord() {
  Random random = new Random();
  try {
    List<String> word = Files.readAllLines(Paths.get("words.txt"));
    return word.get(random.nextInt(word.size()));
  } catch (IOException exception) {
    return null;
  }
}
```
В данном случае такой способ реагирования на ошибку неправилен. Потому что если файл действительно будет отсутствовать, то все равно вылетит исключение, хотя и по другой причине
```
Exception in thread "main" java.lang.NullPointerException: Cannot invoke "String.length()" because "inGameWord" is null
	at com.GameLogic.createMaskedWord(GameLogic.java:30)
```
Здесь должна быть другая реакция на отсутствие файла со словами.  
Нужно сказать юзеру, что файл отсутствует и поэтому работа программы будет завершена. 
И корректно завершить работу программы без вываливания красных эксепшенов в консоль.

**7. Используй классы через их интерфейсы**
```
ArrayList<String> enteredLetters = new ArrayList<>();

//ПРАВИЛЬНО:
List<String> enteredLetters = new ArrayList<>();
```

**8. class UserInterface**

+ 👍 В целом, класс хороший даже с точки зрения ООП.

Здесь в основном простые маленькие методы, которые реализуют логику ввода-вывода и не реализуют бизнес логику.

- Картинки виселицы сделай константами.

**9. class GameLogic**, игровая логика, не использующая представление 

+ 👍 Короткие методы.

- Нарушение инкапсуляции. Не забывай явно указывать область видимости методов: публичные, протектед или приват.

- Буква это символ, метод открытия буквы должен принимать в себя символ буквы, а не строку
```
void openLetter(String letter)

//ПРАВИЛЬНО:
void openLetter(char letter)
```

- Метод открытия буквы должен бросать исключение, если пытаются открыть букву, которой нет в слове. 
Потому что попытка открытия несуществующей буквы это баг алгоритма, который нужно сразу выявить и устранить, а не маскировать.

- Побочные эффекты. 

Судя по названию метод должен проверить, есть ли буква в слове. 
Но кроме этого метод открывает букву и добавляет букву в список введенных. То есть, совершает дейсвия, не предусмотренные контрактом метода
```
boolean letterInWordCheck(String letter) {
  if (inGameWord.toLowerCase().contains(letter.toLowerCase())) {
    openLetter(letter);  <-- ПОБОЧНЫЙ ЭФФЕКТ
    enteredLetters.add(letter.toLowerCase());  <-- ПОБОЧНЫЙ ЭФФЕКТ
    return true;
  }
  enteredLetters.add(letter.toLowerCase());  <-- ПОБОЧНЫЙ ЭФФЕКТ
  return false;
}
```
Метод нужно разделить на несколько, каждый из которых будет выполнять только одну задачу.  
*Мартин "Чистый код", гл.3, "Избавьтесь от побочных эффектов"*  
*Фаулер "Рефакторинг", гл.6, "Извлечение метода"*  

**10. class Main**, содержит точку входа `main()`. Игровая логика, использующая представление 

- Если программа позиционируется как ООП, то этот класс, содержащий `main()`, должен только конфигурировать и запускать игру. 

Остальная бизнес логика приложения(за исключением диалога "играть или выйти") должна находиться в других классах. 
Здесь же находится часть игровой логики: метод `inGame(UserInterface UI)` и проч.  
*Про особенности использования main ищи соответствующую главу в "Чистом коде"* 

+ 👍 Из дополнительной логики здесь еще находится диалог "играть или выйти"- это правильно. 

Но при выборе "играть" нужно просто создавать и запустить игру, а не реализовывать часть игровой логики. 
То есть, должно быть примерно так:
```
static void startGame() {
  UserInterface ui = new UserInterface();
  while (true) {
    switch (UI.mainMenu()) {
      case START -> {
          Game game = new Game(ui);
          game.start();
        };
      case QUIT -> System.exit(0);
    }
  }
}
```

Если хочется иметь что-то типа контроллера, который в себе будет содержать логику игрового процесса, которая(логика) будет использовать UI, то создай отдельный класс, например `GameConroller`. 
Тогда в проекте будет два класса, отвечающие за игровую логику, где один класс будет содержать логику без использования ввода-вывода, а другой- с использованием оного.  
Этот контроллер не должен содержать точку входа `main()`.

## АРХИТЕКТУРА

Для того, чтобы считать программу объектно-ориентированной, недостаточно просто разделить ее на три класса, один из которых(на самом деле два) отвечает за логику, а второй- за ввод-вывод.
Программа в ООП стиле должна быть декомпозирована по правилам ООП и использовать ООП подход для реализации типичных задач.  
Здесь этого нет.

Здесь есть хорошая идея разбить программу на три слоя: представление(UserInterface); игровую логику, не использующую представление(GameLogic) и игровую логику, которая использует представление(Main).  
Но здесь не выявлены базовые сущности, а без них разделение на слои само по себе не делает ООП, а делает хорошо структурированый процедурный проект.

Я мог бы тебе предложить пример декомпозиции этой программы на классы в ООП стиле, как часто делаю, но не буду.
Потому что вижу, что с идеями декомпозиции ООП ты не знаком, хотя с алгоритмизацией в процедурном стиле все ок.  
Поэтому предлагаю для начала самостоятельно ознакомиться с принципами ООП декомпозиции.

## ВЫВОД

Посмотреть ролики Сергея про шахматы. Посмотреть ролики Немчинского про SOLID.

Сравнить различия между ООП и процедурным стилями:  
Стрим Сергея [Крестики-нолики в процедурном стиле](https://www.youtube.com/watch?v=PPikj1qHxrA)  
Мой стрим [Крестики-нолики в ООП стиле](https://t.me/zhukovsd_it_chat/53243/187097)

n.98(185)  
#ревью #виселица 