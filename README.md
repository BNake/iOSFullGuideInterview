# iOS Full Guide Interview
Questions &amp; Answers

##### Обозначения ответов

```
#### Answer:
Картинка 
![image text](URL)
```

##### Прогресс:
- [ ] Вопросы на которые нужно найти ответы. 
- [x] Готовые вопросы. 

##### Используемые обозначения(дополняется):

📌 Выучить назубок
🆘 Нужна помощь

#### Objective-C, Foundation:

---
- [x] **Что такое свойство?**
 
```
@interface TestClass
@property NSString *someString;
@property int someInt;
@end
@implementation testClass
@end
```
Свойство - это переменная экземпляра, объединенная с автогенераторами и сеттерами. Для свойства, называемого someString , getter и setter называются someString и setSomeString: соответственно. Имя переменной экземпляра, по умолчанию, имя свойства с префиксом подчеркивания (так что переменная экземпляра для someString называется _someString , но это может быть изменено с @synthesize директивы в @implementation разделе:
```
@synthesize someString=foo;    //names the instance variable "foo"
@synthesize someString;    //names it "someString"
@synthesize someString=_someString;        //names it "_someString"; the default if 
                                           //there is no @synthesize directive
```
Доступ к свойствам можно получить, вызвав геттеры и сеттеры:
```
[testObject setSomeString:@"Foo"];
NSLog(@"someInt is %d", [testObject someInt]);
```
Их также можно получить с помощью точечной нотации:
```
testObject.someString = @"Foo";
NSLog(@"someInt is %d", testObject.someInt);
```

---
- [x] **Как правильно реализовать сеттер для свойства с параметром retain?**
#### Answer:
Aтрибуты владения (retain/strong/copy/assign/unsafe_unretained/weak). 

retain (соответствующая переменная должна быть с атрибутом _strong) — это значение показывает, что в сгенерированном сеттере счетчик ссылок на присваиваемый объект будет увеличен, а у объекта, на который свойство ссылалось до этого, — счетчик ссылок будет уменьшен. Это значение применимо при выключенном ARC для всех Objective-C классов во всех случаях, когда никакие другие значения не подходят. Это значение сохранилось со времен, когда ARC еще не было и, хотя ARC и не запрещает его использование, при включенном автоматическом подсчете ссылок лучше вместо него использовать strong.
```
//примерно вот такой сеттер будет сгенерирован для свойства 
//с таким значением атрибута владения с отключенным ARC
-(void)setFoo(Foo *)foo {
  if (_foo != foo) {
    Foo *oldValue = _foo;

    //увеличивается счетчик ссылок на новый объект и укзатель на него сохраняется в ivar
    _foo = [foo retain];

    //уменьшается счетчик ссылок на объект, на который раньше указывало свойство
    [oldValue release]; 
  }
}
```
strong (соответствующая переменная должна быть с атрибутом _strong) — это значение аналогично retain, но применяется только при включенном автоматическом подсчете ссылок. При использовании ARC это значение используется по умолчанию. Используйте strong во всех случаях, не подходящих для weak и copy, и все будет хорошо.

Retain — посылает release текущему значению переменной экземпляра, потом посылает retain новому объекту, и присваивает новое значение переменной экземпляра. 
 
---
- [x] **Директива @synthesize, @dynamic, чем отличаются друг от друга?**

@synthesize — дает указание компилятору, что необходимо автоматически сгенерировать сеттеры и геттеры для данных (разделенных запятой) свойств. Сеттеры и геттеры автоматически создаются согласно указанным в объявлении свойства директивам. Если переменные экземпляра называются не так, как указано после директивы @property, вы можете соединить их с помощью знака равенства
@dynamic — сообщает компилятору, что требуемые сеттеры и геттеры для данных свойств будут реализованы вручную или динамически во время выполнения. При доступе к таким свойствам, компилятор не будет выдавать предупреждений, даже если требуемые сеттеры и геттеры не реализованы. Вы можете использовать такие свойства, когда хотите, чтобы сеттеры и геттеры выполняли какой-то специфичный для вас код.
@end — указывает на завершение определения класса или категории.

@synthesize будет генерировать методы getter и setter для вашего свойства. @dynamic просто сообщает компилятору, что методы getter и setter реализуются не самим классом, а где-то в другом месте (например, суперкласс или будут предоставлены во время выполнения).

Использование для @dynamic - это, например, с подклассами NSManagedObject(CoreData) или когда вы хотите создать выход для свойства, определенного суперклассом, который не был определен как выход.

@dynamic также может использоваться для делегирования ответственности за внедрение аксессуаров. Если вы реализуете аксессуры самостоятельно в классе, вы обычно не используете @dynamic.

Супер класс:
```
@property (nonatomic, retain) NSButton *someButton;
...
@synthesize someButton;
```
Подкласс:
```
@property (nonatomic, retain) IBOutlet NSButton *someButton;
...
@dynamic someButton;
```
---
- [x]	**В чем разница между точечной нотацией и использованием квадратных скобок?**

При точечной нотации происходит диспетчеризация через геттер и сеттер
(если они переопределены).

Точечную нотацию правильно использовать только со свойствами.

То есть точку можно и, как многие считают, желательно, использовать для свойств, объявленных с помощью @property и нельзя (нежелательно, хотя и не невозможно) использовать для поведения, то есть для селекторов.

Так, например, в случае с updateInterface (это селектор, то есть поведение) нужно писать через [self updateInterface], а вот для, скажем, view.subviews лучше использовать for ```(NSView *v in myView.subviews) { ... };```, а не ```for (NSView *v in [myView subviews]) { ... };```

---
- [x] **Есть ли приватные и защищенные методы в Objective-C?**

Не совсем так.

ivar-ы могут иметь аксессоры: @public, @private, @protected.

Фактически до любого свойства можно достучаться через KVC или runtime.

Методы могут быть только public (в .h фале) и private (в .m файле)

---
- [x] **Чем категория отличается от расширения(extension, наименованная категория)?**

Категория - это способ добавления методов к существующим классам. Они обычно находятся в файлах, называемых "Class + CategoryName.h", например "NSView + CustomAdditions.h" (и .m, конечно).

Расширение класса является категорией, за исключением двух основных отличий:
У категории нет имени. Он объявляется следующим образом:
```
@interface SomeClass ()
- (void) anAdditionalMethod;
@end
```
Реализация расширения должна быть в основном блоке @implementation файла.

Общепринято видеть расширение класса в верхней части файла .m, объявляющее больше методов в классе, которые затем реализованы ниже в основном разделе @implementation класса. Это способ объявить "псевдо-частные" методы (псевдо-частные в том смысле, что они не являются частными, а не внешними).

Кратко:
В категории объявляются дополнительные методы. 
В расширении добавляются методы и поля. Расширение представляет собой особый тип категории и 
находится внутри реализации.

---
- [x]	**Можно ли добавить ivar в категорию?**

Да, можно.
```
Class c = objc_allocateClassPair([NSObject class], "Person", 0);
class_addIvar(c, "firstName", sizeof(id), log2(sizeof(id)), @encode(id));
Ivar firstNameIvar = class_getInstanceVariable(c, "fistName");
```

---
- [x]	**Когда лучше использовать категорию, а когда наследование?**

Категория используется чтобы не плодить классы, только добавить нужный метод. 
(убрать в категорию анимацию для контроллера).

Наследование используется для переопределения существующих методов. 
(рутовый класс для контроллера).

При наследовании меняетcя поведение класса, обернув его в подкласс категория позволяет добавлять методы к существующим классам без наследования не создавая экземпляр класса, который она расширяет. Новые методы добавляются при компиляции и могут быть выполнены как обычные методы расширенного класса.  


---
- [x]	**Как имитировать множественное наследование?**

Наследоваться от класса, который в свою очередь наследуется от другого класса и т.д.


---
- [x]	**Можно ли чтобы разные классы реализовывали один протокол. (ClassA, ClassB и ClassC - реализуют ClassCProtocol)?**

Ну, да, можно.
Так работают делегаты и те же датасорсы. 

---
- [x]	**Формальный и неформальный (informal) протокол? Протоколы (protocols): основные отличия между c#/java интерфейсами и Objective-C протоколами? Что делать в случае если класс не реализует какой-то метод из протокола?**

Цели для которых используются протоколы:

Ожидание, что класс поддерживающий протокол выполнит описанные в протоколе функции
Поддержка протокола на уровне объекта, не раскрывая методы и реализацию самого класса (в противоположность наследованию)
Ввиду отсутствия множественного наследования - объединить общие черты нескольких классов
Формальные протоколы

Объявление формального протокола гарантирует, что все методы объявленные протоколом будут реализованы классом

Неформальные протоколы

Добавление категории к классу NSObject называется созданием неформального протокола. При работе с неформальными протоколами мы реализуем только те методы, которые хотим. Узнать поддержевает ли класс какой либо метод можно с помощью селекторов:
```
First *f = [[First alloc] init];
if ([f respondsToSelector:@selector(setName:)]) {
    NSLog (@"Метод поддерживается");
}
```
Неформальный протокол - категория над NSObject, которая заставляет все объекты 
адаптировать этот протокол. 

Формальный - обычный протокол @protocol

---
- [x]	**Почему нам не следует вызывать instance методы в методе -init (initialize)?**
Вызовем бесконечный цикл, получается будет инит в ините, просто вызовем краш билда.

---
- [x]	**Что такое назначенный инициализатор (designated initializer)? Приведите пример назначенного инициализатора (имеется ввиду if (self = [super ...]))?**

Назначенный инициализатор (designated initializer) - это главный инициализатор(конструктор), все остальные методы
создающие класс вызывают этот метод.
Как выглядит назначенный инициализатор?
У объектов бывает сразу несколько методов начинающихся с init, например init, initWithName, 
initWithName:balance: и тд

Установившейся практикой в таком случае является выделение среди всех init-методов одного, 
назвыемого designated initializer. Все остальные init-методы должны вызывать его и только он
вызывает унаследованный init-метод.

В нашем случае designated initializer будет -initWithName:balance:
@interface BankAccount : NSObject
```
//Properties
@property (copy) NSString *name;
@property (copy) NSDecimalNumber *balance;


- (id)initWithName:(NSString *)name;

//Designated initializer
- (id)initWithName:(NSString *)name balance:(NSDecimalNumber *)balance;
@end
@implementation BankAccount

- (id)initWithName:(NSString *)name {
    return [self initWithName:name balance:[NSDecimalNumber zero]];
}

//Designated initializer
- (id)initWithName:(NSString *)name balance:(NSDecimalNumber *)balance {
    if ((self = [super init])) {
        self.name = name;
        self.balance = balance;
    }
    return self;
}
```
Как обозначать/маркировать назначенный инициализатор?

Используем макрос NS_DESIGNATED_INITIALIZER для маркировки назначенного инициализатора
```
@interface MyClass : NSObject
@property(copy, nonatomic) NSString *name;
-(instancetype)initWithName:(NSString *)name NS_DESIGNATED_INITIALIZER;
-(instancetype)init;
@end
```
---
- [x]	**Что происходит когода мы пытаемся вызвать метод у nil указателя? Разница между nil и Nil и [NSNull null]?**

Ничего. 
Это безопасно, поскольку не надо делать проверку объекта на nil.

Разница между nil и Nil и [NSNull null]?
```
#define (id)0 nil - указатель на нулевой объект

#define (Class *)0 Nil - нулевой указатель типа Class
```
---
- [x]	**Что такое селектор (selector)? Как его вызвать? как отложить вызов селектора? Что делать если селектор имеет много параметров? (NSInvocation)**

Селектор - это имя метода закодированное специальным образом, используемым objective-c для быстрого поиска. Указание компилятору на селектор происходит при помощи директивы @selector(метод)
```
First* f = [[First alloc] init];
if([f respondsToSelector:@selector(setName:)])
	NSLog (@"Метод поддерживается");
  ```
В этом примере создается экземпляр класса First - f (наследник NSObject), после с помощью метода respondsToSelector проверяем может ли класс ответить на метод setName  			
  		

Это по сути строковое сообщение, которое посылается объекту, и он выполняет метод, ассоциированый с этим селектором.
Как его вызвать?
```
[self performSelector:@selector(method)];
```
Как отложить вызов селектора?
```
[self performSelector:@selector(method) withObject:obj afterDelay:delay];
```
Что делать если селектор имеет много параметров?
```
@selector(method:::) - двоиточий столько сколько параметров.
```
(NSInvocation)
```
Вызывает метод у объекта, можно менять параметры и цель.
```
Как запустить селектор во фоновом потоке?
```
[self performSelectorInBackground:selector withObject:obj];
```

---
- [x]	**Что такое быстрое перечисление (fast enumeration)?**

Это итерация по обьектам любого класса, который реализует протокол NSFastEnumeration, в том числе NSArray, NSSet и NSDictionary. Реализация протокола состоит из одного метода: -(NSUInteger)countByEnumeratingWithState:(NSFastEnumerationState *)state objects:(id *)stackbuf count:(NSUInteger)len;  

Итерация (цикл) по коллекциям (NSArray, NSDictionary): 
for Type *item in Collection {}

---
- [x] **Что такое делегат (delegate)?**

Делегат - объект который использует другой объект для реализации тех или иных функций.

Делегат - это просто объект, которому другой объект отправляет сообщения, когда происходят определенные вещи, так что делегат может обрабатывать специфические для приложения детали, для которых не был создан исходный объект. Это способ настройки поведения без подкласса.

---
- [x] **Что такое notifications (уведомления)?**

Notificaiton – механизм использования возможностей NotificationCenter самой операционной системы. Использование NSNotificationCenter позволяет объектам коммуницировать, даже не зная друг про друга. Это очень удобно использовать когда у вас в паралельном потоке пришел push-notification, или же обновилась база, и вы хотите дать об этом знать активному на даный момент View. Чтобы послать такое сообщение стоит использовать конструкцию типа:
```
NSNotification *broadCastMessage = [NSNotification notificationWithName:@"broadcastMessage" object:self];
NSNotificationCenter *notificationCenter = [NSNotificationCenter defaultCenter];
```
Как видим, мы создали объект типа NSNotification, в котором мы указали имя нашего оповещения: “broadcastMessage”, и, собственно, сообщили о нем через NotificationCenter. Чтобы подписаться на событие в объекте который заинтересован в изменении стоит использовать следующую конструкцию:
```
NSNotificationCenter *notificationCenter = [NSNotificationCenter defaultCenter];
[notificationCenter addObserver:self selector:@selector(update:) name:@"broadcastMessage" object:nil];
```
Собственно, из кода все более-менее понятно: мы подписываемся на событие, и вызывается метод который задан в свойстве selector.

[http://macbug.ru/cocoa/notifications]

---
- [x]	**Какая разница между использованием делегатов (delegation) и нотификейшенов (notification)?**

Уведомление инкапсулирует информацию о событиях, таких как получения фокуса окном или закрытие сетевого соединения. Объекты, которым необходимо знать об этих событиях, регистрируются в центре уведомлений, и при возникновении зарегистрированного события, центр уведомлений информирует все зарегистрированные объекты об событии.

Делегат может модифицировать операцию или отказаться от нее, нотификейшн – прямой приказ.

Основные правила:

Центры оповещений не владеют своими наблюдателями. Если объект является наблюдателем, он обычно удаляется из центра оповещений в своем методе dealloc:
```
- (void)dealloc {
	[[NSNotificationCenter defaultCenter] removeObserver:self];
}
```
Объекты не владеют своими делегатами и источниками данных. Если вы создаете объект, который является делегатом или источником данных, он должен «попрощаться» в своем методе dealloc:
```
- (void)dealloc {
	[windowThatBossesMeAround setDelegate:nil];
	[tableViewThatBegsForData setDataSource:nil];
}
```
Объекты не владеют своими приемниками. Если вы создаете объект, который является приемником, он должен обнулить указатель на приемник в своем методе dealloc:
```
- (void)dealloc {
	[buttonThatKeepsSendingMeMessages setTarget:nil];
}
```
Делегат
Плюсы

Very strict syntax. All events to be heard are clearly defined in the delegate protocol.
Compile time Warnings / Errors if a method is not implemented as it should be by a delegate.
Protocol defined within the scope of the controller only.
Very traceable, and easy to identify flow of control within an application.
Ability to have multiple protocols defined one controller, each with different delegates.
No third party object required to maintain / monitor the communication process.
Ability to receive a returned value from a called protocol method. This means that a delegate can help provide information back to a controller.
Распределение кода, переиспользование => слабосвязанные объекты. Мой объект ничего не знает о делегате, но отправляет ему сообщение.

Минусы
Many lines of code required to define:
(1) the protocol definition
(2) the delegate property in the controller
(3) the implementation of the delegate method definitions within the delegate itself.

Need to be careful to correctly set delegates to nil on object deallocation, failure to do so can cause memory crashes by calling methods on deallocated objects.
Although possible, it can be difficult and the pattern does not really lend itself to have multiple delegates of the same protocol in a controller (telling multiple objects about the same event)

Нотификейшн

Плюсы
Easy to implement, with not many lines of code.
Can easily have multiple objects reacting to the same notification being posted.
Controller can pass in a context (dictionary) object with custom information (userInfo) related to the notification being posted.

Минусы
No compile time to checks to ensure that notifications are correctly handled by observers.
Required to unregister with the notification center if your previously registered object is deallocated.
Not very traceable. Attempting to debug issues related to application flow and control can be very difficult.
Third party object required to manage the link between controllers and observer objects.
Notification Names, and UserInfo dictionary keys need to be known by both the observers and the controllers. If these are not defined in a common place, they can very easily become out of sync.
No ability for the controller to get any information back from an observer after a notification is posted.

Наблюдение
Плюсы
Can provide an easy way to sync information between two objects. For example, a model and a view.
Allows us to respond to state changes inside objects that we did not create, and don’t have access to alter the implementations of (SKD objects).
Can provide us with the new value and previous value of the property we are observing.
Can use key paths to observe properties, thus nested objects can be observed.
Complete abstraction of the object being observed, as it does not need any extra code to allow it to be observed.

Минусы
The properties we wish to observe, must be defined using strings. Thus no compile time warnings or checking occurs.
Refactoring of properties can leave our observation code no longer working.
Complex “IF” statements required if an object is observing multiple values. This is because all observation code is directed through a single method.
Need to remove the observer when it is deallocated.

---
- [x]	**Какая разница между использованием селекторов, делегатов и блоков?**

Простыми словами:
Селектор - это метод который обработает какое-то действие.

Например: у нас есть кнопка и мы указываем селектор с методом myButtonWasPressed, этот метод будет вызван по нажатию на кнопку.
```
[myButton addTarget:self
             action:@selector(myButtonWasPressed)
   forControlEvents:UIControlEventTouchUpInside];

- (void)myButtonWasPressed {
    // Do something about it
}
```

Делегат - это когда один класс работает внутри другого класса. Например у нас есть таблица UITableView и мы хотим чтобы она отображалась в контроллере MainViewController, мы устанавливаем для таблицы делегат MainViewController и теперь методы делегаты для построения ячейки таблицы будут вызываться в MainViewController

Блоки — это замыкания. Прекрасно, но от этого не легче. Говоря другим языком, замыкание — функция, которая ссылается на свободные переменные в своём контексте.
Блоки — это объекты Objective-C. Оно и логично, если мы собираемся их передавать методам или функциям. И как объекты, блоки имеют тип. Но блоки так же возвращают значения, а значит ведут себя как функции.
Когда мы создаем блок, который захватывает внешние переменные, блок размещается в стеке.
Блок — объект Objective-C и располагается в стеке? С обычными объектами Objective-C так поступать не разрешают, но с блоками можно.
Причина, по которой блоки поместили в стек по умолчанию, — это скорость. В общем случае, когда время жизни блока меньше, чем у функции стека, содержащей его, это очень хорошая оптимизация.

ри выполнении асинхронных методов часто требуется знать о том, что метод отработал, да и значения, которые он вернул так же пригодятся.

При разработке под ios очень часто для этих целей используется паттерн Delegate. Буквально с первых строк кода вы можете наблюдать, что сам Xcode создал файл и класс AppDelegate.

Реализовать паттерн Делегат можно и при помощи блоков. Часто применение блоков даже более удобно.  Код получается более компактный, не требуется создание дополнительный протоколов и свойств классов, да еще следить за тем, чтобы делегаты следовали протоколу… А еще проверять, что делегат все еще жив. Всю прелесть работы с множеством интерфейсов вы можете оценить, реализовав архитектуру Viper (не будем здесь о ней), о которой в сети множество всяких статей. Фанаты интерфейсов… вы еще придете к блокам 🙂 

Блоки, в качестве обработчиков завершения добавляют в виде параметров в нужный метод.

Блоки прекрасны еще и тем что они могут выполнятся в бекграунде, и могут вернуть результат своей работа в метод, который уже отработал. До тех пор, пока работает блок, он удерживает всеь контекст метода, который ему необходим.

Поскольку блок — это объект, который имеет свой тип данных, можно легко создать свойства этого типа.

[https://isamples.ru/%D0%B1%D0%BB%D0%BE%D0%BA%D0%B8-%D0%B2-objective-c/]

---
- [x]	**В чем отличия KVC-KVO?**

Key-Value-Coding (KVC) means accessing a property or value using a string.
```
id someValue = [myObject valueForKeyPath:@"foo.bar.baz"];
```
Which could be the same as:
```
id someValue = [[[myObject foo] bar] baz];
```
Key-Value-Observing (KVO) allows you to observe changes to a property or value.

To observe a property using KVO you would identify to property with a string; i.e., using KVC. Therefore, the observable object must be KVC compliant.
```
[myObject addObserver:self forKeyPath:@"foo.bar.baz" options:0 context:NULL];
```
Кратко. 
KVC означает кодирование ключевых значений. Это механизм, с помощью которого свойства объекта можно получить с помощью строки во время выполнения, вместо того, чтобы статически знать имена свойств во время разработки.

KVO означает наблюдение за ключевыми значениями и позволяет контроллеру или классу наблюдать за изменением значения свойства. В KVO объект может запросить уведомление о любых изменениях конкретного свойства: когда это свойство изменяет значение, наблюдатель автоматически уведомляется.

---
- [x]	**Что такое KVO? Когда его нужно использовать? Методы для обозревания объектов? Работает ли KVO с instance переменными (полями) объекта?**


KVO
Еще одна реализация патерна наблюдатель. В этом случае наблюдатель следит не за событиями, а за конкретным свойством объекта. Когда значение этого свойства меняется, наблюдателю приходит уведомление и он соответствующим образом реагируют. По сравнению со многими другими языками реализация KVO в objective c радуют довольно простым синтаксисом. Так в коде наблюдателя достаточно написать:
```
[company_a addObserver:self forKeyPath:@"people" options:NSKeyValueObservingOptionNew context:nil];
```
И каждый раз когда в company_a будет изменяться значение переменной people наблюдатель будет уведомляться с помощью вызова метода - observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary *)change context:(void *)context и надо лишь реализовать код, который будет реагировать на уведомление.

Плюсы

Минимализм кода (достаточно написать всего лишь несколько строчек, чтобы полностью реализовать паттерн наблюдатель)
Возможность слежения за любыми свойствами любых классов как написанными нами, так и чужими. Фактически внешние переменные всегда оформляются через свойства, что позволяет с легкостью следить любыми изменениями.
Недостатки

Первая и очень важная проблема — это заметное падение производительности при обильном использовании KVO. Не стоит писать код, где ваши объекты общаются в основном через KVO. Рассматривайте KVO как вспомогательно средство для работы с чужим кодом, а не как основной инструмент.
Второй проблемой является необходимость очень аккуратно писать код при использовании KVO. Так как строковые идентификаторов не проверяются компилятором на валидность, то это может привести к ошибкам при переименовании переменных. Также, KVO очень чувствительно к порядку добавления / удаления наблюдателей. Так, если наблюдатель пытается отписаться от наблюдаемого, на который наблюдатель в данный момент не подписан, то происходит крэш. Если же, наоброт, наблюдатель не отпишется до того, как наблюдаемый будет уничтожен, то произойдет утечка памяти. Все это приводит к тому, что легко прострелить себе ногу при добавлении / удалении наблюдателей из разных мест кода. Наиболее простой способ обезопасить себя – это хранить в наблюдателе ссылку на объект наблюдения, метод присвоения которой описан следующим образом:
```
- (void) setObservable:(id)observable {
	[_observable addObserver:self forKeyPath:@"property" options:NSKeyValueObservingOptionNew context:nil];
    _observable = observable;
    [_observable removeObserver:self forKeyPath:@"property"];
}
```
Таким образом соотношение между добавлением и удалением строго равно единице и нет необходимости тщательно следить где именно добавляется / удаляется наблюдение. Вызов деструктора также больше не является проблемой, так как перед уничтожением объекта в свойство запишется nil, попутно завершив наблюдение за объектом.

Always handle nil and other unusual values gracefully
Don't use accessors (or dot notation) in init or dealloc

---
- [x]	**Что такое KVC? Когда его нужно использовать?**

KVC
Key-value coding is a mechanism for accessing an object’s properties indirectly, using strings to identify properties, rather than through invocation of an accessor method or accessing them directly through instance variables. In essence, key-value coding defines the patterns and method signatures that your application’s accessor methods implement. Implementing key-value coding compliant accessors in your application is an important design principle. Accessors help to enforce proper data encapsulation, isolate memory management to centralized locations, and facilitate integration with other technologies such as key-value observing, Core Data, Cocoa bindings, and scriptability. Соответствующие методы определены в NSObject, поэтому каждый объект поддерживает данную возможность.
```
[a setProductName:@"Washing Machine"];
```
C использованием KVC:
```
[a setValue:@"Washing Machine" forKey:@"productName"];
```
Запись «ключ-значение» также позволяет прочитать значение переменной. Каждый раз, когда стандартная библиотека записывает данные в ваши объекты, она использует setValue:forKey:. Каждый раз, когда стандартная библиотека читает данные из ваших объектов, она использует valueForKey:. Например, библиотека СоrеData упрощает сохранение объектов в базе данных SQLite и их последующую загрузку. Для работы с пользовательским объектами, содержащими данные, используется запись «ключ-значение».
```
double totalSalary = 0.0;
for (Employee *employee in employees) {
	totalSalary += [employee.salary doubleValue];
}
double averageSalary = totalSalary / [employees count];
```
эквивалентно
```
[employees valueForKeyPath:@"@avg.salary"];
```
KVC Collection Operators allows actions to be performed on a collection using key path notation in valueForKeyPath:. Any time you see @ in a key path, it denotes a particular aggregate function whose result can be returned or chained, just like any other key path. Collection Operators fall into one of three different categories, according to the kind of value they return:

Simple Collection Operators return strings, numbers, or dates, depending on the operator.
Object Operators return an array.
Array and Set Operators return an array or set, depending on the operator.
Simple Collection Operators

@count Returns the number of objects in the collection as an NSNumber.

@sum Converts each object in the collection to a double, computes the sum, and returns the sum as an NSNumber.

@avg Takes the double value of each object in the collection, and returns the average value as an NSNumber.

@max Determines the maximum value using compare:. Objects must support comparison with one another for this to work.

@min Same as @max, but returns the minimum value in the collection.

---
- [x]	**Какие существуют root классы в iOS? Для чего нужны root классы?**

NSObject, NSProxy, Protocol, Class.
Для чего нужны root-классы?
NSObject - корневой класс для всех классов, реализует базовую функциональность.

NSProxy - позволяет предварительно конфигурировать группы объектов по определенным 
шаблонам.


---
- [x]	**Как работает proxy?**

The Proxy design pattern provides a surrogate, or placeholder, for another object in order to control access to that other object. You use this pattern to create a representative, or proxy, object that controls access to another object, which may be remote, expensive to create, or in need of securing. This pattern is structurally similar to the Decorator pattern but it serves a different purpose; Decorator adds behavior to an object whereas Proxy controls access to an object.

NSProxy

The NSProxy class defines the interface for objects that act as surrogates for other objects, even for objects that don’t yet exist. A proxy object typically forwards a message sent to it to the object that it represents, but it can also respond to the message by loading the represented object or transforming itself into it. Although NSProxy is an abstract class, it implements the NSObject protocol and other fundamental methods expected of a root object; it is, in fact, the root class of a hierarchy just as the NSObject class is. Concrete subclasses of NSProxy can accomplish the stated goals of the Proxy pattern, such as lazy instantiation of expensive objects or acting as sentry objects for security. NSDistantObject, a concrete subclass of NSProxy in the Foundation framework, implements a remote proxy for transparent distributed messaging. NSDistantObject objects are part of the architecture for distributed objects. By acting as proxies for objects in other processes or threads, they help to enable communication between objects in those threads or processes. NSInvocation objects, which are an adaptation of the Command pattern, are also part of the distributed objects architecture.

Uses and Limitations

Cocoa employs NSProxy objects only in distributed objects. The NSProxy objects are specifically instances of the concrete subclasses NSDistantObject and NSProtocolChecker. You can use distributed objects not only for interprocess messaging (on the same or different computers) but you can also use it to implement distributed computing or parallel processing. If you want to use proxy objects for other purposes, such as the creation of expensive resources or security, you have to implement your own concrete subclass of NSProxy.

---
- [x]	**Что такое тип id. Что случится во время компиляции если мы посылаем сообщение объекту типа id? Что случится во время выполнения если этот метод существует?**

id - это указатель на любой НЕПРИМИТИВНЫЙ объект. В ARC нельзя сконвертировать примитив в id.
Разница между NSObject и id?

Все непримитивные объекты являются типом id. 
Типу id можно посылать какие угодно сообщения. 
Объекту NSObject можно посылать только декларированые у него сообщения.
Явное указание типа объекта помогает компилятору искать нужные методы и разрешать неоднозначности.

Что случится во время компиляции если мы посылаем сообщение объекту типа id?
Выполнит соответствуюший метод, если найдёт, иначе выбросит исключение.

---
- [x]	**Цепочка ответсвенности, что происходит с методом после того как он не нашелся в объекте класса, которому его вызвали (в сторону forwardInvocation:)?**

Вылет из приложения с NSUnknownException.

---
- [ ]	**Что такое указатель isa? Для чего он нужен?**

Указатель на Class для данного объекта. Каждый объект имеет ссылку на свой класс через isa.


В целом, базовое определение объекта в Objective-C может выглядеть следующим образом:
```
typedef struct objc_object {
    Class isa;
} *id;
```

Это означает, что любая структура данных, которая начинается с указателя на класс, может быть рассмотрена как объект.

Важнейшей особенностью объектов в Objective-C является возможность отправлять сообщения объектам.
```
[@"stringValue"
    writeToFile:@"/file.txt" atomically:YES encoding:NSUTF8StringEncoding error:NULL];
```

Это работает потому что когда Вы отправляете сообщение объекту Objective-C (как NSCFString выше), то среда выполнения следует указателю на класс isa, который ведет к классу объекта (к классу NSCFString как в нашем случае). Класс содержит список методов, которые можно применять ко всем объектам данного класса и указатель на суперкласс (или надкласс) для просмотра наследуемых методов. Среда выполнения просматривает список методов класса и супекласса чтобы найти метод, совпадающий с селектором сообщения (в вышеуказанном случае, writeToFile:atomically:encoding:error on NSString). Далее среда выполнения вызывает функцию (IMP) для данного метода. 

Важным моментом является то, что Класс определяет, какие сообщения можно отправлять объекту.

---
- [x]	**В чем разница между NSArray и NSMutableArray? непотоко-безопасный NSMutableArray?**

NSArray - неизменяемый

NSMutableArray - изменяемый, непотоко-безопасный.

NSArray и NSMutableArray являются базовыми классами и типами данных, которые представляют поведение массива. Вы можете хранить любой объект не-примитивных типов в обоих из них. Оба сохраняют объекты, которые они хранят, и освобождают объекты, когда они удаляются, или сам объект массива освобождается. Когда использовать какой? Ну, если вы вряд ли добавите/удалите объекты в/из массива, вы должны использовать NSArray, вызвав один из статических методов и указав объекты для хранения, то есть:
```
NSArray *colors = [NSArray arrayWithObjects:@"Red", @"Green", @"Blue", nil];
```
Если вы, вероятно, добавите/удалите объекты в/из массива после его создания, вы должны использовать NSMutableArray. Вы можете создать массив с или без указания начальных объектов для хранения, а затем добавлять/удалять объекты в/из массива в любое время, то есть:
```
NSMutableArray *colors = [[NSMutableArray alloc] init];
[colors addObject:@"Red"];
[colors addObject:@"Green"];
[colors addObject:@"Blue"];
[colors removeObjectAtIndex:0];
NSLog(@"Color: %@", [colors objectAtIndex:1]);
[colors release];
```

---
- [x]	**Чем отличается NSSet от NSArray? Какие операции происходят быстро в NSSet и какие в NSArray?**

NSSet предназначен для создания несортированных массивов данных (например каких-либо объектов). Стоит обратить внимание, что объект, который хранится в NSSet, встречается только один раз. Т.е. все элементы NSSet — уникальные. Добавить дубликат элемента в NSMutableSet у вас также не получится. Для создания несортированного массива, в котором можно использовать неуникальные элементы, можно использовать NSCountedSet. Основным преимуществом NSCountedSet перед использованием классического массива NSArray является то, что элемент может быть продублирован огромное количество раз и при этом занимать памяти как один элемент. Это объясняется тем, что NSCountedSet хранит в памяти только одну копию элемента и запоминает сколько раз этот элемент встречается. Если для вас не важен порядок элементов внутри массива и вы используете действительно большие объемы информации, то использование NSSet повысит производительность приложения за счет снижения потребляемой памяти. Несмотря на то, что количество элементов хранящихся в памяти будет одинаковым, NSSet не тратит память на то, чтобы помнить в какой последовательности хранятся элементы.
```
NSSet *set = [NSSet setWithObjects:@"1", @"2", @"3", @"4", @"5", @"6", @"7", @"8", @"9", @"0", nil];
NSLog(@"%@", set);
```
```
{(
    7,
    3,
    8,
    4,
    0,
    9,
    5,
    1,
    6,
    2
)}
```
Отличие.
NSSet - хранит только уникальные объекты.
Операции.
NSArray - добавление, удаление.
NSSet - использует hashvalues для поиска объекта, является неупорядоченным.


---
- [x]	**Как удалить объект в ходе итерации по циклу?**

For clarity I like to make an initial loop where I collect the items to delete. Then I delete them. Here's a sample using Objective-C 2.0 syntax:
```
NSMutableArray *discardedItems = [NSMutableArray array];

for (SomeObjectClass *item in originalArrayOfItems) {
    if ([item shouldBeDiscarded])
        [discardedItems addObject:item];
}

[originalArrayOfItems removeObjectsInArray:discardedItems];
```
Then there is no question about whether indices are being updated correctly, or other little bookkeeping details.

Edited to add:
It's been noted in other answers that the inverse formulation should be faster. i.e. If you iterate through the array and compose a new array of objects to keep, instead of objects to discard. That may be true (although what about the memory and processing cost of allocating a new array, and discarding the old one?) but even if it's faster it may not be as big a deal as it would be for a naive implementation, because NSArrays do not behave like "normal" arrays. They talk the talk but they walk a different walk. See a good analysis here:

The inverse formulation may be faster, but I've never needed to care whether it is, because the above formulation has always been fast enough for my needs.

For me the take-home message is to use whatever formulation is clearest to you. Optimize only if necessary. I personally find the above formulation clearest, which is why I use it. But if the inverse formulation is clearer to you, go for it.

---
- [x]	**Как лучше всего загрузить UIImage c диска(с кеша)?**

В iOS присутствует функция кэширования, но прямого доступа к данным в кэше нет. Для получения доступа следует использовать класс NSCache. NSCache — это объекты, которые реализуют протокол NSDiscardableContent. Класс NSCache включает в себя различные политики автоматического удаления, обеспечивающие использование не слишком большого количества памяти системы.

---
- [x]	**Какой контент лучше хранить в Documents, а какой в Cache?**

NSDocumentDirectory - контент, созданный самим пользователем Library/Caches - все остальное

---
- [x]  **NSCoding, archiving?**

NSCoding это протокол для сериализации и десериализации данных. Реализуется двумя методами: -initWithCoder: и encodeWithCoder :. Классы, которые соответствуют NSCoding могут быть заархивированы с NSKeyedArchiver/NSKeyedUnarchiver

---
- [x]	**Протокол NSCopying, почему мы не можем просто использовать любой собственный объект в качестве ключа в словарях (NSDictionary) , что нужно сделать чтобы решить эту проблему? (разница между глубоким и поверхностным копированием)?**

NSCopying используется для создания копии экземпляра класса со всеми его свойствами Реализуется с помощью метода: -(id)copyWithZone:(NSZone *)zone

---
- [x]	**Суть рантайма (Runtime), отправление сообщения? Как работает Runtime?**

Runtime - предоставляет набор функций в системе исполнения для выполнения скомпилированного кода Автоматический поиск переменных/методов. Apple уже реализовало это в виде Key-Value Coding: вы задаете имя, в соответсвии с этим именем получаете переменную или метод. Вы можете делать это и сами, если вас чем-то не устраивает реализация Apple.(прим. переводчика — например, вот так Better key-value observing for Cocoa) Автоматическая регистрация/вызов подклассов. Используя objc_getClassList вы можете получить список классов, уже известных Runtime и, проследив иерархию классов, выяснить, какие подклассы наследуются из данного класса. Это дает вам возможность писать подклассы, которые будут отвечать за специфичиские форматы данных, или что-то в этом роде, и потом дать суперклассу возможность находить их самому, избавив себя от утомительной необходимости регистрировать их все руками. Автоматически вызвывать метод для каждого класса. Это может быть полезно для unit-testing фреймворков и подобного рода вещей. Очень похоже на #2, но с акцентом на поиск возможных методов, а не на иерархию классов Перегрузка методов во время исполнения. Runtime предоставляет вам полный набор инструментов для изменения реализации методов классов, без необходимости изменять что-либо в их исходном коде Bridging Имеея возможность динамически создавать классы и просматривать необходимые поля, вы можете создать мост между Objective-C и другим (достаточно динамичным) языком.

В Objective-C каждый вызов метода транслируется в соответствующий вызов функции objc_msgSend:
```
// Вызов метода
[array insertObject:foo atIndex:1];
// Соответствующий ему вызов Runtime-функции
objc_msgSend(array, @selector(insertObject:atIndex:), foo, 1);
```
Вызов objc_msgSent инициирует процесс поиска реализации метода в кеш таблице методов класса вверх по иерархии наследования — в суперклассах данного класса. Если же и при поиске по иерархии результат не достигнут, запускается динамический поиск — вызывается один из специальных методов: resolveInstanceMethod или resolveClassMethod. Этот механизм позволяет реализовать Method Swizzling.

---
- [x]	**Что представляет собой объект NSError?**

NSError is a Cocoa class

An NSError object encapsulates information about an error condition in an extendable, object-oriented manner. It consists of a predefined error domain, a domain-specific error code, and a user info dictionary containing application-specific information.

Error is a Swift protocol which classes, structs and enums can and NSError does conform to.

A type representing an error value that can be thrown.

Any type that declares conformance to the Error protocol can be used to represent an error in Swift’s error handling system. Because the Error protocol has no requirements of its own, you can declare conformance on any custom type you create.

The quoted parts are the subtitle descriptions in the documentation.

The Swift’s error handling system is the pattern to catch errors using try - catch. It requires that the error to be caught is thrown by a method. This pattern is much more versatile than the traditional error handling using NSError instances. If you're planning not to implement try - catch you actually don't need the Error protocol.


---
- [x]	**Что такое динамическая диспетчеризация (Dynamic Dispatch)?**

Like many other languages, Swift allows a class to override methods and properties declared in its superclasses. This means that the program has to determine at runtime which method or property is being referred to and then perform an indirect call or indirect access. This technique, called dynamic dispatch, increases language expressivity at the cost of a constant amount of runtime overhead for each indirect usage. In performance sensitive code such overhead is often undesirable. This blog post showcases three ways to improve performance by eliminating such dynamism: final, private, and Whole Module Optimization.

[https://developer.apple.com/swift/blog/?id=27]

---
- [x]	**Какую проблему решает делегирование?**

Основной шаблон проектирования, в котором объект внешне выражает некоторое поведение, но в реальности передаёт ответственность за выполнение этого поведения связанному объекту. Паттерн хорош тем, что нам не нужно хранить всю логику в одном месте и позволяет лучше переиспользовать код. Рассматривайте делегат как обычный обьект, который может выполнять некоторые функции. Например, возьмем NSTableView delegate. Вы хотите отрисовать ячейку таблицы как-то по своему. NSTableView своему делегату пошлет сообщение о том, что он сейчас будет рисовать данную ячейку и делегат уже сам решает что с ней делать (рисовать по своему, не трогать вообще и т.д.). Это, грубо говоря, способ получения и предоставления информации, о которой NSTableView не знает вообще ничего. Или же пример создания собственных делегатов. Представьте, что у вас есть свой класс, который выполняет некоторую функцию. Для выполнения некоторых задач ему необходима информация из другого класса, о котором сейчас не известно ровным счетом ничего, кроме того, что он существует. Тогда создается конструкция вида:
```
@interface Class1 {
	id delegate;
}
- (id)delegate;
- (void)setDelegate:(id)newDelegate;

@implementation Class1
- (id)delegate {
	return delegate;
}

- (void)setDelegate:(id)newDelegate {
	delegate = newDelegate;
}
```
Как видно из примера — наш делегат, это просто указатель на какой-либо обьект. Ну и предоставлены геттер и сеттер. Для того, чтобы делегат выполнил некоторое действие для нас, где-то внутри нашего Class1 мы пошлем сообщение вида
```
[delegate doSomeWork];
```
Обьект же, который мы назначили делегатом для данного класса в свою очередь получит это сообщение и начнет выполнять какое-то действие. В принципе и все. Достаточно просто. Делегирование преследует простую цель — сохранять объекты слабо связанными. Таким образом, вы можете отправлять сообщения делегату, не зная какой именно это объект. А сам делегат, при этом, может выполнять разные действия в зависимости от своей реализации. Так что тут мы имеем одно из проявлений полиморфизма. То есть, грубо говоря, делегирующий объект говорит объекту-делегату ЧТО делать, но его не волнует КАК именно это будет сделано. Плюс, делегирование порой может быть более удобной альтернативой наследованию — вместо того, чтобы плодить иерархию классов, вы определяете необходимый интерфейс для делегатов и используете их. 

---
- [x]	**Как добавить свойство в существующий объект с закрытой реализацией? Можно ли это сделать через runtime?**

Подмену через метод свизлинга, обязательно использовать диспатч. делать в load. И да, это все через рантайм.

---
- [x]	**Сколько различных аннотаций (annotations) доступно в Objective-C?**

В Objective-C есть Nullability annotations: _Null_unsepcified, _Nonull, _Nullable, N/A.

Null unspecified: bridges to a Swift implicitly-unwrapped optional. This is the default.

Non-null: the value won’t be nil; bridges to a regular reference.

Nullable: the value can be nil; bridges to an optional.

Non-null, but resettable: the value can never be nil when read, but you can set it to nil to reset it. Applies to properties only.

The nullability annotations are compiler checks, not runtime checks. 

Аннотации нужны по большей части когда нужно переносят проект на Swift (nice typed bridging).

---
- [x]	**Пожалуйста, объясните ключевое слово (keyword) final в классе?**

Нужно использовать кейворд из C/C++ "const". 
Final пришел из джавы, лучше использовать конст.

НО если речь идет про @finally, тогда вот:
@finally Определяет блок после блока @try, в который предается куправление независимо от того, было или нет сгенерировано исключение.


---
- [x]	**Почему мы используем шаблон делегата для уведомления о событиях текстового поля?**

Потому что так cocoa написан
А написано так потому что, например, у одоного контроллера может быть несколько текстовых полей, на навернка мы захотим чтобы он знал обо всех них.
Так что удобно было сдлеать его делегатом для всех.
И, к слову, ничего не мешало сделать это через блоки. Но во времена, когда текст филды появились их не особенно юзали. Через reactive, кстати, можно подписаться через блоки.

---
- [x]	**What is the difference Delegates and Callbacks?**


Коллбек - это по сути тот же метод, но живет он не так как метод. Метод, можно сказать, создается вместе с созданием класса. Коллбек - механизм создания «метода» в рантайме, причем такого, который по арку может захватить необходимые ему объекты.

Делегат же - просто описание схемы объекта. И этот подход очень хорошо ложится на обратные вызовы, т.к. можно описать что у какого-нибудь сервиса, должны быть методы и проперти для обработки данных, которые провоцирует сервис во время / по итогу своей работы.


#### Memory Management

---
- [x]	**Объявление свойств c атрибутами: retain, assign, nonatomic, readonly, copy, weak, strong, unsafe_unretained?**

@property (<атрибуты>) Type* propertyName; атрибуты: readwrite — у свойства есть как акцессор, так и мутатор. Является атрибутом по умолчанию. readonly — у свойства есть только акцессор.

assign — для задания нового значения используется оператор присваивания. Используется только для POD-типов либо для объектов, которыми мы не владеем. retain — указывает на то, что для объекта, используемого в качестве нового значения instance-переменной, управление памятью происходит вручную (не забываем потом освободить память). copy — указывает на то, что для присваивания будет использована копия переданного объекта. weak — аналог assign при применении режима автоматического подсчёта ссылок. (ARC должен поддерживаться компилятором) strong — аналог retain при применении режима автоматического подсчёта ссылок. (ARC должен поддерживаться компилятором)

Директива @synthesize автоматически генерирует set- и get-методы. Требует dealloc без ARC. @­synthesize property = _property; обязательно при одновременном переопределении set- и get-методов, для iVar не генерируется автоматически @property лишь генерирует сигнатуры геттера и мутатора. Но если свойство синтезированное (через @synthesize или по умолчанию), то кроме этого генерируется и ivar.


---
- [x]	**Реализуйте следующие методы: retain/release/autorelease?**

```
- (id)retain {
   	NSIncrementExtraRefCount(self);
    return self;
}

- (void)release {
    if (NSDecrementExtraRefCountWasZero(self)) {
         NSDeallocateObject(self);
    }
}

- (id)autorelease {  
// Add the object to the autorelease pool
    [NSAutoreleasePool addObject:self];
    return self;
}
```

--
- [x]	**Почему мы не используем strong для enum свойств?**

Поскольку enum не являются объектами, мы не указываем здесь strong или weak.

---
- [x]	**Как происходит ручное управление памятью - MRC в iOS?**

Manual Retain-Release (ручное сохранение-освобождение) или MRR – вы явно управляете памятью, отслеживая объекты, которые у вас есть. Это реализуется с помощью модели, известной как подсчет ссылок, что Foundation класса NSObject обеспечивает совместно со средой выполнения.

Используйте методы доступа, чтобы сделать управление памятью проще. Рассмотрим счетчик объекта, количество которого вы хотите установить.
```
@interface Counter : NSObject {
	NSNumber *_count;
}
@property (nonatomic, retain) NSNumber *count;
@end;
```
В свойстве объявляется два метода доступа. Как правило, вы должны попросить компилятор для синтезации методов, однако, поучительно посмотреть, как они могли бы быть реализованы. В get методе, вы просто возвращаете переменную экземпляра, так что нет необходимости в retain или release:
```
- (NSNumber *)count {
	return _count;
}
```
В set методе, если все остальные играют по тем же правилам, вы должны принять новое значение счетчика, которое может быть удалено в любое время, поэтому вам придется взять на себя ответственность объектно, отправив ему retain сообщение, чтобы убедиться что его не будет. Вы должны также отказаться от права владения на старый объект, отправив ему release сообщение. (Отправка сообщения к nil допускается в Objective-C, так что реализация все равно будет работать, если _count до сих пор не установлена.) Вы должны послать это после [newCount retain] на случай, если два и более объекта захотят, ненароком ее освободить.
```
- (void)setCount:(NSNumber *)newCount {
	[newCount retain];
	[_count release];
	// Make the new assignment.
	_count = newCount;
}
```
Использование методов доступа для установки значений свойств

Предположим, вы хотите реализовать метод для сброса счетчика. У вас есть несколько вариантов. Первая реализация создает экземпляр NSNumber с alloc, который вы балансируете releaseом.
```
- (void)reset {
	NSNumber *zero = [[NSNumber alloc] initWithInteger:0];
	[self setCount:zero];
	[zero release];
}
```
Второй использует удобство конструктора для создания нового объекта NSNumber. Он уже существует поэтому нет необходимости в retain или release

- (void)reset {
	NSNumber *zero = [NSNumber numberWithInteger:0];
	[self setCount:zero];
}
Обратите внимание, что оба используют set метод доступа. Следующий код почти наверняка работает правильно для простых случаев, но как заманчиво бы это не выглядело, чтобы отказаться от методов доступа, сделав это почти наверняка в будущем приведет к ошибке на определенном этапе (например, если вы забыли сохранить или освободить, или если изменится семантика управления памятью для переменной экземпляра).
```
- (void)reset {
	NSNumber *zero = [[NSNumber alloc] initWithInteger:0];
	[_count release];
	_count = zero;
}
```
Отметим также, что если вы используете методику ключ-значение (KVO), то изменение пере-менной, таким путем, не совместимо с KVO.

Не используйте методы доступа в методах инициализаторов и dealloc Единственные места, где вы не должны использовать методы доступа для установки переменной экземпляра находятся в методах - инициализаторе и dealloc. Для инициализации счетчика объекта со значением нуль, вы можете реализовать метод инициализации следующим образом:
```
- init {
	self = [super init];
	if (self) {
		_count = [[NSNumber alloc] initWithInteger:0];
	}
	return self;
}
```
Чтобы позволить счетчику при инициализации принимать значение отличное от нуля, вы можете реализовать метод initWithCount: следующим образом:
```
- initWithCount:(NSNumber *)startingCount {
	self = [super init];
	if (self) {
		_count = [startingCount copy];
	}
	return self;
}
```
Так как переменная счетчика класса - экземпляр объекта, необходимо также реализовать dealloc метод. Следует отказаться от владения любыми переменными экземпляра, отправив им сообщение release, и в конечном счете он должн вызывать реализацию супер класса:
```
- (void)dealloc {
	[_count release];
	[super dealloc];
}
```
Используйте weak (слабые) ссылки чтобы избежать сохранения циклов. Сохранение объекта создает сильную ссылку на этот объект. Объект не может быть освобожден, пока все его сильные ссылки не будут освобождены. Проблема, известная как сохранность цикла, может возникнуть, если два объекта имеют циклические ссылки, то есть, у них есть сильные ссылки друг на друга (либо непосредственно, либо через цепочку других объектов, каждый из которых имеет сильную ссылку на следующую ведущую к первой).

Не освобождайте объекты, которые используете
Не используйте dealloc для управления ограниченными ресурсами Вы должны управлять как правило, не ограниченными ресурсами, такими как дескрипторы файлов, сетевые подключения, и буферы или кэш в dealloc методе. В частности, вы не должны проектировать классы так, чтобы dealloc был вызван, когда вы думаете, он будет вызван. Вызов dealloc, может быть отложен или обойден, либо из-за ошибки или из-за падения приложения.
Коллекции собственных объектов

При добавлении объекта в коллекцию (например, массив, словарь, или набор), коллекция становится его владельцем. Коллекция откажется от владения объектом, когда объект удаляется из коллекции или коллекця будет освобождена. Так, например, если вы хотите создать массив чисел вы можете выполнить одно из следующих действий:
```
NSMutableArray *array = <#Получаем изменяемый массив#>;
NSUInteger i;
// ...
for (i = 0; i < 10; i++) {
	NSNumber *convenienceNumber = [NSNumber numberWithInteger:i];
	[array addObject:convenienceNumber];
}
```
В этом случае вы не вызываете alloc, так что нет необходимости вызывать release. Значит нет необходимости вызова retain для новых номеров (convenienceNumber), так как массив это сделает. Базовая модель используемая для управления памятью со счетчиком ссылок обеспечивается сочетанием методов, определенных в протоколе NSObject и стандартными методами именования. Класс NSObject также определяет метод dealloc, который вызывается автоматически, когда объект будет освобожден. Вы владеете каким-либо объектом вами созданным:

Вы можете создать объект, используя метод, имя которого начинается с alloc, new, copy, или mutableCopy.
Вы можете стать владельцем объекта, используя retain: Полученный объект, как правило, гарантированно остается в силе в течение выполнения метода, в котором он был получен, и этот метод также может безопасно вернуть объект к вызвовшему его методу. Вы используете retain в двух случаях:
В реализации метода доступа или инициализации метода, чтобы стать владельцем объекта, который нужно сохранить как значение свойства
Для предотвращения освобождения объекта как побочного эффекта от некоторых других операций
Если объект вам больше не нужен, вы должны отказаться от права собственности на него: Вы отказываетесь от права собственности на объект, послав ему сообщение release или autorelease сообщение. В Cocoa терминологии, из отказа от права владения объектом, как правило следует, "освобождение" объекта. Вы не должны отказаться от права владения объектом, которого у вас нет: Это всего лишь следствие предыдущих правил политики, заявиленных в явном виде. Используйте autorelease для отправки отложенного release. Вы не являетесь владельцем объектов, возвращаемых по ссылке: Некоторые методы в Cocoa указавают, что объект возвращается по ссылке (то есть, они не имеют аргумента типа ClassName ** или id *). Данная закономерность заключается в использовании NSError объекта, который содержит информацию об ошибках, если они происходит, о чем свидетельствуют initWithContentsOfURL:options:error: (NSData) и initWithContentsOfFile:encoding:error: (NSString). В этих случаях применяются те же правила, как уже было описано. При вызове любого из этих методов, вы не создаете NSError объект, так что вы не являетесь его владельцем. Реализация dealloc для отказа от права владения объектами: Класс NSObject определяет метод dealloc, который вызывается автоматически, когда объект не имеет владельцев и его память будет утилизирована, в терминологии Cocoa это называется "освободил" или "освобождена". Роль dealloc метода заключается в освобождении собственной памяти объекта, и распоряжением любыми ресурсами которыми он располагает, в том числе любыми переменными экземпляра объекта, которыми он владеет. Важно:

Вы никогда не должны ссылаться на dealloc метод другого объекта непосредственно.
Вы должны вызвать реализацию суперкласса в конце вашей реализации.
Вы не должны привязывать управление системными ресурсами, как к объектам кон-тролируемым временем жизни.

---
- [x]	**autorelease vs release?**

Autorelease pool это — механизм отложеного отказа от ответсвенности. Это означет что вы больше не хотите владеть объектом и он вам в общем-то не нужен, но не хотите чтобы он удалился прямо сейчас. Зачастую вам не нужно создавать свой пул, просто используется NSAutoreleasePool и то даже не напрямую. Чтобы поместить объект в autorelease pool нужно отправить ему сообщение autorelease (один объект может быть добавлен в пул несколько раз) и на следующем цикле сообщений autorelease pool отправит всем этим объектам сообщение release, так как сам получит dealloc после чего будет создан новый autorelease pool. Таким образом Cocoa ожидает, что как минимум один autorelease pool будет всегда, и автоматически создает его в main. Autorelease pool добавляются по принципу стека, самый последний добавленый пул будет в самом верху стека авторелиз пулов в текущем потоке. Создание пула осуществляется стандартным alloc и init, удаление drain. Если послать сообщение drain не самому верхнему пулу, то все пулы над ним тоже получат это сообщение и соответсвенно удалят все из себя. Оффициальная документация показывает нам вот такой пример использования своего пула:

NSArray *urls = <# An array of file URLs #>;
for (NSURL *url in urls) {
	NSAutoreleasePool *loopPool = [[NSAutoreleasePool alloc] init];
   	NSError *error = nil;
    NSString *fileContents = [[[NSString alloc] initWithContentsOfURL:url encoding:NSUTF8StringEncoding error:&error] autorelease];
    /* Process the string, creating and autoreleasing more objects. */
    [loopPool drain];
}
Мы тут видим, что был создан свой пул (он разместился вверху стека пулов и будет освобож-ден первым), в него добавляются NSString *fileContents и потом пул освобождается (а так как он самый верхний — освобождается только он).

---
- [x]	**Что означает ARC?**

Автоматический подсчет ссылок является компиляторной функцией, которая обеспечивает автоматическое управление памятью в Objective-C объектах. Вместо того, чтобы думать о со-хранении и освобождении объектов, ARC позволяет сосредоточиться на непосредственном ко-де Вашего приложения. ARC работает путем добавления кода во время компиляции, чтобы время жизни объекта было ровно столько, сколько необходимо, но не более того. Концептуально, это то же управление памятью, что и ручной подсчет ссылок (описанное в практическом управлении памятью) путем добавления соответствующего кода управления памятью, за вас. ARC поддерживается начиная с Xcode 4.2 для Mac OS X v10.6 и v10.7 (64-bit applications), а также iOS 4 и iOS 5. Слабые (weak) ссылки не поддерживаются в Mac OS X v10.6 и iOS 4 и более ранних. Существует несколько ограничений на использование механизма ARC.

Нельзя использовать свойство, имя которого начинается со слова new. Например, не допускается объявление
```
@property NSString *newString;
```
Нельзя использовать свойство только для чтения без атрибутов управления памятью. Если вы не используете механизм ARC, то можете использовать объявление
```
@property (readonly) NSString *title;
```
Но если вы используете механизм ARC, то должны указать, кто управляет памятью, так что достаточно просто вставить ключевое слово unsafe_unretained, потому что по умолчанию используется атрибут assign. ARC only works with retainable object pointers (ROPs). There are three kinds of retainable object pointers:

Block pointers
Objective-C object pointers
Typedefs marked with __attribute__((NSObject))
All other pointer types, such as char * and CF objects such as CFStringRef, are not ARC compatible. If you use pointers that aren’t handled by ARC, you’ll have to manage them yourself. That’s OK, because ARC interoperates with manually managed memory.


---
- [x]	**Что делать, если проект написан с использованием ARC, а нужно использовать классы сторонней библиотеки написанной без ARC?**

1 способ: Edit – Refactor – Convert to ObjC ARC

2 способ: App – Targets – Build Phases – Compile Sources – поставить флаг -fno-objc-arc


---
- [x]	**Atomic vs nonatomic. Чем отличаются? Как вручную переопределить atomic/nonatomic сеттер в не ARC коде?**

atomic (по-умолчанию) и nonatomic свойства с ключевым словом atmoic — потокобезопасны, с nonatomic — могут быть проблемы при многопоточном доступе. Доступ к nonatomic свойствам обычно быстрее чем к atomic, по-этому они часто используются в однопоточных приложениях.

Cинхронизировать чтение/запись между потоками или нет. Atomic – thread safe. Тут все сложнее и неоднозначнее, есть ряд способов как сделать threadsafe аксессоры к пропертям. Самый простой способ это сделать – добавить конструкцию 
```
@synchronized:

- (NSString *)foo {
    @synchronized(self) {
       	return foo;
    }
}

- (void)setFoo:(NSString)newFoo {
    @synchronized(self) {
       	if (foo != newFoo) {
          	[foo release];
          	foo = [newFoo retain];
       	}
    }
}
```
Таким образом используя @synchronized мы лочим по ключу self доступ к foo, однако у такого метода есть очевидный недостаток, если в классе будет две переменные (или 100500) к которым нужен одновременный доступ с разных потоков, то они будут лочиться и друг относительно друга, т.к self для них один и тот же, в таких случаях нужно использовать другие методы лока, как NSLock, NSRecursiveLock,...



---
- [x]	**Вопрос о циклах в графах владения (retain cycle)?**

RETAIN-ЦИКЛЫ

Давайте создадим пример retain-цикла. Во-первых, мы создадим RootViewController и SecondViewController. SecondViewController пудет показан, если кнопка на RootViewController будет нажата. Вы сможете создать такой переход легко с помощью Segue в Storyboard. Кроме того, у нас будет класс ModelObject, у которого будет объект делегата ModelObjectDelegate. Если SecondViewController загружен, то контроллер устанавливает себя как делегат для ModelObject:
```
import Foundation
 
protocol ModelObjectDelegate: class {
    
}
 
class ModelObject {
    
    var delegate: ModelObjectDelegate?
       
}
```

```
import UIKit
 
class SecondViewController: UIViewController, ModelObjectDelegate {
    
    var modelObject: ModelObject?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        modelObject = ModelObject()
        modelObject!.delegate = self
    }
        
    @IBAction func closeButtonPressed(sender: UIButton) {
        dismissViewControllerAnimated(true, completion: nil)
    }
```

Теперь давайте рассмотрим обработку памяти. Если мы закрываем SecondViewController (closeButtonPressed), объем используемой памяти не уменьшится. Но почему это происходит? Мы ожидаем, что закрываясь, SecondViewController будет высвобожден из памяти. Если SecondViewController загружен, он выглядит таким образом:

  ``![image text](https://i0.wp.com/blog.justdev.org/wp-content/uploads/2016/05/retainc1.jpg?w=600&ssl=1)``
  
  Теперь, если SecondViewController будет закрыт, он будет выглядеть так:
  
    ``![image text](https://i2.wp.com/blog.justdev.org/wp-content/uploads/2016/05/retainc1-1.jpg?w=600&ssl=1)``
    
RootViewController не имеет больше сильную (strong) ссылку на SecondViewController. Тем не менее, SecondViewController и ModelObject имеют сильные ссылки друг на друга, и из за этого они не освобождаются.

ХИТРОСТЬ

Хитрость, позволяющая обнаружить такого рода проблемы заключается в том, что если объект высвобождается, будет вызываться его метод deinit. Так что просто достаточно вставить какой то print в этот метод для отображения момента высвобождения в логе:

```
import UIKit
 
class SecondViewController: UIViewController, ModelObjectDelegate {
    
    var modelObject: ModelObject?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        modelObject = ModelObject()
        modelObject!.delegate = self
    }
    
    @IBAction func closeButtonPressed(sender: UIButton) {
        dismissViewControllerAnimated(true, completion: nil)
    }
    
    deinit {
        print("SecondViewController deinit")
    }
}
```

```
import Foundation
 
protocol ModelObjectDelegate: class {
    
}
 
class ModelObject {
    
    var delegate: ModelObjectDelegate?
    
    deinit {
        print("ModelObject deinit")
    }
    
}
```
Если мы закрываем SecondViewController, то видим, что в логе наше сообщение так и не появляется! Это означает что он не высвобожден, а значит что то не так.

РЕШЕНИЕ
Давайте изменим делегат на слабый (weak), что бы устранить одну мешающую нам сильную ссылку:

```
import Foundation
 
protocol ModelObjectDelegate: class {
    
}
 
class ModelObject {
    
    weak var delegate: ModelObjectDelegate?
    
    deinit {
        print("ModelObject deinit")
    }
    
}
```
Графически, наш объект выглядит теперь так:

``![image text](https://i2.wp.com/blog.justdev.org/wp-content/uploads/2016/05/retainc3.jpg?w=600&ssl=1)``
    
Поскольку между SecondViewController и ModelObject теперь есть только одна сильная ссылка, мы ожидаем, что проблем больше не будет.

И в самом деле — теперь все сообщения методов deinit появляются в логе:

SecondViewController deinit
ModelObject deinit
Это то поведение, которое мы ожидали!
Вместо weak вы так же можете использовать ключевое слово unowned. Но в чем разница? Если вы используете weak, то свойство необязательно, а значит имеет право быть nil. При использовании unowned свойство не должно быть опциональным. Так как unowned свойство не является опциональным, его значение должно быть установлено в методе инициализации:

```
import Foundation
 
protocol ModelObjectDelegate: class {
    
}
 
class ModelObject {
    
    unowned var delegate: ModelObjectDelegate
    
    init(delegate:ModelObjectDelegate) {
        self.delegate = delegate
    }
        
}
```

В зависимости от того, опционально свойство или нет, вы можете использовать как weak так и unowned.
Хотя это и не много, но вы должны вставить логирование в метод deinit объектов для того, что бы всегда видеть, что происходит с ними. Вы также можете использовать инструменты Xcode для выявления retain-циклов. Но разве это проще, чем вставить одну строку в метод deinit?

---
- [x]	**Как использовать self внутри блоков? Приведите пример retain cycle в блоке?**

Когда нужен weak refence для self внутри блока?
Если блок находится во владении класса (retained). Например объект хранит свойство - блок.

Объект который владеет блоком - в этом случае и происходит захват self - внутри блока и происходит retain cycle (цикла владения).

Where you get into trouble is something like:
```
//In the interface:
@property (strong) void(^myBlock)(id obj, NSUInteger idx, BOOL *stop);

//In the implementation:
[self setMyBlock:^(id obj, NSUInteger idx, BOOL *stop) {
  [self doSomethingWithObj:obj];     
}];
```
The block retains self, but self doesn't retain the block. If one or the other is released, no cycle is created and everything gets deallocated as it should.

```
objc
__weak MyObject *weakSelf = self;
[[SomeOtherObject alloc] initWithCompletion:^{
   MyObject *strongSelf = weakSelf;
  [strongSelf doSomething];
}];
__weak typeof(self) weakSelf = self;
```
В инструментах можно отследить - Record reference counts

retain cycle - strong reference cycle.

The correct solution is this:
```
__weak typeof(self) weakSelf = self;
dispatch_async(dispatch_get_main_queue(), ^{
    typeof(self) strongSelf = weakSelf;
    if (strongSelf) {
        [strongSelf doSomething];
    } else {
        [someOtherObject doSomethingElse];
    }
});
```

Self-объект хранит (владеет) блок. Блок удерживает self объект.

You don’t need to make two sets of weak references. What you want to avoid with blocks is a retain cycle—two objects keeping each other alive unnecessarily. If I have an object with this property:
```
@property (strong) void(^completionBlock)(void);
and I have this method:

- (void)doSomething
{
    self.completionBlock = ^{
        [self cleanUp];
    };

    [self doLongRunningTask];
}
```
the block will be kept alive when I store it in the completionBlock property. But since it references self inside the block, the block will keep self alive until it goes away—but this won’t happen since they’re both referencing each other.


---
- [ ]	**Зачем все свойства ссылающиеся на делегаты strong для ARC и retain для MRC?**

!!


---
- [x]	**Что такое autorelease pool?**

Autorelease pool это — механизм отложеного отказа от ответсвенности. Это означет что вы больше не хотите владеть объектом и он вам в общем-то не нужен, но не хотите чтобы он удалился прямо сейчас. Зачастую вам не нужно создавать свой пул, просто используется NSAutoreleasePool и то даже не напрямую. Чтобы поместить объект в autorelease pool нужно отправить ему сообщение autorelease (один объект может быть добавлен в пул несколько раз) и на следующем цикле сообщений autorelease pool отправит всем этим объектам сообщение release, так как сам получит dealloc после чего будет создан новый autorelease pool. Таким образом Cocoa ожидает, что как минимум один autorelease pool будет всегда, и автоматически создает его в main. Autorelease pool добавляются по принципу стека, самый последний добавленый пул будет в самом верху стека авторелиз пулов в текущем потоке. Создание пула осуществляется стандартным alloc и init, удаление drain. Если послать сообщение drain не самому верхнему пулу, то все пулы над ним тоже получат это сообщение и соответсвенно удалят все из себя. Оффициальная документация показывает нам вот такой пример использования своего пула:
```
NSArray *urls = <# An array of file URLs #>;
for (NSURL *url in urls) {
	NSAutoreleasePool *loopPool = [[NSAutoreleasePool alloc] init];
   	NSError *error = nil;
    NSString *fileContents = [[[NSString alloc] initWithContentsOfURL:url encoding:NSUTF8StringEncoding error:&error] autorelease];
    /* Process the string, creating and autoreleasing more objects. */
    [loopPool drain];
}
```
Мы тут видим, что был создан свой пул (он разместился вверху стека пулов и будет освобож-ден первым), в него добавляются NSString *fileContents и потом пул освобождается (а так как он самый верхний — освобождается только он).


---
- [x]	**Что такое runLoop (NSAutoreleasePool), когда он используется? (timers, nsurlconnection ...)?**

NSAutoreleasePool пулы обеспечивают механизм, посредством которого можно отправить объекту "отложенные" release сообщения.Каждый поток в Cocoa приложении сохраняет свой ​​собственный стек объектов NSAutoreleasePool

NSRunLoop Циклы выполнения - цикл обработки событий, который используются для планирования работы и координации получения входящих событий. Объект NSRunLoop также обрабатывает события NSTimer
[http://macbug.ru/cocoa/mthreadrunloops]


---
- [x]	**Как связаны NSRunLoop и NSAutoreleasePool на пальцах?**

NSRunLoop запускается внутри NSAutoreleasePool который управляет памятью обьектов.

---
- [x]	**Как можно заимплементировать autorelease pool на с++?**

Boehm garbage collector
[https://en.wikipedia.org/wiki/Boehm_garbage_collector]


---
- [x]	**Если я вызову performSelector:withObject:afterDelay: - объекту пошлется сообщение retain?**

Да. Это создает таймер который вызывает селектор в RunLoop'е текущего потока (thread).


---
- [x]	**Как происходит обработка memory warning(предупреждение о малом количестве памяти)? Зависит ли обработка от версии iOS, как мы должны их обрабатывать?**

[https://developer.apple.com/documentation/uikit/core_app/managing_your_app_s_life_cycle/responding_to_memory_warnings?language=objc]

iOS 2.0 and later.

Your app never calls this method directly. Instead, this method is called when the system determines that the amount of available memory is low. You can override this method to release any additional memory used by your view controller. If you do, your implementation of this method must call the super implementation at some point.
```
- (void)applicationDidReceiveMemoryWarning:(UIApplication *)application {
	/*
	Free up as much memory as possible by purging cached data objects that can be recreated (or reloaded from disk) later.
	*/
}

- (void)didReceiveMemoryWarning {
	// Releases the view if it doesn't have a superview.
    [super didReceiveMemoryWarning];
    // Release any cached data, images, etc that aren't in use.
}
```
Если на устройстве заканчивается память в центр уведомлений приходит сообщение UIApplicationDidReceiveMemoryWarningNotification, наш обьект может об этом узнать через уведомления и что-либо сделать, например почистить кеш или сохранить данные.


---
- [x]	**Напишите простую реализацию NSAutoreleasePool на Objective-C?**
```
int main(void) {
    NSAutoreleasePool *pool;
    pool = [[NSAutoreleasePool alloc] init];
    NSString *string;
    string = [[[NSString alloc] init] autorelease];
    /* use the string */
    [pool drain];
}
```
Every time -autorelease is sent to an object, it is added to the inner-most autorelease pool. When the pool is drained, it simply sends -release to all the objects in the pool.

Autorelease pools are simply a convenience that allows you to defer sending -release until "later". That "later" can happen in several places, but the most common in Cocoa GUI apps is at the end of the current run loop cycle.


NSAutoreleasePool: drain vs. release
Since the function of drain and release seem to be causing confusion, it may be worth clarifying here (although this is covered in the documentation...).

Strictly speaking, from the big picture perspective drain is not equivalent to release:

In a reference-counted environment, drain does perform the same operations as release, so the two are in that sense equivalent. To emphasise, this means you do not leak a pool if you use drain rather than release.

In a garbage-collected environment, release is a no-op. Thus it has no effect.  drain, on the other hand, contains a hint to the collector that it should "collect if needed". Thus in a garbage-collected environment, using drain helps the system balance collection sweeps.

---
- [x]	**Когда нужно использовать метод retainCount (никогда, почему?)**

You should never use -retainCount, because it never tells you anything useful. The implementation of the Foundation and AppKit/UIKit frameworks is opaque; you don't know what's being retained, why it's being retained, who's retaining it, when it was retained, and so on.

For example:

You'd think that [NSNumber numberWithInt:1] would have a retainCount of 1. It doesn't. It's 2.
You'd think that @"Foo" would have a retainCount of 1. It doesn't. It's 1152921504606846975.
You'd think that [NSString stringWithString:@"Foo"] would have a retainCount of 1. It doesn't. Again, it's 1152921504606846975.
Basically, since anything can retain an object (and therefore alter its retainCount), and since you don't have the source to most of the code that runs an application, an object's retainCount is meaningless.

If you're trying to track down why an object isn't getting deallocated, use the Leaks tool in Instruments. If you're trying to track down why an object was deallocated too soon, use the Zombies tool in Instruments.

But don't use -retainCount. It's a truly worthless method.

As an update,[NSNumber numberWithInt:1] now has a retainCount of 9223372036854775807. If your code was expecting it to be 2, your code has now broken.


---
- [x]	**Что случится если вы добавите только что созданный объект в Mutable Array, и пошлете ему сообщение release? Что случится если послать сообщение release массиву? Что случится если вы удалите объект из массива и попытаетесь его использовать?**

You're using the descriptor of "strong" which is an ARC term. This should be retain and if you just set the property to nil it will release it automatically. You should set it to nil in your viewDidUnload since your ViewWillDissappear only means your viewcontroller is leaving visibility and not that it is being destroyed.

No, you don't need to tell each object to be released. When you send a release method to an NSArray, it automatically sends a release method to each item inside first.

Мы не достучимся до него, так как весь массив и все обьекты в нем будут удалены.
Вопрос бредовый, но ладно.

---
- [x]	**С подвохом: как работает сборщик мусора в iOS?**

Сборщика мусора в айфонах нету.


---
- [x]	**Нужно ли ретейнить (посылать сообщение retain) делегаты?**

Нет! Этого никогда не нужно делать (чтобы избежать циклов в графе владения)


---
- [x]	**Для чего используется класс NSCoder?**

NSCoder — это абстрактный класс который преобразует поток данных. Используется для архивации и разархивации объектов. Протокол <NSCoding> позволяет реализовать архивирование или разархивирование данных. Например, у нас есть обьект мы его можем сохранить, а при следующей загрузке приложения подгрузить обратно. Часто программе требуется хранить состояние объектов в файле для дальнейшего их полного либо частичного восстановления, а также работы с ними. Такой процесс называют сериализацией. Многие современные языки и фреймворки предоставляют для этого вспомогательные средства. Рассмотрим, что нам предоставляет Cocoa Framework для Objective-C. Сериализованный объект – объект, преобразованный в поток байтов для сохранения в файле или передачи по сети. NS(M)Array, NS(M)Dictionary, NS(M)Data, NS(M)String, NSNumber, NSDate. Сохранить состояние объекта в Cocoa Framework можно двумя способами при помощи:

архивации (archivation)
сериализации (serialization)
Каждый из них имеет свои области применения. Так, при помощи сериализации нельзя сохранить объект пользовательского класса. Рассмотрим подробнее оба способа. Протокол <NSCoding> объявляет два метода, которые должен реализовать класс, так что экземпляры этого класса могут быть закодированы и декодированы. Эта возможность обеспечивает основу для архивирования (где объекты и другие структуры хранятся на диске) и распространения (где объекты копируются в разные адресные пространства).

encodeWithCoder: кодирует приемник с помощью данного архиватора. (обязательный)
```
- (void)encodeWithCoder:(NSCoder *)encoder
```
initWithCoder: возвращает объект инициализированный из данных в данном разархиваторе. (обязательный)
```
- (id)initWithCoder:(NSCoder *)decoder
```
Создание архивов

Самый простой способ создать архив - использовать метод archiveRootObject:toFile: архиватора. Этот метод класса создает временный экземпляр архиватора и записывает объект в файл.
```
MapView *myMapView;
result = [NSKeyedArchiver archiveRootObject:myMapView toFile:@"/tmp/MapArchive"];
```
Чтение архивов

Для чтения архивов, также как и для записи (см. выше), можно использовать 2 метода. Первый - простой и пригодный для большинства случаев - с использованием метода класса:
```
MapView *myMapView;
myMapView = [NSKeyedUnarchiver unarchiveObjectWithFile:@"/tmp/MapArchive"];
```
Второй метод предполагает создание экземпляра объекта NSKeyedUnarchiver.


---
- [x]	**Опишите правильный способ управления памятью выделяемой под Outlet'ы?**

we generally use weak for IBOutlets (UIViewController's Childs). This works because the child object only needs to exist as long as the parent object does.


---


#### Networking

- [x]	**Преимущества и недостатки синхронного и асинхронного соединения?**

NSURLConnection поддерживает синхронное и асинхронное выполнение запросов. Для выполнения синхронных запросов используется sendSynchronousRequest:returningResponse:error: Использование такого запроса не рекомендуется. Работа приложения остановится, пока данные не будут получены полностью, не произойдёт ошибка или истечет таймаут соединения. При использовании синхронного запроса есть и другие ограничения. Для асинхронных запросов используется - (id)initWithRequest:(NSURLRequest *)request delegate:(id <NSURLConnectionDelegate>)delegate либо `initWithRequest:delegate:startImmediately:``
	
	
---
- [x]	**Что означает http, tcp?**

Transmission Control Protocol (TCP) (протокол управления передачей) — один из основных протоколов передачи данных Интернета, предназначенный для управления передачей данных в сетях и подсетях TCP/IP. HTTP (англ. HyperText Transfer Protocol — «протокол передачи гипертекста») — протокол прикладного уровня передачи данных (изначально — в виде гипертекстовых документов). Основой HTTP является технология «клиент-сервер», то есть предполагается существование потребителей (клиентов), которые инициируют соединение и посылают запрос, и поставщиков (серверов), которые ожидают соединения для получения запроса, производят необходимые действия и возвращают обратно сообщение с результатом. Этот протокол описывает взаимодействие между двумя компьютерами (клиентом и сервером), построенное на базе сообщений, называемых запрос (Request) и ответ (Response). Каждое сообщение состоит из трех частей: стартовая строка, заголовки и тело. При этом обязательной является только стартовая строка.


---
- [x]	**REST, HTTP, JSON. Что это?**

REST (Representational state transfer) – это стиль архитектуры программного обеспечения для распределенных систем, таких как World Wide Web, который, как правило, используется для построения веб-служб. Термин REST был введен в 2000 году Роем Филдингом, одним из авторов HTTP-протокола. Системы, поддерживающие REST, называются RESTful-системами.

В общем случае REST является очень простым интерфейсом управления информацией без использования каких-то дополнительных внутренних прослоек. Каждая единица информации однозначно определяется глобальным идентификатором, таким как URL. Каждая URL в свою очередь имеет строго заданный формат. 

HTTP — широко распространённый протокол передачи данных, изначально предназначенный для передачи гипертекстовых документов (то есть документов, которые могут содержать ссылки, позволяющие организовать переход к другим документам).

Аббревиатура HTTP расшифровывается как HyperText Transfer Protocol, «протокол передачи гипертекста». В соответствии со спецификацией OSI, HTTP является протоколом прикладного (верхнего, 7-го) уровня. Актуальная на данный момент версия протокола, HTTP 1.1, описана в спецификации RFC 2616.

Протокол HTTP предполагает использование клиент-серверной структуры передачи данных. Клиентское приложение формирует запрос и отправляет его на сервер, после чего серверное программное обеспечение обрабатывает данный запрос, формирует ответ и передаёт его обратно клиенту. После этого клиентское приложение может продолжить отправлять другие запросы, которые будут обработаны аналогичным образом.

Задача, которая традиционно решается с помощью протокола HTTP — обмен данными между пользовательским приложением, осуществляющим доступ к веб-ресурсам (обычно это веб-браузер) и веб-сервером. На данный момент именно благодаря протоколу HTTP обеспечивается работа Всемирной паутины.

JSON это сокращение от JavaScript Object Notation — формата передачи данных. Как можно понять из названия, JSON прозошел из JavaScript языка програмирования, но он доступен для использования на многих языках, включая Python, Ruby, PHP и Java, в англоязычным странах его в основном произносят как Jason, то есть как имя ДжЭйсон, в русскоязычных странах ударение преимущественно ставится на “о” — ДжисОн.

Сам по себе JSON использует расширение .json. Когда же он определяется в других файловых форматах, как .html, он появляется в кавычках как JSON строка или может быть объектом, назначенным переменной. Этот формат легко передавать между сервером и клиентской частью или браузером.

Легкочитаемый и компактный, JSON предлагает хорошую альтернативу XML и требует гораздо меньше форматирования. Это информативное руководство поможет вам быстрее разобраться с данными, которые вы можете использовать с JSON и основной структурой с синтаксисом этого же формата.

---
- [x]	**Какие различия между HEAD, GET, POST, PUT?**

GET Используется для запроса содержимого указанного ресурса. С помощью метода GET можно также начать какой-либо процесс. В этом случае в тело ответного сообщения следует включить информацию о ходе выполнения процесса. Клиент может передавать параметры выполнения запроса в URI целевого ресурса после символа ?:

GET /path/resource?param1=value1&param2=value2 HTTP/1.1
HEAD Аналогичен методу GET, за исключением того, что в ответе сервера отсутствует тело. Запрос HEAD обычно применяется для извлечения метаданных, проверки наличия ресурса (валидация URL) и чтобы узнать, не изменился ли он с момента последнего обращения. Заголовки ответа могут кэшироваться. При несовпадении метаданных ресурса с соответствующей информацией в кэше копия ресурса помечается как устаревшая.

POST Применяется для передачи пользовательских данных заданному ресурсу. Например, в блогах посетители обычно могут вводить свои комментарии к записям в HTML-форму, после чего они передаются серверу методом POST и он помещает их на страницу. При этом передаваемые данные (в примере с блогами — текст комментария) включаются в тело запроса. Аналогично с помощью метода POST обычно загружаются файлы на сервер. В отличие от метода GET, метод POST не считается идемпотентным, то есть многократное повторение одних и тех же запросов POST может возвращать разные результаты (например, после каждой отправки комментария будет появляться одна копия этого комментария). Отправить POST-запрос не так тяжело как кажется. Достаточно подготовить «правильный» NSURLRequest.
```
NSString *params = @"param=value&number=1"; // задаем параметры POST запроса
NSURL *url = [NSURL URLWithString:@"http://server.com"]; // куда отправлять
NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
request.HTTPMethod = @"POST";
request.HTTPBody = [params dataUsingEncoding:NSUTF8StringEncoding];
// следует обратить внимание на кодировку
// теперь можно отправить запрос синхронно или асинхронно
[NSURLConnection sendSynchronousRequest:request returningResponse:nil error:nil];
```
PUT Применяется для загрузки содержимого запроса на указанный в запросе URI. Если по заданному URI не существовало ресурса, то сервер создаёт его и возвращает статус 201 (Created). Если же был изменён ресурс, то сервер возвращает 200 (Ok) или 204 (No Content). Сервер не должен игнорировать некорректные заголовки Content-*, передаваемые клиентом вместе с сообщением. Если какой-то из этих заголовков не может быть распознан или не допустим при текущих условиях, то необходимо вернуть код ошибки 501 (Not Implemented). Фундаментальное различие методов POST и PUT заключается в понимании предназначений URI ресурсов. Метод POST предполагает, что по указанному URI будет производиться обработка передаваемого клиентом содержимого. Используя PUT, клиент предполагает, что загружаемое содержимое соответствует находящемуся по данному URI ресурсу.

Единый указатель ресурсов (англ. URL — Uniform Resource Locator) — единообразный локатор (определитель местонахождения) ресурса. URL — это стандартизированный способ записи адреса ресурса в сети Интернет.

URI (англ. Uniform Resource Identifier) — унифицированный (единообразный) идентификатор ресурса. URI — это символьная строка, позволяющая идентифицировать какой-либо ресурс: документ, изображение, файл, службу, ящик электронной почты и т. д. Прежде всего, речь идёт, конечно, о ресурсах сети Интернет и Всемирной паутины. URL это частный случай URI. Понятие URI включает в себя, помимо URL, например, ссылки на адреса электронной почты и т.п. URL указывает на Веб-ресурс, вроде сайта, страницы или кон-кретного файла, расположенных на интернет-серверах.

---
- [x]	**Что такое REST (Restful)?**

REST (Representational state transfer) – это стиль архитектуры программного обеспечения для распределенных систем, таких как World Wide Web, который, как правило, используется для построения веб-служб. Термин REST был введен в 2000 году Роем Филдингом, одним из авторов HTTP-протокола. Системы, поддерживающие REST, называются RESTful-системами. В общем случае REST является очень простым интерфейсом управления информацией без использования каких-то дополнительных внутренних прослоек. Каждая единица информации однозначно определяется глобальным идентификатором, таким как URL. Каждый URL в свою очередь имеет строго заданный формат. Вот как это будет выглядеть на примере:

GET /book/ — получить список всех книг
GET /book/3/ — получить книгу номер 3
PUT /book/ — добавить книгу (данные в теле запроса)
POST /book/3 — изменить книгу (данные в теле запроса)
DELETE /book/3 — удалить книгу
REST (сокр. от англ. Representational State Transfer — «передача репрезентативного состояния») — метод взаимодействия компонентов распределённого приложения в сети Интернет, при котором вызов удаленной процедуры представляет собой обычный HTTP-запрос (обычно GET или POST; такой запрос называют REST-запрос), а необходимые данные передаются в качестве параметров запроса. Этот способ является альтернативой более сложным методам, таким как SOAP, CORBA и RPC. В широком смысле REST означает концепцию построения распределённого приложения, при которой компоненты взаимодействуют наподобие взаимодействия клиентов и серверов во Всемирной паутине. Хотя данная концепция лежит в самой основе Всемирной паутины, но термин REST был введён Роем Филдингом, одним из создателей протокола HTTP, лишь в 2000 году. В своей диссертации в Калифорнийском университете в Ирвайне он подвёл теоретическую основу под метод взаимодействия клиентов и серверов во Всемирной паутине, абстрагировав его и назвав «передачей репрезентативного состояния». Филдинг описал концепцию построения распределённого приложения, при которой каждый запрос (REST-запрос) клиента к серверу содержит в себе исчерпывающую информацию о желаемом ответе сервера (желаемом репрезентативном состоянии), и сервер не обязан сохранять информацию о состоянии клиента («клиентской сессии»).

Архитектура REST

Как необходимые условия для построения распределенных REST-приложений Филдинг перечислил следующие:

Клиент-серверная архитектура.
Сервер не обязан сохранять информацию о состоянии клиента.
В каждом запросе клиента должно явно содержаться указание о возможности кэширования ответа и получения ответа из существующего кэша
Клиент может взаимодействовать не напрямую с сервером, а с произвольным количеством промежуточных узлов. При этом клиент может не знать о существовании промежуточных узлов, за исключением случаев передачи конфиденциальной информации.
Унифицированный программный интерфейс сервера. Филдинг приводил URI в качестве примера формата запросов к серверу, а в качестве примера ответа сервера форматы HTML, XML и JSON, различаемые с использованием идентификаторов MIME.
Филдинг указывал, что приложения, не соответствующие приведённым условиям, не могут называться REST-приложениями. Если же все условия соблюдены, то, по его мнению, приложение получит следующие преимущества:

надёжность (за счет отсутствия необходимости сохранять информацию о состоянии клиента, которая может быть утеряна);
производительность (за счет использования кэша);
масштабируемость;
прозрачность системы взаимодействия, особенно необходимую для приложений обслуживания сети;
простоту интерфейсов;
портативность компонентов;
легкость внесения изменений;
способность эволюционировать, приспосабливаясь к новым требованиям (на примере Всемирной паутины).


---
- [x]	**Какую JSONSerialization имеет ReadingOptions?**

The iOS SDK allows us to connect to the Internet and retrieve and send data using the NSURLConnection class. JSON serialization and deserialization will all be done using the NSJSONSerialization class. XML parsing will be done using NSXMLParser, and the Twitter connectivity will be done using the Twitter framework. The iOS 7 SDK brings along new classes that we can take advantage of in this chapter. One of these classes is the NSURLSession, which manages the connectivity to web services in a more thorough way than the NSURLConnection class does.
```
- (void)getImageFromURL:(NSString *)url {
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        NSURL *myUrl = [NSURL URLWithString:url];
        NSData *data = [NSData dataWithContentsOfURL:myUrl];
        dispatch_sync(dispatch_get_main_queue(), ^{
            self.cityPicture = [UIImage imageWithData:data];
        });
    });
}
```


---
- [x]	**Объясните различия в SOAP и REST?**

[https://habr.com/ru/post/131343/]

Вкратце. 
SOAP и REST – не конкуренты. Они представляют разные весовые категории и вряд ли найдется задача, для которой будет сложно сказать, какой подход рациональнее использовать – SOAP или REST. Поэтому «религиозные» убеждения в вопросах выбора архитектуры для веб-сервиса вряд ли будут полезны. Для тех, кто не знает, с чего начать анализ задачи, могу порекомендовать эту презентацию. У них чаще побеждает REST.

---
- [x]	**Как загрузить что-то из интернета? В чем разница между синхронными и асинхронными запросами? Небольшое задание. Опишите как загрузить изображение из интернета и отобразить его в ImageView — все это должно происходить после нажатия кнопки.**

В моем списке есть еще один пунктик — как в Objective-C загрузить картинку через http в UIImageView.

Код очень простой.

На view лежит UIImageView — c именем imageView. Там же кнопка с таком вот кодом:
```
— (IBAction)actBtnGet:(id)sender {
id path = @»http://www.denaie.ru/wp-content/uploads/2013/06/06_produmai_marshrut-300×262.jpg»;
NSURL *url = [NSURL URLWithString:path];
NSData *data = [NSData dataWithContentsOfURL:url];
UIImage *img = [[UIImage alloc] initWithData:data];
self.imageView.image=img;
}
```

Картинка грузится и отображается. Задача выполнена.

Там же в этом отделе был подвопрос что такое синхронная и асинхронная загрузка. Так вот, точной терминологии нет равно как и не понятно к чему применим сам вопрос. Самое просто это то что в первом случае все идет по порядку и второй файл заливается/скачивается после окончания первого, а во втором это может происходить одновременно. И как правило для этого используется либо многопоточность либо какая-то программная прослойка работающая в фоне и выполняющая роль менеджера загрузок и ей делегируется данная обязанность. Например
Grand Central Dispatch (GCD)

Вот такой вот код найденный в интернете иллюстрирует использование GCD:
```
+ (void)processImageDataWithURLString:(NSString *)urlString andBlock:(void (^)(NSData *imageData))processImage { NSURL *url = [NSURL URLWithString:urlString];

dispatch_queue_t callerQueue = dispatch_get_current_queue();
dispatch_queue_t downloadQueue = dispatch_queue_create(«com.myapp.processsmagequeue», NULL);
dispatch_async(downloadQueue, ^{
NSData * imageData = [NSData dataWithContentsOfURL:url];

dispatch_async(callerQueue, ^{
processImage(imageData);
});
});
dispatch_release(downloadQueue);
}
```



#### Multithreading
---
- [x]	**Зачем мы используем synchronized?**

@synchronized гарантирует, что только один поток может выполнять этот код в блоке в любой момент времени.

@synchronized Заключает блок кода в мьютекс. Обеспечивает гарантию того, что блок кода и объект блокировки будут доступны только из одного потока в момент времени.

Замки являются одним из наиболее часто используемых инструментов синхронизации. Вы можете использовать замки для защиты критической секции вашего кода, который является сегментом кода, к которому разрешен доступ только одному потоку одновременно. Взаимоисключающая (или мьютекс) блокировка действует как защитный барьер вокруг ресурса. Мьютекс является одним из видов семафора, который предоставляет доступ одновременно только одному потоку. Если мьютекс используется и другой поток пытается получить его, что поток блокируется до тех пор, пока мьютекс не освободится от своего первоначального владельца. Если несколько потоков соперничают за одни и те же мьютексы, только одному будет разрешен к нему доступ.


---
- [x]	**В чем разница между синхронным и асинхронным таском (задачей)?**

Ниже описаны термины в привязке к GCD, но с небольшими изменениями они верны и для программирования в целом.

Синхронная операция начинает выполнятся сразу при вызове, блокирует поток. Выполняется в текущем потоке. Асинхронная операция ставит задачу в очередь выполнения, продолжает выполнение кода, из которого вызвана задача. Если очередь однопоточная, то задача будет выполнятся после выполнения всех задач, которые уже поставлены в очередь, если много поточная - возможно ее выполнение в другом потоке.

Нужно запомнить, что синхронность-асинхронность и однопоточность-многопоточность две пары разных характеристить, и одни не зависят от других (и синхронность и асинхронность могут работать как в однопоточной среде, так и в многопоточной)


---
- [x]	**Что такое обработчик завершения (completion handler)? Сравнение с блоками.**

Blocks:

Blocks are a language-level feature added to C, Objective-C and C++, which allow you to create distinct segments of code that can be passed around to methods or functions as if they were values. Blocks are Objective-C objects, which means they can be added to collections like NSArray or NSDictionary.

They can be executed in a later time, and not when the code of the scope they have been implemented is being executed.
Their usage leads eventually to a much cleaner and tidier code writing, as they can be used instead of delegate methods, written just in one place and not spread to many files.
Syntax: ReturnType (^blockName)(Parameters) see example:
```
int anInteger = 42;

void (^testBlock)(void) = ^{

    NSLog(@"Integer is: %i", anInteger);   // anInteger outside variables

};

// calling blocks like
testBlock();
```
Block with argument:
```
double (^multiplyTwoValues)(double, double) =

                          ^(double firstValue, double secondValue) {

                              return firstValue * secondValue;

                          };
// calling with parameter
double result = multiplyTwoValues(2,4);

NSLog(@"The result is %f", result);
```
Completion handler:

Whereas completion handler is a way (technique) for implementing callback functionality using blocks.

A completion handler is nothing more than a simple block declaration passed as a parameter to a method that needs to make a callback at a later time.

Note: completion handler should always be the last parameter in a method. A method can have as many arguments as you want, but always have the completion handler as the last argument in the parameters list.

Example:
```
- (void)beginTaskWithName:(NSString *)name completion:(void(^)(void))callback;

// calling
[self beginTaskWithName:@"MyTask" completion:^{

    NSLog(@"Task completed ..");

}];
```
More example with UIKit classes methods.
```
[self presentViewController:viewController animated:YES completion:^{
        NSLog(@"xyz View Controller presented ..");

        // Other code related to view controller presentation...
    }];
[UIView animateWithDuration:0.5
                     animations:^{
                         // Animation-related code here...
                         [self.view setAlpha:0.5];
                     }
                     completion:^(BOOL finished) {
                         // Any completion handler related code here...

                         NSLog(@"Animation over..");
                     }];
```

---
- [x]	**Что такое параллелизм (concurrency)?**

Параллелизм это понятие нескольких действий происходящих одновременно. Обе системы и Mac OS X и IOS принимают асинхронный подход к выполнению параллельных задач. Вместо того чтобы создавать потоки напрямую, приложения должны только определить конкретные задачи, а затем позволить системе выполнять их. Позволяя системе управлять потоками, приложения получают уровень масштабируемости, который не представляется возможным при работе с потоками напрямую.

[http://macbug.ru/cocoa/mthread]

---
- [x]	**Блокировки читателей-писателей (readers-writers lock)?**

The readers–writers problem occurs when multiple threads share a resource (variable or a data structure in memory), and some threads may write, some may read. When multiple threads read from the shared resource simultaneously nothing wrong can happen. But when one thread write — others must wait, otherwise ending up with corrupted data.

I.e. the readers–writers problem allows concurrent read but write is serial.

---
- [x]	**С подвохом: вопрос о несуществующем параметре atomic, что он означает? Приведите пример кейса с использованием atomic?**

Atomic is indivisible, uninterruptible. Atomic operations are executed at once, without interference. Atomic operation can either succeed or fail without partial or side effects.

We can highlight primitive and high-level atomic operations. High-level is a very broad term and can mean something as complex as database transaction.

Primitive atomic operations are provided by OS or CPU instructions. Some examples are loading, storing, and incrementing a value in memory address. For macOS and iOS common are OSAtomic — group of functions for atomic reading and updating values. This are now deprecated in favour of C11 standard stdatomic.h.

---
- [x]	**Как многопоточность работает с UIKit?**

One of the most common mistakes even experienced iOS/Mac developers make is accessing parts of UIKit/AppKit on background threads. It’s very easy to make the mistake of setting properties like image from a background thread, because their content is being requested from the network in the background anyway. Apple’s code is performance-optimized and will not warn you if you change properties from different threads. For the most part, UIKit classes should be used only from an application’s main thread. This is particularly true for classes derived from UIResponder or that involve manipulating your application’s user interface in any way.


---
- [x]	**Как запустить селектор в (фоновом) потоке?**

[self performSelectorInBackground:selector withObject:obj];

---
- [x]	**Как запустить поток (thread)? Что первым нужно сделать при запуске потока?**

[NSThread detachNewThreadSelector:@selector(myThreadMainMethod:) toTarget:self withObject:nil];

NSAutoreleasePool пулы обеспечивают механизм, посредством которого можно отправить объекту "отложенные" release сообщения.Каждый поток в Cocoa приложении сохраняет свой собственный стек объектов NSAutoreleasePool


---
- [x]	**Что такое deadlock?**

Deadlock — ситуация в многозадачной среде, при которой несколько процессов находятся в состоянии бесконечного ожидания ресурсов, захваченных самими этими процессами.
```
dispatch_queue_t queue = dispatch_queue_create("my.label", DISPATCH_QUEUE_SERIAL);
dispatch_async(queue, ^{
    dispatch_sync(queue, ^{
        //  outer block is waiting for this inner block to complete,
        //  inner block won't start before outer block finishes
        //  => deadlock
    });

    // this will never be reached
})
```

---
- [x]	**Что такое livelock?**

Livelock частая проблема в асинхронных системах. Потоки почти не блокируются на критических ресурсах. Вместо этого они выполняют свою небольшую неблокируемую задачу и отправляют её в очередь на обработку другими потоками. Может возникнуть ситуация, когда потоки друг другу начинают перекидывать какое-то событие и его обработка зацикливается. Явного бесконечного цикла, как бы, не происходит, но нагрузка на асинхронную систему резко возрастает. В результате чего эти потоки больше ничем не успевают занимаются.


---
- [x]	**Что такое семафор (semafor)?**

Семафор позволяет выполнять какой-либо участок кода одновременно только конкретному количеству потоков. В основе семафора лежит счетчик, который и определяет, можно ли выполнять участок кода текущему потоку или нет. Если счетчик больше нуля — поток выполняет код, в противном случае — нет. В GCD выглядит так: semaphore_create – создание семафора (аналог sem_init)
semaphore_destroy – удаление, соответственно (аналог sem_destroy)
semaphore_wait – блокирующее ожидание на семафоре (аналог sem_wait)
semaphore_signal – освобождение семафора (аналог sem_post)


---
- [x]	**Что такое мьютекс (mutex)?**

Мьютекс является одним из видов семафора, который предоставляет доступ одновременно только одному потоку. Если мьютекс используется и другой поток пытается получить его, что поток блокируется до тех пор, пока мьютекс не освободится от своего первоначального владельца. Если несколько потоков соперничают за одни и те же мьютексы, только одному будет разрешен к нему доступ.


---
- [x]	**Какие технологии в iOS возможно использовать для работы с потоками. Преимущества и недостатки.**

Существует три способа достижения параллелизма в iOS:

Потоки (threads)
GCD
NSOperationQueue
Недостатком потоков является то, что они немасштабируемы для разработчика. Вы должны решить, сколько потоков нужно создать и изменять их число динамически в соответствии с условиями. Кроме того, приложение принимает на себя большую часть затрат, связанных с созданием и встраиванием потоков, которые оно использует.

Поэтому в macOS и iOS предпочтительно использовать асинхронный подход к решению проблемы параллелизма, а не полагаться на потоки.

Одной из технологий асинхронного запуска задач является Grand Central Dispatch (GCD), которая отводит управление потоками до уровня системы. Все, что разработчик должен сделать, это определить выполняемые задачи и добавить их в соответствующую очередь отправки. GCD заботится о создании необходимых потоков и время для работы в этих потоках.

Все dispatch queues представляют собой структуры данных FIFO, поэтому задачи всегда запускаются в том же порядке, в котором они добавлены.

В отличие от dispatch queue очереди операций (NSOperation Queue) не ограничиваются выполнением задач в порядке FIFO и поддерживают создание сложных графиков выполнения заказов для ваших задач.


---
- [x]	**А чем реально POSIX-потоки лучше чем GCD или NSOperationQueue вместе с NSOperation? Приходилось ри реально использовать POSIX и как в этом были прюсы? Реально, просто интересно.**

Use POSIX calls if cross-platform portability is required. If you are writing networking code that runs exclusively in OS X and iOS, you should generally avoid POSIX networking calls, because they are harder to work with than higher-level APIs. However, if you are writing networking code that must be shared with other platforms, you can use the POSIX networking APIs so that you can use the same code everywhere.

---
- [x]	**Что такое GCD? Как GCD связан с многопоточностью? Типы queue и queue == thread?**

Одной из технологий для запуска задач асинхронно это Grand Central Dispatch (GCD). Эта технология имеет код управления потоками. Вы обычно пишите свои приложения и продвигаете код до системного уровня. Все, что вам нужно сделать, это определить задачи, которые вы хотите выполнить, и добавить их в соответствующую очередь отправки. GCD заботится о создании необходимых потоков и планирования Ваших задач для работы на этих потоках. Поскольку управление потоками является частью системы, GCD обеспечивает целостный подход к управлению и исполнению задач, обеспечивая лучшую эффективность, чем традиционные потоки.

GCD обеспечивает очереди отправки (dispatch queues) для обработки поставленных задач. Эти очереди управляют задачами, которые вы предоставляете для GCD и выполняют эти задачи в порядке их поступления. Это гарантирует, что первая задача, добавленная в очередь является первой задачей в очереди, вторая задача будет добавлена второй и так далее.

Все очереди отправки сами по себе являются потокобезопасными, и вы можете получить к ним доступ из нескольких потоков одновременно. Преимущества GCD очевидны, когда вы четко понимаете, как распределить очередность исполнения для обеспечения потокобезопасности частям вашего собственного кода. Ключом к этому является выбор правильного вида очереди отправки и правильной функции отправки для добавления вашей работы в очередь.

Последовательные очереди (Serial Queues)
Задачи в последовательных очередях выполняются по одной, каждая последующая задача начинается только после того, как закончится исполнение предыдущей. Кроме того, вы не будете знать, сколько времени пройдет между завершением одной задачи и началом следующей, как показано на рисунке ниже:

 
Время выполнения этих задач находится под контролем GCD. Единственное, что вы гарантированно будете знать, это то, что GCD выполняет только одну задачу единовременно, и что он выполняет задачи в порядке их добавления в очередь.

Поскольку невозможно выполнение двух задач в последовательной очереди одновременно, то и нет никакого риска, что они могут получить доступ к одной и той же критической (critical) секции одновременно, что защищает критическую секцию от "условий гонки" только по отношению к этим задачам. Так что, если единственный способ получить доступ к этой критической секции является задача, переданная в эту очередь отправки, то вы можете быть уверены, что критическая секция остается безопасной.

Согласованные Очереди (Concurrent Queues)
Задачи в согласованных очередях гарантированно будут выполняться в порядке, в котором они были добавлены ... и это все, что вы гарантированно получите! Элементы могут завершаться в любом порядке и у вы не будете знать ни о времени, которое потребуется до начала выполнения следующей задачи, ни количество задач, которые выполняются в текущий момент времени. Опять же, это полностью зависит от GCD.

Обратите внимание, что Задачи 1, 2, 3 быстро завершены, одна за другой, и потребовалось какое-то время для начала выполнения задачи 1 после Задачи 0. Кроме того, Задача 3 начала выполняться после начала выполнения Задачи 2, но закончилось выполнение первым у Задачи 3.

Решение о том, когда начать выполнение задачи полностью лежит на GCD. Если время выполнения одной задачи совпадает с другой, то GCD определяет, должна ли она запуститься на другом ядре, если таковое имеется, или вместо этого устроить переключение контекста на другую задачу.

Для того, чтобы сделать процесс интереснее, GCD предоставляет вам, по крайней мере пять конкретных очередей, чтобы выбрать в пределах каждого типа очереди.

Типы очередей
Во-первых, система представляет вам специальную последовательную очередь - основную очередь (main queue). Как и в любой последовательной очереди, задачи в этой очереди выполняются по одной. Тем не менее, это гарантирует, что все задачи будут выполняться на главном потоке, который является единственным потоком, которому разрешено обновить ваш пользовательский интерфейс. Только эта очередь используется для отправки сообщений на объекты UIView или для размещения уведомлений.

Система также предоставляет вам несколько согласованных очередей. Эти очереди связаны с их собственным классом QoS (Quality of Service). Классы QoS предназначены, чтобы выразить намерения представленной задачи, так чтобы GCD смог определить, как лучше расставить приоритеты:

QOS_CLASS_USER_INTERACTIVE: Интерактивный пользовательский класс (user interactive) представляет задачи, которые необходимо сделать немедленно. Используйте его для обновления пользовательского интерфейса, обработки событий или небольших работ, которые должны быть выполнены с небольшими задержками. Общий объем работ, сделанный в этом классе во время выполнения вашего приложения, должен быть небольшим.
QOS_CLASS_USER_INITIATED: Инициированный пользовательский класс представляет задачи, которые инициируются из пользовательского интерфейса и могут быть выполнены асинхронно. Его нужно использовать, когда пользователь ждет немедленных результатов, и для задач, требующих продолжения взаимодействия с пользователем.
QOS_CLASS_UTILITY: Класс Утилит (utility) представляет длительные  по исполнению задачи, как правило, с видимым для пользователя индикатором загрузки. Используйте его для вычислений, I/O, при работе с сетями, непрерывных каналов передачи данных и подобных друг другу задач. Этот класс является энергоэффективным, к тому же имеет низкое энергопотребление.
QOS_CLASS_BACKGROUND: Класс background представляет задачи, о которых пользователь может не знать напрямую. Используйте его для предварительной выборки, технического обслуживания и других задач, которые не требуют взаимодействия с пользователем и не требовательны ко времени исполнения.
Помните, что Apple API-интерфейсы также использует глобальные очереди отправки, так что задачи, которые вы добавляете, не будут единственными в этих очередях.

Наконец, вы можете создать свою собственную последовательную или согласованную очередь. Это означает, что у вас есть по крайней мере пять очередей в вашем распоряжении: главная очередь, четыре глобальные очереди отправки, плюс любые пользовательские очереди, которые вы добавляете сами!

Вот такая картина вырисовывается об "очередях отправки"!

"Искусство" в GCD сводится к выбору правильной функции очереди отправки для добавления работы в очередь. Лучший способ научиться - это поэксперементировать в приведенных ниже примерах, где мы представили некоторые общие рекомендации.

---
- [x]	**Чем отличается dispatch_async от dispatch_sync?**

Когда это возможно, асинхронное выполнение с использованием dispatch_async и dispatch_async_f функций предпочтительнее, чем синхронный вариант. При добавлении объекта блока или функции в очередь, нет никакого способа узнать, когда этот код будет выполняться. В результате, добавляя блоки или функции асинхронно позволяет запланировать выполнение кода и продолжать делать другую работу из вызывающего потока. Это особенно важно, если вы планировали выполнить задачу из основного потока приложения, возможно, в ответ на некоторые пользовательские события. Хотя вы должны добавлять задачи асинхронно по мере возможности, все же могут быть случаи, когда вам нужно добавить задачу синхронно, чтобы предотвратить гонку условий или другие ошибки синхронизации. В этих случаях можно использовать функции dispatch_sync и dispatch_sync_f для добавления задачи в очередь. Эти функции блокируют текущий поток исполнения до завершения выполнения указанной задачи.

Важно: Вы никогда не должны вызывать функции dispatch_sync или dispatch_sync_f из задачи, которая выполняется в той же очереди, в которой вы планируете переход к функции. Это особенно важно для последовательных очередей, которые гарантированно приведут к deadlock, но также следует избегать одновременных очередей.

Следующий пример показывает, как использовать блочные варианты для отправки задачи асинхронно и синхронно:
```
dispatch_queue_t myCustomQueue;
myCustomQueue = dispatch_queue_create("com.example.MyCustomQueue", NULL);

dispatch_async(myCustomQueue, ^{
	printf("Сделайте некую работу здесь.\n");
});
printf("Первый блок может работать или может не работать.\n");

dispatch_sync(myCustomQueue, ^{
	printf("Сделайте еще некую работу здесь.\n");
});
printf("Оба блока были завершены.\n");
```

---
- [x]	**Для чего при разработке под iOS использовать POSIX-потоки? pthread_create(&thread, NULL, startTimer, (void *)t);**

NSThread - это надстройка для pthreads
[https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Multithreading/CreatingThreads/CreatingThreads.html]


---
- [x]	**Асинхронность vs многопоточность. Чем отличаются?**

Асинхронность говорит о порядке исполнения кода. Если вызываемая функция не возвращает значение сразу, а отдаёт управление вызывающему коду с обещанием выдать значение позже, то эта функция асинхронная. При этом нет никаких предположений о том, как это значение будет считаться: параллельно или нет. Многопоточность говорит о том, что в физически происходит несколько потоков одновременно.

Многопоточность - каждый поток имеет свой стек и планируется на исполнение отдельно kernel’ом. Поток может общаться с другими потоками. Все потоки находятся в общем адресном пространстве приложения и делят одну и ту же виртуальную память и имеют те же права доступа что и процесс приложения. Асинхронность Асинхронные событиямя выполняются независимо от основного потока в неблокирующем режиме, что позволяет основному потоку программы продолжить обработку. Т/е/ результат работы функции приходит не сразу после вызова, а когда-нибудь потом.

в многопоточной реализации бывают случаи когда потоки простаивают в ожидании входных данных тем самым расходуя общие ресурсы асинхронность позволяет всегда занять процессор какими то действиями ставя все события в очередь

---
- [x]	**NSOperation vs GCD?**

While NSOperation and NSOperationQueue have been available since iOS 2, Grand Central Dispatch, GCD for short, was introduced in iOS 4 and OS X 10.6 Snow Leopard. Both technologies are designed to encapsulate units of work and dispatch them for execution. Because these APIs serve the same goal, developers are often confused when to use which.

Before I answer the questions when to use which API, I would like to discuss the key difference between NSOperation and Grand Central Dispatch.

What Sets Them Apart
Comparing NSOperation and Grand Central Dispatch is comparing apples and oranges. Why is that? With the introduction of Grand Central Dispatch, Apple refactored NSOperation to work on top of Grand Central Dispatch. The NSOperation API is a higher level abstraction of Grand Central Dispatch. If you are using NSOperation, then you are implicitly using Grand Central Dispatch.

Grand Central Dispatch is a low-level C API that interacts directly with Unix level of the system. NSOperation is an Objective-C API and that brings some overhead with it. Instances of NSOperation need to be allocated before they can be used and deallocated when they are no longer needed. Even though this is a highly optimized process, it is inherently slower than Grand Central Dispatch, which operates at a lower level.

Benefits of NSOperation
Since NSOperation is built on top of Grand Central Dispatch, you may be wondering what NSOperation offers that Grand Central Dispatch doesn't. There are several compelling benefits that make NSOperation an interesting choice for a number of use cases.

Dependencies
The NSOperation API provides support for dependencies. This is a powerful concept that enables developers to execute tasks in a specific order. An operation is ready when every dependency has finished executing.

Observable
The NSOperation and NSOperationQueue classes have a number of properties that can be observed, using KVO (Key Value Observing). This is another important benefit if you want to monitor the state of an operation or operation queue.

Pause, Cancel, Resume
Operations can be paused, resumed, and cancelled. Once you dispatch a task using Grand Central Dispatch, you no longer have control or insight into the execution of that task. The NSOperation API is more flexible in that respect, giving the developer control over the operation's life cycle.

Control
The NSOperationQueue also adds a number of benefits to the mix. For example, you can specify the maximum number of queued operations that can run simultaneously. This makes it easy to control how many operations run at the same time or to create a serial operation queue.

When to Use Which
In general, Apple advises developers to use the highest level of abstraction that is available. If we apply this advice, then the NSOperation API should be your first choice.

But why does Apple advise developers to use the highest level of abstraction? With every release, Apple tweaks and optimizes the frameworks and libraries that power the operating system. This usually involves changes affecting low(er)-level APIs. Even though APIs built on top of these low-level APIs change less frequently, they still benefit from the improvements Apple makes to the APIs they are built on.

Great. But when do you use which technology? Should you avoid Grand Central Dispatch only because it is a low-level API? You can use Grand Central Dispatch whenever and wherever you like. Many developers swear by Grand Central Dispatch, but most developers use a combination NSOperation and Grand Central Dispatch.

When to Use NSOperation
The NSOperation API is great for encapsulating well-defined blocks of functionality. You could, for example, use an NSOperation subclass to encapsulate the login sequence of an application.

Dependency management is the icing on the cake. An operation can have dependencies to other operations and that is a powerful feature Grand Central Dispatch lacks. If you need to perform several tasks in a specific order, then operations are a good solution.

You can go overboard with operations if you are creating dozens of operations in a short timeframe. This can lead to performance problems due to the overhead inherent to the NSOperation API.

When to Use Grand Central Dispatch
Grand Central Dispatch is ideal if you just need to dispatch a block of code to a serial or concurrent queue. If you don't want to go through the hassle of creating an NSOperation subclass for a trivial task, then Grand Central Dispatch is a great alternative. Another benefit of Grand Central Dispatch is that you can keep related code together. Take a look at the following example.

let dataTask = session.dataTaskWithRequest(request, completionHandler: { (data, response, error) -> Void in
```
    // Process Response
    ...

    dispatch_async(dispatch_get_main_queue(), { () -> Void in
        // Update User Interface
        ...
    })
})
```
In the completion handler of the data task, we process the response and update the user interface by dispatching a closure (or block) to the main queue. This is necessary because we don't know which thread the completion handler is executed on and it most likely is a background thread.

This example illustrates how related code is grouped together. It also highlights the elegance of the Grand Central Dispatch syntax. Asynchronously dispatching a task to the main queue requires only a few lines of code.

NSBlockOperation
The NSOperation class should never be used as is. It is meant to be subclassed. Foundation also provides a concrete subclass that is ready to be used, NSBlockOperation. This class combines the best of both worlds, you can use closures and still benefit from the NSOperation API.
```
let operation = NSBlockOperation(block: { () -> Void in
    // Do Something
    ...
})

operationQueue.addOperation(operation)
```
Some consider the NSBlockOperation redundant because you might as well use Grand Central Dispatch instead. I don't agree with this view. As the above example illustrates, the NSBlockOperation combines the elegance of closures and the benefits of operations. There is nothing wrong with NSBlockOperation in my opinion.

Conclusion
In this article, we explored the pros and cons of Grand Central Dispatch and the NSOperation API. I hope it is clear that you don't need to choose one or the other. A combination of both technologies is the right choice for most projects. Try it out and refactor if necessary.

**Кратко**

GCD is a low-level C-based API that enables very simple use of a task-based concurrency model. NSOperation and NSOperationQueue are Objective-C classes that do a similar thing. NSOperation was introduced first, but as of 10.6 and iOS 4, NSOperationQueue and friends are internally implemented using GCD.

In general, you should use the highest level of abstraction that suits your needs. This means that you should usually use NSOperationQueue instead of GCD, unless you need to do something that NSOperationQueue doesn't support.

Note that NSOperationQueue isn't a "dumbed-down" version of GCD; in fact, there are many things that you can do very simply with NSOperationQueue that take a lot of work with pure GCD. (Examples: bandwidth-constrained queues that only run N operations at a time; establishing dependencies between operations. Both very simple with NSOperation, very difficult with GCD.) Apple's done the hard work of leveraging GCD to create a very nice object-friendly API with NSOperation. Take advantage of their work unless you have a reason not to.

Caveat: On the other hand, if you really just need to send off a block, and don't need any of the additional functionality that NSOperationQueue provides, there's nothing wrong with using GCD. Just be sure it's the right tool for the job.


---
- [x]	**Что такое Dispatch Group?**

это объекты, позволяющие объединять задачи в группы для последующего объединения (joining). Задачи могут быть добавлены в очередь как члены группы, и затем объект группы может быть использован для ожидания завершения всех задач группы.


---
- [x]	**NSOperation — NSOperationQueue — NSBlockOperation?**

В отличие от dispatch queue очереди операций (NSOperation Queue) не ограничиваются выполнением задач в порядке FIFO и поддерживают создание сложных графиков выполнения заказов для ваших задач.

Operation queues are a Cocoa abstraction of the queue model exposed by GCD. While GCD offers more low-level control, operation queues implement several convenient features on top of it, which often makes it the best and safest choice for application developers. The NSOperationQueue class has two different types of queues: the main queue and custom queues. The main queue runs on the main thread, and custom queues are processed in the background. In any case, the tasks which are processed by these queues are represented as subclasses of NSOperation. Whereas dispatch queues always execute tasks in first-in, first-out order, operation queues take other factors into account when determining the execution order of tasks. Because the NSOperation class is essentially an abstract base class, you typically define custom subclasses to perform your tasks. However, the Foundation framework does include some concrete subclasses that you can create and use as is to perform tasks.

Плюсы

Можно для каждой очереди настраивать приоритет и количество одновременно выполняющихся операций. NSOperationQueue самостоятельно создает и поддерживает пул потоков, в которых исполняются NSOperation. Так же NSOperation предоставляет возможность отменять операции, приостанавливать всю очередь, запускать ее снова и много чего прочего.

[https://blog.justdev.org/lessons/prostoj-primer-nsblockoperation/]

---
- [ ]	**Как запустить поток? Что первым нужно сделать при запуске потока? (NSAutoreleasePool - пул автоосвобождения) Что такое runLoop, кодга он используется? (timers, nsurlconnection …)**

!!!


#### UIKit

---
- [x]	**Разница между свойствами bounds и frame объекта UIView? Понимание системы координат?**

frame – это прямоугольник описываемый положением location(x, y) и размерами size (width, height) вьюхи относительно ее superview в которой она содержится.
bounds – это прямоугольник описываемый положением location(x, y) и размерами size (width, height) вьюхи относительно ее собственной системы координат (0, 0).

[https://github.com/dashvlas/awesome-ios-interview/raw/master/Resources/Articles/Frame-Bounds.png]

---
- [x]	**Цикл жизни UIViewController?**

[https://habr.com/ru/post/129557/]

---
- [x]	**Что такое View (представление) и что такое window и цикл жизни UIView?**

[https://www.techotopia.com/index.php/Understanding_iPhone_Views,_Windows_and_the_View_Hierarchy]

Window экземпляр класса UIWindow занимается отображеним видимой View иерархии. Является корневым представлением. UIView - это основной обьект для взаимодействует с пользователем. Основные задачи: 1 Layout and subview management 2 Drawing and animation 3 Event handling


---
- [x]	**Что такое Responder Chain (цепочка обязанностей, паттерн chain of responsibility, на примере UI компонентов iOS ), becomeFirstResponder.**

Это цепочка по которой проходит событие от отправителя к получателю, от First Responder, по иерархии контроллеров, до root view controller, window object и последнего - app object.
```
-UIControl Actions(например, нажатие кнопки)
-User events: (touches, shakes, motion, etc...)
-System events: (low memory, rotation, etc...)
```

---
- [x]	**Что означают IBOutlet и IBAction, для чего они нужны, и что значат для препроцессора?**

[https://nshipster.com/ibaction-iboutlet-iboutletcollection/]

Используются для Interface Builder


---
- [x]	**Как работает UITableView? Жизненный цикл UITableView?**

Ячейки таблицы, которые больше не отображаются на экране, не выбрасываются из памяти. Их можно использовать повторно, указав идентификатор в процессе инициализации. Когда ячейка, отмеченная для повторного использования, пропадает с экрана, UITableView помещает ее в очередь для повторного использования в дальнейшем. Когда dataSource запрашивает у UITableView новую ячейку и указывает идентификатор, UITableView сначала проверяет очередь ячеек для повторного использования на предмет наличия необходимой. Если ячейка не была обнаружена, то создается новая, которая затем передается dataSource'у.

UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:CellIdentifier forIndexPath:indexPath];


---
- [x]	**Что можно сделать если клавиатура при появлении скрывает важную часть интерфейса?**

Сдвинуть интерфейс. 

Например, поменять значение константы констрейта и перерисовать лайаут, или просто обновить фреймы, если не используется лайаут. 

---
- [x]	**Что такое awakeFromNib, в чем разница между xib и nib файлами?**

NSNibAwaking Protocol Reference (informal protocol)

Prepares the receiver for service after it has been loaded from an Interface Builder archive, or nib file.

- (void)awakeFromNib;

An awakeFromNib message is sent to each object loaded from the archive, but only if it can respond to the message, and only after all the objects in the archive have been loaded and initialized. When an object receives an awakeFromNib message, it is guaranteed to have all its outlet instance variables set.

Note: During Interface Builder’s test mode, this method is also sent to objects instantiated from loaded palettes, which include executable code for the objects. It is not sent to objects created using the Classes display of the nib file window in Interface Builder. An example of how you might use awakeFromNib is shown below. Suppose your nib file has two custom views that must be positioned relative to each other at runtime. Trying to position them at initialization time might fail because the other view might not yet be unarchived and initialized yet. However, you can position both of them in the nib file owner’s awakeFromNib method. 

xib - новый формат IB(с версии 3) nib который хранится в текстовом файле, что делает его более подходящим для хранения в системах контроля версий

---
- [x]	**Иерархия наследования UIButton.**

UIButton -> UIControl -> UIView -> UIResponder ->NSObject. UIButton inherits UIControl ,UIControl inherits UIView ,UIView inherits   UIResponder and  UIResponder inherits NSObject.


---
- [x]	**В чем разница CollectionViews & TableViews?**

UICollectionView can do pretty much everything that UiTableView can do. The collection view is much more robust in terms of layout features and other things like animations. The way to set up both views with cell registration, dequeuing the cells, specifying size and heights are pretty much the same.

UITableView is the base mechanism for displaying sets of data on iOS. It’s a simple list, which displays single-dimensional rows of data. It’s smooth because of cell reuse and other magic.
UICollectionView is the model for displaying multidimensional data — it was introduced just a year or two ago with iOS 6, I believe.
The main difference between the two is how you want to display 1000 items. UITableViewhas a couple of styles of cells stacked one over the other. You should not try to bend it to do any other kind of things too much complex than that kind of layout.

UICollectionView is a much more powerful set of classes that allow modifying almost every aspect of how your data will appear on the screen, especially its layout but also other things. You can see UITableViews in almost every iOS application (for example Contacts or iPod), while UICollectionViews are more difficult to see (the grid in Photos, but also the over flow in iPod).

Advantages of UICollectionView
The biggest advantage of using collection view is the ability to scroll horizontally. If you have seen the AppStore app it has horizontal views which concept is used in many applications. If you want multiple columns in your apps then UICollectionView is the best way to do it. It is possible to have multiple columns in table view as well but it gets really messy since you are dealing with and array to display data in a table view. 
UICollectionView Supports complex layouts. Apple provides you with something called UICollectionViewDelegateFlowLayout which provides you the flow of left and right as well as up and down. 
UICollectionView can more easily implement drag and drop functionality which lets you manipulates things on the screen itself. If you try this with UITableView obviously you can’t do that. 
UICollectionView supports custom animations based on different layouts which again cannot be done in UITableView. Advantages of collection view are endless but these are some major ones for me.

Disadvantages of UICollectionView
Major disadvantages of using UICollectionView is auto sizing of your cells, we don not really need auto sizing that much so it’s not that big of a problem. It takes a lot of trial and error to get auto sizing to work correctly ( not saying I’m good at it ). UITableView wins here as all you have to do is return UITableView automatic dimensions for the height of your row in each one of your cells.

So to sum this up, if you need something standard like most table views in iOS I will choose the UITableView, but if you need more control over your layout, go with UICollectionView.


---
- [x]	**Что такое UIStackView?**

В прямом смысле, UIStackView не является графическим элементом и не обладает визуальным представлением, поэтому изменение таких его свойств, как backgroundColor, не имеет эффекта. Единственная задача UIStackView — организация расположения объектов. По сути, он лишь выступает контейнером для других UIView (в том числе и UIStackView, что бывает полезно), которые размещает по определенным правилам.

Основные настройки призводятся свойствами:

axis — ось (направление) расположения элементов, значение UILayoutConstraintAxis. По умолчанию — Horizontal.
alignment — выравнивание по оси, перпендикулярной axis, значение UIStackViewAlignment. По умолчанию — Fill.
distribution — распределение объектов вдоль оси axis, значение UIStackViewDistribution. По умолчанию — Fill.
spacing — расстояние (для некоторых distribution минимальное) между объектами. По умолчанию — 0.
Любое из этих свойств можно изменять динамически, при этом объекты передвинутся автоматически.

Также, разработчики постарались, добавив в Interface Builder практически все настройки UIStackView, поэтому работать там с ним очень удобно, особенно, если необходимо привязывать значения к сайзклассам.

---
- [x]	**Какая ваша любимая библиотека визуализации диаграмм (visualize chart library)?**

Core-plot.

---
- [x]	**Что такое Autolayout?**

Auto Layout занимается динамическим вычислением позиции и размера всех view на основе constraints — правил заданных для того или иного view. Самый большой и очевидный плюс для разработчика в использовании Auto Layout в том, что исчезает необходимость в подгонке размеров приложения под определенные устройства. Auto Layout изменяет интерфейс в зависимости от внешних или внутренних изменений. Минус Auto Layout состоит в том, что вычисление конкретных значений сводится к задаче решения системы линейных уравнений, поэтому добавление каждого нового констрейнта ощутимо увеличивает сложность расчета конкретных значений.



#### CoreData, Persistency

---
- [x]	**Какие есть типы хранилищ (data percistency) и какую стратегию хранения использовать в том или ином случае?**

1. XML
2. SQLite
3. In-Memory
4. Binary


---
- [ ]	**Какие есть лимиты у JSON/PLIST?**


---
- [x]	**Что такое Keychain?**

Одним из наиболее важных элементов безопасности для iOS разработчиков является Keychain, специализированная база данных для хранения метаданных и конфиденциальной информации. Использование Keychain — лучшая практика для хранения небольших фрагментов данных, которые имеют решающее значение для вашего приложения, таких как секреты и пароли.

Зачем использовать Keychain для более простых решений? Достаточно ли хранить пароль пользователя в base-64 в UserDefaults? Точно нет! Для злоумышленника вполне тривиально восстановить пароль, сохраненный таким образом. Безопасность сложна, и попытка создания вашего собственного решения не очень хорошая идея. Даже если ваше приложение не для финансового учреждения, хранение личных данных пользователя не следует воспринимать легкомысленно.

---
- [x]	**Какие есть лимиты у SQLite?**

Максимальный объем хранимых данных базы SQLite составляет 2 терабайта. Чтение из базы данных может производиться одним и более потоками, например несколько процессов могут одновременно выполнять SELECT. Однако запись в базу данных может осуществляться, только, если база в данный момент не занята другим процессом. SQLite не накладывает ограничения на типы данных. Любые данные могут быть занесены в любой столбец. Ограничения по типам данных действуют только на INTEGER PRIMARY KEY, который может содержать только 64-битное знаковое целое. SQLite версии 3.0 и выше позволяет хранить BLOB данные в любом поле, даже если оно объявлено как поле другого типа. Обращение к SQLite базе из двух потоков одновременно неизбежно вызовет краш. Выхода два:

Синхронизируйте обращения при помощи директивы @synchronized. Если задача закладывается на этапе проектирования, завести менеджер запросов на основе NSOperationQueue. Он страхует от ошибок автоматически, а то, что делается автоматически, часто делается без ошибок. Пример SQLite:
```
- (int)createTable:(NSString *)filePath {
sqlite3 *db = NULL;
int rc = 0;

rc = sqlite3_open_v2([filePath cStringUsingEncoding:NSUTF8StringEncoding], &db, SQLITE_OPEN_READWRITE | SQLITE_OPEN_CREATE, NULL);
if (SQLITE_OK != rc) {
sqlite3_close(db);
NSLog(@"Failed to open db connection");
} else {
char *query ="CREATE TABLE IF NOT EXISTS students (id INTEGER PRIMARY KEY AUTOINCREMENT, name  TEXT, age INTEGER, marks INTEGER)";
char *errMsg;
rc = sqlite3_exec(db, query, NULL, NULL, &errMsg);
if (SQLITE_OK != rc) {
NSLog(@"Failed to create table rc:%d, msg=%s",rc,errMsg);
}
sqlite3_close(db);
}
return rc;
}
```

---
- [x]	**Какие есть преимущества у Realm?**

Преимущества над другими БД

- Быстрая: Realm — невероятно быстрая библиотека для работы с базой данных. Realm быстрее, чем SQLite и CoreData (ORM-обертка над SQLite), и сравнительные тесты — лучшее доказательство для этого.
- Кросс-платформенная: Файлы базы данных Realm кросс-платформенные и могут совместно использоваться iOS и Android. Независимо от того, Вы работаете с Java, Objective-C, или Swift, Вы будете использовать высокоуровневые модели.
- Производительность: С позиции работоспособности было доказано что мобильная база данных Realm выполняет запросы и синхронизирует объекты значительно быстрее, чем Core Data, и осуществляет параллельный доступ к данным без проблем. Это значит, что несколько источников могут получить доступ к одному и тому же объекту без необходимости управлять блокировкой или каких-либо проблем с несогласованностью данных.
- Шифрование: Мобильная база данных Realm предлагает службы шифрования для защиты базы на диске с помощью AES-256 + SHA2 64-разрядного шифрования
- Хорошо документированная и есть отличная поддержка: Команда Realm предоставила читаемую, хорошо организованную документацию о Realm. Если у вас возникли проблемы, вы можете связаться с ними через Twitter, Github или Stackoverflow.
-Бесплатная: Со всеми этими удивительными функциями Realm абсолютно бесплатна.


---
- [x]	**Что такое нормализация данных?**

[https://habr.com/ru/post/254773/]

---
- [x]	**Что такое Core Data?**

Core Data уменьшает количество кода, написанного для поддержки модели слоя приложения, как правило, на 50% - 70%, измеряемое в строках кода. Core Data имеет зрелый код, качество которого обеспечивается путем юнит-тестов, и используется ежедневно миллионами клиентов в широком спектре приложений. Структура была оптимизирована в течение нескольких версий. Она использует информацию, содержащуюся в модели и выполненяет функции, как правило, не работающие на уровне приложений в коде. Кроме того, в дополнение к отличной безопасности и обработке ошибок, она предлагает лучшую масштабируемость при работе с памятью, относительно любого конкурирующего решения. Другими словами: вы могли бы потратить долгое время тщательно обрабатывая Ваши собственные решения оптимизации для конкретной предметной области, вместо того, чтобы получить преимущество в производительности, которую Core Data предоставляет бесплатно для любого приложения.

Когда нецелесообразно использовать Core Data:

Если планируется использовать очень небольшой объем данных. В этом случае проще воспользоваться для хранения Ваших данных объектами коллекций - массивами или словарями и сохранять их в plist-файлы
Если используется кроссплатформерная архитектура или требуется доступ к строго определенному формату файла с данными (хранилищу), например SQLite
Использование баз данных клиент-сервер, например MySQL или PostgreSQL


---
- [x] **Что такое NSFetchRequest?**

NSFetchRequest используется в качестве запроса выборки данных из модели. Этот инструмент позволяет задавать правила фильтрации и сортировки объектов на этапе извлечения их из базы данных. Данная операция становится во много раз эффективнее и производительнее, чем если бы мы сначала делали извлечение всех объектов (а если их 10000 и больше?), а потом вручную сортировали или производили фильтрацию интересующих нас данных.


Зная основные свойства этого класса, можно с лёгкостью оперировать запросами и получать конкретную выборку без разработки дополнительных алгоритмов и костылей — всё уже реализовано в Core Data.


---
- [x]	**Что такое NSPersistentContainer?**

NSPersistentContainer — класс, для сохранения и извлечения данных.


---
- [x]	**Использовали ли NSFetchedResultsController? Почему?**

NSFetchedResultsController представляет собой контроллер, предоставляемый фреймворком Core Data для управления запросами к хранилищу. При изменении данных в CoreData информация в таблице будет актуализироваться.

NSFetchedResultsController предоставляет механизм для обработки данных (изменения, удаления, добавления) и отображает эти изменения в таблице.


---
- [x]	**Что такое контекст (Managed object context)? Как происходят изменения в NSManagedObjectContext?**

NSManagedObjectContext - это среда в которой находится объект и которая следит за состоянием обьекта и зависимыми объектами.

---
- [x]	**Что такое Persistent store coordinator? Зачем нужен NSPersistentStoreCoordinator?**

NSPersistentStoreCoordinator отвечает за хранение объектов данных которые передаются из NSManagedObjectContext

---
- [x]	**Зачем нужно делать двустороннии связи в таблицах?**

Связь многие ко многим реализуется в том случае, когда нескольким объектам из таблицы А может соответствовать несколько объектов из таблицы Б, и в тоже время нескольким объектам из таблицы Б соответствует несколько объектов из таблицы А.


---
- [x]	**Что таке Fetched Property и особенности работы с ним по сравнению с обычной связью?**

Это свойство NSManagedObject которое хранит NSFetchRequest

---
- [x]	**Что такое Fault и зачем он нужен?**

Fault является прототипом объекта, который представляет собой управляемый объект, который еще не был полностью реализован, или коллекцию объектов, которые представляют отношения: -Управляемый объект понижает экземпляр соответствующего класса, но его постоянные переменные не инициализируются. -Отношение понижения является подклассом класса коллекции, который представляет отношения.


---
- [x]	**В каких случаях лучше использовать SQLite, а в каких Core Data?**

В большинстве случаев Core Data хранит данные в SQLite


---
- [x]	**Какие есть нюансы при использовании Core Data в разных потоках? Как синхронизировать данные между потоками(Как синхронизировать контекст)? Синхронизация разных типов NSManagedObjectContext (получение и изменение данных в child контекстах)?**

NSManagedObjectContext - это среда в которой находится объект и которая следит за состоянием обьекта и зависимыми объектами.

NSManagedObjectContext не thread-safe для многопоточности основная идея - создавать для кажного потока свой NSManagedObjectContext и потом синхронизтровать.


---
- [x]	**Как использовать СoreData совместно с многопоточностью?**

NSManagedObjectContext не thread-safe read для многопоточности основная идея - создавать для каждого потока свой NSManagedObjectContext и потом синхронизировать.


---
- [x]	**Что такое NSManagedObjectId? Можем ли мы сохранить его на потом если приложение закроется?**

NSManagedObjectID объект является универсальным идентификатором для управляемого объекта, а также предоставляет основу для уникальности в структуре Core Data. NSManagedObjectID – универсальный потокобезопасный идентификатор. Бывает временным и постоянным. Используется в случае передачи объекта из одного контекста в другой.

---
- [x]	**Какие типы хранилищ поддерживает CoreData?**

XML SQLite In-Memory Binary



---
- [x]	**Что такое ленивая загрузка (lazy loading)? Что ее связывает с CoreData? Опишите ситуация когда она может быть полезной?**

Для загрузки данных из БД в память приложения удобно пользоваться загрузкой не только данных об объекте, но и о сопряжённых с ним объектах. Это делает загрузку данных проще для разработчика: он просто использует объект, который, тем не менее вынужден загружать все данные в явном виде. Но это ведёт к случаям, когда будет загружаться огромное количество сопряжённых объектов, что плохо скажется на производительности в случаях, когда эти данные реально не нужны. Паттерн Lazy Loading (Ленивая Загрузка) подразумевает отказ от загрузки дополнительных данных, когда в этом нет необходимости. Вместо этого ставится маркер о том, что данные не загружены и их надо загрузить в случае, если они понадобятся. Как известно, если Вы ленивы, то вы выигрываете в том случае, если дело, которое вы не делали на самом деле и не надо было делать.

Это автоматическая подгрузка fault зависимостей lazy loading лучше для использования памяти, и быстрее, для извлечения связанных больших или редкоиспользуемых объектов


---
- [ ]	**Составить SQL запрос на выборку всех проектов на которых сидит девелопер с id ==3. (Developers:id,name; Projects:id,name; Developers&Projects:project_id,developer_id)?**




#### CoreAnimation, CoreGraphics
---
- [x]	**Что такое CALayer?**

Полный тутор по корграфике.
[https://habr.com/ru/post/309506/]

---
- [x]	**Чем отличается UIView от CALayer?**

в UIView добавлены обработчики событий которых нет в CALayer


---
- [x]	**Какие типы CALayer есть?**

- CATextLayer 
- CAOpenGLLayer 
- CATiledLayer 
- CALayer

---
- [x]	**Чем отличается UIView based Animation от Core Animation?**

UIView Animation - это по факту Cocoa оболочка над CoreAnimation, но с упрощенным интерфейсом


---
- [x]	**Тайминги в CoreAnimation?**
Классы анимации и протокол CAMediaTiming предоставляют функции для управления продолжительностью анимации, темпом (как интерполированные значения распределяются по длительности), повторить ли анимацию и сколько раз, следует ли автоматически обратная когда каждый цикл завершается, и ее визуальное состояние, когда анимация завершена.

---
- [ ]	**Что такое backing store?**

a bitmap that stores the result of all the rendering operations.

This backing store image can be displayed on screen repeatedly until the view needs some different drawing (e.g., for a highlighted state). At that point, the process would start over by sending the view the -[UIView setNeedsDisplay] message.
```
enum {
   kCGBackingStoreRetained     = 0,
   kCGBackingStoreNonretained  = 1,
   kCGBackingStoreBuffered     = 2
};
```

---
- [x]	**Чем отличаются аффинные преобразования от трехмерных?**
As MSN said, they are used in different cases. CGAffineTransform is used for 2-D manipulation of NSViews, UIViews, and other 2-D Core Graphics elements.

CATransform3D is a Core Animation structure that can do more complex 3-D manipulations of CALayers. CATransform3D has the same internal structure as an OpenGL model view matrix, which makes sense when you realize that Core Animation is built on OpenGL (CALayers are wrappers for OpenGL textures, etc.). I've found that this similarity of internal structure, combined with some nice helper functions that Apple provides, can let you do some neat OpenGL optimizations, as I post here.

When it comes down to choosing which do use, ask yourself if you're going to work with views directly in a 2-D space (CGAffineTransform) or with the underlying Core Animation layers in 3-D (CATransform3D). I use CATransform3D more frequently, but that's because I spend a lot of time with Core Animation.

---
- [ ]	**Нужно ли ретейнить (посылать сообщение retain) делегат для CAAnimation?**


---
- [ ]	**Можно ли отследить изменение alpha, через KVO в CALayer?**




#### iOS Platform
---
- [x]	**Какие бывают состояния (states) у приложения?**

Not running

The app has not been launched or was running but was terminated by the system.

Inactive

The app is running in the foreground but is currently not receiving events. (It may be executing other code though.) An app usually stays in this state only briefly as it transitions to a different state.

Active

The app is running in the foreground and is receiving events. This is the normal mode for foreground apps.

Background

The app is in the background and executing code. Most apps enter this state briefly on their way to being suspended. However, an app that requests extra execution time may remain in this state for a period of time. In addition, an app being launched directly into the background enters this state instead of the inactive state. For information about how to execute code while in the background, see Background Execution.

Suspended

The app is in the background but is not executing code. The system moves apps to this state automatically and does not notify them before doing so. While suspended, an app remains in memory but does not execute any code.

When a low-memory condition occurs, the system may purge suspended apps without notice to make more space for the foreground app.


---
- [x]	**Жизненный цикл приложения?**

When an iOS app is launched the first thing called is

application: willFinishLaunchingWithOptions:-> Bool. This method is intended for initial application setup. Storyboards have already been loaded at this point but state restoration hasn’t occurred yet.

Launch

application: didFinishLaunchingWithOptions: -> Bool is called next. This callback method is called when the application has finished launching and restored state and can do final initialization such as creating UI.
applicationWillEnterForeground: is called after application: didFinishLaunchingWithOptions: or if your app becomes active again after receiving a phone call or other system interruption.
applicationDidBecomeActive: is called after applicationWillEnterForeground: to finish up the transition to the foreground.
Termination

applicationWillResignActive: is called when the app is about to become inactive (for example, when the phone receives a call or the user hits the Home button).
applicationDidEnterBackground: is called when your app enters a background state after becoming inactive. You have approximately five seconds to run any tasks you need to back things up in case the app gets terminated later or right after that.
applicationWillTerminate: is called when your app is about to be purged from memory. Call any final cleanups here.
Both application: willFinishLaunchingWithOptions: and application: didFinishLaunchingWithOptions: can potentially be launched with options identifying that the app was called to handle a push notification or url or something else. You need to return true if your app can handle the given activity or url.

Knowing your app’s lifecycle is important to properly initialize and set up your app and objects. You don’t have to run everything in application: didFinishLaunchingWithOptions, which often becomes a kitchen sink of setups and initializations of some sort.

I hope, above tutorial will help to clear the concepts of application lifecycle.


---
- [x]	**Какого разрешение экранов iphon'ов, и в чем разница между points (точками) и пикселями (pixels)?**

-Pixels (px) - точки на экране. -Points (pt) - плотность точек на экране.

![image](https://github.com/dashvlas/awesome-ios-interview/raw/master/Resources/Articles/Points-Pixels.png)


---
- [x]	**Что такое responder chain?**

Это цепочка по которой проходит событие от отправителя к получателю, от First Responder, по иерархии контроллеров, до root view controller, window object и последнего - app object.

UIControl Actions (например, нажатие кнопки)
User events: (touches, shakes, motion, etc...)
System events: (low memory, rotation, etc...)

---
- [ ]	**Какие типы нотификаций есть в iOS?**



---
- [ ]	**Как работают push нотификации?**

iOS-приложения не могут долгое время находиться в фоновом режиме. В целях сохранения заряда батареи приложениям, работающим в фоне, разрешено выполнять ограниченный набор действий. Вместо того, чтобы беспрерывно проверять события или производить какие-либо действия в фоновом режиме, вы можете создать серверную сторону приложения, которая будет выполнять эти действия. А когда наступит интересующее событие, серверная сторона сможет отправить приложению push-уведомление. Абсолютно любое push-уведомление может выполнять следующие три действия:

- Показать короткое текстовое сообщение.
- Воспроизвести короткий звуковой сигнал.
- Установить число на бейдже иконки приложения.

![image](https://github.com/sashakid/ios-guide/raw/master/Images/apns.png)

---
- [ ]	**Какие ограничение есть у платформы iOS?**

!!

---
- [x]	**Что такое Code Coverage (покрытие кода)?**
Покры́тие ко́да — мера, используемая при тестировании программного обеспечения. Она показывает процент исходного кода программы, который был выполнен в процессе тестирования.

Существует несколько различных способов измерения покрытия, основные из них:

- покрытие операторов — каждая ли строка исходного кода была выполнена и протестирована;
- покрытие условий — каждая ли точка решения (вычисления истинно ли или ложно выражение) была выполнена и протестирована;
- покрытие путей — все ли возможные пути через заданную часть кода были выполнены и протестированы;
- покрытие функций — каждая ли функция программы была выполнена;
- покрытие вход/выход — все ли вызовы функций и возвраты из них были выполнены.
- покрытие значений параметров — все ли типовые и граничные значения параметров были проверены.

---
- [x]	**Что делает подписание кода (code signing)?**

Подпись исполняемого кода — это процесс, при котором в непосредственно исполняемые файлы или скрипты встраиваются дополнительно электронные подписи, позволяющие произвести проверку их авторства или целостность, часто с использованием хеш-сумм.

Подпись исполняемого кода помогает решить сразу несколько задач. Так как она генерируется на этапе создания исполняемых файлов, в некоторых случаях она может быть использована для предотвращения конфликтов пространств имён. Похожим образом в файлы может встраиваться информация о системе сборки, использованной при его создании, организации-создателе или авторе. Кроме того, в прикреплённую подпись может быть добавлена информация, заключающая в себе дополнительные мета-данные, такие как атрибуты, торговые марки, версии используемых компонентов


---
- [x]	**Что такое TVMLKit?**

The TVMLKit framework enables you to evaluate TVMLKit JS and TVML files from within your tvOS app. You can create TVML elements, styles, views, and view controllers through the JavaScript environment.

---
- [ ]	**Что такое ABI?**

Двоичный (бинарный) интерфейс приложений (англ. application binary interface, ABI) — набор соглашений для доступа приложения к операционной системе и другим низкоуровневым сервисам, спроектированный для переносимости исполняемого кода между машинами, имеющими совместимые ABI. В отличие от API, который регламентирует совместимость на уровне исходного кода, ABI можно рассматривать как набор правил, позволяющих компоновщику объединять откомпилированные модули компонента без перекомпиляции всего кода, в то же время определяя двоичный интерфейс.


Уровни и интерфейсы между ними. API, ABI и архитектура набора команд (ISA)
Двоичный интерфейс приложений регламентирует:

использование регистров процессора,
состав и формат системных вызовов и вызовов одного модуля другим;
формат передачи аргументов и возвращаемого значения при вызове функции.
Двоичный интерфейс приложений отличается от интерфейса программирования приложений (API). API описывает функциональность, предоставляемую библиотеками: список функций, аргументы функций, возвращаемые значения, возможные исключения, поведение функций.

Двоичный интерфейс приложений описывает функциональность, предоставляемую ядром ОС и архитектурой набора команд (без привилегированных команд). Если интерфейс программирования приложений разных платформ совпадают, код для этих платформ можно компилировать без изменений. Если для разных платформ совпадают и API, и ABI, исполняемые файлы можно переносить на эти платформы без изменений. Если API или ABI платформ отличаются, код требует изменений и повторной компиляции. API не обеспечивает совместимость среды выполнения программы — это задача двоичного интерфейса.


---
- [x]	**Что такое #keyPath()?**

And, being able to observe changes for any key-path on an ad-hoc basis can yield flexibility and promote abstraction, but the truth remains that it’s far too easy to scrub. Swift 3 smashes this problem to absolute oblivion by enforcing compile time checks on such key-paths.

Using #keyPath(), a static type check will be performed by virtue of the key-path literal string being used as a StaticString or StringLiteralConvertible. At this point, it’s then checked to ensure that it A) is actually a thing that exists and B) is properly exposed to Objective-C.

---
- [ ]	**Что IGListKit дает разработчикам?**
!!

---
- [x]	**Каковы три основных улучшения отладки в Xcode 8?**

Оперировать существующим значением свойства.
Добавлять новую строку кода.


---
- [x]	**Что такое биткод (bitcode)?**

. Для начала, вы должны иметь представление о Low Level Virtual Machine (LLVM) — универсальная система трансформации, для преобразования существующего кода в машинный код для различных архитектур.

LLVM состоит из двух частей: “фронтенда” и “бэкенда”. Первая — это высокоуровневый язык программирования, на котором вы пишете свое приложение. Например, Objective-C, Swift, Python или Ruby. Вторая часть служит для компиляции этого приложения в машинный код. Преобразование команд в понятный отдельно взятому процессору. При такой архитектуре Bitcode является прослойкой или промежуточным языком, который может повторно скомпилировать приложение в машинный код. Bitcode может преобразовать код в исполняемое приложение, основанное на необходимом наборе инструкций.


---
- [ ]	**Что такое GraphQL?**

В двух словах, GraphQL это синтаксис, который описывает как запрашивать данные, и, в основном, используется клиентом для загрузки данных с сервера. GraphQL имеет три основные характеристики:


- Позволяет клиенту точно указать, какие данные ему нужны.
- Облегчает агрегацию данных из нескольких источников.
- Использует систему типов для описания данных.


---
- [x]	**What is the biggest changes in UserNotifications?**

![Все о нотификациях](https://www.freecodecamp.org/news/ios-10-notifications-inshorts-all-in-one-ad727e03983a/)

---
- [x]	**Как получить токен устройства (device token)?**


```
- (void)application:(UIApplication *)app didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken 
{
    NSString *token = [[deviceToken description] stringByTrimmingCharactersInSet: [NSCharacterSet characterSetWithCharactersInString:@"<>"]];
    token = [token stringByReplacingOccurrencesOfString:@" " withString:@""];
    NSLog(@"content---%@", token);
} 
```

---
- [x]	**Какие ограничения (limits) у Remote Notifications?**

Remote notifications — you use one of your company’s servers to push data to user devices via the Apple Push Notification service (APNs).

Local and push notifications serve different design needs. A local notification is local to an application on an iPhone, iPad, or iPod touch. Push notifications—also known as remote notifications—arrive from outside a device. They originate on a remote server—the application’s provider—and are pushed to applications on devices (via the Apple Push Notification service) when there are messages to see or data to download.




#### Architecture
---
- [ ]	**Если вам нужно сделать рефакторинг, с чего бы вы начали?**


---
- [ ]	**SOLID?**


---
- [ ]	**Что такое protocol oriented programming?**


---
- [ ]	**Алгоритмическая сложность (big-o notation)?**


---
- [ ]	**Что такое VIPER архитектура?**


---
- [ ]	**What is the difference open & public access level?**


---
- [ ]	**What is the difference fileprivate & private access level?**


---
- [ ]	**Что такое внутренний доступ (internal access)?**


---
- [ ]	**Что такое TDD vs.BDD?**


---
- [ ]	**Что такое DDD?**


---
- [ ]	**Расскажите о паттерне MVC. Чем отличается пассивная модель от активной?**


---
- [ ]	**Паттерн MVC vs MVP vs MVVM?** 
  https://habrahabr.ru/post/215605/
  
  
---
- [ ]	**Принципы DRY?**


---
- [ ]	**Принципы KISS?**


---
- [ ]	**Что такое IoC?**


---
- [ ]	**Где мы используем Dependency Injection?**


---
- [ ]	**Когда подходящее время для внедрения зависимостей (dependency injection) в наши проекты?**


---
- [ ]	**Explain Priority Inversion and Priority Inheritance?**


---
- [ ]	**Clean Architecture?**


---
- [ ]	**Каковы главные цели фреймворков (framework)?**


---
- [ ]	**Which of the communication methods allows for a loosely coupled, one-to-many pattern and one-to-one pattern?**


---
- [ ]	**Игра в разбитые окна?**


---
- [ ]	**Объясните разницу между SDK и Framework?**


---
- [ ]	**В чем недостаток жесткого кодирования? (What is the disadvantage to hard-coding log statements?)**





#### Unit Testing

- [ ]	Что такое RGR ( Red — Green — Refactor )?

- [ ]	Объясните “Arrange-Act-Assert”?

- [ ]	Какие преимущества в написании тестов в приложениях?

- [ ]	Опишите Test Driven Development в трех простых правилах?

- [ ]	Что такое TDD?

- [ ]	Что такое Continuous Integration?

- [ ]	Чем отличается Mock от Stub. (mock - имитация поведения, stub - вводные данные)



#### Programming

- [ ]	Что такое куча (heap) и стэк (stack)? В какой памяти создаются объекты, примитивные типы и блоки?

- [ ]	Что такое полиморфизм?

- [ ]	Что такое инкапсуляция? Что такое нарушение инкапсуляции?

- [ ]	Чем абстрактный класс отличается от интерфейса?

- [ ]	Что такое сериализация объекта?

- [ ]	Что такое регулярные выражения (regular expression)?

- [ ]	Что такое перегрузка операторов (operator overloading)?

- [ ]	Что такое указатель (pointer)?

- [ ]	Что такое функция (function)?

- [ ]	Как передавать переменную как ссылку?

- [ ]	Что такое класс?

- [ ]	Что такое объект?

- [ ]	Что такое интерфейс?

- [ ]	Когда и почему мы используем объект вместо структур?



#### General

- [ ]	Ваше любимое видео с WWDC?

- [ ]	Какое ваше любимое приложение и почему?

- [ ]	Что нового появилось в iOS 10/iOS 9?

- [ ]	Что такое парное программирование?

- [ ]	Что такое ограничивающая рамка (bounding box)?

- [ ]	Сколько есть API'шек для эффективного отслеживания местоположения?

- [ ]	Какое самое главное преимущество у Swift?

- [ ]	Что делает React Native специальным для iOS?

- [ ]	Что означает бритье Яка (Yak Shaving)?

- [ ]	Каковы пять основных практических рекомендаций для улучшения типографического качества (typographic quality) мобильных проектов?

- [ ]	Что такое Alamofire?

- [ ]	Вы раньше работали в качестве подрядчика?Have you worked as a contractor before?

- [ ]	Что такое управление зависимостями (Dependency Management)?

- [ ]	Что такое диаграммы классов UML?



#### Patterns

- [ ]	Что такое паттерн Фабрика (Factory)?

- [ ]	Что такое паттерн Фасад (Facade)?

- [ ]	Что такое паттерн Декоратор (Decorator)?

- [ ]	Что такое паттерн Адаптер (Adapter)?

- [ ]	Что такое паттерн Наблюдатель (Observer)?

- [ ]	Что такое паттерн Мементо (Memento)?

- [ ]	Реализация Cинглтона (Singleton) в ARC и в non-ARC?

- [ ]	Назовите основные отличия синглтона от статического класса, и когда следует использовать один, а когда другой?

- [ ]	Как пересоздать синглтон? Можно ли обнулить объект синглтона?


#### Git

- [ ]	В чем разница между SVN и Git?

- [ ]	Какая команда Git позволяет объединить коммиты?

- [ ]	Какая команда Git позволяет нам найти плохие коммиты?

- [ ]	Какая команда Git сохраняет ваш код без коммита?



#### Additional

- [ ]	Что такое Б-деревья (B-Trees)?

- [ ]	С: Как представлены Си-структуры (CGRect, CGSize, CGPoint) в Objective-C?


#### Algorithms

- [ ]	Напишите код, который разворачивает строку на С++. (переставить символы в строке в обратном порядке)?

- [ ]	Поменять местами a и b не используя промежуточную переменную?

- [ ]	Написать функцию вычисления n-го числа последовательности фебоначи. (если решить через рекурсию, спросят чем опасно такое решение)?

- [ ]	Решить задачку о массиве: дан массив из 1001 элемента в котором присутсвуют все числа от 1 до 1000 и одно повторяется дважды, узнать какое, если к каждому элементу можно обратиться только 1 раз?

- [ ]	Как из строки вытащить подстроку?

- [ ]	В массиве 1001 число в диапазоне от 1 до 1000 включительно. Лишь одно число в массиве повторяется дважды. Надо за линейное время (и обратившись к каждому числу максимум один раз) найти продублированное число.



### Logical

- [ ]	Есть 4 человека, каждый проходит мост за 1, 2, 5, и 10 минут соответственно. Через мост они могут переходить только парами, держась за руку и только с фонариком. Притом один должен вернуться обратно и передать фонарик. Необходимо переправить всех за 17 мин на другую сторону. Задача решаема.


#### Code Puzzels

- [ ] Что произойдет здесь (компиляция  + время выполнения): 

```objc
NSString *s = [NSNumber numberWithInt:3]; 
int i = [s intValue];
```

- [ ] Что не так с этим кодом? Зачем нужны `инициализаторы`?

```objc
[[[SomeClass alloc] init] init];
```

- [ ] Сработает ли `таймер`? Почему? 

```objc
void startTimer(void *threadId) {
   [NSTimer  scheduleTimerWithTimeInterval:10.0f 
      target:aTarget 
          selector:@selector(tick: ) 
          userInfo:nil
           repeats:NO];
}

pthread_create(&thread, NULL, startTimer, (void *)t);
```

- [ ] Какой метод вызовется: класса A или класса B? Как надо изменить код, чтобы вызвался метод класса A?

```objc
@interface A : NSObject
- (void)someMethod;
@end

@implementation A
- (void)someMethod {
    NSLog(@"This is class A");
}
@end

@interface B : A
@end

@implementation B
- (void)someMethod  {
    NSLog(@"This is class B");
}
@end

@interface C : NSObject
@end

@implementation C
- (void)method  {
    A *a = [B new];
    [a someMethod];
}
```

- [ ] В каких случаях лучше использовать `strong`, а в каких `copy` для NSString? Почему?

```objc
@property (nonatomic, strong) NSString *someString;
@property (nonatomic, copy) NSString *anotherString;
```

- [ ] Что выведется в консоль? Почему?

```objc
- (BOOL)objectsCount {
    NSMutableArray *array = [NSMutableArray new];
    for (NSInteger i = 0; i < 1024; i++) {
        [array addObject:[NSNumber numberWithInt:i]];
    }
    return array.count;
}

- (void)someMethod {
    if ([self objectsCount]) {
        NSLog(@"has objects");
    }
    else {
        NSLog(@"no objects");
    }
}
```

- [ ] Выведется ли в дебагер «Hello world»? Почему?

```objc
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    dispatch_sync(dispatch_get_main_queue(), ^{
        NSLog(@"Hello world");
    });

   /* Another implementation */
   return YES;
}
```

- [ ] Что выведется в консоль?

```objc
 dispatch_async(dispatch_get_main_queue(), ^
    {
        NSLog(@"A %d", [object retainCount]);
        dispatch_async(dispatch_get_main_queue(), ^{
            NSLog(@"B %d", [object retainCount]);
        });
        NSLog(@"C %d", [object retainCount]);
    });
    NSLog(@"D %d", [object retainCount]);
```

- [ ] Что произойдет при исполнении следующего кода? 

```objc
Ball *ball = [[[[Ball alloc] init] autorelease] autorelease];
```
#### Extended questions

- [ ]	Анкета в которой просят оценить свои знания по технологиям по 10 бальной шкале.

- [ ]	Objective-C

- [ ]	C

- [ ]	Swift

- [ ]	iOS Platform

- [ ]	Architecture

- [ ]	Memory Management

- [ ]	Multithreading

- [ ]	UIKit

- [ ]	CoreData, Persistency

- [ ]	Design Patterns

- [ ]	Unit Testing

- [ ]	Git

- [ ]	Core Animation

- [ ]	Algorithms and Data Structure

- [ ]	Networking

- [ ]	Swift

- [ ]	Фундаментальные типы и коллекции?

- [ ]	Аттрибут @UIApplicationMain ?

- [ ]	Что такое Bridge Header? Как использовать Objective-C код в Swift проекте?

- [ ]	Оператор guard?

- [ ]	Интерполяция vs конкатенация строк?

- [ ]	let vs var?

- [ ]	typealias? Создание своего собственного типа?

- [ ]	nil в Swift vs nil в Objective-C? Различия?

- [ ]	Оператор '??'?



#### MOAR QUESTIONS

- [ ]	NSCoding, archiving.

- [ ]	Протокол NSCopying, почему мы не можем просто использовать любой собственный объект в качестве ключа?

- [ ]	В словарях (NSDictionary) , что нужно сделать чтобы решить эту проблему? (разница между глубоким и поверхностным копированием).

- [ ]	Что такое View (представление) и что такое window?

- [ ]	Что такое responder chain (цепочка обязанностей, паттерн chain of responsibility, на примере UI компонентов iOS), becomeFirstResponder.

- [ ]	Что означают IBOutlet и IBAction, для чего они нужны, и что значат для препроцессора?

- [ ]	Как работает UITableView?

- [ ]	Что можно сделать если клавиатура при появлении скрывает важную часть интерфейса?

- [ ]	Вы можете объяснить, что происходит когда вы посылаете объекту сообщение autorelease? 

- [ ]	Чем отвлеченный класс отличается от интерфейса?

- [ ]	Расскажите о паттерне MVC. Чем отличается пассивная модель от энергичной?

- [ ]	Какие еще паттерны знаете?

- [ ]	Что такое deadlock?

- [ ]	Что такое livelock?

- [ ]	Что такое семафор?

- [ ]	Что такое мьютекс?

- [ ]	Превосходства и недочеты синхронного и асинхронного соединения?

- [ ]	Что обозначает http, tcp?

- [ ]	Какие отличия между HEAD, GET, POST, PUT?

- [ ]	Какие спецтехнологии в iOS допустимо применять для работы с потоками. Превосходства и недочеты.

- [ ]	Какие существуют root классы в iOS? Для чего необходимы root классы?

- [ ]	Что такое указатель isa? Для чего он необходим?

- [ ]	Что происходит со способом позже того, как он не нашелся в объекте класса, которому его вызвали?

- [ ]	Чем категория отличается от растяжения (extention, неименованная категория)?

- [ ]	Дозволено ли добавить ivar в категорию?

- [ ]	Когда отменнее применять категорию, а когда наследование?

- [ ]	Какая разница между применением делагатов и нотификейшенов?

- [ ]	Как происходит ручное управление памятью в iOS?

- [ ]	Autorelease vs release?

- [ ]	Что обозначает ARC?

- [ ]	Что делать, если план написан с применением ARC, а необходимо применять классы сторонней библиотеки написанной без ARC?

- [ ]	Weak vs assign, strong vs copy?

- [ ]	Atomic vs nonatomic. Чем отличаются? Как вручную переопределить atomic/nonatomic сеттер в не ARC коде?

- [ ]	Для чего все свойства ссылающиеся на делегаты strong/retain?

- [ ]	Что такое autorelease pool?

- [ ]	Как дозволено заимплементировать autorelease pool на С ?

- [ ]	Чем отличается NSSet от NSArray? Какие операции стремительно происходят в NSSet и какие в NSArray?

- [ ]	Formal vs informal protocol.

- [ ]	Есть ли приватные либо защищенные способы в Objective-C?

- [ ]	Как имитировать множественное наследование?

- [ ]	Что такое блоки? Для чего они необходимы?

- [ ]	Когда необходимо копировать блок? Кто за это ответственен: caller либо reciever?

- [ ]	Что такое designated initializer?

- [ ]	Что такое Run Loop?

- [ ]	Чем отличается frame от bounds?

- [ ]	Что такое responder chain?

- [ ]	Если я вызову performSelector:withObject:afterDelay: – объекту пошлется сообщение retain?

- [ ]	Какие бывают состояния у приложения?

- [ ]	Как работают push нотификации?

- [ ]	Цикл жизни UIViewController?

- [ ]	Как происходит обработка memory warning? Зависит ли обработка от версии iOS?

- [ ]	Как отменнее каждого загрузить UIImage c диска(с кеша)?

- [ ]	Какой контент отменнее беречь в Documents, а какой в Cache?

- [ ]	Что такое Core Data?

- [ ]	Чем отличается UIView от CALayer?

- [ ]	Какие типы CALayer есть?

- [ ]	Чем отличается UIView based Animation от Core Animation?

- [ ]	Тайминги в CoreAnimation.

- [ ]	Что такое backing store.

- [ ]	Чем отличаются аффинные преобразования от трехмерных.

- [ ]	Для чего нужны SizeClasses.

- [ ]	Для чего нужны Content Hugging Priority / Content Compression Resistance Priority.

- [ ]	В какой момент autolayout изменяет frame у UIView.

- [ ]	Для чего служит reuseIdentifier в экземплярах ячеек.

- [ ]	Как можно ускорить работу UITableView.

- [ ]	Как отобразить вью поверх всех остальных вне зависимости от контроллера.

- [ ]	В чем отличие делегирования от KVO.

- [ ]	В чем отличие Array (Swift) и NSArray (ObjC).

- [ ]	Что такое tuple?

- [ ]	Что такое defer и guard?

- [ ]	Чем отличаются свифтовый класс и структура?

- [ ]	Что такое generic, чем отличается от AnyObject?

- [ ]	Как работает lazy в Swift?

- [ ]	В чем отличие escaping от не escaping closure?

- [ ]	Чем отличается weak от unowned?

- [ ]	Как работает public? 

- [ ]	Как работает open?

- [ ]	Как работают методы map filter reduce sort?

- [ ]	Как сохранить данные таким образом, чтобы они сохранились после удаления приложения?

- [ ]	Как получить данные их CoreData. За что отвечает NSFetchRequest, NSPersistentContainer, NSFetchedResultsController?

- [ ]	Какие есть нюансы при работе с CoreData в разных потоках?

- [ ]	Как работают связи в CoreData. Чем отличаются No action, Nullify, Cascade, Deny?

- [ ]	Как при помощи DispatchGroup или DispatchSemaphore синхронизировать 3 URL запроса так, чтобы первые 2 выполнились параллельно, а третий после того как выполнятся первые два? А как при помощи reactive?

- [ ]	В каком потоке приходит нотификация - в котором отправили, или в котором подписались?

- [ ]	В чем отличие WKWebView и SafariController?

- [ ]	Паттерн декоратор.

- [ ]	Паттерн фабрика.

- [ ]	С какими CI серверами работал, как решал проблему сертификации.

- [ ]	Что такое Fastlane.

- [ ]	Чем отличается TestFlight от Fabric / Crashlitycs.

- [ ]	Как можно открыть приложение по клику на сайте или в письме?

- [ ]	Как работает git ignore?

- [ ]	Как работает git checkout?

- [ ]	Как работает git rebase?

- [ ]	Как работают таблицы (реюз)?

- [ ]	Многопоточность и GDC.

- [ ]	Определения SOLID, MVC, принципы ООП.

- [ ]	KVC/KVO обязательно.

- [ ]	Как работает ARC? - это то и то performSelector

