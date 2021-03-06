---
published: true
layout: post
title: MVP for students by student
---
Навеяно дискуссиями о шаблоне в группе...

*Саша.* Хорошо, Model-View-Presenter позволяет абстрагировать пользовательский интерфейс от логики приложения, чтобы ...

*Маша.* Чтобы поведение приложения не зависело от его конкретного внешнего вида.

*Саша.* Давай на примере.

*Маша.* Например, под разной торговой маркой может прятаться один и тот же продукт. Продукт - это как бы Модель, а его упаковка - это Представление.

*Витя.* Есть идея по выполнению домашки. Давайте коллективными усилиями реализуем логику калькулятора в Модели, а вьюшки - каждый свою прикрутит.

*Маша.* Думаешь, прокатит?

*Даша.* Зато сразу раскрываются возможности шаблона. Вот, написала команда какое-то чудо-приложение, тестами код покрыла, выяснила, что все классно работает. А приложение-то не продается. И тут маркетологи сообщают, что причина кроется в отвратительном внешнем виде приложения.

*Маша.* Вьюшка видимо никуда не годится.

*Даша.* Ну да, и как такой ужас вообще можно потребителю предлагать? Дизайнер получает нагоняй, после чего вьюшку заменяет на супер-вьюшку, со всеми этими дизайнерскими штучками, - и уровень продаж стремительно растет.

*Петя.* Ага, вот так просто взяли и заменили.

*Саша.* Просто заменили. Презентер работает с интерфейсом вьюшки. Какую вьюшку не подставь, главное, чтобы она реализовала интерфейс, через который Презентер будет обновлять данные. Вот тебе и принцип Dependency Inversion. И вся соль проектирования интерфейсами. И даже можно сделать заглушку, которая будет на момент разработки возвращать то, что нам нужно.

*Даша.* Смотрите, если задача Презентера - получить действие пользователя и передать его в Модель, а затем передать изменение состояния Модели во вьюшку, получается, что Презентер - просто прослойка. Может тогда стоит разместить код вызова соответствующих методов в Представлении?

*Маша.* Ну да, и превратить архитектуру SOLID в архитектуру STUPID. В частности, у представления
появится еще одна обязанность - логика управления моделью.

*Саша.* Чего это?

*Маша.* Того, что представление будет заниматься интерпретацией пользовательского ввода, чтобы определить, какой метод вызвать у модели, а это нарушит принцип единственной ответственности.

*Витя.* Ну да, если разобраться, то Презентер не просто передает данные в Модель и Представление. Он отвечает за интерпретацию ввода для вызова соответствующих методов модели. Вот нажимает пользователь на кнопку, и Презентер знает, какой метод Модели вызвать. Внутри модели что-то происходит или не происходит. Когда Презентер получает уведомление от Модели о том, что состояние изменилось, он определяет, какое свойство считать у Модели, и какому метду вьюшки передать свойство в качестве параметра.

*Маша.* Получается, свойства, изменяемые Моделью, должны быть реализованы в Представлении. Иначе говоря входной интерфейс Представления соответствует выходному интерфейсу Модели.

*Петя.* Модель тоже может реализовать интерфейс, через который Презентер будет с ней взаимодействовать. На случай если разработчику нужно будет подправить реализацию.

*Саша.* Погодите с поправлением реализации. Давайте лучше определимся, что входит в обязанность Модели. Модель — это бизнес-логика приложения. Окей, логика калькулятора - считать. И тогда получается, что Модель калькулятора будет выполнять калькуляцию, то есть два плюс два складывать. А кто же тогда будет валидировать пользовательсикй ввод? Или раскидывать данные по операторам-операндам? Модель? Презентер?

*Даша.* А ты попробуй абстрагироваться. Модель - это бизнес-логика приложения. И если задача приложения - считать, то результат подсчета - это итог работы Модели. И мало ли что за подсчетом скрыто. То, что этому подсчету предшествуют валидация и парсинг - вполне нормально, и это как раз и есть бизнес-логика калькулятора.

*Саша.* А тебе не кажется, что это нарушение принципа единственной ответственности? Хотя куда валидацию ни прикрути, все равно появится еще одна ответственность.

*Петя.* Еще как кажется, особенно, если у тебя все в одном классе Модели будет сидеть. И валидация, и парсинг, и подсчет.

*Маша.* Вот именно. Если все в одном классе будет сидеть. Модель - ведь понятие абстрактное. За ней может стоять как просто слой данных, так и целая логика. Но не обязательно это все инкапсулировать в одном классе. Идея данного шаблона ведь в чем - отделить дизайн от логики работы приложения. Поэтому все, что не относится к прорисовке, отнесем к логике работы модели. А в самой модели никто не мешает придерживаться принципов SOLID и использоать другие шаблоны проектирования.

*Саша.* В итоге получаем пассивную вьюшку, "тонкого" Презентера, и максимум логики в Модели.

*Маша.* И минимум кода, который невозможно покрыть модульными тестами.

Вот какие заметки по Калькулятору получились у студентов:

```c#
public interface IView
{
    event EventHandler<SomeType> Digit2Pressed;
    event EventHandler<SomeType> SomethingElsePressed;
    ...
    UpdateOperand(string value);
    UpdateExpression(string value);
    ...
}
```

```c#
public interface IModel
{
    event EventHandler<SomeType> OperandChanged;
    event EventHandler<SomeType> ExpressionChanged;
    string Operand{get;}
    string Expression{get;}
    Appendvalue(string value);
    ...
}
```

Пользователь взаимодействует с Представлением:

```c#
private void OnDigit2Pressed(object sender, EventArgs e)
{
    Digit2Pressed?.Invoke(this, new SomeType("2"));
}
```

Презентер, получивший ссылку на реализацию интерфейса Представления, подписывается на события Представления и по запросу изменяет Модель:

```c#
private void View_Digit2Pressed(object sender, SomeType args)
{
    if (args == null) return;
    _model.AppendValue(args.Value);
}
```

Когда и если в Модели что-то меняется, она уведомляет об этом Презентера, 

```c#
public string Operand
{
    get => _backingField;
    private set
    {
        _backingField = value;
        OnOperandChanged();
    }
}
...
private void OnOperandChanged()
{
    OperandChanged?.Invoke(this, new SomeType());
}
```

который вызывает соответствующий метод обновления у Представления:

```c#
private void Model_OperandChanged(object sender, SomeType args)
{
if (args == null) return;
    _view.UpdateOperand(_model.Operand);
}
```


> Все герои вымышлены, полное или частичное сходство является совпадением.

