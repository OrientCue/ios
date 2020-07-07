
 **[Back](https://orientcue.github.io/ios/ "Table of Content")**
 
# App Sandbox & App Bundle

<!-- TOC -->
- [Sandbox](#sandbox)
- [Bundle](#bundle)
- [NSUserDefaults](#nsuserdefaults)
- [NSKeyedArchiver](#nskeyedarchiver)
- [Пример использования NSKeyedUnarchiver](#пример-использования-nskeyedunarchiver)
<!-- /TOC -->

##  Sandbox

Sandbox(песочница) - функция безопасности для iOS. Все наши данные и части нашего приложения, файлы, бандлы. Sanbox инкапсулирует их. 

<img src="https://github.com/OrientCue/ios/blob/master/_resources/dcd3da3d8b584d14aeae9fad3661e9e9.png?raw=true">

Для того чтобы нам получить данные за пределами песочницы его необходимо запросить. 

<img src="https://github.com/OrientCue/ios/blob/master/_resources/1858bd01213246039b5b03e0d5a659d4.png?raw=true">

Когда пользователь запрещает доступ, эти настройки можно изменить в Settings самого приложения 

<img src="https://github.com/OrientCue/ios/blob/master/_resources/d50ebea4056646409f8f09fa26967c25.png?raw=true">

Внутри sandbox также есть свои директории, с которыми можно работать. 

<img src="https://github.com/OrientCue/ios/blob/master/_resources/9efbce87465644b0a3eb075273648576.png?raw=true">

OS профили:
*.metadata.plist - описание политики безопасности
Location: `~/Library/Containers/<bundle_id>`

Папки:
- **Documents** - отличное место для сохранения пользовательских данных. Вы должны сами побеспокоится о своевременной очистке папки. Можно самому устроить иерархию папок. 
- **Library** - содержит папки \Application Support и \Caches. Вы имеете доступ только к ним. 
(Can be purged by operating system in case of memory warning.)
- **tmp** - временное хранилище

**!!ВАЖНО!!! папки Temp и Cashes система будет чистить сама. Обычно, Temp будет чистить после закрытия приложения. В Cashes данные могут хранится дольше. Единсвенное правильное место для сохранения данных - папка Documents.** 

Можно узнать путь к папкам вызвав функцию: 
`NSLog(@"%@", NSHomeDirectory());`
Результат будет примерно следующим: 
`/var/mobile/Containers/Application/08AA3B09- FB31-4DA9-9988-A2AB6BDDAE70`
 На симуляторе результат будет выглядеть примерно так:
`/Users/your_user_name/Library/Developer/CoreSimulator/Devices/5-XXXX-XXXX-XXXX-XXXXXXXXXXXX/data/Containers/ Data/Application/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX`


URL for temp directory

```objc
NSURL *url = [NSURL fileURLWithPath:NSTemporaryDirectory()];
```
## Bundle
**Bundle** — это представление кода и ресурсов, хранящихся в каталоге проекта на диске (*.h, *.m, *.xib и.т.д). Bundle находится внутри sandbox.  



## NSUserDefaults
По умолчанию у нас есть пользовательское хранилище, база данных, где вы храните пары ключ-значение постоянно при вызовах вашего приложения. Это хранилище представляет собой, ни что иное как *.plist файл. 

<img src="https://github.com/OrientCue/ios/blob/master/_resources/0e0ca22570824ffeb3f3daf15bb4a28b.png?raw=true">


Поддерживает следующие форматы данных:
- **NSData**
- **NSString**
- **NSNumber**
- **NSDate**
- **NSArray**
- **NSDictionary**

Если нужно сохранить объект, его необходимо привести к любом вышеописанному формату. 

Что бы записать или считать обекты, необходимо обратится к следующим методам:

```objc
[[NSUserDefaults standardUserDefaults] setObject:@"OBJECT"forKey:@"KEY"];
    [[NSUserDefaults standardUserDefaults] objectForKey:@"KEY"];
```
Записанные значение можно наблюдать, используя механизм key-value observing. 

У NSUserDefaults есть метод для сохранения состояния sychonized(). В ранних версиях его необходимо было вызывать всегда, но сейчас его необходимо вызывать только для принудительного сохранения состояния. 

Все возвращаемые объекты NSUserDefaults имутабельны. 

В NSUserDefaults имеется возможность зарегистрировать значение по умолчанию для определенного ключа. 

```objc
NSUserDefaults *standartDefaults = [NSUserDefaults standardUserDefaults];
    if (![standartDefaults objectForKey:@"timeout"]) {
        [standartDefaults setObject:timeout forKey:@"timeout"];
    }
    [[NSUserDefaults standardUserDefaults] registerDefaults:@{
        @"timeout": timeout
    }];
```

## NSKeyedArchiver

**NSKeyedArchiver**, подкласс **NSCoder**, предоставляет способ кодирования объектов (и скалярных значений) в независимый от архитектуры формат, который можно сохранить в файле. Когда вы архивируете набор объектов, информация о классе и переменные экземпляра для каждого объекта записываются в архив.
Сопутствующий класс NSKeyedUnarchiver декодирует данные в архиве и создает набор объектов, эквивалентный исходному набору.

## Пример использования NSKeyedUnarchiver
1)	Определить в своей классе требуемые методы <NSSecureCoding>

```objc
+ (BOOL)supportsSecureCoding {
  return true;
}

- (void)encodeWithCoder:(nonnull NSCoder *)coder {
  [coder encodeInteger:self.age forKey:@"age"];
  [coder encodeObject:self.firstName forKey:@"firstName"];
  [coder encodeObject:self.avatar forKey:@"avatar"];
}

- (nullable instancetype)initWithCoder:(nonnull NSCoder *)coder {
  if (self = [super init]) {
    _firstName = [coder decodeObjectOfClass:NSString.class forKey:@"firstName"];
    _avatar = [coder decodeObjectOfClass:NSImage.class forKey:@"avatar"];
    _age = [coder decodeIntegerForKey:@"age"];
  }
  return self;
}
```

2)	использовать методы NSKeyedUnarchiver

```objc
// encode object
NSData *data = [NSKeyedArchiver archivedDataWithRootObject:user requiringSecureCoding:true error:&error];
// decode object
User *user = [NSKeyedUnarchiver unarchivedObjectOfClass:User.class fromData:data error:&error];
```

Ключи, предоставляемые закодированным значениям, должны быть уникальными только в рамках текущего объекта.
NSKeyedUnarchiver является иерархическим, поэтому ключи, используемые объектом A для кодирования переменных экземпляра, не конфликтуют с ключами, используемыми объектами B, даже если A и B являются экземплярами одного и того же класса.
Однако в пределах одного объекта ключи, используемые подклассом, могут конфликтовать с ключами, используемыми в его суперклассах.




