# Videogamermx
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mega Web Store</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>

<header>
    <h1>Mega Web Store</h1>
</header>

<main>

    <section id="products">
        <h2>Productos</h2>
        <div class="product" data-id="1" data-name="Producto A" data-price="25000">
            <p>Producto A - $25.000</p>
            <button onclick="addToCart(1)">Agregar</button>
        </div>

        <div class="product" data-id="2" data-name="Producto B" data-price="40000">
            <p>Producto B - $40.000</p>
            <button onclick="addToCart(2)">Agregar</button>
        </div>

        <div class="product" data-id="3" data-name="Producto C" data-price="32000">
            <p>Producto C - $32.000</p>
            <button onclick="addToCart(3)">Agregar</button>
        </div>
    </section>

    <section id="cart">
        <h2>Carrito</h2>
        <ul id="cart-items"></ul>
        <p>Total: <span id="total">0</span></p>
        <button onclick="pay()">Pagar</button>
    </section>

</main>

<script src="script.js"></script>
</body>
</html>
body {
    background: #0d1117;
    color: #c9d1d9;
    font-family: Arial, sans-serif;
    margin: 0;
}

header {
    padding: 20px;
    text-align: center;
    background: #161b22;
}

h1, h2 {
    color: #58a6ff;
}

#products, #cart {
    width: 90%;
    margin: auto;
    padding: 20px;
}

.product, #cart {
    background: #161b22;
    padding: 15px;
    border-radius: 10px;
    margin-bottom: 20px;
}

button {
    background: #238636;
    color: white;
    padding: 10px;
    border: none;
    border-radius: 8px;
    cursor: pointer;
}

button:hover {
    background: #2ea043;
}
const backend = "https://TU-BACKEND.onrender.com";  // <-- CAMBIA ESTO

let cart = [];

function addToCart(id) {
    const product = document.querySelector(`.product[data-id="${id}"]`);
    const name = product.dataset.name;
    const price = parseInt(product.dataset.price);

    cart.push({ id, name, price });
    renderCart();
}

function renderCart() {
    const ul = document.getElementById("cart-items");
    ul.innerHTML = "";

    cart.forEach(item => {
        const li = document.createElement("li");
        li.textContent = `${item.name} - $${item.price.toLocaleString()}`;
        ul.appendChild(li);
    });

    const total = cart.reduce((acc, p) => acc + p.price, 0);
    document.getElementById("total").textContent = total.toLocaleString();
}

async function pay() {
    if (cart.length === 0) {
        alert("Tu carrito está vacío.");
        return;
    }

    const total = cart.reduce((acc, p) => acc + p.price, 0);

    const res = await fetch(`${backend}/create-payment`, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ amount: total })
    });

    const data = await res.json();

    if (data.url) {
        window.location.href = data.url;
    } else {
        alert("Error al generar el pago");
        console.log(data);
    }
}
