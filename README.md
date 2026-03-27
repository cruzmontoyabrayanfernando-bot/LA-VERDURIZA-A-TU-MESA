<html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>La Verduriza</title>

<style>
body {
  font-family: Arial;
  margin: 0;
  background: #f4f4f4;
}

/* HEADER */
header {
  background: #0366d6;
  color: white;
  padding: 20px;
  text-align: center;
}

/* CONTENEDOR */
.container {
  padding: 20px;
  max-width: 1200px;
  margin: auto;
}

/* GRID */
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(180px, 220px));
  justify-content: center; /* 👈 centra las tarjetas */
  gap: 15px;
}

/* TARJETAS */
.card {
  background: white;
  padding: 15px;
  border-radius: 15px;
  box-shadow: 0 3px 8px rgba(0,0,0,0.15);
  text-align: center;
  transition: transform 0.2s;
  width: 100%;
}

.card:hover {
  transform: scale(1.03);
}

/* IMAGEN */
.product-img {
  width: 100%;
  height: 120px;
  object-fit: cover;
  border-radius: 10px;
}

/* CONTROLES */
.qty-controls {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 8px;
  margin: 10px 0;
}

.qty-controls button {
  width: 30px;
  height: 30px;
  border-radius: 50%;
  font-size: 16px;
  padding: 0;
}

/* BOTONES */
button {
  background: #43a047;
  color: white;
  border: none;
  padding: 8px 12px;
  border-radius: 6px;
  cursor: pointer;
}

button:hover {
  background: #0366d6;
}

.remove-btn {
  background: red;
}

/* CARRITO */
#cartBox {
  position: fixed;
  bottom: 15px;
  right: 15px;
  background: #ff9800;
  padding: 10px 15px;
  border-radius: 50px;
  display: flex;
  align-items: center;
  gap: 10px;
  color: white;
  font-weight: bold;
  cursor: pointer;
  z-index: 999;
}

#cartBox img {
  width: 30px;
}

/* MODAL */
#modal {
  display: none;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0,0,0,0.6);
  justify-content: center;
  align-items: center;
}

.modal-content {
  background: white;
  padding: 20px;
  border-radius: 10px;
  width: 95%;
  max-width: 400px;
  max-height: 90vh;
  overflow-y: auto;
}

.close {
  float: right;
  cursor: pointer;
  color: red;
}

/* 📱 MOBILE */
@media (max-width: 500px) {
  .grid {
    grid-template-columns: repeat(2, 1fr); /* 2 columnas */
    justify-content: center;
  }

  .card {
    max-width: 100px;
    margin: auto;
  }

  #cartBox {
    width: calc(100% - 20px);
    left: 10px;
    right: 10px;
    justify-content: center;
  }
}
</style>
</head>

<body>

<header>
<h1>LA VERDURIZA</h1>
<p>FRUTAS Y VERDURAS FRESCAS HASTA LA PUERTA DE TU CASA</p>
</header>

<div class="container">
<h2>Productos</h2>
<div class="grid" id="products"></div>
</div>

<!-- CARRITO DE COMPRA-->
<div id="cartBox" onclick="openModal()">
  <img src="https://img.pikbest.com/png-images/20240926/png-of-supermarket-shopping-cart-filled-with-groceries-e2-80-93transparent-background_10889722.png!bw700">
  <div>
    <span>Carrito</span>
    <p>$<span id="total">0</span></p>
  </div>
</div>

<!-- MODAL -->
<div id="modal">
<div class="modal-content">

<span class="close" onclick="closeModal()">X</span>

<h2>🧾 Tu pedido</h2>
<div id="cartList"></div>

<hr>

<h3>Datos de entrega</h3>

<label>Nombre:</label>
<input type="text" id="name">

<label>Dirección:</label>
<input type="text" id="address">

<label>Referencias:</label>
<textarea id="references"></textarea>

<label>Método de pago:</label>
<select id="payment">
  <option value="Efectivo">Efectivo</option>
  <option value="Transferencia">Transferencia</option>
</select>

<label>¿Con cuánto paga?</label>
<input type="number" id="cash" oninput="calculateChange()">

<p id="changeText"></p>

<label>¿No encontraste lo que buscas?</label>
<textarea id="extra"></textarea>

<button onclick="sendWhatsApp()">Confirmar pedido</button>

</div>
</div>

<script>

const products = [
  { name: "Guineo", price: 22, img: "https://uvn-brightspot.s3.amazonaws.com/assets/vixes/imj/1/106401731.jpg" },
  { name: "Plátano", price: 22, img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTndFDmfikZJMF6qeCEWuGVPmkTY0z76rLtgg&s" },
  { name: "Naranja", price: 22, img: "https://frutas.consumer.es/sites/frutas/files/styles/pagina_cabecera_desktop/public/2025-04/naranja.webp" },
  { name: "Tomate", price: 22, img: "https://agrichem.mx/wp-content/uploads/2017/02/tomate2.jpg" },
  { name: "Aguacate", price: 22, img: "https://freshify.com.mx/cdn/shop/files/Aguacate_Primera.webp?v=1744306706&width=600" },
  { name: "Cebolla", price: 22, img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQFoN9386WFngYKhLQG9mSOTqumjeVJs6Ckkw&s" },
   { name: "Chayote", price: 22, img: "https://www.centralenlinea.com/images/thumbs/002/0026729_chayote-verde-oscuro-sin-espinas_550.png" },
  { name: "Zanahoria", price: 22, img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQsQ4D1t2mJA1i6sQC2hghLKu-Bzn_FXZ9v4Q&s" },
  
  
];

let cart = [];
let total = 0;

const container = document.getElementById("products");

products.forEach((p, i) => {
  const div = document.createElement("div");
  div.className = "card";

  div.innerHTML = `
    <img src="${p.img}" class="product-img">
    <h3>${p.name}</h3>
    <p>$${p.price} / kg</p>
    <input type="number" id="qty${i}" min="1" value="1"> kg
    <button onclick="addToCart(${i})">Agregar</button>
  `;

  container.appendChild(div);
});

function addToCart(i) {
  const qty = document.getElementById(`qty${i}`).value;
  if (qty <= 0) return;

  const product = products[i];
  const subtotal = product.price * qty;

  cart.push({
    name: product.name,
    qty: qty,
    subtotal: subtotal
  });

  total += subtotal;
  updateTotal();
}

function updateTotal() {
  document.getElementById("total").innerText = total;
}

function removeFromCart(index) {
  total -= cart[index].subtotal;
  cart.splice(index, 1);
  renderCart();
  updateTotal();
  calculateChange();
}

function renderCart() {
  const list = document.getElementById("cartList");
  list.innerHTML = "";

  if (cart.length === 0) {
    list.innerHTML = "<p>Carrito vacío</p>";
    return;
  }

  cart.forEach((item, i) => {
    list.innerHTML += `
      <div>
        ${item.name} - ${item.qty} kg = $${item.subtotal}
        <button class="remove-btn" onclick="removeFromCart(${i})">X</button>
      </div>
    `;
  });
}

function openModal() {
  renderCart();

  if (cart.length === 0) {
    alert("El carrito está vacío");
    return;
  }

  document.getElementById("modal").style.display = "flex";
   // 👇 OCULTAR CARRITO
  document.getElementById("cartBox").style.display = "none";
}

function closeModal() {
  document.getElementById("modal").style.display = "none";
  // 👇 MOSTRAR CARRITO
  document.getElementById("cartBox").style.display = "flex";
}

function calculateChange() {
  const cash = parseFloat(document.getElementById("cash").value) || 0;
  const changeText = document.getElementById("changeText");

  if (cash === 0) {
    changeText.innerText = "";
    return;
  }

  if (cash >= total) {
    const cambio = cash - total;
    changeText.innerText = `Cambio: $${cambio}`;
    changeText.style.color = "green";
  } else {
    const falta = total - cash;
    changeText.innerText = `Faltan: $${falta}`;
    changeText.style.color = "red";
  }
}

function sendWhatsApp() {

  const name = document.getElementById("name").value.trim();
  const address = document.getElementById("address").value.trim();
  const references = document.getElementById("references").value.trim();
  const payment = document.getElementById("payment").value;
  const cash = parseFloat(document.getElementById("cash").value) || 0;
  const extra = document.getElementById("extra").value.trim();

  if (!name || !address) {
    alert("Completa nombre y dirección");
    return;
  }

  let message = "🛒 *Pedido - La Verduriza* %0A%0A";

  message += `👤 ${name}%0A📍 ${address}%0A📝 ${references}%0A`;
  message += `💳 Pago: ${payment}%0A`;

  if (payment === "Efectivo") {
    message += `💵 Paga con: $${cash}%0A`;

    if (cash >= total) {
      const cambio = cash - total;
      message += `🔄 Cambio: $${cambio}%0A`;
    } else {
      message += `⚠️ Falta: $${(total - cash)}%0A`;
    }
  }

  message += "%0A🧺 *Productos:* %0A";

  cart.forEach(item => {
    message += `• ${item.name} - ${item.qty} kg = $${item.subtotal}%0A`;
  });

  if (extra) {
    message += `%0A❓ También busco:%0A${extra}%0A`;
  }

  message += `%0ATotal: $${total}`;

  const phone = "5219613267670";
  const url = `https://wa.me/${phone}?text=${message}`;

  window.open(url, "_blank");
}

</script>

</body>
</html>
