https://github.com/Savromat/hangman/blob/master/src/Main.java  
[Pavel]

Игра в процедурном стиле, выполнена в одном классе. Есть баги.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Нет защиты от дурака
```
1 - легкий, будут открыты две случайные буквы в маске слова, 2 - сложный, без подсказок:
q
Exception in thread "main" java.util.InputMismatchException
```

2. Команды работают неправильно- выход из программы должен только при вводе 'н', раз так заявлено
```
Хотите сыграть еще раз? д/н
r

Process finished with exit code 0
```

3. Команды работают неправильно. Я ввел 'д', но сыграть еще раз мне не дали
```
Хотите сыграть еще раз? д/н
Д

Process finished with exit code 0
```

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву русского языка
+ 👍 Два уровня сложности: с подсказками и без

## ЗАМЕЧАНИЯ

**0. Ссылки давай на репозиторий, а не на один из файлов репозитория**
```
https://github.com/Savromat/hangman/blob/master/src/Main.java

//ПРАВИЛЬНО:
https://github.com/Savromat/hangman
```

**1. Нейминг**

- Название обманывает. Метод не проверяет состояние игры, но устанавливает состояние игры
```
void checkGameState(String word, String mask, int mistakes) {
  if (mistakes == MAX_MISTAKES) {
    isLose = true;
  }
  //...    
}
```

- Название должно как можно лучше объяснять суть явления. Это конечно "ответ", но этот ответ является командой для выполнения действий: выйти или играть
```
System.out.println("Хотите сыграть еще раз? д/н");
char answer = scanner.next().charAt(0);

//ЛУЧШЕ:
char command = scanner.next().charAt(0);
```
*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*  

**2. Нарушение DRY**, магические буквы, числа, слова. Вводи константы. А если они есть- пользуйся 
```
private static final int EASY = 1;

System.out.println("Выберите уровень сложности 1 или 2");
System.out.println("1 - легкий, будут открыты две случайные буквы в маске слова, 2 - сложный, без подсказок:");
System.out.println("Хотите сыграть еще раз? д/н");

//ПРАВИЛЬНО:
private static final int EASY = 1;
private static final int HARD = 2;
private static final char START = 'д';
private static final char QUIT = 'н';

System.out.printf("Выберите уровень сложности %d или %d %n", EASY, HARD);
System.out.printf("%d - легкий, будут открыты две случайные буквы в маске слова, %d - сложный, без подсказок: %n", EASY, HARD);
System.out.printf("Хотите сыграть еще раз? %s/%s %n", START, QUIT);
```
*Фаулер, "Рефакторинг", гл.8 п."Замена магического числа символической константой"*   
*refactoring.guru "Замена магического числа символьной константой"*  

**3. Создавай вспомогательные методы**, делай программу более простой и понятной
```
if (word.indexOf(letter) != -1) {
  mask = openLetter(word, mask, letter);
} else {
  mistakes++;
}

//ПРАВИЛЬНО:
if (этаБукваЕстьВслове(letter)) {
  mask = openLetter(word, mask, letter);
} else {
  mistakes++;
}

private static boolean этаБукваЕстьВслове(char letter) {
  return word.indexOf(letter) != -1;
}
```

**4. Слишком длинные конструкции**, которые трудно понять. Вводи поясняющие переменные.
```
words.add(scanner.nextLine().toLowerCase().trim());

//ПРАВИЛЬНО:
String word = scanner.nextLine().toLowerCase().trim();
words.add(word);
```
*Фаулер, "Рефакторинг", гл.6,п."Введение поясняющей переменной".*  

**5. Если нужно печатать или создавать строку** с более, чем одним подстановочным значением или значение вставляется внутрь сообщения, используй форматированный вывод- тогда сразу будет виден весь шаблон
```
System.out.println("Вы уже вводили эту букву: " + input + ". Введите другую букву.");

//ПРАВИЛЬНО:
System.out.printf("Вы уже вводили эту букву: %s. Введите другую букву. %n", input);
```

**6. Перехватывай проверяемые исключения и вместо них бросай проверяемые**, тогда не придется в сигнатуре всех методов прописывать `throws`
```
public static void main(String[] args) throws FileNotFoundException { <-- ГИРЛЯНДА В СИГНАТУРЕ
  List<String> words = readWords();
  //...
}

private static List<String> readWords() throws FileNotFoundException { <-- ГИРЛЯНДА В СИГНАТУРЕ
  Scanner scanner = new Scanner(new File("words.txt"));
  //...
}

//ПРАВИЛЬНО:
private static List<String> readWords()  { <-- НЕТ ГИРЛЯНДЫ В СИГНАТУРЕ
  Scanner scanner = null;
  try {
    scanner = new Scanner(new File("words.txt"));
  } catch (FileNotFoundException e) {
    throw new RuntimeException(FILE_EXCEPTION_MESSAGE, e); <-- ЗАМЕНА НА НЕПРОВЕРЯЕМОЕ ИСКЛЮЧЕНИЕ
  }
  //...
}
```
*"ЧК", гл.7, п."...проверяемые исключения"*

**7. Инициализируй массивы сразу на этапе создания**
```
String[] stages = new String[7];
stages[0] = """
  ________
  |      |
  |
 _|_
   """;
stages[1] = """
   ________
   |      |
   |      o
   |
   """;
//stages[2] - [6]

//ПРАВИЛЬНО:
String[] stages = {
  """
    ________
    |      |
    |
   _|_
   """,
  """
   ________
   |      |
   |      o
   |
   """,
   //oth's     
}
```

**8. Картинки виселицы**

- Массив с картинками нужно вынести из метода и сделать этот массив константой.  
Если массив с картинками находится в методе, значит этот объект(массив это объект) пересоздается каждый раз при вызове метода.

- Желательно картинки и метод распечатки вынести в отдельный класс, чтобы в основном классе не смешивать логику и графику.  
Процедурный стиль дозволяет делать проекты, состоящие из нескольких классов.

**9. Состояния игры**

Будет лучше, если вместо кеширования(выставления) состояний игры, будут использоваться методы определения текущего состояния
```
isWin = false; <-- ПОЯСНЯЮЩИЕ ПЕРЕМЕННЫЕ
isLose = false;
do {
  //...
  checkGameState(word, mask, mistakes); <-- ЗДЕСЬ ВЫСТАВЛЕТСЯ ТЕКУЩЕЕ СОСТОЯНИЕ
} while (!isWin && !isLose);

private static void checkGameState(String word, String mask, int mistakes) {
  if (mistakes == MAX_MISTAKES) {
   isLose = true;
  } else if (mask.equals(word)) {
    isWin = true;
  }
}

//ЛУЧШЕ:
do {
  //...
} while (!isGameOver());

private static boolean isGameOver() {
  return isLose() || isWin();
}

private static boolean isLose() {
  return mistakes == MAX_MISTAKES
}

private static boolean isWin() {
  return mask.equals(word)
}
```

**10. Побочный эффект**

Судя по названию метод должен только производить валидированный ввод(буквы).
Но кроме этого метод еще пополняет перечень введенных букв.
```
private static char inputValidLetter(Scanner scanner) {
  System.out.println("Введите одну букву русского алфавита:");
  while (true) {
    //...
    usedLetters += input; <-- ПОБОЧНЫЙ ЭФФЕКТ
    return letter;
  }
}
```
Пополнение перечня букв должно происходить не в этом методе.
*Мартин "Чистый код", гл.3, "Избавьтесь от побочных эффектов"*  
*Фаулер "Рефакторинг", г.6, п."Извлечение метода"*  

**11. Соблюдается инкапсуляция**

👍 Все поля и методы приватные, кроме `main()`- это хорошо, инкапсуляция соблюдается.

## ВЫВОД

В целом, для процедурного стиля приемлемо. 

n.90(173)
#ревью #виселица 