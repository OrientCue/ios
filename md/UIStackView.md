
 **[Back](https://orientcue.github.io/ios/ "Table of Content")**

# **UIStackView**

- [Attributes](#attributes)
- [Adding subviews](#adding-subviews)

UIStackView предоставляет удобный интерфейс для расположения дочерних элементов по горизонтали или вертикали. UIStackView позволяет использовать всю мощь autolayout без использования констрейнтов, позволяет создавать пользовательский интерфейс, который динамически реагирует на внешние и внутренние изменения. Для позиционирования UIStackView вам придется использовать свои констреинты, без них система может посчитать размер UIStackView на основе его элементов. Если ширина и высота указаны явно, то UIStackView будет регулировать размер и положение дочерних элементов для того, чтобы заполнить все заданное пространство. 


<img src="https://github.com/OrientCue/ios/blob/master/_resources/db5567edf6ad4e358816e47c4dd8c9c1.png?raw=true">"

UIStackView опирается на intrinsic content size, приоритеты compression resistance и content hugging для того, чтобы знать какое пространство занимают элементы и то, как их можно сжимать. 
Для финального расчета размера и положения элементов в UIStackView зависит от его свойств, а также от того, будете ли вы добавлять свои констрейнты внутри самого UIStackView. Это возможно сделать, например можно добавить констрейнт высоты или ширины. Но лучше этим не увлекаться, так как сам UIStackView создает внутри себя множество констрейнтов и это может сказываться как на производительности, так и на том, что ваши констреинты могут конфликтовать с констрейнтами внутри UIStackView. 

* * *

## Attributes 
- **Axis** Определяет ориентацию UIStackView

<img src="https://github.com/OrientCue/ios/blob/master/_resources/95ae54bc783e47a39af957def561ac09.png?raw=true">

- **Alignment**
Определяет расположение элементов перпендикулярно ориентации UIStackView. 

Горизонтальная ориентация:


<img src="https://github.com/OrientCue/ios/blob/master/_resources/48618193c2d34211a24a88ec61e49c89.png?raw=true">

Вертикальная ориентация:

<img src="https://github.com/OrientCue/ios/blob/master/_resources/68b97b21bb524050939df1a9ad463756.png?raw=true">

- **Distribution**
Определяет расположение элементов в текущей ориентации. По умолчанию
	- Fill - заполняет все свободное прастранство на основе intrinsicContentSize и contentCompressionResistancePriority и contentHuggingPriority, чтобы определить какие элементы увеличивать или уменьшать.
	- Fill Equally - заполняет все свободное пространство чтобы каждый элемент был эквивалентен друг другу. 
	- Equal Spacing - заполняет все свободное пространство растягивая элементы по ширине или высоте 
	- Equal Centering - распределяет элементы так, чтобы между их центрами было равное расстояние


<img src="https://github.com/OrientCue/ios/blob/master/_resources/b03ee208a9174bccad6bc37d6cffae3d.png?raw=true">

- **Spacing**
Определяет расстояние между соседними элементами в UIStackView. По умолчанию он равен 0. 


<img src="https://github.com/OrientCue/ios/blob/master/_resources/f6456a3e32df48efa62f5f9695828495.png?raw=true">

* * *

## Adding subviews

Дочерние элементы можно добавлять через Xib\Storyboard, а также нам доступно следующее API.
Все дочерние элементы лежат в следующем свойстве:
```
@property(nonatomic, readonly, copy) NSArray<__kindof UIView *> *arrangedSubviews;
```
В отличии от стандартного для UIView свойства subviews. subviews это просто добавленные дочерние элементы, и UIStackView не будет управлять их положение и размером. Чтобы позволить UIStackView управлять дочерними элементами, добавление должно происходить через следующие методы: 
```objc
- (void)addArrangedSubview:(UIView *)view;
- (void)insertArrangedSubview:(UIView *)view 
                      atIndex:(NSUInteger)stackIndex;
и удаление:
- (void)removeArrangedSubview:(UIView *)view;
```
Есть важный момент, когда мы удаляем элемент используя removeArrangedSubview:, элемент продолжает оставаться в массиве subviews, и он может продолжать показываться, но UIStackView прекращает управлять ее размером и положением. 
Используя это API, UIStackView автоматически обновляет layout отслеживая добавление\удаление а также свойство isHidden. А также следит за своми собственными атрибутами. Например если мы поменяем ориентацию, UIStackView автоматически перерисует свое представление. Все эти действия могут быть анимированы. 





