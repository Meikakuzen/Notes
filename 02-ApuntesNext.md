# Next --typescript

- Para crear una app 

            npx create-next-app@latest --typescript
--------
## Next UI

        npm install @nextui-org/react

- Importar NextUIProvider en _app.tsx y envolver el componente con el Provider

~~~js
import '../styles/globals.css'
import type { AppProps } from 'next/app'
import {NextUIProvider} from '@nextui-org/react'

function MyApp({ Component, pageProps }: AppProps) {
  return (
  <NextUIProvider>
    <Component {...pageProps} />

  </NextUIProvider>
  )
}

export default MyApp
~~~

- Funciona como todas, importando el componente y costumizando a travésd e las props
~~~js

import type { NextPage } from 'next'
import {Button} from '@nextui-org/react'


const HomePage: NextPage = () => {
  return (
    <>
      <h1>Hola mundo</h1>
      <Button color="gradient">Presióname</Button>
    </>
  )
}

export default HomePage
~~~

----

## _document.ts

- CssBaseline viene a ser como el Normalize. Debe hacerse en _document.ts
- Next pide que se coloque en ./pages/_document.js con la estructura que mandan
- Hay que añadirle la importación de DocumentContext y colocarlo en el ctx
- Puedo colocar el lenguaje en español y otros metatags en el Head

~~~~js
import Document, { Html, Head, Main, NextScript, DocumentContext } from 'next/document'

class MyDocument extends Document {
  static async getInitialProps(ctx: DocumentContext) { //aquí
    const originalRenderPage = ctx.renderPage

    // Run the React rendering logic synchronously
    ctx.renderPage = () =>
      originalRenderPage({
        // Useful for wrapping the whole react tree
        enhanceApp: (App) => App,
        // Useful for wrapping in a per-page basis
        enhanceComponent: (Component) => Component,
      })

    // Run the parent `getInitialProps`, it now includes the custom `renderPage`
    const initialProps = await Document.getInitialProps(ctx)

    return initialProps
  }

  render() {
    return (
      <Html lang="es">
        <Head>
            <title>Hola mundo</title>
        </Head>
        <body>
          <Main />
          <NextScript />
        </body>
      </Html>
    )
  }
}

export default MyDocument
~~~~

Hay que añadir el styles entre llaves en el return del initialProps, importar el CssBaseline y añadirlo en el Head

~~~~js
import Document, { Html, Head, Main, NextScript, DocumentContext } from 'next/document'
import {CssBaseline} from '@nextui-org/react'

class MyDocument extends Document {
  static async getInitialProps(ctx: DocumentContext) {
    const originalRenderPage = ctx.renderPage

    // Run the React rendering logic synchronously
    ctx.renderPage = () =>
      originalRenderPage({
        // Useful for wrapping the whole react tree
        enhanceApp: (App) => App,
        // Useful for wrapping in a per-page basis
        enhanceComponent: (Component) => Component,
      })

    // Run the parent `getInitialProps`, it now includes the custom `renderPage`
    const initialProps = await Document.getInitialProps(ctx)

    return {
      ...initialProps,
      styles: <>{initialProps.styles}</>
    }
  }

  render() {
    return (
      <Html lang="es">
        <Head>
              {CssBaseline.flush()}
          </Head> 
        <body>
          <Main />
          <NextScript />
        </body>
      </Html>
    )
  }
}

export default MyDocument
~~~~
-----
# Implementando Dark Theme

- Creo la carpeta themes en la raíz y allí creo dos archivos:
- DarkTheme.ts

~~~js
import {createTheme} from '@nextui-org/react'

export const darkTheme = createTheme({
        type: 'dark',
        theme: {
            colors: {},
        }
})

~~~

Y el index:
~~~js
export * from './DarkTheme'
~~~

- Lo paso como props al Provider en el _app.tsx

~~~js
 <NextUIProvider theme= {darkTheme}>
    <Component {...pageProps} />

  </NextUIProvider>
~~~

- De esta manera , si tengo varios temas puedo importarlos del index e intercambiarlos de forma dinámica
------

## Layout Principal

Creo varias carpetas dentro de components: layouts, ui, pokemon. 
- Creo el archivo Layout.tsx dentro de layouts
- Hay que definir las props para que typescript no se queje
~~~js
import Head from 'next/head'
import { FC } from 'react'

type Props = {
  children: React.ReactNode;
}

const Layout: FC <Props> = ({children}) => {
  return (
    <>
        <Head>
            <title>Pokemon App</title>
            <meta  name="author" content="Miguel"/>
            <meta name="description" content="Información sobre el pokemon XXXX" /> 
            <meta name="keywords" content="XXXX, pokemon, pokedex" /> 

        </Head>

        {/*Futuro Navbar*/ }
        
        <main>
            { children }
        </main>
    </>
  )
}

export default Layout
~~~

- Vuelvo a usar un archivo index.ts de barril para exportar por defecto Layout

       export * from './Layout'

- Lo uso en .pages/index.ts
~~~js

const HomePage: NextPage = () => {
  return (
    <>
    <Layout title= "Pokemon">

      <h1>Hola mundo</h1>
      <Button color="gradient">Presióname</Button>

    </Layout>
    </>
  )
}


~~~



- Le pongo un titulo opcional
~~~js

type Props = {
  children: React.ReactNode,
  title?: string
}

export const Layout: FC <Props> = ({children, title}) => {
  return (
    <>
        <Head>
            <title>{title || "Pokemon App"}</title>
            <meta  name="author" content="Miguel"/>
            <meta name="description" content="Información sobre el pokemon XXXX" /> 
            <meta name="keywords" content="XXXX, pokemon, pokedex" /> 

        </Head>

        {/*Navbar*/ }
        <main>
            { children }
        </main>
    </>
  )
}

~~~~
- Importo el useTheme para agregar un color al background del darkTheme
- Importo también el tag Text, le doy estilos
- Importo un spacer para dar espacio, y le doy flex1 para que ocupe todo el ancho y me mande al top right el Favoritos

~~~js
import {useTheme, Text, Spacer} from '@nextui-org/react'


const Navbar = () => {

    const {theme} = useTheme()


  return (
    <div style={{
      display: 'flex',
      width: '100%',
      flexDirection: 'row',
      alignItems: 'center',
      justifyContent: 'start',
      padding: '0px 20px',
      backgroundColor: theme?.colors.gray100.value

    }}>
      <Text color='yellow' h2>P</Text>
      <Text color='red' h3>ókemon</Text>

     <Spacer css={{flex:1}}></Spacer>
      <Text color='white'>Favoritos</Text>
    </div>
  )
}

export default Navbar
~~~
- Para los componentes se recomienda usar la propiedad css en lugar de style
- El componente image en Next tiene varias ventajas
~~~js
 <Image src="https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/132.png"
      alt="icono de la app"
      width={70} height={70}/>

~~~

- Esto da error, porque hay que decirle a Next que permita las imagenes de esta url.
- Copio la url hasta .com y voy al archivo de next.config.js
- Agrego images/domains y la ur dentro de un arreglo
~~~js
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  images:{
    domains: ['raw.githubusercontent.com']
  }
}

module.exports = nextConfig
~~~

- Ahora se ve un mini pokemon
