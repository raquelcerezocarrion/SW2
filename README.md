# SW2
---

````markdown
---

## 🟢 NIVEL 1 – CONSULTAS BÁSICAS

```js
// Ver un documento
db.listingsAndReviews.findOne()

// Ver todos los documentos
db.listingsAndReviews.find().pretty()

// Contar documentos
db.listingsAndReviews.find().count()
````

---

## 🟡 NIVEL 2 – BÚSQUEDAS SIMPLES

```js
// Igualdad
db.listingsAndReviews.find({ beds: 2 })

// Distinto
db.listingsAndReviews.find({ property_type: { $ne: "Apartment" } })

// Comparadores
db.listingsAndReviews.find({ price: { $gt: 100 } })   // Mayor que
db.listingsAndReviews.find({ beds: { $lt: 4 } })      // Menor que
db.listingsAndReviews.find({ price: { $gte: 80 } })   // Mayor o igual
db.listingsAndReviews.find({ price: { $lte: 200 } })  // Menor o igual
```

---

## 🟠 NIVEL 3 – ARRAYS Y DOT NOTATION

```js
// Valor dentro de un array
db.listingsAndReviews.find({ amenities: "Wifi" })

// Coincidencia exacta del array
db.listingsAndReviews.find({ amenities: ["Wifi", "TV"] })

// Array que contiene varios valores
db.listingsAndReviews.find({ amenities: { $all: ["Wifi", "Kitchen"] } })

// Tamaño del array
db.listingsAndReviews.find({ amenities: { $size: 10 } })

// Subdocumentos (dot notation)
db.listingsAndReviews.find({ "address.country": "Spain" })
db.listingsAndReviews.find({ "review_scores.review_scores_rating": { $gte: 95 } })
```

---

## 🔵 NIVEL 4 – OPERADORES LÓGICOS

```js
// AND implícito
db.listingsAndReviews.find({
  beds: { $gte: 2 },
  price: { $lte: 150 }
})

// AND explícito
db.listingsAndReviews.find({
  $and: [
    { beds: { $lte: 5 } },
    { property_type: "Apartment" },
    { maximum_nights: { $gte: 10 } }
  ]
})

// OR
db.listingsAndReviews.find({
  $or: [
    { beds: 1 },
    { property_type: "House" }
  ]
})

// NOT
db.listingsAndReviews.find({
  beds: { $not: { $lte: 2 } }
})

// NOR
db.listingsAndReviews.find({
  $nor: [
    { beds: 1 },
    { property_type: "House" }
  ]
})
```

---

## 🟣 NIVEL 5 – `$expr` (comparar campos)

```js
// Comparar campos directamente
db.trips.find({
  $expr: {
    $eq: [ "$start station id", "$end station id" ]
  }
})

// Comparar con número
db.trips.find({
  $expr: {
    $gt: [ "$tripduration", 1200 ]
  }
})
```

---

## 🔴 NIVEL 6 – AGREGACIONES BÁSICAS

```js
// $match – filtrar
db.listingsAndReviews.aggregate([
  { $match: { amenities: "Wifi" } }
])

// $project – mostrar campos
db.listingsAndReviews.aggregate([
  { $match: { amenities: "Wifi" } },
  { $project: { price: 1, address: 1 } }
])

// $group – agrupar y contar
db.listingsAndReviews.aggregate([
  { $project: { "address.country": 1 } },
  { $group: { _id: "$address.country", count: { $sum: 1 } } }
])
```

---

## 💥 NIVEL EXAMEN – CONSULTAS COMPLEJAS

```js
// AND + arrays + dot notation
db.listingsAndReviews.find({
  $and: [
    { "address.country": "Spain" },
    { "review_scores.review_scores_rating": { $gte: 95 } },
    { amenities: { $all: ["Wifi", "Kitchen"] } }
  ]
})

// $expr con AND
db.trips.find({
  $expr: {
    $and: [
      { $eq: [ "$start station id", "$end station id" ] },
      { $gt: [ "$tripduration", 1200 ] }
    ]
  }
})

// Agregación completa
db.listingsAndReviews.aggregate([
  { $match: { amenities: "Wifi" } },
  { $project: { "address.country": 1 } },
  { $group: { _id: "$address.country", total: { $sum: 1 } } }
])
```

---Perfecto. Aquí tienes una **colección de ejemplos complejos y típicos de examen**, que combinan varios operadores como `$and`, `$or`, `$not`, `$expr`, arrays, dot notation y agregaciones. Todos los ejemplos **se basan únicamente en lo que aparece en tu PDF**, y están pensados para que puedas resolver cualquier consulta difícil que te planteen.

---

# 💥 CONSULTAS DIFÍCILES COMBINADAS (Nivel Examen)

---

### ✅ 1. Buscar alojamientos en España con WiFi y al menos 3 camas

```js
db.listingsAndReviews.find({
  $and: [
    { "address.country": "Spain" },
    { amenities: "Wifi" },
    { beds: { $gte: 3 } }
  ]
})
```

---

### ✅ 2. Buscar alojamientos que **no** estén en "Spain" ni tengan solo una cama

```js
db.listingsAndReviews.find({
  $nor: [
    { "address.country": "Spain" },
    { beds: 1 }
  ]
})
```

---

### ✅ 3. Buscar alojamientos con **más de 2 camas** y que **no sean del tipo "Hostel"**

```js
db.listingsAndReviews.find({
  $and: [
    { beds: { $gt: 2 } },
    { property_type: { $ne: "Hostel" } }
  ]
})
```

---

### ✅ 4. Buscar alojamientos que tengan **Wifi y Kitchen** en `amenities` y una puntuación de al menos 95

```js
db.listingsAndReviews.find({
  $and: [
    { amenities: { $all: ["Wifi", "Kitchen"] } },
    { "review_scores.review_scores_rating": { $gte: 95 } }
  ]
})
```

---

### ✅ 5. Comparar dos campos con `$expr`: alojamientos con número máximo de noches mayor que el mínimo

```js
db.listingsAndReviews.find({
  $expr: {
    $gt: [ "$maximum_nights", "$minimum_nights" ]
  }
})
```

---

### ✅ 6. Buscar viajes donde el ID de la estación de inicio y final son iguales **y** el viaje dura más de 20 minutos

```js
db.trips.find({
  $expr: {
    $and: [
      { $eq: [ "$start station id", "$end station id" ] },
      { $gt: [ "$tripduration", 1200 ] }
    ]
  }
})
```

---

### ✅ 7. Buscar alojamientos que tengan más de 8 amenities **y** no incluyan "TV"

```js
db.listingsAndReviews.find({
  $and: [
    { amenities: { $not: { $in: ["TV"] } } },
    { amenities: { $size: { $gt: 8 } } } // Esta combinación no es válida directamente
  ]
})
```

> ⚠️ *Nota:* No se puede usar `$gt` dentro de `$size`. Para contar elementos > N se necesita `$expr`, pero eso no está incluido en tus apuntes. Si te preguntan por tamaño exacto, solo puedes hacer:

```js
{ amenities: { $size: 9 } }
```

---

### ✅ 8. Agregación: contar cuántos alojamientos hay por país con WiFi

```js
db.listingsAndReviews.aggregate([
  { $match: { amenities: "Wifi" } },
  { $project: { "address.country": 1 } },
  { $group: { _id: "$address.country", total: { $sum: 1 } } }
])
```

---

### ✅ 9. Agregación: alojamientos en España y mostrar solo `name`, `price` y `beds`, sin `_id`

```js
db.listingsAndReviews.aggregate([
  { $match: { "address.country": "Spain" } },
  { $project: { name: 1, price: 1, beds: 1, _id: 0 } }
])
```

---

### ✅ 10. Buscar alojamientos que tengan **Wifi** o **más de 3 camas**, pero que **no estén en Estados Unidos**

```js
db.listingsAndReviews.find({
  $and: [
    {
      $or: [
        { amenities: "Wifi" },
        { beds: { $gt: 3 } }
      ]
    },
    { "address.country": { $ne: "United States" } }
  ]
})
```

---




