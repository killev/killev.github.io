# Глава 1. ООП

## Краткий экскурс в историю


Основаная прикладная задача, которую призван решить ООП - это отображение реального мира в программный код. Структурами и процедурами думать сложно. Гораздо проще думать объектами. Вместо: "прочитай количество строк в этом файле", проще мыслить как: "файл, скажи мне количество строк".

```swift
///Вместо вызова
numberOfLines(in file: File)
/// Вызов метода объекта
file.numberOfLines()

```

У истоков ООП, оказыватся, стоит вовсе не Бьярн Страуструп, котого мы все хорошо знаем.
Тот ООП, который мы видим сейчас, немного отличается от того, каким он изначально задумывался Аланом Кеем.

В 1969м (на 10 лет раньше Бьярна), Кей предложил ООП как парадигму разработки программного обеспечения в которой программа представлена в виде классов и экземпляров классов (объектов), которые взаимодействуют друг с другом при помощи асинхронных сообщений.

Что это значит для нас как для программистов.
* Первое: функция (ну, или ее аналог) не может возвращать значение.
  Мы больше не можем написать: 

```swift
class TextFile
{
...
func numberOfLines()->Int
...
```
вместо этого предлагалось сообщать результат тоже с помощью сообщения! Были разные варианты. Самым удобным (с точки зрения синтаксиса, а не внутренней реализации) оказалась передача callback:
```swift
class TextFile
{
...
void numberOfLines(callback: (Int)->Void)
...
```
Вместо: "скажи мне сколько у тебя строк", теперь надо: "пойди посмотри сколько у тебя строк и перезвони мне по этому номеру".

* И второе (непосредственно сдледует из первого). У класса не может быть свойств. Если рассматривать свойство класса как *getter* и *setter*, то *getter* не возможен. Мы же не можем возвращать значение. 

Данная концепция 1980 году воплотилась в революционном на тот момент языке Smalltalk. За 3 года до привычного нам всем C++.

Почему же сообщения? Такой подход делает местоположение конкретного объекта не важным. Он может быть в другом потоке, процессе, на другом компьторе, в другой вселенной! Не важно. Лишь бы система перезвонила (доставила) нам ответ. И это свойство дает возможность легко масштабировать систему горизонтально, добавляя ЦПУ когда это необходимо и убирая, когда не надо. И соединяя все это как-нибудь, например, сетью.

Но реальность внесла свои коррективы. Оказалось, что надежная передача сообщений требует от системы сложных диспатчеров и транспортов о очереденй соообщений. Что нагружало, и так не очень быстрые, ЦПУ того времени, и требовало дополнительную память, которой и так было не много. Да и  и нет никакой гарантии, что к тому времени, как ответ придет, он еще будет иметь какой-то смысл. Поэтому, идея с сообщениями на начальном этапе развития не получила.

А получила распросранение другая.

А вот Бьярн Страуструп все упростил. И сказал: пусть те же объекты взаимодействуют другом но при помощи давно известных и всем привычных функций. Синхронно вызывая друг у друга методы и свойства. А сам ООП язык - это синтаксический сахар который позволяет старые добрые функция типа:

```swift 
numberOfLines(in file: TextFile)
numberOfNumbers(in file: TextFile)
```

вызывать по-новому. Уже в стиле ООП методов:

```swift 
/// Вызов метода объекта
file.numberOfLines()
file.numberOfNumbers()
```

Не важно что они делают, важно что первый параметр можно вынести перед именем функции и он автоматически пробросится в функцию.
Так в 1983 из **С** появился **C++**. А уже из него выросли Java, C# и многие другие c-подобные ООП языки. И не c-подобные тоже.

## Что такое ООП

Как я уже сказано выше, ООП - это, в первую очередь, инструмент отображения (моделирования) реального мира в код. Так появились классы (как описание) и объекты (как физические сущности). 

Помимо этого ООП был призван решить еще несколько задач:
1. Целостность данных. Как часто программа наламывалась только потому что мы вызывали функции не в том порядке? Или даже просто поменяли значие переменной забыв вызвать нужный обработчик после этого?
2. Переиспользование кода.
3. Переисользование структуры кода. Это когда надо вызвать функции в нужном порядке, но вот какие именно - мы заранее не сказать не можем. Своеобразное мета-программирование.

Решения этих проблем вылились в принципа ООП:
1. **Инкапсуляция**. Если просто - то это наличие в языке ключевых слов private, public и т.д.
2. **Наследование**. Это фича языка, которая позволяет задавать отношение иерархии (наследование) для классов. При этом класс наледник обретает все метода и свойства родителя (естественно дозволенные после инкапсуляции), и в свою очередь становится неотличим от родителя. Т.е. любой потомок может быть полностью изпользован вместо родителя.
3. **Полиморфизм**. Это тоже фича языка. Она позволяет использвать различные реализации функций у объектов разных типов в одном и том же "куске кода". 

Например такой "кусок кода":
```swfit

func doSomething(obj: SomeObject) {
  obj.prepare()
  if obj.changeData( SomeNewData() ) {
    obj.commit()
  }
}
```
В коде выше не важно какого типа объект obj. Лишь бы у него были определены функции:

```swift
class SomeObject {
...
  func prepare()
  func changeData(data: SomeData) -> Bool
  func commit()
...
}
....
doSomething(SomeObject1())
doSomething(SomeObject2())
```
А уже какой тип и как их определит - это личная проблема уже данного конкретного класса.
Сказонное выше относится к т.н. **динамическому полиморфизму**. Это когда конкретная реализация функций определяется только уже в момент вызова функции. Хочу отметить, что я специально здесь не говорю ни о иерархии, ни о виртуальности функций. Об этом ниже.

В некоторых языках еще выделяют **статический полиморфизм**.
```
func doSomething<T: SomeObject>(obj: T) {
  obj.prepare()
  if obj.changeData( SomeNewData() ) {
    obj.commit()
  }
}
doSomething(SomeObject1())
doSomething(SomeObject2())
```
В данном случае функции совсем не обязаны быть виртуальными. Они просто должны быть. В **templates** в **C++** вобще не обязывают задавать вообще какую бы то ни было начальную структуру используемого класса. Но все-таки **статический полиморфизм** - он не совсем про ООП. Поэтому дальше я буду говорить только про **динамический**.

4. Иногда еще выделяют **абстракцию**.. Но.. во-первых, я не до конца понимаю что это такое. Во-вторых, это не самое важное для этой статьи.

## Зачем это все в ООП

И вот тут-то у меня и возник спор со знакомым, из которого и родилась эта статья. Какими фичами из представленых 3-х (4-х) выше должен обладать язык чтобы он считался ООП. Ну, или без каких фич он становится бесполезен.

Дальше я расскажу свое личное мнение по поводу 3-х первых.

### Инкапсуляция

Имхо, это самая спорная и, имхо, бесполезная фича из всех. Во-первых, так или иначе, но ее постоянно приходися обходить. Либо с помощью переделки хедеров для специальной сборки в с++. Либо с помощью рефлексии.. Либо... И, поверьте, дело не в дизайне! Дело скорее в том, что есть часть клиенткого кода, которая должна видеть внутренности другого класса. А есть, которая не должна. Например отношение db <-> entity. Весь клиентский код должен видеть etity как immutable object. Т.е. видеть только getter. Поменять его можно только, через db. И вот тут db должна видеть еще и setter. Опана!

В общем. В Python, например, нет инкапсуляции на уровне языка. Но есть договоренность "условно скрытые" методы начинать с префикса \_\_. Вызывать то мы их можем, но уже за последствия отвечаем сами. И при этом язык прекрасно себя чувствует.

### Наследование

Тоже спорное свойство. Нет. Оно не бесполезное. Но надо четко понимать что наследование - это синтаксический сахар, который просто заменяет композицию. "Физически" же в класс наследник просто добавляется свойство *parent*. Ну и часть функций переадресуется в parent, если у наследника нет такой же. Везде где нам нужно использовать объект-наслежник как объект-родитель - мы просто подставляем parent. Все просто.

Некоторые языки не поддерживают наследование напрямую. Например JavaScript. И живут же.
В общем без него можно, но сложно.

### Полиморфизм

Помним, что далее речь идет только о **динамическом полиморфизме**
И вот тут самое интересное. Это единственное свойство ООП, с которым согласны все любители ООП. 

Стоп! А как же наследование? Ведь полиморфизм реализвуется как раз через наследование. 

А вот это уже правда только отчасти. Действительно, большиство (если не все) строго-типизированные языки реализуют полиморфизм через наследование. Дело в том, такому языку необходимо еще на этапе компиляции знать тип объекта, чтобы выяснить сможем ли мы получить от него то, что надо. И полиморфизм возникает при совпадении двух условий:
1. Наличие у родителя хотя бы одного виртуального метода
2. Наличие хотя бы одного наследника, в котором хоя бы 1 из виртуальных методов базового класса был бы переопределен.

Теоретически, даже 1го уже достаточно. Но формально все таки надо 2.

Т.е. существует четкая сформированная парадигма, что динапически мы можем переопределять только виртуальные функций, и только после наследование... 

Однако. Есть еще и нетипизированные языки. Когда мы вобще не знаем какой тип объект мы передаем в функцию. И включается механизм так называемой "утиной типизции". Если у этого объекта, какого бы типа он ни был на самом деле, есть нужные функции - то мы можем его ипользовать. А наличие этих функци производится динамически перед самим вызовом. Что, с одной стороны делает разработку более опасной ибо пока программа не дойдет до нужно строчки - никто не знает обладает ли объект нужно функцией или нет, с другой - более гибкой ибо вы не ограничены иерархиями классов и используете наследование исключительно для переиспользования когда.

И это тоже полиморфизм. И, заметте, ни слова про наследование и виртуальные функции.


## Итак.
Едиственная фича ООП которая делает его действительно полезним инструментом - есть полиморфизм. Наследование является необходиммым только для строго типизированных языков и то, потому что через него реализуется полиморфизм. 

PS. 
Любители нетипизированных языков с сарказмом говорят, что нет никакой разницы, почему программа упадет: потому что нет функции или потому что нет данных.




