https://github.com/IvanZvyagin/Hangman  
[Ivan Zvyagin]

Игра в процедурном стиле, выполнена в одно классе. Функционал заметно лучше внутренного устройства.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

1. Неправильная последовательность в порядке отображения введенных букв

Букву 'ц' я ввел второй, но в распечатке она стала первой. 
При этом введенная третьей буква 'к' в распечатке стала на третью позицию
```
Введите букву: 
й
<....>
Использованные буквы [й]

Введите букву: 
ц
<....>
Использованные буквы [ц, й]

Введите букву: 
к
<....>
Использованные буквы [ц, й, к]
```
Введенные буквы должны размещаться по алфавиту(й, к, ц) либо в порядке ввода(й, ц, к).

2. Резкое повешение

Вешать нужно плавно- прорисовывать по одной части тела за раз. Сейчас юзер ожидает, что у него есть еще две попытки- две ноги. Допускает (одну) ошибку и бац- приехали
```
 +---+
 |   |
 O   |
/|\  |
     |
     |
 =====
<...>
Введите букву: 
ш
Буквы ш нет в слове!
 +---+
 |   |
 O   |
/|\  |
/ \  |
     |
 =====
 <...>
 Игра окончена
```

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Можно ввести только одиночную букву русского языка
+ 👍 Есть список введенных букв

## ЗАМЕЧАНИЯ

**1. Нейминг**

- Если придерживаться Oracle Java code conventions, эту константу нужно писать стилем UPPER_SNAKE. А если конвенции Google, то так, как здесь написано.  
Главное, делать это осознанно
```
//СЕЙЧАС ТАК:
private static final List<String> words = new ArrayList<>();

//КОНВЕНЦИЯ ORACLE:
private static final List<String> WORDS = new ArrayList<>();

//КОНВЕНЦИЯ GOOGLE:
private static final List<String> words = new ArrayList<>();
```

- Если метод называется "сравнить"(check), то его результат должен быть boolean. Если его результат не boolean, значит он не должен так называться
```
void checkErrors(String secretWord, char letter, Set<Character> usedLetters) 
```

- Название обманывает. Метод возвращает не индекс случайного слова, а само случайное слово
```
public static String getRandomIndexWord(List<String> words)
```

- Если в названии метода есть связка "And", значит или с названием, или с методом что-то не так. 

В данном случае нет необходимости в уточнении, что слово не только читается, но и возвращается- и так ясно, что результатом чтения слова является его возврат/получение.
Но здесь суть метода состоит немного в другом- чтение файла здесь является *способом* получить слова и выбрать из них одно. Например, этот способ- чтение файла со словами.
А суть этого метода- вернуть случайное слово. При этом подробность, как именно это слово было взято, например путем чтения откуда-то, мне видится второстепенной
```
String[] readAndOutWord(List<String> words) 

//ПРАВИЛЬНО:
String[] getRandomWord(List<String> words) 
```
Если очень хочется указать, что слово читается именно из файла, то `String[] getRandomWordFromFile(List<String> words)`

- Судя по названию, метод должен вернуть *одно* слово
```
String[] readAndOutWord(List<String> words) 
```
Но возвращает массив стрингов. Значит, метод либо не возвращает слово. 
Либо возвращает слово и дополнительно еще что-то. 
Например, возвращает слово и маску этого слова.

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*  

**2. Вертикальное форматирование кода**

У развработчика есть определенная свобода по форматированию своего кода. Но вертикальные зазоры в две строки и более- это чересчур.
Тем более странно (и ацки) выглядят две пустых строки перед закрытием блока
```
public static void main(String[] args) throws FileNotFoundException {
  System.out.println("Добро пожаловать в игру \"Виселица\" \nВведите: ");
  do {...} while (true);


}


public static void gameLoop(String currentMask, Set<Character> usedLetters) throws FileNotFoundException {...}
```
*Мартин "ЧК", гл.5, "Вертикальное разделение концеаций"*

**3. Нарушение инкапсуляции**. Публичным должен быть только метод `main()`. Остальные методы и поля здесь должны быть приватными.

**4. Сравнивай строки без учета регистра**
```
if (start.equals("N") || start.equals("n")) {...}

//ПРАВИЛЬНО:
if (start.equalsIgnoreCase("n")) {...}
```

**5. Нарушение DRY**, магические буквы, числа, слова. Вводи константы
```
System.out.println("[N]ew game чтобы начать или [E]xit чтобы выйти");
if (start.equals("N") || start.equals("n")) {...}
if (start.equals("E") || start.equals("e")) {...}
      
//ПРАВИЛЬНО:
private final static String START = "N";
private final static String QUIT = "E"

System.out.printf("[%s]ew game чтобы начать или [%s]xit чтобы выйти \n", START, QUIT);
if (start.equalsIgnoreCase(START)) {...}
if (start.equalsIgnoreCase(QUIT)) {...}
```
*Фаулер, "Рефакторинг", гл.8, "Замена магического числа символической константой"*  
*refactoring.guru "Замена магического числа символьной константой"*  

**6. Если нужно печатать или создавать строку** с более, чем одним подстановочным значением или значение вставляется внутрь сообщения, используй форматированный вывод- тогда сразу будет виден весь шаблон
```
System.out.println("Количество ошибок " + errors + "/" + MAX_ERRORS);
System.out.println("Буква " + letter + " уже использовалась");

//ПРАВИЛЬНО:
System.out.printf("Количество ошибок %d/%d \n", errors, MAX_ERRORS);
System.out.printf("Буква %s уже использовалась \n", letter);
```

**7. Приоритет- читаемость кода**, а не экономия строчек

Теперь будет видно, что сообщение распечатается в две строки
```
System.out.println("Добро пожаловать в игру \"Виселица\" \nВведите: ");

//ПРАВИЛЬНО:
System.out.println("Добро пожаловать в игру 'Виселица'");
System.out.println("Введите: ");
```

**8. Переменные бери из окружения, если они там уже есть**, а не принимай в аргументы метода
```
private static String secretWord;
private static int errors = 0;

public static boolean isGameOver(String currentMask, String secretWord, int errors) {
  //Работа с secretWord, errors - принимаются во входящие аргументы
}

//ПРАВИЛЬНО:
public static boolean isGameOver(String currentMask) {
  //Работа с secretWord, errors - берутся из окружения, т.е. из полей класса
}
```

**9. Избыточно**. При объявлении полей класса, если им не заданы значения, то они устанавливаются значениями по умолчанию. 
Для int это будет 0 
```
private static String maskSecretWord;
private static int errors = 0;

//ПРАВИЛЬНО:
private static String maskSecretWord;  // = null
private static int errors;  // = 0
```
А вот в `Си++` действительно при объявлении переменной всегда нужно устанавливать ей явное значение.

**10. Создавай вспомогательные методы**, делай программу более простой и понятной
```
while (currentMask.contains("*")) {...}

//ПРАВИЛЬНО:
while (!isWin()) {...}

private boolean isWin() {
  return !currentMask.contains(MASK_SYMBOL);
}
```
Еще лучше- сделай такие вспомогательные методы
```
boolean isWin() {...}
boolean isLose() {...}

boolean isGameOver() {
  return isWin() || isLose();  
}
```

**11. Комментарии**

- Коментарии в основном не несут полезной нагрузки, а констатируют очевидное
```
//вывод слова на экран
System.out.println("Загаданное слово: " + maskSecretWord);
```
- Комментарий противоречит коду. Здесь происходит не ввод буквы с консоли, а ввод буквы, строки, числа, знаков припинания- всего чего угодно, что решит ввести юзер
```
//ввод с консоли буквы
String input = scanner.nextLine().trim().toLowerCase();
```
Когда в проекте много каментов, это плохо- пользы от них практически нет, они только забивают пространство и мешают читать код.
В идеале, комментариев вообще не должно быть, код должен объяснять сам себя через правильный нейминг и лаконичный код.  
*Мартин, "ЧК", гл.4*  

**12. Реакция на ошибки**

В проекте предусмотрено, что может возникнуть исключение при чтени файла со словами.  
В этом случае проект просто перехватывает проверяемое исключение и вместо него кидает непроверяемое
```
try (BufferedReader bf = new BufferedReader(new FileReader("_src/words.txt"))) {
  //...
} catch (IOException e) {
  throw new RuntimeException(e);
}
```
В данном случае такой способ реагирования на ошибку неправилен. 
Потому что если файл действительно будет отсутствовать, то программа аварийно прекратит свою работу. 
И напишет в консоль непонятные для пользователя сообщения
```
[N]ew game чтобы начать или [E]xit чтобы выйти
n
Exception in thread "main" java.lang.RuntimeException: java.io.FileNotFoundException: _src\words.txt (Системе не удается найти указанный путь)
	at Game.readAndOutWord(Game.java:76)
	at Game.startGame(Game.java:56)
```
Здесь должна быть другая реакция на отсутствие файла со словами.  
Нужно сказать юзеру, что файл со словами по указанному пути открыть не удалось и поэтому работа программы будет завершена. 
И корректно завершить работу программы без вываливания красных эксепшенов в консоль.

**13. Побочные эффекты**

- Судя по названию, метод должен сравнить ошибки, что бы это ни значило.  
Но кроме этого метод печатает сообщения, печатает виселицу, увеличивает счетчик ошибок
```
  public static void checkErrors(String secretWord, char letter, Set<Character> usedLetters) {
    if (usedLetters.contains(letter)) {
      System.out.println("Буква " + letter + " уже использовалась");
    } else if (!secretWord.contains(String.valueOf(letter))) {
      System.out.println("Буквы " + letter + " нет в слове!");
      drawGallows(errors);
      errors++;
      System.out.println("Количество ошибок " + errors + "/" + MAX_ERRORS);
    }
  }
```

- Судя по названию, метод должен только проверить, закончилась ли игра.  
Но кроме этого метод печатает сообщения о том, с каким результатом игра закончилась
```
public static boolean isGameOver(String currentMask, String secretWord, int errors) throws FileNotFoundException {
  if (errors >= MAX_ERRORS) {
    System.out.println("Игра окончена, вы были повешены! Загаданное слово: " + secretWord); <-- ПОБОЧНОЕ
    return true;
  } else if (!currentMask.contains("*")) {
    System.out.println("Поздравляем! Вы угадали слово: " + secretWord);  <-- ПОБОЧНОЕ
  }
  return false;
}
```

- Судя по названию, метод должен только вернуть слово. 
Но кроме этого метод печатает сообщение, создает маску слова, заполняет словами список слов из окружения. 
Этот список слов методу передается во входящие
```
private static final List<String> words = new ArrayList<>();

String[] gameData = readAndOutWord(words);

String[] readAndOutWord(List<String> words) {
  //...
  while ((word = bf.readLine()) != null) {
    words.add(word);
  }
  //...  
}
```
Методы нужно разделить на несколько, каждый из которых будет выполнять только одну задачу.  
*Мартин "Чистый код", гл.3, "Избавьтесь от побочных эффектов"*  
*Фаулер "Рефакторинг", гл.6, "Извлечение метода"*  

**14. Нельзя в методе устаналивать поля класса и возвращать эти же поля в качестве возвращаемых значений**

Это снова побочный эффект
```
private static String secretWord;
private static String maskSecretWord;

public static String[] readAndOutWord(List<String> words) {
  //...
  secretWord = getRandomIndexWord(words).toLowerCase();  
  maskSecretWord = secretWord.replaceAll(".", "*");

  //...
  return new String[]{secretWord, maskSecretWord}; 
}

//ПРАВИЛЬНО ТАК:
public static void readAndOutWord(List<String> words) {
  //...
  secretWord = getRandomIndexWord(words).toLowerCase();  
  maskSecretWord = secretWord.replaceAll(".", "*");
}

//ИЛИ ТАК:
public static String[] readAndOutWord(List<String> words) {
  //...
  String[] result = new String[2];
  result[0] = getRandomIndexWord(words).toLowerCase();  
  result[1] = secretWord.replaceAll(".", "*");
  return result;
}
```

Сейчас поле `secretWord` за один ход инициализируется дважды
```
private static String secretWord;

//...
String[] gameData = readAndOutWord(words);
secretWord = gameData[0];  <-- 2

public static String[] readAndOutWord(List<String> words) {
  //...
  secretWord = getRandomIndexWord(words).toLowerCase();  <-- 1
  //...
}
```

**15. Интересный баг**

При каждом новом новом раунде игры, список слов увеличивается в два раза- заново читается файл со словами и эти слова снова добавляются в список слов.  
Проверь:
```
public static void startGame() throws FileNotFoundException {
  //...
  if (start.equals("N") || start.equals("n")) {
    Set<Character> usedLetters = new HashSet<>();
    String[] gameData = readAndOutWord(words);
    System.out.println("!!! Размер List<String> words: " + words.size()); <-- РАЗМЕР СПИСКА ПЕРЕД КАЖДЫМ РАУНДОМ
    secretWord = gameData[0];
    //...
  } 
  //...
}
```
Результат:
```
[N]ew game чтобы начать или [E]xit чтобы выйти
n
Загаданное слово: ******
!!! Размер List<String> words: 478
Введите букву: 
<играю пока не проиграл>

Игра окончена, вы были повешены! Загаданное слово: атеизм
[N]ew game чтобы начать или [E]xit чтобы выйти
n
Загаданное слово: **********
!!! Размер List<String> words: 956
```

**16. Распечатка виселицы**

- Картинки виселицы нужно вынести из метода распечатки и сделать их массивом-константой. Иначе, объект с картинками(а массив это объект) пересоздается каждый раз при вызове метода распечатки.

- Желательно картинки и метод распечатки вынести в отдельный класс, чтобы в основном классе не смешивать игровую логику и графику.  
Процедурный стиль дозволяет делать проекты, состоящие из нескольких классов.

## АРХИТЕКТУРА

Основа архитектуры здесь- методы с побочными эффектами.
Поэтому код сложный, с запутанными связями.

Методы нужно переделать так, чтобы каждый метод делал только какое-то одно дело.

#### Рассмотрим на примере 

Есть метод вышеупомянутый метод `String[] readAndOutWord(List<String> words)`, вот что он делает:
* принимает в себя пустой список строк
* читает слова из файла и добавляет их в этот список
* выбирает случайное слово из этого списка
* устанавливает значение полей класса secretWord и maskSecretWord
* печатает maskSecretWord в консоль
* возвращает значения полей  класса secretWord и maskSecretWord в виде массива строк

Используется этот метод клиентом сейчас так
```
List<String> words = new ArrayList<>();
String[] gameData = readAndOutWord(words);
secretWord = gameData[0];  
String currentMask = gameData[1];
```

**Последовательно отрефакторим `readAndOutWord(...)`**

а) Этот метод не должен принимать в себя пустой список, а должен создавать его внутри себя.
Потому что этот список использует только сам метод, это часть его внутреннего устройства
```
public static String[] readAndOutWord(List<String> words) {
  //...
  while ((word = bf.readLine()) != null) {
    words.add(word);
  }   
  //...
  secretWord = getRandomIndexWord(words).toLowerCase();
  maskSecretWord = secretWord.replaceAll(".", "*");
  //..
}

//ПРАВИЛЬНО:
public static String[] getRandomWord() {
  List<String> words = new ArrayList<>();
  //...
  while ((word = bf.readLine()) != null) {
    words.add(word);
  }   
  //...
  secretWord = getRandomIndexWord(words).toLowerCase();
  maskSecretWord = secretWord.replaceAll(".", "*");
  //..
}
```

б) Читаем слова из файла, выбираем случайное и возвращаем его. Возвращаем только само слово, а не слово + маска
```
public static String getRandomWord() {
  List<String> words = new ArrayList<>();
  //...
  while ((word = bf.readLine()) != null) {
    words.add(word);
  }   
  //...
  Random random = new Random();
  int index = random.nextInt(words.size());
  return words.get(index);
  //..
}
```

Тогда клиент будет использовать этот метод так
```
String secretWord = getRandomWord();
String currentMask = createMask(secretWord);
```

в) Но есть проблема- во время чтения файла со словами что-то может пойти не так(файл не найден) и тогда произойдет исключение. 
Программа аварийно прекратит свою работу и напишет в консоль что-то страшное и непонятное для юзера.
Нужно сделать так, чтобы в этом случае программа сообщила о возникшей проблеме и коректно остановила работу.

Напрашивается самый простой вариант
```
String path = "src/words.txt";
try (BufferedReader bf = new BufferedReader(new FileReader(path))) {
  //чтение файла
} catch (IOException e) {
  System.out.println("Файл со словами не найден: " + path);
  System.out.println("Работа программы завершена. Чао, буратины!");
  System.exit(0);
}
```
***Но делать так- неправильно***

Потому что метод должен возвращать слово, а не принимать решение, когда можно завершать работу программы. 
Завершение работы всего приложения, если оно будет происходить в методе получения слова- это снова побочный эффект метода получения слова.
Решить проблему можно разными способами. 

г) Один из вариантов- использовать класс Optional
```
private static final String FILE_PATH = "src/words.txt";

public static Optional<String> getRandomWord() {
  List<String> words = new ArrayList<>();
  
  try (BufferedReader bf = new BufferedReader(new FileReader(FILE_PATH))) {
    //чтение файла
  } catch (IOException e) {
    return Optional.empty();
  }
  Random random = new Random();
  int index = random.nextInt(words.size());
  String word =  words.get(index);
  return Optional.of(word);
}
```

И тогда клиент будет использовать метод так
```
Optional<String> optional = getRandomWord();
if(optional.isEmpty()) {
  //если optional пустой- значит файл не прочитался
  //распечатать сообщение о завершении работы программы
  //завершить работу программы
}
String secretWord = optional.get();
String currentMask = createMask(secretWord);
```

д) И, конечно, внутри себя `getRandomWord()` не должен ничего печатать в консоль.

## ВЫВОД

Рефакторить.

n.105(199)  
#ревью #виселица #архитектура #optional 