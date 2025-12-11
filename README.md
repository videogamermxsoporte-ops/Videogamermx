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
const backend = https://github.com/videogamermxsoporte-ops/Videogamermx.git

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
{
  "name": "megawebstore-backend",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^4.19.0",
    "axios": "^1.6.0",
    "crypto": "latest",
    "cors": "^2.8.5"
  }
import express from "express";
import axios from "axios";
import cors from "cors";

const app = express();
app.use(express.json());
app.use(cors());

const WOMPI_PRIVATE_KEY = process.env.WOMPI_PRIVATE_KEY;
const REDIRECT_URL = process.env.REDIRECT_URL || "https://tu-netlify.netlify.app/pago";
const WOMPI_URL = "https://production.wompi.co";

app.post("/create-payment", async (req, res) => {
    try {
        const { amount } = req.body;

        const charge = {
            amount_in_cents: amount * 100,
            currency: "COP",
            customer_email: "cliente@example.com",
            reference: "MEGAWEB-" + Date.now(),
            redirect_url: REDIRECT_URL
        };

        const wompiRes = await axios.post(
            `${WOMPI_URL}/v1/transactions`,
            charge,
            {
                headers: {
                    Authorization: `Bearer ${WOMPI_PRIVATE_KEY}`,
                    "Content-Type": "application/json"
                }
            }
        );

        const checkoutURL = wompiRes.data.data.payment_method.extra.async_payment_url;

        return res.json({ url: checkoutURL });

    } catch (err) {
        console.error(err.response?.data || err.message);
        return res.json({ error: err.response?.data || err.message });
    }
});

app.listen(3000, () => console.log("Servidor corriendo en puerto 3000"));
