https://github.com/Ciltonn/hangman-game  
[Елена Казакова]

Простая процедурная реализация, выполнена в одном классе.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Неправильная последовательность в порядке отображения введенных букв: букву 'ц' я ввел второй, но в распечатке она стала первой.
При этом введенная третьей буква 'к' в распечатке стала на третью позицию
```
й
Введенные буквы: [й]

ц
Введенные буквы: [ц, й]

к
Введенные буквы: [ц, й, к]
```
Введенные буквы должны размещаться по алфавиту(й, к, ц) либо в порядке ввода(й, ц, к).

2. Это кириллическая буква
```
Введите букву
ё
Введите кириллическую букву
```

3. Негодяя уже повесили, но игра продолжается
```
______
|    |
|    0
|   /|\
|   / \
|
========

Загаданное слово: ______
Ошибок: 5/6
Введите букву
```

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву русского языка
+ 👍 Есть список введенных букв

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Сеты, коллекции, массивы должны называться во множественном числе
```
Set<Character> usedLetter = new HashSet<>()

//ПРАВИЛЬНО:
Set<Character> usedLetters = new HashSet<>()
```

- Название обманывает. Метод не проверяет данные, метод осуществляет ввод данных
```
int choice = checkMenuChoice(scanner);

//ПРАВИЛЬНО:
int choice = inputMenuChoice(scanner);
```

- Название должно как можно лучше объяснять суть явления. Это не выбор, это команда
```
if (choice == END) {
  //выход из игры  
}

//ЛУЧШЕ:
if (command == END) {
  //выход из игры  
}
```

- В названии переменных не пиши тип данных, к которым они относится. И вообще не употребляй венгерскую ноттацию
```
char[] arrayLetters = mask.toCharArray();

//ПРАВИЛЬНО:
char[] letters = mask.toCharArray();
```
*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*  

**2. Нарушение DRY**, магические буквы, числа, слова. Вводи константы. А если они уже есть- пользуйся 
```
private static final int START = 1;
private static final int END = 2;

System.out.println("Для запуска игры введите 1, для выхода введите 2");
System.out.println("Неверный ввод. Введите только 1 или 2");

//ПРАВИЛЬНО:
private static final int START = 1;
private static final int END = 2;

System.out.printf("Для запуска игры введите %d, для выхода введите %d \n", START, END);
System.out.printf("Неверный ввод. Введите только %d или %d \n", START, END);
```

Другие магические штуки в проекте: "_", 'а', 'я'.

*Фаулер, "Рефакторинг", гл.8 п."Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*  

**3. При прочих равных, нужно использовать примитивный тип**, а не класс-обертку
```
private static Integer checkMenuChoice(Scanner scanner) {
  int choice = scanner.nextInt();
  //...
  return choice; 
}

//ПРАВИЛЬНО:
private static int checkMenuChoice(Scanner scanner) 
```
Здесь нет оснований для того, чтобы метод вместо int возвращал Integer.

**4. Если нужно печатать или создавать строку** с более, чем одним подстановочным значением или значение вставляется внутрь сообщения, используй форматированный вывод- тогда сразу будет виден весь шаблон
```
System.out.println("Ошибок: " + mistakes + "/" + MAX_MISTAKES);

//ПРАВИЛЬНО:
System.out.printf("Ошибок: %d/%d \n" mistakes, MAX_MISTAKES);
```

**5. Если в блоке if есть return**(break, continue, throw, exit и т.д.), то else не пишется - в этом случае неважно, будет else или нет, так как программа будет работать одинаково и код выглядит читабельней
```
if (choice == END) {
   System.out.println("До свидания.");
   break;
} else {
  words = getDictionary();
  //еще миллион строк
}

//ПРАВИЛЬНО:
if (choice == END) {
   System.out.println("До свидания.");
   break;
} 
words = getDictionary();
//еще миллион строк
```

**6. Создавай вспомогательные методы**, делай программу более простой и понятной
```
if (mistakes >= MAX_MISTAKES) {
  System.out.println("Вы проиграли. Загаданное слово: " + secretWord);
  break;
} if (mask.equals(secretWord)) {
  System.out.println("Вы выйграли!");
  break;
}

//ЛУЧШЕ:
if (isLose()) {
  printLoseMessage();
  break;
} 
if (isWin()) {
  printWinMessage();
  break;
}

private static boolean isLose() {
  return mistakes >= MAX_MISTAKES;  
}
```

**7. Разделяй команды и запросы**. Этот метод должен или выполнить действие, то есть открыть букву в маске. Или вернуть маску, то есть выполнить запрос. Но не то и другое вместе
```
 private static String openLatterInMask(char letter) {
  char[] arrayLetters = mask.toCharArray();

  for (int i = 0; i < secretWord.length(); i++) {
    if (secretWord.charAt(i) == letter) {
      arrayLetters[i] = letter;
    }
  }
  return mask = new String(arrayLetters);  
  //mask = new String(arrayLetters);  <-- ВЫПОЛНЕНИЕ КОМАНДЫ
  //return mask  <-- ВЫПОЛНЕНИЕ ЗАПРОСА
}
```

В данном случае это тем более бессмысленно потому что маска у тебя выставляется два раза подряд
```
main(String[] args) {
  //...
  mask = openLatterInMask(letter);  <-- (2)
}

private static String openLatterInMask(char letter) {
  //открытие буквы в маске
  return mask = new String(arrayLetters);  <-- (1)
}
```

Метод должен просто открывать букву, то есть выполнять команду вот так:
```
main(String[] args) {
  //...
  openLatterInMask(letter);
}

private static void openLatterInMask(char letter) {
  //открытие буквы в маске
}
```
*Мартин, "Чистый код", гл.3, п."Разделение команд и запросов"*  

**8. Картинки хангмана**

+ 👍 Хранение картинок в массиве это хорошо.

- Массив с картинками нужно вынести из метода и сделать этот массив константой.  
Если массив с картинками находится в методе, значит этот объект(массив это объект) пересоздается каждый раз при вызове метода.

- Массив с картинками и метод их распечатки лучше вынести в отдельный класс, процедурный стиль дозволяет использовать несколько классов в проекте.
Тогда в основном классе не будет смешиваться логика игры и графика. 

**9. Точка входа main()**

+ 👍 Хорошо, что в проекте только один публичный метод и это `main()`, тем самым соблюдается инкапсуляция.

- В майне нужно только спросить у юзера будет он играть или нет. Алгоритм игры нужно вынести в отдельный метод
```
public static void main(String[] args) {
  while (true) {  <-- ПЕРВЫЙ БЕСКОНЕЧНЫЙ ЦИКЛ
    //...
    if (choice == END) {
      System.out.println("До свидания.");
      break;
    } else {
      words = getDictionary();
      while (true) {  <-- ВТОРОЙ БЕСКОНЕЧНЫЙ ЦИКЛ
        //миллион строк алгоритма игры
      }
    }
  }
}

//ПРАВИЛЬНО:
public static void main(String[] args) {
  while (true) {
    //...
    if (choice == END) {
      System.out.println("До свидания.");
      break;
    } 
    playGame();
  }
}
```

**10. Побочный эффект**

Метод должен только вернуть случайное слово, а не *установить* и вернуть случайное слово
```
private static String getRandomWord() {
  Random random = new Random();
  int index = random.nextInt(words.size());
  secretWord = words.get(index);  <-- ПОБОЧНЫЙ ЭФФЕКТ
  return secretWord;
}

//ПРАВИЛЬНО:
private static String getRandomWord() {
  Random random = new Random();
  int index = random.nextInt(words.size());
  return words.get(index);
}
```
*Мартин "Чистый код", гл.3, "Избавьтесь от побочных эффектов"*

## ВЫВОД

Для процедурного стиля норм.

n.89(172)  
#ревью #виселица 