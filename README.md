# Read Me

![Redux-saga](https://redux-saga.js.org/logo/0800/Redux-Saga-Logo-Landscape.png)

## redux-saga <a id="redux-saga"></a>

redux-saga es una biblioteca que tiene como objetivo hacer que los efectos secundarios de la aplicación \(es decir, cosas asincrónicas como la obtención de datos y cosas impuras como acceder al caché del navegador\) sean más fáciles de administrar, más eficientes de ejecutar, fáciles de probar y mejores para manejar fallas.

El modelo mental es que una saga es como un hilo separado en su aplicación que es el único responsable de los efectos secundarios. redux-saga es un middleware redux, lo que significa que este hilo puede iniciarse, pausarse y cancelarse desde la aplicación principal con acciones redux normales, tiene acceso al estado completo de la aplicación redux y puede enviar acciones redux también.

Es posible que haya usado redux-thunk antes para manejar su recuperación de datos. Contrariamente al redux thunk, no terminas en el infierno de devolución de llamada, puedes probar tus flujos asincrónicos fácilmente y tus acciones se mantienen puras.

#### Instalación <a id="instalaci&#xF3;n"></a>

Para instalar la versión estable:

`$ npm i -S redux-saga` o `$ yarn add redux-saga`

Alternativamente, puede usar las compilaciones UMD proporcionadas directamente en la etiqueta de una página HTML. Ver [esta sección](https://redux-saga.js.org/#using-umd-build-in-the-browser).

**Ejemplo de uso**

Supongamos que tenemos una interfaz de usuario para obtener algunos datos de usuario de un servidor remoto cuando se hace clic en un botón. \(Por brevedad, solo mostraremos el código de activación de la acción\).

```javascript
class UserComponent extends React.Component {
  ...
  onSomeButtonClicked() {
    const { userId, dispatch } = this.props
    dispatch({type: 'USER_FETCH_REQUESTED', payload: {userId}})
  }
  ...
}
```

El componente envía una acción de objeto simple a la tienda. Crearemos una Saga que vigile todas las acciones de USER\_FETCH\_REQUESTED y active una llamada API para obtener los datos del usuario.

#### `sagas.js`

```javascript
import { call, put, takeEvery, takeLatest } from 'redux-saga/effects'
import Api from '...'

// worker Saga: will be fired on USER_FETCH_REQUESTED actions
function* fetchUser(action) {
   try {
      const user = yield call(Api.fetchUser, action.payload.userId);
      yield put({type: "USER_FETCH_SUCCEEDED", user: user});
   } catch (e) {
      yield put({type: "USER_FETCH_FAILED", message: e.message});
   }
}

/*
  Starts fetchUser on each dispatched `USER_FETCH_REQUESTED` action.
  Allows concurrent fetches of user.
*/
function* mySaga() {
  yield takeEvery("USER_FETCH_REQUESTED", fetchUser);
}

/*
  Alternatively you may use takeLatest.

  Does not allow concurrent fetches of user. If "USER_FETCH_REQUESTED" gets
  dispatched while a fetch is already pending, that pending fetch is cancelled
  and only the latest one will be run.
*/
function* mySaga() {
  yield takeLatest("USER_FETCH_REQUESTED", fetchUser);
}

export default mySaga;
```

Para ejecutar nuestra Saga, tendremos que conectarla a la Tienda Redux usando el middleware redux-saga.

**main.js**

```javascript
import { createStore, applyMiddleware } from 'redux'
import createSagaMiddleware from 'redux-saga'

import reducer from './reducers'
import mySaga from './sagas'

// create the saga middleware
const sagaMiddleware = createSagaMiddleware()
// mount it on the Store
const store = createStore(
  reducer,
  applyMiddleware(sagaMiddleware)
)

// then run the saga
sagaMiddleware.run(mySaga)

// render the application
```

## Documentación

* \[Introducción\] \([https://redux-saga.js.org/docs/introduction/BeginnerTutorial.html](https://redux-saga.js.org/docs/introduction/BeginnerTutorial.html)\)
* \[Conceptos básicos\] \([https://redux-saga.js.org/docs/basics/index.html](https://redux-saga.js.org/docs/basics/index.html)\)
* \[Conceptos avanzados\] \([https://redux-saga.js.org/docs/advanced/index.html](https://redux-saga.js.org/docs/advanced/index.html)\)
* \[Recetas\] \([https://redux-saga.js.org/docs/recipes/index.html](https://redux-saga.js.org/docs/recipes/index.html)\)
* \[Recursos externos\] \([https://redux-saga.js.org/docs/ExternalResources.html](https://redux-saga.js.org/docs/ExternalResources.html)\)
* \[Solución de problemas\] \([https://redux-saga.js.org/docs/Troubleshooting.html](https://redux-saga.js.org/docs/Troubleshooting.html)\)
* \[Glosario\] \([https://redux-saga.js.org/docs/Glossary.html](https://redux-saga.js.org/docs/Glossary.html)\)
* \[Referencia API\] \([https://redux-saga.js.org/docs/api/index.html](https://redux-saga.js.org/docs/api/index.html)\)

## Traducción

* \[Chino\] \([https://github.com/superRaytin/redux-saga-in-chinese](https://github.com/superRaytin/redux-saga-in-chinese)\)
* \[Chino tradicional\] \([https://github.com/neighborhood999/redux-saga](https://github.com/neighborhood999/redux-saga)\)
* \[Japonés\] \([https://github.com/redux-saga/redux-saga/blob/master/README\_ja.md](https://github.com/redux-saga/redux-saga/blob/master/README_ja.md)\)
* \[Coreano\] \([https://github.com/mskims/redux-saga-in-korean](https://github.com/mskims/redux-saga-in-korean)\)
* \[Portugués\] \([https://github.com/joelbarbosa/redux-saga-pt\_BR](https://github.com/joelbarbosa/redux-saga-pt_BR)\)
* \[Ruso\] \([https://github.com/redux-saga/redux-saga/blob/master/README\_ru.md](https://github.com/redux-saga/redux-saga/blob/master/README_ru.md)\)

## Usando umd build en el navegador

También hay una compilación **umd** de `redux-saga` disponible en la carpeta `dist/`. Cuando se utiliza la compilación umd, `redux-saga` está disponible como`ReduxSaga` en el objeto de la ventana. Esto le permite crear el middleware Saga sin usar la sintaxis ES6 `import` como esta:

```javascript
var sagaMiddleware = ReduxSaga.default()
```

La versión umd es útil si no usa Webpack o Browserify. Puede acceder directamente desde \[unpkg\] \([https://unpkg.com/](https://unpkg.com/)\).

Las siguientes compilaciones están disponibles:

* \[[https://unpkg.com/redux-saga/dist/redux-saga.umd.jsfont&gt;\(https://unpkg.com/redux-saga/dist/redux-saga.umd.js](https://unpkg.com/redux-saga/dist/redux-saga.umd.jsfont>%28https://unpkg.com/redux-saga/dist/redux-saga.umd.js)\)
* \[[https://unpkg.com/redux-saga/dist/redux-saga.umd.min.jsfont&gt;\(https://unpkg.com/redux-saga/dist/redux-saga.umd.min.js](https://unpkg.com/redux-saga/dist/redux-saga.umd.min.jsfont>%28https://unpkg.com/redux-saga/dist/redux-saga.umd.min.js) \)

**¡Importante!** Si el navegador al que apunta no es compatible con _generadores ES2015_, debe transpilarlos \(es decir, con \[babel plugin\] \([https://github.com/facebook/regenerator/tree/master/packages](https://github.com/facebook/regenerator/tree/master/packages) / regenerator-transform\)\) y proporciona un tiempo de ejecución válido, como \[el que está aquí\] \([https://unpkg.com/regenerator-runtime/runtime.js](https://unpkg.com/regenerator-runtime/runtime.js)\). El tiempo de ejecución debe importarse antes de **redux-saga**:

```javascript
import 'regenerator-runtime/runtime'
// then
import sagaMiddleware from 'redux-saga'
```

## Construyendo ejemplos de fuentes

```bash
$ git clone https://github.com/redux-saga/redux-saga.git
$ cd redux-saga
$ yarn
$ npm test
```

A continuación se muestran los ejemplos portados \(hasta ahora\) de los repositorios de Redux.

#### Ejemplos de contadores

Hay tres ejemplos contrarios.

**contra vainilla**

Demostración usando JavaScript vanilla y compilaciones UMD. Toda la fuente está en línea en `index.html`.

Para iniciar el ejemplo, abra `index.html` en su navegador.

> Importante: su navegador debe ser compatible con los generadores. Las últimas versiones de Chrome / Firefox / Edge son adecuadas.

**mostrador**

Demostración con `webpack` y API de alto nivel`takeEvery`.

```bash
$ npm run counter

# test sample for the generator
$ npm run test-counter
```

**contador cancelable**

Demostración con API de bajo nivel para demostrar la cancelación de tareas.

```bash
$ npm run cancellable-counter
```

#### Ejemplo de carrito de compras

```bash
$ npm run shop

# test sample for the generator
$ npm run test-shop
```

#### ejemplo asíncrono

```bash
$ npm run async

# test sample for the generators
$ npm run test-async
```

#### ejemplo del mundo real \(con recarga de paquete web\)

```bash
$ npm run real-world

# sorry, no tests yet
```

#### TypeScript

Redux-Saga con TypeScript requiere `DOM.Iterable` o`ES2015.Iterable`. Si su `objetivo` es`ES6`, es probable que ya esté configurado, sin embargo, para `ES5`, deberá agregarlo usted mismo. Verifique su archivo `tsconfig.json` y la documentación oficial de  [opciones del compilador](https://www.typescriptlang.org/docs/handbook/compiler-options.html).

#### Logo

Puede encontrar el logotipo oficial de Redux-Saga con diferentes sabores en el \[directorio de logotipos\] \([https://github.com/redux-saga/redux-saga/tree/master/logo](https://github.com/redux-saga/redux-saga/tree/master/logo)\).

### Redux Saga elige generadores en lugar de `async/await`

A \[pocos\] \([https://github.com/redux-saga/redux-saga/issues/1373\#issuecomment-381320534](https://github.com/redux-saga/redux-saga/issues/1373#issuecomment-381320534)\) \[problemas\] \([https://github.com/redux-saga/redux-saga/issues/987\#issuecomment-301039792](https://github.com/redux-saga/redux-saga/issues/987#issuecomment-301039792)\) se han planteado preguntando si la saga Redux planea usar la sintaxis `async/await` en lugar de generadores.

Continuaremos usando \[generadores\] \([https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Generator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator)\). El mecanismo principal de 'async/await' es Promises y es muy difícil mantener la simplicidad de programación y la semántica de los conceptos existentes de Saga usando Promises. `async/await` simplemente no permite ciertas cosas, como la cancelación. Con los generadores tenemos pleno poder sobre cómo y cuándo se ejecutan los efectos.

### Patrocinadores

Apóyanos con una donación mensual y ayúdanos a continuar con nuestras actividades. \[[Conviértase en un patrocinador](https://opencollective.com/redux-saga#backer)\]

#### Patrocinadores

Conviértase en un patrocinador y obtenga su logotipo en nuestro archivo README en Github con un enlace a su sitio. \[[Conviértase en un patrocinador](https://opencollective.com/redux-saga#sponsor)\]

### License

Copyright \(c\) 2015 Yassine Elouafi.

Licensed under The MIT License \(MIT\).

