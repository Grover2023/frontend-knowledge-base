# router

## Hash

- Hash 是 URL 的一部分。
- 早期的时候主要被用作锚定。
- Hash 的变化，不会请求服务器。

### 模仿实现

index.html 如下：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <link rel="stylesheet" href="./index.css" />
  </head>
  <body>
    <nav>
      <a href="#home">Home</a>
      <a href="#about">About</a>
      <a href="#contact">Contact</a>
    </nav>
    <div id="content"></div>
    <script src="./index.js"></script>
  </body>
</html>
```

index.css 如下：

```css
body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
}

nav {
  background-color: #333;
  color: white;
  padding: 1em;
  text-align: center;
}

nav a {
  color: white;
  margin: 0 1em;
  text-decoration: none;
}

#content {
  padding: 2em;
}
```

index.js 如下：

```js
document.addEventListener("DOMContentLoaded", () => {
  const content = document.getElementById("content");

  const renderPage = (hash) => {
    switch (hash) {
      case "#home":
        content.innerHTML = "<h1>Home</h1>";
        break;
      case "#about":
        content.innerHTML = "<h1>About</h1>";
        break;
      case "#contact":
        content.innerHTML = "<h1>Contact</h1>";
        break;
      default:
        content.innerHTML = "<h1>404</h1>";
    }
  };

  window.addEventListener("hashchange", () => {
    renderPage(window.location.hash);
  });

  renderPage(window.location.hash || "#home");
});
```

## HTML5 模式

- 基于 History API 实现。
- 用户直接访问 `https://example.com/user/id`，就会得到一个 404 错误。这时，需要在服务器上添加一个回退路由，如果 URL 不匹配任何静态资源，则提供 `index.html` 页面。

### 模仿实现

index.html 如下：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <link rel="stylesheet" href="./index.css" />
  </head>
  <body>
    <nav>
      <a href="/" data-link>Home</a>
      <a href="/about" data-link>About</a>
      <a href="/contact" data-link>Contact</a>
    </nav>
    <div id="content"></div>
    <script src="./index.js"></script>
  </body>
</html>
```

index.css 如下：

```css
body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
}

nav {
  background-color: #333;
  color: white;
  padding: 1em;
  text-align: center;
}

nav a {
  color: white;
  margin: 0 1em;
  text-decoration: none;
}

#content {
  padding: 2em;
}
```

index.js 如下：

```js
document.addEventListener("DOMContentLoaded", () => {
  const content = document.getElementById("content");

  const renderPage = (path) => {
    console.log(path, "path");
    switch (path) {
      case "/":
      case "/index.html":
        content.innerHTML = "<h1>Home</h1>";
        break;
      case "/about":
        content.innerHTML = "<h1>About</h1>";
        break;
      case "/contact":
        content.innerHTML = "<h1>Contact</h1>";
        break;
      default:
        content.innerHTML = "Not Found";
    }
  };

  document.querySelectorAll("a[data-link]").forEach((link) => {
    link.addEventListener("click", (e) => {
      // 阻止默认事件
      e.preventDefault();
      const path = e.target.getAttribute("href");
      // 推入到浏览器历史记录中
      history.pushState(null, null, path);
      renderPage(path);
    });
  });

  renderPage(window.location.pathname || "/");
});
```