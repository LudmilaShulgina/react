# Неделя 27

![%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2027%20641ece28ad83439eaf0175509a0061c6/14902521821100182833.png](%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2027%20641ece28ad83439eaf0175509a0061c6/14902521821100182833.png)


## Стилизация компонентов

React ничего не знает про то, как определяются стили. После отрисовки, в DOM страницы добавляется обычная HTML-разметка. И если оставить ее как есть, к ней по умолчанию применится стандартное оформление браузера.  Серое и безжизненное.

Facebook, компания-разработчик React, для Web в плане стилизации React-компонентов ничего толкового не предлагает. Зато нашлось множество талантов, которые написали свои решения. 

Есть большое количество способов как добавить стили в React-приложение. И когда мы создаем новое приложение, мы должны учитывать, какие инструменты разработки будут использоваться. С большой вероятностью, помимо самого React в вашем приложении будут инструменты вроде babel, webpack, browserify, gulp, post- pre- css-процессоры. От этого стоит отталкиваться, подбирая способы стилизации. 

### Метод 1: Инлайновая стилизация

Решение в лоб. Быстрый в использовании режим для стилизации компонентов React.

Мы можем добавлять инлайновые стили компонентам, которые хотим отрендерить, схоже с тем, как мы могли бы это сделать с простым HTML-тегом:

```jsx
const Button = () => (
  <button style={{backgroundColor: 'red'}}>Нажми меня</button>
)
```

Aтрибут `style` по-разному выглядит в React и HTML. Основное различие заключается в том, что для React-компонента аттрибуты стиля – это объект. Поэтому он оборачивается фигурными скобками. Внешние фигурные скобки вставляют JavaScript в JSX.

Еще одно отличие — ключи стилей записываются в camelCase. Префиксы браузера кроме `ms` должны начинаться с заглавной буквы.

Свойства стилей указываются с помощью строк или чисел. React автоматически добавит суффикс «px» к свойствам стилей с числовым значением, все другие единицы измерения придется прописывать вручную в виде строки.

Стилевые объекты и компоненты необязательно должны быть вместе. Мы можем вынести стили в отдельную переменную или даже создать отдельный js файл для наших стилей, экспортировать эти стили и затем импортировать их компоненту, с которым мы хотим его использовать.

```jsx
const myStyle = {
  width: 200,
  height: 50,
	fontSize: '1.5 em',
  backgroundColor: 'red',
};

const Button = () => (
  <button style={ myStyle }>Нажми меня</button>
)
```

### Встроенные стили — это плохо?

<aside>
💡 В документации по React написано, что использование атрибута `style` для оформления элементов не рекомендуется. На деле, такой подход можно использовать только для примеров и учебных целей. В продуктовых проектах такой код не пройдет code review

</aside>

В современной разработке такой подход не любят, и на это есть много причин:

- Встроенные стили проседают по производительности, CSS-классы справляются намного лучше.
- Невозможность использования псевдоклассов и псевдоэлементов вроде :hover, :active, ::placeholder, :before, :after и прочих.
- Значительное увеличение размера разметки, ухудшение читабельности. В случае серверного рендеринга увеличится еще и нагрузка на канал трафика, а также время загрузки страницы пользователем.
- Отсутствие подсказок. Для JS-кода объект стиля — это обычный набор ключей и значений, он ничего не знает про то, какими они могут и не могут быть. Это значительно усложняет написание кода, а поиск ошибки превращается в увлекательное приключение.

### Метод 2: Таблицы стилей CSS

Этот подход вам также уже знаком. Стили храняться в отдельных файлах с расширением .css. Их можно импортировать в компонент и добавлять как аттрибут `className`

```css
/* button.css */
.button {
    width: 200px;
    line-height: 1.5em;
    text-transform: uppercase;
    margin: 20px;
}

.button__success {
    background: green;
    color: white;
}
```

```jsx
import React from "react";
import styles from './button.css'

export default class Button extends React.Component {
    render() {
         return (
            <button className={'button button__success'}>
                Нажми меня
            </button>
        );
    }
}
```

Используя эту стратегию, вы можете подключать существующие фреймворки, такие как Bootstrap и препроцессоры (LESS, SASS, SCSS).

Но этот способ уже также устаревает, т.к. используя его вы упускаете множество других функций, таких как предотвращение конфликтов имен.

Кстати, обычно CSS-классы зависят от пропсов или состояния. И в нашем предыдущем примере, кнопка должна окрашиваться в зеленый цвет только если ей передан аттрибут `success === true`

Перепишем помпонент, добавим в него проверку props:

```jsx
import React from "react";
import styles from './button.css'

export default class Button extends React.Component {
    render() {
        let className = 'button';
        if (this.props.success) {
            className += '  button__success';
        }
        return (
            <button className={className}>
                Нажми меня
            </button>
        );
    }
}
```

<aside>
💡 **Совет:** пакет [classnames](https://www.npmjs.com/package/classnames#usage-with-reactjs) создан специально для того чтобы упрощать такие конструкции. Его можно использовать и с другими методами стилизации.

</aside>

Добавим `classnames` в компонент:

```jsx
import React from "react";
import styles from './button.css';
import * as classnames from 'classnames';

export default class Button extends React.Component {
    render() {
        const className = classnames(
            'button',
            {
                ['button__success']: this.props.success,
            },
        );

        return (
            <button className={className}>
                Нажми меня
            </button>
        );
    }
}
```

### Метод 3: CSS модули

Имена классов CSS и имена анимации имеют глобальную область видимомости по-дефолту. Это может привести к конфликтам стилей, особенно в больших CSS. Попросту один стиль может быть перезаписан другим стилем. Это именно та проблема, которую решают CSS модули. CSS классы доступны только внутри компонента, в котором он используется.

Для того чтобы показать этот принцип, необходимо переименовать файл стилей по шаблону `[name].module.css` или `[name].module.scss`. Например для компонента `Button` файл будет стилей будет называться `button.module.css`.

CSS модуль это на самом деле .css файл, который скомпилирован. Будучи скомпилированным он выдает две выдачи. 

Первая - это CSS, который являет собой модифицированную версию написанного CSS с переименованными классами. А другая - это JavaScript объект, который формирует исходный CSS с уже переименованными классами.

Дальше все точно так же как в предыдущем методе, но разбиваем стили на множество файлов в соответствии с компонентами. Изменилось только присваивание класса, теперь мы делаем это через точку (как у объекта):

```jsx
import React from "react";
import styles from './button.module.css';
import * as classnames from 'classnames';

export default class Button extends React.Component {
    render() {
        const className = classnames(
            styles.button,
            {
                [styles.button__success]: this.props.success,
            },
        );

        return (
            <button className={className}>
                Нажми меня
            </button>
        );
    }
}
```

Использование CSS-Modules подразумевает, что каждый React-компонент может иметь свой стиль root, и никаких коллизий не будет — имена стилей трансформируются при сборке.

Вот как будет выглядеть имена классов после сборки:

```html
<button class="pE2xFn0OXfSkBWFMEa0m5">Нажми меня</button>
```

Большой плюс CSS модулей - нет конфликтов пространств имен и конфликтов имен, код прост в использовании и рефакторинге, что позволяет легко удалять мертвый код из проектов. Кроме того, он поддерживают все, что вам нужно в CSS.

<aside>
💡 Есть большое количество других решений для стилизации компонентов.
Очень популярными являются пакеты [styled-components](https://styled-components.com/) и [JSS](https://cssinjs.org/?v=v10.6.0)
У этих проектов подробная документация и большая база пользователей, что очень важно при решении проблем. Скорее всего, решения уже найдены до вас, и вы легко их отыщете😉

</aside>

### Темизация

Вместо того, чтобы импортировать стиль в компонент, компонент может принимать стиль как свойство. Таким образом можно использовать разные темы и даже менять их динамически.

Создадим 2 темы для компонента:

```css
/* theme-a.css */
.outer { background: green; }
.inner { color: blue; }
```

```css
/* theme-b.css */
.outer { background: red; }
.inner { color: yellow; }
```

Теперь создадим компонент, который будет принимать аттрибут темы

```jsx
import React from "react";

export default class Component extends React.Component {
    render() {
        const theme = this.props.theme;
        return (
            <div className={theme.outer}>
                <div className={theme.inner}>
                    <h3>Заголовок</h3>
                </div>
            </div>
        )
    }
}
```

Теперь, при вызове компонента, мы будем передавать ему тему как свойство

```jsx
/* App.js */
import Component from './Component/Component';
import themeA from "./Component/theme-a.css";
import themeB from "./Component/theme-b.css";

function App() {
  return (
    <div>
      <Component theme={themeA}/>
      <Component theme={themeB}/>
    </div>
  );
}

export default App;
```

### CSS-переменные

CSS модули являются одним из основных подходов для React проектов, но разработчики обычно быстро обнаруживают, что им нужен способ выделять и использовать общие стили.

Например, вы сделали большое приложение в зелено-белой гамме, в котором каждому компоненту соответствует свой css-файл. И потом заказчики решили поменять ее на сине-белую. Вам придется вручную поменять все стили проекта.

Конечно, нам нужно более элегантное решение.

![%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2027%20641ece28ad83439eaf0175509a0061c6/Untitled.png](%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2027%20641ece28ad83439eaf0175509a0061c6/Untitled.png)

Чаще всего, общую информацию представляют собой переменные (цвета, размеры шрифта, и т. д.). Импорт переменных можно осуществить через CSS, или препроцессор, например, SASS или Less

Создадим новый css-файл, который будет содержать наши переменные.

```css
/* variables.css */
@value customBlue: #0c77f8;
@value customRed: #ff0000;
@value customGreen: #aaf200;
```

И импортируем в уже существующий файл 

```css
/* button.css */
@value colors: "../variables.css";
@value customBlue, customRed, customGreen from colors;

.button {
    width: 200px;
    line-height: 1.5em;
    text-transform: uppercase;
    margin: 20px;
    color: customBlue;
}

.button__success {
    background: green;
    color: customRed;
}
```

Так, вместо изменения каждого файла по отдельности, мы поменяем переменные только в одном месте и стили обновяться во всем приложении

### Метод 4: CSS-препроцессоры

CSS препроцессор — это надстройка над CSS, которая добавляет ранее недоступные возможности для CSS, с помощью новых синтаксических конструкций.

Такие файлы имеют свое собственное расширение, и напрямую в браузере не работают, нуждаются в компиляции, результатом которой является CSS-файл. Как это работает: вы запускаете какое-либо ПО (например, webpack), оно следит за сохранением препроцессорных файлов и собирает (компилирует) из них CSS-файл.

![%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2027%20641ece28ad83439eaf0175509a0061c6/Untitled%201.png](%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2027%20641ece28ad83439eaf0175509a0061c6/Untitled%201.png)

На картинке мы видим файл `styles.scss`, после компиляции автоматически генерируются файлы `styles.css` и `[styles.css.map](http://styles.css.map)` 

Сгенерированный файл импортируется в компонент точно также как и обычный.

Независимо от того, какой препроцессор вы выберете, главными плюсами являются:

- **Разделение на небольшие файлы.** Мы можем организовать блочную систему стилей
- **Переменные.** Вынести общие ****(цвета, размеры шрифта, и т. д.) и импортировать их в нужный файл
- **Математические операции:**
    
    ```scss
    .tabs {
      width: calc(100% - 3em);
    }
    ```
    
- **Вложения.** Позволяет не повторять написание селектора, внутри которого нужно что-то стилизовать. Просто пишем вложенные селекторы внутри родительских. Ощутимо ускоряет работу.
    
    ```scss
    .promo {
      padding: 1em;
    
      h1 { // после компиляции превратится в .promo h1 {...}
        font-size: 48px;
      }
    
    }
    ```
    
- **Амперсанд.** Позволяет не повторять родительский селектор, быстро менять его. Помогает сделать код читабельней.
    
    ```scss
    .btn {
      color: #000;
    
      &:hover { // после компиляции превратится в .btn:hover {...}
        color: #808080;
      }
    
    }
    ```
    
- **Примеси.** Набор правил, который можно использовать многократно.
    
    ```scss
    @mixin clearfix {
    
      &:after {
        content: "";
        display: table;
        clear: both;
      }
    }
    
    // Применение примеси
    .promo__wrapper {
      @include clearfix;
      padding: 1em;
    }
    ```
    

<aside>
💌 CSS-препроцессоры очень широко распространены, и эти знания вам обязательно понадобятся. Учите препроцессоры

</aside>

Несколько самых популярных CSS-препоцессоров:

[Sass: Syntactically Awesome Style Sheets](http://sass-lang.com/)

[Getting started | Less.js](http://lesscss.org/)

[Expressive, dynamic, robust CSS](http://stylus-lang.com/)

[PostCSS](https://postcss.org/)

## Условный рендеринг

В самом своем названии React намекает, что он создан не просто для того чтобы выводить статичную разметку, а реагировать и изменять интерфейс в зависимости от действий пользователя.

Вернемся к нашим карточкам товара. Когда мы нажимаем на кнопку "заказать", наш товар попадает в корзину. Чаще всего, после этого кнопка исчезает, а вместо нее появляется форма, в которой мы можем настроить количество товаров. Это поведение повторяется на многих сайтах, и пользователь к нему привык. Такое поведение ожидаемо, поэтому по законам **UX**, нам следует сделать также

![%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2027%20641ece28ad83439eaf0175509a0061c6/Frame_2.png](%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2027%20641ece28ad83439eaf0175509a0061c6/Frame_2.png)

Условный рендеринг в React работает так же, как условные выражения работают в JavaScript, и мы можем применить тернарный оператор или выражения подобные if

Добавим в карточку товара свойство `addedToCart` которое будет получать количество товара, добавленного в корзину. Если аттрибут не передан, значит товар в корзину не добавлен. 

Вынесем кнопку "Заказать" как отдельный элемент `AddToCartButton`

```jsx
function AddToCartButton() {
    return <button className="card-add">Заказать</button>;
}
```

Добавим элемент формы для измения количества товара в корзине. Пока он не будет работать, только принимать props и отображать его в карточке товара:

```jsx
function AddToCartForm(props) {
    return (
        <form>
            <button className="card-form__del">-</button>
            <input className="card-form__input" value={props.addedToCart}/>
            <button className="card-form__add">+</button>
        </form>
    );
}
```

### If

Теперь мы можем переписать наш компонент `Card` . Перед отрисовкой мы будем проверять свойство `addedToCart` и отрисовывать компонент в зависимости от его значения:

```jsx
export default class Card extends React.Component {
    render() {
        const {title, price, description, imgLink, addedToCart } = this.props;
        let actionItem;
        if (addedToCart) {
            actionItem = <AddToCartForm addedToCart={addedToCart} />;
        } else {
            actionItem = <AddToCartButton />;
        }
        return (
            <div className="card">
                <div className="card-body">
                    <img src={imgLink} alt={title}/>
                    <h4 className="card-title">{title}</h4>
                    <p className="card-text">{description}</p>
                </div>
                <div className="card-footer">
                    <span className="card-price">${price}</span>
                    {actionItem}
                </div>
            </div>
        );
    }
}
```

Нет ничего плохого в том, чтобы объявить переменную и условно рендерить компонент if-выражением. Но есть более короткие способы, которые можно включать прямо в JSX.

<aside>
💡 Если вы хотите, чтобы реакт проигнорировал компонент, можно вернуть `null`
Но лучше воспользоваться [логическим оператором &&](%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2027%20641ece28ad83439eaf0175509a0061c6.md)

</aside>

### Тернарный оператор

Уже знакомый способ из JS

```jsx
export default class Card extends React.Component {
    render() {
        const {title, price, description, imgLink, addedToCart } = this.props;
        
        return (
            <div className="card">
                <div className="card-body">
                    <img src={imgLink} alt={title}/>
                    <h4 className="card-title">{title}</h4>
                    <p className="card-text">{description}</p>
                </div>
                <div className="card-footer">
                    <span className="card-price">${price}</span>
                    {addedToCart
                        ? <AddToCartForm addedToCart={addedToCart} />
                        : <AddToCartButton />
                    }
                </div>
            </div>
        );
    }
}
```

### Логический оператор &&

От обычного If он отличается тем, что принимает только два значения, а не три. Проще говоря, для него нет понятия `else`

Предположим, не у всех наших товаров есть картинка. Тогда выводить изображение мы будем только если есть аттрибут `imgLink`:

```jsx
export default class Card extends React.Component {
    render() {
        const {title, price, description, imgLink, addedToCart } = this.props;

        return (
            <div className="card">
                <div className="card-body">
                    { imgLink &&
                        <img src={imgLink} alt={title}/>
                    }
                    <h4 className="card-title">{title}</h4>
                    <p className="card-text">{description}</p>
                </div>
                <div className="card-footer">
                    <span className="card-price">${price}</span>
                    {addedToCart
                        ? <AddToCartForm addedToCart={addedToCart} />
                        : <AddToCartButton />
                    }
                </div>
            </div>
        );
    }
}
```

Если условие истинно (true), то элемент, идущий непосредственно за **&&**, будет передан на вывод. Если же оно ложно (false), то React проигнорирует и пропустит его.

<aside>
💡 Выбор синтаксиса может зависеть не только от ваших предпочтений, но и от принятого в команде стиля. По опыту, if оператор встречается довольно редко.

</aside>

## Домашнее задание на эту неделю:

### Вопросы 💎

1. Какую ошибку я допустила в [этом](%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2027%20641ece28ad83439eaf0175509a0061c6.md) примере?  
2. Какие есть способы работы со стилями в React?
3. Как будет выглядеть карточка товара, если ей передать аттрибут `addedToCart`===0?
4. Какие еще проверки нужно было бы сделать для аттрибута `addedToCart`?
5. Клиент попросил повесить тег "New" на товары из новой коллекции. Как это сделать, какой условный оператор выбрать?
6. Какими тремя способами можно написать условный рендеринг? 
7. Представьте, что вы пишете компонент логина. Если пользователь авторизован, то мы показываем его имя, а если нет, то даем возможность ввода логина и пароля. Какой код для этого нужно написать, если за авторизацию пользователя отвечает флаг isAutorized?
8. В чем преимущества использования препроцессоров? Какой есть еще способ использовать переменные, кроме $ в препроцессорах?

### Практическое задание 💎

1. Реализуйте список тарифов как на картинке, выделив "Тариф" как компонент. Сделайте так, чтобы тариф с ценой 550р выделялся заметнее остальных:
    
    ![%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2027%20641ece28ad83439eaf0175509a0061c6/Untitled%202.png](%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2027%20641ece28ad83439eaf0175509a0061c6/Untitled%202.png)
    
2. **Задание для проекта:** 
На прошлой неделе мы создали основные элементы интерфейса (хедер, футер, карточку слова, список слов и пр.), на этой неделе вам предстоит их стилизовать. 
    
    Пока что вы можете задавать слова вручную. Чуть позже мы переключим это на API. Структуру для строения слова можете взять отсюда: 
    
    [http://itgirlschool.justmakeit.ru/api/words](http://itgirlschool.justmakeit.ru/api/words)
    
    - Подключите стили для всех созданных элементов
    - Выделите основные цвета, шрифты и размеры в отдельный файл
    - Добавьте анимацию наведения, фокуса и нажатия на все элементы взаимодействия с пользователем (кнопки, формы и т.д.)
    - Сделайте список слов в виде таблицы. Реализуйте условный рендеринг для полей таблицы: должны отображаться либо поля для чтения и с кнопками редактировать/удалить, либо поля ввода с кнопками сохранить/отмена. Похожий пример:
    
    ![%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2027%20641ece28ad83439eaf0175509a0061c6/Untitled%203.png](%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2027%20641ece28ad83439eaf0175509a0061c6/Untitled%203.png)
    
    <aside>
    💡 Бонусное задание, творческое 🎨 
    Подумайте какого функционала не хватает нашему приложению.
    
    </aside>
    

