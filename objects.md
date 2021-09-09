# Objects

## 1.1-Объекты-Прототип

---

Прототип - объект, которому можно поручить поиск конкретного свойства.

Он помогает определить свойства и функциональные возможности.

Все это позволяет писать код в стиле ООР.

В javascript `объект` - это коллекция именованных свойств со значениями.

Чтобы не создавать много повторяющегося кода,

можно использовать `наследование` - расширение возвожностей одного объекта в другом.

`В javascript наследование реализуется через прототипы.`

Принцип `прототипа`: у каждого объекта может быть ссылка на свой прототип, то есть

на объект, в котором будет осуществляться поиск, если свойства нет в самом объекте.

У объектов можно легко проверять наличие свойства:

```
const airpalane = {
  fly: true,
};
const car = {
  drive: true,
};

console.log('fly' in airpalane); // true
console.log('fly' in car);       // false
```

## 1.2-Объекты-Метод Object.setPrototypeOf()

---

Метод `Object.setPrototypeOf(obj1, obj2)` позволяет утанавливать один объект

в качестве прототипа для второго. Так `obj2` станет прототипом для `obj1` и все свойства будут

доступны в первом.

```
const airpalane = {
  fly: true,
};
const car = {
  drive: true,
};
const ship = {
  swim: true,
};

Object.setPrototypeOf(car, airpalane);
Object.setPrototypeOf(ship, car);

console.log('fly' in airpalane); // true
console.log('fly' in car);       // true
console.log('fly' in ship);      // true
```

Ссылка на прототип `[[prototype]]` хранится в закрытом свойтсве обекта. Его из кода не получить,

поэтому используется метод Object.setPrototypeOf(парам1, парам2). Вторым параметром станет объект,

который выступит прототипом первого, то есть в нем будет искать свойства, не найденные в первом!

У каждого объекта может быть прототип, у прототипа свой прототип и так далее. Это называется

`цепочкой прототипов`.

## `1.3-Объекты-Функция-конструктор для создания объектов`

---

Экземпляр объекта одного и тогоже типа удобно создавать через функцию-конструктор.

Как и вдругих языках ООП в javaScript реализована операция `new`, позволяющая получать экземпляр

объекта через конструктор.

И у каждой функции-конструктора есть свой объект-прототип, который автоматически задается

как прототип для создаваемых конструктором объектов.

```
function Novelist() {}
```

Прототип может расширяться подобно любому объекту.

```
Novelist.prototype.canWrite = function () {
  return true;
};
```

```
const louis_ferdinand = new Novelist();

louis_ferdinand.canWrite(); // true
```

Новый экземпляр объекта получил метод из прототипа функции.

- Когда функция-конструктор вызывается через опереацию `new`, то в памяти создается новый объект,

становящийся ее контекстом и доступный по ссылке `this`. Собственно результатом возвращаемого значения

и становится ссылка на контекст.

- У прототипа функции есть свойство `constructor`, которое сожержит обратную ссылку на функцию.

## `1.4-Объекты-Свойства экземпляров`

---

Когда вызываем функцию через операцию `new`, то создаем новый экземпляр объекта, но его методы

можно определять не только через прототип, но и через параметр `this`.

```
function Novelist() {
  this.isWriter = true;
  this.canWrite = function () {
    return !this.isWriter;
  };
}
```

Если указать то же свойсто через прототип, то оно не перепишет:

```
Novelist.prototype.canWrite = function () {
  return true;
};
const louis_ferdinand = new Novelist();

louis_ferdinand.canWrite(); // false
```

Совойства, созданные в конструкторе непосредственно для экземпляра - приоритетнее.

## `1.5-Объекты-Побочные эффекты динамического характера`

---

Изменим порядок кода.

```
function Novelist() {
  this.isWriter = true;
}
const louis_ferdinand = new Novelist();

Novelist.prototype.canWrite = function () {
  return this.isWriter;
};

louis_ferdinand.canWrite(); // true
```

Здесь все как обычно.

А теперь полностью перепишем прототип :

```
Novelist.prototype = {
  speackFrench: function () {
    return true;
  },
};

sartre = new Novelist();

louis_ferdinand.canWrite(); // true
louis_ferdinand.speackFrench(); // error

sartre.canWrite(); //error
sartre.speackFrench(); //true

```

`louis_ferdinand` сохранил ссылку на свой предыдущий прототип, а `sartre` уже основан на

новом. Связь с прототипом утанавливается в момент создания экземпляра.

## `1.6-Объекты-Типизицая объектов через конструкторы`

---

Иногда полезно знать какая функция создала экземпляр объекта.

У функции-конструктора есть прототип, а у прототипа есть свойство `constructor`.

То есть конструктор объекта доступен через свойство `constructor`.

Это помогает контролировать типы объектов.

```
function Novelist() {
  this.isWriter = true;
}
const louis_ferdinand = new Novelist();
```

Проверка по `typeof` ничего особо не даст: все они и так объекты

```
typeof louis_ferdinand; // object
```

А вот проверка через операцию `instanceof` позволяет проверить был ли создан экземпляр

объекта с помощью конкретной функции-конструктора:

```
louis_ferdinand instanceof Novelist; // true

```

Помимо этого можно воспользоваться свойством `constructor` - это обратная ссылка на родительскую

функцию, которая вводится в каждый экземпляр.

```
louis_ferdinand.constructor === Novelist; // true

```

Основная польза от `constructor` как раз узнать истоник создания объекта.

Хотя можно даже создавать новые экземпляры через нее:

```
const sartre = new louis_ferdinand.constructor();

sartre instanceof Novelist; // true

sartre.constructor === Novelist; // true
```

## `1.7-Объекты-Достижение наследования`

---

`Наследование` - это форма повторного использования кода, в которой новые объекты получают

свойства уже существующих.

Важно не путать наследование с копированием.

```
function Novelist() {}
Novelist.prototype.canWrite = function () {
  return 'Yes, I can write.';
};

function Singer() {}
Singer.prototype = { canWrite: Novelist.prototype.canWrite };

const singer1 = new Singer();
```

Мы не можем таким способом сделать объект `singer1` наследователем `Novelist`.

Мы просто копируем в данном случае свойство.

```
singer1.canWrite();            // 'Yes, I can write.'
```

Но все это не имеет отношение к наследованию:

```
singer1 instanceof Novelist;   // false
```

По факту:

```
singer1 instanceof Singer;     // true
```

В вопросе наследования на самом деле следует создать `цепочку прототипов`.

Что-то вроде:

```
SubClass.prototype = new SuperClass();
```

То есть, например, есть класс `Animal` и подкласс `Mammal`:

```
function Animal() {}

Animal.prototype.canBreathe = function () {
  return true;
};

function Mammal() {}

Mammal.prototype = new Animal();

const dog = new Mammal();

```

```
dog instanceof Mammal; // true
```

идем выше:

```
dog instanceof Animal; // true
```

идем выше:

```
dog instanceof Object; // true
```

```
dog.canBreathe(); // true

typeof dog.canBreathe === 'function'; // true
```

Здесь функция-конструктор `Animal` создает новый экземпляр и именно он задается в качестве

прототипа для функции-конструктора типа `Mammal`:

```
Mammal.prototype = new Animal();

```

Связь с собственным [[prototype]] типа `Mammal` обрывается, этот прототип потом будет удален.

Ситуация со свойством `dog.canBreathe(); `

```
dog.canBreathe(); // true
```

Вызов идет так: поиск в самом `dog` (нет его )=> `Mammal` (нет его) => `Animal` (а тут нашли)
