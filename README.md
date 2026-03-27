<html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>La Verduriza - Frutas y Verduras</title>

  <style>
    
        * { box-sizing: border-box; }
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            margin: 0;
            background: #f9f9f9;
            color: #333;
        }

        header {
            background: #0366d6;
            color: white;
            padding: 2rem; /* Antes era 100%, un error grave */
            text-align: center;
        }

        .container {
            padding: 20px;
            max-width: 1000px;
            margin: auto;
        }

        /* GRID DE PRODUCTOS */
        .grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 20px;
        }

        .card {
            background: white;
            padding: 20px; /* Ajustado de 600px */
            border-radius: 15px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.1);
            text-align: center;
            transition: transform 0.2s;
        }

        .card:hover { transform: scale(1.02); }

        .product-img {
            width: 100%;
            height: 150px;
            object-fit: cover;
            border-radius: 10px;
        }

        /* CONTROLES DE CANTIDAD */
        .qty-controls {
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 15px;
            margin: 15px 0;
        }

        .qty-controls button {
            width: 35px;
            height: 35px;
            border-radius: 50%;
            border: 1px solid #ddd;
            background: #eee;
            cursor: pointer;
            font-size: 18px;
        }

        .qty-number { font-size: 1.2rem; font-weight: bold; }

        /* BOTONES */
        .btn-add {
            background: #43a047;
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 8px;
            cursor: pointer;
            width: 100%;
            font-weight: bold;
        }

        .btn-add:hover { background: #2e7d32; }

        /* CARRITO FLOTANTE */
        #cartBox {
            position: fixed;
            bottom: 20px;
            right: 20px;
            background: #ff9800;
            padding: 10px 20px;
            border-radius: 50px;
            display: flex;
            align-items: center;
            gap: 10px;
            color: white;
            font-weight: bold;
            cursor: pointer;
            box-shadow: 0 4px 15px rgba(0,0,0,0.3);
            z-index: 100;
        }

        #cartBox img { width: 30px; filter: invert(1); }

        /* MODAL */
        #modal {
            display: none;
            position: fixed;
            top: 0; left: 0;
            width: 100%; height: 100%;
            background: rgba(0,0,0,0.7);
            justify-content: center;
            align-items: center;
            z-index: 1000;
        }

        .modal-content {
            background: white;
            padding: 25px;
            border-radius: 15px;
            width: 90%;
            max-width: 450px;
            max-height: 85vh;
            overflow-y: auto;
        }

        .cart-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 10px 0;
            border-bottom: 1px solid #eee;
        }

        .remove-btn { background: #ff5252; color: white; border: none; padding: 5px 10px; border-radius: 4px; cursor: pointer;}

        /* FORMULARIO */
        input, textarea, select {
            width: 100%;
            margin-bottom: 10px;
            padding: 10px;
            border: 1px solid #ccc;
            border-radius: 5px;
        }

        .send-btn {
            background: #25d366;
            color: white;
            width: 100%;
            padding: 15px;
            border: none;
            border-radius: 8px;
            font-size: 1rem;
            font-weight: bold;
            cursor: pointer;
            margin-top: 10px;
        }
    </style>
</head>
<body>

<header>
    <h1>🥬 La Verduriza</h1>
    <p>Frutas y verduras frescas a domicilio</p>
</header>

<div class="container">
    <h2>Nuestros Productos</h2>
    <div class="grid" id="products"></div>
</div>

<div id="cartBox" onclick="openModal()">
    <img src="https://cdn-icons-png.flaticon.com/512/263/263142.png" alt="Carrito">
    <span>Total: $<span id="total">0</span></span>
</div>

<div id="modal">
    <div class="modal-content">
        <span style="float:right; cursor:pointer; color:red; font-weight:bold;" onclick="closeModal()">Cerrar (X)</span>
        <h2>🧾 Tu pedido</h2>
        <div id="cartList">
            <p>El carrito está vacío</p>
        </div>
        <hr>
        <h3>Datos de entrega</h3>
        <input type="text" id="name" placeholder="Tu nombre">
        <input type="text" id="address" placeholder="Dirección completa">
        <textarea id="references" placeholder="Referencias (casa azul, cerca de...)"></textarea>
        
        <label>Método de pago:</label>
        <select id="payment" onchange="toggleCashInput()">
            <option value="Efectivo">Efectivo</option>
            <option value="Transferencia">Transferencia</option>
        </select>

        <div id="cashSection">
            <input type="number" id="cash" placeholder="¿Con cuánto pagas?" oninput="calculateChange()">
            <p id="changeText"></p>
        </div>

        <button class="send-btn" onclick="sendWhatsApp()">🚀 Enviar Pedido por WhatsApp</button>
    </div>
</div>

<script>
const products = [
    { name: "Guineo", price: 20, img: "https://uvn-brightspot.s3.amazonaws.com/assets/vixes/imj/1/106401731.jpg" },
    { name: "Plátano", price: 22, img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTndFDmfikZJMF6qeCEWuGVPmkTY0z76rLtgg&s" },
    { name: "Naranja", price: 27, img: "https://frutas.consumer.es/sites/frutas/files/styles/pagina_cabecera_desktop/public/2025-04/naranja.webp" }
];

let cart = [];
let total = 0;

// Renderizar productos al cargar
function init() {
    const container = document.getElementById("products");
    products.forEach((p, i) => {
        const div = document.createElement("div");
        div.className = "card";
        div.innerHTML = `
            <img src="${p.img}" class="product-img">
            <h3>${p.name}</h3>
            <p><strong>$${p.price}</strong> / unidad</p>
            <div class="qty-controls">
                <button onclick="changeQty(${i}, -1)">-</button>
                <span id="qty${i}" class="qty-number">1</span>
                <button onclick="changeQty(${i}, 1)">+</button>
            </div>
            <button class="btn-add" onclick="addToCart(${i})">Agregar al carrito</button>
        `;
        container.appendChild(div);
    });
}

function changeQty(i, change) {
    const el = document.getElementById(`qty${i}`);
    let value = parseInt(el.innerText);
    value = Math.max(1, value + change);
    el.innerText = value;
}

function addToCart(i) {
    const qty = parseInt(document.getElementById(`qty${i}`).innerText);
    const product = products[i];
    
    // Verificar si ya existe en el carrito
    const existingIndex = cart.findIndex(item => item.name === product.name);
    
    if (existingIndex > -1) {
        cart[existingIndex].qty += qty;
        cart[existingIndex].subtotal += (product.price * qty);
    } else {
        cart.push({ name: product.name, qty, subtotal: product.price * qty });
    }
    
    total += product.price * qty;
    updateTotal();
    alert(`${product.name} agregado!`);
}

function updateTotal() {
    document.getElementById("total").innerText = total;
}

function renderCart() {
    const list = document.getElementById("cartList");
    if (cart.length === 0) {
        list.innerHTML = "<p>El carrito está vacío</p>";
        return;
    }
    
    list.innerHTML = "";
    cart.forEach((item, i) => {
        list.innerHTML += `
            <div class="cart-item">
                <span><strong>${item.name}</strong> (x${item.qty})</span>
                <span>$${item.subtotal}</span>
                <button class="remove-btn" onclick="removeFromCart(${i})">Eliminar</button>
            </div>
        `;
    });
}

function removeFromCart(i) {
    total -= cart[i].subtotal;
    cart.splice(i, 1);
    renderCart();
    updateTotal();
    calculateChange();
}

function openModal() {
    renderCart();
    document.getElementById("modal").style.display = "flex";
}

function closeModal() {
    document.getElementById("modal").style.display = "none";
}

function toggleCashInput() {
    const payment = document.getElementById("payment").value;
    document.getElementById("cashSection").style.display = (payment === "Efectivo") ? "block" : "none";
}

function calculateChange() {
    const cash = parseFloat(document.getElementById("cash").value) || 0;
    const changeText = document.getElementById("changeText");
    if (cash > total) {
        changeText.innerText = "Cambio: $" + (cash - total);
        changeText.style.color = "green";
    } else if (cash > 0 && cash < total) {
        changeText.innerText = "Faltan: $" + (total - cash);
        changeText.style.color = "red";
    } else {
        changeText.innerText = "";
    }
}

function sendWhatsApp() {
    if (cart.length === 0) return alert("El carrito está vacío");
    
    const name = document.getElementById("name").value;
    const addr = document.getElementById("address").value;
    const pay = document.getElementById("payment").value;

    if (!name || !addr) return alert("Por favor completa nombre y dirección");

    let message = `*NUEVO PEDIDO - LA VERDURIZA*%0A`;
    message += `--------------------------%0A`;
    cart.forEach(p => {
        message += `• ${p.name} x${p.qty} = $${p.subtotal}%0A`;
    });
    message += `--------------------------%0A`;
    message += `*TOTAL:* $${total}%0A%0A`;
    message += `*Cliente:* ${name}%0A`;
    message += `*Dirección:* ${addr}%0A`;
    message += `*Pago:* ${pay}`;

    const phone = "5219613267670";
    window.open(`https://wa.me/${phone}?text=${message}`);
}

init();
</script>

</body>
</html>
