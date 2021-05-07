brew install yarn
yarn init
yarn add express

touch ./server.js
yarn add express-react-views react react-dom
```js
const express = require('express')
const app = express()

const port = process.env.PORT || 3000

app.use(express.static('public'))

// views directory
app.set('views', __dirname + '/views')

// view engine set jsx
app.set('view engine', 'jsx')

// create express-react-views
app.engine('jsx', require('express-react-views').createEngine())


app.get('/', (req, res) => {
    res.render('index', { name: 'Rhapsodist' })
})

app.listen(port, () => console.log(`express started with port ${port}`))

```
node server.js