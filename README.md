<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>La Verduriza</title>

<style>
body { font-family: Arial; margin: 0; background: #f4f4f4; }

header {
  background: #2e7d32;
  color: white;
  padding: 20px;
  text-align: center;
}

/* CONTENEDOR RESPONSIVE */
.container {
  padding: 20px;
  max-width: 1200px;
  margin: auto;
}

/* GRID RESPONSIVE */
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
  gap: 12px;
}

/* TARJETAS */
.card {
  background: white;
  padding: 12px;
  border-radius: 10px;
  box-shadow: 0 2px 5px rgba(0,0,0,0.2);
  text-align: center;
}

.product-img {
  width: 100%;
  height: 120px;
  object-fit: cover;
  border-radius: 10px;
  margin-bottom: 10px;
}

/* INPUTS */
input, textarea, select {
  width: 100%;
  padding: 10px;
  margin-top: 5px;
  border-radius: 5px;
  border: 1px solid #ccc;
  font-size: 16px;
}

/* BOTONES */
button {
  background: #43a047;
  color: white;
  border: none;
  padding: 10px;
  border-radius: 5px;
  cursor: pointer;
  margin-top: 5px;
  width: 100%;
}

button:hover { background: #2e7d32; }

.remove-btn {
  background: red;
  width: auto;
}

/* CARRITO PRO */
#cartBox {
  position: fixed;
  bottom: 15px;
  right: 15px;
  background: #ff9800;
  padding: 12px 15px;
  border-radius: 50px;
  cursor: pointer;
  color: white;
  font-weight: bold;
  display: flex;
  align-items: center;
  gap: 10px;
  box-shadow: 0 4px 10px rgba(0,0,0,0.3);
  z-index: 999;
  transition: transform 0.2s;
}

#cartBox:hover {
  transform: scale(1.05);
}

#cartBox img {
  width: 30px;
  height: 30px;
}

#cartBox span {
  font-size: 14px;
}

#cartBox p {
  margin: 0;
  font-size: 16px;
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
  max-width: 420px;
  max-height: 90vh;
  overflow-y: auto;
}

.close {
  float: right;
  cursor: pointer;
  color: red;
  font-weight: bold;
}

#changeText {
  font-weight: bold;
  margin-top: 10px;
}

/* MOBILE */
@media (max-width: 480px) {

  header h1 { font-size: 100%; }

  .grid {
    grid-template-columns: repeat(2, 1fr);
  }

  .product-img {
    height: 90px;
  }

  button {
    padding: 12px;
    font-size: 16px;
  }

  #cartBox {
    width: calc(100% - 20px);
    right: 10px;
    left: 10px;
    justify-content: center;
  }

}
</style>
</head>

<body>

<header>
<h1>🥬 La Verduriza</h1>
<p>Frutas y verduras frescas hasta tu puerta</p>
</header>

<div class="container">
<h2>Productos</h2>
<div class="grid" id="products"></div>
</div>

<!-- CARRITO MEJORADO -->
<div id="cartBox" onclick="openModal()">
  <img src="https://cdn-icons-png.flaticon.com/512/263/263142.png">
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
  { name: "Guineo", price: 20, img: "https://uvn-brightspot.s3.amazonaws.com/assets/vixes/imj/1/106401731.jpg" },
  { name: "Plátano", price: 22, img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTndFDmfikZJMF6qeCEWuGVPmkTY0z76rLtgg&s" },
  { name: "Naranja", price: 27, img: "https://frutas.consumer.es/sites/frutas/files/styles/pagina_cabecera_desktop/public/2025-04/naranja.webp" },
  { name: "Tomate", price: 42, img: "https://agrichem.mx/wp-content/uploads/2017/02/tomate2.jpg" },
  { name: "Cebolla", price: 20, img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQFoN9386WFngYKhLQG9mSOTqumjeVJs6Ckkw&s" },
  { name: "Zanahoria", price: 22, img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQsQ4D1t2mJA1i6sQC2hghLKu-Bzn_FXZ9v4Q&s" }
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
}

function closeModal() {
  document.getElementById("modal").style.display = "none";
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
