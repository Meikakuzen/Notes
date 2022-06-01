# Next basis

- Todos los componentes que son una página deben de tener el export por default
- Los archivos van en minúscula y no tiene porqué tener el mismo nombre.
- El nombre del archivo es el path
- Next tiene sus propios componentes como Head, Link, etc. Se necesita importación 
        
        import Head from 'next/head'
- El Head es la info que debería estar en el head del html.
- Se pueden tener varios heads en diferentes componentes
------
## Static Generation y Server Side Rendering

- Next utiliza el Static Generation hasta donde puede:

- Server Side Rendering renderiza cada vez que el/la usuario/a hace un cambio
- En el Static Generation el renderizado se genera en el build de la página 
-----
# Link

- Con el componente de next Link no hay full refresh
- Puede mandarse un objeto
~~~js
<Link
    href={{
        pathname:'/about', 
        query: { name: 'test'}
    }} />
~~~

- El replace por defecto esta en false. Se usa para que no se pueda regresar en la navegación a la página anterior
-------
## Componentes Personalizados

- En pages puedo colocar páginas, middlewares, endpoints, pero no una carpeta de components, esta debe de estar en la raíz
- Si tengo elementos como el navbar, un heading que se repite en todas las páginas, ahi entran los Layouts.
------
## Manejo de estilos

- El componente Link no se puede estilizar con CSS. Al final se renderiza como un a, puedo apuntar al a
- En next se estila usar los .module.css. Puedo manejarlo con un clase en el nav y apuntar a los a

~~~~js
import Link from 'next/link'
import styles from '../components/Navbar.module.css'

const Navbar = () => {
  return (
    <nav className={styles.menu}>
        <Link href="/">Home</Link>
        <Link href="/about">About</Link>
    </nav>
  )
}

export default Navbar
~~~~
- Para poner guiones a las clases se deben usar los corchetes
~~~js
import Link from 'next/link'
import styles from '../components/Navbar.module.css'

const Navbar = () => {
  return (
    <nav className={styles['menu-container']}>
        <Link href="/">Home</Link>
        <Link href="/about">About</Link>
    </nav>
  )
}

export default Navbar
~~~
------
# Custom Active Link

- Para estilizar los Links y mostrarlo coloreado de forma condicional.
- Desestructuro el asPath del useRouter para usarlo de manera condicional y colorear el link en el cual me encuentro

~~~js
import Link from 'next/link'
import {useRouter} from 'next/router    
    const style ={
        color: '#0070F3',
        textDecoration: 'underline'
    }

const ActiveLink = ({text, href}) => {
    
    const {asPath} = useRouter()  
    return (

    <Link href={href}>
        
        <a style={asPath === href? style: null} text={text}>{text}</a>
    
    </Link>
  )
}

export default ActiveLink
~~~
- Si quiero guardar las páginas en carpetas dentro de pages, debo nombrar a la página en cuestión index.jsx
--------
# Layouts
- Hago un componente que voy a reutilizar con el children

~~~js
import Head from 'next/head'
import Navbar from '../Navbar'
import styles from './MainLayout.module.css'

const MainLayout = ({children}) => {
  return (
    <>
    <Head>
      <title>Home</title>
      <meta name="description" content="Home Page" />
      <meta name="keywords" content="fernando, herrera, curso, next.js" />
    </Head>

    <Navbar/>
    <main className={styles.main}>
        {children}
    </main>
    </>
  )
}

export default MainLayout
~~~
------

## Múltiples Layouts (Layouts anidados)
- Creo otro Layout y lo coloco dentro del MainLayout, coloco el contenido en el segundo layout. Es una manera.
- Otra es añadiendo al prototipo

~~~js
AboutPage.getLayout = function getLayout(page){
  return (
    <MainLayout>
      <DarkLayout>
        {page}
      </DarkLayout>
    </MainLayout>
  )
}
~~~
- Dónde ejecuto esta función? 
## en el _app.js!
- Reviso el componente que se esta queriendo generar en ese momento, le paso la función.
- Si existe obtiene ese layout y si no regresa la misma página ( no haga nada )
~~~~js
import '../styles/globals.css'

function MyApp({ Component, pageProps }) {
  const getLayout = Component.getLayout || ((page) => page);
  
  return getLayout(<Component {...pageProps} />)
}

export default MyApp
~~~
------




