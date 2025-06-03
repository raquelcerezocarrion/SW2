# SW2
¡Perfecto! Aquí tienes los apuntes completos listos para copiar y pegar en un `README.md`. Están en formato Markdown (legible y bonito en GitHub, VS Code, etc.).

---

````markdown
# 📘 Apuntes de Consultas MongoDB (según el PDF)

Este resumen contiene todas las consultas y operadores explicados en el PDF, ordenados de más fáciles a más difíciles. Incluye consultas de nivel examen usando solo lo que está permitido.

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

---

