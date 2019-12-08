# Read Me

![Redux-saga](https://redux-saga.js.org/logo/0800/Redux-Saga-Logo-Landscape.png)

## Read Me

redux-saga es una biblioteca que tiene como objetivo hacer que los efectos secundarios de la aplicación \(es decir, cosas asincrónicas como la obtención de datos y cosas impuras como acceder al caché del navegador\) sean más fáciles de administrar, más eficientes de ejecutar, fáciles de probar y mejores para manejar fallas.

El modelo mental es que una saga es como un hilo separado en su aplicación que es el único responsable de los efectos secundarios. redux-saga es un middleware redux, lo que significa que este hilo puede iniciarse, pausarse y cancelarse desde la aplicación principal con acciones redux normales, tiene acceso al estado completo de la aplicación redux y puede enviar acciones redux también.

Es posible que haya usado redux-thunk antes para manejar su recuperación de datos. Contrariamente al redux thunk, no terminas en el infierno de devolución de llamada, puedes probar tus flujos asincrónicos fácilmente y tus acciones se mantienen puras.

### Instalación <a id="instalaci&#xF3;n"></a>

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

`sagas.js`

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

#### `main.js`

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

