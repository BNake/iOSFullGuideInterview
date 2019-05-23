# iOS Full Guide Interview
Questions &amp; Answers

##### Обозначения ответов

```
#### Answer:
```

##### Прогресс:
- [ ] Вопросы на которые нужно найти ответы. 
- [x] Готовые вопросы. 

##### Используемые обозначения(дополняется):

📌 Выучить назубок
🆘 Нужна помощь

#### Objective-C, Foundation:

---
- [x] Что такое свойство?
 
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
- [x] Как правильно реализовать сеттер для свойства с параметром retain?
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
- [x]	Директива @synthesize, @dynamic, чем отличаются друг от друга?

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
- [x]	В чем разница между точечной нотацией и использованием квадратных скобок?

При точечной нотации происходит диспетчеризация через геттер и сеттер
(если они переопределены).

Точечную нотацию правильно использовать только со свойствами.

То есть точку можно и, как многие считают, желательно, использовать для свойств, объявленных с помощью @property и нельзя (нежелательно, хотя и не невозможно) использовать для поведения, то есть для селекторов.

Так, например, в случае с updateInterface (это селектор, то есть поведение) нужно писать через [self updateInterface], а вот для, скажем, view.subviews лучше использовать for ```(NSView *v in myView.subviews) { ... };```, а не ```for (NSView *v in [myView subviews]) { ... };```

---
- [x]	Есть ли приватные и защищенные методы в Objective-C?

Не совсем так.

ivar-ы могут иметь аксессоры: @public, @private, @protected.

Фактически до любого свойства можно достучаться через KVC или runtime.

Методы могут быть только public (в .h фале) и private (в .m файле)

---
- [x]	Чем категория отличается от расширения(extension, наименованная категория)?

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
- [x]	Можно ли добавить ivar в категорию?

Да, можно.
```
Class c = objc_allocateClassPair([NSObject class], "Person", 0);
class_addIvar(c, "firstName", sizeof(id), log2(sizeof(id)), @encode(id));
Ivar firstNameIvar = class_getInstanceVariable(c, "fistName");
```

---
- [x]	Когда лучше использовать категорию, а когда наследование?

Категория используется чтобы не плодить классы, только добавить нужный метод. 
(убрать в категорию анимацию для контроллера).

Наследование используется для переопределения существующих методов. 
(рутовый класс для контроллера).

При наследовании меняетcя поведение класса, обернув его в подкласс категория позволяет добавлять методы к существующим классам без наследования не создавая экземпляр класса, который она расширяет. Новые методы добавляются при компиляции и могут быть выполнены как обычные методы расширенного класса.  


---
- [x]	Как имитировать множественное наследование?

Наследоваться от класса, который в свою очередь наследуется от другого класса и т.д.


---
- [x]	Можно ли чтобы разные классы реализовывали один протокол. (ClassA, ClassB и ClassC - реализуют ClassCProtocol)?

Ну, да, можно.
Так работают делегаты и те же датасорсы. 

---
- [x]	Формальный и неформальный (informal) протокол? Протоколы (protocols): основные отличия между c#/java интерфейсами и Objective-C протоколами? Что делать в случае если класс не реализует какой-то метод из протокола?

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
- [ ]	Почему нам не следует вызывать instance методы в методе -init (initialize)?
Вызовем бесконечный цикл, получается будет инит в ините, просто вызовем краш билда.

---
- [x]	Что такое назначенный инициализатор (designated initializer)? Приведите пример назначенного инициализатора (имеется ввиду if (self = [super ...]))?

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
- [x]	Что происходит когода мы пытаемся вызвать метод у nil указателя? Разница между nil и Nil и [NSNull null]?

Ничего. 
Это безопасно, поскольку не надо делать проверку объекта на nil.

Разница между nil и Nil и [NSNull null]?
```
#define (id)0 nil - указатель на нулевой объект

#define (Class *)0 Nil - нулевой указатель типа Class
```
---
- [x]	Что такое селектор (selector)? Как его вызвать? как отложить вызов селектора? Что делать если селектор имеет много параметров? (NSInvocation)

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
- [x]	Что такое быстрое перечисление (fast enumeration)?

Это итерация по обьектам любого класса, который реализует протокол NSFastEnumeration, в том числе NSArray, NSSet и NSDictionary. Реализация протокола состоит из одного метода: -(NSUInteger)countByEnumeratingWithState:(NSFastEnumerationState *)state objects:(id *)stackbuf count:(NSUInteger)len;  

Итерация (цикл) по коллекциям (NSArray, NSDictionary): 
for Type *item in Collection {}

---
- [x]	Что такое делегат (delegate)?

Делегат - объект который использует другой объект для реализации тех или иных функций.

Делегат - это просто объект, которому другой объект отправляет сообщения, когда происходят определенные вещи, так что делегат может обрабатывать специфические для приложения детали, для которых не был создан исходный объект. Это способ настройки поведения без подкласса.

---
- [ ] Что такое notifications (уведомления)?

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
- [x]	Какая разница между использованием делегатов (delegation) и нотификейшенов (notification)?

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
- [ ]	Какая разница между использованием селекторов, делегатов и блоков?

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
- [x]	В чем отличия KVC-KVO?

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
- [x]	Что такое KVO? Когда его нужно использовать? Методы для обозревания объектов? Работает ли KVO с instance переменными (полями) объекта?


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
- [x]	Что такое KVC? Когда его нужно использовать?

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
- [x]	Какие существуют root классы в iOS? Для чего нужны root классы?

NSObject, NSProxy, Protocol, Class.
Для чего нужны root-классы?
NSObject - корневой класс для всех классов, реализует базовую функциональность.

NSProxy - позволяет предварительно конфигурировать группы объектов по определенным 
шаблонам.


---
- [x]	Как работает proxy?

The Proxy design pattern provides a surrogate, or placeholder, for another object in order to control access to that other object. You use this pattern to create a representative, or proxy, object that controls access to another object, which may be remote, expensive to create, or in need of securing. This pattern is structurally similar to the Decorator pattern but it serves a different purpose; Decorator adds behavior to an object whereas Proxy controls access to an object.

NSProxy

The NSProxy class defines the interface for objects that act as surrogates for other objects, even for objects that don’t yet exist. A proxy object typically forwards a message sent to it to the object that it represents, but it can also respond to the message by loading the represented object or transforming itself into it. Although NSProxy is an abstract class, it implements the NSObject protocol and other fundamental methods expected of a root object; it is, in fact, the root class of a hierarchy just as the NSObject class is. Concrete subclasses of NSProxy can accomplish the stated goals of the Proxy pattern, such as lazy instantiation of expensive objects or acting as sentry objects for security. NSDistantObject, a concrete subclass of NSProxy in the Foundation framework, implements a remote proxy for transparent distributed messaging. NSDistantObject objects are part of the architecture for distributed objects. By acting as proxies for objects in other processes or threads, they help to enable communication between objects in those threads or processes. NSInvocation objects, which are an adaptation of the Command pattern, are also part of the distributed objects architecture.

Uses and Limitations

Cocoa employs NSProxy objects only in distributed objects. The NSProxy objects are specifically instances of the concrete subclasses NSDistantObject and NSProtocolChecker. You can use distributed objects not only for interprocess messaging (on the same or different computers) but you can also use it to implement distributed computing or parallel processing. If you want to use proxy objects for other purposes, such as the creation of expensive resources or security, you have to implement your own concrete subclass of NSProxy.

---
- [x]	Что такое тип id. Что случится во время компиляции если мы посылаем сообщение объекту типа id? Что случится во время выполнения если этот метод существует?

id - это указатель на любой НЕПРИМИТИВНЫЙ объект. В ARC нельзя сконвертировать примитив в id.
Разница между NSObject и id?

Все непримитивные объекты являются типом id. 
Типу id можно посылать какие угодно сообщения. 
Объекту NSObject можно посылать только декларированые у него сообщения.
Явное указание типа объекта помогает компилятору искать нужные методы и разрешать неоднозначности.

Что случится во время компиляции если мы посылаем сообщение объекту типа id?
Выполнит соответствуюший метод, если найдёт, иначе выбросит исключение.

---
- [x]	Цепочка ответсвенности, что происходит с методом после того как он не нашелся в объекте класса, которому его вызвали (в сторону forwardInvocation:)?

Вылет из приложения с NSUnknownException.

---
- [ ]	Что такое указатель isa? Для чего он нужен?

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
- [x]	В чем разница между NSArray и NSMutableArray? непотоко-безопасный NSMutableArray?

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
- [x]	Чем отличается NSSet от NSArray? Какие операции происходят быстро в NSSet и какие в NSArray?

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
- [x]	Как удалить объект в ходе итерации по циклу?

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
- [x]	Как лучше всего загрузить UIImage c диска(с кеша)?

В iOS присутствует функция кэширования, но прямого доступа к данным в кэше нет. Для получения доступа следует использовать класс NSCache. NSCache — это объекты, которые реализуют протокол NSDiscardableContent. Класс NSCache включает в себя различные политики автоматического удаления, обеспечивающие использование не слишком большого количества памяти системы.

---
- [x]	Какой контент лучше хранить в Documents, а какой в Cache?

NSDocumentDirectory - контент, созданный самим пользователем Library/Caches - все остальное

---
- [x]	NSCoding, archiving?

NSCoding это протокол для сериализации и десериализации данных. Реализуется двумя методами: -initWithCoder: и encodeWithCoder :. Классы, которые соответствуют NSCoding могут быть заархивированы с NSKeyedArchiver/NSKeyedUnarchiver

---
- [x]	Протокол NSCopying, почему мы не можем просто использовать любой собственный объект в качестве ключа в словарях (NSDictionary) , что нужно сделать чтобы решить эту проблему? (разница между глубоким и поверхностным копированием)?

NSCopying используется для создания копии экземпляра класса со всеми его свойствами Реализуется с помощью метода: -(id)copyWithZone:(NSZone *)zone

---
- [x]	Суть рантайма (Runtime), отправление сообщения? Как работает Runtime?

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
- [x]	Что представляет собой объект NSError?

NSError is a Cocoa class

An NSError object encapsulates information about an error condition in an extendable, object-oriented manner. It consists of a predefined error domain, a domain-specific error code, and a user info dictionary containing application-specific information.

Error is a Swift protocol which classes, structs and enums can and NSError does conform to.

A type representing an error value that can be thrown.

Any type that declares conformance to the Error protocol can be used to represent an error in Swift’s error handling system. Because the Error protocol has no requirements of its own, you can declare conformance on any custom type you create.

The quoted parts are the subtitle descriptions in the documentation.

The Swift’s error handling system is the pattern to catch errors using try - catch. It requires that the error to be caught is thrown by a method. This pattern is much more versatile than the traditional error handling using NSError instances. If you're planning not to implement try - catch you actually don't need the Error protocol.


---
- [x]	Что такое динамическая диспетчеризация (Dynamic Dispatch)?

Like many other languages, Swift allows a class to override methods and properties declared in its superclasses. This means that the program has to determine at runtime which method or property is being referred to and then perform an indirect call or indirect access. This technique, called dynamic dispatch, increases language expressivity at the cost of a constant amount of runtime overhead for each indirect usage. In performance sensitive code such overhead is often undesirable. This blog post showcases three ways to improve performance by eliminating such dynamism: final, private, and Whole Module Optimization.

[https://developer.apple.com/swift/blog/?id=27]

---
- [x]	Какую проблему решает делегирование?

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
- []	Как добавить свойство в существующий объект с закрытой реализацией? Можно ли это сделать через runtime?

HELP!

---
- [ ]	Сколько различных аннотаций (annotations) доступно в Objective-C?


---
- [ ]	Пожалуйста, объясните ключевое слово (keyword) final в классе?


---
- [ ]	Почему мы используем шаблон делегата для уведомления о событиях текстового поля?

---
- [ ]	What is the difference Delegates and Callbacks?



#### Memory Management

- [ ]	Объявление свойств c атрибутами: retain, assign, nonatomic, readonly, copy, weak, strong, unsafe_unretained?

- [ ]	Реализуйте следующие методы: retain/release/autorelease?

- [ ]	Почему мы не используем strong для enum свойств?

- [ ]	Как происходит ручное управление памятью - MRC в iOS?

- [ ]	autorelease vs release?

- [ ]	Что означает ARC?

- [ ]	Что делать, если проект написан с использованием ARC, а нужно использовать классы сторонней библиотеки написанной без ARC?

- [ ]	Atomic vs nonatomic. Чем отличаются? Как вручную переопределить atomic/nonatomic сеттер в не ARC коде?

- [ ]	Вопрос о циклах в графах владения (retain cycle)?

- [ ]	Как использовать self внутри блоков? Приведите пример retain cycle в блоке?

- [ ]	Зачем все свойства ссылающиеся на делегаты strong для ARC и retain для MRC?

- [ ]	Что такое autorelease pool?

- [ ]	Что такое runLoop (NSAutoreleasePool), когда он используется? (timers, nsurlconnection ...)?

- [ ]	Как связаны NSRunLoop и NSAutoreleasePool на пальцах?

- [ ]	Как можно заимплементировать autorelease pool на с++?

- [ ]	Если я вызову performSelector:withObject:afterDelay: - объекту пошлется сообщение retain?

- [ ]	Как происходит обработка memory warning(предупреждение о малом количестве памяти)? Зависит ли обработка от версии iOS, как мы должны их обрабатывать?

- [ ]	Напишите простую реализацию NSAutoreleasePool на Objective-C?

- [ ]	Когда нужно использовать метод retainCount (никогда, почему?)

- [ ]	Что случится если вы добавите только что созданный объект в Mutable Array, и пошлете ему сообщение release? Что случится если послать сообщение release массиву? Что случится если вы удалите объект из массива и попытаетесь его использовать?

- [ ]	С подвохом: как работает сборщик мусора в iOS?

- [ ]	Нужно ли ретейнить (посылать сообщение retain) делегаты?

- [ ]	Для чего используется класс NSCoder?

- [ ]	Опишите правильный способ управления памятью выделяемой под Outlet'ы?



#### Networking

- [ ]	Преимущества и недостатки синхронного и асинхронного соединения?

- [ ]	Что означает http, tcp?

- [ ]	REST, HTTP, JSON. Что это?

- [ ]	Какие различия между HEAD, GET, POST, PUT?

- [ ]	Как загрузить что-то из интернета? В чем разница между синхронными и асинхронными запросами? Небольшое задание. Опишите как загрузить изображение из интернета и отобразить его в ImageView — все это должно происходить после нажатия кнопки.

- [ ]	Что такое REST (Restful)?

- [ ]	Какую JSONSerialization имеет ReadingOptions?

- [ ]	Объясните различия в SOAP и REST?


#### Multithreading

- [ ]	Зачем мы используем synchronized?

- [ ]	В чем разница между синхронным и асинхронным таском (задачей)?

- [ ]	Что такое блоки (blocks)?

- [ ]	Какие типы блоков вы знаете (глобальные/локальные, heap/stack)?

- [ ]	Что такое обработчик завершения (completion handler)?

- [ ]	Что такое параллелизм (concurrency)?

- [ ]	Блокировки читателей-писателей (readers-writers lock)?

- [ ]	С подвохом: вопрос о несуществующем параметре atomic, что он означает? Приведите пример кейса с использованием atomic?

- [ ]	Как многопоточность работает с UIKit?

- [ ]	Как запустить селектор в (фоновом) потоке?

- [ ]	Как запустить поток (thread)? Что первым нужно сделать при запуске потока?

- [ ]	Что такое GCD? Как GCD связан с многопоточностью? Типы queue и queue == thread?

- [ ]	NSOperation vs GCD?

- [ ]	Что такое Dispatch Group?

- [ ]	NSOperation — NSOperationQueue — NSBlockOperation?

- [ ]	Что такое deadlock?

- [ ]	Что такое livelock?

- [ ]	Что такое семафор (semafor)?

- [ ]	Что такое мьютекс (mutex)?

- [ ]	Асинхронность vs многопоточность. Чем отличаются?

- [ ]	Какие технологии в iOS возможно использовать для работы с потоками. Преимущества и недостатки.

- [ ]	Как запустить поток? Что первым нужно сделать при запуске потока? (NSAutoreleasePool - пул автоосвобождения) Что такое runLoop, кодга он используется? (timers, nsurlconnection …)

- [ ]	Чем отличается dispatch_async от dispatch_sync?

- [ ]	Для чего при разработке под iOS использовать POSIX-потоки? pthread_create(&thread, NULL, startTimer, (void *)t);

- [x]	А чем реально POSIX-потоки лучше чем GCD или NSOperationQueue вместе с NSOperation? Приходилось ри реально использовать POSIX и как в этом были прюсы? Реально, просто интересно.

|    Answer  | 
| -----------| 
| Use POSIX calls if cross-platform portability is required. If you are writing networking code that runs exclusively in OS X and iOS, you should generally avoid POSIX networking calls, because they are harder to work with than higher-level APIs. However, if you are writing networking code that must be shared with other platforms, you can use the POSIX networking APIs so that you can use the same code everywhere.| 

#### UIKit

- [ ]	Разница между свойствами bounds и frame объекта UIView? Понимание системы координат?

- [ ]	Цикл жизни View Controller?

- [ ]	Что такое View (представление) и что такое window и цикл жизни UIView?

- [ ]	Что такое Responder Chain (цепочка обязанностей, паттерн chain of responsibility, на примере UI компонентов iOS ), becomeFirstResponder.

- [ ]	Что означают IBOutlet и IBAction, для чего они нужны, и что значат для препроцессора?

- [ ]	Как работает UITableView? Жизненный цикл UITableView?

- [ ]	Что можно сделать если клавиатура при появлении скрывает важную часть интерфейса?

- [ ]	Почему мы должны релизить IBOutlet'ты во viewDidUnload?

- [ ]	Что такое awakeFromNib, в чем разница между xib и nib файлами?

- [ ]	Иерархия наследования UIButton.

- [ ]	В чем разница CollectionViews & TableViews?

- [ ]	Что такое UIStackView?

- [ ]	Какая ваша любимая библиотека визуализации диаграмм (visualize chart library)?

- [ ]	Что такое Autolayout?

#### CoreData, Persistency

- [ ]	Какие есть типы хранилищ (data percistency) и какую стратегию хранения использовать в том или ином случае?

- [ ]	Какие есть лимиты у JSON/PLIST?

- [ ]	Что такое Keychain?

- [ ]	Какие есть лимиты у SQLite?

- [ ]	Какие есть преимущества у Realm?

- [ ]	Что такое нормализация данных?

- [ ]	Что такое Core Data?

- [ ] Что такое NSFetchRequest?

- [ ]	Что такое NSPersistentContainer?

- [ ]	Использовали ли NSFetchedResultsController? Почему?

- [ ]	Что такое контекст (Managed object context)? Как происходят изменения в NSManagedObjectContext?

- [ ]	Что такое Persistent store coordinator? Зачем нужен NSPersistentStoreCoordinator?

- [ ]	Зачем нужно делать двустороннии связи в таблицах?

- [ ]	Что таке Fetched Property и особенности работы с ним по сравнению с обычной связью?

- [ ]	Что такое Fault и зачем он нужен?

- [ ]	В каких случаях лучше использовать SQLite, а в каких Core Data?

- [ ]	Какие есть нюансы при использовании Core Data в разных потоках? Как синхронизировать данные между потоками(Как синхронизировать контекст)? Синхронизация разных типов NSManagedObjectContext (получение и изменение данных в child контекстах)?

- [ ]	Как использовать СoreData совместно с многопоточностью?

- [ ]	Что такое NSManagedObjectId? Можем ли мы сохранить его на потом если приложение закроется?

- [ ]	Какие типы хранилищ поддерживает CoreData?

- [ ]	Что такое ленивая загрузка (lazy loading)? Что ее связывает с CoreData? Опишите ситуация когда она может быть полезной?

- [ ]	Составить SQL запрос на выборку всех проектов на которых сидит девелопер с id ==3. (Developers:id,name; Projects:id,name; Developers&Projects:project_id,developer_id)?


#### CoreAnimation, CoreGraphics

- [ ]	Что такое CALayer?

- [ ]	Чем отличается UIView от CALayer?

- [ ]	Какие типы CALayer есть?

- [ ]	Чем отличается UIView based Animation от Core Animation?

- [ ]	Можно ли отследить изменение alpha, через KVO в CALayer?

- [ ]	Тайминги в CoreAnimation?

- [ ]	Что такое backing store?

- [ ]	Чем отличаются аффинные преобразования от трехмерных?

- [ ]	Нужно ли ретейнить (посылать сообщение retain) делегат для CAAnimation?


#### iOS Platform

- [ ]	Какие бывают состояния (states) у приложения?

- [ ]	Жизненный цикл приложения?

- [ ]	Каковы самые важные методы делегирования в приложении, с которыми будет сталкиваться разработчик?

- [ ]	Какого разрешение экранов iphon'ов, и в чем разница между points (точками) и пикселями (pixels)?

- [ ]	Что такое responder chain?

- [ ]	Какие типы нотификаций есть в iOS?

- [ ]	Как работают push нотификации?

- [ ]	Какие ограничение есть у платформы iOS?

- [ ]	Какие ограничение есть у платформы tvOS?

- [ ]	Что такое Code Coverage (покрытие кода)?

- [ ]	Что делает подписание кода (code signing)?

- [ ]	Что такое TVMLKit?

- [ ]	Что такое ABI?

- [ ]	Что такое #keyPath()?

- [ ]	Что IGListKit дает разработчикам?

- [ ]	Каковы три основных улучшения отладки в Xcode 8?

- [ ]	Что такое биткод (bitcode)?

- [ ]	Какие есть ограничения (limits) у SiriKit?

- [ ]	Что нового в iOS 10?

- [ ]	Что такое GraphQL?

- [ ]	What is the biggest changes in UserNotifications?

- [ ]	Как получить токен устройства (device token)?

- [ ]	Какие ограничения (limits) у Remote Notifications?


#### Architecture

- [ ]	Если вам нужно сделать рефакторинг, с чего бы вы начали?

- [ ]	SOLID?

- [ ]	Что такое protocol oriented programming?

- [ ]	Алгоритмическая сложность (big-o notation)?

- [ ]	Что такое VIPER архитектура?

- [ ]	What is the difference open & public access level?

- [ ]	What is the difference fileprivate & private access level?

- [ ]	Что такое внутренний доступ (internal access)?

- [ ]	Что такое TDD vs.BDD?

- [ ]	Что такое DDD?

- [ ]	Расскажите о паттерне MVC. Чем отличается пассивная модель от активной?

- [ ]	Паттерн MVC vs MVP vs MVVM? 
  https://habrahabr.ru/post/215605/

- [ ]	Принципы DRY?

- [ ]	Принципы KISS?

- [ ]	Что такое IoC?

- [ ]	Где мы используем Dependency Injection?

- [ ]	Когда подходящее время для внедрения зависимостей (dependency injection) в наши проекты?

- [ ]	Explain Priority Inversion and Priority Inheritance?

- [ ]	Clean Architecture?

- [ ]	Каковы главные цели фреймворков (framework)?

- [ ]	Which of the communication methods allows for a loosely coupled, one-to-many pattern and one-to-one pattern?

- [ ]	Игра в разбитые окна?

- [ ]	Объясните разницу между SDK и Framework?

- [ ]	В чем недостаток жесткого кодирования? (What is the disadvantage to hard-coding log statements?)



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

