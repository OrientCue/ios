# UIElements
Библиотека UIKit имеет множество элементов, такие как кнопки, поля для ввода и вывода текста и прочее.

<!-- TOC -->
- [UILabel](#uilabel)
- [UIControl](#uicontrol)
- [Наследование от UIControl](#наследование-от-uicontrol)
- [UIButton](#uibutton)
- [Содержание кнопки:](#содержание-кнопки)
- [UITextField](#uitextfield)
- [UITextFieldDelegate](#uitextfielddelegate)
- [UITextField Overlay](#uitextfield-overlay)
- [Keyboard attributes](#keyboard-attributes)
- [UIScrollView](#uiscrollview)
- [UITextView](#uitextview)
- [UITextViewDelegate](#uitextviewdelegate)
<!-- /TOC -->


## UILabel 
*Наследуется от UIView*
Элемент для отображения одной или нескольких строк текста. Можно изменять внешний вид отображаемого текста используя свойства UILabel, также UILabel поддерживает **attributedString**. 
Часто используемые свойства:

- `NSString *text`
- `NSAttributedString *attributedText`
Свойства обычного или форматированного текста, По умолчанию свойство равно nil. 
- `UIColor *textColor`
Свойство для цвета отображаемого текста. 
- `UIFont *font`
Свойство для шрифта отображаемого текста.  
- `CGFloat minimumFontSize` [deprecated]
Свойство для указания минимального размера шрифта. Оно необходимо для того что-бы поместить большой текст в статическую область через уменьшение размера шрифта.  
- `CGFloat minimumScaleFactor`
Свойство от 0.0 до 1.0 указывающее на сколько система может уменьшить шрифт чтобы поместить весь текст в статичную область. 
- `BOOL adjustsFontSizeToFitWidth`
Свойство, отвечающее за то, будут ли применены механизмы уменьшения текста, если текст не помещается в текущую область. свойство работает в коллаборации с minimumFontSize и minimumScaleFactor. Эти свойства будут работать только если adjustsFontSizeToFitWidth установлен в YES
По умолчанию свойство равно NO. 
- `BOOL allowsDefaultTighteningForTruncation`
Свойство, отвечающее за то, в случае если текст не помещается в заданную область, будет ли применен механизм “сжатия” текста, посредством уменьшения межбуквенного и интервала пробела. 
По умолчанию свойство равно NO. 
- `NSTextAlignment textAlignment`
Свойство, указывающее на направление выравнивания текста. По умолчанию свойство равно **NSTextAlignmentNatural**
- `NSInteger numberOfLines`
Свойство, отвечающее за количество строк в тексте. Если свойсво установить в 0, то количество строк ограничено не будет 
По умолчанию свойство равно 1. 
- `NSLineBreakMode lineBreakMode`
Свойство отвечающее правило переноса на новую строку. По умолчанию свойство равно **NSLineBreakByTruncatingTail**

**Пример использования lineBreakMode:**

<img src="https://github.com/OrientCue/ios/blob/master/_resources/4460ad3024494dc0b1c806dd137b6c9a.png?raw=true">

*Пример использования numberOfLines c lineBreakMode:*

<img src="https://github.com/OrientCue/ios/blob/master/_resources/068abcab18844ba893183d39a410abb8.png?raw=true">

**Пример использования lineBreakMode:**

<img src="https://github.com/OrientCue/ios/blob/master/_resources/43b640e87e4e49d0a47031d8f6f59a80.png?raw=true">


* * *
# UIControl
*Наследуется от **UIView***
Базовый визуальный класс для элементов управления и взаимодействия на который могут передаваться в ответ на взаимодействия с пользователем. К примеру нажатие. Наследники UIControl включает такие элементы как: кнопки, слайдеры, степперы поля ввода текста. Для того чтобы распознать действия пользователя используется паттерн как target-action 

Target-Action Mechanism
Объекты UIControls используют механизм target-action для сообщения о том, что произошли интересующие нас события, на которые мы должны вызвать необходимый код. Механизм target-action упрощает код, вместо написания кода для отслеживания событий, нам необходимо только написать action метод, реагирующий на конкретное событие. Объект UIControl выполняет всю работу по отслеживанию событий и определяет, когда нам вызывать action метод. 
Пример работы: 
```objc
- (void)addTarget:(id)target 
           action:(SEL)action 
 forControlEvents:(UIControlEvents)controlEvents;
```

Чтобы добавить action метод к классу UIControl нам необходимо указать объект который будет реализовывать этот метод (addTarget:(id)target), селектор для реализованного метода (action:(SEL)action), и указать какое именно событие нас интересует (forControlEvents:(UIControlEvents)controlEvents) 

В роли (id)target может выступать любой объект, но обычно для этого используется объект UIViewController. Если указан nil то объект ищется по Responder Chain. 

 action:(SEL)action метод может принимать три формы:
```objc
- (IBAction)doSomething;
- (IBAction)doSomething:(id)sender;
- (IBAction)doSomething:(id)sender forEvent:(UIEvent*)event;
```


**UIControlEvents** тип, определяющий каким именно образом пользователь взаимодействовал с UIControls классом. 


### Наследование от UIControl
Вы может наследовать класс UIControl. Это предоставит вам доступ к target-action механизму и упрощенной поддержке обработки событий. Вы можете наследоваться от уже существующих классов UIControl и изменить их поведение следующим способом: 

1)	Переопределить метод 
```objc
- (void)sendAction:(SEL)action 
                to:(id)target 
          forEvent:(UIEvent *)event;
```
Его можно переопределить для наблюдения и изменения диспетчеризации действия для связанных таргетов нашего класса. Также для изменения поведения отправки на основе указанного селектора или события. 

2)	Переопределить методы: 
```objc
- (BOOL)beginTrackingWithTouch:(UITouch *)touch 
                     withEvent:(UIEvent *)event;
- (BOOL)continueTrackingWithTouch:(UITouch *)touch 
                        withEvent:(UIEvent *)event;
- (void)endTrackingWithTouch:(UITouch *)touch 
                   withEvent:(UIEvent *)event;
- (void)cancelTrackingWithEvent:(UIEvent *)event;
```

Для обработки событий касания. Получаемую информацию можно использовать для выполнения дополнительных действий. В своей реализации необходимо обязательно вызвать super в любой из моментов обработки. Эти методы всегда стоит использовать для определения событий касания вместо методов класса UIResponder. 

## UIButton
*Наследуется от **UIControl***
Класс кнопки, элемент управления исполняющий код в ответ на взаимодействие с пользователем используя target-action механизм. 
Чтобы добавить кнопку, следует придерживаться следующих шагов:
1.	Установить тип кнопки во время создания(info, details, custom)
2.	Установить текст или изображение для кнопки и подогнать размер в соответствии с содержимым. 
3.	Подключить action методы
4.	Настраиваем Auto Layout 
5.	Предоставляем информацию для локализации
Сигнатура action методов для UIButton: 
```objc
- (IBAction)doSomething;
- (IBAction)doSomething:(UIButton*)sender;
- (IBAction)doSomething:(UIButton*)sender forEvent:(UIEvent*)event;
```
Кнопки имеют пять состояний, которые определяют их внешний вид: default, highlighted, focused, selected, and disabled. Когда пользователь взаимодействует с кнопкой, ее состояние меняется. За состояниями можно наблюдать, и менять атрибуты в зависимости от состояния, например цвет. 
**Content**. Содержание кнопки состоит из указанной нами строки или изображения. Указанный нами контент используется для настройки UILabel и UIImageView которые находятся внутри кнопки и управляются ей.Мы можем получить доступ к этим объектам используя методы titleLabel or imageView и изменить их значения напрямую. 
### Содержание кнопки:


<img src="https://github.com/OrientCue/ios/blob/master/_resources/583feec326204aea95eb9effb5fcbec0.png?raw=true">

**tintColor**. Свойство оттенка, унаследованное от UIView. Свойство устанавливает цвет изображения кнопки и текста. По умолчанию наследует цвет superview



* * *


# UITextField
*Наследуется от **UIControl***
Текстовое поле. Используется в основном для сбора от пользователя текстовой информации с помощью экранной клавиатуры. Например, имя пользователя, пароль, почта и т.д, так же, как и кнопка использует target-action механизм, помимо это используется паттерн делегат для отслеживания изменений, внесенных в текст. 

Конфигурация UITextField выглядит так:

1)	Настраиваем таргеты для текстового поля
2)	Настраиваем связанные с клавиатурой атрибуты текстового поля
3)	Назначаем объект делегата (UITextFieldDelegate), для обработки событий, таких как:

    1)	Разрешено ли редактирование содержимого текстового поля
    2)	Проверка введенного текста (валидация)
    3)	Обработка события нажатия кнопки Return на клавиатуре 
    4)	Переадресация введенного текста и др.

4)	Сохранение ссылки на объект текстового поля.

Форматирование текстового поля в UITextField.

Есть два подхода помимо изменения свойства класса UITextField:

1)	используя NSAttributerString
В отличи от дерективного изменения свойств класса UITextField, при помощи NSAttributerString можно менять внешний вид не только строки, но и ее подстрок. 
2)	используя NSFormatter
Встроенного механизма использовать NSFormatter нет, но нам доступна реализация метода делегата UITextField, например 
```objc
- (BOOL)textField:(UITextField *)textField 
shouldChangeCharactersInRange:(NSRange)range 
replacementString:(NSString *)string;
```
который мы можем использовать для проверки форматированная, используя, например NSDateFormatter. Как видим, метод должен вернуть булевское значение, если мы возвращаем YES, строка будет изменена, если NO, строка останется без изменения. 

### UITextFieldDelegate

UITextField вызывает методы делегата в ответ на некоторые пользовательские действия. Например эти методы используются для проверки текста введенного пользователем, проверки действий связанных с клавиатурой и т.д
Например: 

1)	Прежде чем стать firstResponder, UITextField вызывает метод делега textFieldShouldBeginEditing: а также отправляет сообщение **UITextFieldTextDidBeginEditingNotification**
2)	Каждый раз когда меняется текст в текстовом поле, UITextField вызывает метод делегата textField:shouldChangeCharactersInRange:replacementString: и посылает сообщение **UITextFieldTextDidChangeNotification**
3)	Когда пользователь нажимает кнопку очистки текстового поля, UITextField вызывает метод делегата  textFieldShouldClear:
4)	Когда пользователь нажимает кнопку Return на клавиатуре, UITextField вызывает метод делегата textFieldShouldReturn:
5)	Преждем чем UITextField теряет свойство firstResponder, UITextField вызывает метод делегата textFieldShouldEndEditing:, когда UITextField перестает быть firstResponder, система посылает сообщение **UIKeyboardWillHideNotification** и **UIKeyboardDidHideNotification**
6)	Когда клавиатура спрятана и UITextField перестал быть FirstResponder, UITextField вызывает метод делегата  textFieldDidEndEditing: и посылает сообщение **UITextFieldTextDidEndEditingNotification**

### UITextField Overlay
В дополнения к текстовому функционалу вы можете добавить overlay view для отображения дополнительной информации и функционала. Например, кнопка поиска или очистки текстового поля. Overlay представляет из себя небольшое изображение слева или справа от вводимого текста. 


<img src="https://github.com/OrientCue/ios/blob/master/_resources/56294a3b3b22499daab680bd2b571920.png?raw=true">



Ниже представлен листинг того, как мы можем добавить overlay кнопки в текстовое поле:
```objc
UIButton *overlayButton = [UIButton buttonWithType:UIButtonTypeCustom];
[overlayButton setImage:[UIImage imageNamed:@"bookmark"] forState:UIControlStateNormal];
[overlayButton addTarget:self action:@selector(displayBookmarks:)
        forControlEvents:UIControlEventTouchUpInside];
overlayButton.frame = CGRectMake(0, 0, 28, 28);

self.textField.leftView = overlayButton;
self.textField.leftViewMode = UITextFieldViewModeAlways;
```

В последней строчке через свойство leftViewMode мы определяем, когда будет показыватся наш overlay. Определение этого свойства дано через следующее перечисление: 
```objc
typedef NS_ENUM(NSInteger, UITextFieldViewMode) {
    UITextFieldViewModeNever, // По умолчанию
    UITextFieldViewModeWhileEditing,
    UITextFieldViewModeUnlessEditing,
    UITextFieldViewModeAlways
};
```
Стоит заметить, что это свойства по умолчанию установленно в **UITextFieldViewModeNever**, поэтому каждый раз, когда мы устанавливаем свой overlay, нам всегда нужно определить, когда нам нужно показывать наш overlay view. 

### Keyboard attributes
Атрибуты, связанные с тем, как будет выглядеть клавиатура. 
1.	Capitalization - **UITextAutocapitalizationType**
2.	Correction - **UITextAutocorrectionType**
3.	Spell Checking - **UITextSpellCheckingType**
4.	Keyboard Type - **UIKeyboardType**
5.	Appearance - **UIKeyboardAppearance**
6.	Return Key - **UIReturnKeyType**


* * *

## UIScrollView
*Наследуется от **UIView***
Элемент отображения позволяющий прокручивать и масштабировать содержащийся в нем отображения. UIScrollView обрезает содержимое в своем frame, который обычно (но не обязательно) совпадает с содержимым главного окна приложения. UIScrollView отслеживает движения пальцев и соответственно регулирует начало координат (origin).
Сам UIScrollView не рисует, за исключением отображения индикаторов вертикальной и горизонтальной прокрутки.
UIScrollView должно знать размер представления содержимого, чтобы знать, когда следует прекратить прокрутку.
 

Пример работы UIScrollView: 


<img src="https://github.com/OrientCue/ios/blob/master/_resources/35d3f1402d24410cb70fa9cad2607c2f.png?raw=true">

После прокрутки:


<img src="https://github.com/OrientCue/ios/blob/master/_resources/aea5462acc2e431dbc4a3dd67bca5d62.png?raw=true">


Из примера видно, что frame и contentSize не изменились, а меняется только contentOffset.

## UITextView
*Наследуется от **UIScrollView***
Служит для отображения большого текста к возможности прокрутки. Для изменения внешнего вида текста можно использовать свойства UITextView, но в таком случае внешний вид будет изменен во всем текста. Для UITextView лучше использовать attributedString. 


* * *

## UITextViewDelegate
Все методы делегата являются **опциональными**. Методы похожи на методы делегата UITextField.
- **textViewShouldBeginEditing**:
Спрашивает делегата, разрешено ли редактирование
- **textViewDidBeginEditing**:
Сообщает делегату, что редактирование началось.
- **textViewShouldEndEditing**:
Спрашивает делегата, разрешено ли закончить редактирование. 
- **textViewDidEndEditing**:
Сообщает делегату, что редактирование завершено.
- **textView: shouldChangeTextInRange: replaceText:**
Спрашивает делегата, следует ли заменить указанный текст.
- **textViewDidChange**:
Сообщает делегату, что текст или атрибуты были изменены пользователем.
- **textViewDidChangeSelection**:
Сообщает делегату, что выделенный текст изменился.

- **textView:shouldInteractWithTextAttachment:inRange:interaction:**
Asks the delegate if the specified text view should allow the specified type of user interaction with the provided text attachment in the given range of text.
- **textView:shouldInteractWithURL:inRange:interaction:**
Asks the delegate if the specified text view should allow the specified type of user interaction with the given URL in the given range of text.




