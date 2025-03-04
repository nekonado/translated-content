---
title: Использование переменных в CSS
slug: Web/CSS/CSS_cascading_variables/Using_CSS_custom_properties
---

{{CSSRef}}

**CSS переменные** (**пользовательские CSS-свойства**) это сущности, определяемые автором CSS, хранящие конкретные значения, которые можно повторно использовать в документе. Они устанавливаются с использованием custom property нотации (например. **`--main-color: black;`**) и доступны через функцию [var()](/ru/docs/Web/CSS/var) (например. `color: var(--main-color);`) .

Сложные веб-сайты имеют очень большое количество CSS, часто с множеством повторяющихся значений. Например, один и тот же цвет может использоваться в сотнях разных мест, что требует глобального поиска и замены, если этот цвет необходимо изменить. CSS переменные позволяют сохранять значение в одном месте, а затем многократно использовать его в любом другом месте. Дополнительным преимуществом являются семантические идентификаторы. Для примера: запись `--main-text-color` более понятна, чем `#00ff00`, особенно если этот же цвет используется и в другом контексте.

CSS переменные подчиняются каскаду и наследуют значения от своих родителей.

## Основное использование

Объявление переменной:

```css
element {
  --main-bg-color: brown;
}
```

Использование переменной:

```css
element {
  background-color: var(--main-bg-color);
}
```

> [!NOTE]
> В более ранней спецификации префикс для переменных был `var-` , но позже был изменён на `--`. Firefox 31 и выше следуют новой спецификации.([Firefox bug 985838](https://bugzil.la/985838))

## Первый шаг с CSS Переменными

Начнём с этого простого CSS, который окрасит элементы разных классов одинаковым цветом:

```css
.one {
  color: white;
  background-color: brown;
  margin: 10px;
  width: 50px;
  height: 50px;
  display: inline-block;
}

.two {
  color: white;
  background-color: black;
  margin: 10px;
  width: 150px;
  height: 70px;
  display: inline-block;
}
.three {
  color: white;
  background-color: brown;
  margin: 10px;
  width: 75px;
}
.four {
  color: white;
  background-color: brown;
  margin: 10px;
  width: 100px;
}

.five {
  background-color: brown;
}
```

Мы применим его к этому HTML:

```html
<div>
  <div class="one"></div>
  <div class="two">Text <span class="five">- more text</span></div>
  <input class="three" />
  <textarea class="four">Lorem Ipsum</textarea>
</div>
```

что приводит нас к этому:

{{EmbedLiveSample("sample1",600,180)}}

Обратите внимание на повторения в CSS. Коричневый фон установлен в нескольких местах. Для некоторых CSS объявлений можно указать этот цвет выше в каскаде и наследование CSS решит эту проблему. Но для нетривиальных проектов это не всегда возможно. Объявив переменную в псевдоклассе :root, автор CSS может избежать ненужных повторений, используя эту переменную.

```css
:root {
  --main-bg-color: brown;
}

.one {
  color: white;
  background-color: var(--main-bg-color);
  margin: 10px;
  width: 50px;
  height: 50px;
  display: inline-block;
}

.two {
  color: white;
  background-color: black;
  margin: 10px;
  width: 150px;
  height: 70px;
  display: inline-block;
}
.three {
  color: white;
  background-color: var(--main-bg-color);
  margin: 10px;
  width: 75px;
}
.four {
  color: white;
  background-color: var(--main-bg-color);
  margin: 10px;
  width: 100px;
}

.five {
  background-color: var(--main-bg-color);
}
```

```html hidden
<div>
  <div class="one"></div>
  <div class="two">Text <span class="five">- more text</span></div>
  <input class="three" />
  <textarea class="four">Lorem Ipsum</textarea>
</div>
```

Это приводит к тому же результату, что и предыдущий пример, но позволяет объявить желаемое свойство только один раз.

## Наследование переменных в CSS и возвращаемые значения

Пользовательские свойства могут наследоваться. Это означает, что если не установлено никакого значения для пользовательского свойства на данном элементе, то используется свойство его родителя:

```html
<div class="one">
  <div class="two">
    <div class="three"></div>
    <div class="four"></div>
  </div>
</div>
```

со следующим CSS:

```css
.two {
  --test: 10px;
}

.three {
  --test: 2em;
}
```

В результате `var(--test) будет:`

- для элемента с классом `class="two"`: `10px`
- для элемента с классом `class="three"`: `2em`
- для элемента с классом `class="four"`: `10px` (унаследовано от родителя)
- для элемента с классом `class="one"`: _недопустимое значение_, что является значением по умолчанию для любого пользовательского свойства.

Используя [var()](/ru/docs/Web/CSS/var) вы можете объявить множество **возвращаемых значений** когда данная переменная не определена, это может быть полезно при работе с Custom Elements и Shadow DOM.

Первый аргумент функции это имя [пользовательского свойства](https://www.w3.org/TR/css-variables/#custom-property). Второй аргумент функции, если имеется, это возвращаемое значение, который используется в качестве замещающего значения, когда [пользовательское свойство](https://www.w3.org/TR/css-variables/#custom-property) является не действительным. Например:

```css
.two {
  color: var(--my-var, red); /* red если --my-var не определена */
}

.three {
  background-color: var(
    --my-var,
    var(--my-background, pink)
  ); /* pink если --my-var и --my-background не определены */
}

.three {
  background-color: var(
    --my-var,
    --my-background,
    pink
  ); /* "--my-background, pink" будет воспринят как значение в случае, если --my-var не определена */
}
```

> [!NOTE]
> В замещаемых значениях можно использовать запятые по аналогии с [пользовательскими свойствами](https://www.w3.org/TR/css-variables/#custom-property). Например, var(--foo, red, blue) определить red, blue как замещающее значение (от первой запятой и до конца определения функции)

> [!NOTE]
> Данный метод также вызывает проблемы с производительностью. Он отображает страницу медленнее чем обычно, т.к. требует время на разбор.

## Обоснованность и полезность

Понятие классической концепции CSS, связанность с каждым свойством, не очень удобно в плане пользовательских свойств. Когда значения пользовательских свойств обрабатываются, браузер не знает где они будут применяться, поэтому нужно учитывать почти все допустимые значения.

К сожалению, эти значения могут использоваться через функциональную запись `var()` , в контексте где они, возможно, не имеют смысла. Свойства и пользовательские переменные могут привести к невалидным выражениям CSS, что приводит к новой концепции _валидности во время исполнения._

## Совместимость с браузерами

{{Compat}}
