# Sass

## Sintaxis bàsica

- Variables: 
    - s' utilitza el símbol de dòlar al principi per declarar-les

      
          $nom_variable: valor;
          $primary-color: crimson;

- Per cridar-les només cal utilitzar el simbol de dòlar

        p{
            color: $primary-color
        }

- Per cridar als elements dins d'altres elements simplement anidant amb sass ja està

        article{
            background-color: $primary-color
            
            p{
               color: blue 
            }
        }

- Puc anidar propietats

        font:{
            size: 16px;
            color: red;
            family: italic;
            weight: 700
        }
        text:{
            align: center;
            transform: uppercase;
            shadow: 0 0 5px rgba(#000, 0.5)
        }

- Pots indicar el mateix selector (pare) utilitzant &
- També permet l'anidació

        article{
            background-color: green;

            &:hover{
                background-color: red
                
                .titulo{
                    color: white;
                }
            }
        }

--------

## Operacions

- Amb sass es pot treballar amb resta, suma, multiplicació i divisió.
- S'han de respectar les unitats i fer les operacions amb les mateixes unitats


        .container{
            width: 200px / 600px * 100%; // per fer percentatges
            margin: 0 auto

            section{
                float: left;
                width: 200px * 2;
                height: 100vh -20vh
            }
        }

-------------
## Mixins

- Son funcions dins de sass
- Molt útils per no repetir codi

        @mixin estils_text(){
            font-size: 40px:
            color: gray;
            tezt-align: center
        }

- Per cridar-les s'utilitza l' @includes

        h1{
            @includes estils_text()

        }
- Poden tenir paràmetres

        @mixin estils_text( $color, $weight, $family ){
            color: $color;
            weight: $weight;
            font-family: $family
        }

        h1{
            @includes estils_text( black, 600, bold )

        }


------
## Llistes

- Son arrais, pero la posició num1 es el número 1 

        $colors: blue, red, orange, pink
        $colors2: black, white

- Per accedir utilitzo nth

        h1{
            color: nth( $colores, 3 )
        }

- Per combinar llistes utilitzo join, separant per comes

        $colors_total = join( $colors, $colors2, coma )

        h1{
            color: nth( $colors_total, 6 )
        }

- Per afegir un element s'utilitza append


        $afegirElement: append( $colors, green, coma ) 

------
## Mapes

- Son objectes
- S'utilitza map-get per cridar els valors

        $titols:{
            h1: 60px,
            h2: 50px,
            h3: 40px
            h4: 29px
            }
        $colors:{
            blau: blue,
            vermell: red
        }

        h1{
            font-size: map-get($titols, h1)
        }

- Barrejar mapes amb map-merge

        titols_colors: map-merge( $titols, $colors )

- map-remove per eliminar un element
- Elimina l'h1, l'h2 i l'h3 del mapa $titols

        $nou_titol: map-remove($titols, h1, h2, h3)

------
## Móduls

- Permet organitzar millor el CSS
- Ajunta en una única pàgina totes les pàgines d'estils
- Es posa un guió baix al principi del nom de l'arxiu, així sass ho detecta i no fa una cópia de css
- S'ajunten tots a un scss utilitzant @import sense guió baix
- Si _generals.scss es l'arxiu d'estils generals l'importo a l'arxiu barril moduls.scss
        
        @import "generals"
----------------------

## Extend

- Molt útil per no repetir codi, on hi ha codi similar a elements amb només un o dos valor diferents

        .contenidor{
            height: 300px;
            width: 200px;
            background-color: red
        }
        
        .contenidor-blue{
            @extend .contenidor
            background-color: brown
        }

-------
## Condicionals

- També hi ha @elseif

        $color: azul
        h1{
            @if ($color == azul){
                color: blue
            }
            @else{
            color: red
            }
        }
-------
## Bucles

        @for $i from 1 through 6 {
            
            h#($i){
                    font-size: $i*20em             
            }
        }



- A més del for també hi ha el each. No li veig utilitat en CSS

-------
## Media querys

- Lo habitual es per desktop, tablet i smartphones
- El content es com el children

        $desktop: (max-width: 1024px)
        $tablet: (max-width: 720px)
        $phone: (max-width: 480px)

        @mixin desktop{
            @media screen and #($desktop) {
                @content;
            }
        }


           p{
               @include desktop(){
                    font-size: 36px;
                    color: yellow
               }
           }