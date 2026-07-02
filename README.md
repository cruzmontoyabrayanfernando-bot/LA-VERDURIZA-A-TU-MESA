<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>La Verduriza</title>

<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Baloo+2:wght@500;700;800&family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">

<style>
  :root {
    --verde-oscuro: #1B4332;
    --verde-fresco: #40916C;
    --verde-claro: #74C69D;
    --crema: #FAF3E7;
    --madera: #C48A5A;
    --etiqueta: #E8590C;
    --texto: #22333B;
    --blanco: #FFFFFF;
    --radio: 16px;
    --sombra: 0 6px 18px rgba(27, 67, 50, 0.12);
    --sombra-hover: 0 10px 24px rgba(27, 67, 50, 0.2);
  }

  * { box-sizing: border-box; }

  body {
    font-family: 'Inter', Arial, sans-serif;
    margin: 0;
    background: var(--crema);
    color: var(--texto);
    -webkit-font-smoothing: antialiased;
  }

  /* Foco visible para accesibilidad */
  a:focus-visible,
  button:focus-visible,
  input:focus-visible,
  select:focus-visible,
  textarea:focus-visible {
    outline: 3px solid var(--etiqueta);
    outline-offset: 2px;
  }

  /* HEADER */
  header {
    background: linear-gradient(135deg, var(--verde-oscuro), var(--verde-fresco));
    color: var(--blanco);
    padding: clamp(24px, 6vw, 40px) 20px;
    text-align: center;
    position: relative;
    overflow: hidden;
  }

  header::after {
    content: "";
    position: absolute;
    inset: 0;
    background: repeating-linear-gradient(
      45deg,
      rgba(255,255,255,0.04) 0px,
      rgba(255,255,255,0.04) 2px,
      transparent 2px,
      transparent 14px
    );
    pointer-events: none;
  }

  header h1 {
    font-family: 'Baloo 2', sans-serif;
    font-weight: 800;
    font-size: clamp(1.8rem, 6vw, 2.6rem);
    margin: 0 0 6px;
    letter-spacing: 0.5px;
  }

  header p {
    margin: 0;
    font-size: clamp(0.9rem, 3vw, 1.05rem);
    opacity: 0.92;
  }

  /* CONTENEDOR */
  .container {
    padding: 24px 16px 100px;
    max-width: 1200px;
    margin: auto;
  }

  .container h2 {
    font-family: 'Baloo 2', sans-serif;
    color: var(--verde-oscuro);
    font-size: 1.4rem;
    margin: 0 0 16px;
  }

  /* GRID responsive: 1 col en celular chico, 2 en celular grande, 3-4 en tablet/desktop */
  .grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 14px;
  }

  @media (min-width: 480px) {
    .grid { grid-template-columns: repeat(2, 1fr); gap: 16px; }
  }

  @media (min-width: 640px) {
    .grid { grid-template-columns: repeat(3, 1fr); }
  }

  @media (min-width: 960px) {
    .grid { grid-template-columns: repeat(4, 1fr); gap: 20px; }
  }

  @media (max-width: 380px) {
    .grid { grid-template-columns: 1fr 1fr; gap: 10px; }
  }

  /* TARJETAS estilo "etiqueta de cajón de madera" */
  .card {
    background: var(--blanco);
    padding: 14px;
    border-radius: var(--radio);
    box-shadow: var(--sombra);
    text-align: center;
    transition: transform 0.2s ease, box-shadow 0.2s ease;
    border: 1px solid rgba(27, 67, 50, 0.06);
    display: flex;
    flex-direction: column;
  }

  .card:hover,
  .card:focus-within {
    transform: translateY(-3px);
    box-shadow: var(--sombra-hover);
  }

  .card.just-added {
    animation: pop 0.35s ease;
  }

  @keyframes pop {
    0%   { transform: scale(1); }
    40%  { transform: scale(1.04); }
    100% { transform: scale(1); }
  }

  /* IMAGEN */
  .product-img-wrap {
    position: relative;
    border-radius: 12px;
    overflow: hidden;
    aspect-ratio: 4 / 3;
    background: var(--crema);
  }

  .product-img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    display: block;
  }

  .price-tag {
    position: absolute;
    top: 8px;
    right: 8px;
    background: var(--etiqueta);
    color: var(--blanco);
    font-weight: 700;
    font-size: 0.85rem;
    padding: 4px 9px;
    border-radius: 999px;
    box-shadow: 0 2px 6px rgba(0,0,0,0.2);
  }

  .card h3 {
    font-family: 'Baloo 2', sans-serif;
    margin: 10px 0 2px;
    font-size: 1.05rem;
    color: var(--verde-oscuro);
  }

  .unit-label {
    margin: 0;
    font-size: 0.78rem;
    color: #6b7d75;
  }

  /* CONTROLES */
  .qty-controls {
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 10px;
    margin: 10px 0;
  }

  .qty-controls button {
    width: 32px;
    height: 32px;
    border-radius: 50%;
    font-size: 16px;
    padding: 0;
    line-height: 1;
  }

  .qty-number {
    min-width: 22px;
    font-weight: 700;
    font-variant-numeric: tabular-nums;
  }

  /* BOTONES */
  button {
    background: var(--verde-fresco);
    color: white;
    border: none;
    padding: 10px 14px;
    border-radius: 10px;
    cursor: pointer;
    font-family: 'Inter', sans-serif;
    font-weight: 600;
    font-size: 0.92rem;
    transition: background 0.15s ease, transform 0.1s ease;
  }

  button:hover { background: var(--verde-oscuro); }
  button:active { transform: scale(0.97); }

  button:disabled {
    background: #b7bcb9;
    cursor: not-allowed;
  }
  button:disabled:hover { background: #b7bcb9; }

  .add-btn {
    margin-top: auto;
    width: 100%;
  }

  .remove-btn {
    background: transparent;
    color: #d64545;
    border: 1px solid #f0b8b8;
    padding: 4px 10px;
    font-size: 0.8rem;
  }
  .remove-btn:hover { background: #fdeaea; color: #b83232; }

  /* CARRITO flotante */
  #cartBox {
    position: fixed;
    bottom: 16px;
    right: 16px;
    background: var(--etiqueta);
    padding: 12px 20px;
    border-radius: 50px;
    display: flex;
    align-items: center;
    gap: 10px;
    color: white;
    font-weight: 700;
    cursor: pointer;
    z-index: 999;
    box-shadow: 0 6px 18px rgba(232, 89, 12, 0.4);
    border: none;
    font-size: 1rem;
  }

  #cartBox:hover { background: #c94f0a; }

  #cartCount {
    background: rgba(255,255,255,0.25);
    border-radius: 999px;
    padding: 2px 9px;
    font-size: 0.8rem;
  }

  /* MODAL */
  #modal {
    display: none;
    position: fixed;
    inset: 0;
    background: rgba(20, 30, 25, 0.55);
    justify-content: center;
    align-items: center;
    z-index: 1000;
  }

  #modal.open { display: flex; }

  .modal-content {
    background: var(--crema);
    padding: 24px;
    border-radius: 18px;
    width: 95%;
    max-width: 420px;
    max-height: 90vh;
    overflow-y: auto;
    box-shadow: 0 20px 50px rgba(0,0,0,0.3);
  }

  .modal-content h2 {
    font-family: 'Baloo 2', sans-serif;
    color: var(--verde-oscuro);
    margin-top: 0;
  }

  .close {
    float: right;
    cursor: pointer;
    color: var(--texto);
    background: rgba(0,0,0,0.06);
    width: 28px;
    height: 28px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    font-weight: 700;
  }
  .close:hover { background: rgba(0,0,0,0.12); }

  #cartList {
    margin: 14px 0;
    display: flex;
    flex-direction: column;
    gap: 8px;
  }

  .cart-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    background: var(--blanco);
    padding: 8px 12px;
    border-radius: 10px;
    font-size: 0.92rem;
  }

  .empty-cart {
    text-align: center;
    color: #6b7d75;
    padding: 20px 0;
    font-size: 0.95rem;
  }

  .cart-total-line {
    display: flex;
    justify-content: space-between;
    font-weight: 700;
    font-size: 1.1rem;
    color: var(--verde-oscuro);
    padding: 10px 0;
    border-top: 2px dashed rgba(27,67,50,0.15);
    margin-top: 6px;
  }

  label {
    display: block;
    font-size: 0.8rem;
    font-weight: 600;
    color: var(--verde-oscuro);
    margin: 12px 0 4px;
  }

  input, select, textarea {
    width: 100%;
    padding: 10px 12px;
    border-radius: 10px;
    border: 1px solid #d8ddd9;
    font-family: 'Inter', sans-serif;
    font-size: 0.95rem;
    background: var(--blanco);
    color: var(--texto);
  }

  textarea { resize: vertical; min-height: 60px; }

  .field-error {
    color: #c0392b;
    font-size: 0.78rem;
    margin: 3px 0 0;
    min-height: 1em;
  }

  #changeText {
    font-weight: 700;
    margin: 8px 0 0;
    color: var(--verde-oscuro);
  }

  #cash-field { display: none; }

  #sendBtn {
    width: 100%;
    margin-top: 18px;
    padding: 13px;
    font-size: 1rem;
    border-radius: 12px;
  }

  #formMsg {
    text-align: center;
    color: #c0392b;
    font-size: 0.82rem;
    margin-top: 8px;
    min-height: 1.2em;
  }

  /* 📱 MOBILE: modal en hoja completa desde abajo */
  @media (max-width: 500px) {
    #cartBox {
      left: 12px;
      right: 12px;
      width: auto;
      justify-content: center;
    }

    #modal {
      align-items: flex-end;
    }

    .modal-content {
      width: 100%;
      max-width: 100%;
      max-height: 92vh;
      border-radius: 20px 20px 0 0;
      animation: slideUp 0.25s ease;
    }
  }

  @keyframes slideUp {
    from { transform: translateY(30px); opacity: 0; }
    to   { transform: translateY(0); opacity: 1; }
  }

  @media (prefers-reduced-motion: reduce) {
    * { animation: none !important; transition: none !important; }
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

<button id="cartBox" onclick="openModal()" aria-haspopup="dialog">
  <img src="https://cdn-icons-png.flaticon.com/512/263/263142.png" width="26" alt="" aria-hidden="true">
  <span>$<span id="total">0</span></span>
  <span id="cartCount">0</span>
</button>

<div id="modal" role="dialog" aria-modal="true" aria-labelledby="modalTitle">
  <div class="modal-content">

    <span class="close" onclick="closeModal()" role="button" tabindex="0" aria-label="Cerrar">✕</span>

    <h2 id="modalTitle">🧾 Tu pedido</h2>
    <div id="cartList"></div>

    <div class="cart-total-line">
      <span>Total</span>
      <span>$<span id="modalTotal">0</span></span>
    </div>

    <label for="name">Nombre</label>
    <input type="text" id="name" placeholder="Tu nombre completo">
    <p class="field-error" id="nameError"></p>

    <label for="address">Dirección</label>
    <input type="text" id="address" placeholder="Calle, número, colonia">
    <p class="field-error" id="addressError"></p>

    <label for="references">Referencias (opcional)</label>
    <textarea id="references" placeholder="Ej. casa color azul, junto a la tienda..."></textarea>

    <label for="payment">Forma de pago</label>
    <select id="payment" onchange="togglePaymentFields()">
      <option value="Efectivo">Efectivo</option>
      <option value="Transferencia">Transferencia</option>
    </select>

    <div id="cash-field">
      <label for="cash">¿Con cuánto paga?</label>
      <input type="number" id="cash" placeholder="Ej. 200" min="0" oninput="calculateChange()">
      <p id="changeText"></p>
    </div>

    <button id="sendBtn" onclick="sendWhatsApp()">Enviar pedido por WhatsApp</button>
    <p id="formMsg"></p>

  </div>
</div>

<script>

const WHATSAPP_NUMBER = "5219613267670";

const products = [
  { name: "Guineo",   price: 20, unit: "kg", img: "https://uvn-brightspot.s3.amazonaws.com/assets/vixes/imj/1/106401731.jpg" },
  { name: "Plátano",  price: 22, unit: "kg", img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTndFDmfikZJMF6qeCEWuGVPmkTY0z76rLtgg&s" },
  { name: "Naranja",  price: 27, unit: "kg", img: "https://frutas.consumer.es/sites/frutas/files/styles/pagina_cabecera_desktop/public/2025-04/naranja.webp" }
];

// cart: [{ name, price, qty, subtotal }]
let cart = [];
let total = 0;

const container = document.getElementById("products");

products.forEach((p, i) => {
  const div = document.createElement("div");
  div.className = "card";
  div.id = `card${i}`;

  div.innerHTML = `
    <div class="product-img-wrap">
      <img src="${p.img}" class="product-img" alt="${p.name}" loading="lazy">
      <span class="price-tag">$${p.price}</span>
    </div>
    <h3>${p.name}</h3>
    <p class="unit-label">precio por ${p.unit}</p>

    <div class="qty-controls">
      <button type="button" onclick="changeQty(${i}, -1)" aria-label="Quitar uno de ${p.name}">➖</button>
      <span id="qty${i}" class="qty-number">1</span>
      <button type="button" onclick="changeQty(${i}, 1)" aria-label="Agregar uno de ${p.name}">➕</button>
    </div>

    <button type="button" class="add-btn" onclick="addToCart(${i})">Agregar</button>
  `;

  container.appendChild(div);
});

function changeQty(i, change) {
  const el = document.getElementById(`qty${i}`);
  let value = parseInt(el.innerText, 10) + change;
  if (value < 1) value = 1;
  el.innerText = value;
}

function addToCart(i) {
  const qtyEl = document.getElementById(`qty${i}`);
  const qty = parseInt(qtyEl.innerText, 10);
  const product = products[i];
  const subtotal = product.price * qty;

  // Si el producto ya está en el carrito, suma la cantidad en vez de duplicar la línea
  const existing = cart.find(item => item.name === product.name);
  if (existing) {
    existing.qty += qty;
    existing.subtotal += subtotal;
  } else {
    cart.push({ name: product.name, price: product.price, qty, subtotal });
  }

  total += subtotal;
  updateTotal();

  // Reinicia el selector de cantidad a 1
  qtyEl.innerText = 1;

  // Pequeña animación de confirmación
  const card = document.getElementById(`card${i}`);
  card.classList.remove("just-added");
  void card.offsetWidth; // reinicia la animación si se hace clic varias veces
  card.classList.add("just-added");
}

function updateTotal() {
  document.getElementById("total").innerText = total;
  document.getElementById("modalTotal").innerText = total;
  const count = cart.reduce((sum, item) => sum + item.qty, 0);
  document.getElementById("cartCount").innerText = count;
}

function renderCart() {
  const list = document.getElementById("cartList");
  list.innerHTML = "";

  if (cart.length === 0) {
    list.innerHTML = `<p class="empty-cart">Tu carrito está vacío. Agrega productos para empezar 🛒</p>`;
    return;
  }

  cart.forEach((item, i) => {
    const div = document.createElement("div");
    div.className = "cart-item";
    div.innerHTML = `
      <span>${item.name} x${item.qty} = $${item.subtotal}</span>
      <button type="button" class="remove-btn" onclick="removeFromCart(${i})">Quitar</button>
    `;
    list.appendChild(div);
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
  document.getElementById("modal").classList.add("open");
  document.getElementById("formMsg").innerText = "";
}

function closeModal() {
  document.getElementById("modal").classList.remove("open");
}

// Cerrar con la tecla Escape
document.addEventListener("keydown", (e) => {
  if (e.key === "Escape") closeModal();
});

function togglePaymentFields() {
  const isCash = document.getElementById("payment").value === "Efectivo";
  document.getElementById("cash-field").style.display = isCash ? "block" : "none";
  if (!isCash) document.getElementById("changeText").innerText = "";
}

function calculateChange() {
  const cash = parseFloat(document.getElementById("cash").value) || 0;
  const changeText = document.getElementById("changeText");

  if (document.getElementById("cash").value === "") {
    changeText.innerText = "";
    return;
  }

  if (cash >= total) {
    changeText.innerText = "Cambio: $" + (cash - total);
  } else {
    changeText.innerText = "Faltan: $" + (total - cash);
  }
}

function sendWhatsApp() {
  const name = document.getElementById("name").value.trim();
  const address = document.getElementById("address").value.trim();
  const references = document.getElementById("references").value.trim();
  const payment = document.getElementById("payment").value;
  const cash = document.getElementById("cash").value;

  const nameError = document.getElementById("nameError");
  const addressError = document.getElementById("addressError");
  const formMsg = document.getElementById("formMsg");

  nameError.innerText = "";
  addressError.innerText = "";
  formMsg.innerText = "";

  let hasError = false;

  if (cart.length === 0) {
    formMsg.innerText = "Tu carrito está vacío. Agrega al menos un producto.";
    hasError = true;
  }
  if (!name) {
    nameError.innerText = "Por favor escribe tu nombre.";
    hasError = true;
  }
  if (!address) {
    addressError.innerText = "Por favor escribe tu dirección.";
    hasError = true;
  }

  if (hasError) return;

  let message = "🧾 *Nuevo pedido - La Verduriza*%0A%0A";

  cart.forEach(p => {
    message += `${p.name} x${p.qty} = $${p.subtotal}%0A`;
  });

  message += `%0A*Total: $${total}*%0A%0A`;
  message += `👤 Nombre: ${name}%0A`;
  message += `📍 Dirección: ${address}%0A`;
  if (references) message += `📝 Referencias: ${references}%0A`;
  message += `💳 Pago: ${payment}%0A`;

  if (payment === "Efectivo" && cash) {
    const cambio = parseFloat(cash) - total;
    message += `💵 Paga con: $${cash}%0A`;
    message += cambio >= 0 ? `↩️ Cambio: $${cambio}%0A` : `⚠️ Falta: $${Math.abs(cambio)}%0A`;
  }

  window.open(`https://wa.me/${WHATSAPP_NUMBER}?text=${message}`, "_blank");
}

// Estado inicial de los campos de pago
togglePaymentFields();

</script>
</body>
</html>
