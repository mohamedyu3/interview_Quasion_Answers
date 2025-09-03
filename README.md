PHP / Laravel Interview Q&A Cheat Sheet

# 🟢 PHP / OOP

## الفرق بين Abstract Class و Interface و Trait

\- Abstract Class: يحتوي على دوال Abstract + دوال عادية. يستخدم لما تحتاج كود مشترك + بعض الدوال الإلزامية.  
\- Interface: عقد فقط. كل كلاس ينفذه لازم يطبق جميع الدوال. يستخدم للـ Contracts.  
\- Trait: لإعادة استخدام كود مشترك بين أكثر من Class.

📌 مثال:

abstract class Vehicle {  
abstract public function move();  
public function fuel() { echo "Add fuel"; }  
}  
class Car extends Vehicle {  
public function move() { echo "Drive"; }  
}  
<br/>interface Payment {  
public function pay($amount);  
}  
class PayPal implements Payment {  
public function pay($amount) { echo "PayPal: $amount"; }  
}  
<br/>trait Logger {  
public function log($msg) { echo $msg; }  
}  
class User { use Logger; }

# 🟢 Laravel

## Relations

1\. One To One: User ↔ Profile  
2\. One To Many: User ↔ Posts  
3\. Many To Many: Student ↔ Courses (جدول Pivot)  
4\. Has Many Through: Country ↔ Posts عبر Users  
5\. Polymorphic: صورة لمنتج أو مقال (imageable).

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
<br/>Listener:  
class SendWelcomeEmail { public function handle(UserRegistered $event){ ... } }  
<br/>Queue:  
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
Profile::create(\['user_id' => $user->id\]);  
}  
}  
<br/>User::observe(UserObserver::class);

## Design Patterns في Laravel

\- Repository → للتعامل مع DB.  
\- Service Layer → Business Logic.  
\- Strategy → Auth Drivers.  
\- Observer → Model Events.  
\- Singleton → App Container.  
\- Factory → Model Factories.

# 🟢 Database

## Index

Index = نسخة مرتبة (B-Tree) لتسريع البحث.  
📌 مثال: CREATE INDEX idx_email ON users(email);

## Normalization

1NF: إزالة القيم المكررة (Phones).  
2NF: اعتماد كامل على المفتاح (ProductName → Products).  
3NF: إزالة الاعتماد غير المباشر (DeptName → Departments).

# 🟢 SOLID Principles

1\. S – Single Responsibility → كل كلاس له وظيفة واحدة.  
2\. O – Open/Closed → مفتوح للتوسع، مغلق للتعديل.  
3\. L – Liskov → SubClass ينفع يحل محل Parent.  
4\. I – Interface Segregation → قسم الـ Interface الكبيرة.  
5\. D – Dependency Inversion → اعتمد على Abstraction.

# 🟢 Design Patterns

1\. Strategy → لاختيار خوارزمية (طرق الدفع).  
2\. Factory → لإنشاء Objects (Model Factory).  
3\. Builder → لبناء Object معقد (Query Builder).  
4\. Observer → مراقبة أحداث (Events, Listeners).  
5\. Singleton → كائن وحيد (DB::connection()).

# 🟢 Extra Interview Questions

\- الفرق بين Repository و Service؟ Repository للتعامل مع DB / Service للـ Business Logic.  
\- ما هو Service Provider؟ مكان تسجيل الخدمات (bindings, observers).  
\- إزاي تمنع Race Condition؟ Transactions + lockForUpdate أو Redis Lock.  
\- الفرق بين Cache::remember و Cache::put؟ remember يتحقق ويحفظ / put يخزن مباشرة.  
\- Lazy Loading vs Eager Loading؟ Lazy → وقت الطلب / Eager → مسبقاً.  
\- أفضل Queue Driver؟ Redis أو SQS.

## جدول مقارنة: Abstract vs Interface vs Trait

| الميزة | Abstract Class | Interface | Trait |
| --- | --- | --- | --- |
| يحتوي على دوال عادية | ✔️  | ❌   | ✔️  |
| يحتوي على دوال Abstract | ✔️  | ✔️  | ❌   |
| وراثة متعددة | ❌   | ✔️ (يمكن تنفيذ أكثر من Interface) | ✔️ (يمكن استخدام أكثر من Trait) |
| الاستخدام الرئيسي | أساس + كود مشترك | عقد (Contract) | إعادة استخدام Methods |

## جدول مقارنة: Lazy Loading vs Eager Loading

| النقطة | Lazy Loading | Eager Loading |
| --- | --- | --- |
| التنفيذ | يُنفذ عند الطلب | يُنفذ مسبقاً |
| الأداء | ممكن يعمل N+1 | أسرع عند جلب بيانات كثيرة |
| الكود | $user->profile | User::with('profile')->get() |

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
ملاحظة: PHP لا يدعم Overloading الحقيقي بتغيير التواقيع، لكن يمكنك استخدام معاملات افتراضية أو \__call.

interface Shape { public function area(); }  
<br/>class Circle implements Shape {  
public function area() { return 3.14 \* 5 \* 5; }  
}  
class Square implements Shape {  
public function area() { return 5 \* 5; }  
}  
<br/>function printArea(Shape $s) { echo $s->area(); }

## Encapsulation (التغليف)

إخفاء التفاصيل الداخلية وجعل الوصول عبر واجهات (Getters/Setters).

class User {  
private string $name;  
public function \__construct($name){ $this->name = $name; }  
public function name(){ return $this->name; } // Getter  
}

## Abstraction (التجريد)

تركيز على (ما يجب فعله) وليس (كيف يتم فعله). نستخدم Abstract/Interface لتعريف العقود.

abstract class Notifier {  
abstract public function send(string $to, string $msg);  
}  
class EmailNotifier extends Notifier {  
public function send(string $to, string $msg){ /\* ... \*/ }  
}

## Composition over Inheritance (التركيب أفضل من الوراثة)

بدل ما تكبر شجرة الوراثة، ركّب كائنات داخل كائنات أخرى لمرونة أعلى.

class Engine { public function start(){ /\* ... \*/ } }  
class Car {  
public function \__construct(private Engine $engine){}  
public function go(){ $this->engine->start(); }  
}

# 🟢 جداول مقارنة إضافية

## جدول مقارنة: أنواع الفهارس (Indexes)

| النوع | كيف يعمل باختصار | الاستخدام الشائع | ملاحظات |
| --- | --- | --- | --- |
| Primary | B-Tree على المفتاح الأساسي | تحديد الصف uniquely | مفتاح وحيد لكل جدول |
| Unique | B-Tree يمنع التكرار | البريد الإلكتروني/الهويات الفريدة | يسمح بـ NULL مرة واحدة في MySQL |
| Composite | Index على أكثر من عمود | WHERE col1 AND col2 | ترتيب الأعمدة مهم (leftmost) |
| Fulltext | محرك بحث نصّي | البحث في المقالات/الوصف | MATCH ... AGAINST للنص |
| Spatial (GIS) | فهرسة بيانات مكانية | نقاط/مضلعات جغرافية | تُستخدم مع أعمدة POINT/POLYGON |

## جدول مقارنة: Normalization Levels

| المستوى | القاعدة | مخالفة شائعة | التصحيح/المثال |
| --- | --- | --- | --- |
| 1NF | لا توجد أعمدة متعددة القيم + نوع واحد لكل عمود | phone1/phone2/phone3 | إنشاء جدول Phones (user_id, phone) |
| 2NF | لا اعتمادية جزئية على جزء من المفتاح المركب | OrderItem يخزن ProductName | ProductName في جدول Products ويرتبط بالمفتاح |
| 3NF | لا اعتمادية انتقالية (غير مباشرة) | Employees يخزن DeptName مع DeptID | نقل DeptName إلى Departments |
| BCNF | كل محدِّد وظيفي يجب أن يكون مفتاحًا | جداول بروابط غير فريدة | إعادة تصميم المفاتيح/العلاقات |

## جدول: Design Patterns في Laravel وأين تُستخدم

| Pattern | الهدف | أين في Laravel | مثال سريع |
| --- | --- | --- | --- |
| Repository | فصل Data Access | app/Repositories | UserRepository@getByEmail |
| Service Layer | Business Logic | app/Services | OrderService@checkout |
| Strategy | تبديل خوارزميات | Auth/Payments | PaymentInterface + PayPal/Stripe |
| Observer | رد فعل لأحداث الموديل | Observers | UserObserver@created |
| Factory Method | إنشاء كائنات | Model Factories | UserFactory |
| Builder | بناء كائن/استعلام | Query Builder/Eloquent | User::where()->orderBy()->get() |
| Singleton | نسخة وحيدة | Container/DB | app()->instance('key', obj) |
| Adapter | توافق APIs مختلفة | Integrations | لفّ مكتبة طرف ثالث |
| Decorator | إضافة سلوك ديناميكي | Middleware | RateLimiter Middleware |
| Facade | واجهة ثابتة لخدمة | Facades | Cache::remember(...) |

## جدول مقارنة: Queue Drivers

| Driver | مناسب لـ | مميزات | ملاحظات |
| --- | --- | --- | --- |
| sync | تجارب محلية فقط | بلا إعدادات | لا يُستخدم للإنتاج |
| database | مشاريع صغيرة | سهل الضبط | أبطأ وتنافسية أعلى |
| redis | High Throughput | أداء عالي + Delayed | يتطلب Redis + مراقبة |
| sqs | سحابي/موزع | قابلية توسع عالية | اعتماد على AWS + تكلفة |

## جدول مقارنة: أنواع الـ JOIN

| النوع | يرجع | مثال SQL | متى تستخدم |
| --- | --- | --- | --- |
| INNER | السجلات المتقاطعة فقط | SELECT ... FROM A INNER JOIN B ON ... | عند الحاجة للمشترك فقط |
| LEFT | كل A + المتطابق من B | SELECT ... FROM A LEFT JOIN B ON ... | الحفاظ على كل سجلات A |
| RIGHT | كل B + المتطابق من A | SELECT ... FROM A RIGHT JOIN B ON ... | نادرًا في التصميم الجيد |
| FULL (إن وُجد) | كل A وB | SELECT ... FULL OUTER JOIN ... | بدائل بـ UNION في MySQL |

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
public function calculateTotal() { /\* حساب \*/ }  
public function printInvoice() { /\* طباعة \*/ }  
public function sendEmail() { /\* إيميل \*/ }  
}  
<br/>✅ صح:  
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
<br/>✅ صح (Strategy):  
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
<br/>✅ الحل: لا ترث Square من Rectangle، اعمل Interface Shape وكل كلاس ينفذه بطريقته.

## Interface Segregation Principle (ISP)

لا تجبر الكلاسات على تنفيذ دوال مش محتاجاها.  
المشكلة: واجهات ضخمة تجبر الكود على دوال فارغة.

❌ خطأ:  
interface Machine { public function print(); public function scan(); public function fax(); }  
class SimplePrinter implements Machine {  
public function print(){} public function scan(){} public function fax(){} // مش محتاج  
}  
<br/>✅ صح:  
interface Printer{public function print();}  
class SimplePrinter implements Printer{ public function print(){} }

## Dependency Inversion Principle (DIP)

اعتمد على Abstraction مش Implementation.  
المشكلة: صعوبة تغيير أو استبدال Dependencies.

❌ خطأ:  
class Order {  
private $paypal;  
public function \__construct(){ $this->paypal=new PayPal(); }  
}  
<br/>✅ صح:  
interface PaymentMethod { public function pay($a); }  
class PayPal implements PaymentMethod { public function pay($a){} }  
class Stripe implements PaymentMethod { public function pay($a){} }  
class Order {  
private PaymentMethod $p;  
public function \__construct(PaymentMethod $p){ $this->p=$p; }  
}
