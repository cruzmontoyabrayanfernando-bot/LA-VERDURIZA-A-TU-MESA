<html>
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
  padding: 100px;
  text-align: center;
}

.container {
  padding: 100px;
  max-width: 1200px;
  margin: auto;
}

.grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
  gap: 12px;
}

.card {
  background: white;
  padding: 30px;
  border-radius: 30px;
  box-shadow: 0 3px 8px rgba(0,0,0,0.15);
  text-align: center;
  transition: transform 0.2s;
}

.card:hover {
  transform: scale(1.03);
}

.product-img {
  width: 100%;
  height: 110px;
  object-fit: cover;
  border-radius: 10px;
}

/* CONTROLES + - */
.qty-controls {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 10px;
  margin: 10px 0;
}

.qty-controls button {
  width: 30px;
  height: 30px;
  border-radius: 50%;
  font-size: 18px;
  padding: 0;
}

.qty-number {
  font-size: 18px;
  font-weight: bold;
}

/* BOTONES */
button {
  background: #43a047;
  color: white;
  border: none;
  padding: 10px;
  border-radius: 6px;
  cursor: pointer;
}

button:hover { background: #2e7d32; }

.remove-btn {
  background: red;
}

/* CARRITO */
#cartBox {
  position: fixed;
  bottom: 15px;
  right: 15px;
  background: #ff9800;
  padding: 12px 15px;
  border-radius: 50px;
  display: flex;
  align-items: center;
  gap: 10px;
  color: white;
  font-weight: bold;
  cursor: pointer;
  box-shadow: 0 4px 10px rgba(0,0,0,0.3);
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
  max-width: 420px;
  max-height: 90vh;
  overflow-y: auto;
}

.close {
  float: right;
  cursor: pointer;
  color: red;
}

/* MOBILE */
@media (max-width: 480px) {
  .grid { grid-template-columns: repeat(2, 1fr); }

  #cartBox {
    width: calc(100% - 20px);
    left: 10px;
    right: 10px;
    justify-content: center;
  }
}

#changeText {
  font-weight: bold;
  margin-top: 10px;
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

<div id="cartBox" onclick="openModal()">
<img src="https://cdn-icons-png.flaticon.com/512/263/263142.png">
<span>$<span id="total">0</span></span>
</div>

<div id="modal">
<div class="modal-content">

<span class="close" onclick="closeModal()">X</span>

<h2>🧾 Tu pedido</h2>
<div id="cartList"></div>

<hr>

<input type="text" id="name" placeholder="Nombre">
<input type="text" id="address" placeholder="Dirección">
<textarea id="references" placeholder="Referencias"></textarea>

<select id="payment">
<option>Efectivo</option>
<option>Transferencia</option>
</select>

<input type="number" id="cash" placeholder="¿Con cuánto paga?" oninput="calculateChange()">
<p id="changeText"></p>

<button onclick="sendWhatsApp()">Enviar pedido</button>

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

const container = document.getElementById("products");

products.forEach((p, i) => {
  const div = document.createElement("div");
  div.className = "card";

  div.innerHTML = `
    <img src="${p.img}" class="product-img">
    <h3>${p.name}</h3>
    <p>$${p.price}</p>

    <div class="qty-controls">
      <button onclick="changeQty(${i}, -1)">➖</button>
      <span id="qty${i}" class="qty-number">1</span>
      <button onclick="changeQty(${i}, 1)">➕</button>
    </div>

    <button onclick="addToCart(${i})">Agregar</button>
  `;

  container.appendChild(div);
});

function changeQty(i, change) {
  const el = document.getElementById(`qty${i}`);
  let value = parseInt(el.innerText);
  value += change;
  if (value < 1) value = 1;
  el.innerText = value;
}

function addToCart(i) {
  const qty = parseInt(document.getElementById(`qty${i}`).innerText);
  const product = products[i];
  const subtotal = product.price * qty;

  cart.push({ name: product.name, qty, subtotal });
  total += subtotal;

  updateTotal();
}

function updateTotal() {
  document.getElementById("total").innerText = total;
}

function renderCart() {
  const list = document.getElementById("cartList");
  list.innerHTML = "";

  cart.forEach((item, i) => {
    list.innerHTML += `
      <div>
        ${item.name} x${item.qty} = $${item.subtotal}
        <button class="remove-btn" onclick="removeFromCart(${i})">X</button>
      </div>
    `;
  });
}

function removeFromCart(i) {
  total -= cart[i].subtotal;
  cart.splice(i, 1);
  renderCart();
  updateTotal();
}

function openModal() {
  renderCart();
  document.getElementById("modal").style.display = "flex";
}

function closeModal() {
  document.getElementById("modal").style.display = "none";
}

function calculateChange() {
  const cash = parseFloat(document.getElementById("cash").value) || 0;

  if (cash >= total) {
    document.getElementById("changeText").innerText = "Cambio: $" + (cash - total);
  } else {
    document.getElementById("changeText").innerText = "Faltan: $" + (total - cash);
  }
}

function sendWhatsApp() {
  let message = "Pedido:%0A";

  cart.forEach(p => {
    message += `${p.name} x${p.qty} = $${p.subtotal}%0A`;
  });

  message += `Total: $${total}`;

  window.open(`https://wa.me/5219613267670?text=${message}`);
}

</script>

</body>
</html>
