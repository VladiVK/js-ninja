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

## `1.8-Объекты-Проблемы при переопределении свойства Constructor`

---

У нас выше есть дынный код:

```
function Mammal() {}

Mammal.prototype = new Animal();

const dog = new Mammal();
```

Дело в том, что мы таким образом потеряли связь с конструктором типа `Mammal`.

Так как свойство `constructor` может быть использовано для поиска функции, с помощью которой

был создан объект, то могут возникнуть проблемы:

```
dog.constructor === Mammal; // false

dog.constructor === Animal; // true
```

По сути не верно, так как код был следующим `const dog = new Mammal();`

Такую ситуацию следует исправлять самостоятельно. Об этом ниже...

## `1.9-Объекты-Настройка параметров свойств объектов`

---

B JavaScript каждое свойство объекта описывается специальным `дескриптором свойства`,

через который можно изменить следующие поля:

|                |                                                                                                                                                                             |
| -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `configurable` | если стоит `true`, то дескриптор свойства может быть изменен, а само свойство удалено. Если false, то все это невозможно                                                    |
| `enumerable`   | если стоит true, то при переборе в for in это свойство доступно                                                                                                             |
| `value`        | здесть просто значение свойства. По умолчанию стоит undefined                                                                                                               |
| `writable`     | при значении true, то значение свойства может быть изменено через присваивание                                                                                              |
| `get`          | определяется _`метод получения`_, который вызывается при доступе к свойству. Поле нельзя определять вместе с полями `value` и `writable`                                    |
| `set`          | определяется _`метод установки`_, который вызывается каждый раз, если требуестся присвоить конкретное значение. Поле нельзя определять вместе с полями `value` и `writable` |

Например:

```
dog.name = 'Fred';
```

Данное свойство будет настраиваемым (`configurable`), перечисляемым (`enumerable`) и

доступным по записи (`writable`). Полю (`value`) будет присвоено значение, а методы `get` и

`set` останутся неопределенными (`undefined`).

А вот для более тонкой настройки следует использовать метод:

- ### `Object.defineProperty()`

Используем новое свойство:

Сначала создадим объект с парой свойств.

```
const cat = {};
cat.name = 'Nika';
cat.age = '1';
```

Теперь добавим свойство `'hasKittens'` с настройками:

```
Object.defineProperty(cat, 'hasKittens', {
  configurable: false,
  enumerable: false,
  value: true,
  writable: true,
});
```

Свойство ненастраиваемо и неперечисляемое:

```
  configurable: false,
  enumerable: false,
```

Значение свойства доступно и изменеямо:

```
  value: true,
  writable: true
```

То есть:

```
cat.hasKittens; // true
cat.hasKittens = false;

cat.hasKittens; // false
```

Все это удобно использовать для того, чтобы не терять свойство `constructor`.

Вернемся к старой проблеме:

```
function Animal() {}

Animal.prototype.canBreathe = function () {
  return true;
};

function Mammal() {}

Mammal.prototype = new Animal();

Object.defineProperty(Mammal.prototype, 'constructor', {
  enumerable: false,
  value: Mammal,
  writable: true,
});

const dog = new Mammal();

```

Теперь мы без путаницы можем предложить любому разработчику узнать функцию-конструктор

объекта `dog`:

```
dog instanceof Mammal; // true
dog instanceof Animal; // true
dog instanceof Object; // true

НО  !!!!!!!

dog.constructor === Mammal; //true
dog.constructor === Animal; //false

```

То есть мы востанови связь экземпляра с его конструктором.

## `1.10 - Объекты - Опереация instanceof`

---

В большинстве языков программирования самый простой способ проверить относится ли

конкретный объект к определенной иерархии классов, состоит в том, чтобы

использовать операцию `instanceof`.

В `JavaScript` все немного сложнее: данная операция идет по цепочки прототипов.

То есть проверка скажет находится ли текущий прототип фугкции-конструктора в

цепочке прототипов экземпляра. Мы это видили выше:

```
dog instanceof Mammal; // true
dog instanceof Animal; // true
```

И хотя операция `instanceof` чаще всего используется для определения функции-конструктора,

на самом деле она действует иначе: она отвечает на вопрос, находится ли прототип функции

в правой части операции в цепочки прототипов объекта в левой части операции.

Все из-за того, что `JavaScript` динамический: во время выполнения программы можно

можно много чего поменять.

```
function Soldiers() {}

const rembo = new Soldiers();

Soldiers.prototype = {};
```

Экземпляр создан, но прототип функции-конструктора потом переопределен.

То есть на текущий момент `rembo` не имеет в своей цепочки прототипов функцию `Soldiers`.

Снача была, а теперь - нет:

```
rembo instanceof Soldiers; // false
```

Хотя свой конструктор он помнит из-за контекста вызова:

```
rembo.constructor === Soldiers; // true
```

То есть `НАСТОЯЩАЯ СЕМАНТИКА` операции `instanceof` ориентирована не на поиск функции-конструктора

для экземпляра, а для проверки находится ли этот прототип в цепочке прототипов экземпляра.

## `2 - Объекты - "Классы" стандарта ES6`

---

В `Javascript` наследование происходит с помощью прототипов. Но все хотели классы для большей

абстракции и упрощения системы наследования. Многи стали пользоваться библиотеками-симуляторами

классов, поэтому решили систематизировать синтаксис. "Классы" введены, но система наследования

прототипов осталась под капотом.

```
class Mammal {
  constructor(name) {
    this.name = name;
  }

  canBreathe() {
    return true;
  }
}

const dog = new Mammal('Fred');
```

C помощью ключевого слова `class` мы создаем новый класс. С помощью классов можно явно

задать функцию-конструктор, которая и будет вызываться при получении экземпляра объекта.

В конструкторе черз ключевой слово `this` получаем доступ к экземпляру и можем вводить новые свойства,

а в самом теле класса вводим методы, доступные всем экземплярам.

```
dog instanceof Mammal; // true
dog.name; // 'Fred'
dog.canBreathe(); // true
```

Все это синтаксической удобство под капотом выглядит так:

```
function Mammal(name) {
  this.name = name;
}
Mammal.prototype.canBreathe = function () {
  return true;
};

const dog = new Mammal('Fred');
```

## `2.1 - Объекты - Статические методы`

---

В классических объектно-ориентированных языках на уровне классов можно определять ститические методы.

```
class Sportsmen {
  constructor(name, level) {
    this.name = name;
    this.level = level;
  }

  canBreathe() {
    return true;
  }

  static compare(sportsman1, sportsman2) {
    return sportsman1.level - sportsman2.level;
  }
}

const runner1 = new Sportsmen('Bob', 3);
const runner2 = new Sportsmen('BHomer', 5);
```

У нас есть статический метод, определяющий уровни спортсменов.

Он определен на уровне класса !!! и не доступен для экземпляров!

```
runner1.canBreathe(); // true
runner2.canBreathe(); // true
```

Но

```
'compare' in runner1; // false
'compare' in runner2; // false
```

Доступ только из класса:

```
Sportsmen.compare(runner1, runner2); // -2

```

## `2.2 - Объекты - Наследование в ES6`

---

До ЕS6 реализовать наследование без потери конструктора было сложно.

Вот прошлый пример:

```
function Animal() {}

Animal.prototype.canBreathe = function () {
  return true;
};

function Mammal() {}

Mammal.prototype = new Animal();

Object.defineProperty(Mammal.prototype, 'constructor', {
  enumerable: false,
  value: Mammal,
  writable: true,
});

const dog = new Mammal();

```

На этапе `Mammal.prototype = new Animal();` мы теряем конструктор `Mammal` и потом

болезненно его настраиваем через `Object.defineProperty(....)`.

Теперь все проще:

```
class Animal {
  constructor(name) {
    this.name = name;
  }

  canBreathe() {
    return true;
  }
}

class Mammal extends Animal {    // ключевое слово "extends" наследует от другого класса
  constructor(name, age) {
    super(name);                 // ключевое слово "super" позволяет вызвать конструктор
    this.age = age;              // базового класса Animal
  }

  loveMilk() {
    return true;
  }
}

const dog = new Mammal('Fred', 3);

```

Теперь проверим свойства и методы:

```
dog.constructor === Mammal; // true

dog.constructor === Animal; // false

dog.canBreathe(); // true

dog.name; // 'Fred'

.....

```

Теперь не надо слишком переживать о цепочке прототипов и побочных

эффектах переопределяемых свойств.

## `3.1 - Объекты - Управление доступом к свойствам объектов через методы получения и установки`

---

Объекты в `Javascript` - это относительно простые коллекции свойств.

Слежение за ходом программы часто состоит в видоизменении этих свойств.

```
function Animal(name, secretSkill) {
  this.name = name;
  this.secretSkill = secretSkill;
}

const dog = new Animal('Fred', 'bite');
const cat = new Animal('Nika', 'claw');
```

Код обычный, но что если нам надо

- застраховаться от присваивания неверного типа

```
cat.secretSkill = 101; // нам не подходит цифра
```

- регистрировать все изменения свойства

- отображать в интерфейсе

Похожие методы ранее оговаривались через имитацию закрытых свойств в функции-конструкторе

через замыкания:

```
function Animal(name, secretSkill) {
  let skillLevel;

  this.name = name;
  this.secretSkill = secretSkill;

  this.getSkillLevel = () => {
    console.log('Skill level was shown');
    return skillLevel;
  };

  this.setSkillLevel = (value) => {
    console.log('Skill level was changed from ' + skillLevel + ' to ' + value);
    skillLevel = value;
  };
}
```

Создадим экземпляры:

```
const dog = new Animal('Fred', 'bite');
const cat = new Animal('Nika', 'claw');

```

У каждого экземпляра свое лексическое окружение, то есть он буде изменять значение `skillLevel`

только в своем варианте.

Сложность в том, что все надо явно указывать, все свойства прописывать.

## `3.2 - Объекты - Определение методов получения и установки`

---

В `JavaScript` методы получения и установки могут быть определены двумя способами:

1. путем указания их в литералах объектов или как методы классов в ES6

2. через встроенный метод `Object.defineProperty()`

## `3.3 - Объекты - Определение методов явным образом`

---

Определение методов явным образом практичекси выглядит в использовании как обычно:

```
const animasCollection = {

  animals: ['cat', 'dog', 'mouse'],

  get firstAnimal() {
    console.log('Getting first animal');
    return this.animals[0];
  },

  set firstAnimal(value) {
    console.log('Setting first animal');
    this.animals[0] = value;
  },
};

```

Здесь все просто, по сути обычное использование, но расширяем через методы функционал:

доступ как к обычному свойству, но на самом деле через метод получения!

```
animasCollection.firstAnimal;

// 'Getting first animal'
// 'cat';

animasCollection.firstAnimal = 'monkey';

// 'Setting first animal'
```

Встроенные методы получения и установки позволяют сделать доступ как обычным способом.

При доступе к свойству эти методы вызываются неявно: все обычно выглядит

```
cat.name;
```

Этот метод прост и популярен весьма.

## `3.4 - Объекты - Определение методов получения и установки через классы ES6`

---

Переписать на классы можно и получим все тот же код:

```
class AnimasCollection {
  constructor() {
    this.animals = ['cat', 'dog', 'mouse'];
  }
  get firstAnimal() {
    console.log('Getting first animal');
    return this.animals[0];
  }
  set firstAnimal(value) {
    console.log('Setting first animal');
    this.animals[0] = value;
  }
}

const animasCollection = new AnimasCollection();
```

Все это первый из двух способов.

> Важно
>
> Если указать только геттер, то есть метод получения, то при попытке установить свойство,
> в обычном режиме просто ничего не произойдет, а в строгом будет сгенерирована ошибка!

## `3.5 - Объекты - Определение методов получения и установки через Object.defineProperty()`

---

Еще раз важно уточнить, что все эти настройки с методами получения и установки обычно

используются для управления доступом к закрытым свойствам. Увы, но в языке не поддерживаются

закрытые свойства. Мы использовали уже имитацию через замыкания, но это не работает.

Дело в том, что методы через литерал объекта или в классе все рано не создаются в той

области видимости, где и переменная, которую можно было бы использовать как закрытое свойство.

Поэтому есть решение через `Object.defineProperty()`:

```

function Soldiers() {
  let _secretValue = 0;

  Object.defineProperty(this, 'secretValue', {
    get: () => _secretValue,

    set: (value) => (_secretValue = value),
  });
}

const rembo1 = new Soldiers();
const rembo2 = new Soldiers();

```

Здесь все внешне работает как прежде:

```
rembo1.secretValue; // 0
rembo1.secretValue = 10;
```

Здесь, в отличие от литералов объектов и классов, методы `get()` и `set()` создаются

через `Object.defineProperty()` в той же области видимости, где и "закрытая" переменная.

## `3.6 - Объекты - Применение методов получения и установки для проверки достоверности свойств`

---

Все эти удобства с методами установки используют, когда надо проверить значение свойства.

То есть не позволять мелким багам ломать приложение. То есть, например, ввод должен быть числом,

а не строкой.

```
function Soldiers() {
  let _secretValue = 0;

  Object.defineProperty(this, 'secretValue', {

    get: () => _secretValue,

    set: (value) => {
      if (!Number.isInteger(value)) {
        throw new TypeError('Secret value should be a number!');
      }
      _secretValue = value;
    },
  });
}

const rembo1 = new Soldiers();
const rembo2 = new Soldiers();

rembo1.secretalue = 'cu-cu'; // Uncaught TypeError: Secret value should be a number!



```

## `3.7 - Объекты - Применение методов получения и установки для вычисляемых свойств`

---

Здесь все как обычно. И ничем не отличается от простого метода, например, `getFullInfo`,

который можно было бы создать. Использовать код ниже - это на усмотрение.

Но пример просто приведем:

```
const person = {
  name: 'Louis-Ferdinand',
  language: 'French',

  get fullInfo() {
    return `${this.name} speak ${this.language}`;
  },
  set fullInfo(value) {
    const segments = value.split(' ');
    this.name = segments[0];
    this.language = segments[1];
  },
};

person.fullInfo; // 'Louis-Ferdinand speak French'

person.fullInfo = 'Bob english';
person.fullInfo; // 'Bob speak english'
```
