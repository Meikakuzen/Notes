# Instalación

>npm i react-redux redux redux-thunk

# Crear store, Reducer y dev tools

- Crear archivo store en src. Importar createStore, applyMiddleware, compose de redux y thunk (sin llaves)
- Crear carpeta Reducers con index.js y productosReducer.js
- En el index.js importar combineReducers de redux
- Declaro el objeto de initialState en productosReducer, seguido del reducer (switch(action.type)) exportado por defecto con el initialState y el action cómo parámetros por defecto
- El reducer siempre devuelve por defecto el state
- Importo el reducer en el index.js de la carpeta Reducers
    - Declaro combineReducers(con export default) con el state productos y el productosReducer
- Importo el reducer de CombineReducers en el store
    - Creo el store con createStore.
    - Lo exporto por defecto
    - Le paso como parámetros el reducer
    - El compose con el applyMiddleware dentro y el thunk.
    - Como segundo parámetro del compose la configuración de redux dev tools
- En app.js importo el provider de react-redux entre llaves
    - Importo el store
    - Incorporo el provider con el store como props rodeando el enrutado por debajo del BrowserRouter
    - Mejoro el código de las dev tools del store con window.__REDUX_DEVTOOLS_EXTENSION__

~~~js
import {createStore, applyMiddleware, compose} from 'redux'
import thunk from 'redux-thunk'
import reducer from './Reducers'

const store = createStore(
    reducer,
    compose( applyMiddleware(thunk),
    
    typeof window === 'object' &&
    typeof window.__REDUX_DEVTOOLS_EXTENSION__ !== 'undefined' ?
    window.__REDUX_DEVTOOLS_EXTENSION__() : f=> f
    )
)

export default store
~~~
----
- ProductosReducer
~~~js
const initialState ={
    productos: [],
    error: null,
    loading: false
}

export default function(state=initialState, action){
    switch(action.type){
        default:
            return state
    }
}
~~~

----
- Index.js (src/Reducers)
~~~js
import{combineReducers} from 'redux'
import productosReducer from './productosReducer';

export default combineReducers({
    productos: productosReducer
})
~~~
----

# Acciones en Redux

- Creo la carpeta types en src con un index.js
- Exporto cada type 
- Los importo en productosReducer
- creo la carpeta actions en /src con un index.js
    - Importo los types en actions/index.js
    - Creo el primer action, lo llamo crearNuevoProductoAction
    - Hago una función de flecha del return y llamo a un console.log("desde action") para seguirle el rastro
    - Lo importo en el componente indicado (NuevoProducto)
- En NuevoProducto hago en el onSubmit del form, creo una nueva función
- Para crear el nuevo producto ejecutaré una función (agregarProducto) en la función del submit pasada la validación
    - Esta función (agregarProducto) tiene que usar el dispatch, lo importo y lo inicializo
    - Siempre que sea algo del action mandas llamar al dispatch
    - agregarProducto es unafunción que usa dispatch para comunicarse con las acciones y manda ejecutar esta función
    - El dispatch es una función que toma otra función, le paso el crearNuevoProductoAction
    - Ahora si le doy a submit aparece desde actione en consola
- Nuevo Producto
~~~js
import React from 'react'
import { crearNuevoProductoAction } from '../actions'
import {useDispatch, useSelector} from 'react-redux'


const NuevoProducto = () => {

const dispatch= useDispatch()
   
const agregarProducto=()=>dispatch(crearNuevoProductoAction())

const submitNuevoProducto =(e)=>{
    e.preventDefault()

    agregarProducto()
}


  return (
    <div className="row justify-content-center">
        <div className="col-md-8">
            <div className="card">
                <div className="card-body shadow mt-3">
                    <h2 className="text-center mb-4 fw-bold">Agregar Nuevo Producto</h2>
                    <form onSubmit={submitNuevoProducto}>
                        <div className="form-group">
                            <label className="mb-2">Nombre Producto</label>
                            <input type="text" 
                            className="form-control"
                            placeholder="Nombre producto"
                            name="nombre"/>
                        </div>
                        <div className="form-group mt-3">
                            <label className="mb-2">Precio Producto</label>
                            <input type="text" 
                            className="form-control"
                            placeholder="Precio"
                            name="precio"/>
                        </div>
                        
                        <button type="submit" className="btn btn-primary fw-bold text-uppercase d-block w-100 mt-3">Agregar</button>
                    </form>
                </div>
            </div>
        </div>

    </div>
  )
}

export default NuevoProducto
~~~
---
- actions/index.js

~~~js
import{
    AGREGAR_PRODUCTO,
    AGREGAR_PRODUCTO_EXITO,
    AGREGAR_PRODUCTO_ERROR
} from '../types'


export function crearNuevoProductoAction(){
    return()=> console.log('desde action')
}
~~~
----
# Defino el state y lo paso al reducer
- Importo el useState en NuevoProducto. Uso el value y el onChange para captar el input con e.target.value
- Valido el formulario
- Le paso el state a agregarProducto y coloco producto como parámetro de las funciones, también en el crearNuevoProductoAction
    - Ahora en lugar del console.log("desde action") le puedo pasar producto para que lo imprima en consola
    - Es en actions donde se hacen las consultas a las bases de datos

- NuevoProducto
~~~js
import {useState} from 'react'
import { crearNuevoProductoAction } from '../actions'
import {useDispatch, useSelector} from 'react-redux'


const NuevoProducto = () => {

const dispatch= useDispatch()

const [nombre, guardarNombre] = useState('')
const [precio, guardarPrecio] = useState(0)
   
const agregarProducto=(producto)=>dispatch(crearNuevoProductoAction(producto))

const submitNuevoProducto =(e)=>{
    e.preventDefault()
    if (nombre.trim()==='' || precio <= 0){
        return
    }
    agregarProducto({
        nombre, precio
    })
}


  return (
    <div className="row justify-content-center">
        <div className="col-md-8">
            <div className="card">
                <div className="card-body shadow mt-3">
                    <h2 className="text-center mb-4 fw-bold">Agregar Nuevo Producto</h2>
                    <form onSubmit={submitNuevoProducto}>
                        <div className="form-group">
                            <label className="mb-2">Nombre Producto</label>
                            <input type="text" 
                            className="form-control"
                            placeholder="Nombre producto"
                            name="nombre"
                            value={nombre}
                            onChange={(e)=>guardarNombre(e.target.value)} />
                        </div>
                        <div className="form-group mt-3">
                            <label className="mb-2">Precio Producto</label>
                            <input type="text" 
                            className="form-control"
                            placeholder="Precio"
                            name="precio"
                            value={precio}
                            onChange={(e)=> guardarPrecio(Number(e.target.value))} />
                        </div>
                        
                        <button type="submit" className="btn btn-primary fw-bold text-uppercase d-block w-100 mt-3">Agregar</button>
                    </form>
                </div>
            </div>
        </div>

    </div>
  )
}

export default NuevoProducto

~~~
- actions
~~~js
import{
    AGREGAR_PRODUCTO,
    AGREGAR_PRODUCTO_EXITO,
    AGREGAR_PRODUCTO_ERROR
} from '../types'


export function crearNuevoProductoAction(producto){
    return()=> console.log(producto)
}
~~~

- RESUMEN: en Redux vas a definir los types que es lo que pasa en la aplicación
    - Después en el action declaro una función que se tiene que usar en el componente
    - De esta forma los datos del componente se pueden pasar a las acciones
    - Utilizo dispatch para ejecutar esas acciones
    - Y donde el console.log(precio) es donde se incluye toda la lógica para insertar en la base de datos y también mandar ejecutar el reducer
-----
# Agregar Nuevo Producto

- Añado el dispatch como parámetro al return de crearNuevoProductoAction (actions) y lo uso con una nueva función que llamaré agregarProducto
- Creo la función agregarProducto, abro parentesis, abro llaves, incluyo el type AGREGAR_PRODUCTO y el payload en true(será para el loading)
- Creo el caso en el reducer. Retorno una copia del state y seteo el loading con action.payload
    - Coloco un try catch dentro del return despues del dispatch para manejar la llamada a la base de datos y el error
    - En el try coloco el dispatch con una nueva función llamada agregarProductoExito
    - En el catch coloco en el dispatch agregarProductoError
    - Genero las dos acciones respectivas
    - Incluyo los cases en el reducer 
- index.js
~~~js
import{
    AGREGAR_PRODUCTO,
    AGREGAR_PRODUCTO_EXITO,
    AGREGAR_PRODUCTO_ERROR
} from '../types'


export function crearNuevoProductoAction(producto){
    return(dispatch)=>{
        dispatch(agregarProducto())
        try {
            dispatch( agregarProductoExito(producto))
        } catch (error) {
            
            dispatch(agregarProductoError(true))
        }
    } 
}

const agregarProducto=()=>({
    type: AGREGAR_PRODUCTO,
    payload: true
})

const agregarProductoExito=(producto)=>({

    type: AGREGAR_PRODUCTO_EXITO,
    payload: producto

})

const agregarProductoError = (estado)=>({
    type: AGREGAR_PRODUCTO_ERROR,
    payload: estado

})

~~~
----
- productosReducer
~~~js
import{
    AGREGAR_PRODUCTO,
    AGREGAR_PRODUCTO_EXITO,
    AGREGAR_PRODUCTO_ERROR
} from '../types'


const initialState ={
    productos: [],
    error: null,
    loading: false
}

export default function(state=initialState, action){
    switch(action.type){
        case AGREGAR_PRODUCTO:
            return{
                ...state,
                loading: action.payload
            }
        case AGREGAR_PRODUCTO_EXITO:
            return{
                ...state,
                loading: false,
                productos: [...state.productos, action.type]
            }
        case AGREGAR_PRODUCTO_ERROR:
            return{
                ...state,
                loading: false,
                error: action.payload
            }
        default:
            return state
    }
}
~~~
----
- Configuro axios (clienteAxios) con la baseUrl del server para no estar repitiendo la url
- Lo importo en action y lo añado en el try con el método post con '/productos' como primer argumento y el producto com segundo
-  actions/index.js
~~~js
import{
    AGREGAR_PRODUCTO,
    AGREGAR_PRODUCTO_EXITO,
    AGREGAR_PRODUCTO_ERROR
} from '../types'

import clienteAxios from '../config/axios'


export function crearNuevoProductoAction(producto){
    return(dispatch)=>{
        dispatch(agregarProducto())
        try {
            clienteAxios.post('/productos', producto)
            dispatch( agregarProductoExito(producto))
        } catch (error) {
            
            dispatch(agregarProductoError(true))
        }
    } 
}

const agregarProducto=()=>({
    type: AGREGAR_PRODUCTO,
    payload: true
})

const agregarProductoExito=(producto)=>({

    type: AGREGAR_PRODUCTO_EXITO,
    payload: producto

})

const agregarProductoError = (estado)=>({
    type: AGREGAR_PRODUCTO_ERROR,
    payload: estado

})

~~~
----
- config/axios
~~~js
import axios from 'axios'

const clienteAxios = axios.create({
    baseURL:'http://localhost:4000'
})

export default clienteAxios
