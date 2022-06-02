# Next y getStaticProps

- Renderiza en el build time una página o unas propiedades
- La aplicación solo va a mostrar los 151 pokemon, haga lo que haga el usuario.
- Hay varias formas, y hay que elegir la más conveniente
    - Server-Side rendering
    - Static-Side generation
    - Client-Side rendering
    - Dinamic routing
    - Incremental Static Regeneration

- Lo más rápido es generar todo antes de que se haga el request
- En este caso, el tipo sería static-side generation, porque siempre van a ser esos 151 pokemon
- Antes de hacer el request, yo ya se lo que voy a mostrar (151 pokemons)
- Por ello, es adecuado usar GetStaticProps cuando la data requerida para renderizar esta disponible en build time y no en tiempo de solicitud

## Todo pasa por getStaticProps
- Se ejecuta del lado del servidor y sólo se llama en el build time.
- Pide el context y lo que sea que le pase en las props de la funcion es lo que se irá a las props del componente

~~~~js
export const getStaticProps: GetStaticProps = async (ctx)=>{

  console.log('Hola mundo')

  return{
    props:{
        name: 'Migue'
    }
  }
}
~~~~
- Este hola mundo no se imprime en la consola del navegador, si no en la terminal porque se ejecuta del lado del servidor y solo en build time
- Ahora en desarrollo se ejecuta varias veces pero en producción se ejecuta una única vez
- El getStaticProps solo se puede usar dentro de las páginas, no en componentes, solo en .pages
- Va del lado del servidor, pero lo que yo ponga en las props de la función va a caer en las props del componente, en este caso Migue
- Esta función esta fuera del componente, al final
-------

## Cargar de forma estática los Pokemons


- Puedo cambiar el endpoint para que me devuelva solo 151 pokemon

        https://pokeapi.co/api/v2/pokemon?limit=151

- Cuando la persona entra en la página ya va a traer la información y no va a hacer el fetch por ese lado
- Creo una carpeta llamada api en el root, con un index dentro. Creo pokeAPi donde configuro el endpoint con axios y lo exporto desde el index
~~~js
import axios from 'axios';

const pokeApi= axios.create({
    baseURL: 'https://pokeapi.co/api/v2'
})

export default pokeApi;
~~~

- En el index
~~~jsx
export {default as pokeApi} from './pokeApi'
~~~
- Ahora ya puedo hacer la petición con el restante del endpoint

~~~~js
export const getStaticProps: GetStaticProps = async (ctx)=>{

  const resp = await pokeApi.get('/pokemon?limit=150')
  console.log({resp})

  return{
    props:{
      name: 

    }
  }
}
~~~~

- Pero no se imprime en la consola del navegador sino en la terminal!! Corre del lado del servidor ; )

- Puedo desestructurar la data y mandarsela por las props al componente
~~~js
import type { NextPage, GetStaticProps } from 'next'
import {Button} from '@nextui-org/react'
import {Layout} from '../components/layouts'
import { pokeApi } from '../api'

const HomePage: NextPage = (props) => {
  console.log(props)
  return (
    <>
    <Layout title= "Listado Pokemon">

     <ul>
      
     </ul>

    </Layout>
    </>
  )
}


export const getStaticProps: GetStaticProps = async (ctx)=>{

  const {data} = await pokeApi.get('/pokemon?limit=150')
  console.log({data})

  return{
    props:{
      pokemons: data.results

    }
  }
}


export default HomePage
~~~
- Ahora, si abro el objeto en la consola del navegador tengo count, next, previous y results (151) con los pokemons
-----
# Colocar el tipo de respuesta de Axios

- Hago un copy past en https://app.quicktype.io/ de la respuesta de los pokemons para sacar las interfaces de typescript.
- Creo una carpeta llamada interfaces con un index de barril para exportar por defecto y un archivo llamada pokemon-list con las interfaces
- El next y el previous son opcionales
~~~js
export interface PokemonListResponse {
    count:    number;
    next?:     string;
    previous?: string;
    results:  SmallPokemon[];
}

export interface SmallPokemon {
    name: string;
    url:  string;
    id: number,   //puedo añadir campos, las interfaces no obligan a que estén
    img: string
}
~~~

- El index:
~~~js
export * from './pokemon-list'
~~~
- Ahora puedo importarlas y usarlas con typescript en la petición get
~~~js
export const getStaticProps: GetStaticProps = async (ctx)=>{

  const {data} = await pokeApi.get<PokemonListResponse>('/pokemon?limit=150')
  console.log({data})

  return{
    props:{
      pokemons: data.results

    }
  }
}
~~~
- Creo la interface de las props, e importo SmallPokemon
~~~js

interface Props{
  pokemons: SmallPokemon[];
}

const HomePage: NextPage<Props>= (props) => {
  return(...)
~~~

- Declaro el tipo de pokemons, hago un .map con el índice.
- Me traigo todo el elemento poke, e incorporo al arreglo pokemons un id +1 y la imagen en la que reemplazo el numero del pokemon por el id con backticks.

- Ahora, en las props tengo el arreglo pokemons con un id y una imagen
~~~js
export const getStaticProps: GetStaticProps = async (ctx)=>{

  const {data} = await pokeApi.get<PokemonListResponse>('/pokemon?limit=150')

  const pokemons: SmallPokemon[]=data.results.map((poke, i)=>({
    ...poke,
    id: i+1,
    img: `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/dream-world/${i + 1}.svg`
  }) )


  return{
    props:{
        pokemons
    }
  }
}
~~~

- Hago un .map en un ul para hacer un renderizado provisional

~~~js
import type { NextPage, GetStaticProps } from 'next'
import {Layout} from '../components/layouts'
import { pokeApi } from '../api'
import { PokemonListResponse, SmallPokemon } from '../interfaces'

interface Props{
  pokemons: SmallPokemon[];
}

const HomePage: NextPage<Props>= ({pokemons}) => {
  console.log(pokemons)
  return (
    <>
    <Layout title= "Listado Pokemon">
      <ul>
        {pokemons.map(pokemon=>(
          <li key={pokemon.id}>
            <span>{pokemon.name}</span>
            <img src={pokemon.img} alt={pokemon.name} />
          </li>
        ))}
      </ul>
    </Layout>
    </>
  )
}


export const getStaticProps: GetStaticProps = async (ctx)=>{

  const {data} = await pokeApi.get<PokemonListResponse>('/pokemon?limit=150')

  const pokemons: SmallPokemon[]=data.results.map((poke, i)=>({
    ...poke,
    id: i+1,
    img: `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/dream-world/${i + 1}.svg`
  }) )


  return{
    props:{
        pokemons
    }
  }
}


export default HomePage
~~~

- Importo Grid de nextui-org/react
- Dentro del map le indico xs (super pequeño) que ocupe la mitad de la pantalla (el grid son 12 y le pongo 6)
- sm (de small) de 3, md de mediana de 2 y xl de 1, es decir visualizar 12 tarjetas 
- Importo el componente Card  y le añado las propiedades hoverable y clickeable, etc
- Lo muevo todo a un nuevo componente Card en components/pokemon
- Creo el cascarón, será un FC (functional Component)
- Le hago la interface de las Props de tipo SmallPokemon (no un arreglo, ojo!) y lo incorporo
~~~js
import React from 'react'
import { FC } from 'react'
import { SmallPokemon } from '../../interfaces'

interface Props{
    pokemon: SmallPokemon
}

const PokemonCard: FC<Props> = () => {
  return (
    <>
    </>
  )
}

export default PokemonCard

~~~

- Añado el código

~~~js
const PokemonCard: FC<Props> = ({pokemon}) => {
  return (
    <>
    <Grid key={pokemon.id}>
            <Card hoverable clickable>
              <Card.Body css={{p:1}}>
                <Card.Image src={pokemon.img}
                width="100%"
                height={140}/>

              </Card.Body>
              <Card.Footer>
                <Row justify="space-between">
                <Text>{pokemon.name}</Text>
                </Row>
              </Card.Footer>
            </Card>

          </Grid>
    </>
  )
}

export default PokemonCard
~~~
Al renderizarlo dentro del map debo añadirle por props el id y el pokemon

-----
# Recibir argumentos por URL y Navegación
- Creo la carpeta de pokemon en .pages para hacer las rutas pokemon/1.tsx
- Creo este componente en pokemon/1.tsx

~~~js
import {Layout}  from "../../components/layouts"
import { useRouter } from "next/router"

const PokemonPage = () => {
  
  const router= useRouter()
  
  console.log(router.query)

  return (
    <Layout title="algun pokemon">
        <h1>Hola Mundo</h1>
    </Layout>
  )
}

export default PokemonPage
~~~
- Yo lo que quiero es que ese 1 sea el index de uno de los 151 pokemons
- Puedo cambiar el 1 por corchetes id en el nombre del archivo [id].tsx

    [id].tsx

- Se puede obtener el id del lado del servidor o del cliente.
- En PokemonPage ,i hago un console.log del router, router = useRouter() incluye el query
- Puedo hacer un console.log de router.query y veo que si escribo en la barra de navegación pokemon/100 el query es 100
- Yo lo que quiero es clicar en una card de pokemon y que me lleve a la página de ese pokemon
- Para ello creo una función en PokemonCard que se llama onPokemonClick, guardo fuera de la función el useRouter en una constante llamada router
- Hago un router.push(`/pokemon/${pokemon.id}`)
~~~js
const PokemonCard: FC<Props> = ({pokemon}) => {

const router= useRouter()

const onPokemonClick=()=>{
    router.push(`/pokemon/${pokemon.id}`)
} 

  return (...)
 
~~~

- La historia es que cuando llame a ese pokemon/id tiene que venir ya renderizado. 
- Yo sé que me van a pedir un pokemon del 1 al 151
- Hasta dónde sea posible, yo debo mantener el Static-Side generation

## getStaticPaths

- Creo la interface de las Props, digo que es una NextPage (con su importación), desestructuro el id y el name
~~~js
import { NextPage } from "next";
import { useRouter } from "next/router"
import {Layout}  from "../../components/layouts"

interface Props {
    id: string;
    name: string;

}

const PokemonPage: NextPage<Props> = ({id, name}) => {

    const router = useRouter()
    console.log(router.query)



  return (
    <Layout title="algun pokemon">
        <h1>{id}-{name}</h1>
    </Layout>
  )
}

export default PokemonPage
~~~

- El problema es que esas props que he desestructurado no tengo forma de pasárselas.
- Tengo que volver a llamar a la función getStaticProps
- Pongo un pokemon en duro provisional para ver como lo renderiza al llamarlo desde getStaticPaths
~~~js
import { NextPage, GetStaticProps } from "next";
import { useRouter } from "next/router"
import {Layout}  from "../../components/layouts"

interface Props {
    id: string;
    name: string;

}

const PokemonPage: NextPage<Props> = ({id, name}) => {

    const router = useRouter()
    console.log(router.query)



  return (
    <Layout title="algun pokemon">
        <h1>{id}-{name}</h1>
    </Layout>
  )
}

export const getStaticProps: GetStaticProps = async (ctx)=>{



  return{
    props:{
      id:1,
      name: 'Bulbasur'
    }
  }
}

export default PokemonPage
~~~
- Me salta un error que dice que getStaicPaths es reuqerido patra hacer este tipo de renderizado dinámico.
- Esto es porque la página tiene un argumento dinámico [id].tsx
- Normalmente se escriben encima del getStaticProps
- Aquí es donde voy a escribir las rutas que voy a permitir.
- Cada uno de estos params debe de tener la data que van a ser los parametors que voy a mandarle al getStaticProps
~~~js
export const getStaticPaths: GetStaticPaths = async (ctx:any) =>{
  
  return { 
    paths:[
      {
        params:{  //estos paths son los páginas que voy a pregenerar en build time 
          id: '1' //tiene que ser un string
        }
      },
      {
        params:{
          id: '2'   //debería hacer los 151 pokemons si quiero hacerlo así
        }
      }
    ],
    fallback: false //false en lugar de "blocking" porque quiero que si se pasa un id que noe sta en la lista mande un error 404
  }
}
~~~
- Ahora me renderiza todo el rato Bulbasur porque es lo que tengo en duro
- De esta manera sería inviable.
- Creo un arreglo con 151 elementos.
- De la desestructuración hago un .map, lo que me interesa es el index
- En producción esta instrucción sólo se ejecutará una vez
~~~~js
export const getStaticPaths: GetStaticPaths = async (ctx:any) =>{
  
  const pokemon151 = [...Array(151)].map((value, index) => `${index+1}`)


  return { 
    paths: pokemon151.map( id =>({
      params: {id}
    })),
    fallback: false //false en lugar de "blocking" porque quiero que si se pasa un id que noe sta en la lista mande un error 404
  }
}
~~~~

- Después de que se ejecutan los paths pasa a getStaticProps.
- Puedo desestructurar del context del getStaticProps los params que es dónde tengo el id
- Necesito enviar a las props un pokemon (todavía no lo tengo)
~~~~js
export const getStaticProps: GetStaticProps = async ({params})=>{

  const {id} = params as {id: string}; //tipado



  return{
    props:{
      pokemon: 'Fernando'
    }
  }
}

export default PokemonPage
~~~~
- Puedo generar en build time toda la info que necesito del pokemon.
- Para eso (importo pokeApi) copy+past del get de getStaticProps pero ahora no va a ser de PokemonListResponse  ni va a ser esa url
~~~js
const {data} = await pokeApi.get<PokemonListResponse>('/pokemon?limit=150')
~~~
- Voy a Postman, hago un get de pokemon/1, copio en el clipboard el resultado, lo llevo a vscode
- Uso paste JSON as code para sacar las interfaces en typescript que guardo en un archivo llana¡mado pokemon-full en /interfaces
- esto me saca todas las interfaces
- Uso el archivo de barril index para exportarlas todas
- Ahora vuelvo a [id].tsx
- Este pokeApi va a ser de tipo Pokemon, no PokemonListResponse
- Import Pokemon de '../interfaces¡
- Ahora la petición get será de tipo Pokemon y Pokemon será de tipo  Pokemon
- Le paso la data por las props y cambio la url por bacticks y el id
~~~js
import { NextPage, GetStaticProps, GetStaticPaths } from "next";
import { useRouter } from "next/router"
import {Layout}  from "../../components/layouts"
import  pokeApi  from '../../api/pokeApi'
import {Pokemon} from '../../interfaces'


interface Props {
    pokemon: Pokemon

}

const PokemonPage: NextPage<Props> = ({pokemon}) => {

console.log(pokemon)



  return (
    <Layout title="algun pokemon">
        <h1>Temporal</h1>
    </Layout>
  )
}
export const getStaticPaths: GetStaticPaths = async (ctx:any) =>{
  
  const pokemon151 = [...Array(151)].map((value, index) => `${index+1}`)


  return { 
    paths: pokemon151.map( id =>({
      params: {id}
    })),
    fallback: false
  }
}

export const getStaticProps: GetStaticProps = async ({params})=>{

  const {id} = params as {id: string}; //tipado
  const {data} = await pokeApi.get<Pokemon>(`/pokemon/${id}`) //id


  return{
    props:{
      pokemon: data //regreso la data
    }
  }
}

export default PokemonPage
~~~
interface
~~~~js
export interface Pokemon{

    abilities:                Ability[];
    base_experience:          number;
    forms:                    Species[];
    game_indices:             GameIndex[];
    height:                   number;
    held_items:               any[];
    id:                       number;
    is_default:               boolean;
    location_area_encounters: string;
    moves:                    Move[];
    name:                     string;
    order:                    number;
    past_types:               any[];
    species:                  Species;
    sprites:                  Sprites;
    stats:                    Stat[];
    types:                    Type[];
    weight:                   number;
}     

~~~~

- Ahora si escribo data. aparecen todas estas
- Con un console.log de pokemon aparecen en consola toda la info cuando clico sobre una card
- Ya tengo la data en tiempo de compilación
- Ahora ya puedo hacer la página de cada pokemon
-----
# Link al home de Pokemon
- Es necesario hacerlo asi porque el NextLink solo acepta un children. Para que este Link reciba el "/" le añado el passHref
~~~js
import NextLink from 'next/link'
import {Link, Spacer, Text, useTheme} from '@nextui-org/react'

<NextLink href="/" passHref>
  <Link>
    <Text color="white">P</Text>
    <Text color="white">okémon</Text>

  </Link>

</NextLink>
~~~
---------

# Resumen

- Static Props son propiedades generadas en tiempo de construcción de la aplicación.
- En producción se ejecuta una única vez
- Así la página va a tener toda la data generada del lado del servidor en formato JSON
- Se pueden construir rutas con argumentos, como el [id].tsx
- Cuando se de antemano todos los ids permitidos, puedo usar el getStaticPaths. También se ejecuta unicamente una vez
- El fallback en false para mostrar un 404 cuando sea una página fuera del rango
- Se recomienda elegir generar la imagen de antemano hasta dónde se pueda

