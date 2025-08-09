https://github.com/kkkvnp/gallows-console-game   
[Elina]

Игра в процедурном стиле, выполнена в одном классе.

## НЕДОСТАТКИ РЕАЛИЗАЦИИ

Это русская буква
```
ВВЕДИТЕ БУКВУ И НАЖМИТЕ ENTER: ё
Ошибка ввода. Неверный формат - это не буква русского языка! Введите букву и нажмите enter: 
```

## ХОРОШО

+ 👍 Игра запускается
+ 👍 Перед стартом игры распечатываются подробные правила
+ 👍 Можно ввести только одиночную букву русского языка
+ 👍 Есть список введенных букв
+ 👍 Применяются константы, форматированный вывод

## ЗАМЕЧАНИЯ

**1. Нейминг**

- UPPER_SNAKE только для констант, а это не константы
```
private static String WORD;
private static char[] WORD_LETTERS;
```

- Преобразование одного в другое это "to"
```
/**
* Получения из слова массива символов
*/
public static char[] getWordLetters(String word) {
  return word.toUpperCase().toCharArray();
}

//ПРАВИЛЬНО:
public static char[] toLetters(String word) 
```

*Oracle Java code conventions, part."Naming conventions"*  
*Мартин, "Чистый код", гл.2*  
*Ютуб, Немчинский "Как называть переменные, методы и классы?"*

**2. Нарушение инкапсуляции**. Публичным здесь должен быть только `main()`.

**3. Нарушение конвенции кода**. В любой ситуации выделяй тело блока скобочками, даже если тело состоит из одной строки. 
Исключение- метод equals(), там можно после if не выделять блоки скобочками
```
if (!isMatchValueInArray) COUNT_MISTAKE++;

//ПРАВИЛЬНО:
if (!isMatchValueInArray) {
  COUNT_MISTAKE++;
}
```

**4. Избыточно**

- Можно склеить
```
System.out.print("СЛОВО: ");
System.out.println(SECURITY_WORD_LETTERS);

//ПРАВИЛЬНО:
System.out.println("СЛОВО: " + SECURITY_WORD_LETTERS);
```

- Если регулярное выражение используется многократно, как здесь, то нужно его откомпилировать.  
Компиляция регулярного выражения в объект Pattern с помощью Pattern.compile() позволяет повысить производительность при многократном использовании
```
if (!value.matches("[а-яА-Я]")) {...}
```

- Если нужно просто подставить слово в начале или в конце, просто складывай их. 
Форматирование имеет смысл только тогда, когда нужно вставить текст внутрь другого текста
```
System.out.printf("ПОПЫТКИ ЗАКОНЧАЛИСЬ, ВЫ ПРОИГРАЛИ! ЗАГАДАННОЕ СЛОВО - %s.", WORD);

//ПРАВИЛЬНО:
System.out.print("ПОПЫТКИ ЗАКОНЧАЛИСЬ, ВЫ ПРОИГРАЛИ! ЗАГАДАННОЕ СЛОВО - " + WORD);
```

**5. Комментарии**

- Коментарии тут в основном не несут полезной нагрузки, а констатируют очевидное. 
Например, здесь комментарии просто дублируют названия методов:
```
/**
* Рисование виселицы
*/
 public static void drowGallows(int contMistake) {...}

/**
* Вывод засекреченного слова для пользователя
*/
public static void showSecurityWordLetters() {...}
```
Когда в проекте много каментов, это плохо- пользы от них практически нет, они только забивают пространство и мешают читать код.
В идеале, комментариев вообще не должно быть, код должен объяснять сам себя через правильный нейминг и лаконичный код.  
*Мартин, "ЧК", гл.4*

**6. Чтение файла**

Избыточно сложная логика
```
Файл с топ-1000 существительных РЯ. В исходном файле он пронумерован по строкам -> перезаписываем слова в новый файл без порядковых номеров и пробелов (табуляции).
```
Если тебе нужен текстовый файл без номеров, просто держи в папке файл без номеров, а файл с номерами не держи. Каждый раз перезаписывать этот файл- беcсмысленная затея.

**7. Реакция на ошибки**

В проекте предусмотрено, что может возникнуть исключение при чтении файла со словами.  
В этом случае проект просто перехватывает проверяемое исключение и печатает сообщение об ошибке
```
try (BufferedReader reader = new BufferedReader(new FileReader(fileName));
  //...
} catch (IOException e) {
  System.out.println("ПРОИЗОШЛА ОШИБКА: " + e.getMessage());
}
```
В данном случае такой способ реагирования на ошибку неправилен. 
Потому что если файл действительно будет отсутствовать, то программа аварийно прекратит свою работу. 
И напишет в консоль непонятные для пользователя сообщения
```
Подождите, сейчас программа загадает слово…
***
Exception in thread "main" java.lang.RuntimeException: java.nio.file.NoSuchFileException: _resource\modified_word.txt
	at ru.trashkova.gallows.GallowsApplication.getAllCountLineFile(GallowsApplication.java:153)
```
Здесь должна быть другая реакция на отсутствие файла со словами.  
Нужно сказать юзеру, что файл со словами по указанному пути открыть не удалось и поэтому работа программы будет завершена. 
И корректно завершить работу программы без вываливания красных эксепшенов в консоль.

**8. Не надо никакой паузы**
```
//TODO добавить паузу 3сек
```

**9. Создавай вспомогательные методы**, делай программу более простой и понятной
```
while (COUNT_MISTAKE < MAX_COUNT_MISTAKE) {
  //...
  if (COUNT_MISTAKE != MAX_COUNT_MISTAKE) {
    showSecurityWordLetters(); // чтобы не печатало засекреченное слово, когда кол-во ошибок 6, а сразу печать, что игра закончилась
  } else System.out.printf("ПОПЫТКИ ЗАКОНЧАЛИСЬ, ВЫ ПРОИГРАЛИ! ЗАГАДАННОЕ СЛОВО - %s.", WORD);

  if (Arrays.equals(SECURITY_WORD_LETTERS, WORD_LETTERS)) {
    System.out.println("ВЫ ВЫЙГРАЛИ! ПОЗДРАВЛЯЕМ!");
    break;
  }
}

//ЛУЧШЕ:
while (!isGameOver()) {
  //...
  if (isLose()) {
    printLoseMessage();
  } else if (isWin()) {
    printWinMessage();
  }
}

private static boolean isGameOver() {
  return isWin() || isLose();  
}
```

**10. Побочные эффекты.**

Нельзя в методе устанавливать поле класса и возвращать эти же данные через return. 
В этом случае инициализация поля класса будет происходить неявно для клиентского кода. 
То есть, это будет побочный эффект
```
private static char[] WORD_LETTERS; <-- ПОЛЕ КЛАССА

public static char[] getSecurityWordLetters(char inputLetter) {
  for (int i = 0; i < WORD_LETTERS.length; i++) {
    if (WORD_LETTERS[i] == inputLetter) {
      SECURITY_WORD_LETTERS[i] = inputLetter;  <-- ИЗМЕНЯЕТСЯ ПОЛЕ КЛАССА
    }
  }
  return SECURITY_WORD_LETTERS;  <-- ВОЗВРАЩАЕТСЯ ПОЛЕ КЛАССА
}

//ПРАВИЛЬНО ТАК:
public static char[] toSecurityWordLetters(char inputLetter) {
  chat[] letters = new char[wordLetters.length];   
  for (int i = 0; i < WORD_LETTERS.length; i++) {
    if (WORD_LETTERS[i] == inputLetter) {
      letters[i] = inputLetter;
    }
  }
  return letters;
}

//ИЛИ ТАК:
private static void initSecurityWordLetters(char inputLetter) {
  for (int i = 0; i < WORD_LETTERS.length; i++) {
    if (WORD_LETTERS[i] == inputLetter) {
      SECURITY_WORD_LETTERS[i] = inputLetter;
    }
  }
}
```
То же самое в `int getCountMistake(char letter)` и др.  
*Мартин "Чистый код", гл.3, "Избавьтесь от побочных эффектов"*  
*Фаулер "Рефакторинг", гл.6, "Извлечение метода"*

**11. Рисование виселицы**

- В любом switch-case должен быть default. В данном случае default должен бросать исключение с сообщением, что такого номера картинки не существует.

- Новая запись switch-case лучше старой
```
switch (contMistake) {
  case 0:
    System.out.println(conditionGallows);
    break;
  case 1:
    System.out.println(conditionHead);
    break;
    //...
}

//ЛУЧШЕ:
String picture = switch (contMistake) {
  case 0 -> conditionGallows;
  case 1 -> conditionHead;
  //...
}
System.out.println(picture);
```
Такая запись switch-case появилась начиная с java 14.

- Не экономь строчки, приоритет- понятность кода
```
String conditionGallows = "_______\n|\n|\n|\n|\n";
String conditionHead = "_______\n|   ()\n|\n|\n|\n";

//ПРАВИЛЬНО:
String conditionGallows = """
    -----   
    |       
    |       
    |       
    |       
    ------- 
  """;
```

- Распечатка картинки виселицы через switch-case или if-elseif - индусский код.
Картинки нужно хранить в статическом массиве и печатать по номеру, например, так
```
private static final String[] PICTURES = {
    """
    -----   
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
     ------- 
     """
   ,
   // more pics
};

public static void printPicture(int numPicture) {  
  System.out.println(PICTURES[numPicture]);
}
```
Желательно картинки и метод распечатки вынести в отдельный класс, чтобы в основном классе не смешивать логику и графику.  
Процедурный стиль дозволяет делать проекты, состоящие из нескольких классов.

- Нарушение SRP. Метод делает две вещи одновременно: преобразует строку в массив символов и распечатывает инфу
```
public static char[] getSecurityWordLetters(String word) {
  char[] securityWordArray = new char[word.length()];
  Arrays.fill(securityWordArray, '_');
  System.out.print("ЗАГАДАННОЕ СЛОВО: ");
  System.out.println(securityWordArray);
  return securityWordArray;
}
```
Каждый метод должен делать только одно дело. 
Этот метод нужно раздлить на два: один преобразует строку, второй- распечатывает инфу.

## ВЫВОД

Этот неуместный UPPER_SNAKE здорово мешает читать код. Поэтому нужно начать с базы: "Oracle Java code conventions".  
Для развития навыков процедурной декомпозиции, посмотреть стрим Сергея [Крестики-нолики в процедурном стиле](https://www.youtube.com/watch?v=PPikj1qHxrA)

n.115(215)  
#ревью #виселица 