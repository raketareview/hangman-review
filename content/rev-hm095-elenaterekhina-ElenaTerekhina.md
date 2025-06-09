https://github.com/ElenaTerekhina/HangmanGame  
[Elena Terekhina]

Игра в процедурном стиле.  
Весь код находится в единственном методе- это конечно ужасно.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

Можно ввести не только русскую букву, но английскую букву, цифру и все что угодно. 

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Есть список введенных букв
+ 👍 Оригинальные картинки
+ 👍 Реализация доказывает, что Хангмана возможно написать в одном классе и в единственном методе

## ЗАМЕЧАНИЯ

**1. Нейминг**

- В названии переменных не пиши тип данных, к которым они относится. И вообще не употребляй венгерскую ноттацию.  
Название переменной должно отвечать на вопрос что хранит переменная, а не как хранит
```
ArrayList<String> wordList

//ПРАВИЛЬНО:
ArrayList<String> words
```

- Название должно как можно лучше объяснять суть явления. В данном случае 'Y' и 'N' отвечают на вопрос, запустить игру или выйти из игры
```
String positiveChoice = "Y";
String negativeChoice = "N";

//ПРАВИЛЬНО:
String start = "Y";
String quit = "N";
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*  

**2. Сверхбольшой супербожественный метод main** 

Единственный метод в проекте, его длина- 205 строк.  Из-за своего размера и сложности, метод абсолютно нечитабилен.  
Не делай таких больших методов. Оптимальный размер методов: до 20 строк.  
Если метод получается больше, то скорее всего он делает много разного и его нужно разделить на несколько.

- Раздели этот метод на несколько вспомогательных, каждый из которых будет делать что-то одно.

Например, один метод будет только принимать количество допущенных ошибок и распечатывать соответствующую картинку хангмана.
Второй метод будет проверять, закончилась ли игра выигрышем, третий- закончилась ли игра проигрышем и так далее:
```
private static void printHangman(int errorCount)
private static boolean isWin()
private static boolean isLose()
```
Про то, какими должны быть методы, в том числе касательно размеров, читай тут:  
*Мартин "Чистый код", гл.3*  
*Фаулер "Рефакторинг", гл.6, "Извлечение метода"* 

- Когда разделишь один большой метод на много маленьких, общие поля сделай не полями метода, а полями класса. 

Сейчас так:
```
class HangmanGame {
  public static void main(String[] args) {
    Scanner scanner = new Scanner(System.in);  <-- ПОЛЯ МЕТОДА
    String positiveChoice = "Y";
    String negativeChoice = "N";
    while (true) {
      StringBuilder mask = new StringBuilder();
      ArrayList<Character> usedLetters = new ArrayList<>(); // список введенных букв
      String[] hangmanStages = {
          // 0 ошибок: только виселица
          """
       __________
       |/       |
       |
       |
    ___|___________
    |             |""",
       //...
      }
    //...
    }
  }
}  
```
Надо так:
```
class HangmanGame {
  //ПОЛЯ КЛАССА  
  private static final Scanner SCANNER = new Scanner(System.in);  <-- КОНСТАНТЫ
  private static final String START = "Y";
  private static final String QUIT = "N";
  private static final String[] HANGMAN_STAGES = {
          // 0 ошибок: только виселица
          """
       __________
       |/       |
       |
       |
    ___|___________
    |             |""",
       //...
    //...
    }

    private static StringBuilder mask = new StringBuilder();  <-- ИЗМЕНЯЕМЫЕ ПОЛЯ (не константы)
    private static ArrayList<Character> usedLetters = new ArrayList<>(); 

  public static void main(String[] args) {...}
}  
```

**3. Используй классы через их интерфейсы**
```
ArrayList<Character> usedLetters = new ArrayList<>(); 

//ПРАВИЛЬНО:
List<Character> usedLetters = new ArrayList<>(); 
```

**4. Комментарии**

Коментарии в основном не несут полезной нагрузки, а констатируют очевидное
```
ArrayList<Character> usedLetters = new ArrayList<>(); // список введенных букв
```
Когда в проекте много каментов, это плохо- пользы от них практически нет, они только забивают пространство и мешают читать код.
В идеале, комментариев вообще не должно быть, код должен объяснять сам себя через правильный нейминг и лаконичный код.  
*Мартин, "ЧК", гл.4*

**5. Нарушение DRY**, магические буквы, числа, слова. Вводи константы. А если они уже есть- пользуйся
```
public class HangmanGame {
  public static void main(String[] args) {
    String positiveChoice = "Y";
    String negativeChoice = "N";
    int lives = 5;
    while (!userChoice.equalsIgnoreCase("Y") && !userChoice.equalsIgnoreCase("N")) {
      System.out.println("Сыграем ещё раз? ДА (Y) или НЕТ (N)");
      //...
      System.out.println("Отлично! Ваша задача — угадать слово, вводя буквы, которые, по вашему мнению, в нём есть. У вас 5 жизней. ");
    }
  }
}

//ПРАВИЛЬНО:
private static final String START = "Y";
private static final String QUIT = "N";
private static final int MAX_LIVES = 5;

public class HangmanGame {
  public static void main(String[] args) {
    while (!userChoice.equalsIgnoreCase(START) && !userChoice.equalsIgnoreCase(QUIT)) {
      System.out.printf("Сыграем ещё раз? ДА (%s) или НЕТ (%s) \n", START, QUIT);
      //...
      System.out.printf("Отлично! Ваша задача — угадать слово, вводя буквы, которые, по вашему мнению, в нём есть. У вас %d жизней. \n", MAX_LIVES);
    }
  }
}
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*   
*refactoring.guru "Замена магического числа символьной константой"*  

**6. Создавай вспомогательные методы**, делай программу более простой и понятной
```
while ((lives > 0) && (mask.toString().contains("_"))) {...}

//ПРАВИЛЬНО:
while (!isGameOver()) {...}

private boolean isGameOver() {
  return isWin() || isLose();
}

private boolean isWin() {
  return !mask.toString().contains("_");
}

private boolean isLose() {...}
```

**7. Картинки висельницы**

+ 👍 Картинки в массиве, это хорошо.

- Картинки висельницы и метод их распечатки лучше вынести в отдельный класс, чтобы в основном классе не смешивать логику и графику.
Процедурный стиль дозволяет делать проект из нескольких классов.

Чтение слов из файла также лучше вынести в отдельный класс.

**8. Реакция на ошибки**

В проекте предусмотрено, что может возникнуть исключение при чтени файла со словами.  
В этом случае проект просто информирует юзера о том, что файл не найден
```
try {
  //миллион строк кода
} catch (FileNotFoundException e) {
  System.out.println("файл не найден");
}
```
В данном случае такой способ реагирования на ошубку неправилен. Потому что если файл действительно будет отсутствовать, то программа будет работать очень странно:
```
Отлично! Ваша задача — угадать слово, вводя буквы, которые, по вашему мнению, в нём есть. У вас 5 жизней. 
файл не найден
Поздравляю! Вы угадали слово: 


Привет! Сыграем в 'Виселицу'? ДА (Y) или НЕТ (N).
```
Здесь должна быть другая реакция на отсутствие файла со словами.  
Нужно сказать юзеру, что файл отсутствует и поэтому работа программы будет завершена. И соответственно завершить работу программы.

**9. Счетчик инициализируй прямо в for'e**
```
int i;
for (i = 0; i < hiddenWord.length(); i++)

//ПРАВИЛЬНО:
for (int i = 0; i < hiddenWord.length(); i++)
```

**10. Нарушение DRY**, сплошное многократное вложенное дублирование кода
```
while (!userChoice.equalsIgnoreCase("Y") && !userChoice.equalsIgnoreCase("N")) {
  //...
  if (!answerQuestion.equalsIgnoreCase("Y") && !answerQuestion.equalsIgnoreCase("N")) {
    //...
    while (!answerQuestion.equalsIgnoreCase("Y") && !answerQuestion.equalsIgnoreCase("N")) {
      //...
    }
  }
}

//ПРАВИЛЬНО:
while (!isCommand(userChoice)) {
  //...
  if (!isCommand(answerQuestion)) {
    //...
    while (!isCommand(answerQuestion)) {
      //...
    }
  }
}

private finastatic boolean isCommand(String s) {
  return s.equalsIgnoreCase(START) || answerQuestion.equalsIgnoreCase(QUIT);
}
```

**11. Глубокая вложенность**

Из-за того, что весь алгоритм игры находится в единственном методе, есть глубокая вложенность блоков друг в друга.  
Антипаттерн "Стрела"
```
while (true) {
  while ((lives > 0) && (mask.toString().contains("_"))) {
    while (!userChoice.equalsIgnoreCase("Y") && !userChoice.equalsIgnoreCase("N")) {
      if (...) {
        while (!answerQuestion.equalsIgnoreCase("Y") && !answerQuestion.equalsIgnoreCase("N")) {
          if (...) {
            if (...) {
              //НАКОНЕЧНИК СТРЕЛЫ
            }
          }
        }
      }
    }
  }
}
```
Когда разделишь этот метод на несколько, неизбежно уйдет такая глубокая вложенность.  
Не должно быть больше 2-3 уровней вложенности. 

## ВЫВОД

Ничего страшного, обычные ошибки новичка. Нужно больше тренироваться.

n.95(181)  
#ревью #виселица #виселицаводномметоде 