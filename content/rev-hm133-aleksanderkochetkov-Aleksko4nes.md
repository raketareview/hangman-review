https://github.com/Aleksko4nes/Hangman  
[Aleksander Kochetkov]

Игра в процедурном стиле, выполнена в нескольких классах.  
В целом, норм.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Можно ввести не одну букву, а набор букв и это будет считаться допустимым вводом

2. "Угаданное слово" это то слово, которое угадали. Если слово угадывали, но не угадали, то это слово "угадываемое"
```
Слово - К _ Н _ 
Увы и ах... Вы проиграли!
Угаданное слово - КОНЬ
```

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Есть список введенных букв
+ 👍 Простой понятный алгоритм
+ 🚀 Несколько уровней сложности

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Для одной концепции используй одно название. Оба метода печатают сообщения, а потому оба должны быть или "display", или "show"
```
public static void displayUsedLetters (List<Character> usedLetters)
public static void showFailCounter(int failCounter)
```

- Слова-паразиты. В данном случае, это слово `processor`
```
class GameProcessor
class HangmanProcessor

//ЛУЧШЕ, учитывая их функционал:
class Game
class View
```

- КОНСТАНТЫ_НУЖНО_ПИСАТЬ_СТИЛЕМ_UPPER_SNAKE
```
private static final String[] processViewer

//ПРАВИЛЬНО:
private static final String[] PROCESS_VIEWER
```

- UPPER_SNAKE только для констант, а это не константа. Константы это поля класса, а не метода
```
public void play() {
  int MAX_TRIES = 6;
  //...
}

//ПРАВИЛЬНО:
public void play() {
  int maxTries = 6;
  //...
}
```

- Здесь хранятся картинки виселицы
```
private static final String[] PROCESS_VIEWER

//ПРАВИЛЬНО:
private static final String[] PICTURES
```

- Массивы, коллекции, сеты и другие перечни нужно называть во множественном числе
```
char[] guessedLetter

//ПРАВИЛЬНО:
char[] guessedLetters
```
*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Нарушение DRY**, магические буквы, числа, слова. Вводи константы 
```
System.out.println("1 - Легкий (короткие слова)");
System.out.println("2 - Средний (средние слова)");
System.out.println("3 - Сложный (длинные слова)");
System.out.print("Ваш выбор (1-3): ");

case "1": //...
case "2": //...
case "3": //...

//ПРАВИЛЬНО:
private final static int LOW = 1;
private final static int MIDDLE = 2;
private final static int HIGH = 3;

System.out.println(LOW + " - Легкий (короткие слова)");
System.out.println(MIDDLE + " - Средний (средние слова)");
System.out.println(HIGH + " - Сложный (длинные слова)");
System.out.printf("Ваш выбор (%d-%d): ", LOW, HIGH);

case "1": //...
case "2": //...
case "3": //...

```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*  

**3. Реакция на ошибки** 

- Если по указанному адресу в `class WordLoader` не будет найден файл со словами, то программа аварийно вылетит и напечатает непонятные для юзера сообщения в консоль
```
Выберите уровень сложности:
1 - Легкий (короткие слова)
2 - Средний (средние слова)
3 - Сложный (длинные слова)
Ваш выбор (1-3): 1
Exception in thread "main" java.lang.RuntimeException: File not found in classpath: easy_words.txt_
	at WordLoader.loadWords(WordLoader.java:24)
```
Здесь должна быть другая реакция на отсутствие файла со словами.  
Нужно сказать юзеру, что файл со словами по указанному пути открыть не удалось и поэтому работа программы будет завершена. 

Причем, путь нужно указать не относительный, как у тебя(`easy_words.txt`). 
А абсолютный, чтобы юзер знал, где конкретно ему нужно искать файл: `c:/..../easy_words.txt`.

После этого корректно завершить работу программы без вываливания красных эксепшенов в консоль.

**4. class WordLoader**

- Нарушение Low Coupling.

Это класс для чтения файла. Ему не нужно знать про концепцию уровней сложности. 
Единственное, что ему нужно знать для своей работы- путь к файлу, который нужно прочитать
```
public WordLoader(Difficulty difficulty) 

//ПРАВИЛЬНО:
public WordLoader(String filepath) 
```

- Если список слов будет пуст, то метод вместо корректного слова вернет "is empty": 
```
public String getWord() {
  if (words == null || words.isEmpty()) {
    return "is empty";
  }
  return words.get(new Random().nextInt(words.size()));
}
```
И юзер будет пытаться его отгадать:
```
Увы и ах... Вы проиграли!
Угаданное слово - is empty
```
Самое плохое тут то, чо увидев в конце игры `Угаданное слово - is empty`, юзер почувствует себя одураченным. 
Он-то вводил русские буквы и был уверен, что отгадывает русское слово...  
А оказалось, что он отгадывал фразу, отгадать которую не имел ни малейшего шанса.

Здесь должна быть другая реакция:  
Список со словами может быть пустым только при нештатной ситуации.  
А значит, в этом случае нужно выдть сообщение с описанием этой нештатной ситуации и корректно завершить работу программы.

**5. class HangmanProcessor**, класс для распечатки сообщений. Что-то типа View

+ 👍 Картинки хранятся в массиве-константе, это хорошо.
+ 👍 Маленькие методы, которые только печатают информацию и не содержат логику более высокого порядка.

**6. class GameProcessor**

- Если есть отдельный класс для распечатки сообщений, `HangmanProcessor`, то вся распечатка должна идти через него
```
System.out.println("Добро пожаловать в игру!");
System.out.println("Ваша сложность - " + difficulty);
System.out.println("Слово состоит из " + word.length() + " букв.");

//ПРАВИЛЬНО:
HangmanProcessor.displayTitle(difficulty, word);
```

- Создавай вспомогательные методы, делай программу более простой и понятной 
```
while (failCounter < MAX_TRIES && !isWordGuessed()) {
  //...      
}

//ПРАВИЛЬНО:
while (!isGameOver()) {
  //процесс игры
}

boolean isWin() {...}

boolean isLose() {
  return failCounter >= MAX_TRIES;  
}

boolean isGameOver() {
  return isWin() || isLose();  
}
```

- Нарушение DRY
```
if (isWordGuessed()) {
  System.out.println("Поздравляем вы выйграли!");
  System.out.println("Угаданное слово - " + word);
} else {
  System.out.println("Увы и ах... Вы проиграли!");
  System.out.println("Угаданное слово - " + word);
}

//ПРАВИЛЬНО:
if (isWordGuessed()) {
  System.out.println("Поздравляем вы выйграли!"); 
} else {
  System.out.println("Увы и ах... Вы проиграли!");
}
System.out.println("Угаданное слово - " + word);
```

- Создавай вспомогательные методы
```
private void processLetter(char letter) {
  if (word.indexOf(letter) >= 0) {
    for (int i = 0; i < word.length(); i++) {
      if (word.charAt(i) == letter) {
        guessedLetters[i] = letter;
      }
    }
  }
   //...
}

//ПРАВИЛЬНО:
private void processLetter(char letter) {
  if (isWordLetter(letter)) {
    openLetter(letter);
  }
  //...
}
```

**7. class Main**, содержит точку входа main

+ 👍 Только создает и запускает игру `GameProcessor`, это хорошо.
+ 👍 Тут, а не в `GameProcessor`, происходит диалог с юзером для выбора уровня сложности, это хорошо.

## АРХИТЕКТУРА

Несмотря на то, что в проекте несколько классов, он написан в процедурном стиле. 
Программа в ООП стиле должна быть декомпозирована по правилам ООП и использовать ООП подход для реализации типичных задач.  
Здесь не видно ООП декомпозиции.

Если так и планировалось, то ок.

Если нет, то сравнить различия между ООП и процедурным стилями:  
Стрим Сергея [Крестики-нолики в процедурном стиле](https://www.youtube.com/watch?v=PPikj1qHxrA)  
Мой стрим [Крестики-нолики в ООП стиле](https://t.me/zhukovsd_it_chat/53243/187097)

## ВЫВОД

Есть простейшие ошибки при наименовании констант- изучить *Oracle Java code conventions*.  
В целом, для процедурного стиля норм.

n.133(245)  
#ревью #виселица 