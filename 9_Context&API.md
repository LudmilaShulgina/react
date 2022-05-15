# Неделя 36

![%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2036%207f1db567b9524b51983345b16418b311/bad-server-status.jpg](%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2036%207f1db567b9524b51983345b16418b311/bad-server-status.jpg)

## Context

Контекст обеспечивает способ передачи данных через дерево компонентов без необходимости передавать свойства вручную на каждом уровне.

### Что не так с пропс?

Мы уже умеем передавать состояние от родительского компонента к дочерним и этот инструмент называется пропс, так к чему изобретать велосипед? 
Представим структуру приложения в виде дерева, где каждый узел дерева - это отдельный компонент:

![%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2036%207f1db567b9524b51983345b16418b311/1.png](%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2036%207f1db567b9524b51983345b16418b311/1.png)

Если нам нужно передать состояние дочернему элементу, это легко делается через пропс:

![%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2036%207f1db567b9524b51983345b16418b311/2.png](%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2036%207f1db567b9524b51983345b16418b311/2.png)

Если нам нужно передать свойства дальше, на более глубокие уровни вложенности, то мы будем передавать их по цепочке, пока не дойдем до нужного элемента. Решение довольно простое, а главное — рабочее. Но что делать, если нужно передать свойство дальнему блоку?

![%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2036%207f1db567b9524b51983345b16418b311/3.png](%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2036%207f1db567b9524b51983345b16418b311/3.png)

Для этого нужно «поднять» свойство по всему дереву вверх до самого первого блока, а потом «спустить» обратно к нужному дочернему блоку. Проблема в том, что это свойство будет проходить через кучу промежуточных компонентов, которые будут каждый раз перерисовываться при изменении состояния. 

<aside>
💡 Процесс передачи по цепочке называется пробрасывание (англ. prop drilling)

</aside>

![%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2036%207f1db567b9524b51983345b16418b311/4.png](%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2036%207f1db567b9524b51983345b16418b311/4.png)

Как раз эту проблему и решает Context API: он позволяет передавать свойства, минуя промежуточные звенья:

![%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2036%207f1db567b9524b51983345b16418b311/5.png](%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2036%207f1db567b9524b51983345b16418b311/5.png)

<aside>
💡 Компонент, который хранит общее состояние, называется *Provider* (донор). 
Компоненты, принимающие состояние, называются *Consumer* (получатель)

</aside>

Контекст разработан для совместного использования данных, которые нужны большому количеству компонентов приложения на разных уровнях вложенности, например таких как текущий аутентифицированный пользователь, тема или предпочтительный язык.

### Создание Context

Создается контекст очень легко. Для этого нужно создать компонент контекста и обернуть им наше приложение.
Для создания нового контекста, нужно воспользоваться методом `React.createContext`

```jsx
const ThemeContext = React.createContext(defaultValue); 
//Название для нового контекста может быть любым
```

<aside>
💡 Контекстов в приложении может быть несколько, в таком случае, создать нужно будет несколько независимых компонентов

</aside>

Нужно сделать так, чтобы все приложение имело доступ к Context. Для этого нужно обернуть всё приложение в `ThemeContext.Provider`. Ещё стоит передать ему свойство value. В нём будет храниться состояние, доступное вложенным компонентам

```jsx
<ThemeContext.Provider value={"light"}>
  <App />
</ThemeContext.Provider>
```

### Получение свойств из Context

Использование свойств контекста также реализуется через оборачивание компонента. 
В тех компонентах, где это необходимо, добавляется `ThemeContext.Consumer`

```jsx
<ThemeContext.Consumer>
  {value =>
    <button className={value}>value.toUpperCase()</button>
  }
</ThemeContext.Consumer>;
```

### Изменение Context

В остальном контекст очень похож на обычные компоненты. Мы можем применять уже полученные нами знания, и передавать в контекст динамические данные, а также методы для его изменения: 

```jsx
class App extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            theme: 'light'
        }
    }
    handleChangeTheme = () =>{
        const theme = this.state.theme;
        this.setState({
            theme: theme==='light'?'dark':'light'
        })
    }
    render() {
        return (
            <ThemeContext.Provider value={
                {
                    theme: this.state.theme,
                    handleChangeTheme: this.handleChangeTheme,
                }
            }>
                <Main/>
            </ThemeContext.Provider>
        )
    }

}
```

Теперь во вложенных компонентах нам будет доступен метод `handleChangeTheme`, который может менять состояние корневого элемента. Как только состояние обновится, все компоненты, которые принимают его как props, также обновятся:

```jsx
function Main() {
    return (
        <ThemeContext.Consumer>
            {({theme, handleChangeTheme}) =>
                <button onClick={handleChangeTheme}>{theme}</button>
            }
        </ThemeContext.Consumer>
    );
}
```

## Context в функциональном компоненте

### Создание context

```jsx
import React, { useState } from "react";
const ThemeContext = React.createContext();

function ThemeContextProvider(props) {
  const [theme, setTheme] = useState("dark");

  function toggleTheme() {
    setTheme(prevTheme => (prevTheme === "light" ? "dark" : "light"));
  }

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {props.children}
    </ThemeContext.Provider>

  );
}

export { ThemeContextProvider, ThemeContext };
```

### Предоставление доступа к контексту

Предоставление доступа к контексту в функциональном компоненте работает так же как и в классовом компоненте:

```jsx
<ThemeContextProvider>
	<App />
</ThemeContextProvider>
```

### Использование значений контекста внутри компонента

Значения из контекста внутри компонента можно получить с помощью хука ***useContext***

```jsx
import React, { useContext } from "react";
import { ThemeContext } from "./themeContext";

function Button() {
  const { theme, toggleTheme } = **useContext**(ThemeContext);
  return (
    <button onClick={toggleTheme} className={`${theme}-theme`}>
      Switch Theme
    </button>
  );
}

export default Button;
```

## Работа с API

Очень распространённой задачей в современных приложениях является получение отдельных элементов данных с сервера для обновления разделов страницы. 

В подавляющем большинстве случаев данные находятся где-то на сервере, могут добавляться и изменяться другими пользователями, зависеть от сторонних серверов и т.д.

Первоначальная загрузка страницы в Интернете была простой - вы отправляли запрос на сервер, и если всё работает, как и должно, то вся необходимая информация о странице будет загружена и отображена на вашем компьютере.

![%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2036%207f1db567b9524b51983345b16418b311/Untitled.png](%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2036%207f1db567b9524b51983345b16418b311/Untitled.png)

Проблема с этой моделью заключается в том, что всякий раз, когда вы хотите обновить любую часть страницы, например, чтобы отобразить новый набор продуктов или загрузить новую страницу, вам нужно снова загрузить всю страницу. 

Это приводит к потере производительности и не соответствует философии React.

К тому же, мы не можем предсказать, как долго будет отвечать нам сервер и какой код ответа он нам вернет. Это значит, что нам нужно проработать не только положительные сценарии, но и поведение при загрузке (loading) и ошибках.

Это привело к созданию технологий, позволяющих веб-страницам запрашивать небольшие фрагменты данных (например, HTML, XML, JSON или обычный текст) и отображать их только при необходимости, помогая решать проблему, описанную выше.

![%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2036%207f1db567b9524b51983345b16418b311/Untitled%201.png](%D0%9D%D0%B5%D0%B4%D0%B5%D0%BB%D1%8F%2036%207f1db567b9524b51983345b16418b311/Untitled%201.png)

На данный момент существует несколько решений для обращения к API. Но у них есть общая черта: все они являются асинхронными. 

Асинхронный запрос - это такой запрос, который выполняется в фоновом режиме и не мешает пользователю взаимодействовать со страницей.
На практике это означает, что пользователь может спокойно продолжать пользоваться приложением, пока приложение ожидает ответ сервера, и обновиться, когда получит ответ или ошибку. 

На сегодняшний день существует множество решений для создания запросов

**Устаревающие**: 

[XMLHttpRequest](https://developer.mozilla.org/ru/docs/Web/API/XMLHttpRequest)

**Современные:**

[A Fetch API Primer | DigitalOcean](https://www.digitalocean.com/community/tutorials/js-fetch-api)

[Axios](https://axios-http.com/)

**Набирающие популярность:** 

[A query language for your API](https://graphql.org/)

<aside>
💡 В нашем курсе мы будем использовать Fetch API, т.к. это удобный и популярный инструмент самого JS для создания запросов. Освоив его, вам будет очень легко переключиться на работу других библиотек.

</aside>

### API & React

Существует несколько мест в иерархии компонента React, где вы можете получать данные. 
Но когда именно это делать - уже другая история. Вам также нужно определить, какую технологию для получения данных использовать и где потом хранить эти данные.

<aside>
💡 Мы подготовили API, к которому вы можете обращаться для написания своего курсового проекта. Он доступен по адресу [http://itgirlschool.justmakeit.ru/api/words](http://itgirlschool.justmakeit.ru/api/words)

</aside>

API - очень прост. Есть единая точка входа /words. Он выводит все сохраненные слова в ответ на HTTP GET запрос.
GET Получение слова по id: [http://itgirlschool.justmakeit.ru/api/words](http://itgirlschool.justmakeit.ru/api/words)[/2](http://sandbox.itgirlschool.ru/api/words/2) (Подставить id)

POST Добавление нового слова: [http://itgirlschool.justmakeit.ru/api/words](http://itgirlschool.justmakeit.ru/api/words)[/add](http://sandbox.itgirlschool.ru/api/words/add)

POST Изменение слова: [http://itgirlschool.justmakeit.ru/api/words](http://itgirlschool.justmakeit.ru/api/words)[/22/update](http://sandbox.itgirlschool.ru/api/words/22/update) (Подставить id)

POST Удаление слова: [http://itgirlschool.justmakeit.ru/api/words](http://itgirlschool.justmakeit.ru/api/words)[/22/delete](http://sandbox.itgirlschool.ru/api/words/22/delete) (Подставить id)

### Безопасность и CORS

Если бы мы сейчас попытались сделать наш запрос на [http://itgirlschool.justmakeit.ru](http://itgirlschool.justmakeit.ru/api/words)[/api/words](http://sandbox.itgirlschool.ru/api/words) из приложения, развернутого на localhost, то получили бы такую ошибку: 

<aside>
❌ Access to fetch at '[http://itgirlschool.justmakeit.ru/api/words](http://itgirlschool.justmakeit.ru/api/words)' from origin 'http://localhost:3000' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.

</aside>

Так выглядит забота разработчиков браузеров о нашей безопасности. 
Причиной, по которой мы не получим ответ в JavaScript, является Same-Origin Policy. По умолчанию мы можем получить доступ к ресурсам только в том случае, если источник этих ресурсов и источник запроса совпадают.

Например: если вы заходите на **example.com**, вы бы не хотели, чтобы этот веб-сайт отправлял запрос на ваш банковский веб-сайт и получал баланс вашего счета и транзакции.

**Same-Origin Policy** предотвращает именно это.

«источник (origin)» в этом случае состоит из

- протокол (например `http`)
- хост (например `example.com`)
- порт (например `8000`)

Так что **http://sample.org** и **http://www.sample.org** и **http://sample.org:3000** — это три разных источника.

### CORS на стороне клиента

Чтобы обеспечить безопасность запросов к другим источникам, браузеры используют механизм под названием **CORS**. Аббревиатура CORS расшифровывается как Cross-Origin Resource Sharing (Технология совместного использования ресурсов между разными источниками)

<aside>
💡 Можно использовать CORS, чтобы внести небольшие коррективы в ограничения браузеров и при этом быть уверенным, что доступ будет безопасным

</aside>

Для того, чтобы запрашивать данные с другого домена, нужно добавить параметр `proxy` в `package.json`

```json
{
  ...
  "proxy": "http://itgirlschool.justmakeit.ru",
  ...
}
```

Теперь запрос на адрес "/api/words" будет перенаправлен на [http://itgirlschool.justmakeit.ru/api/words,](http://itgirlschool.justmakeit.ru/api/words) и мы избежим ошибки

При необходимости, мы можем также направлять запросы на разные домены в зависимости от адреса. Для этого нужно отредактировать `proxy` в `package.json` следующим образом:

```json
{
  "proxy": {
    "/search": {
      "target": "https://www.google.com",
      "changeOrigin": true
    },
    "/api": {
      "target": "http://itgirlschool.justmakeit.ru",
      "changeOrigin": true
    }
  }
}
```

### Fetch и промисы

С [GET и POST запросами](https://www.notion.so/21-5b373c9b7c1240039c3e76d459c19956) мы уже познакомились в блоке про JS, теперь посмотрим как эти технологии применяются совместно с React.

Мы уже настроили `proxy` в `package.json`

```json
{
  ...
  "proxy": "http://itgirlschool.justmakeit.ru",
  ...
}
```

Создадим простой компонент `Words`, который будет выводить список всех слов, пришедших с сервера. 

```jsx
class Words extends React.Component {
    constructor(props){
        super(props);
        this.state = {
            words: []
        }
    }

    componentDidMount() {
        fetch('/api/words')
            .then((response) => response.json())
            .then((response) => this.setState({ words: response }))
    }

    render (){
        const {words} = this.state

        return(
            <ol>
                {
                    words.map(word => {
                        return <li key={word.id}>{word.english} - {word.russian}</li>
                    })
                }
            </ol>
        )
    }
}
```

Изначально список слов пуст. Мы делаем запрос на сервер, и когда получаем новые данные, обновляем состояние компонента. 

<aside>
💡 Метод `componentDidMount` считается самым удачным местом для первого запроса данных с сервера

</aside>

Так как к этому моменту компонент уже смонтирован, то мы не получаем потери производительности. `componentDidMount` вызывается **один раз** в жизненном цикле сразу после монтирования компонента в DOM. Перед выполнением этого метода вызывается метод рендеринга. 

Если нам понадобится делать запросы многократно(например, добавлять/изменять/удалять данные), то для этого нам нужно будет создавать отдельные методы, привязанные к действиям пользователя. Но процесс составления запроса и обновление состояния при этом не изменится. 

### Загрузка и обработка ошибок

Наш компонент уже исправно функционирует, но для улучшения пользовательского опыта, нам необходимо показывать индикатор загрузки, а также выводить сообщения при ошибках.

Индикатор загрузки должен появляться в период ожидания ответа сервера. Добавим состояние `isLoading` компоненту, и это значение может быть true или false. Воспользуемся нашими знаниями об условном рендеринге и будем выводить индикатор загрузки, пока мы не получили ответ сервера. Важно не забыть переключить этот параметр на false, когда запрос будет завершен.

```jsx
class Words extends React.Component {
    constructor(props){
        super(props);
        this.state = {
            words: [],
            isLoading: false, //Изначально индикатор закрузки выключен
        }
    }

    componentDidMount() {
        this.setState({ isLoading: true }); //Включаем индикатор

        fetch('/api/words')
            .then((response) => response.json())
            .then((response) => {
                this.setState({
                    words: response,
                    isLoading: false, //Выключаем индикатор
                })
            })
    }

    render (){
        const {words, isLoading} = this.state

        if (isLoading) {
            return <p>Loading ...</p>; 
        }

        return(
            <ol>
                {
                    words.map(word => {
                        return <li key={word.id}>{word.english} - {word.russian}</li>
                    })
                }
            </ol>
        )
    }
}
```

Так как в методе `render` мы использовали `return`, то при выполнении `isLoading==true`, весь дальнейший код будет проигнорирован, и на экране будет отображаться только индикатор загрузки.

Добавим еще одно свойство для отображения ошибок.  Очень важно сообщить пользователю о том, что произошла ошибка, иначе поведение приложения будет ему непонятно, что приведет к негативному пользовательскому опыту.

<aside>
💡 Сообщения об ошибках не являются негативным пользовательским опытом. Наоборот, понятные сообщения улучшают ваш сервис

</aside>

Для отлова ошибок в Fetch API существует метод `catch`, который будет вызван, если сервер вернул код ошибки. При этом, некоторые коды ошибок он считает за валидные, например 401(ошибка авторизации) и 404(файл не найден). Поэтому мы должны добавить еще одну проверку и насильно выводить ошибку при таких ответах: 

```jsx
class Words extends React.Component {
    constructor(props){
        super(props);
        this.state = {
            words: [],
            isLoading: false,
            error: null,
        }
    }

    componentDidMount() {
        this.setState({ isLoading: true });

        fetch('/api/words')
            .then(response => {
                if (response.ok) { //Проверяем что код ответа 200
                    return response.json();
                } else {
                    throw new Error('Something went wrong ...');
                }
            })
            .then((response) => {
                this.setState({
                    words: response,
                    isLoading: false,
                })
            })
            .catch(error => this.setState({ error, isLoading: false }));
    }

    render (){
        const { words, isLoading, error } = this.state;

        if (error) {
            return <p>{error.message}</p>;
        }

        if (isLoading) {
            return <p>Loading ...</p>;
        }

        return(
            <ol>
                {
                    words.map(word => {
                        return <li key={word.id}>{word.english} - {word.russian}</li>
                    })
                }
            </ol>
        )
    }
}
```

Запросы к API и обработка ответов являются неотъемлемой частью работы современных приложений. При этом  можно заметить, как сильно из-за них разрастается логика наших приложений. Поэтому чаще всего инструменты работы с API и передачи состояний используются совместно. На следующем уроке мы посмотрим как еще мы можем управлять состояниями и познакомимся с технологией MobX.

## Домашнее задание на эту неделю:

### Вопросы 💎

1. Можно ли создать несколько контекстов в одном приложении?
2. Можем ли мы влиять на контекст из вложенных компонентов?
3. Как выбрать компонент, в котором будет определяться контекст?
4. Возможно ли создать контекст, который доступен только в части приложения? 
5. На каком этапе жизненного цикла компонента лучше всего запросить данные с сервера?
6. Вернет ли сообщение об ошибке Fetch API, если код ответа 401 (ошибка авторизации)?
7. Каким еще способом можно делать запросы к API кроме fetch?
8. Зачем нужен CORS?

### Практическое задание 💎

**Задание для проекта:** 

<aside>
💡 На следующем уроке мы будем проходить еще один способ передачи состояния компонентам, поэтому предлагаем создать новую git-ветку приложения, чтобы попробовать сделать оба метода и сравнить их на практике

</aside>

- Создайте новую git-ветку приложения и перейдите в нее
- Создайте общий контекст приложения, в котором будет храниться коллекция всех слов
- Создайте метод для получения списка слов из базы, он должен вызываться в функции `componentDidMount`. Не забудьте о проверке ошибок и индикаторе загрузки.
- Передайте полученные данные в таблицу и в компонент с карточками слов
- Создайте методы для изменения, удаления и добавления слов. Проверьте, что они работают. Передайте их в компонент, отвечающий за отображение таблицы слов
- При сохранении изменений слова в таблице, отправьте изменения на сервер и обновите таблицу.
- Создайте компонент для отображения ошибки, стилизуйте его

<aside>
💡 Бонусное задание, творческое 🎨 
Создайте компонент для индикатора загрузки. Индикатор загрузки должен быть анимированным. Вдохновиться можно [тут](https://uxplanet.org/10-creative-loading-indicators-1a15c562b75a)

</aside>
