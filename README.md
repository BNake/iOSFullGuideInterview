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
- [x]	**Если вам нужно сделать рефакторинг, с чего бы вы начали?**

Согласно «Википедии», рефакторинг — это процесс изменения внутренней структуры программы, не затрагивающий её внешнего поведения. Его цель — упростить понимание работы программы.

Итак, что значит «упростить понимание работы программы»? Конкретные цели рефакторинга могут быть такими:

улучшить проект существующего кода;
найти ошибки;
сделать код более понятным для других участников команды;
сделать код менее раздражающим;
упростить добавление нового кода.
Также рефакторинг помогает быстрее реализовать программные продукты. Повышается качество — и, соответственно, скорость разработки. Рефакторинг точно необходим, если к вам в команду приходит новый человек, и код в таком виде, в котором он существует, ему не понятен. Это говорит о том, что качество кода неудовлетворительно.

Для рефакторинга, во-первых, напишите хорошие тесты: unit, функциональные или интеграционные. Во-вторых, изменяйте код небольшими итерациями. На каждом шаге прогоняйте тесты. Для качественного рефакторинга полезно знать шаблоны проектирования. Без них будет сложнее проектировать и масштабировать большие проекты.

[https://iiba.ru/basic-principles-and-rules-of-refactoring/]

![image](https://iiba.ru/wp-content/uploads/2016/11/refactoring_mindmap.png)

---
- [x]	**SOLID?**

SOLID (сокр. от англ. Single responsibility, Open-closed, Liskov substitution, Interface segregation и Dependency inversion) - акроним, введённый Майклом Фэзерсом для первых пяти принципов, названных Робертом Мартином в начале 2000-х, которые означали пять основных принципов ООП и проектирования.

Принцип единственной ответственности
обозначает, что каждый объект должен иметь одну ответственность и эта ответственность должна быть полностью инкапсулирована в класс. Все его поведения должны быть направлены исключительно на обеспечение этой ответственности. Следующие приёмы позволяют соблюдать принцип единственной ответственности: выделение класса, фасад, DAO.

Принцип открытости / закрытости
означает, что программные сущности должны быть:

открыты для расширения: поведение сущности может быть расширено, путём создания новых типов сущностей
закрыты для изменения: в результате расширения поведения сущности, не должны вносится изменения в код, которые эти сущности использует
Принцип подстановки Барбары Лисков
даёт определение понятия замещения — если S является подтипом T, тогда объекты типа T в программе могут быть замещены объектами типа S без каких-либо изменений желательных свойств этой программы (например, корректность). Более простыми словами можно сказать, что поведение наследуемых классов не должно противоречить поведению, заданному базовым классом, то есть поведение наследуемых классов должно быть ожидаемым для кода, использующего переменную базового типа.

Принцип разделения интерфейса Роберт Мартин
определил так: «Клиенты не должны зависеть от методов, которые они не используют». Принцип разделения интерфейсов говорит о том, что слишком «толстые» интерфейсы необходимо разделять на более маленькие и специфические, чтобы клиенты маленьких интерфейсов знали только о методах, которые необходимы им в работе. В итоге, при изменении метода интерфейса не должны меняться клиенты, которые этот метод не используют.

Принцип инверсии зависимостей
— принцип, используемый для уменьшения зацепления в компьютерных программах. Модули верхних уровней не должны зависеть от модулей нижних уровней. Оба типа модулей должны зависеть от абстракций. Абстракции не должны зависеть от деталей. Детали должны зависеть от абстракций.


---
- [x]	**Что такое protocol oriented programming?**

[https://habr.com/ru/post/358804/]

---
- [x]	**Алгоритмическая сложность (big-o notation)?**

Оценка сложности алгоритмов — сложная штука. В быту разработки широко используется базовый вариант Big O notation — подсчёт количества элементарных операций с сокращением констант. Если на пальцевом примере, то сложность функции перебора списка от первого элемента до последнего равна O(n), где n — количество элементов списка.

Фигня в том, что при всех вариантах комплексной оценки, теорий и практик разработчики тяготеют к вышеописанному простейшему, оставляя за кадром нюансы. А оно местами здорово расходится с современной практикой.


---
- [x]	**Что такое VIPER архитектура?**

[https://habr.com/ru/post/358412/]

Отличается тем, что не относится к категории MVC. Вместо привычных 3-х слоев он предлагает 5:

View
Interactor
Presenter
Entity
Router
View: отвечает за отображение данных на экране и оповещает Presenter о действиях пользователя. Как правило слоем View в данной модели является ViewController. Пассивен, сам никогда не запрашивает данные, только получает их от презентера. Не содержит в себе логики отображения: принимает от presenter готовые к отображению данные, например, готовые строки текста, и размещает их на себе. События пользователя передает в presenter, который их обрабатывает.

Interactor: содержит бизнес-логику, связанную с получением данных (Entities).

Presenter: получает от View информацию о действиях пользователя и преображает ее в запросы к Router’у, Interactor’у, а также получает данные от Interactor’a, подготавливает их и отправляет View для отображения

Entity: простые объекты данных, по сути модель хранящая информацию и ничего более, они не являются слоем доступа к данным, так как это ответственность Interaptor.

Router: несет ответственность за переходы между VIPER-модулями.

Даже при таком поверхностном осмотре очевидно, что лучшее разделение обязанностей получается за счет большого количества классов с небольшим количеством обязанностей.


---
- [x]	**What is the difference open & public access level?**

Public and Private
Like private and fileprivate, the public and open access levels have several things in common. Entities that are declared public or open are accessible from the module in which they are defined and from any module that imports the module they are defined in. That makes sense. Right?

What Is the Difference
The difference between public and access is subtle but important. The open access level was introduced to impose limitations on class inheritance in Swift. This means that the open access level can only be applied to classes and class members, such as properties and methods.

The idea is simple. An open class can be subclassed in the module it is defined in and in modules that import the module in which the class is defined. The same applies to class members. An open method can be overridden by subclasses in the module it is defined in and in modules that import the module in which the method is defined.

This sounds very much like the public access level. That is true for Swift 2. The meaning of the public access level has changed slightly. Classes that are declared public can only be subclassed in the module they are defined in. The same applies to public class members, which can only be overridden by subclasses defined in the module they are defined in.

Why Is This Necessary
The public and open access levels add an additional layer of access control. Some classes of libraries and frameworks are not designed to be subclassed and doing so may result in unexpected behavior.

The Core Data framework is a fine example. The documentation clearly states that some methods of the NSManagedObject class should not be overridden. If Apple were to apply the public access level to these methods, developers would no longer be able to override these methods in their NSManagedObject subclasses. That is what the public and open access levels are designed for. They protect the integrity of a library or framework.

If a class or class member is marked as open, the maintainer of the library or framework communicates to developers that it is fine to subclass or override the class or class member. That is a very useful statement if you ask me.

What Are the Consequences
Before the introduction of the open access level, the public access level was the least restrictive access level. This is no longer true for classes and class members. This means that libraries and frameworks need to be updated for Swift 3.

Why is that? Public classes and class members are still accessible by other modules. Great. But public classes can no longer be subclassed and public class members can no longer be overridden. This can dramatically decrease the usefulness of several libraries and frameworks.

More Transparency
If you maintain a library or framework, the addition of the open access level is an interesting exercise. You are forced to decide whether the classes of your library or framework should be subclassable. And the same applies to class members.


---
- [x]	**What is the difference fileprivate & private access level?**


File Private 
File-private access restricts the use of an entity to its own defining source file. Use file-private access to hide the implementation details of a specific piece of functionality when those details are used within an entire file. 
Syntax: fileprivate <var type> <variable name> 
Example: fileprivate class SomeFilePrivateClass {}


Private 
Private access restricts the use of an entity to the enclosing declaration, and to extensions of that declaration that are in the same file. Use private access to hide the implementation details of a specific piece of functionality when those details are used only within a single declaration. 
Syntax: private <var type> <variable name> 
Example: private class SomePrivateClass {}

---
- [x]	**Что такое внутренний доступ (internal access)?**

Internal access enables entities to be used within any source file from their defining module, but not in any source file outside of that module. You typically use internal access when defining an app’s or a framework’s internal structure.


---
- [x]	**Что такое TDD vs.BDD?**
TDD (Test Driven Development) и BDD (Behaviour Driven Development)

 TDD — это больше о программировании и тестировании на уровне технической реализации продукта, когда тесты создают сами разработчики. BDD предполагает описание тестировщиком или аналитиком пользовательских сценариев на естественном языке — если можно так выразиться, на языке бизнеса.
 
 BDD — скорее процесс, целью которого является удешевление реализации новых фич.


---
- [x]	**Что такое DDD?**

[https://habr.com/ru/post/258693/]

Предметно-ориентированное проектирование (реже проблемно-ориентированное, англ. Domain-driven design, DDD) — это набор принципов и схем, направленных на создание оптимальных систем объектов. Сводится к созданию программных абстракций, которые называются моделями предметных областей. В эти модели входит бизнес-логика, устанавливающая связь между реальными условиями области применения продукта и кодом.

Предметно-ориентированное проектирование не является какой-либо конкретной технологией или методологией. DDD — это набор правил, которые позволяют принимать правильные проектные решения. Данный подход позволяет значительно ускорить процесс проектирования программного обеспечения в незнакомой предметной области.

Подход DDD особо полезен в ситуациях, когда разработчик не является специалистом в области разрабатываемого продукта. К примеру: программист не может знать все области, в которых требуется создать ПО, но с помощью правильного представления структуры, посредством предметно-ориентированного подхода, может без труда спроектировать приложение, основываясь на ключевых моментах и знаниях рабочей области.


---
- [x]	**Расскажите о паттерне MVC. Чем отличается пассивная модель от активной?**

Концепция MVC позволяет разделить данные, представление и обработку действий пользователя на три отдельных компонента:

Модель (англ. Model). Модель предоставляет знания: данные и методы работы с этими данными, реагирует на запросы, изменяя своё состояние. Не содержит информации, как эти знания можно визуализировать.
Представление, вид (англ. View). Отвечает за отображение информации (визуализацию). Часто в качестве представления выступает форма (окно) с графическими элементами.
Контроллер (англ. Controller). Обеспечивает связь между пользователем и системой: контролирует ввод данных пользователем и использует модель и представление для реализации необходимой реакции.
Важно отметить, что как представление, так и контроллер зависят от модели. Однако модель не зависит ни от представления, ни от контроллера. Тем самым достигается назначение такого разделения: оно позволяет строить модель независимо от визуального представления, а также создавать несколько различных представлений для одной модели.

Активная модель
Активная модель — модель оповещает представление о том, что в ней произошли изменения, а представления, которые заинтересованы в оповещении, подписываются на эти сообщения. Это позволяет сохранить независимость модели как от контроллера, так и от представления.

Пассивная модель
Пассивная модель — модель не имеет никаких способов воздействовать на представление или контроллер, и пользуется ими в качестве источника данных для отображения. Все изменения модели отслеживаются контроллером и он же отвечает за перерисовку представления, если это необходимо. Такая модель чаще используется в структурном программировании, так как в этом случае модель представляет просто структуру данных, без методов их обрабатывающих.


---
- [x]	**Паттерн MVC vs MVP vs MVVM?** 

  [https://habrahabr.ru/post/215605/]
  
MVVM
Этот паттерн удобен в проектах, где используются такие фреймворки, как ReactiveCocoa i RxSwift, в которых есть концепция «связывания данных» — связывание данных с визуальными элементами в двустороннем порядке. В этом случае, использование паттерна MVC является очень неудобным, поскольку привязка данных к представлению (View) — это нарушение принципов MVC.

View (ViewController) и Model имеют «посредника» — View Model. View Model — это независимое от UIKit представления View. View Model вызывает изменения в Model и самостоятельно обновляется с уже обновленным Model, и, так как связывание происходит через View, то View обновляется тоже.

Недостатком является то, что «вместо 1000 строк в ViewController может выйти 1000 строк в ViewModel». Также одна из проблем использования фреймворков для «реактивного программирования» — достаточно просто все поломать и может пойти очень много времени на багфиксинг. Кому-то может показаться, что RxSwift, например, упрощает написание кода, но достаточно заглянуть в стек вызовов друга «rx-» метода, чтобы оценить это «упрощение». Можно сюда же добавить проблемы с документацией и постоянные проблемы с автокомплитом в xCode.

MVP
MVP-паттерн «эволюционировал» из MVC и состоит из таких трех компонентов:

Presenter (независимый посредник UIKit)
Passive View (UIView и/или UIViewController)
Model
Этот паттерн определяет View как получающий UI-события от пользователя и тогда вызывает соответствующий Presenter, если это нужно. Presenter же отвечает за обновление View с новыми данными, полученными из модели.

Достоинства
лучшее разделение кода
хорошо тестируется
Недостатки
сравнительно с MVC имеет значительно больше кода
разработка и поддержка занимают больше времени
  
  
---
- [x]	**Принципы DRY?**

Это принцип разработки программного обеспечения, нацеленный на снижение повторения информации различного рода, особенно в системах со множеством слоёв абстрагирования. Принцип DRY формулируется как: «Каждая часть знания должна иметь единственное, непротиворечивое и авторитетное представление в рамках системы». Он был сформулирован Энди Хантом (англ.) и Дэйвом Томасом (англ.) в их книге The Pragmatic Programmer (англ.). Они применяли этот принцип к «схемам баз данных, планам тестирования, сборкам программного обеспечения, даже к документации». Когда принцип DRY применяется успешно, изменение единственного элемента системы не требует внесения изменений в другие, логически не связанные элементы. Те элементы, которые логически связаны, изменяются предсказуемо и единообразно. Помимо использования методов и функций в коде, Томас и Хант считают необходимым использование генераторов кода, автоматических систем компиляции.


---
- [ ]	**Принципы KISS?**

Принцип, запрещающий использование более сложных средств, чем необходимо. Изречение часто вызываемое при обсуждении вопросов проектирования с целью парирования нарастающей функциональности и управления сложностью разработки. Возможно, связано с изречением Keep It Short and Simple. Принцип декларирует простоту системы в качестве основной цели и/или ценности. Эрик Рэймонд в своей книге резюмирует философию UNIX как широко используемый принцип KISS.

- Разбивайте задачи на подзадачи которые не должны по вашему мнению длиться более 4-12 часов написания кода
- Разбивайте задачу на множество более маленьких задач, каждая задача должна решаться одним или парой классов
- Сохраняйте ваши методы маленькими. Каждый метод должен состоять не более чем из 30-40 строк. Каждый метод должен решать одну маленькую задачу, а не множество случаев. Если в вашем методе множество условий, разбейте его на несколько. Это повысит читаемость, позволит легче поддерживать код и быстрее находить ошибки в нём. Вы полюбите улучшать код.
- Сохраняйте ваши классы маленькими. Здесь применяется та же техника что и с методами.
- Придумайте решение задачи сначала, потом напишите код. Никогда не поступайте иначе. Многие разработчики придумывают решение задачи во время написания кода и в этом нет ничего плохого. Вы можете делать так и при этом придерживаться выше обозначенного правила. Если вы можете в уме разбивать задачу на более мелкие части, когда вы пишете код, делайте это любыми способами. И не бойтесь переписывать код ещё и ещё и ещё… В счёт не идёт число строк, до тех пор пока вы считаете что можно ещё меньше/ещё лучше.
- Не бойтесь избавляться от кода. Изменение старого кода и написание нового решения два очень важных момента. Если вы столкнулись с новыми требованиями, или не были оповещены о них ранее, тогда порой лучше придумать новое более изящное решение решающее и старые и новые задачи.


---
- [x]	**Что такое IoC?**

Inversion of Control (инверсия управления) — это некий абстрактный принцип, набор рекомендаций для написания слабо связанного кода. Суть которого в том, что каждый компонент системы должен быть как можно более изолированным от других, не полагаясь в своей работе на детали конкретной реализации других компонентов.

IoC-контейнер — это какая-то библиотека, фреймворк, программа если хотите, которая позволит вам упростить и автоматизировать написание кода с использованием данного подхода на столько, на сколько это возможно. Их довольно много, пользуйтесь тем, чем вам будет удобно, я продемонстрирую все на примере Ninject.


---
- [x]	**Где мы используем Dependency Injection?**

Dependency Injection (внедрение зависимостей) — это одна из реализаций принципа IoC (помимо этого есть еще Factory Method, Service Locator).

 IoC, DI — очень хороший инструмент, но и как любой другой механизм использовать его нужно осознанно и к месту. Скажем, одно дело какое-нибудь небольшое консольное приложение, в котором вряд ли что-то будет меняться, или серьезный крупный проект, где пожелания заказчика часто изменчивы и противоречивы.
Вот и все, буду очень рад услышать ваши комментарии, конструктивные замечания.
Удачного всем деплоя на продакшене.


---
- [x]	**Когда подходящее время для внедрения зависимостей (dependency injection) в наши проекты?**

[https://habr.com/ru/post/434380/]



---
- [x]	**Explain Priority Inversion and Priority Inheritance?**

In one line, Priority Inversion is a problem while Priority Inheritance is a solution. Literally, Priority Inversion means that priority of tasks get inverted and Priority Inheritance means that priority of tasks get inherited. Both of these phenomena happen in priority scheduling. Basically, in Priority Inversion, higher priority task (H) ends up waiting for middle priority task (M) when H is sharing critical section with lower priority task (L) and L is already in critical section. Effectively, H waiting for M results in inverted priority i.e. Priority Inversion. One of the solution for this problem is Priority Inheritance. In Priority Inheritance, when L is in critical section, L inherits priority of H at the time when H starts pending for critical section. By doing so, M doesn’t interrupt L and H doesn’t wait for M to finish. Please note that inheriting of priority is done temporarily i.e. L goes back to its old priority when L comes out of critical section.


---
- [x]	**Clean Architecture?**

[https://habr.com/ru/post/269589/]


---
- [x]	**Каковы главные цели фреймворков (framework)?**

Фреймворк (англ. framework — каркас, структура) — программное обеспечение, облегчающее разработку и объединение разных компонентов большого программного проекта. Употребляется также слово «каркас», это набор всевозможных библиотек (инструментов) для быстрой разработки повседневных (рутинных) задач. Чаще всего использует одну из распространенных архитектур приложения (к примеру MVC) для разделения проекта на логические сегменты (модули). Главная цель фреймворка, предоставить программисту удобную среду для проекта с большим и хорошо расширяемым функционалом.


---
- [x]	**Игра в разбитые окна?**

В криминалистике существует интересная теория под названием "Теория разбитых окон" (ТРО). Суть её в том, что разбитое окно, при несвоевременной замене, влечёт за собой целую серию разбитых окон. Более того, серия разбитых окон может быть индикатором повышающегося уровня преступности в заданном регионе. На ум сразу приходит известная всем фраза "Чисто не там, где убирают, а там где не сорят". Согласно этой теории, чисто именно там, где убирают, стимулируя тем самым людей не сорить в будущем. Стоит заметить, что это применимо не только к окнам :) Автолюбителям наверняка знакома эта теория на дорогах, хотя они могут и не догадываться о её существовании. Я не раз замечал большое скопление автомобилей, припаркованных под знаком "Остановка запрещена". Стоит лишь одному остановиться под ним, остальные водители не заставят себя долго ждать. Удивительно как мало внимания дорожная полиция уделяет этому факту. Правонарушения должны своевременно пресекаться.

Но вернёмся всё же в мир разработки программного обеспечения. Удивительно, но и здесь ТРО находит свой отклик. Современный процесс создания ПО находится под жестким прессингом сроков. Бизнесу очень важно как можно раньше поставить продукт на рынок по ряду причин. Отсюда рождаются различные методологии управления вроде Agile, Lean, формируются концепции MVP (Minimum Viable Product). Как следствие, страдает качество кода, он начинает "протухать". С "вонючим кодом" можно жить, более того, практически всегда он есть в той или иной степени, это нормально. Но его нарастающая доля служит одним из первых индикаторов того, что пора "бить во все колокола". Почему? Основываясь на собственном опыте скажу, что программист охотнее "говнокодит" там, где этого "говнокода" предостаточно. И наоборот, человек несколько раз подумает, прежде чем отправлять свой шедевр на код ревью, если в проекте стараются соблюдать чистоту кода. Помимо прочего, "разбитое окно" в коде создаёт ощущение наплевательского отношения к проекту, тем самым порождая чувство безразличности к нему. Зачем пытаться что-то изменить, если всем наплевать?

Чините "разбитые окна" в коде как можно чаще.


---
- [x]	**Объясните разницу между SDK и Framework?**

Library:

A library is a collection of subroutines or classes used to develop software. Libraries contain code and data that provide services to independent programs. This allows code and data to be shared and changed in a modular fashion.

Framework:

A software framework, in computer programming, is an abstraction in which common code providing generic functionality can be selectively overridden or specialized by user code providing specific functionality. Frameworks are similar to software libraries in that they are reuseable abstractions of code wrapped in a well-defined API. Unlike libraries, however, the overall program's flow of control is not dictated by the caller, but by the framework. This inversion of control is the distinguishing feature of software frameworks.

SDK:

A software development kit (SDK or "devkit") is typically a set of development tools that allows a software engineer to create applications for a certain software package, software framework, hardware platform, computer system, video game console, operating system, or similar platform. It may be something as simple as an application programming interface in the form of some files to interface to a particular programming language or include sophisticated hardware to communicate with a certain embedded system. Common tools include debugging aids and other utilities often presented in an IDE. SDKs also frequently include sample code and supporting technical notes or other supporting documentation to help clarify points from the primary reference material.

So:

Library is code that your application calls.
Framework is an application or library that is almost ready made. You just fill in some blank spots with your own code that the framework calls.
SDK is a bigger concept as it can include libraries, frameworks, documentation, tools, etc.
.NET is really more like a platform, not a software framework.


---
- [x]	**В чем недостаток жесткого кодирования? (What is the disadvantage to hard-coding log statements?)**

**Wikipedia:

Hard coding (also hard-coding or hardcoding) refers to the software development practice of embedding what may, perhaps only in retrospect, be considered an input or configuration data directly into the source code of a program or other executable object, or fixed formatting of the data, instead of obtaining that data from external sources or generating data or formatting in the program itself with the given input.

Hard-coding is considered an antipattern.

Considered an anti-pattern, hard coding requires the program's source code to be changed any time the input data or desired format changes, when it might be more convenient to the end user to change the detail by some means outside the program.

Sometimes you cannot avoid it but it should be temporary.

Hard coding is often required. Programmers may not have a dynamic user interface solution for the end user worked out but must still deliver the feature or release the program. This is usually temporary but does resolve, in a short term sense, the pressure to deliver the code. Later, softcoding is done to allow a user to pass on parameters that give the end user a way to modify the results or outcome.

- Hardcoding of messages makes it hard to internationalize a program.
- Hardcoding paths make it hard to adapt to another location.


**The only advantage of hardcoding seems to be fast deliver of code.



#### Unit Testing

---
- [x]	**Что такое RGR ( Red — Green — Refactor )?**

В основе TDD лежит очень простая идея — пишем тесты до того, как пишем код. В теории звучит пугающе, но через какое-то время приходит понимание практик и приемов, и вариант написание тестов после вызывает ощутимый дискомфорт. Одна из ключевых практик это итеративность, т.е. делать все маленькими, сфокусированными итерациями, каждая из которых описывается как Red-Green-Refactor.


В красной фазе — пишем падающий тест, при чем очень важно, чтобы он падал с ясной, понятной причиной и описанием и чтобы сам тест был законченым и проходил, когда написан код. Тест должен проверять поведение, а не реализацию, т.е. следовать подходу "черного ящика", далее поясню почему.


В зеленой фазе пишем минимально необходимый код чтобы пройти тест. Иногда бывает интересно попрактиковаться и доводить до асбсурда (хотя лучше не увлекаться) и когда функция возвращает boolean в зависимости от состояния системы, первым "проходом" может быть просто return true.


В фазе рефакторинга, к которой можно приступать только когда все тесты зеленые, рефакторим код и приводим его в надлежащее состояние. Необязательно даже для куска кода, который мы написали, поэтому и начинать рефакторинг важно на стабильной системе. Подход "черного ящика" как раз поможет выполнять рефакторинг, меняя реализацию, но не трогая поведение.


---
- [x]	**Объясните “Arrange-Act-Assert”?**

Суть его заключается в том, чтобы в модульном тесте чётко определить предусловия (инициализация тестовых данных,
предварительные установки), действие (собственно то, что тестируется) и постусловия (что должно быть в
результате выполнения действия). Подобное оформление повышает читаемость теста и облегчает его 
использование в качестве документации к тестируемой функциональности.


---
- [x]	**Какие преимущества в написании тестов в приложениях?**

Когда дело дойдет до написания тестов, Вы столкнетесь как с хорошими, так и с плохими новостями. Плохие новости заключаются в том, что в модульном тестировании существует следующее недостатки:

Большое количество кода: В проектах с большим тестовым охватом у Вас, возможно, будет больше тестов, чем функционального кода.
Больше поддержки: Чем больше кода, тем больше его нужно поддерживать.
Никакого верного решения: Модульное тестирование не гарантируют, и не может гарантировать, что ваш код будет без ошибок.
Занимает больше времени:  Написание тестов занимает некоторое время.


Хотя и нет идеального решения, есть светлая сторона – написание тестов имеет следующие преимущества:

Уверенность: Вы можете убедиться, что ваш код работает.
Быстрые отзывы: Вы можете использовать модульное тестирование для быстрой проверки кода, который спрятан под многими слоями навигации, – слишком большими компонентами, которые нужно проверять вручную.
Модульность: Модульное тестирование помогает Вам сосредоточить внимание на написании более модульного кода.
Ориентированность: Написания тестов для микрокомпонентов поможет Вам сосредоточить внимание на маленьких деталях.
Регрессия: Убедитесь, что ошибки, которые вы исправили ранее, остаются исправленными — и не нарушены последующими исправления.
Рефакторинг: Пока Xcode не станет достаточно умным, чтобы переписывать код самостоятельно, Вам будет нужно модульное тестирование для проверки рефакторинга.
Документация: Модульное тестирование описывает то, что Вы думаете, код должен делать; он является другим способом написание кода.


---
- [x]	**Опишите Test Driven Development в трех простых правилах?**
- You are not allowed to write any production code unless it is to make a failing unit test pass.
- You are not allowed to write any more of a unit test than is sufficient to fail; and compilation failures are failures.
- You are not allowed to write any more production code than is sufficient to pass the one failing unit test.

---
- [x]	**Что такое TDD?**
Техника разработки программного обеспечения, которая основывается на повторении очень коротких циклов разработки: сначала пишется тест, покрывающий желаемое изменение, затем пишется код, который позволит пройти тест, и под конец проводится рефакторинг нового кода к соответствующим стандартам. Кент Бек, считающийся изобретателем этой техники, утверждал в 2003 году, что разработка через тестирование поощряет простой дизайн и внушает уверенность (англ. inspires confidence).

В 1999 году при своём появлении разработка через тестирование была тесно связана с концепцией «сначала тест» (англ. test-first), применяемой в экстремальном программировании, однако позже выделилась как независимая методология.


---
- [x]	**Что такое Continuous Integration?**

Практика разработки программного обеспечения, которая заключается в постоянном слиянии рабочих копий в общую основную ветвь разработки (до нескольких раз в день) и выполнении частых автоматизированных сборок проекта для скорейшего выявления потенциальных дефектов и решения интеграционных проблем. В обычном проекте, где над разными частями системы разработчики трудятся независимо, стадия интеграции является заключительной. Она может непредсказуемо задержать окончание работ. Переход к непрерывной интеграции позволяет снизить трудоёмкость интеграции и сделать её более предсказуемой за счёт наиболее раннего обнаружения и устранения ошибок и противоречий, но основным преимуществом является сокращение стоимости исправления дефекта, за счёт раннего его выявления.


---
- [x]	**Чем отличается Mock от Stub. (mock - имитация поведения, stub - вводные данные)**
Stub

I believe the biggest distinction is that a stub you have already written with predetermined behavior. So you would have a class that implements the dependency (abstract class or interface most likely) you are faking for testing purposes and the methods would just be stubbed out with set responses. They would not do anything fancy and you would have already written the stubbed code for it outside of your test.

Mock

A mock is something that as part of your test you have to setup with your expectations. A mock is not setup in a predetermined way so you have code that does it in your test. Mocks in a way are determined at runtime since the code that sets the expectations has to run before they do anything.

Difference between Mocks and Stubs

Tests written with mocks usually follow an initialize -> set expectations -> exercise -> verify pattern to testing. While the pre-written stub would follow an initialize -> exercise -> verify.

Similarity between Mocks and Stubs

The purpose of both is to eliminate testing all the dependencies of a class or function so your tests are more focused and simpler in what they are trying to prove.



#### Programming
---
- [x]	**Что такое куча (heap) и стэк (stack)? В какой памяти создаются объекты, примитивные типы и блоки?**

Стек – очередь LIFO (last-in-first-out) структурированная область памяти, в отличие от кучи. Последовательный список с переменной длинной, включение и исключение только из вершины стека. Состоит из последовательности фреймов. Пример: после вызова метода из стека выделяется запрошенная область памяти – фрейм, который хранит значения объявленных переменных.

Операции:
- Включение
- Исключение
- Определение размера
- Очистка
- Неразрушающее чтение

Функция стека – сохранить работу, невыполненную до конца, с возможностью переключения на другую работу.

![image](https://github.com/sashakid/ios-guide/raw/master/Images/stack.png)

Куча как структура данных представляет собой дерево, где родитель A >= ребенка B => A – корень кучи. Max куча, Min куча.

Операции:
- Найти max или min
- Удалить max или min
- Увеличить ключи или уменьшить
- Добавить
- Слияние

Куча как область памяти – реализация динамически распределяемой памяти, в которой хранятся все объекты (вызов alloc в Objective-C выделяет из кучи требуемую область памяти).

---
- [x]	**Что такое полиморфизм?**

– возможность объектов с одинаковой спецификацией иметь различную реализацию (использование одного имени для решения двух или более схожих, но технически разных задач). Если функция описывает разные реализации (возможно, с различным поведением) для ограниченного набора явно заданных типов и их комбинаций, это называется ситуативным полиморфизмом (ad hoc polymorphism). Ситуативный полиморфизм поддерживается во многих языках посредством перегрузки функций и методов. Если же код написан отвлеченно от конкретного типа данных и потому может свободно использоваться с любыми новыми типами, имеет место параметрический полиморфизм. Некоторые языки совмещают различные формы полиморфизма, порой сложным образом, что формирует самобытную идеологию в них и влияет на применяемые методологии декомпозиции задач. Например, в Smalltalk любой класс способен принять сообщения любого типа, и либо обработать его самостоятельно (в том числе посредством интроспекции), либо ретранслировать другому классу — таким образом, несмотря на широкое использование перегрузки функций, формально любая операция является неограниченно полиморфной и может применяться к данным любого типа.


---
- [x]	**Что такое инкапсуляция? Что такое нарушение инкапсуляции?**

– скрытие методов и переменных от других методов или переменных или других частей программы. Целособразно:

Инкапсуляция позволяет вам знать о существовании двери, о том, открыта она или заперта, но при этом вы не можете узнать, из чего она сделана (из дерева, стекловолокна, стали или другого материала), и уж никак не сможете рассмотреть отдельные волокна древесины.


---
- [x]	**Чем абстрактный класс отличается от интерфейса?**

Интерфейсы, в отличии от абстрактных классов, поддерживают множественное наследование. Т.е. класс-потомок может реализовывать 2 или более интерфейсов одновременно: class A implements Int1, Int2, но class A extends AbstrB

Интерфейс содержит исключительно объявления методов, но не содержит реализации. Абстрактный класс же может содержать как абстрактные методы (без реализации), так и обыкновенные.

Интерфейс не может включать в себя свойства, в абстрактном классе, как и в любом другом, свойства могут присутствовать.

Класс-потомок обязан реализовывать все методы интерфейса, методы абстрактного класса же могут в нем не присутствовать.

В интерфейсе все методы открытые, в абстактном классе они могут содержать спецификаторы доступа (private, protected, pubic).


---
- [x]	**Что такое сериализация объекта?**

процесс перевода какой-либо структуры данных в последовательность битов. Обратной к операции сериализации является операция десериализации (структуризации) — восстановление начального состояния структуры данных из битовой последовательности.

NSCoding это протокол для сериализации и десериализации данных. Реализуется двумя методами: -initWithCoder: и encodeWithCoder :. Классы, которые соответствуют NSCoding могут быть заархивированы с NSKeyedArchiver/NSKeyedUnarchiver

---
- [x]	**Что такое регулярные выражения (regular expression)?**

строка или последовательность символов, которые задают шаблон. С помощью которого можно делать очень гибкие поисковые выборки в тексте.

Допустим у нас имеется некий текст в строке и наша задача найти в нем все перечисленные email-адреса. Самый лучший инструмент для этой задачи это регулярные выражения.

egular Expressions can be used to solve almost any problem, so it’s good to know you can use them in NSPredicates as well. To use regular expressions in your NSPredicate, you need to use the MATCHES operator. Let’s filter all books that are about iPad or iPhone programming:
```
predicate = [NSPredicate predicateWithFormat:@"title MATCHES '.*(iPhone|iPad).*'"];
filtered = [bookshelf filteredArrayUsingPredicate:predicate];
NSLog(@"Books that contain 'iPad' or 'iPhone' in their title", filtered);
```
You need to obey some rules when using regular expressions in NSPredicate: most importantly, you cannot use regular expression metacharacters inside a pattern set.


---
- [x]	**Что такое перегрузка операторов (operator overloading)?**

Перегрузка операторов в программировании — один из способов реализации полиморфизма, заключающийся в возможности одновременного существования в одной области видимости нескольких различных вариантов применения оператора, имеющих одно и то же имя, но различающихся типами параметров, к которым они применяются.

Operator overloading is **NOT** a feature of Objective-C. If two instances of your classes can be added together, provide a method and allow them to be added using that method:
```
Thing *result = [thingOne thingByAddingThing:thingTwo];
Or, if your class is mutable:

[thingOne addThing:thingTwo];
```

---
- [x]	**Что такое указатель (pointer)?**

позволяют эффективно представлять сложные структуры данных, изменять значения, передаваемые в виде аргументов функциям и методам, а также проще и эффективнее работать с массивами.

Указатель(Pointer) — это средство косвенного доступа к значению определенного элемента данных. Рассмотрим, как действуют указатели. Определим переменную с именем count:
```
int count = 10;
```
С помощью следующего объявления мы можем определить другую переменную с именем intPtr для косвенного доступа к значению переменной count.
```
int * intPtr;
```
Звездочка показывает, что переменная intPtr является указателем на тип int. Это означает, что программа будет использовать intPtr для косвенного доступа к значению одной или нескольких целых переменных.

[http://c-objective.ru/objective-c-dlya-nachinayushih/ukazatelipointer-v-objective-c/]


---
- [x]	**Что такое функция (function)?**

фрагмент программного кода (подпрограмма), к которому можно обратиться из другого места программы. В большинстве случаев с функцией связывается идентификатор, но многие языки допускают и безымянные функции. С именем функции неразрывно связан адрес первой инструкции (оператора), входящей в функцию, которой передаётся управление при обращении к функции. После выполнения функции управление возвращается обратно в адрес возврата — точку программы, где данная функция была вызвана.

---
- [x]	**Как передавать переменную как ссылку?**

Objective-C only support passing parameters by value. The problem here has probably been fixed already (Since this question is more than a year old) but I need to clarify some things regarding arguments and Objective-C.

Objective-C is a strict superset of C which means that everything C does, Obj-C does it too.

By having a quick look at Wikipedia, you can see that Function parameters are always passed by value

Objective-C is no different. What's happening here is that whenever we are passing an object to a function (In this case a UILabel *), we pass the value contained at the pointer's address.

Whatever you do, it will always be the value of what you are passing. If you want to pass the value of the reference you would have to pass it a **object (Like often seen when passing NSError).

This is the same thing with scalars, they are passed by value, hence you can modify the value of the variable you received in your method and that won't change the value of the original variable that you passed to the function.

Here's an example to ease the understanding:
```
- (void)parentFunction {
    int i = 0;
    [self modifyValueOfPassedArgument:i];
    //i == 0 still!
}

- (void)modifyValueOfPassedArgument:(NSInteger)j {
    //j == 0! but j is a copied variable. It is _NOT_ i
    j = 23;
    //j now == 23, but this hasn't changed the value of i.
}
```
If you wanted to be able to modify i, you would have to pass the value of the reference by doing the following:
```
- (void)parentFunction {
    int i = 0; //Stack allocated. Kept it that way for sake of simplicity
    [self modifyValueOfPassedReference:&i];
    //i == 23!
}

- (void)modifyValueOfPassedReference:(NSInteger *)j {
    //j == 0, and this points to i! We can modify i from here.
    *j = 23;
    //j now == 23, and i also == 23!
}
```

---
- [x]	**Что такое класс?**

Класс – это способ описания сущности, определяющий состояние и поведение, зависящее от этого состояния, а также правила для взаимодействия с данной сущностью (контракт). 

С точки зрения программирования класс можно рассматривать как набор данных (полей, атрибутов, членов класса) и функций для работы с ними (методов).

С точки зрения структуры программы, класс является сложным типом данных.



---
- [x]	**Что такое объект?**

Объект (экземпляр) – это отдельный представитель класса, имеющий конкретное состояние и поведение, полностью определяемое классом.

Говоря простым языком, объект имеет конкретные значения атрибутов и методы, работающие с этими значениями на основе правил, заданных в классе. В данном примере, если класс – это некоторый абстрактный автомобиль из «мира идей», то объект – это конкретный автомобиль, стоящий у вас под окнами.


---
- [x]	**Что такое интерфейс?**

Интерфейс – это набор методов класса, доступных для использования другими классами. 

Очевидно, что интерфейсом класса будет являться набор всех его публичных методов в совокупности с набором публичных атрибутов. По сути, интерфейс специфицирует класс, чётко определяя все возможные действия над ним. 
Хорошим примером интерфейса может служить приборная панель автомобиля, которая позволяет вызвать такие методы, как увеличение скорости, торможение, поворот, переключение передач, включение фар, и т.п. То есть все действия, которые может осуществить другой класс (в нашем случае – водитель) при взаимодействии с автомобилем.

[https://habr.com/ru/post/87119/]

---
- [x]	**Когда и почему мы используем объект вместо структур?**

 И в классах и в структурах можно:

- Объявлять свойства для хранения значений
- Объявлять методы, чтобы обеспечить функциональность
- Объявлять индексы, чтобы обеспечить доступ к их значениям, через синтаксис индексов
- Объявлять инициализаторы, чтобы установить их первоначальное состояние
- Они оба могут быть расширены, чтобы расширить их функционал за пределами стандартной реализации
- Они оба могут соответствовать протоколам, для обеспечения стандартной функциональности определенного типа
Для получения дополнительной информации смотрите главы Свойства, Методы, Индексы, Инициализация, Расширения и Протоколы.

Классы имеют дополнительную возможность, которых нет у структур:

- Наследование позволяет одному классу наследовать характеристики другого
- Приведение типов позволяет проверить и интерпретировать тип экземпляра класса в процессе выполнения
- Деинициализаторы позволяют экземпляру класса освободить любые ресурсы, которые он использовал
- Подсчет ссылок допускает более чем одну ссылку на экземпляр класса. Для получения дополнительной информации смотрите Наследование, Приведение типов, Деинициализаторы и Автоматический подсчет ссылок.

Дополнительные возможности поддержки классов связаны с увеличением сложности. Лучше использовать структуры и перечисления, потому что их легче понимать. Также не забывайте про классы. На практике - большинство пользовательских типов данных, с которыми вы работаете - это структуры и перечисления. Для более детального сравнения см. раздел «Выбор между структурами и классами».



#### General

---
- [x]	**Какое ваше любимое приложение и почему?**

TODOist, отслеживания выполненных задач, всегда все помнишь.
MyFitnesPal - отслеживание питания. 

---
- [x]	**Что такое парное программирование?**

техника программирования, при которой исходный код создаётся парами людей, программирующих одну задачу, сидя за одним рабочим местом. Один программист («ведущий») управляет компьютером и, в основном, думает над кодированием в деталях. Другой программист («штурман») сосредоточен на картине в целом и непрерывно просматривает код, производимый первым программистом. Время от времени они меняются ролями, обычно, каждые полчаса.


---
- [x]	**Что такое ограничивающая рамка (bounding box)?**

Bounding Box (BBox, ограничивающий параллелепипед) — это некая простая фигура (обычно, параллелепипед), ограничивающая форму более сложной геометрической модели. Bounding Box играет роль габаритного контейнера для такой модели. Как упрощённая аппроксимация сложной фигуры, Bounding Box контейнер используются для быстрого и простого определения расположения объекта в пространстве.

Bounding Box используется для предварительной проверки в различных алгоритмах, таких как определение попадания объекта в зону видимости, пересечение объекта лучом, столкновение объектов и проч.

Как правило, объём, заданный формой Bounding Box включает в себя объект, хотя иногда может быть и наоборот.

Развитие идеи Bounding Box — это создание для фигуры не одного, а нескольких ограничивающих объёмов для лучшей её аппроксимации, или для создания нескольких уровней точности аппроксимации.

Различают ориентированные (Oriented Bounding Box, OBB) и неориентированные (Axis Aligned Bounding Box, AABB) габаритные контейнеры. Неориентированный BBox — это частный случай OBB, у которого ориентация всегда совпадает с ортами пространства. То есть положение и ориентация неориентированного Bounding Box-а задаётся координатами и размерами, а ориентированный Bounding Box может иметь ещё информацию о повороте объекта (заданного, например, углами Эйлера или кватернионом).

Хотя слово box (ящик) подразумевает параллелепипед, в качестве Bounding Box могут выступать и другие фигуры, такие как куб, сфера, капсула, пирамида (или в плоскости: квадрат, окружность, эллипс), то есть любая простая фигура. Для таких фигур могут использоваться соответствующие названия: Bounding Cube, Bounding Sphere и т.д.


---
- [x]	**Сколько есть API'шек для эффективного отслеживания местоположения?**

Core Location API?


---
- [x]	**Какое самое главное преимущество у Swift?**

Легче поддерживать, более читаемый, не требует много кода, быстрее, безопаснее, есть динамичекие библиотеки.

---
- [x]	**Что делает React Native специальным для iOS?**

[https://habr.com/ru/company/qlean/blog/416097/]

---
- [x]	**Что означает бритье Яка (Yak Shaving)?**

Yak shaving is a progranming term that refers to a series of tasks that need to be performed before a project can progress to its next milestone. This term is believed to have been coined by Carlin Vieri and was inspired by an episode of "The Ren & Stimpy Show." The term's name alludes to the seeming uselessness of the tasks being performed, even though they may be necessary to solve a larger problem. The process of complicating a simple activity also may be considered yak shaving.

---
- [x]	**Каковы пять основных практических рекомендаций для улучшения типографического качества (typographic quality) мобильных проектов?**

[https://uxdesign.cc/how-to-make-the-typography-of-your-ios-app-not-suck-a6de09fb7c41]

- Size
- Weight
- Line Height
- Characters Per Line
- Information Hierarchy


---
- [x]	**Что такое Alamofire?**

Alamofire - это HTTP сетевая библиотека на Swift для iOS и Mac OS X. Она обеспечивает элегантный интерфейс сетевого стека Foundation от Apple, упрощающего ряд общих сетевых задач.

Alamofire предоставляет цепочку методов ответ/запрос, параметр JSON и ответ сериализации, аутентификацию и многие другие функции. В этом туториале по Alamofire вы будете использовать Alamofire для выполнения основных сетевых задач, таких как загрузка файлов и запрос данных со сторонних RESTful API.


---
- [x]	**Что такое управление зависимостями (Dependency Management)?**

Сложное название для загрузки, удаления кода (библиотек) из интернета. (Cocoapods, cartage).
Каждый pod это зависимость. 

---
- [x]	**Что такое диаграммы классов UML?**

UML – унифицированный язык моделирования (Unified Modeling Language) – это система обозначений, которую можно применять для объектно-ориентированного анализа и проектирования.
Его можно использовать для визуализации, спецификации, конструирования и документирования программных систем.
Словарь UML включает три вида строительных блоков:

- Диаграммы.
- Сущности.
- Связи.

Сущности – это абстракции, которые являются основными элементами модели, связи соединяют их между собой, а диаграммы группируют представляющие интерес наборы сущностей.

Диаграмма – это графическое представление набора элементов, чаще всего изображенного в виде связного графа вершин (сущностей) и путей (связей). Язык UML включает 13 видов диаграмм, среди которых на первом месте в списке — диаграмма классов, о которой и пойдет речь.
Диаграммы классов показывают набор классов, интерфейсов, а также их связи. Диаграммы этого вида чаще всего используются для моделирования объектно-ориентированных систем. Они предназначены для статического представления системы.
Большинство элементов UML имеют уникальную и прямую графическую нотацию, которая дает визуальное представление наиболее важных аспектов элемента.

Сущности
Диаграммы классов оперируют тремя видами сущностей UML:
- Структурные.
- Поведенческие.
- Аннотирующие.
Структурные сущности – это «имена существительные» в модели UML. В основном, статические части модели, представляющие либо концептуальные, либо физические элементы. Основным видом структурной сущности в диаграммах классов является класс.
 
Поведенческие сущности – динамические части моделей UML. Это «глаголы» моделей, представляющие поведение модели во времени и пространстве. Основной из них является взаимодействие – поведение, которое заключается в обмене сообщениями между наборами объектов или ролей в определенном контексте для достижения некоторой цели. Сообщение изображается в виде линии со стрелкой, почти всегда сопровождаемой именем операции.
 
Поведенческие сущности
Аннотирующие сущности – это поясняющие части UML-моделей, иными словами, комментарии, которые можно применить для описания, выделения и пояснения любого элемента модели. Главная из аннотирующих сущностей – примечание. Это символ, служащий для описания ограничений и комментариев, относящихся к элементу либо набору элементов. Графически представлен прямоугольником с загнутым углом; внутри помещается текстовый или графический комментарий.


#### Patterns
---
- [x]	**Что такое паттерн Фабрика (Factory)?**

Используется, когда необходимо сделать выбор между классами, которые реализуют общий протокол или разделяют общий базовый класс. Шаблон содержит в себе логику, которая решает, какой класс выбрать.

Вся логика, как предполагает название шаблона, находится в методе, который инкапсулирует решение.

У нас есть два варианта реализации: глобальный метод или использование базового класса.

Глобальный метод:
```
func createRentalCar(_ passengers:Int) -> RentalCar? {
        var carImp: RentalCar.Type?
        switch passengers {
        case 0...3: carImp = Compact.self
        case 4...8: carImp = SUV.self
        default: carImp = nil
        }
        return carImp?.createRentalCar(passengers)
    }
```
Использование базового класса предполагает перенос этой логики в базовый класс.

Также известен как виртуальный конструктор — порождающий шаблон проектирования, предоставляющий подклассам интерфейс для создания экземпляров некоторого класса. В момент создания наследники могут определить, какой класс создавать. Иными словами, Фабрика делегирует создание объектов наследникам родительского класса. Это позволяет использовать в коде программы не специфические классы, а манипулировать абстрактными объектами на более высоком уровне.

---
- [x]	**Что такое паттерн Фасад (Facade)?**

Этот шаблон предлагает один интерфейс (упрощенный) для сложных систем. Вместо того, чтобы показывать пользователю целую кучу методов с разными интерфейсами, мы создаем свой класс, инкапсулируя в нем другие объекты и показываем более упрощенный интерфейс для пользователя.

Полезно в том случае, когда вам приходится заменять, например, Alamofire на NSURLSession. Вы делаете изменение только в вашем Facade-классе, не трогая его интерфейс.

Пример интерфейса — мой класс SocketManager:
```
final internal class SocketManager : NSObject {
 
    static internal let sharedInstance: SocketManager.SocketManager
 
    override internal func copy() -> Any
 
    override internal func mutableCopy() -> Any
 
    internal var connectionWasOpened: Bool
 
    internal var lastUserId: String
 
    internal func openNewConnection(userId: String)
 
    internal func closeConnection()
 
    internal func logoutFromWebSocket()
 
    internal func reconnect()
}
```
Конечный пользователь может не знать, что я использую SocketRocket. А через какое-то время я могу заменить его на что-то другое, и мне не надо будет вносить изменения во всех местах, где он был использован: достаточно будет поправить один класс.


---
- [x]	**Что такое паттерн Декоратор (Decorator)?**

Добавляет поведение и обязанности к объекту, не модифицируя его код, например, когда используем 3d party libraries и не имеем доступа к исходному коду.

В swift есть две очень распространенные реализации этого шаблона: extensions и delegation.

---
- [ ]	**Что такое паттерн Адаптер (Adapter)?**

Адаптер позволяет классам с несовместимыми интерфейсами работать вместе. Apple реализует этот шаблон с помощью protocols. Адаптер используется, когда нужно интегрировать компонент, код которого нельзя менять. Возможно, это старый legacy product (мы не знаем, как это работает, и боимся трогать).


---
- [x]	**Что такое паттерн Наблюдатель (Observer)?**

Паттерн, который помогает реагировать на изменения происходящие в объекте, – все подписанные на него объекты тут же узнают про изменение. Идея проста: объект который мы называем Subject – дает возможность другим объектам, которые реализуют интерфейс Observer, подписываться и отписываться от изменений происходящих в Subject. Когда изменение происходит – всем заинтерeсованным объектам высылается сообщение, что изменение произошло. В нашем случае – Subject – это издатель газеты, Observer это мы с вами – те кто подписывается на газету, ну и собственно изменение – это выход новой газеты, а оповещение – отправка газеты всем кто подписался.

Способы реализации паттерна Observer
Notification – механизм использования возможностей NotificationCenter самой операционной системы. Использование NSNotificationCenter позволяет объектам коммуницировать, даже не зная друг про друга. Это очень удобно использовать когда у вас в параллельном потоке пришел push-notification, или же обновилась база, и вы хотите дать об этом знать активному на даный момент View. Чтобы послать такое сообщение стоит использовать конструкцию типа:
```
NSNotification *broadCastMessage = [NSNotification notificationWithName:@"broadcastMessage" object:self];
```
Как видим мы создали объект типа NSNotification в котором мы указали имя нашего оповещения: "broadcastMessage", и собственно сообщили о нем через NotificationCenter. Чтобы подписаться на событие в объекте который заинтересован в изменении стоит использовать следующую конструкцию:
```
NSNotificationCenter * notificationCenter = [NSNotificationCenter defaultCenter];
[notificationCenter addObserver:self selector:@selector(update:) name:@"broadcastMessage" object:nil];
```
Мы подписываемся на событие и вызывается метод, который задан в свойстве selector.


---
- [x]	**Что такое паттерн Мементо (Memento)?**

Сохраняет ваши объекты. Это и UserDefaults, и Archiving с помощью NSCoding protocol, и, в принципе, тот же CoreData.


---
- [x]	**Реализация Cинглтона (Singleton) в ARC и в non-ARC?**

Существует в системе в единственном экземпляре => не может быть повторно создан. Объект, к которому обращаются много объектов. Примеры синглтонов в системе:
```
[NSUserDefaults standardUserDefaults];   
[UIApplication sharedApplication];   
[UIScreen mainScreen];   
[NSFileManager defaultManager];   
```
Минусы Singleton
- Глобальное состояние. Про вред глобальных переменных вроде бы уже все знают, но тут та же самая проблема. Когда мы получаем доступ к экземпляру класса, мы не знаем текущее состояние этого класса, и кто и когда его менял, и это состояние может быть вовсе не таким, как ожидается. Иными словами, корректность работы с синглтоном зависит от порядка обращений к нему, что вызывает неявную зависимость подсистем друг от друга и, как следствие, серьезно усложняет разработку.
- Зависимость обычного класса от синглтона не видна в публичном контракте класса. Так как обычно экземпляр синглтона не передается в параметрах метода, а получается напрямую, через GetInstance(), то для выявления зависимости класса от синглтона надо залезть в тело каждого метода — просто просмотреть публичный контракт объекта недостаточно.
- Наличие синглтона понижает тестируемость приложения в целом и классов, которые используют синглтон, в частности. Во-первых, вместо синглтона нельзя подставить Mock-объект, а во-вторых, если синглтон имеет интерфейс для изменения своего состояния, то тесты начинают зависеть друг от друга. Говоря же проще — синглтон повышает связность, и все вышеперечисленное, в том или ином виде, есть следствие повышения связности.

Представьте ситуацию, когда вам нужно иметь только одну копию объекта. Например, это может быть реальный объект: принтер, сервер или что-то, чего не должно быть несколько копий. У Apple, например, есть объект UserDefaults, доступ к нему осуществляется через свойство standard. Пример реализации:
```
private override init() {}
    static let sharedInstance = EventManager()
    override func copy() -> Any {
        fatalError("You are not allowed to use copy method on singleton!")
    }
    override func mutableCopy() -> Any {
        fatalError("You are not allowed to use copy method on singleton!")
    }
```
Для класса EventManager я сделал инициализатор private, чтобы никто не мог создать новую копию объекта. Добавил статическую переменную sharedInstance и инициализировал ее объектом EventManager(). Далее, чтобы избежать копирования объекта, я переписал методы copy() и mutableCopy(). В прежних версиях swift можно было писать dispatch_once{}, но в swift 3.0 эта функция более недоступна.


---
- [x]	**Назовите основные отличия синглтона от статического класса, и когда следует использовать один, а когда другой?**

|  List         | Singleton     | Static class |
| ------------- | ------------- | -------------|
| Количество точек доступа  | Одна (и только одна) точка доступа — статическое поле Instance  | N (зависит от количества публичных членов класса и методов) |
| Наследование классов  | Возможно, но не всегда (об этом — ниже) | Невозможно — статические классы не могут быть экземплярными, поскольку нельзя создавать экземпляры объекты статических классов |
| Наследование интерфейсов  | Возможно, безо всяких ограничений  | Невозможно по той же причине, по которой невозможно наследование классов |
| Возможность передачи в качестве параметров | Возможно, поскольку Singleton предоставляет реальный объект  | Отсутствует |
| Контроль времени жизни объекта  | Возможно — например, отложенная инициализация (или создание по требованию) | Невозможно по той же причине, по которой невозможно наследование классов |
| Использование абстрактной фабрики для создания экземпляра класса  | Возможно  | Невозможно по причине осутствия самой возможности создания экземпляра |
| Сериализация | Возможно  | Неприменима по причине отсутствия экземпляра |


#### Git
---
- [x]	**В чем разница между SVN и Git?**

1. GIT распределяется, а SVN - нет. Другими словами, если есть несколько разработчиков работающих с репозиторием у каждого на локальной машине будет ПОЛНАЯ копия этого репозитория. Разумеется есть и где-то и центральная машина, с которой можно клонировать репозиторий. Это напоминает SVN. Основной плюс в том, что если вдруг у вас нет доступа к интернету, сохраняется возможность работать с репозиторием. Потом только один раз сделать синхронизацию и все остальные разработчики получат поолную историю.

2. GIT сохраняет метаданные изменений, а SVN целые файлы. Это экономит место и время.

3. Система создания branches, versions и прочее в GIT и SVN отличаются значительно. В GIT проще переключатся с ветки на ветку, делать merge между ними. В общем GIT я нахожу немного проще и удобнее, но бывают конечно иногда сложности. Но где их не бывает?

Разумеется есть гораздо больше отличий, но я перечислил те, которые чаще всего встречаются при работе с репозиториями и на МОЙ взгляд наиболее важные.


---
- [x]	**Какая команда Git позволяет объединить коммиты?**

Чтобы объединить два или более последних коммитов в один используется команда git rebase с ключом -i (интерактивный режим).

Для примера объединим последние 2 коммита в один. Выполняем команду:
```
git rebase -i HEAD~2
```
Откроется текстовый редактор, в котором первые две строки соответствуют последним двум коммитам:
```
pick ab37583 Added feature 1.
pick 3ab2b83 Added feature 2.

# Rebase e46d230..3ab2b83 onto e46d230 (2 commands)
#
# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
...
```
В начале каждой строки стоит слово pick. Вам необходимо изменить слово pick на squash или просто на букву s у второй строки. Это означает, что данный коммит будет объединен с предыдущим коммитом. Итак, замените pick на s, у вас должно получиться что-то вроде:
```
pick ab37583 Added feature 1.
s 3ab2b83 Added feature 2.
...
```
Сохраните изменения и закройте редактор.


---
- [x]	**Какая команда Git позволяет нам найти плохие коммиты?**

Пример команды: git bisect

Команда git bisect использует принцип «разделяй и властвуй» для поиска плохого коммита в большой истории изменений.

Представьте, что вы вернулись из продолжительного отпуска. Вы забираете последнюю версию проекта из удалённого репозитория и обнаруживаете, что та фича, над которой вы работали перед самым отпуском, теперь не работает. Вы проверяете последний сделанный вами коммит — там всё работает. Однако за время вашего отпуска в проекте появились сотни других коммитов, и вы представления не имеете, который из них оказался плохим и сломал вашу фичу.

Так что же делает git bisect?

После того, как вы укажете коммит, в котором ничего не работает («плохой» коммит) и коммит, в котором всё работает («хороший» коммит), git bisect разделит все коммиты, которые располагаются между ними пополам, переключится в новую (безымянную) ветку на этом срединном коммите и позволит вам проверить, работает ли в нём ваша фича.

Предположим, в этом «срединный» коммите всё работает. Тогда вы говорите об этом гиту с помощью команды git bisect good и у вас останется только половина всех коммитов для поиска того самого, поломавшего всё.

После выполнения этой команды Git разделит оставшиеся коммиты пополам и снова переключится в безымянную ветку на новом срединном коммите, позволив вам протестировать работоспособность вашей фичи. И так далее, пока вы не обнаружите тот самый «плохой» коммит.


---
- [x]	**Какая команда Git сохраняет ваш код без коммита?**

git stash
It will save any uncommitted stuff in a special area where you can get it back later using

git stash apply
You can see what is in the stash with

git stash list



#### Additional
---
- [x]	**Что такое Б-деревья (B-Trees)?**

 структура данных, дерево поиска. С точки зрения внешнего логического представления, сбалансированное, сильно ветвистое дерево. Часто используется для хранения данных во внешней памяти.

Использование B-деревьев впервые было предложено Р. Бэйером (англ. R. Bayer) и Э. МакКрейтом (англ. E. McCreight) в 1970 году.

Сбалансированность означает, что длина любых двух путей от корня до листьев различается не более, чем на единицу.

Ветвистость дерева — это свойство каждого узла дерева ссылаться на большое число узлов-потомков.

С точки зрения физической организации B-дерево представляется как мультисписочная структура страниц памяти, то есть каждому узлу дерева соответствует блок памяти (страница). Внутренние и листовые страницы обычно имеют разную структуру.


---
- [x]	**С: Как представлены Си-структуры (CGRect, CGSize, CGPoint) в Objective-C?**

CGRect – структура, которая содержит переменные для хранения координат и размеров. frame – это прямоугольник описываемый положением location(x, y) и размерами size (width, height) вьюхи относительно ее superview в которой оа содержится. bounds – это прямоугольник описываемый положением location(x, y) и размерами size (width, height) вьюхи относительно ее собственной системы координат (0, 0).

![image](https://github.com/sashakid/ios-guide/raw/master/Images/uiview_frame.png)

[https://nshipster.com/cggeometry/]



#### Algorithms
---
- [x]	**Напишите код, который разворачивает строку на С++. (переставить символы в строке в обратном порядке)?**
```
char* rev_str(char *s1) {
    int l = strlen(s1);
    char *res = new char [l];
    for(int a = l-1, b = 0; a >= 0; a--, b++) {
        res[b] = s1[a];
    }
    return res;
}
```
а еще:
```
char s1_pr[100];
cout << "Напишите любую строку: ";
cin.getline( s1_pr, 100 );
```

---
- [x]	**Поменять местами a и b не используя промежуточную переменную?**
```
b=5;
a=a+b;
b=a-b;// здесь уже будет 3
a=a-b; // а теперь равно 5````

[или так](http://ideone.com/nEzFkn)
```


---
- [x]	**Написать функцию вычисления n-го числа последовательности фебоначи. (если решить через рекурсию, спросят чем опасно такое решение)?**
```
#include <iostream>
 
int fib(int n)
{
    switch (n)
    {
    case 0:
        return 1;
    case 1:
        return 1;
    default:
        return fib(n - 2) + fib(n - 1);
    }
}
 
int main()
{
    int n;
 
    std::cin >> n;
 
    std::cout << fib(n);;
 
    std::cin.clear();
    std::cin.ignore();
    std::cin.get();
}
```

---
- [x]	**Решить задачку о массиве: дан массив из 1001 элемента в котором присутсвуют все числа от 1 до 1000 и одно повторяется дважды, узнать какое, если к каждому элементу можно обратиться только 1 раз?**

```
int sum = 0;
for (int i=0;i<=1000;i++)
sum = sum + m[i];
cout« sum-(1+1000)*1000/2;
```

---
- [x]	**Как из строки вытащить подстроку?**

Assuming either the key or value contains a quotation mark. The following will output the value after the ":". You can also use it in a loop to repeatedly extract the value field if you have multiple key-value pairs in the input string, provided that you keep a record of the position of last found instance.
```
#include <iostream>
using namespace std;

string extractInformation(size_t p, string key, const string& theEntireString)
{
  string s = "\"" + key +"\":\"";
  auto p1 = theEntireString.find(s);
  if (string::npos != p1)
    p1 += s.size();
  auto p2 = theEntireString.find_first_of('\"',p1);
  if (string::npos != p2)
    return theEntireString.substr(p1,p2-p1);
  return "";
}

int main() {
  string data = "\"key\":\"val\" \"key1\":\"val1\"";
  string res = extractInformation(0,"key",data);
  string res1 = extractInformation(0,"key1",data);
  cout << res << "," << res1 << endl;
}
```
Outputs:
```
val,val1
```

### Logical
---
- [x]	**Есть 4 человека, каждый проходит мост за 1, 2, 5, и 10 минут соответственно. Через мост они могут переходить только парами, держась за руку и только с фонариком. Притом один должен вернуться обратно и передать фонарик. Необходимо переправить всех за 17 мин на другую сторону. Задача решаема.**

```
Самое оптимальное решение:
- 1+2 = 2 min + 1 came back = 3 min
- 3+4= 10 min + 2 came back = 12 min
- 1+2 = 2 min
-------------
3+12+2=17 min
```

#### Code Puzzels
---
- [x] **Задача про банерокрутилку**
Из заданного списка вывести поочередно, рандомно, а главное, без повторений, все его элементы.
```
import java.util.Random;
class Banner
{
	public static void main(String[] args)
	{
		Random r = new Random();
		int[] list = new int[100];// в угоду наглядности в дебри линкедлистов лезть не будем
		for (int i = 0; i < 100; ++i)
		{
			list[i] = i;
		}
		/*А теперь главное - логика алгоритма*/
		int index = 99;
		int number;
		while (index > 0)
		{
			number = r.nextInt(index);
			System.out.println(list[number]);
			list[number] = list[index];
			--index;
		}
	}
}
```
Теперь кратко и по сути, что здесь происходит. Пока в списке есть элементы, мы берем случайное число в пространстве длины массива, выводим его, потом последний элемент ставим на место только что выведенного, а индекс длины уменьшаем на единицу, пока не останется ничего.

---
- [x] **Задача на регулярное выражение**

В системе авторизации есть следующее ограничение на формат логина: он должен начинаться с латинской буквы, может состоять из латинских букв, цифр, точки и минуса и должен закан-чиваться латинской буквой или цифрой. Минимальная длина логина — 1 символ. Максимальная — 20 символов.
```
BOOL loginTester(NSString* login) {
    NSError *error = NULL;
    NSRegularExpression *regex = [NSRegularExpression regularExpressionWithPattern:@"\\A[a-zA-Z](([a-zA-Z0-9\\.\\-]{0,18}[a-zA-Z0-9])|[a-zA-Z0-9]){0,1}\\z" options:NSRegularExpressionCaseInsensitive error:&error];
    // Здесь надо бы проверить ошибки, но если регулярное выражение оттестированное и
    // не из пользовательского ввода - можно пренебречь.
    NSRange rangeOfFirstMatch = [regex rangeOfFirstMatchInString:login options:0 range:NSMakeRange(0, [login length])];
    return (BOOL)(rangeOfFirstMatch.location!=NSNotFound);
}
```
---
- [x] **Метод, возвращающий N наиболее часто встречающихся слов во входной строке**
```
- (NSArray *)mostFrequentWordsInString:(NSString *)string count:(NSUInteger)count {
    // получаем массив слов.
    // такой подход для человеческих языков будет работать хорошо.
    // для языков, вроде китайского, или когда язык заранее не известен,
    // лучше использовать enumerateSubstringsInRange с опцией NSStringEnumerationByWords
    NSMutableCharacterSet *separators = [[NSCharacterSet whitespaceAndNewlineCharacterSet] mutableCopy];
    [separators formUnionWithCharacterSet:[NSCharacterSet punctuationCharacterSet]];
    NSArray *words = [string componentsSeparatedByCharactersInSet:separators];
    NSCountedSet *set = [NSCountedSet setWithArray:words];
    // тут бы пригодился enumerateByCount, но его нет.
    // будем строить вручную
    NSMutableArray *selectedWords = [NSMutableArray arrayWithCapacity:count];
    NSMutableArray *countsOfSelectedWords = [NSMutableArray arrayWithCapacity:count];
    for (NSString *word in set) {
        NSUInteger wordCount = [set countForObject:word];
        NSNumber *countOfFirstSelectedWord = [countsOfSelectedWords count] ? [countsOfSelectedWords objectAtIndex:0] : nil; // в iOS 7 появился firstObject
        if ([selectedWords count] < count || wordCount >= [countOfFirstSelectedWord unsignedLongValue]) {
            NSNumber *wordCountNSNumber = [NSNumber numberWithUnsignedLong:wordCount];
            NSRange range = NSMakeRange(0, [countsOfSelectedWords count]);
            NSUInteger indexToInsert = [countsOfSelectedWords indexOfObject:wordCountNSNumber inSortedRange:range options:NSBinarySearchingInsertionIndex usingComparator:^(NSNumber *n1, NSNumber *n2) {
                NSUInteger _n1 = [n1 unsignedLongValue];
                NSUInteger _n2 = [n2 unsignedLongValue];
                if (_n1 == _n2)
                    return NSOrderedSame;
                else if (_n1 < _n2)
                    return NSOrderedAscending;
                else
                    return NSOrderedDescending;
            }];
            [selectedWords insertObject:word atIndex:indexToInsert];
            [countsOfSelectedWords insertObject:wordCountNSNumber atIndex:indexToInsert];
            // если слов уже есть больше чем нужно, удаляем то что с наименьшим повторением
            if ([selectedWords count] > count) {
                [selectedWords removeObjectAtIndex:0];
                [countsOfSelectedWords removeObjectAtIndex:0];
            }
        }
    }
    return [selectedWords copy];
    // можно было бы сразу вернуть selectedWords,
    // но возвращать вместо immutable класса его mutable сабклас нехорошо - может привести к багам
}
// очень интересный метод для юнитестов: правильный результат может быть разным и зависит от порядка слов в строке.
```

Я бы именно такой подход и использовал, если бы мне нужно было решить эту задачу в реальном iOS приложении, при условии, что я понимаю, откуда будут браться входные данные для поиска и предполагаю, что размеры входной строки не будут больше нескольких мегабайт. Вполне разумное допущение для iOS приложения, на мой взгляд. Иначе на входе не было бы строки, а был бы файл. При реально больших входных данных прийдется попотеть над регулярным выражением для перебора слов, чтоб избавиться от одного промежуточного массива. Такое регулярное выражение очень зависит от языка — то что сработает для русского не проканает для китайского. А вот что делать со словами дальше — кроме прямолинейного алгоритма в голову ничего не приходит. Если бы нужно было выбрать одно наиболее часто встречающееся слово — это Fast Majority Voting. Но вся красота этого алгоритма в том, что он работает для выбора одного значения. Модификаций алгоритма для выбора N значений мне не известны. Самому модифицировать не получилось.

---
- [x] **Используя NSURLConnection, напишите метод для асинхронной загрузки текстового документа по HTTP. Приведите пример его использования**
```
- (void)pullTextFromURLString:(NSString *)urlString completion:(void(^)(NSString *text))callBack {
    NSURLRequest *request = [NSURLRequest requestWithURL: [NSURL URLWithString:urlString] cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:60.0];
    [NSURLConnection sendAsynchronousRequest:request queue:[NSOperationQueue mainQueue] completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {
        if (error) {
            NSLog(@"Error %@", error.localizedDescription);
        } else {
            // вообще, не мешало бы определить кодировку, чтоб не было неприятностей
            callBack( [[NSString alloc]initWithData:data encoding:NSUTF8StringEncoding] );
        }
    }];
}
```

---
- [x] **Перечислите все проблемы, которые вы видите в приведенном ниже коде. Предложите, как их исправить**
```
NSOperation *operation = [NSBlockOperation blockOperationWithBlock:^{
    for (int i = 0; i < 1000; ++i) {
        if ([operation isCancelled]) return;
        process(data[i]);
    }
}];
[queue addOperation:operation];
```
Лично я вижу проблему в том, что переменная operation, «захваченная» блоком при создании блока, еще не проинициализирована до конца. Какое реально значение этой переменной будет в момент «захвата», зависит от того, используется ли этот код в методе класса или в простой функции. Вполне возможно, что сгенерированный код будет вполне работоспособен и так, но мне этот код не ясен. Как выйти из ситуации? Так:
```
NSBlockOperation *operation = [[NSBlockOperation alloc] init];
[operation addExecutionBlock:^{
    for (int i = 0; i < 1000; ++i) {
        if ([operation isCancelled]) return;
        process(data[i]);
    }
}];
[queue addOperation:operation];
```
Возможно, достаточно было бы просто добавить модификатор _block в объявление переменной operation. Но так, как в коде выше — наверняка. Некоторые даже создают _weak копию переменной operation и внутри блока используют ее. Хорошо подумав я решил, что в данном конкретном случае, когда известно время жизни переменной operation и блока — это излишне. Ну и я бы подумал, стоит ли использовать NSBlockOperation для таких сложных конструкций. Разумных доводов привести не могу — вопрос личных предпочтений. Что еще с этим кодом не так? Я не люблю разных магических констант в коде и вместо 1000 использовал бы define, const, sizeof или что-то в этом роде. В длинных циклах в objective-c нужно помнить об autoreleased переменных и, если такие пере-менные используются в функции или методе process, а сам этот метод или функция об этом не заботится, нужно завернуть этот вызов в @autoreleasepool {}. Создание нового пула при каждой итерации цикла может оказаться накладным или излишним. Если не используется ARC, можно создавать новый NSAutoreleasePool каждые, допустим, 10 итераций цикла. Если исполь-зуется ARC, такой возможности нет. Кстати, это, наверное, единственный довод не использо-вать ARC. По коду не ясно, меняются ли данные в процессе обработки, обращается ли кто-то еще к этим данным во время обработки из других потоков, какие используются структуры данных, заботится ли сам process о монопольном доступе к данным тогда когда это нужно. Может понадобиться позаботиться о блокировках.

Doh. Dear future googlers: of course operation is nil when copied by the block, but it doesn't have to be copied. It can be qualified with _block like so:
```
//THIS MIGHT LEAK! See the update below.
__block NSBlockOperation *operation = [NSBlockOperation blockOperationWithBlock:^{
   while( ! [operation isCancelled]){
      //do something...
   }
}];
```
UPDATE:
Upon further meditation, it occurs to me that this will create a retain cycle under ARC. In ARC, I believe _block storage is retained. If so, we're in trouble, because NSBlockOperation also keeps a strong references to the passed in block, which now has a strong reference to the operation, which has a strong reference to the passed in block, which… It's a little less elegant, but using an explicit weak reference should break the cycle:
```
NSBlockOperation *operation = [[NSBlockOperation alloc] init];
__weak NSBlockOperation *weakOperation = operation;
[operation addExecutionBlock:^{
   while(![weakOperation isCancelled]){
      //do something...
   }
}];
```

---
- [x] **Напишите запрос, возвращающий названия треков, скачанных более 1000 раз**
```
CREATE TABLE tracks (
  id INT NOT NULL AUTO_INCREMENT,
  name VARCHAR(255) NOT NULL,
  PRIMARY KEY (id)
)

CREATE TABLE track_downloads (
  download_id BIGINT(20) NOT NULL AUTO_INCREMENT,
  track_id INT NOT NULL,
  download_time TIMESTAMP NOT NULL DEFAULT 0,
  ip INT NOT NULL,
  PRIMARY KEY (download_id)
)
```
Вот такой запрос справляется с задачей замечательно:
```
select name from tracks where id in
    (select track_id from
        (select track_id, count(*) as track_download_count from track_downloads
            group by track_id order by track_download_count desc)
				where track_download_count > 1000)
```

---
- [x] **Какой метод вызовется: класса A или класса B?**
```
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
- (void)someMethod {
	NSLog(@"This is class B");
}
@end

@interface C : NSObject
@end

@implementation C
- (void)method {
	A *a = [B new];
	[a someMethod];
}
@end
```
Ответ: вызовется метод класса B.

---
- [x] **Что выведется в консоль?**
```
NSObject *object = [NSObject new];
dispatch_async(dispatch_get_main_queue(), ^ {
	NSLog(@"A %d", [object retainCount]);
	dispatch_async(dispatch_get_main_queue(), ^ {
		NSLog(@"B %d", [object retainCount]);
	});
	NSLog(@"C %d", [object retainCount]);
});
NSLog(@"D %d", [object retainCount]);
```
Ответ:
```
D 2
A 2
C 3
B 2
```

---
- [x] **Нужно сделать повторяемый таймер, который вызывается каждую минуту в бекграунде. Как это сделать?**

Пояснение к заданию: Прицельная точность тиков не важна, достаточно некая периодичность.

Решение: Если надо сделать таймер в фоне, то стоит выбирать поток с бегущим ранлупом. Либо воспользоваться уже готовым решением для GCD.
```
dispatch_source_t CreateDispatchTimer(uint64_t interval, uint64_t leeway, dispatch_queue_t queue, dispatch_block_t block) {
    dispatch_source_t timer = dispatch_source_create(DISPATCH_SOURCE_TYPE_TIMER, 0, 0, queue);
    if (timer) {
        dispatch_source_set_timer(timer, dispatch_walltime(NULL, 0), interval, leeway);
        dispatch_source_set_event_handler(timer, block);
        dispatch_resume(timer);
    }
    return timer;
}
```

---
- [x] **Что произойдет после запуска приложения?**
```
func application(_ application: UIApplication, didFinishLaunchingWithOptions...) -> Bool {
    DispatchQueue.global().async {
        Timer.scheduledTimer(timeInterval: 0.4, target: self,
	                         selector: #selector(self.tickTimer),
				 userInfo: nil, repeats: true)
    }
    return true
}

func tickTimer() {
    print("Tick-Tack")
}
```
Решение: Ничего не произойдет. Ранлуп не взведен. А еще будет небольшая утечка памяти.
Для исправления ошибки нужно выбирать бегущий ранлуп или использовать функцию sync




#### Extended questions
---
- [ ]	**Фундаментальные типы и коллекции?**

![image](https://github.com/sashakid/ios-guide/raw/master/Images/objc_collections.png)

Mutability

Most collection classes exist in two versions: mutable and immutable (default). What’s the big advantage? Thread safety. Immutable collections are fully thread safe and can be iterated from multiple threads at the same time, without any risk of mutation exceptions. Your API should never expose mutable collections. Of course there’s a cost when going from immutable and mutable and back - the object has to be copied twice, and all objects within will be retained/released. Sometimes it’s more efficient to hold an internal mutable collection and return a copied, immutable object on access. Notably, some of the more modern collection classes like NSHashTable, NSMapTable, and NSPointerArray are mutable by default and don’t have immutable counterparts. They are meant for internal class use, and a use case where you would want those immutable would be quite unusual.

Core Foundation:
CFMutableDictionary The access time for a value in the dictionary is guaranteed to be at worst O(N) for any implementation, current and future, but will often be O(1) (constant time). Insertion or deletion operations will typically be constant time as well, but are O(N^2) in the worst case in some implementations. Access of values through a key is faster than accessing values directly (if there are any such operations). Dictionaries will tend to use significantly more memory than a array with the same number of values.
CFMutableArray
CFMutableBag
CFBinaryHeap
CFMutableBitVector
CFMutableTree
CFMutableSet
Foundation:
NSArray (NSMutableArray) – управляет упорядоченной коллекцией элементов, называемой массивом. Вы можете использовать объекты этого класса для создания неизменяемых массивов. Это значит, что все элементы объектов класса NSArray доступны только для чтения. Имеется возможность доступа к элементам массива по индексу. Массивы могут хранить элементы различных типов. Массивы поддерживают сортировку и поиск элементов, а также сравнение самих массивов между собой. The most interesting part is that Apple doesn’t guarantee O(1) access time on individual object access - as you can read in the note about Computational Complexity in the CFArray.h CoreFoundation header: The access time for a value in the array is guaranteed to be at worst O(lg N) for any implementation, current and future, but will often be O(1) (constant time). Linear search operations similarly have a worst case complexity of O(Nlg N), though typically the bounds will be tighter, and so on. Insertion or deletion operations will typically be linear in the number of values in the array, but may be O(Nlg N) clearly in the worst case in some implementations. There are no favored positions within the array for performance; that is, it is not necessarily faster to access values with low indices, or to insert or delete values with high indices, or whatever.
NSPointerArray – mutable collection modeled after NSArray but it can also hold NULL values, which can be inserted or extracted (and which contribute to the object’s count). Moreover, unlike traditional arrays, you can set the count of the array directly. In a garbage collected environment, if you specify a zeroing weak memory configuration, if an element is collected it is replaced by a NULL value.
NSDictionary (NSMutableDictionary) – следует использовать когда требуется удобный и эффективный способ хранения данных, ассоциированных с ключом. Объекты класса NSDictionary позволяют хранить неизменяемые пары объектов “ключ/значение” различных типов. Ключи в словаре NSDictionary не могут дублироваться, повторение значений допускается. Типы ключей и значений могут, но не обязаны совпадать. Особенно эффективными по скорости будут операции поиска по ключу, так как словарь специально оптимизирован для них.
NSSet (NSMutableSet) – объекты представляют неупорядоченные множества различных объектов. Вы можете использовать множества в качестве альтернативы массивам, когда порядок элементов не важен, но требуется быстрое определение O(1) принадлежности объекта множеству. Операция определения принадлежности выполняется значительно быстрее в сравнении с массивами. NSSet can only work efficiently if the hashing method used is balanced; if all objects are in the same hash bucket, then NSSet is not much faster in object-existence checking than NSArray. Variants of NSSet are also NSCountedSet, and the non-toll-free counter-variant CFBag / CFMutableBag.
NSOrderedSet (NSMutableOrderedSet) – объявляет программный интерфейс для упорядоченного множества объектов. Вы задаёте записи неизменяемого множества на этапе его создания, после этого записи не могут быть изменены. Вы можете использовать упорядоченные множества как альтернативу массивам, когда порядок элементов является важным и требуется высокая скорость поиска элементов в коллекции. Класс NSMutableOrderedSet объявляет программный интерфейс к изменяемому упорядоченному множеству различных объектов. Объекты класса NSMutableOrderedSet объекты не похожи на массивы языка Си. Во время создания такого множества вы можете указать размер, но реальный размер всё равно будет равен 0.
NSCountedSet – объявляет программный интерфейс к изменяемой, неупорядоченной коллекции нечетких объектов. Счётное множество также известно как Bag. Каждый отдельный объект, вставленный в NSCountedSet, имеет счётчик, связанный с ним. Объект NSCountedSet отслеживает количество раз, когда объекты были вставлены, и требует, чтобы объекты были удалены такое же количество раз. В то же время, внутри объекта NSSet существует только один экземпляр вставляемого объекта, даже если этот объект был добавлен в множество несколько раз.
NSIndexSet (NSMutableIndexSet) – represents an immutable collection of unique unsigned integers, known as indexes because of the way they are used. This collection is referred to as an index set. You use index sets in your code to store indexes into some other data structure. For example, given an NSArray object, you could use an index set to identify a subset of objects in that array. You should not use index sets to store an arbitrary collection of integer values because index sets store indexes as sorted ranges. This makes them more efficient than storing a collection of individual integers. It also means that each index value can only appear once in the index set. The designated initializers of the NSIndexSet class are: init, initWithIndexesInRange:, and initWithIndexSet:. You must not subclass the NSIndexSet class. The mutable subclass of NSIndexSet is NSMutableIndexSet.
NSHashTable – в отличие от NSSet, поддерживает слабые ссылки. Он может содержать слабые ссылки на объекты. Объекты класса NSHashTable могут содержать произвольные указатели, хранимые объекты не ограничиваются объектами классов. Можно настроить экземпляр NSHashTable для работы с произвольными указателями, а не только с объектами классов. Благодаря своим свойствам, класс NSHashTable это не множество, потому что он может вести себя по-другому.
NSMapTable – is a general-purpose analogue of NSDictionary. Contrasted with the behavior of NSDictionary / NSMutableDictionary, NSMapTable has the following characteristics:
NSDictionary / NSMutableDictionary copies keys, and holds strong references to values.
NSMapTable is mutable, without an immutable counterpart.
NSMapTable can hold keys and values with weak references, in such a way that entries are removed when either the key or value is deallocated.
NSMapTable can copy its values on input.
NSMapTable can contain arbitrary pointers, and use pointer identity for equality and hashing checks.
Usage: Instances where one might use NSMapTable include non-copyable keys and storing weak references to keyed delegates or another kind of weak object.

10.NSIndexPath – представляет путь к конкретному узлу в виде дерева вложенных массивов коллекций. Этот путь известен как индексный путь. Каждый индекс в индексном пути представляет индекс в массиве дочерних элементов от одного узла в дереве к другому.

![image](https://github.com/sashakid/ios-guide/raw/master/Images/complexity.png)

NSArray is the best choice to use for a list of items if you're going to iterate over them in sequence, or access directly by index. They are also efficient to use as a queue or stack, as adding or removing items from either the beginning or is O(1). Checking to see if an object exists in the array using containsObject: is an O(N) operation, as it may take up to N comparisons to find the match. NSSet is a great choice for checking containsObject: due to efficient hashing algorithms. Adding/removing items is always O(1). In addition, you have fast set arithmetic operations. NSDictionary is a great choice if you have a natural key you can use to access objects. This has no inherent order, but if you know the key you can retrieve any object as O(1).

Разница между Set и Array
NSSet предназначен для создания несортированных массивов данных (например каких-либо объектов). Стоит обратить внимание, что объект, который хранится в NSSet, встречается только один раз. Т.е. все элементы NSSet — уникальные. Добавить дубликат элемента в NSMutableSet у вас также не получится. Для создания несортированного массива, в котором можно использовать неуникальные элементы, можно использовать NSCountedSet. Основным преимуществом NSCountedSet перед использованием классического массива NSArray является то, что элемент может быть продублирован огромное количество раз и при этом занимать памяти как один элемент. Это объясняется тем, что NSCountedSet хранит в памяти только одну копию элемента и запоминает сколько раз этот элемент встречается. Если для вас не важен порядок элементов внутри массива и вы используете действительно большие объемы информации, то использование NSSet повысит производительность приложения за счет снижения потребляемой памяти. Несмотря на то, что количество элементов хранящихся в памяти будет одинаковым, NSSet не тратит память на то, чтобы помнить в какой последовательности хранятся элементы.
```
NSSet *set = [NSSet setWithObjects:@"1", @"2", @"3", @"4", @"5", @"6", @"7", @"8", @"9", @"0", nil];
NSLog(@"%@", set);
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
Difference between NSArray and CFArray
What's the point of them both existing? There are a few reasons.

If you want to provide a C API, like the Carbon API, and you need things like arrays and dictionaries of referenced-counted objects, you want a library like Core Foundation (which provides CFArray), and of course it needs to have a C API.
If you want to write libraries for third-parties to use on Windows (for example), you need to provide a C API.
If you want to write a low-level library, say for interfacing with your operating system's kernel, and you want avoid the overhead of Objective-C messaging, you need a C API.
So those are good reasons for having Core Foundation, a pure C library.

But if you want to provide a higher-level, more pleasant API in Objective-C, you want Objective-C objects that represent arrays, dictionaries, reference-counted objects, and so on. So you need Foundation, which is an Objective-C library.

When should you use one or the other? Generally, you should use the Objective-C classes (e.g. NSArray) whenever you can, because the Objective-C interface is more pleasant to use: myArray.count (or [myArray count]) is easier to read and write than CFArrayGetCount(myArray). You should use the Core Foundation API only when you really need to: when you're on a platform that doesn't have Objective-C, or when you need features that the Core Foundation API provides but the Objective-C objects lack. For example, you can specify callbacks when creating a CFArray or a CFDictionary that let you store non-reference-counted objects. The NSArray and NSDictionary classes don't let you do that - they always assume you are storing reference-counted objects. Are the CF objects just legacy objects? Not at all. In fact, Nextstep existed for years with just the Objective-C Foundation library and no Core Foundation library. When Apple needed to support both the Carbon API and the Cocoa API on top of the same lower-level operating system facilities, they created Core Foundation support both.

Enumeration
Enumeration is where computation gets interesting. It's one thing to encode logic that's executed once, but applying it across a collection – that's what makes programming so powerful.

Procedural increments a pointer within a loop
Object Oriented applies a function or block to each object in a collection
Functional works through a data structure recursively
Cocoa содержит три основных способа перечисления содержимого коллекции. К ним относятся классический луп "for", быстрое перечисление и блочное перечисление. Существует также NSEnumerator класс, хотя в целом был замещен быстрым перечислением.
```
C Loops (for/while)

for and while loops are the "classic" method of iterating over a collection. Anyone who's taken Computer Science 101 has written code like this before:

for (NSUInteger i = 0; i < [array count]; i++) {
	id object = array[i];
	NSLog(@"%@", object);
}
```
But as anyone who has used C-style loops knows, this method is prone to off-by-one errors—particularly when used in a non-standard way. Fortunately, Smalltalk significantly improved this state of affairs with an idea called list comprehensions, which are commonly known today as for/in loops.

List Comprehension (for/in)

By using a higher level of abstraction, declaring the intention of iterating through all elements of a collection, not only are we less prone to error, but there's a lot less to type:

for (id object in array) {
    NSLog(@"%@", object);
}
Поведение для быстрого перечисления немного отличается в зависимости от типа коллекции. Массивы и наборы перечисляют их содержимое, а словари перечисляют свои ключи. NSIndexSet и NSIndexPath не поддерживают быстрое перечисление.
```
NSString *key;
for (key in someDictionary) {
   	NSLog(@"Key: %@, Value %@", key, [someDictionary objectForKey: key]);
}
```
In Cocoa, comprehensions are available to any class that implements the NSFastEnumeration protocol, including NSArray, NSSet, and NSDictionary. Быстрое перечисление является предпочтительным методом перечисления содержимого коллекции, поскольку оно обеспечивает следующие преимущества:

Перечисление является более эффективным, чем использование NSEnumerator напрямую.
Синтаксис краток
Перечислитель вызывает исключение, если вы измените коллекции при перечислении.
Вы можете выполнять несколько перечислений одновременно.
Блочное перечисление

Для перечисления с блоком, вызовите соответствующий метод и укажите блок для использования.
```
NSArray *anArray = [NSArray arrayWithObjects:@"A", @"B", @"D", @"M", nil];
NSString *string = @"c";
[anArray enumerateObjectsUsingBlock:^(id obj, NSUInteger index, BOOL *stop) {
	if ([obj localizedCaseInsensitiveCompare:string] == NSOrderedSame) {
		NSLog(@"Object Found: %@ at index: %i", obj, index);
		*stop = YES;
	}
}];
```
Для перечисления NSArray, параметр index полезен для одновременного перечисления. Без этого параметра, единственный способ получить доступ к индексу был бы использованием метода indexOfObject:, который является неэффективным. stop параметр важен для производительности, так как он позволяет остановить перечисление раньше, на основе некоторого условия, определяемого в пределах блока. Методы перечисления на основе блока в других коллекциях, немного отличаются по названию.

Использование перечислителя NSEnumerator

Абстрактный класс, экземпляры подклассов которого перечисляют коллекции других объектов, таких как массивы и словари. Все методы создания определены в классах коллекций, таких как NSArray, NSSet и NSDictionary, которые обеспечивают специальные объекты NSEnumerator для перечисления их содержимого. Например, класс NSArray имеет два метода, которые возвращают объект NSEnumerator: objectEnumerator и reverseObjectEnumerator. NSDictionary также имеет два метода, которые возвращают объект NSEnumerator: keyEnumerator и objectEnumerator. Эти методы позволяют перечислить содержимое словаря по ключу или по значению, соответственно. Вы отправляете nextObject, чтобы вновь созданный объект NSEnumerator возвращал следующий объект в оригинальной коллекции. Когда коллекция будет исчерпана, то возвращается nil. Вы не можете "сбросить" перечислитель после того, как он исчерпал свои коллекции. Подклассы NSEnumerator, используемые NSArray, NSDictionary и NSSet сохраняют коллекцию во время перечисления. Когда перечисление закончено, временные коллекции освобождаются. Примечание: не безопасно изменение коллекции во время её перечисления. Некоторые коллекции в настоящее время поддерживают такие операции, но такое поведение не гарантировано в будущем. Для перечисления коллекции, вы должны создать новый перечислитель:
```
NSMutableDictionary *myMutableDictionary = ... ;
NSMutableArray *keysToDeleteArray = [NSMutableArray arrayWithCapacity:[myMutableDictionary count]];
NSString *aKey;
NSEnumerator *keyEnumerator = [myMutableDictionary keyEnumerator];
while (aKey = [keyEnumerator nextObject]) {
	if (/* Проверка критериев для ключа или значения */) {
		[keysToDeleteArray addObject:aKey];
	}
}
[myMutableDictionary removeObjectsForKeys:keysToDeleteArray];
```
Array
![image](https://github.com/sashakid/ios-guide/raw/master/Images/array_performance.png)

Dictionary

![image](https://github.com/sashakid/ios-guide/raw/master/Images/dictionary_performance.png)

Why is NSFastEnumeration so slow here? Iterating the dictionary usually requires both key and object; fast enumeration can only help for the key, and we have to fetch the object every time ourselves. Using the block-based enumerateKeysAndObjectsUsingBlock: is more efficient since both objects can be more efficiently prefetched.

**Filtering**
If you look at an arbitrary code base, chances are you’ll sooner or later run into a piece of code similar to this one:
```
NSMutableArray *oldSkoolFiltered = [[NSMutableArray alloc] init];
for (Book *book in bookshelf) {
	if ([book.publisher isEqualToString:@"Apress"]) {
		[oldSkoolFiltered addObject:book];
	}
}
```
It’s a straight-forward approach to filtering an array of items (in this case, we’re talking about books) using a rather simple if-statement. Nothing wrong with this, but despite the fact we’re using a fairly simple expression here, the code is rather verbose. We can easily imagine what will happen in case we need to use more complicated selection criteria or a combination of filtering criteria.

**Simple filtering with NSPredicate**

Thanks to Cocoa, we can simplify the code by using NSPredicate. NSPredicate is the object representation of an if-statement, or, more formally, a predicate. Predicates are expressions that evaluate to a truth value, i.e. true or false. We can use them to perform validation and filtering. In Cocoa, we can use NSPredicate to evaluate single objects, filter arrays and perform queries against Core Data data sets. Let’s have a look at how our example looks like when using NSPredicate:
```
NSPredicate *predicate = [NSPredicate predicateWithFormat:@"publisher == %@", @"Apress"];
NSArray *filtered  = [bookshelf filteredArrayUsingPredicate:predicate];
```
**Filtering with Regular Expressions**

Regular Expressions can be used to solve almost any problem, so it’s good to know you can use them in NSPredicates as well. To use regular expressions in your NSPredicate, you need to use the MATCHES operator. Let’s filter all books that are about iPad or iPhone programming:
```
predicate = [NSPredicate predicateWithFormat:@"title MATCHES '.*(iPhone|iPad).*'"];
filtered = [bookshelf filteredArrayUsingPredicate:predicate];
NSLog(@"Books that contain 'iPad' or 'iPhone' in their title", filtered);
```
You need to obey some rules when using regular expressions in NSPredicate: most importantly, you cannot use regular expression metacharacters inside a pattern set.

**Filtering using set operations**

Let’s for a moment assume you want to filter all books that have been published by your favorite publishers. Using the IN operator, this is rather simple: first, we need to set up a set containing the publishers we’re interested in. Then, we can create the predicate and finally perform the filtering operation:
```
NSArray *favoritePublishers = [NSArray arrayWithObjects:@"Apress", @"O'Reilly", nil];
predicate = [NSPredicate predicateWithFormat:@"publisher IN %@", favoritePublishers];
filtered  = [bookshelf filteredArrayUsingPredicate:predicate];
NSLog(@"Books published by my favorite publishers", filtered);
```

**Advanced filtering thanks to KVC goodness**

NSPredicate relies on key-value coding to achieve its magic. On one hand this means your classes need to be KVC compliant in order to be queried using NSPredicate (at least the attributes you want to query). On the other hand, this allows us to perform some very interesting things with very little lines of code. Let’s for example retrieve a list of books written by authors with the name “Mark”:
```
predicate = [NSPredicate predicateWithFormat:@"authors.lastName CONTAINS %@", @"Mark" ];
filtered  = [bookshelf filteredArrayUsingPredicate:predicate];
```
In case we’d want to return the value of one of the aggregate functions, we don’t need to use NSPredicate itself, but instead use KVC directly. Let’s retrieve the average price of all books on our shelf:
```
NSNumber *average = [bookshelf valueForKeyPath:@"@avg.price"];
```
**Sorting
Сортировка массивов**

Вам может потребоваться разместить несколько созданных пользователем строк в алфавитном порядке, либо вам потребуется разместить номера в убыванию или по возрастанию – используйте дескрипторы блоков и селекторов. Дескрипторы сортировки (экземпляры NSSortDescriptor) обеспечивают удобный и абстрактный способ описания порядка сортировки. Простая сортировка по алфавиту:
```
NSArray *myArray = @[@"v", @"a", @"c", @"b", @"z"];
NSLog(@"%@", [myArray sortedArrayUsingSelector:@selector(caseInsensitiveCompare:)]);
sortedArrayUsingDescriptors: или sortUsingDescriptors:

//Сначала создадим массив из словарей
NSString *LAST = @"lastName";
NSString *FIRST = @"firstName";
NSMutableArray *array = [NSMutableArray array];
NSArray *sortedArray;
NSDictionary *dict;
dict = [NSDictionary dictionaryWithObjectsAndKeys:@"Jo", FIRST, @"Smith", LAST, nil];
[array addObject:dict];
dict = [NSDictionary dictionaryWithObjectsAndKeys:@"Joe", FIRST, @"Smith", LAST, nil];
[array addObject:dict];
dict = [NSDictionary dictionaryWithObjectsAndKeys:@"Joe", FIRST, @"Smythe", LAST, nil];
[array addObject:dict];
dict = [NSDictionary dictionaryWithObjectsAndKeys:@"Joanne", FIRST, @"Smith", LAST, nil];
[array addObject:dict];
dict = [NSDictionary dictionaryWithObjectsAndKeys:@"Robert", FIRST, @"Jones", LAST, nil];
[array addObject:dict];
//Далее сортируем содержимое массива по last name затем first name
//Результаты могут быть показаны пользователю
//Обратите внимание на использование localizedCaseInsensitiveCompare:selector
NSSortDescriptor *lastDescriptor = [[NSSortDescriptor alloc] initWithKey:LAST ascending:YES selector:@selector(localizedCaseInsensitiveCompare:)];
NSSortDescriptor *firstDescriptor = [[NSSortDescriptor alloc] initWithKey:FIRST ascending:YES selector:@selector(localizedCaseInsensitiveCompare:)];
NSArray *descriptors = [NSArray arrayWithObjects:lastDescriptor, firstDescriptor, nil];
sortedArray = [array sortedArrayUsingDescriptors:descriptors];
```
**Сортировка с помощью функции**

Такой подход значительно менее гибкий.
```
NSInteger lastNameFirstNameSort(id person1, id person2, void *reverse) {
NSString *name1 = [person1 valueForKey:LAST];
NSString *name2 = [person2 valueForKey:LAST];
NSComparisonResult comparison = [name1 localizedCaseInsensitiveCompare:name2];
    if (comparison == NSOrderedSame) {
        name1 = [person1 valueForKey:FIRST];
        name2 = [person2 valueForKey:FIRST];
        comparison = [name1 localizedCaseInsensitiveCompare:name2];
    }
	if (*(BOOL *)reverse == YES) {
        return 0 - comparison;
    }
    return comparison;
}
BOOL reverseSort = YES;
sortedArray = [array sortedArrayUsingFunction:lastNameFirstNameSort context:&reverseSort];
```

**Сортировка с блоками**
```
NSArray *sortedArray = [array sortedArrayUsingComparator: ^(id obj1, id obj2) {
	if ([obj1 integerValue] > [obj2 integerValue]) {
		return (NSComparisonResult)NSOrderedDescending;
	}
	if ([obj1 integerValue] < [obj2 integerValue]) {
		return (NSComparisonResult)NSOrderedAscending;
	}
	return (NSComparisonResult)NSOrderedSame;
}];
```

**Сортировка с помощью функций и селекторов**

Следующий листинг иллюстрирует использование методов sortedArrayUsingSelector:, sortedArrayUsingFunction:context:, и sortedArrayUsingFunction:context:hint:. Самым сложным из этих методов является sortedArrayUsingFunction:context:hint:. Он наиболее эффективен, когда у вас есть большой массив (N записей), которые вам надо отсортировать раз и затем лишь слегка изменить (P добавлений и удалений, где P гораздо меньше, чем N). Вы можете использовать работу, которую вы сделали в оригинальнй сортировке, и сделать своего рода слияние между N "старых" предметов и Р "новых" предметов. Чтобы получить соответствующую подсказку, вы используете sortedArrayHint когда исходный массив был отсортирован, и держите его, пока вам это нужно (если вы хотите, отсортировать массив после того, как он был изменен).
```
NSInteger alphabeticSort(id string1, id string2, void *reverse) {
	if (*(BOOL *)reverse == YES) {
		return [string2 localizedCaseInsensitiveCompare:string1];
	}
	return [string1 localizedCaseInsensitiveCompare:string2];
}
NSMutableArray *anArray = [NSMutableArray arrayWithObjects:
@"aa", @"ab", @"ac", @"ad", @"ae", @"af", @"ag", @"ah", @"ai", @"aj", @"ak", @"al", @"am", @"an", @"ao", @"ap", @"aq", @"ar", @"as", @"at", @"au", @"av", @"aw", @"ax", @"ay", @"az", @"ba", @"bb", @"bc", @"bd", @"bf", @"bg", @"bh", @"bi", @"bj", @"bk", @"bl", @"bm", @"bn", @"bo", @"bp", @"bq", @"br", @"bs", @"bt", @"bu", @"bv", @"bw", @"bx", @"by", @"bz", @"ca", @"cb", @"cc", @"cd", @"ce", @"cf", @"cg", @"ch", @"ci", @"cj", @"ck", @"cl", @"cm", @"cn", @"co", @"cp", @"cq", @"cr", @"cs", @"ct", @"cu", @"cv", @"cw", @"cx", @"cy", @"cz", nil];
// внимание: anArray отсортирован
NSData *sortedArrayHint = [anArray sortedArrayHint];
[anArray insertObject:@"be" atIndex:5];
NSArray *sortedArray;
// сортировка используя селектор
sortedArray = [anArray sortedArrayUsingSelector:@selector(localizedCaseInsensitiveCompare:)];
// сортировка используя функцию
BOOL reverseSort = NO;
sortedArray = [anArray sortedArrayUsingFunction:alphabeticSort context:&reverseSort];
// сортировка с подсказкой
sortedArray = [anArray sortedArrayUsingFunction:alphabeticSort context:&reverseSort hint:sortedArrayHint];
```
Sorting 1,000,000 elements:
```
selector: 4947.90[ms]
function: 5618.93[ms]
block: 5082.98[ms]
```
---
- [x]	**Аттрибут @UIApplicationMain ?**

Применяйте этот атрибут к классу для отображения того, что этот класс является “делегатом приложения”. Использование этого атрибута эквивалентно вызову функции UIApplicationMain и передачи в нее имени класса, в качестве имени класса-делегата.

Если вы не используете этот атрибут, то вы должны предоставить в main.swift в функцию main вызов функции UIApplicationMain(_:_:_:). Например, если ваше приложение использует пользовательский подкласс UIApplication в качестве своего основного класса, то вызывайте функцию UIApplicationMain(_:_:_:) вместо использования этого атрибута.


---
- [x]	**Что такое Bridge Header? Как использовать Objective-C код в Swift проекте?**

При добавлении файла Swift к сборке на Objective-C или файла Objective-C к сборке на Swift, Xcode предложит вам создать связующий заголовок (bridging header). Это файл .h, входящий в состав проекта. По умолчанию его имя производится от имени сборки, но вообще оно произвольное и может быть изменено, если вы аналогичным образом измените настройку «связующий заголовок» и в сборке Objective-C.
Файл .h на Objective-C будет виден для Swift при условии, что вы импортируете его (#import) в этот связующий заголовок.


---
- [x]	**Оператор guard?**

В Swift 2 было введено новое ключевое слово guard, которое позволяет улучшить ваши ранние возвраты в трех направлениях:

Делает вашу цель более очевидной: вы говорите guard, что вы хотите видеть, а не то, чего не хотите. guard создан специально для вылавливания невалидных параметров, так что каждый, кто будет читать ваш код, будет явно видеть то, что вы хотите получить.
Все опциональные значения, извлеченные при помощи оператора guard, остаются в пределах видимости даже после того, как guard завершает свою работу, так что вы можете продолжать их использовать. Это означает, что вы проводите проверку и извлечение опционального значения, а затем, просто используете его дальше.
guard позволяет сократить ваш код, что в свою очередь означает меньшее количество багов и более счастливых разработчиков.
Любые условия, которые вы бы проверили при помощи if ранее, теперь вы можете проверить при помощи guard. И вот вам в доказательство несколько примеров:
```
guard name.characters.count > 0 else { throw InputError.NameIsEmpty } guard age > 18 else { return false } guard #available(iOS 9, *) else { return } func printName() { guard let unwrappedName = name else { print("You need to provide a name.") return } print(unwrappedName) }
```
В последнем примере отображено, как можно извлечь опциональное значение, а затем, использовать его далее. Именно это достоинство guard является его главным преимуществом перед методом ранних возвратов.


---
- [x]	**Интерполяция vs конкатенация строк?**

Concatenation allows you to combine to strings together and it only works on two strings. Swift uses string interpolation to include the name of a constant or variable as a placeholder in a longer string, and to prompt Swift to replace it with the current value of that constant or variable.

An update. Not sure what earlier versions of Swift allowed, but currently you can concatenate more than 2 Strings together in the same statement:
```
let str = "Hi, my name is "

var concat = str + "2" + "3" + "4" + "5" + " works" //displays "Hi, my name is 2345 works"
```
Because both operands of + need to be Strings, you have to do a little extra work if you want to add a number to a String:
```
var concat2 = str + String(2) //displays "Hi, my name is 2"
```
Re why interpolation rather than concatenation, here's a quote from Apple's intro for interpolation: "String interpolation is a way to construct a new String value from a mix of constants, variables, literals, and expressions" In other words, you can use interpolation with numbers, booleans, etc. without first turning them into Strings, which you would have to do if you used concatenation.


---
- [ ]	**let vs var?**

The let keyword defines a constant:
```
let theAnswer = 42
```
The theAnswer cannot be changed afterwards. This is why anything weak can't be written using let. They need to change during runtime and you must be using var instead.

The var defines an ordinary variable.

What is interesting:
```
The value of a constant doesn’t need to be known at compile time, but you must assign the value exactly once.
```
Another strange feature:
```
You can use almost any character you like for constant and variable names, including Unicode characters:
```
```
let 🐶🐮 = "dogcow"
```

---
- [x]	**typealias? Создание своего собственного типа?**

Объявление псевдонима типа вводит именованный псевдоним существующего типа в программу. Объявления псевдонима типа объявляются с помощью ключевого слова typealias и имеют следующий вид:

typealias имя = существующий тип
После того, как псевдоним типа объявлен, имя псевдонима может быть использовано вместо существующего типа везде в вашей программе. Существующий тип может быть именованным типом или составным типом. Псевдонимы типов не создают новые типы, они просто позволяют имени ссылаться на существующий тип.


---
- [ ]	**nil в Swift vs nil в Objective-C? Различия?**

Swift’s nil is not the same as nil in Objective-C. In Objective-C, nil is a pointer to a non-existent object. In Swift, nil is not a pointer—it is the absence of a value of a certain type. Optionals of any type can be set to nil, not just object types

The compiler will convert Objective-C nils to Swift nils. That is why when translating from Objective-C all pointers must become optionals in Swift.

The usefulness of this knowledge is right in the description you gave:

Optionals of any type can be set to nil, not just object types

In Swift all types can be made optional and therefore nil. For example, in Objective-C you cannot make an NSInteger nil.

Technically nil is just a case in the Optional enum:
```
enum Optional<T> {
    case None
    case Some(T)
}
```
nil is just shorthand for the None case


---
- [x]	**Оператор '??'?**

объект слева == nil
то вернуть какой-то не nil объект справа




#### NEW QUESTIONS

---
- [x] **Как скопировать объект полностью в Objective-C**

Проблема копирования объектов в Objective-C
В этом примере переменная account1 является просто указателем:
```
BankAccount *account1;
BankAccount *account1 = [[BankAccount alloc] init];
```
Такой код, вместо копирования объекта скопирует его адрес в памяти:
```
BankAccount *account2;
account2 = account1;
```
Обе переменные будут указывать на один и тот же объект.

Решение 1 - поверхностное копирование (shallow copies)
Большинство классов унаследованы от базового класса NSObject. Преимущество такого наследования в том что наследуются методы для создания, управления, и манипулирования объектами. Два таких метода copy и mutableCopy. Эти методы используют <NSCopying> Protocol. Протокол определяет что должно быть реализовано в объекте чтобы он был копируемым этими методами. 

Классы из Foundation Framework уже обычно работают по <NSCopying> Protocol. Поэтому для них можно вызывать copy и mutableCopy методы:

```
NSString *myString1 = @"Hello";
NSString *myString2;
myString2 = [myString1 mutableCopy];
```

Таким образом мы получили изменяемую копию объекта myString1.

Если не реализовать протокол <NSCopying> то при вызове методов copy и mutableCopy будет появляться ошибка:

```
*** -[BankAccount copyWithZone:]: unrecognized selector sent to instance 0x1034f0
*** Terminating app due to uncaught exception 'NSInvalidArgumentException', reason: '*** [BankAccount copyWithZone:]: unrecognized selector sent to instance 0x1034f0'
```

Это потому что эти методы вызывают метод copyWithZone.

Как должна выглядеть @interface секция:

```
@interface BankAccount: NSObject <NSCopying>
{
        double accountBalance;
        long accountNumber;
}
-(void) setAccount: (long) y andBalance: (double) x;
-(double) getAccountBalance;
-(long) getAccountNumber;
-(void) setAccountBalance: (double) x;
-(void) setAccountNumber: (long) y;
-(void) displayAccountInfo;
-(id) copyWithZone: (NSZone *) zone;
@end
```
Как должна выглядеть @implementation секция:

```
-(id) copyWithZone: (NSZone *) zone
{
 BankAccount *accountCopy = [[BankAccount allocWithZone: zone] init];
 [accountCopy setAccount: accountNumber andBalance: accountBalance];
 return accountCopy;
}

Пример:
int main (int argc, const char * argv[])
{
    @autoreleasepool {

        BankAccount *account1;
        BankAccount *account2;

        account1 = [BankAccount alloc];

        account1 = [account1 init];

        [account1 setAccountBalance: 1500.53];
        [account1 setAccountNumber: 34543212];

        [account1 displayAccountInfo];

        account2 = [account1 copy];

        [account2 displayAccountInfo];

    }
    return 0;
}
```

Проблема поверхностного копирования

```
NSArray *myArray1;
NSArray *myArray2;
NSMutableString *tmpStr;
NSMutableString *string1;
NSMutableString *string2;
NSMutableString *string3;
string1 = [NSMutableString stringWithString: @"Red"];
string2 = [NSMutableString stringWithString: @"Green"];
string3 = [NSMutableString stringWithString: @"Blue"];
myArray1 = [NSMutableArray arrayWithObjects: string1, string2, string3, nil];

myArray2 = [myArray1 copy];

tmpStr = [myArray1 objectAtIndex: 0];
[tmpStr setString: @"Yellow"];
NSLog (@"First element of myArray2 = %@", [myArray2 objectAtIndex: 0]); // First element of myArray2 = Yellow
```

Решение 2 - глубокое копирование (deep copy)

```
NSArray *myArray1;
NSArray *myArray2;
NSMutableString *tmpStr;
NSMutableString *string1;
NSMutableString *string2;
NSMutableString *string3;
NSData *buffer;


string1 = [NSMutableString stringWithString: @"Red"];
string2 = [NSMutableString stringWithString: @"Green"];
string3 = [NSMutableString stringWithString: @"Blue"];

myArray1 = [NSMutableArray arrayWithObjects: string1, string2, string3, nil];

buffer = [NSKeyedArchiver archivedDataWithRootObject: myArray1];
myArray2 = [NSKeyedUnarchiver unarchiveObjectWithData: buffer];

tmpStr = [myArray1 objectAtIndex: 0];

[tmpStr setString: @"Yellow"];

NSLog (@"First element of myArray1 = %@", [myArray1 objectAtIndex: 0]); // First element of myArray1 = Yellow
NSLog (@"First element of myArray2 = %@", [myArray2 objectAtIndex: 0]); // First element of myArray2 = Red
```


---
- [x] **Интерфейсы vs. классы?**

Обсуждая с различными людьми — в большинстве своём опытными разработчиками — классический труд «Приёмы объектно-ориентированного проектирования. Паттерны проектирования» Гаммы, Хелма и др., я с изумлением встретил полное непонимание одного из базовых подходов ООП — различия классов и интерфейсов.



Авторам книги этот вопрос кажется настолько прозрачным, что они посвящают ему едва две страницы, предполагая, что читателям всё это и так должно быть очевидно. И, действительно, это не вызвало у меня никаких вопросов — это казалось настолько само собой разумеющимся, что, когда я некоторое время назад встретил у сразу нескольких программистов откровенное непонимание концепции, я даже не смог найти слов, чтобы объяснить её суть.

Поэтому я попытался систематизировать своё понимание вопроса в этой заметке.

Главное отличие класса от интерфейса — в том, что класс состоит из интерфейса и реализации.

Любой класс всегда неявно объявляет свой интерфейс — то, что доступно при использовании класса извне. Если у нас есть класс Ключ и у него публичный метод Открыть, который вызывает приватные методы Вставить, Повернуть и Вынуть, то интерфейс класса Ключ состоит из метода Открыть. Когда мы унаследуем какой-то класс от класса Ключ, он унаследует этот интерфейс.

Кроме этого интерфейса, у класса есть также реализация — методы Вставить, Повернуть, Вынуть и их вызов в методе Открыть. Наследники Ключа наследуют вместе с интерфейсом и реализацию.

И вот здесь таятся проблемы. Предположим, у нас есть некая модель, которая предполагает использование ключа для открытия двери. Она знает интерфейс Ключа и поэтому вызывает метод Открыть.

Но, предположим, некоторые двери открываются не таким вот поворотным ключом, а магнитной карточкой — которая ведь тоже по своей сути ключ! Интерфейс этой карточки никак принципиально не отличается от интерфейса обычного ключа — можно Открыть ключом, а можно Открыть карточкой.

И мы хотим сделать класс Магнитную Карточку, который тоже будет содержать интерфейс Ключа. Для этого мы унаследуем Магнитную Карточку от Ключа. Но вместе с интерфейсом унаследовалась и реализация, которая в методе Открыть вызывает методы Вставить, Повернуть и Вынуть — а это совершенно не подходит для Магнитной Карточки.

Нам придётся самое меньшее перегружать в Магнитной Карточке реализацию метода Открыть, используя уже последовательность Вставить, Провести и Вынуть. Это уже плохо, потому что мы не знаем детали реализации класса Ключ — вдруг мы упустили какое-то очень важное изменение данных, которое должно было быть сделано — и было сделано в методе Ключ:: Открыть? Нам придётся лезть во внутреннюю реализацию Ключа и смотреть, что и как — даже если у нас есть такая техническая возможность (open source навсегда и так далее), это грубое нарушение инкапсуляции, которое ни к чему хорошему не приведёт.

Именно так и пишут Гамма и др.: наследование является нарушением инкапсуляции.

Можете попробовать самостоятельно поразмышлять над такими вопросами:
— Что делать с тем фактом, что Ключ вставляется просто в скважину, а Магнитная Карточка — обязательно сверху (не посередине и не снизу)?
— Что делать, когда нам понадобиться сделать Бесконтактную Карточку, которую надо не вставлять, а подносить?

Правильный подход к данному вопросу — отделить интерфейс от реализации. Во многих современных языках для этого предусмотрен специальный синтаксис. В отсталых языках можно использовать хаки, например, в Цэ-два-креста — чисто абстрактные классы и множественное наследование (извините за обсценную лексику — из песни слова не выкинешь).

Мы должны опираться на интерфейсы, а не классы.

Объявим интерфейс Ключ, содержащий метод Открыть.

Объявим класс Поворотный Ключ, реализующий интерфейс Ключ при помощи своих методов Вставить, Повернуть и Вынуть.

Объявим класс Магнитная Карточка, тоже реализующий интерфейс Ключ, но уже по-своему — и без каких-либо неприятных пересечений с реализацией Поворотного Ключа. Этого помогло нам достичь отделение интерфейса от реализации.

Всегда помните об общем принципе: нам нужно использовать не класс, а интерфейс. Нам не важно, что это за штука — поворотный ключ или магнитная карточка, — нам важно, что им можно открыть дверь. То есть, вместо того, чтобы задумываться о природе объекта, мы задумываемся о способах его использования.

Вам может показаться странным, но это именно то, что отличает человека от животного — использование интерфейсов вместо классов. Вы наверняка помните классический опыт с обезьяной, которую приучили гасить огонь водой из ведёрка; а потом поставили ведёрко на плот посреди бассейна, но обезьяна всё равно бегала по мостику на плот и черпала воду из ведёрка, вместо того, чтобы черпать воду прямо из бассейна. То есть обезьянка использовала класс Вода-в-Ведёрке вместо интерфейса Вода (и даже больше, скажу по секрету: вместо интерфейса Средство-для-Тушения).

Когда мы мыслим классами — уподобляемся животным. Люди мыслят (и программируют) интерфейсами.

Использование интерфейсов даёт большие возможности. Например, класс может реализовывать несколько интерфейсов: класс Ключ-от-Домофона может содержать интерфейсы Ключ и Брелок.

Что касается наследования классов, которое, как вы помните, нарушает инкапсуляцию — часто вместо наследования лучше использовать делегирование и композицию. Не забыли Бесконтактную Карточку? Так хочется сделать её родственной Магнитной Карточке! (Например, чтобы знать, что их обе можно положить в Отделение-для-Карточек в Бумажнике.) Однако у них, кажется, нет ничего общего: интерфейс Ключ их роднит в той же мере, что и Поворотный Ключ с Ключом-от-Домофона.

Решение? Делаем класс (а может, и интерфейс — подумайте, что здесь подойдёт лучше) Карточка, реализующий интерфейс Ключ за счёт делегирования Магнитной Карточке либо же Бесконтактной Карточке. А чтобы узнать, что такое делегирование и композиция, а так же при чём тут абстрактная фабрика — обратитесь к книгам, посвящённым паттернам проектирования.

У использования интерфейсов вместо классов есть ещё много преимуществ. Вы сами сможете увидеть их на практике.


---
- [x] **Что происходит со способом позже того, как он не нашелся в объекте класса, которому его вызвали?**

Вылет из приложения с NSUnknownException.

---
- [x] **Чем категория отличается от extention (неименованная категория)?**

The main difference is that with an extension, the compiler will expect you to implement the methods within your main @implementation, whereas with a category you have a separate @implementation block. So you should pretty much only use an extension at the top of your main .m file (the only place you should care about ivars, incidentally) -- it's meant to be just that, an extension.

---
- [ ] **Когда применять категорию, а когда наследование?**

Категория используется чтобы не плодить классы, только добавить нужный метод. 
(убрать в категорию анимацию для контроллера).

Наследование используется для переопределения существующих методов. 
(рутовый класс для контроллера).


---
- [ ] **Когда необходимо копировать блок? Кто за это ответственен: caller либо reciever?**

Blocks “just work” when you pass blocks up the stack in ARC mode, such as in a return. You don’t have to call Block Copy any more. You still need to use [^{} copy] when passing “down” the stack into arrayWithObjects: and other methods that do a retain.

it's no longer necessary to manually call copy on a block when adding it to a container. The lack of automatic copy in this case has been considered a compiler bug and fixed in llvm long time ago.

"We consider this to be a compiler bug, and it has been fixed for months in the open-source clang repository."
```
- (NSArray *)blocks {
    NSMutableArray *array = [NSMutableArray array];
    for (NSInteger i = 0; i < 3; i++) {
        [array addObject:^{ return i; }];
    }
    return array;
}

- (void)test {
    for (NSInteger (^block)() in [self blocks]) {
        NSLog(@"%li", block());
    }
}
```
The above example correctly prints
```
0
1
2
```
under ARC and it crashes with EXC_BAD_ACCESS in MRC.

Note that this is - finally - coherent with the llvm documentation, which states

whenever these semantics call for retaining a value of block-pointer type, it has the effect of a Block_copy

meaning that whenever ARC has to retain a pointer and this pointer happens to be a block-pointer type, Block_copy will be called instead of retain.


---
- [x] **Что такое Run Loop?**

A run loop is an abstraction that (among other things) provides a mechanism to handle system input sources (sockets, ports, files, keyboard, mouse, timers, etc).

Each NSThread has its own run loop, which can be accessed via the currentRunLoop method.

In general, you do not need to access the run loop directly, though there are some (networking) components that may allow you to specify which run loop they will use for I/O processing.

A run loop for a given thread will wait until one or more of its input sources has some data or event, then fire the appropriate input handler(s) to process each input source that is "ready.".

After doing so, it will then return to its loop, processing input from various sources, and "sleeping" if there is no work to do.

That's a pretty high level description (trying to avoid too many details).

An attempt to address the comment. I broke it into pieces.

it means that i can only access/run to run loop inside the thread right?
Indeed. NSRunLoop is not thread safe, and should only be accessed from the context of the thread that is running the loop.

is there any simple example how to add event to run loop?
If you want to monitor a port, you would just add that port to the run loop, and then the run loop would watch that port for activity.
```
- (void)addPort:(NSPort *)aPort forMode:(NSString *)mode
```
You can also add a timer explicitly with

```
- (void)addTimer:(NSTimer *)aTimer forMode:(NSString *)mode
```
what means it will then return to its loop?

The run loop will process all ready events each iteration (according to its mode). You will need to look at the documentation to discover about run modes, as that's a bit beyond the scope of a general answer.

is run loop inactive when i start the thread?
In most applications, the main run loop will run automatically. However, you are responsible for starting the run loop and responding to incoming events for threads you spin.

is it possible to add some events to Thread run loop outside the thread?
I am not sure what you mean here. You don't add events to the run loop. You add input sources and timer sources (from the thread that owns the run loop). The run loop then watches them for activity. You can, of course, provide data input from other threads and processes, but input will be processed by the run loop that is monitoring those sources on the thread that is running the run loop.

does it mean that sometimes i can use run loop to block thread for a time
Indeed. In fact, a run loop will "stay" in an event handler until that event handler has returned. You can see this in any app simply enough. Install a handler for any IO action (e.g., button press) that sleeps. You will block the main run loop (and the whole UI) until that method completes.

The same applies to any run loop.

I suggest you read the following documentation on run loops:

[https://developer.apple.com/documentation/foundation/nsrunloop]

and how they are used within threads:

[https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW1]


---
- [ ] **Как работают push нотификации?**

iOS-приложения не могут долгое время находиться в фоновом режиме. В целях сохранения заряда батареи приложениям, работающим в фоне, разрешено выполнять ограниченный набор действий.

Но что если происходит что-то интересное и вы хотите сообщить об этом пользователям, даже если ваше приложение у них не запущено?

Например, пользователь получил ответ в Твиттере, или его любимая команда выиграла игру, или его обед готов. Так как приложение не запущено, оно не может проверить и получить эти данные.

К счастью, корпорация Apple предусмотрела решение  этой проблемы. Вместо того, чтобы беспрерывно проверять события или производить какие-либо действия в фоновом режиме, вы можете создать серверную сторону приложения, которая будет выполнять эти действия.

А когда наступит интересующее событие, серверная сторона сможет отправить приложению push-уведомление! Абсолютно любое push-уведомление может выполнять следующие три действия:

Показать короткое текстовое сообщение.
Воспроизвести короткий звуковой сигнал.
Установить число на бейдже иконки приложения.

Вы можете комбинировать эти действия, как считаете нужным; например, воспроизвести звук и установить число на бейдже, но не показывать сообщение.


**Краткий обзор**

Схема работы механизма push-уведомлений:
![image](https://habrastorage.org/storage2/e96/ff8/b76/e96ff8b764d4114090f84d979aeb070c.png)

После установки приложения появится всплывающее сообщение с подтверждением принятия push-уведомлений.

iOS запрашивает у сервера Apple Push Notification Service (APNS) токен девайса.
Приложение получает токен девайса. Можно считать, что токен – это адрес для отправки push-уведомлений.
Приложение отправляет токен девайса на ваш сервер.
Когда произойдёт какое-либо событие для вашего приложения, сервер отправит push-уведомление в APNS.
APNS отправит push-уведомление на девайс пользователя.

Когда пользователь получит push-уведомление, появится сообщение, и/или будет воспроизведён звуковой сигнал, и/или обновится бейдж на иконке приложения. Пользователь может открыть приложение из уведомления. Приложение получит контент push-уведомления и сможет обработать его.

Стоит ли по-прежнему использовать push-уведомления, если уже в iOS 4.0 появились локальные уведомления и мультизадачность? Ещё бы!

Локальные уведомления — это ограниченные по времени события. Только VOIP-приложения, навигация и фоновое воспроизведения звука обладают возможностью неограниченного фонового выполнения. Если необходимо уведомить пользователей приложения (пока оно закрыто) о каком-либо внешнем событии, вы всё ещё должны использовать push-уведомления.

В этом руководстве будет детально описана работа системы push-уведомлений и как её интегрировать в своё приложение.

Что необходимо для push-уведомлений

Для интеграции push-уведомлений в приложение необходимо:

iPhone, iPad или iPod touch. Push-уведомления не работают в симуляторе, поэтому для тестирования нужен девайс.

Регистрация в iOS Developer Program. Для каждого приложения, в котором будет интегрирован механизм push-уведомлений, необходимо создать новый App ID и provisioning profile, а также SSL-сертификат для сервера. Эти действия выполняются на iOS Provisioning Portal.

Если хотите полностью выполнять примеры из этого руководства, вам необходимо создать provisioning profile и SSL-сертификат. Я в деталях объясню, как это сделать.

Сервер, подключенный к интернету. Push-уведомления всегда отправляются сервером. В процессе разработки вы можете использовать ваш собственный Мак в качестве сервера, но для релиза нужно что-то наподобие VPS (Virtual Private Server).

Для работы с push-уведомлениями дешёвого виртуального хостинга недостаточно. Вам необходимо запустить фоновое выполнение на сервере, установить SSL-сертификат, настроить исходящее TLS-соединение на определённых портах. Большинство провайдеров виртуального хостинга не позволят вам это сделать. Хотя если обратиться в службу технической поддержки, то вам, скорее всего, помогут решить все проблемы. Но всё же я настоятельно рекомендую использовать VPS.

Анатомия push-уведомлений

Ваш сервер ответственный за создание сообщений для push-уведомлений. Поэтому полезно знать, как эти сообщения выглядят.

Push-уведомление — это короткое сообщение, состоящее из токена девайса, полезной нагрузки (payload) и ещё некоторой информации. Полезная нагрузка — это актуальные данные, которые будут отправляться на девайс.

Ваш сервер должен преобразовать полезную нагрузку в JSON-словарь. Полезная нагрузка для простого push-сообщения выглядит следующим образом:

{
   "aps":
   {
      "alert": "Hello, world!",
      "sound": "default"
   }
}

Блок, ограниченный фигурными скобками содержит словарь, который состоит из пар «ключ-значение» (так же, как и в NSDictionary).

Полезная нагрузка — это словарь, который состоит из, по крайней мере, одной пары «ключ-значение» «aps», значение которой само по себе является словарём. В примере выше «aps» содержит два поля: «alert» и «sound». Когда на девайс придёт push-уведомление, отобразится всплывающее сообщение с текстом «Hello, world!» и будет воспроизведён стандартный звуковой сигнал.

Кроме этого, в «aps» можно добавить и другие поля для настройки уведомления. Например:

{
   "aps":
   {
      "alert":
      {
         "action-loc-key": "Open",
         "body": "Hello, world!"
      },
      "badge": 2
   }
}

Теперь значение поля «alert» — это словарь. «action-loc-key» содержит альтернативный текст для кнопки «Запустить». Поле «badge» содержит число, которое будет отображено на бейдже иконки приложения. Push-уведомление не будет сопровождаться звуковым сигналом.

Есть довольно много способов формирования JSON полезной нагрузки. Вы можете изменить звуковой сигнал уведомления, добавить свои собственные поля. Дополнительную информацию можно найти на странице «Local and Push Notification Programming Guide» сайта разработчиков Apple.

Push-уведомления — это нечто довольно маленькое; размер полезной нагрузки не может превышать 256 байт. Это примерно столько же, сколько позволяет вместить в себя СМС или твит. Push-сервер не будет тратиться на переносы на новую строку и пробелы, а сгенерирует что-то наподобие этого:

{"aps":{"alert":"Hello, world!","sound":"default"}}

Такое представление менее читаемо, однако предоставляет достаточно места для более ценной информации. APNS отклонит push-уведомления, чьи размеры превышают 256 байт.

Понимание push-уведомлений

Они не надёжны! Нет гарантий, что push-уведомления будут доставлены, даже если APNS примет их.

Как только ваш сервер сформировал push-уведомление, он безответно отправляет его в APNS. Нет способа узнать статус доставки уведомления конечному пользователю после отправки. Время доставки может варьироваться от нескольких секунд до получаса.

Кроме этого, у пользователей i-девайсов может не быть возможности получать push-уведомления всё время. Например, рядом нет Wi-Fi сети с доступом в интернет либо девайс может быть вообще выключен.

APNS будет пытаться доставить последнее отправленное уведомление, когда девайс станет доступен для приёма. Но эти попытки ограничены по времени. После тайм-аута push-уведомление будет потеряно навсегда!

Они могут быть дорогими! Добавить push-функционал в приложение довольно просто и недорого, если вы владеете данными. Однако если у вас много пользователей либо необходимо запрашивать данные, то затраты резко возрастают.

К примеру, вы без проблем сможете оповестить пользователей об изменении RSS-ленты, потому что вы контролируете ленту и знаете, когда будут внесены изменения — когда обновится контент на сайте — ваш сервер мгновенно отправит уведомление.

Но что если ваше приложение — это RSS-читалка, позволяющая пользователям вносить URL-адреса своих лент? В этом случае вам необходимо придумать механизм слежения за обновлением добавленных лент.

На практике это означает, что вашему серверу нужно постоянно проверять ленты на изменение. Если у вас много пользователей, то возможно, придётся установить дополнительные сервера для обработки всех процессов и поддержки стабильной пропускной способности. Для таких приложений, как RSS-читалка, реализация push-функционала может стать довольно затратной и не представлять ценности для вас.

Ладно, хватит теории. Настало время изучить процесс реализации всех этих push-вещей. Но до того, как приступать к самому «вкусному» — программированию! — нужно выполнить несколько скучных настроек на iOS Provisioning Portal. Что ж, давайте сделаем их настолько быстро, насколько это возможно.

Provisioning Profiles и Сертификаты

Для того чтобы подключить push-уведомления к приложению, необходимо подписать его правильно сконфигурированным provisioning profile. Кроме этого, вашему серверу необходимо соединиться с APNS при помощи SSL-сертификата.

Provisioning profile и SSL-сертификат тесно связаны друг с другом и действительны только для одного App ID. Это защита, гарантирующая, что только ваш сервер может отправлять push-уведомления пользователям вашего же приложения.

Как вы знаете, для разработки и релиза приложения используют разные provisioning profiles. Есть два типа push-сертификатов для сервера:

Разработка (Development). Если приложение работает в режиме отладки и подписано provisioning profile для разработки (Code Signing Identity — «iPhone Developer»).
Релиз (Production). Приложения, созданные как Ad-Hoc или подготовленные для загрузки на App Store (Code Signing Identify — «iPhone Distribution») должны сообщить серверу, что используют сертификат для релиза (Production certificate). Если между ними будут несоответствия, то push-уведомления не смогут приходить в ваше приложение.

---
- [x] **Какой контент лучше беречь в Documents, а какой в Cache?**


Кеш - это специальный буфер (контейнер), содержащий информацию. Эта информация может быть запрошена с наибольшей вероятностью. Соответственно, доступ к этому буферу должен быть очень быстрым, он должен быть быстрее чем доступ к сети или к данным на жестком диске. В операционной системе iOS присутствует функция кэширования, но прямого доступа к данным в кэше нету. Для получения доступа следует использовать класс NSCache.

Только документы и другие данные, созданные пользователем или не могут быть повторно созданы вашим приложением, должны храниться в каталоге  / Documents и автоматически создаваться резервными копиями iCloud.

Данные, которые могут быть загружены заново или регенерированы, должны храниться в каталоге  / Library / Caches. Примеры файлов, которые вы должны поместить в каталог Caches, включают файлы кэша базы данных и загружаемый контент, например, используемый журналами, газетами и приложениями для карт.

Данные, которые используются только временно, должны храниться в каталоге  / tmp. Хотя эти файлы не скопированы в iCloud, не забудьте удалить эти файлы, когда вы закончите с ними, чтобы они не продолжали потреблять пространство на устройстве пользователя.

Используйте атрибут «не создавать резервную копию» для указания файлов, которые должны оставаться на устройстве, даже в ситуациях с низким объемом памяти. Используйте этот атрибут с данными, которые можно воссоздать, но для сохранения работоспособности вашего приложения даже в ситуациях с низким объемом хранения его необходимо сохранять или потому, что клиенты ожидают его доступности в автономном режиме. Этот атрибут работает с отмеченными файлами независимо от того, в каком каталоге они находятся, включая каталог Документы. Эти файлы не будут удалены и не будут включены в iCloud или iTunes. Поскольку эти файлы используют пространство на устройстве, ваше приложение отвечает за периодический мониторинг и очистку этих файлов.  	


---
- [x] **Для чего нужны SizeClasses.**

Итак, для чего же предназначена Size Classes? Все мы знаем, что на подходе уже iPhone 6 двумя (как минимум) разными размерами дисплея (4,7 и 5,5), после чего разработчикам еще больше придется заморачиваться с версткой UI для них + само собой расширения iPad«ов. В итоге количество всех поддерживаемых экранов будет около 7 (маленький привет Android). Герой сегодняшнего дня — Size Classes — как раз и предназначен для того, что бы помочь решить данную проблему.

Создадим тестовый пример в Xcode 6. Выбирем SingleViewApplication, в пункте Devices ставим Universal:

![image](https://habrastorage.org/files/744/a11/28f/744a1128f1f14be484e634d03c9c922c.png)

Далее выбираем наш Main.storyboard и в вкладке File Inspector ставим галочку напротив Use Size Classes (если она не выбрана по умолчанию):

![image](https://habrastorage.org/files/3e0/c8e/d83/3e0c8ed83d98495caad192f26a6fca6a.png)

Теперь обратим внимание на наш storyboard, он не совсем такой, каким мы привыкли его видеть:

![image](https://habrastorage.org/files/dc3/221/b82/dc3221b827a14cd892ced8dda547cac5.png)

После того, как был выбран Use Size Classes, ViewController принял некий абстрактный (базовый) размер, с обозначением wAny hAny (любая высота, любая ширина) о которой поговорим чуть дальше.

Немного теории. Каждый созданый ViewController имеет свой UITraitCollection объект (предоставляющий подробную информацию о характеристиках ViewController, доступен с iOS 8, подробней можно почитать тут), который в свою очередь имеет 2 Size Class: горизонтальный и вертикальный. Каждый Size Class может иметь 3 возможных значения: компактный (compact), обычный (regular) и любой (any). Эти значения изменяются в зависимости от устройства, а также его ориентации. Тоесть, приложение будет иметь свой отдельный интерфейс для каждого ViewController основываясь на текущих Size Class. 

Возвращаясь к нашему wAny hAny, что же это? Это не что иное как сетка выбора конфигурации, для работы с определенным типом устройства, которая выглядит следующим образом:

![image](https://habrastorage.org/files/6bd/76e/025/6bd76e02519f4ce99245b460296a5546.png)

При наведении курсора на сетку Apple дает подсказки разработчику при каких размерах какие устройства имеются ввиду. Общая картина сетки выглядит таким образом:

Базовые значения (any Width | any Height):

![image](https://habrastorage.org/files/6bd/76e/025/6bd76e02519f4ce99245b460296a5546.png)

iPhone ландшафтный режим (compact Width | compact Height):

![image](https://habrastorage.org/files/40a/ee2/891/40aee2891398431ba3e25d717b22f451.png)

iPhone портретный режим (compact Width | regular Height):

![image](https://habrastorage.org/files/e3c/160/db9/e3c160db952447b08e159427b9261d9d.png)

iPad ландшафтный и портретный режим (regular Width | regular Height):

![image](https://habrastorage.org/files/24b/297/3a6/24b2973a626c4d67b1e383c0a80ba086.png)

Нужно обратить внимание на зеленые точки посредине каждого квадрата сетки. Они показывают к каким устройствам будут применены изменения, которые вы делаете в данной конфигурации. Например, при базовых значениях (any Width | any Height) точки показывают разработчику что изменения будут применятся ко всем типам устройств. По этой причине Apple, рекомендует делать большую часть работы с интерфейсом именно в этой конфигурации.

Вернемся к нашему тестовому примеру. Как было сказано выше сейчас у нашего ViewController базовый Size Class (any Width | any Height). Добавим на наш ViewController 3 простых View размером 100 на 100, окрасив их в зеленый цвет, также добавим UILabel и UIButton шириной 600 и высотой 50 также окрасив их для наглядности: 

![image](https://habrastorage.org/files/9ab/234/d20/9ab234d2005a4bbd9ac20a01ff22121d.png)

Добавим Constraints:
кнопке — центрирование по вертикали и горизонтали, а также Height и Wight. 
трем View — Height и Wight, Horizontal space и Vertical Space. Тоже самое проделаем и с UILabel. 

В итоге наш ViewController вместе с Constraints будет иметь следующий вид: 

![image](https://habrastorage.org/files/5a5/afc/89c/5a5afc89c3984455bb46c8342b8cc6c0.png)

Запустим наше приложение на выполнение на iPhone 5 portrait и landscape:

![image](https://habrastorage.org/files/7e8/d77/e41/7e8d77e412c5461e83929a3213dfaa0c.png)
![image](https://habrastorage.org/files/211/d6a/9a5/211d6a9a5ef4479e905a9381f1521159.png)
и iPad 2:
![image](https://habrastorage.org/files/9f6/39a/4d9/9f639a4d9af04420bd4c2de2061d3ce0.png)

Как видим результат не очень утешительный, на iPhone 5 portrait две вью вылезли непонятно куда, в landscape режиме с размерами вьюх и лейбла просто беда, а их ширина чересчур велика. В iPad все не так страшно, но расстояние между лейблом и кнопкой слишком большое, а первая UIView растянулась. 

Сначала исправим все для iPhone 5. Для начала в Size Classes выберем (compact Width | regular Height) для портретной ориентации:

![image](https://habrastorage.org/files/b1e/e87/1d6/b1ee871d6fe04ac0be20ece1b26144e9.png)

Обратите внимание, что полоска выбора Size Classes стала синей. Это говорит о том, что бы вы были повнимательней, так как на данный момент находитесь не в базовой конфигурации, и изменения 4х типов будут применимы только к данному типу и ориентации устройства. Какие же это изменения? Как было сказано, их 4:

1) значения Constraint'ов 
2) шрифты 
3) включение/выключение Constraint'ов
4) включение/выключение subviews 

P.S. Напомним, все что будет далее сделано, применимо ТОЛЬКО к портретной ориентации iPhone 5, о чем нас информирует зеленая точка в сетке Size Classes!

Начнем с 1 пункта и исправим значения Constraint, что бы все выглядело корректно. В данной конфигурации мы видим ошибки Constraint:

![image](https://habrastorage.org/files/d52/77a/c7a/d5277ac7a3e5411781f4b1e5aafc73d7.png)

Для их исправления в данной ориентации идем к настройкам Constraint, и выберем значение горизонтального расстояния между View так как для iPhone оно слишком большое:

![image](https://habrastorage.org/files/8bb/f10/ba8/8bbf10ba899b4804b9a04f54213217bf.png)

В панели Utilities в вкладке Size Inspector возле пункта Constant (а также возле „Installed“, но о нем позже) жмем небольшой знак „+“:

![image](https://habrastorage.org/files/34b/f5f/95c/34bf5f95c8d24bd7b8af17bccecfad1b.png)

И выбираем „Compact Width | Regular Height (current)“:

![image](https://habrastorage.org/files/c98/a72/4d0/c98a724d005646a6a25f31684ca21f65.png)

Видим, что у нас появилась новая Constant (wC hR), для данной ориентации, введем значение 34. Тоже самое проделываем со вторым Constraint между 2й и 3й вью.

![image](https://habrastorage.org/files/d4a/b9c/4d8/d4ab9c4d86e84f17b4a80d89a22d6cee.png)

После чего у нас должны исчезнуть ошибки связанные с расстоянием по ширине между вью:

![image](https://habrastorage.org/files/226/811/009/226811009e5b40f0b8b6777251c4814d.png)

Тоже самое проделаем и с шириной кнопки и лейбла для данной ориентации. Выберем их ширину, добавим Constant „Compact Width | Regular Height (current)“, и установим значение 200:

![image](https://habrastorage.org/files/347/e3e/279/347e3e2794aa49abb13bd46d2165e50e.png)

Как мы помним, нашей кнопке мы устанавливали Horizontal Center in Container и Vertical Center in Container, от чего и возникают оставшиеся конфликты. Их можно решить как минимум 2 способами в зависимости от того что требуется, 1й способ, разместить кнопку по центру как она того и требует, либо оставить все как есть и воспользоваться 3 пунктом из списка выше. В данном примере мы воспользуемся способом под номером 2, так как это все таки статья о Size Classes.

Итак, для того чтобы „выключить“ Constraint, которая нам мешает, нужно ее выбрать из списка:

![image](https://habrastorage.org/files/19a/aed/5e0/19aaed5e0b124a98b6bf78adc6e94fe6.png)

И снова обратиться к вкладке Size Inspector, только теперь нажимаем „+“ который возле чекбокса „Installed“:

![image](https://habrastorage.org/files/34b/f5f/95c/34bf5f95c8d24bd7b8af17bccecfad1b.png)

Опять таки выбираем и выбираем „Compact Width | Regular Height (current)“, и теперь у нас как и с Constant (wC hR) появился чекбокс wC hR „Installed“, убираем его и видим, что ошибки исчезли, а для данной конфигурации данный Constrain выключен:

![image](https://habrastorage.org/files/b64/2e9/ccd/b642e9ccd7d54c59a303e5e076698c6f.png)

Запустим наше приложение на выполнение и увидим, что все отображается корректно (ну почти все, так как размеры View не подгонялись для конкретного устройства Xcode автоматически уменьшает одну из них):

![image](https://habrastorage.org/files/9e6/5e1/93b/9e65e193b1b544e695563a5b3fe8facf.png)

Данную проблему мы исправим позже.

Проделываем аналогичные действия для iPhone 5 landscape, выберем в сетке Size Classes (compact Width | compact Height):

![image](https://habrastorage.org/files/ac4/e3b/426/ac4e3b4265f84940bd80b2423813ee64.png)

Нам нужно так как и для портретного режима изменить расстояние между вью и размеры кнопки и лейбла, я выбрал такие же значения как и для портретного режима: расстояние между вью 34, размер кнопки и лейбла 200. Запустим наш апп:

![image](https://habrastorage.org/files/3bf/879/5e5/3bf8795e5b394705a527de4abb845b41.png)

Опять таки из за того, что мы не подгоняли размер, Xcode растягивает View. Исправим эту ошибку в портретной (compact Width | regular Height) и ландшафтной (compact Width | compact Height) ориентации. Выделим наши 3 вьюхи и выбираем в меню Editor -> Pin -> Widths Equally. После чего запустим наше приложение на выполнение и видим что все отображается корректно: 

![image](https://habrastorage.org/files/a89/f88/2d2/a89f882d2a124d16bb054782a6bfd3a1.png)
![image](https://habrastorage.org/files/343/072/397/343072397b4140d89aaf80e1a932b998.png)

Дабы не повторяться для iPad, мы проделываем все тоже самое, что и для iPhone, только в сетке Size Classes выбираем regular Width | regular Height, что соответствует портретной и ландшафтной ориентации iPad. В данной ситуации мы просто обновили все Constraint, отцентрировали и выставили Width, Height Equally:

![image](https://habrastorage.org/files/546/c15/519/546c155191644972a7fd44ef1db1ac72.png)
![image](https://habrastorage.org/files/46a/1a3/d05/46a1a3d05d544cd19087b4fcc38ec50c.png)

Осталось 2 пункта, по которым не прошлись, это включение/выключение subviews и шрифты. Выберем ландшафтный режим iPhone compact Width | compact Height. Выделим наш UILabel и перейдем в Attributes Inspector, напротив шрифта мы увидим уже знакомый нам „+“: 

![image](https://habrastorage.org/files/87d/21a/706/87d21a706caa4c85a57b525c9e0bd84a.png)

По аналогии с Constraint добавляем „compact Width | compact Height (current)“. Для значения wC hC выставим шрифт Helvetica Neue Ultra Light и размер 23. При запуске мы увидим что в портретной ориентации шрифт стандартный а уже в ландшафте Helvetica Neue:

![image](https://habrastorage.org/files/9e5/9c6/fc7/9e59c6fc7a044d17ae638212d989a32b.png)
![image](https://habrastorage.org/files/7de/e0b/6fb/7dee0b6fba0c4018a7d83af8f2d19682.png)

Итак, последний пункт это включение и выключение subviews. Я думаю, тут объяснять уже ничего не надо, и так все становится понятно после всех этих разжовываний. Все по аналогии с включением выключением Constraint, разница лишь только в том, что тут мы выключим кнопку для конкретной ориентации:

![image](https://habrastorage.org/files/80d/126/c3e/80d126c3e302430db18824116d723070.png)
![image](https://habrastorage.org/files/9e5/9c6/fc7/9e59c6fc7a044d17ae638212d989a32b.png)

В общем, моя первая статья получилась не маленькая, но и небольшая, надеюсь, она интересная и полезная. На момент написания статьи Xcode 6 еще в бете, и не выпущен iPhone 6, после презентации которого возможно пополнится документация, а также изменится и внешний вид ViewController'ов в storyboard, так как в данной ситуации не очень удобно все размещать в „квадратах“, хотя в сетке Size Classes выбран iPhone landscape. 


---
- [x] **Для чего нужны Content Hugging Priority / Content Compression Resistance Priority.**


Auto Layout занимается динамическим вычислением позиции и размера всех view в view иерархии, на основе constraints — правил заданных для того или иного view. Самый большой и очевидный плюс для разработчика в использовании Auto Layout в том, что исчезает необходимость в подгонке размеров приложения под определенные устройства — Auto Layout делает это за вас, динамически изменяя интерфейс в зависимости от внешних или внутренних изменений.

Примером внешних изменений может быть: Изменение размера окна в macOS, изменение ориентации экрана, различные размеры экранов.

Пример внутренних изменений: Изменение контента в окне, изменения в зависимости от языка и т.д.

Создать свой интерфейс можно 3-мя способами: программно, на основе маски, которая автоматически подстраивается под изменения или использовать Auto Layout.

Отличие Auto Layout от других способов в том, что вам больше не нужно писать код, который изменяет интерфейс в зависимости от размера окна и других элементов, вместо этого Auto Layout самостоятельно вычисляет расположение элемента интерфейса в приложении и изменяет его относительно окружения.

**Auto Layout без ограничений**

Если вы по каким-либо причинам не хотите использовать правила(constraints) или ваш интерфейс содержит в себе множество элементов расположение которых можно изменять бесконечно, вам на помощь придет Stack View. 

Stack View — это ваша палочка выручалочка при создании комплексных интерфейсов. Он может расставлять элементы внутри себя с данными параметрами:

axis (только UIStackView) — определяет ориентацию, горизонтально или вертикально;
orientation (только NSStackView) — тоже что и axis у UIStackView;
distribution — определяет расположение элементов в данной ориентации;
alignment — определяет расположение элементов перпендикулярно ориентации StackView;
spacing — определяет расстояние между соседними элементами;

Для получения максимально удовлетворительных результатов вы можете использовать constraints в самом StackView либо вкладывать несколько StackView в StackView и затем использовать constraints например для выравнивания по центру экрана.

**Анатомия Constraint**

Вся суть правил сводится к созданию вычисления у которого может быть только один ответ — расположение элемента интерфейса.

Выглядит это приблизительно так:

Кнопка.Верх = ВысшаяТочкаИнтерфейса.Низ + 100

По данному выражению понятно что оно означает и какое правило устанавливает для view. Как уже было сказано, вычисления Auto Layout всегда происходят относительно ближайших элементов, будь это граница экрана или соседняя кнопка.

В своих вычислениях constraints используют множители, ближайшие объекты и константы вроде + 100 из примера выше. Так же при создании правил не обязательно, чтобы это были равенства, вы можете использовать >= или<=.

При создании layout желательно указывать два правила для каждого измерения на элемент. Но не забывайте про StackView, который вам очень поможет при создании интерфейса.

Самым интересным фактом является то, что при создании constraints вы можете устанавливать приоритетность самих constraints. При вычислении, Auto Layout старается удовлетворить все constraint'ы в порядке приоритетности. Приоритет = 1000 — обязателен. Все остальные, менее приоритетные правила вы можете устанавливать для придания четкости обработке расположения элементов вашего интерфейса. В случае, если один из constraint'ов будет не правильно вычислен, Auto Layout использует ближайший constraint и начнет отталкиваться от него. Тем не менее, настоятельно рекомендую не перегружать интерфейс различными правилами и использовать дополнения только для достижения нужного результата.

**Создание Auto Layout и его составляющих**

Вы можете создавать constraint'ы 3-мя способами:

1. CTRL + Перетаскивание, например, от label к верхней границе.
2. Используя Stack, Align, Pin и Resolve Tools.
3. Предоставить Interface Builder построить constraints вместо вас.

Среди данных пунктов самый важный именно 2-й, так как использование этой панели является основным инструментом при создании разметки.

Stack — собственно та самая кнопка, с помощью которой вы можете поместить выделенные детали интерфейса в StackView. Interface Builder сам решает каким будет StackView в зависимости от расположения элементов. Кроме кнопки Stack, StackView можно создать перетягиванием из библиотеки объектов, как любой другой элемент.

Align — меню, которое позволит вам установить элементы четко по определенной линии, будь то с боку, вертикально по центру или снизу. Вы часто используете такой подход, когда выравниваете текст по центру или строго слева от начала страницы в текстовых редакторах.

Pin — меню, позволяющее вам задать жесткие рамки относительно своего размера или ближайшего предмета. В нем вы выбираете какой constraint вы хотите задать в том или ином направлении и установить его параметры. Также с помощью данного меню можно, к примеру, придать группе кнопок одинаковый размер, не изменяющийся не смотря на масштаб экрана.

Resolve Tools — самый лучший помощник в отладке constraint'ов. Основные возможности этого меню: убрать все правила, добавить предположительные constraints(Interface Builder построит все правила за вас), добавить отсутствующие constraints, обновить constraints или frames(положение объектов).
Как вы видите, тут довольно много важных пунктов и они призваны облегчить все тяготы разработчика.

Редактировать constraint'ы можно нажав на них в Interface Builder, найти в Size Inspector или в списке Document Outline. При редактировании параметров вы можете задавать идентификаторы для более легкого понимания и нахождения их в логах и консоли при выполнении различных отладок.

Немаловажным аспектом при установке правил для элементов, являются параметры CHCR (Content-Hugging and Compression-Resistance Priorities) — эти параметры влияют на изменение самого элемента в зависимости от вышестоящего view. Грубо говоря Hugging — это нежелание элемента увеличиваться, а Compression-Resistance — нежелание уменьшаться. С помощью параметров CHCR можно к примеру изменять соотношение сжатия-расширения элементов в StackView в зависимости от размеров находящихся в нем элементов.

Будьте внимательны — macOS и iOS рассчитывают layout'ы по разному: В macOS, Auto Layout может изменять размер окна и размер содержимого, а в iOS он может менять только размер содержимого, так как система сама определяет размер и границы приложения.





---
- [x] **Для чего служит reuseIdentifier в экземплярах ячеек.**

The reuseIdentifier is used to group together similar rows in an UITableView.

A UITableView will normally allocate just enough UITableViewCell objects to display the content visible in the table.

If reuseIdentifier has not been set, the UITableView will be forced to allocate new UITableViewCell objects for each new item that scrolls into view, potentially leading to laggy animations.


---
- [x] **Как можно ускорить работу UITableView.**

Even if your cell is actually that simple (background image and label) there are some things to consider

**Image caching** This is the obvious thing - if you are using the same image everywhere, load it once into the UIImage and reuse it. Even if the system will cache it on its own, directly using the already loaded one should never hurt.

**Fast calculation** Another rather obvious thing - make calculating height and content as fast as possible. Don't do synchronous fetches (network calls, disk reads etc.).

**Alpha channel** in image What's expensive when drawing is transparency. As your cell background has nothing behind it, make sure that you save your image without alpha channel. This saves a lot of processing.

**Transparent label** The same holds true for the label on top of your background view, unfortunately making it opaque might ruin the looks of your cell - but it depends on the image.

**Custom cell** In general, subclassing UITableViewCell and implementing drawRect: yourself is faster than building the subview hierarchy. You might make your image a class variable that all instances use. In drawRect: you'd draw the image and the text on top of it.

**Check compositing** The simulator has a tool to highlight the parts that are render-expensive because of transparency (green is ok, red is alpha-blending). It can be found in the debug menu: "Color Blended Layers"


---
- [x] **Как отобразить вью поверх всех остальных вне зависимости от контроллера.**

If you're targeting iOS 5+ and iPad only, you can make a top-level view controller which has two contained view controllers. The first would be a view controller for your "TouchDisplay" view. The second would be the application's normal root view controller. (i.e. your existing main view controller; you'll need to set definesPresentationContext to YES on this view controller) Since you're writing the container view controller, you can order those two subviews however you like. There is a WWDC 2011 Talk on view controller containment that goes into great detail about this. This is the most "correct" approach IMHO, because it gives you a view controller for your TouchDisplay view, handles rotation and generally plays nice with others. (This only works on iPad, because on iPhone a new modal view always covers the full screen.)

A more straight-forward approach is to simply add your TouchView to your existing top-level UIWindow as a subview with addSubview:. Most applications don't actually remove the top-level view controller or add new top-level ones; they just present other view controllers from it. A view you add in the top-level window will stay above those. Of course, your app may not follow this rule, in which case you might try option #3 instead. This has rotation gotchas (your view will not auto-rotate when the device rotates, so you need to do this yourself.) You could also force your view back to top, say, on a 1-second timer, if you are having issues with other things covering it. This is also not as nice as option #1 because you don't get a UIViewController, just a UIView.

The most extreme approach is that you can create another UIWindow and give it a higher window level, such as UIWindowLevelAlert and put your TouchDisplay view in that. You can then make the window background transparent, and it will stay above your normal app content. There are lots of gotchas here, especially about auto-rotation and which window is the keyWindow (which is why you should use #1 or #2 instead if you can).


---
- [x] **В чем отличие делегирования от KVO.**

Use a delegate if you want to talk to only one object. For example, a tableView has a delegate - only one object should be responsible for dealing with it.

Use notifications if you want to tell everyone that something has happened. For example in low memory situations a notification is sent telling your app that there has been a memory warning. Because lots of objects in your app might want to lower their memory usage it's a notification.

I don't think KVO is a good idea at all and try not to use it but, if you want to find out if a property has changed you can listen for changes.


---
- [x] **В чем отличие Array (Swift) и NSArray (ObjC).**

For one thing, NSArray is always immutable, whereas Array is immutable when declared with let, and mutable when declared with var.



---
- [x] **Что такое tuple?**

Кортежи в основном являются значением, которое может содержать несколько других значений. Составной тип может содержать также “именованные типы”, которые включают в себя классы, структуры и перечисления (также протоколы, но так как они не хранят значения непосредственно, я знал, что должен упомянуть их отдельно), а также другие составные типы. Это означает, что кортеж может содержать другие кортежи. Другой составной тип, который может содержать кортеж, является “функциональным типом”, который различным способом ссылаться на тип. Он описывает замыкания в частности стиля типа “() >() ”, чьи функции и методы соответствуют ему. Также функциональный тип может содержать другие составные типы, как кортеж, и замыкания, про которые Вы читали в моем предыдущем посте "Замыкание и Определение в Swift".


Будучи некомпетентным в технических вопросах, Вы можете думать о кортежах как о классе или структуре, которые Вы можете быстро написать, не имея необходимость определять абсолютный класс или структуру (вложенную или наоборот). Хотя согласно iBook от Apple, они, вероятно, должны использоваться только для временных значений, как возвращаемое значение из функции. Apple не объясняет почему, но я должен был догадаться, что, они, вероятно, были, оптимизированы для быстрого создания, за счет того, как они храняться. Тем не менее, одна из потрясающих новых возможностей Swift – это возвращение нескольких типов, и кортежи помогают Swift достигать этой цели, так как они технически возвращают единственное значение (сам кортеж).

Иногда пользователям интересно как же все-таки произносится кортеж на английском языке. Согласно онлайн словарю Dictionary.com, есть два допустимых произношения. В фонетической транскрипции словаря можно увидеть и вариант /ˈtjʊpəl/ и /tʌpəl/.Для тех, кому любопытно, ʊ — это звук который на письме отвечает “oo” как в слове «took»; ə — это звук который соответствует «а» на письме как в слове «about»; и ʌ звук буквы “u” как в “gut.” Это означает, что эти звуки произносятся (примерно) как too-puhl и tuh-puhl, соответственно. Я хотел узнать, откуда берет начало их происхождение, но наткнулся только на то, что они в основном являются составной частью других слов как “quadruple,” “quintuple,” и “octuple” (заметьте “tuple”” в конце каждого слова). Я лично предпочитаю too-puhl.

Но, я предполагаю, что произношения изменятся, так как каждая англоязычная страна будет ссылаться на свои стандарты произношения. Те, кто слушают Accidental Tech Podcast, очевидно согласятся с обоими вариантами Dictionary.com huhv-er (/ˈhʌv ər) и hov-er (ˈhɒv-/ ər). Те, которые не слушают его, Вы действительно должны это сделать.
Во всяком случае, хотя, Вы не зашли сюда, чтобы изучать фонетику английского языка, пришло время узнать, как использовать кортежи в Swift!

Создание кортежа

Есть несколько способов создания кортежей. Технически, я считаю, что один способ называется «Tuple Pattern». Это в значительной степени выглядит в точности как литеральный массив, кроме как того, что находиться в круглых скобках (вместо квадратных скобок), и он может иметь любой тип (в отличие от того же типа в Массиве):
```
let firstHighScore = ("Mary", 9001)
```

Вышеупомянутый способ является самым легким. Существует еще один способ создания кортежа, который пользуется преимуществом именованных параметров Swift. Позже Вы увидите, как этот способ может быть полезен:
```
let secondHighScore = (name: "James", score: 4096)
```

Это действительно все, что нужно сделать. Если бы это была структура, Вам пришлось бы описать структуру где-то выше в коде, с ее внутренними свойствами и т.п. Если бы это был класс, Вам бы пришлось написать инициализатор для него самостоятельно. Все, что Вам нужно сделать с кортежами, это поместить значения в пару круглых скобок и разделить их запятыми. Если Вы действительно хотите, то можете даже назвать их для последующего использования.

Получение данных из кортежей

Есть несколько способов чтения из кортежа. Какой Вы будете использовать, будет зависеть от того, где этот кортеж используется. Вы можете использовать любой из них в любое время, но, вероятно, в определенных ситуациях будет проще использовать какой-то один способ, чем другие.

Во-первых, Вы можете связать содержимое кортежа, используя механизм сопоставления с образцом:
```
let (firstName, firstScore) = firstHighScore
```

В этом случае, так как мы используем сопоставление с образцом, Вы также можете использовать подчеркивания, чтобы проигнорировать некоторые значение, если Вы хотите использовать только некоторые из них. Если Вы хотели получить только некоторые значение из Вашего кортежа, можно сделать следующее:
```
let (_, onlyFirstScore) = firstHighScore
```

Им также присваиваются имена по умолчанию, которые стартуют как и индексы в массивах от 0, таким образом, Вы также можете написать:
```
let theName = firstHighScore.0
let theScore = firstHighScore.1
```

Наконец, если Вы определили название кортеджа при его создании, Вы можете получить доступ к ним c помощью способа, который использует точечный синтаксис:
```
let secondName = secondHighScore.name
let secondScore = secondHighScore.score
```

Возврат кортежа из функции

Об этом шла речь в моем предыдущем посте «Функции Swift: Параметры и тип возвращаемого значения», но поскольку этот пост содержит все что, нужно знать о кортежах Swift, то я решил объединить всю информацию в одном месте.

Вот функция, которая возвращает Ваш высокий балл:
```
func getAHighScore() -> (name: String, score: Int)
{
	let theName = "Patricia"
	let theScore = 3894
	return (theName, theScore)
}
```

Таким образом, мы назвали то, чем они будут в значении возврата функции, и когда мы фактически создали кортеж, мы не должны даже давать ему имя. Так как кортеж, который я вернул и требуемый кортеж, который был возвращен в прототип функции были типами (String, Int), потому у компилятора не возникало проблемы с ним. Если не хотите определять их в прототипе функции, это нормально, но Вы просто должны указать тип, так и с указанием возвращаемого типа, поскольку ”(String, Int),” также приемлемо для компилятора.

Если Вы хотите дополнительно вернуть кортеж, Вам просто нужно добавить вопросительный знак после типа возвращаемого значения вот так:
```
func maybeGetHighScore() -> (String, Int)?
{
    return nil
}
```

Конечно, возвращаемый кортеж является опциональными, таким образом, Вы должны будете развернуть его, чтобы использовать значения. Вы можете сделать это через дополнительное связывание, вот так:
```
if let possibleScore = maybeGetHighScore()
{
    possibleScore.0
    possibleScore.1
}
else
{
    println("Nothing Here")
}
```

Если Вы хотите узнать немного больше о Optionals в Swift, прочитайте мой предыдущий пост "Swift Optionals – Объявление, Unwrapping и Binding".

Нужно отметить еще одну интересную деталь о Кортежах и функциях, когда у Вас есть функция, которая не определяет возвращаемого значения, она фактически, возвращает пустой кортеж, который обозначен просто как” () “, просто открытая и затем закрыта круглая скобка (не используйте кавычки, они нужны только для компоновки символов, чтобы сделать их более очевидными).

Контроль доступом и Кортежи

Поскольку мои посты были в основном предназначены для иллюстрации языка программирования Swift, а не полноценные уроки (но это скоро изменится), мы не говорили слишком много о контроле доступа с моего первого поста «Контроль доступа в Swift». Как только мы полностью погрузимся в работу над уроками, они будут появляться намного чаще.

Тем не менее, они действительно влияют на кортежи в Swift. Уровень доступа кортежа определяется его составляющими и не установлен непосредственно, так как Вы бы установили для свойства или функции. Уровень доступа кортежа будет определяться уровнем доступа своего самого ограниченного компонента. Таким образом, если один тип является приватным, а другой общедоступным, то контроль доступа кортежа будет приватным.

Кортеж — тип значения

Обновление 7 октября 2014: Благодаря разработчику Clang, который направил меня на пост с Блога Apple прос Swift: Тип значения и ссылки – Блог Swift – Разработчик Apple, который действительно относит Кортеж к типу значения, рядом со структурами и перечислениями. 

Прежде чем упомянуть о вышесказанном, я сделал тест используя playground, чтобы узнать, является ли кортеж типом значения или нет:
```
var someScore = ("John", 55)
 
var anotherScore = someScore
anotherScore.0 = "Robert"
 
println(anotherScore.0)  //Outputs:  "Robert"
println(someScore.0)     //Outputs:  "John"
```

Тест действительно окончательно показал, что кортежи являются типами значений. Я присвоил someScore другой переменной, названной “anotherScore”. Тогда я изменил значение для переменной anotherScore на “ Robert ”. Когда я проверял, какое имя было у anotherScore, я конечно увидел “Robert ”, но когда я проверял someScore (исходная переменная) то у него все еще было имя “John”.


---
- [x] **Что такое defer и guard?**

**guard**
Рассмотрим один из популярных примеров. Во многих случаях нужно проверить объекты перед использованием. Например пользовательский ввод, данные из базы данных, что-то из файла и другое. Объекты могут содержать null (nil из iOS). Для этого в разных языках существуют разные проверки. В C# до последнего года приходилось городить огород из if (obj != null), год назад с выходом новой версии C# появился оператор .?, который выполняет следющий за ним выражение, если предыдущее не null. Это оказалось удобно. Что есть в Swift?

В Swift рекомендуется и используется конструкция if let. Например 
```
let a = "10".toInt()
let b = "5".toInt()
let c = "3".toInt()

if let a = a {
    if let b = b {
        if let c = c {
            if c != 0 {
                println("(a + b) / c = \((a + b) / c)")
            }
        }
    }
}
```
Можно заметить, что для трех переменных конструкция становится лесенкой. В народе это называется пирамида смерти. Возможное решение использование глобальных функций (аналог макросов), но это глупости, не решение а костыль. В Swift 1.2 появилась возможность проверять несколько Optional переменных за раз:
```
if let a = a, b = b, c = c where c != 0 {
    println("(a + b) / c = \((a + b) / c)")     // (a + b) / c = 5
}
```
Видно, что количество кода сократилось, повысился уровень восприятия. Часто приходится проверять условие, и, если условие не выполнилось возвращать управление. Ключевое слово guard проверяет условие, и если оно не выполнилось — требует выйти из текущего блока. Например:
```
guard let cellObj = cell as? MyCell else { return false; }
guard index == 0 else { return false; }
guard let userId = _userModel.Id else { return false; }
```
Таким образом осуществляется проверка нескольких условий, и выход из блока, если условия не выполнились. Основной (главный) блок функции будет выполнен, если все условия guard выполнились успешно. Рекомендуется использовать guard вместо if let потому что:

cинтаксис такой, что код не захламляется условиями и скобками блоков
безопаснее сделать все проверки сразу и выполнять код функции, если все данные есть и не nil
дополнительная информация компилятору для выявления возможных ошибок
В своем коде мне очень понравилось использовать новый оператор.

**defer**
В то время как guard используется для проверки объектов на nil, чтобы избежать возможного исключения — defer призван отложить выполнения кода, объявленного в defer до окончания выполнения текущего блока. Звучит запутанно, попробуем разобраться на примере. Допустим вы открываете файл. Что нужно сделать обязательно? Правильно (надеюсь) — закрыть его. Но глупо закрывать файл, сразу после открытия. С другой стороны, если метод работы с файлом больше 20 строк то есть вероятность потеряться и не закрыть его. Swift гарантирует, что код будет запущено прежде, чем текущий блок будет закончен.
```
let file = openFile()
defer { closeFile(file) }
```
Открыли файл и обозначили, что его нужно закрыть. Далее работаем с файлом и не беспокоимся о том, что его нужно будет закрыть.
```
let hardwareStatus = fetchHardwareStatus()
guard hardwareStatus != "disaster" else { return }
file.write(hardwareStatus)
```
defer будет вызван независимо от того, какой из блоков guard сработает, даже если ни один из них не сработает. Если метод завершается по ошибке, return'ом или штатное завершение defer будет выполнен. 

Одна из интересных функций defer позволяет добавить в стек несколько блоков defer. Это означает, что все блоки будут выполнены позже (в обратном порядке FILO).

defer очень интересная особенность Swift, которая, уверен будет полезна и в других языках программирования. Возможно, к минусам можно отнести то, что из defer нельзя выполнить выход (return). В своих проектах мне еще не довелось полностью погрузиться идеологию defer, но это скоро исправится.

---
- [x] **Что такое generic, чем отличается от AnyObject?**

Generics are type safe, meaning if you pass a string as a generic and try to use as a integer the compiler will complain and you will not be able to compile your (which is good). (This happens because Swift is using Static typing, and is able to give you a compiler error)

If you use AnyObject the compiler has no idea if the object can be treated as a String or as an Integer. It will allow you to do whatever you want with it (which is bad).

e.g. if you try to pass a String when it the your previously used Integer the application will crash. (This happens because Swift is using Dynamic typing and will only give you a runtime crash)

Generics basically tells the compiler:

"I am going to give you a type later and I want you to enforce that type everywhere I specify."

AnyObject basically tells the compiler:

"Don't worry about this variable no need to enforce any type here let me do whatever I want to."


---
- [x] **Как работает lazy в Swift?**

Ленивое свойство хранения - свойство, начальное значение которого не вычисляется до первого использования. Индикатор ленивого свойства - ключевое слово lazy.

Заметка
Всегда объявляйте свойства ленивого хранения как переменные (с помощью ключевого слова var), потому что ее значение может быть не получено до окончания инициализации. Свойства-константы всегда должны иметь значение до того, как закончится инициализация, следовательно они не могут быть объявлены как свойства ленивого хранения.

Ленивые свойства полезны, когда исходное значение свойства зависит от внешних факторов, значения которых неизвестны до окончания инициализации. Так же ленивые свойства полезны, когда начальное значение требует комплексных настроек или сложных вычислений, которые не должны быть проведены до того момента, пока они не понадобятся.

Пример ниже использует ленивое хранение свойства для избежания ненужной инициализации сложного класса. Этот пример объявляет два класса DataImporter и DataManager, но ни один из них мы не показали полностью:
```
class DataImporter {
    /*  
     DataImporter - класс для импорта данных с внешних источников
     Считаем, что классу требуется большое количество времени для инициализации
     */
    var fileName = "data.txt"
    // класс DataImporter функционал данных будет описан тут
}

class DataManager {
    lazy var importer = DataImporter()
    var data = [String]()
    // класс DataManager обеспечит необходимую функциональность тут
}
 
let manager = DataManager()
manager.data.append("Some data")
manager.data.append("Some more data")
// экземпляр класса DataImporter для свойства importer еще не создано
```
Класс DataManager хранит свойство data, которое инициализируется с новым пустым массивом значений типа String. Несмотря на то, что большая часть функциональности недоступна, цель класса DataManager - это управление и обеспечение доступа к этому массиву данных типа String.

Часть функциональности класса DataManager - это импорт данных из файла. Эта функциональность обеспечивается классом DataImporter, который, как мы подразумеваем, требует уйму времени для инициализации. Это может происходить из-за того, что экземпляр класса DataImporter должен открыть файл и прочитать его содержимое в памяти, когда DataImporter уже инициализирован.

Это возможно для экземпляра DataManager управлять своими данными даже не импортируя их из файла, так что нет такой надобности, как создавать экземпляр DataImporter, когда сам DataManager создан. Вместо этого логичнее создать экземпляр DataImporter тогда, когда он будет впервые востребован.

Так как он создан как модификатор lazy, экземпляр DataImporter для свойства importer создается только тогда, когда впервые к нему обращаются, например когда запрашивается свойство fileName:
```
print(manager.importer.fileName)
// экземпляр DataImporter для свойства importer только что был создан
// Выведет "data.txt"
```
Заметка
Если к свойству обозначенному через модификатор lazy обращаются сразу с нескольких потоков единовременно, и если оно еще не было инициализировано, то нет никакой гарантии того, что оно будет инициализировано всего один раз.


---
- [x] **В чем отличие escaping от не escaping closure?**

Согласно документации, вы обязаны пометить замыкание, передаваемое в функцию в качестве параметра, ключевым словом @escaping, если оно будет вызвано после возвращения из функции.

A closure is said to escape a function when the closure is passed as an argument to the function, but is called after the function returns

Иными словами, если замыкание (closure) будет вызвано асинхронно внутри (callback) или за пределами (delegate) функции, то вы должны его пометить как @escaping.

По сути @escaping позволяет вам отложить выполнение передаваемого в качестве параметра замыкания до нужного вам момента (например, по срабатыванию таймера или по завершению асинхронной операции).

Если вы попробуете присвоить non-escaping замыкание в свойство класса, структуры или перечисления вы получите compile-time ошибку:
```
class MainDispatcher {
    var work: (() -> Void)? = nil

    func async(_ block: () -> Void) {
        self.work = block
    }
}
```
```
error: assigning non-escaping parameter 'block' to an @escaping closure self.work = block
```
Если вы попробуете вызвать non-escaping замыкание асинхронно (то есть там где ожидается escaping замыкание), вы также получите compile-time ошибку:
```
class MainDispatcher {    
    func async(_ block: () -> Void) {
        DispatchQueue.main.async {
            block()
        }
    }
}
```
Closure use of non-escaping parameter 'block' may allow it to escape

По умолчанию замыкание, передаваемое в качестве параметра, является non-escaping, поэтому там, где вам необходимо вы должны использовать ключевое слово @escaping.
```
class MainDispatcher {
    var work: (() -> Void)? = nil

    func async(_ block: @escaping () -> Void) {
        self.work = block
        DispatchQueue.main.async {
            self.work?()
        }
    }

    func sync(_ block: () -> Void) {
        DispatchQueue.main.sync {
            block()
        }
    }
}
```

---
- [x] **Чем отличается weak от unowned?**

Both weak and unowned references do not create a strong hold on the referred object (a.k.a. they don't increase the retain count in order to prevent ARC from deallocating the referred object).

But why two keywords? This distinction has to do with the fact that Optional types are built-in the Swift language. Long story short about them: optional types offer memory safety (this works beautifully with Swift's constructor rules - which are strict in order to provide this benefit).

A weak reference allows the posibility of it to become nil (this happens automatically when the referenced object is deallocated), therefore the type of your property must be optional - so you, as a programmer, are obligated to check it before you use it (basically the compiler forces you, as much as it can, to write safe code).

An unowned reference presumes that it will never become nil during it's lifetime. A unowned reference must be set during initialization - this means that the reference will be defined as a non-optional type that can be used safely without checks. If somehow the object being referred is deallocated, then the app will crash when the unowned reference will be used.

From the Apple docs:

Use a weak reference whenever it is valid for that reference to become nil at some point during its lifetime. Conversely, use an unowned reference when you know that the reference will never be nil once it has been set during initialization.

In the docs there are some examples that discusses retain cycles and how to break them. All these examples are extracted from the docs.

Example for the weak keyword:
```
class Person {
    let name: String
    init(name: String) { self.name = name }
    var apartment: Apartment?
}

class Apartment {
    let number: Int
    init(number: Int) { self.number = number }
    weak var tenant: Person?
}
```
And now, for some ASCII art (you should go see the docs - they have pretty diagrams):
```
Person ===(strong)==> Apartment
Person <==(weak)===== Apartment
```
The Person and Apartment example shows a situation where two properties, both of which are allowed to be nil, have the potential to cause a strong reference cycle. This scenario is best resolved with a weak reference. Both entities can exist without having a strict dependency upon the other.

Example for the unowned keyword:
```
class Customer {
    let name: String
    var card: CreditCard?
    init(name: String) { self.name = name }
}

class CreditCard {
    let number: UInt64
    unowned let customer: Customer
    init(number: UInt64, customer: Customer) { self.number = number; self.customer = customer }
}
```
In this example, a Customer may or may not have a CreditCard, but a CreditCard will always be associated with a Customer. To represent this, the Customer class has an optional card property, but the CreditCard class has a non-optional (and unowned) customer property.
```
Customer ===(strong)==> CreditCard
Customer <==(unowned)== CreditCard
```
The Customer and CreditCard example shows a situation where one property that is allowed to be nil and another property that cannot be nil have the potential to cause a strong reference cycle. This scenario is best resolved with an unowned reference.

Note from Apple:

Weak references must be declared as variables, to indicate that their value can change at runtime. A weak reference cannot be declared as a constant.

There is also a third scenario when both properties should always have a value, and neither property should ever be nil once initialization is complete.

And there are also the classic retain cycle scenarios to avoid when working with closures.


---
- [x] **Как работают методы map filter reduce sort?**

Метод filter создает новый массив с отфильтрованными элементами, то есть из массива удалены так называемые «нежелательные» элементы
Функция, передаваемая как аргумент, возвращает false, если элемент «нежелательный»

```
filter (includeElement:(T) -> Bool) -> [T]
```

Аргументом filter является функция, которая по каким либо критериям внутри себя  решает будет ли  элемент «нежелательный» или  «желательный».

Метод map cоздает новый массив путем трансформации каждого элемента массива во что-то другое

Элемент, в который идет трансформация, может иметь другой тип, чем элемент исходного массива
```
map (transform:(T) -> U) -> [U]
let stringified: [String]&nbsp; = [1,2,3].map {"\($0)"}
```
В данном случае stringified — массив из строк  [«1″,»2″,»3»]. Перебирая каждый элемент массива функция map создает массив строк с помощью  замыкания {«\($0)»}
 

Метод reduce  cворачивает целый массив до единственного значения
```
reduce (initial:U, combine: (U,T) -> U) -> U
let sum: Int  = [1,2,3].reduce(0){$0 +$1}//добавляет числа в массиве
```
Он берет начальное значение 0 и прибавляет первый элемент массива. То что именно прибавляет задано замыканием {$0 +$1}. Но мы можем использовать любую функцию (U,T) -> U.  Затем результат сложения устанавливается в переменную initial:U и те же действия происходят с остатком массива.  Таким образом sum — это сумма элементов массива.


---
- [ ] **Какие есть нюансы при работе с CoreData в разных потоках?**

Как известно, Core Data является мощным Apple фреймворком для управления объектным графом. На Хабре немало статей о Core Data, тем не менее, многопоточность освещена достаточно слабо, а, как мне кажется, вопросом о том как правильно ее реализовать, задавался почти каждый.

Общие положения

Если вкратце, то стек Core Data состоит из нескольких основных частей.

1) NSPersistentStore, которым может выступать бинарный файл, XML, SQLite файл.
2) NSManagedObjectModel, которая является скомпилированной бинарной версией модели данных.
3) NSPersistentStoreCoordinator, занимается загрузкой данных из NSPersistentStore и NSManagedObjectModel, сохранением и кешированием.
4) NSManagedObjectContext, загрузка данных из NSPersistentStore в память, операции с экземплярами.
5) NSManagedObject — объект модели данных.

Основной, на мой взгляд, неприятной особенностью всего этого чуда является то, что NSManagedObjectContext не thread-safe.

Инициализация стека

При больших размерах БД, при миграциях инициализация стека на главном потоке может занимать более 30 секунд. Это приведет к тому, что система просто убьет приложение. Выход есть, инициализировать стек в другом потоке.
```
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        // Создание NSManagedObjectModel
        NSURL *modelURL = [[NSBundle mainBundle] URLForResource:kModelFileName withExtension:@"momd"];
        NSManagedObjectModel *mom = [[NSManagedObjectModel alloc] initWithContentsOfURL:modelURL];
        
        // Создаем NSPersistentStoreCoordinator
        NSPersistentStoreCoordinator *psc =  [[NSPersistentStoreCoordinator alloc] initWithManagedObjectModel:mom];
        
        // Добавляем к NSPersistentStoreCoordinator хранилище, именно на этой операции приложение может висеть очень долго
        NSURL *doсURL = [[[NSFileManager defaultManager] URLsForDirectory:NSDocumentDirectory
                                                                     inDomains:NSUserDomainMask] lastObject];
        NSURL *storeURL = [doсURL URLByAppendingPathComponent:@"CoreData.sqlite"];
        NSError *error = nil;
        NSPersistentStore *store =  [psc addPersistentStoreWithType:NSSQLiteStoreType
                                  configuration:nil
                                            URL:storeURL
                                        options:nil
                                          error:&error];
       
       // Создание контекстов

});
```

Итак, наше приложение было запущено, юзер не получил лагов UI, все довольны. Следуем далее.

Создание главных контекстов

Как я писал выше, NSManagedObjectContext не thread-safe. Следовательно главный контекст приложения уместно держать именно на главном потоке. Но в таком случае будет тормозить UI при сохранении этого самого контекста, что же делать? А вот что, в iOS 6 появились типы NSManagedObjectContext.

1) NSMainQueueConcurrencyType – доступен исключительно с главного потока.
2) NSPrivateQueueConcurrencyType – работает на фоновом потоке.
3) NSConfinementConcurrencyType – работает на том потоке, на котором создали.

А также появилась возможность создавать дочерние контексты. Дочерний контекст при сохранении пушит все свои изменения родителю. Соответственно, теперь появилась возможность обустроить свой менеджер по работе с CoreData следующим образом.
```
// Этот код нужно вызывать после инициализации стека в том же потоке. 
// _daddyManagedObjectContext является настоящим отцом всех дочерних контекстов юзер кода, он приватен.

_daddyManagedObjectContext = [[NSManagedObjectContext alloc]  initWithConcurrencyType:NSPrivateQueueConcurrencyType];
    [_daddyManagedObjectContext setPersistentStoreCoordinator:psc];
    // Далее в главном потоке инициализируем main-thread context, он будет доступен пользователям
    dispatch_async(dispatch_get_main_queue(), ^{
        _defaultManagedObjectContext = [[NSManagedObjectContext alloc]  initWithConcurrencyType:NSMainQueueConcurrencyType];
        // Добавляем наш приватный контекст отцом, чтобы дочка смогла пушить все изменения
        [_defaultManagedObjectContext setParentContext:_daddyManagedObjectContext];
    });

```
На этом месте инициализация нашего менеджера по работе с Core Data заканчивается, теперь у нас есть скрытый от любопытных глаз отец-контекст, а также дочка на главном потоке.

Создание дочерних контекстов

Как можно легко догадаться, при работе на бекграунд-потоках мы можем создавать свои контексты, просто добавляя наш, созданный выше, дочерний контекст в качестве предка.
```
- (NSManagedObjectContext *)getContextForBGTask {
    NSManagedObjectContext *context = [[NSManagedObjectContext alloc] initWithConcurrencyType:NSPrivateQueueConcurrencyType];
    [context setParentContext:_defaultManagedObjectContext];
    return context;
}
```

Такой контекст при сохранении всегда будет сохранять изменения в своего родителя. Таким образом у нас всегда будет самая актуальная информация в _defaultManagedObjectContext (который пушит изменения в реального родителя).

Сохранение контекстов

Осталось самое главное — сохранение. К контекстам, которые живут на бекграунд-потоках обращаться можно только через performBlock: и performBlockAndWait:. Поэтому сохранение бекграунд потока будет выглядеть следующим образом.
```
- (void)saveContextForBGTask:(NSManagedObjectContext *)bgTaskContext {
    if (bgTaskContext.hasChanges) {
        [bgTaskContext performBlockAndWait:^{
            NSError *error = nil;
            [backgroundTaskContext save:&error];
        }];
       // Save default context
    }
}
```

После сохранения дочернего контекста необходимо сохранить родительский.
```
- (void)saveDefaultContext:(BOOL)wait {
    if (_defaultManagedObjectContext.hasChanges) {
        [_defaultManagedObjectContext performBlockAndWait:^{
            NSError *error = nil;
            [_defaultManagedObjectContext save:&error];
        }];
    }

    // А после сохранения _defaultManagedObjectContext необходимо сохранить его родителя, то есть _daddyManagedObjectContext
    void (^saveDaddyContext) (void) = ^{
        NSError *error = nil;
        [_daddyManagedObjectContext save:&error];
    };
    if ([_daddyManagedObjectContext hasChanges]) {
        if (wait)
            [_daddyManagedObjectContext performBlockAndWait:saveDaddyContext];
        else 
            [_daddyManagedObjectContext performBlock:saveDaddyContext];
    }
}

```


---
- [x] **Как при помощи DispatchGroup или DispatchSemaphore синхронизировать 3 URL запроса так, чтобы первые 2 выполнились параллельно, а третий после того как выполнятся первые два? А как при помощи reactive?**

dispatch_group is a part of Apple’s Grand Central Dispatch (GCD) set of APIs that allows you to coalesce and synchronise the results of multiple asynchronous functions into a single point of execution.

With dispatch_groups, a thread is blocked until all tasks associated with that group are finished. And once they are finished, normal execution resumes, and you can be safe in the knowledge that all your asynchronous code has finished running.

Suppose you have multiple async network calls, that are not dependent on each other, but you still need to wait for all the calls to complete before proceeding. A naïve implementation of this (that I’m guilty of 😅) would look like this:

```
func asyncFunctionA(){
	let urlRequest: NSURLRequest = ...
	let task = self.session?.dataTaskWithRequest(urlRequest, completionHandler: {
  	(data:NSData?, response:NSURLResponse?, error:NSError?) in

		self.taskAComplete = true
		self.checkResults()

    	})
}

func asyncFunctionB(){
	let urlRequest: NSURLRequest = ...
	let task = self.session?.dataTaskWithRequest(urlRequest, completionHandler: {
  	(data:NSData?, response:NSURLResponse?, error:NSError?) in

		self.taskBComplete = true
		self.checkResults()

    	})
}

func checkResults(){
	// Why does this function have to be called twice?!
	if taskAComplete && taskBComplete {
		//Handle Result
	}
}
```
Or perhaps you’d chain one function to another, in nested callback hell. You’d lose some of the asynchronous nature of your code in that case.

With dispatch_groups though, the implementation becomes much cleaner:
```
let dispatch_group: dispatch_group_t = dispatch_group_create()

func startRequests(){
	 asyncFunctionA()
	 asyncFunctionB()
	 dispatch_group_wait(dispatch_group, DISPATCH_TIME_FOREVER) // Wait forever! or set a reasonable default for timeout
}

func alsoStartRequests(){
	 asyncFunctionA()
	 asyncFunctionB()
	 dispatch_group_notify(dispatch_group, dispatch_get_main_queue()) { 
            // Single callback that is executed after all tasks are complete.
            if (self.errorA != nil) || (self.errorB != nil) {
            	//Handle error
            	return
            }
            self.processResponse(self.responseA, self.responseB)
     	}
}

func asyncFunctionA(){
	let urlRequest: NSURLRequest = ...
	dispatch_group_enter(dispatch_group)
	let task = self.session?.dataTaskWithRequest(urlRequest, completionHandler: {
  	(data:NSData?, response:NSURLResponse?, error:NSError?) in
  		self.errorA = error
  		self.responseA = response
		dispatch_group_leave(self.dispatch_group)
    	})
}

func asyncFunctionB(){
	let urlRequest: NSURLRequest = ...
	dispatch_group_enter(dispatch_group)
	let task = self.session?.dataTaskWithRequest(urlRequest, completionHandler: {
  	(data:NSData?, response:NSURLResponse?, error:NSError?) in
  		self.errorB = error
  		self.responseB = response
		dispatch_group_leave(self.dispatch_group)
    	})
}
```
Notice how there are no longer multiple unnecessary calls to a checkResults() function 😛

How it works
Before calling the different async functions, a dispatch_queue is first created. This could be an instance variable in your class, as above.

Next, in each function, before the execution becomes concurrent, a call to dispatch_group_enter is made. This tells GCD that a task was added.

When the async call returns with results, dispatch_group_leave is called. This tells GCD that a task was completed.

When all the entries are balanced out by the same number of leaves, the group is treated as finished, and the thread proceeds to the next step. Note that it is important that this balancing occurs — otherwise, the group will never finish.

Once the group is finished, there are two ways of handling it:

Using dispatch_group_wait: The thread pauses execution at this stage, until all tasks are complete. The next statement will only be executed after either that, or the timeout specified - whichever is earlier. This is the approach in startRequests().
Using a notification block: This provides you the opportunity of executing some code, on a specified thread, after all tasks are complete. This is the approach in alsoStartRequests().



---
- [x] **В чем отличие WKWebView и SafariController?**

UIWebView is deprecated.

From Apple Docs.

Important

Starting in iOS 8.0 and OS X 10.10, use WKWebView to add web content to your app. Do not use UIWebView or WebView.

@import WebKit And use WKWebView

WKWebView has much improved JS implementation and use. It is very similar in performance to Safari.

Differences: WKWebView can be used as a view of your apps controller. Safari runs as a separate app. SafariController runs Safari in it's own app within your app.


---
- [x] **С какими CI серверами работал, как решал проблему сертификации.**

Пока еще не работал.

---
- [x] **Что такое Fastlane.**

Fastlane  -  это инструмент для автоматизации процессов сборки и выкладки мобильных iOS и Android приложений, которая включает в себя также генерирование скриншотов, запуск Unit/UI тестов, отправка сообщений в Slack, подключение к Crashlytics и многие другие полезные вещи, которые упрощают жизнь. 


Какой профит?

На первоначальную настройку базовых команд для автоматизации выкладки приложения, например, для публикации в App Store или на TestFlight, уйдет не более двух часов, однако в будущем это сэкономит уйму времени, т.к. весь процесс будет запускаться одним вызовом из командной строки.


ВНИМАНИЕ: Для выполнения всех шагов необходима подписка Apple Developer, так как доступ в App Store Connect отсутствует для бесплатных аккаунтов.


---
- [x] **Чем отличается TestFlight от Fabric / Crashlitycs.**

Summary

Use Fabric Beta for distributing your alpha version to a small group of testers.
Use Apple TestFlight for distributing your beta version to a larger group of testers.
Fabric Beta Advantages

Distribution of your app to existing testers is quick and easy. You can release more often and get changes/fixes into your testers' hands sooner. You don't need to upload your app to iTunes, submit it to Beta Review, and wait for approval.

If you're already using Fabric's Answers Kit to track events, adding the kit is simple.

Fabric Beta Disadvantages

Onboarding a new tester is painful. Here is the general flow:

Add a new tester via the Fabric Mac App using their email.
The new tester receives an email inviting them to test the app.
The new tester accepts the invitation.
You get a notification email that they have accepted.
The new tester is prompted to install the Crashlytics iOS App.
After they install, you get a notification email with their device info (UUID).
You download the device info and upload it to iTunes Connect.
You open xCode and re-download your provisioning profile with the new device info.
You re-archive your app.
You re-distribute your app to this tester.
If you're recruiting more than just a few testers, this gets out of hand quickly.

You end up with a bunch of builds with the same build number. The code is the same, just the provisioning profile contains different device info. Tracking which testers are on which build becomes tedious.

The 2-step process creates a road block. The tester has to wait to download your app after they accept the invitation. You need to hold their hand a bit more and make sure they understand the process.

Installing the Crashlytics App can be a road block. It opens settings and asks them for permission to access their device info. Testers that you don't personally know may be wary of this and not make it past step 4. Then you may find yourself in a vortex of emails trying to convince them to trust you, that it's Crashlytics asking for permission and not you, that Crashlytics is trustworthy, and on and on...

Apple TestFlight Advantages

Onboarding a new user is pretty simple. The process is as follows:

Add a new tester via iTunes Connect using their email.
The new tester receives an email inviting them to test the app. This email prompts them to download the TestFlight app and gives them a redeem code to get your app.
The new tester downloads the TestFlight app.
The new user enters the redeem code.
The new tester downloads your app.
As you can see, there aren't any significant roadblocks between the invitation email and the tester downloading your app.

If a previous version of your app has been approved by Beta Review, a subsequent release that does not contain major changes does not need to be reviewed.

Apple TestFlight Disadvantages

You have to upload an archive of your app to iTunes Connect before releasing it. After uploading, your build enters the 'processing' state. Processing can take anywhere from a few minutes to forever.

Your app must be submitted to Beta Review and approved before you can distribute it. The review process can take anywhere from a couple hours to a week (according to anecdotes I've read).

Conclusion

Fabric Beta seems like the right choice if you're working with a small number of testers who you know personally and will be actively engaged in the testing process. After the painful onboarding process, you can push new versions and even new app projects to them very easily. It's great for getting your alpha version into the hands of a few.

Apple TestFlight is superior when you want to take your testing to the next level. If you're recruiting a large pool of people to test your beta app, TestFlight's more friendly onboarding process makes it the better choice. At this point your app should be almost fully functional, so getting it through Beta Review shouldn't be a problem.

Regardless of which service makes the most sense for you, don't forget to look at Fabric Answers (or a similar service) for tracking events. Direct feedback from testers is incredibly helpful, but collecting data on their in-app behaviors can definitely shed light on other important things.


---
- [x] **Как можно открыть приложение по клику на сайте или в письме?**

Универсальным линком или глубоким я думаю.

---
- [x] **Как работает git ignore?**

В большинстве проектов есть файлы или целые директории, в которые мы не хотим (и, скорее всего, не захотим) коммитить. Мы можем удостовериться, что они случайно не попадут в git add -A при помощи файла .gitignore

Создайте вручную файл под названием .gitignore и сохраните его в директорию проекта.
Внутри файла перечислите названия файлов/папок, которые нужно игнорировать, каждый с новой строки.
Файл .gitignore должен быть добавлен, закоммичен и отправлен на сервер, как любой другой файл в проекте.
Вот хорошие примеры файлов, которые нужно игнорировать:

Логи
Артефакты систем сборки
Папки node_modules в проектах node.js
Папки, созданные IDE, например, Netbeans или IntelliJ
Разнообразные заметки разработчика.
Файл .gitignore, исключающий все перечисленное выше, будет выглядеть так:
```
*.log
build/
node_modules/
.idea/
my_notes.txt
```
Символ слэша в конце некоторых линий означает директорию (и тот факт, что мы рекурсивно игнорируем все ее содержимое). Звездочка, как обычно, означает шаблон.


---
- [x] **Как работает git checkout?**
взять из репозитория какое-либо его состояние.

Команда git checkout -b <branch name> создает новую ветвь и переносит в эту ветвь рабочую область.
	
git checkout <branch> -- <path> извлекает файлы из другой ветви в рабочий каталог, так что можно взять изменения из другой ветви.

---
- [x] **Как работают таблицы (реюз)?**

Таблицы являются базой для большинства списков выбора на iOS. Почта, список контактов, последние звонки  - все эти приложения используют возможности класса UITableView. Чаще всего таблицы используются вместе с UINavigationController, который обеспечивает навигацию между таблицей и детальным представлением элемента, но бывают и исключения о которых я расскажу позже. Xcode содержит уже готовый шаблон (Navigation-Based Application) в котором имеются связанные контроллер навигации и табличное представление. Ниже представлен результат нашей будущей работы.

Для начала создадим новый проект, как я уже говорил из шаблона Navigation-Based Application и назовем его TableView.

Таблица имеет три основных составляющие: саму таблицу, разделы таблицы (или группировки) и ячейки таблицы (отдельные строки в таблице). Данные в таблицы помещяются в очередь из источника данных таблицы. Источник данных - это объект, предоставляющий таблице информацию о том, какие данные отображать, например, имена файлов, сообщения электронной почты и т. д. Для облегчения понимания работы с таблицей возмем обычный массив, в который будут включены только строки.

Чтобы создать наш источник данных в интерфейсе класса RootViewController объявите переменную students и укажите ей тип NSArray. Затем добавьте для этой переменной свойства и методы доступа (как это сделать я уже описывал здесь).

В методе viewDidLoad инициализируем массив на основании пяти объектов строкового типа, а панели навигации даем имя Students. Теперь наш метод viewDidLoad  выглядит так:

 
```
- (void)viewDidLoad
{
    [super viewDidLoad];
    self.title = @"Students";
    
    self.students = [NSArray arrayWithObjects:@"Tom", @"Bill", @"Tom", @"Joe", @"Tom", nil];
}
```
В первую очередь, следует указать сколько секций будет в нашей таблице. Делается это с помощью метода numberOfSectionsInTableView:

```
- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView
{
    return 1;
}
```

Чтобы "сказать" нашей таблице сколько в ней будет строк (а их будет столько же, сколько и элементов в источнике данных) используйте метод numberOfRowsInSection:

 
```
- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
{
    return [students count];
}
 ```

Теперь нам нужно заполнить ячейки нашей таблицы. Для заполнения воспользуемся методов cellForRowAtIndexPath. Он предоставляет нам “indexPath” (тип “NSIndexPath“). С помощью данного значения мы сможем выяснить текущий номер строки, чтобы заполнить ее элементом источника с таким же индексом. В самом методе cellForRowAtIndexPath уже имеется код, отвечающий за поиск и создание ячейки. Более того, Xcode вставил комментарий в том месте, где нам следует изменять ячейку ("// Configure the cell.") Давайте заменим этот комментарий на наш код. Теперь метод cellForRowAtIndexPath выглядит так:

 
```
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    static NSString *CellIdentifier = @"Cell";

    //Поиск ячейки
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:CellIdentifier];
    
    //Если ячейка не найдена
    if (cell == nil) {
        //Создание ячейки
        cell = [[[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault 
                                       reuseIdentifier:CellIdentifier] autorelease];
    }
    
    cell.textLabel.text = [students objectAtIndex:indexPath.row];
    
    return cell;
}
```

Наш пример готов к работе, можете смело запускать проект на выполнение и наслаждаться результатом.

Приведенный в примере код - это тот минимум, который необходимо знать для работы с базовыми таблицами. В следующем примере мы проведем более серьезную работу над таблицами.


---
- [x] **В какой момент autolayout изменяет frame у UIView.**

Forget that the frame property exists. Never set it directly. A view’s frame is the result of the Auto Layout process, not an input variable. To change the frame, change the constraints. This forces you to change the way you think about your UI. Rather than thinking in positions and sizes, think of the rules that specify a each view’s positioning in relation to its siblings and parents. It’s not unlike CSS.

[https://oleb.net/blog/2013/03/things-you-need-to-know-about-cocoa-autolayout/]


---
- [x] **Как работают связи (relationships) в CoreData. Чем отличаются No action, Nullify, Cascade, Deny(удаление)?**

For the purposes of this tutorial, I have created a simple project with Core Data Entities that will handle both One-To-One and One-To-Many relationships.

There are 3 Entities created in the example:

Person - this will be the main entity, that will have relationships with the Phone and Friends entities.
Phone - an entity that will keep the Person’s mobile phone information. It will be used as a One-To-One relationship, assuming that the Person has only one phone.
Friends - an entity that will keep all the Person’s friends. It will be used as a One-To-Many relationship, assuming that the person has more than one friend.

![image](https://cdn-images-1.medium.com/max/800/1*KmbyBDJ7i8rJ6gSkTyheRg.png)

As you can see in the above screenshot, I have already created the relationships. I will now explain to you how to that properly (it’s quite straightforward).👇

One-To-One Relationship (Person -> Phone)
If you have created the Entities we can proceed with creating the relationship between Person and Phone. You will need to add 3 values in order to create a relationship.

Relationship - name your relationship.
Destination - add the entity you want to establish a relationship with (in our case Phone).
Inverse - create an inverse relationship from Phone and pick it under Person. Apple recommends that you always add an inverse value, so never leave this empty.

![gif](https://cdn-images-1.medium.com/max/800/1*lyRZO4ll1jxynOJA-XGONQ.gif)
![gif](https://cdn-images-1.medium.com/max/800/1*0XV1pTdP4TiEiJPRlJFSrQ.gif)

Code
Each Entity contains its own automatically generated NSManagedObject that you can work within the code. This is one of the advantages of Core Data before others.

Here is an example how you can write in Person and its One-To-One Relationship (Phone).👇

```

let context = persistentContainer.viewContext

let person = Person(context: context)
person.firstName = "John"
person.lastName = "Doe"
        
let phone = Phone(context: context)
phone.brand = "Apple"
phone.model = "iPhone X"
phone.os = "iOS"
person.phone = phone
        
saveContext()
```

One-To-Many Relationship (Person -> Friends)
I hope that by far you understood how relationships work. Now we will go further and create a One-To-Many relationship. The concept is the same as the One-To-One relationship, just with some minor changes.

When creating a One-To-Many relationship, you will have to change the type to To Many from the Data Model Inspector. This isn’t the case with One-To-One because this type is set to To One by default.

![gif](https://cdn-images-1.medium.com/max/800/1*p9fOxbXPLgvWCr_yo5mEDw.gif)

Code
Here is an example how you can write in Person and its One-To-Many Relationship (Friends).👇
```
let context = persistentContainer.viewContext

let person = Person(context: context)
person.firstName = "John"
person.lastName = "Doe"
        
let friend1 = Friends(context: context)
friend1.firstName = "Friend"
friend1.lastName = "One"
person.addToFriends(friend1)
        
let friend2 = Friends(context: context)
friend2.firstName = "Friend"
friend2.lastName = "Two"
person.addToFriends(friend2)

saveContext()
```
The NSManagedObject contains generic methods like addToFriends() where you can pass either a Friends object or an array of Friends.
``
NOTE: The code that you saw in this tutorial is written in the AppDelegate for simplicity and to provide faster tests, due to the predefined context and Core Data save method.
``

Отвечая на вопрос, это все способы удаления, разницы почти нету.

---
- [x] **Как сохранить данные таким образом, чтобы они сохранились после удаления приложения?**

Keychain может сохранить все данные после удаления, не смотря на то что это используется для сохранения паролей ;)

---
- [x] **В каком потоке приходит нотификация - в котором отправили, или в котором подписались?**

В котором отправили.

