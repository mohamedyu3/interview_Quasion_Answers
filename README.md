PHP / Laravel Interview Q&A Cheat Sheet
# 🟢 PHP / OOP
## الفرق بين Abstract Class و Interface و Trait
- Abstract Class: يحتوي على دوال Abstract + دوال عادية. يستخدم لما تحتاج كود مشترك + بعض الدوال الإلزامية.
- Interface: عقد فقط. كل كلاس ينفذه لازم يطبق جميع الدوال. يستخدم للـ Contracts.
- Trait: لإعادة استخدام كود مشترك بين أكثر من Class.
📌 مثال:
abstract class Vehicle {
    abstract public function move();
    public function fuel() { echo "Add fuel"; }
}
class Car extends Vehicle {
    public function move() { echo "Drive"; }
}

interface Payment {
    public function pay($amount);
}
class PayPal implements Payment {
    public function pay($amount) { echo "PayPal: $amount"; }
}

trait Logger {
    public function log($msg) { echo $msg; }
}
class User { use Logger; }
# 🟢 Laravel
## Relations
1. One To One: User ↔ Profile
2. One To Many: User ↔ Posts
3. Many To Many: Student ↔ Courses (جدول Pivot)
4. Has Many Through: Country ↔ Posts عبر Users
5. Polymorphic: صورة لمنتج أو مقال (imageable).
📌 مثال One To Many:
class User {
    public function posts() { return $this->hasMany(Post::class); }
}
class Post {
    public function user() { return $this->belongsTo(User::class); }
}
## Event / Listener / Queue
Event: 
class UserRegistered { public $user; ... }

Listener:
class SendWelcomeEmail { public function handle(UserRegistered $event){ ... } }

Queue:
class SendWelcomeEmail implements ShouldQueue { ... }
## N+1 Problem
❌ خطأ:
foreach(User::all() as $user) {
    echo $user->profile->name;
}
✅ الحل (Eager Loading):
$users = User::with('profile')->get();
foreach($users as $user) echo $user->profile->name;
## Observer
class UserObserver {
    public function created(User $user) {
        Profile::create(['user_id' => $user->id]);
    }
}

User::observe(UserObserver::class);
## Design Patterns في Laravel
### Table: Design Patterns in Laravel
| Pattern        | Purpose             | Where in Laravel   | Example                  |
|----------------|---------------------|-------------------|--------------------------|
| Repository     | Data access layer   | app/Repositories  | UserRepository@getByEmail|
| Service Layer  | Business logic      | app/Services      | OrderService@checkout    |
| Strategy       | Swap algorithms     | Auth, Payments    | PaymentInterface + PayPal/Stripe |
| Observer       | React to model events | Observers       | UserObserver@created     |
| Factory Method | Create objects      | Model Factories   | UserFactory              |
| Builder        | Build complex object| Query Builder     | User::where()->orderBy() |
| Singleton      | Single instance     | Container, DB     | app()->instance()        |
| Adapter        | Adapt APIs          | Integrations      | Wrap third-party library |
| Decorator      | Add behavior        | Middleware        | RateLimiter middleware   |
| Facade         | Static interface    | Facades           | Cache::remember()        |

- Repository → للتعامل مع DB.
- Service Layer → Business Logic.
- Strategy → Auth Drivers.
- Observer → Model Events.
- Singleton → App Container.
- Factory → Model Factories.
# 🟢 Database
## Index
Index = نسخة مرتبة (B-Tree) لتسريع البحث.
📌 مثال: CREATE INDEX idx_email ON users(email);
## Normalization
### Table: Normalization Levels
| Level | Rule                                  | Common violation        | Fix/Example                  |
|-------|---------------------------------------|-------------------------|------------------------------|
| 1NF   | No multi-valued columns               | phone1, phone2, phone3  | Create Phones table          |
| 2NF   | No partial dependency on composite PK | OrderItem stores ProductName | Move ProductName to Products |
| 3NF   | No transitive dependency              | Employees has DeptName with DeptID | Create Departments table |
| BCNF  | Every determinant is a candidate key  | Non-unique dependencies | Redesign keys/relations      |

1NF: إزالة القيم المكررة (Phones).
2NF: اعتماد كامل على المفتاح (ProductName → Products).
3NF: إزالة الاعتماد غير المباشر (DeptName → Departments).
# 🟢 SOLID Principles
1. S – Single Responsibility → كل كلاس له وظيفة واحدة.
2. O – Open/Closed → مفتوح للتوسع، مغلق للتعديل.
3. L – Liskov → SubClass ينفع يحل محل Parent.
4. I – Interface Segregation → قسم الـ Interface الكبيرة.
5. D – Dependency Inversion → اعتمد على Abstraction.
# 🟢 Design Patterns
### Table: Design Patterns in Laravel
| Pattern        | Purpose             | Where in Laravel   | Example                  |
|----------------|---------------------|-------------------|--------------------------|
| Repository     | Data access layer   | app/Repositories  | UserRepository@getByEmail|
| Service Layer  | Business logic      | app/Services      | OrderService@checkout    |
| Strategy       | Swap algorithms     | Auth, Payments    | PaymentInterface + PayPal/Stripe |
| Observer       | React to model events | Observers       | UserObserver@created     |
| Factory Method | Create objects      | Model Factories   | UserFactory              |
| Builder        | Build complex object| Query Builder     | User::where()->orderBy() |
| Singleton      | Single instance     | Container, DB     | app()->instance()        |
| Adapter        | Adapt APIs          | Integrations      | Wrap third-party library |
| Decorator      | Add behavior        | Middleware        | RateLimiter middleware   |
| Facade         | Static interface    | Facades           | Cache::remember()        |

1. Strategy → لاختيار خوارزمية (طرق الدفع).
2. Factory → لإنشاء Objects (Model Factory).
3. Builder → لبناء Object معقد (Query Builder).
4. Observer → مراقبة أحداث (Events, Listeners).
5. Singleton → كائن وحيد (DB::connection()).
# 🟢 Extra Interview Questions
- الفرق بين Repository و Service؟ Repository للتعامل مع DB / Service للـ Business Logic.
- ما هو Service Provider؟ مكان تسجيل الخدمات (bindings, observers).
- إزاي تمنع Race Condition؟ Transactions + lockForUpdate أو Redis Lock.
- الفرق بين Cache::remember و Cache::put؟ remember يتحقق ويحفظ / put يخزن مباشرة.
- Lazy Loading vs Eager Loading؟ Lazy → وقت الطلب / Eager → مسبقاً.
- أفضل Queue Driver؟ Redis أو SQS.
## جدول مقارنة: Abstract vs Interface vs Trait
### Table: Abstract vs Interface vs Trait
| Feature             | Abstract Class | Interface | Trait |
|---------------------|----------------|-----------|-------|
| Has normal methods  | ✔️              | ❌        | ✔️     |
| Has abstract methods| ✔️              | ✔️        | ❌     |
| Multiple inheritance| ❌              | ✔️        | ✔️     |
| Main usage          | Base + common logic | Contract | Code reuse |

## جدول مقارنة: Lazy Loading vs Eager Loading
### Table: Lazy Loading vs Eager Loading
| Aspect      | Lazy Loading          | Eager Loading                  |
|-------------|-----------------------|--------------------------------|
| Execution   | On demand             | Pre-fetched                    |
| Performance | Can cause N+1 problem | Faster when loading relations  |
| Example     | `$user->profile`      | `User::with('profile')->get()` |

# 🟢 OOP Core: Inheritance, Polymorphism, and Other Concepts
## Inheritance (الوراثة)
الوراثة تسمح لكلاس (Child) أن يرث خصائص ودوال من كلاس آخر (Parent) لإعادة استخدام الكود وتخصيص السلوك.
class Animal {
    public function sound() { echo "Some sound"; }
}
class Dog extends Animal {
    public function sound() { echo "Bark"; } // Override
}
$dog = new Dog();
$dog->sound(); // Bark
## Polymorphism (تعدد الأشكال)
نفس الواجهة (Interface) أو الدالة تُنفّذ بطرق مختلفة حسب الكلاس.
ملاحظة: PHP لا يدعم Overloading الحقيقي بتغيير التواقيع، لكن يمكنك استخدام معاملات افتراضية أو __call.
interface Shape { public function area(); }

class Circle implements Shape {
    public function area() { return 3.14 * 5 * 5; }
}
class Square implements Shape {
    public function area() { return 5 * 5; }
}

function printArea(Shape $s) { echo $s->area(); }
## Encapsulation (التغليف)
إخفاء التفاصيل الداخلية وجعل الوصول عبر واجهات (Getters/Setters).
class User {
    private string $name;
    public function __construct($name){ $this->name = $name; }
    public function name(){ return $this->name; } // Getter
}
## Abstraction (التجريد)
تركيز على (ما يجب فعله) وليس (كيف يتم فعله). نستخدم Abstract/Interface لتعريف العقود.
abstract class Notifier {
    abstract public function send(string $to, string $msg);
}
class EmailNotifier extends Notifier {
    public function send(string $to, string $msg){ /* ... */ }
}
## Composition over Inheritance (التركيب أفضل من الوراثة)
بدل ما تكبر شجرة الوراثة، ركّب كائنات داخل كائنات أخرى لمرونة أعلى.
class Engine { public function start(){ /* ... */ } }
class Car {
    public function __construct(private Engine $engine){}
    public function go(){ $this->engine->start(); }
}
# 🟢 جداول مقارنة إضافية
## جدول مقارنة: أنواع الفهارس (Indexes)
### Table: Types of Indexes
| Type       | How it works      | Common use            | Notes                           |
|------------|------------------|----------------------|---------------------------------|
| Primary    | B-Tree on PK     | Unique row id        | Only one per table              |
| Unique     | Prevents duplicates | Emails, usernames  | Allows one NULL in MySQL        |
| Composite  | Multi-column      | WHERE col1 AND col2  | Column order matters (leftmost) |
| Fulltext   | Text search       | Articles, content    | Use MATCH...AGAINST             |
| Spatial    | Geospatial data   | POINT, POLYGON       | For GIS data                    |

## جدول مقارنة: Normalization Levels
### Table: Normalization Levels
| Level | Rule                                  | Common violation        | Fix/Example                  |
|-------|---------------------------------------|-------------------------|------------------------------|
| 1NF   | No multi-valued columns               | phone1, phone2, phone3  | Create Phones table          |
| 2NF   | No partial dependency on composite PK | OrderItem stores ProductName | Move ProductName to Products |
| 3NF   | No transitive dependency              | Employees has DeptName with DeptID | Create Departments table |
| BCNF  | Every determinant is a candidate key  | Non-unique dependencies | Redesign keys/relations      |

## جدول: Design Patterns في Laravel وأين تُستخدم
### Table: Design Patterns in Laravel
| Pattern        | Purpose             | Where in Laravel   | Example                  |
|----------------|---------------------|-------------------|--------------------------|
| Repository     | Data access layer   | app/Repositories  | UserRepository@getByEmail|
| Service Layer  | Business logic      | app/Services      | OrderService@checkout    |
| Strategy       | Swap algorithms     | Auth, Payments    | PaymentInterface + PayPal/Stripe |
| Observer       | React to model events | Observers       | UserObserver@created     |
| Factory Method | Create objects      | Model Factories   | UserFactory              |
| Builder        | Build complex object| Query Builder     | User::where()->orderBy() |
| Singleton      | Single instance     | Container, DB     | app()->instance()        |
| Adapter        | Adapt APIs          | Integrations      | Wrap third-party library |
| Decorator      | Add behavior        | Middleware        | RateLimiter middleware   |
| Facade         | Static interface    | Facades           | Cache::remember()        |

## جدول مقارنة: Queue Drivers
### Table: Queue Drivers
| Driver   | Use case           | Pros                  | Cons/Notes               |
|----------|-------------------|-----------------------|--------------------------|
| sync     | Local testing     | No setup needed       | Not for production       |
| database | Small projects    | Easy setup            | Slower, more contention  |
| redis    | High throughput   | Fast, delayed jobs    | Requires Redis + monitoring |
| sqs      | Cloud/distributed | Scalable, reliable    | AWS dependency + cost    |

## جدول مقارنة: أنواع الـ JOIN
# 🟢 Q&A إضافية مهمة للمقابلة
❓ ما الفرق بين Repository و Service؟
✅ Repository: طبقة للوصول للبيانات (DB/API). Service: طبقة المنطق التجاري. اجمعهما لسهولة الاختبار وتغيير المصدر.
❓ ما هو Service Provider في Laravel؟
✅ مكان لتسجيل الـ bindings/observers/events والتهيئة العامة. يُستدعى عند الإقلاع.
❓ إزاي تمنع Race Condition؟
✅ استخدم معاملات + DB::transaction + lockForUpdate() أو قفل موزع عبر Redis. فكّر في مستويات العزل.
❓ Cache::remember vs Cache::put؟
✅ remember('key', ttl, fn): ينفذ الدالة لو القيمة مش موجودة ثم يخزنها. put('key', value, ttl): يخزن مباشرة.
❓ Lazy vs Eager Loading؟
✅ Lazy: تحميل عند الطلب وقد يسبب N+1. Eager: with() يقلل الاستعلامات لما تحتاج العلاقات.
❓ أفضل Queue Driver لـ High Load؟
✅ Redis داخل نفس البنية أو SQS سحابي للتوسع الأفقي.
❓ Sanctum vs Passport vs JWT؟
✅ Sanctum: بسيط للـ SPA/APIs. Passport: OAuth2 كامل. JWT: توكن مستقل مناسب للخدمات الصغيرة/الموزعة.
❓ متى أستخدم Transaction؟
✅ عند تحديث أكثر من جدول مرتبط (تحويل رصيد، إنشاء طلب وفواتيره). يضمن All-or-Nothing.
❓ مستويات العزل (Isolation Levels)؟
✅ Read Uncommitted/Committed, Repeatable Read, Serializable. InnoDB افتراضيًا Repeatable Read.
❓ ACID باختصار؟
✅ Atomicity, Consistency, Isolation, Durability. ضمان نزاهة العمليات.
❓ Pagination أداء أفضل؟
✅ استخدم keyset pagination (WHERE id > ?) بدل OFFSET كبير. أضف فهارس مناسبة.
❓ EXPLAIN كيف يفيد؟
✅ يعرض خطة التنفيذ: أي فهرس يُستخدم، نوع الانضمام، التقديرات. يساعد في تحسين الفهارس والاستعلام.
❓ ما هو Observer مقابل Event/Listener؟
✅ Observer مرتبط بموديل محدد لأحداثه (creating/updating). Event/Listener عام لأحداث متنوعة عبر النظام.
❓ DTO / Value Object؟
✅ كائن لنقل/تثبيت البيانات بدون منطق ثقيل. يقلل تسريب تفاصيل Eloquent لطبقات أخرى.
# 🟢 SOLID Principles (تفصيل + أمثلة + مشاكل)
## Single Responsibility Principle (SRP)
كل كلاس له وظيفة واحدة فقط.
المشكلة: God Class (كلاس ضخم وصعب الصيانة).
❌ خطأ:
class Invoice {
    public function calculateTotal() { /* حساب */ }
    public function printInvoice() { /* طباعة */ }
    public function sendEmail() { /* إيميل */ }
}

✅ صح:
class InvoiceCalculator { public function calculateTotal(){} }
class InvoicePrinter { public function print(Invoice $i){} }
class InvoiceMailer { public function send(Invoice $i){} }
## Open/Closed Principle (OCP)
الكود مفتوح للإضافة، مغلق للتعديل.
المشكلة: كل إضافة جديدة تكسر الكود القديم.
❌ خطأ:
class Payment {
    public function pay($type) {
        if ($type == 'paypal') {...}
        if ($type == 'stripe') {...}
    }
}

✅ صح (Strategy):
interface PaymentMethod { public function pay($a); }
class PayPal implements PaymentMethod { public function pay($a){} }
class Stripe implements PaymentMethod { public function pay($a){} }
class Payment {
    public function checkout(PaymentMethod $m,$a){ $m->pay($a); }
}
## Liskov Substitution Principle (LSP)
أي SubClass لازم ينفع يحل محل Parent بدون مشاكل.
المشكلة: الوراثة تكسر التوقعات.
❌ خطأ:
class Rectangle {
    public function setWidth($w){...}
    public function setHeight($h){...}
}
class Square extends Rectangle {
    public function setWidth($w){ $this->width=$this->height=$w; }
}

✅ الحل: لا ترث Square من Rectangle، اعمل Interface Shape وكل كلاس ينفذه بطريقته.
## Interface Segregation Principle (ISP)
لا تجبر الكلاسات على تنفيذ دوال مش محتاجاها.
المشكلة: واجهات ضخمة تجبر الكود على دوال فارغة.
❌ خطأ:
interface Machine { public function print(); public function scan(); public function fax(); }
class SimplePrinter implements Machine {
    public function print(){} public function scan(){} public function fax(){} // مش محتاج
}

✅ صح:
interface Printer{public function print();}
class SimplePrinter implements Printer{ public function print(){} }
## Dependency Inversion Principle (DIP)
اعتمد على Abstraction مش Implementation.
المشكلة: صعوبة تغيير أو استبدال Dependencies.
❌ خطأ:
class Order {
    private $paypal;
    public function __construct(){ $this->paypal=new PayPal(); }
}

✅ صح:
interface PaymentMethod { public function pay($a); }
class PayPal implements PaymentMethod { public function pay($a){} }
class Stripe implements PaymentMethod { public function pay($a){} }
class Order {
    private PaymentMethod $p;
    public function __construct(PaymentMethod $p){ $this->p=$p; }
}
