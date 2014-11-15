Ошибка -- в том, что метод `walk` присваивается в конструкторе `Animal` самому объекту вместо прототипа.

Казалось бы -- ничего страшного, ведь `Animal.apply(this, arguments)` создаст его. Код работает.

Однако, если мы решим перезаписать этот метод своим, специфичным для кролика, то будет сюрприз:

```js
//+ run
function Animal(name) {
  this.name = name;

  this.walk = function() { 
    alert("ходит " + this.name);
  };
}

function Rabbit(name) {
  Animal.apply(this, arguments);
}
Rabbit.prototype = Object.create(Animal.prototype);

*!*
Rabbit.prototype.walk = function() { 
  alert("прыгает! и ходит: " + this.name);
};
*/!*

var rabbit = new Rabbit("Кроль");
*!*
rabbit.walk(); // ходит Кроль
*/!*
```

Переопределение не сработало!

Причина -- метод `walk` создаётся в конструкторе, записывается в сам объект и тем самым игнорирует цепочку прототипов и говорит "гуд-бай" всей иерархии наследования.

Правильно было бы определять `walk` как `Animal.prototype.walk`.

Тем более, что этот метод является общим для всех объектов, тратить память и время на запись его в каждый конструктор определённо ни к чему.