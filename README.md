# PHP / Laravel Interview Q&A Cheat Sheet

## 🟢 PHP / OOP

### الفرق بين Abstract Class و Interface و Trait
- **Abstract Class**: يحتوي على دوال Abstract + دوال عادية. يستخدم لما تحتاج كود مشترك + بعض الدوال الإلزامية.  
- **Interface**: عقد فقط. كل كلاس ينفذه لازم يطبق جميع الدوال. يستخدم للـ Contracts.  
- **Trait**: لإعادة استخدام كود مشترك بين أكثر من Class.  

📌 **مثال:**
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
