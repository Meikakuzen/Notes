- Los radio inputs y los checkbox no funcionan con value, funcionan con checked 
- Por ello debo hacer una validación si es de tipo checkbox === target.checked, si no target.value
- Puedo hacer una desestructuración de e
- También debo comparar el valor de los radio button con el campo de name

~~~~js
import { useState } from "react"


function App() {
  
const [value, setValue] =useState({
  nombre:'',
  email:'',
  select:'',
  check: false,
  estado:'wild'
})

const handleInputChange=({target})=>{
    setValue({
      ...value,
      [target.name]: target.type=== 'checkbox'
      ? target.checked 
      : target.value
    })
}

const handleSubmit=()=>{
  console.log('Enviando Formulario')
}

  return (
    <form>
      <label> Nombre</label>
      <input type="text" name="nombre" value={value.nombre} onChange={handleInputChange}/>
      <label>Email</label>
      <input type="email" name="email" value={value.email} onChange={handleInputChange}/>
      
      <select value={value.select} name="select" onChange={handleInputChange}>
        <option value="">--Seleccione</option>
        <option value="opcion1">Opcion1</option>
        <option value="opcion2">Opcion2</option>
        <option value="opcion3">Opcion3</option>
      </select>
      <input type="checkbox" name="check" onChange={handleInputChange} checked={value.check}/>
      <div>
        <label> Behaviour options</label>
        <input onChange={handleInputChange} type="radio"  name="estado" checked= {value.estado === "wild" }value="wild"/>WILD
        <input onChange={handleInputChange} type="radio"  name="estado" checked= {value.estado === "sleepy"}value="sleepy"/>SLEEPY
        <input onChange={handleInputChange} type="radio" name="estado"  checked= {value.estado === "next" }value="next"/>NEXT
      </div>
    </form>
  )
}

export default App
~~~~