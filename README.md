PHP / Laravel Interview Q&A Cheat Sheet
# 🟢 PHP / OOP
## الفرق بين Abstract Class و Interface و Trait
- Abstract Class: يحتوي على دوال Abstract + دوال عادية. يستخدم لما تحتاج كود مشترك + بعض الدوال الإلزامية.
- Interface: عقد فقط. كل كلاس ينفذه لازم يطبق جميع الدوال. يستخدم للـ Contracts.
- Trait: لإعادة استخدام كود مشترك بين أكثر من Class.
📌 مثال:
```php
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
```
# 🟢 Laravel
## Relations
1. One To One: User ↔ Profile
2. One To Many: User ↔ Posts
3. Many To Many: Student ↔ Courses (جدول Pivot)
4. Has Many Through: Country ↔ Posts عبر Users
5. Polymorphic: صورة لمنتج أو مقال (imageable).
📌 مثال One To Many:
```php
class User {
    public function posts() { return $this->hasMany(Post::class); }
}
class Post {
    public function user() { return $this->belongsTo(User::class); }
}
```
## Event / Listener / Queue
Event: 
class UserRegistered { public $user; ... }

Listener:
class SendWelcomeEmail { public function handle(UserRegistered $event){ ... } }

Queue:
class SendWelcomeEmail implements ShouldQueue { ... }
## N+1 Problem
```php
❌ خطأ:
```
foreach(User::all() as $user) {
    echo $user->profile->name;
}
```php
✅ الحل (Eager Loading):
```
$users = User::with('profile')->get();
foreach($users as $user) echo $user->profile->name;
## Observer
```php
class UserObserver {
    public function created(User $user) {
        Profile::create(['user_id' => $user->id]);
    }
}

User::observe(UserObserver::class);
```
## Design Patterns في Laravel
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
## جدول مقارنة: Lazy Loading vs Eager Loading
# 🟢 OOP Core: Inheritance, Polymorphism, and Other Concepts
## Inheritance (الوراثة)
الوراثة تسمح لكلاس (Child) أن يرث خصائص ودوال من كلاس آخر (Parent) لإعادة استخدام الكود وتخصيص السلوك.
```php
class Animal {
    public function sound() { echo "Some sound"; }
}
class Dog extends Animal {
    public function sound() { echo "Bark"; } // Override
}
$dog = new Dog();
$dog->sound(); // Bark
```
## Polymorphism (تعدد الأشكال)
نفس الواجهة (Interface) أو الدالة تُنفّذ بطرق مختلفة حسب الكلاس.
ملاحظة: PHP لا يدعم Overloading الحقيقي بتغيير التواقيع، لكن يمكنك استخدام معاملات افتراضية أو __call.
```php
interface Shape { public function area(); }

class Circle implements Shape {
    public function area() { return 3.14 * 5 * 5; }
}
class Square implements Shape {
    public function area() { return 5 * 5; }
}

function printArea(Shape $s) { echo $s->area(); }
```
## Encapsulation (التغليف)
إخفاء التفاصيل الداخلية وجعل الوصول عبر واجهات (Getters/Setters).
```php
class User {
    private string $name;
    public function __construct($name){ $this->name = $name; }
    public function name(){ return $this->name; } // Getter
}
```
## Abstraction (التجريد)
تركيز على (ما يجب فعله) وليس (كيف يتم فعله). نستخدم Abstract/Interface لتعريف العقود.
```php
abstract class Notifier {
    abstract public function send(string $to, string $msg);
}
class EmailNotifier extends Notifier {
    public function send(string $to, string $msg){ /* ... */ }
}
```
## Composition over Inheritance (التركيب أفضل من الوراثة)
بدل ما تكبر شجرة الوراثة، ركّب كائنات داخل كائنات أخرى لمرونة أعلى.
```php
class Engine { public function start(){ /* ... */ } }
class Car {
    public function __construct(private Engine $engine){}
    public function go(){ $this->engine->start(); }
}
```
# 🟢 جداول مقارنة إضافية
## جدول مقارنة: أنواع الفهارس (Indexes)
## جدول مقارنة: Normalization Levels
## جدول: Design Patterns في Laravel وأين تُستخدم
## جدول مقارنة: Queue Drivers
## جدول مقارنة: أنواع الـ JOIN
# 🟢 Q&A إضافية مهمة للمقابلة
❓ ما الفرق بين Repository و Service؟
```php
✅ Repository: طبقة للوصول للبيانات (DB/API). Service: طبقة المنطق التجاري. اجمعهما لسهولة الاختبار وتغيير المصدر.
```
❓ ما هو Service Provider في Laravel؟
```php
✅ مكان لتسجيل الـ bindings/observers/events والتهيئة العامة. يُستدعى عند الإقلاع.
```
❓ إزاي تمنع Race Condition؟
```php
✅ استخدم معاملات + DB::transaction + lockForUpdate() أو قفل موزع عبر Redis. فكّر في مستويات العزل.
```
❓ Cache::remember vs Cache::put؟
```php
✅ remember('key', ttl, fn): ينفذ الدالة لو القيمة مش موجودة ثم يخزنها. put('key', value, ttl): يخزن مباشرة.
```
❓ Lazy vs Eager Loading؟
```php
✅ Lazy: تحميل عند الطلب وقد يسبب N+1. Eager: with() يقلل الاستعلامات لما تحتاج العلاقات.
```
❓ أفضل Queue Driver لـ High Load؟
```php
✅ Redis داخل نفس البنية أو SQS سحابي للتوسع الأفقي.
```
❓ Sanctum vs Passport vs JWT؟
```php
✅ Sanctum: بسيط للـ SPA/APIs. Passport: OAuth2 كامل. JWT: توكن مستقل مناسب للخدمات الصغيرة/الموزعة.
```
❓ متى أستخدم Transaction؟
```php
✅ عند تحديث أكثر من جدول مرتبط (تحويل رصيد، إنشاء طلب وفواتيره). يضمن All-or-Nothing.
```
❓ مستويات العزل (Isolation Levels)؟
```php
✅ Read Uncommitted/Committed, Repeatable Read, Serializable. InnoDB افتراضيًا Repeatable Read.
```
❓ ACID باختصار؟
```php
✅ Atomicity, Consistency, Isolation, Durability. ضمان نزاهة العمليات.
```
❓ Pagination أداء أفضل؟
```php
✅ استخدم keyset pagination (WHERE id > ?) بدل OFFSET كبير. أضف فهارس مناسبة.
```
❓ EXPLAIN كيف يفيد؟
```php
✅ يعرض خطة التنفيذ: أي فهرس يُستخدم، نوع الانضمام، التقديرات. يساعد في تحسين الفهارس والاستعلام.
```
❓ ما هو Observer مقابل Event/Listener؟
```php
✅ Observer مرتبط بموديل محدد لأحداثه (creating/updating). Event/Listener عام لأحداث متنوعة عبر النظام.
```
❓ DTO / Value Object؟
```php
✅ كائن لنقل/تثبيت البيانات بدون منطق ثقيل. يقلل تسريب تفاصيل Eloquent لطبقات أخرى.
```
# 🟢 SOLID Principles (تفصيل + أمثلة + مشاكل)
## Single Responsibility Principle (SRP)
كل كلاس له وظيفة واحدة فقط.
المشكلة: God Class (كلاس ضخم وصعب الصيانة).
```php
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
```
## Open/Closed Principle (OCP)
الكود مفتوح للإضافة، مغلق للتعديل.
المشكلة: كل إضافة جديدة تكسر الكود القديم.
```php
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
```
## Liskov Substitution Principle (LSP)
أي SubClass لازم ينفع يحل محل Parent بدون مشاكل.
المشكلة: الوراثة تكسر التوقعات.
```php
❌ خطأ:
class Rectangle {
    public function setWidth($w){...}
    public function setHeight($h){...}
}
class Square extends Rectangle {
    public function setWidth($w){ $this->width=$this->height=$w; }
}

✅ الحل: لا ترث Square من Rectangle، اعمل Interface Shape وكل كلاس ينفذه بطريقته.
```
## Interface Segregation Principle (ISP)
لا تجبر الكلاسات على تنفيذ دوال مش محتاجاها.
المشكلة: واجهات ضخمة تجبر الكود على دوال فارغة.
```php
❌ خطأ:
interface Machine { public function print(); public function scan(); public function fax(); }
class SimplePrinter implements Machine {
    public function print(){} public function scan(){} public function fax(){} // مش محتاج
}

✅ صح:
interface Printer{public function print();}
class SimplePrinter implements Printer{ public function print(){} }
```
## Dependency Inversion Principle (DIP)
اعتمد على Abstraction مش Implementation.
المشكلة: صعوبة تغيير أو استبدال Dependencies.
```php
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
```
