# Using-PUT
const express = require("express")
const app = express()
const fs = require("fs")

function hasContent(str){
    return typeof str === 'string' && str.length > 0
}

app.use(express.json())
app.use(express.urlencoded({ extended: true })) //middleware 

app.get("/products", (req, res) => {
    const {search, category, subcategory} = req.query
    
    let fileContent = JSON.parse(fs.readFileSync("./products.json", {encoding:"utf-8"}))
    let products = fileContent.products

    if(hasContent(category)){
        products = products.filter(product => product.category.toLowerCase() == category.toLowerCase())
    }
    if(hasContent(subcategory)){
        products = products.filter(product => product.subcategory.toLowerCase() === subcategory.toLowerCase())
    }
    if(hasContent(search)){
        products = products.filter(product => {
            const strProduct = JSON.stringify(product).toLowerCase()
            const lowerSearch = search.toLowerCase()
            return strProduct.includes(lowerSearch)
        })
    }
    
    res.json({
        count: products.length, 
        products
    })
})

app.post('/products', (req, res) => {

    const newProduct = req.body

    const db = JSON.parse(fs.readFileSync("./products.json", {encoding:"utf-8"}))

    db.products.push(newProduct)

    fs.writeFileSync("./products.json", JSON.stringify(db, null, 2))

    res.status(201).json(newProduct)
})

app.put('/products/:id', (req, res) => {
    
    const {body, params:{id}} = req

    const db = JSON.parse(fs.readFileSync("./products.json", {encoding:"utf-8"}))

    const productIndex = db.products.findIndex(product => product.id == id)

    

    res.json({
        msg: `Product with id ${id} was successfully modified.`
    })
})

app.listen(9000, () => {
    console.log("Server running on port 9000")
})
