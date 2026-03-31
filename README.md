<html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>La Verduriza</title>

    <!-- 🔥 LEAFLET -->
  <link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>
    
<style>
        body {
            font-family: Arial;
            margin: 0;
            background: #f4f4f4;
        }

        header {
            background: #2e7d32;
            color: white;
            padding: 10px;
            text-align: center;
        }

        /* CONTENEDOR RESPONSIVE */
        .container {
            padding: 20px;
            width: 100%;
            margin: auto;
        }

        /* GRID RESPONSIVE */
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
  gap: 14px;
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
  width: 90%;
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
            border-radius: 10px;
            cursor: pointer;
            margin-top: 2px;
            width: 100%;
        }

        button:hover {
            background: #2e7d32;
        }

        .remove-btn {
            background: red;
            width: auto;
        }

        /* CARRITO */
        #cartBox {
            position: fixed;
            bottom: 5px;
            right: 5px;
            background: #ff9800;
            padding: 5px 10px;
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
            width: 40px;
            height: 40px;
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
            width: 70%;
            max-width: 400px;
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
        @media (max-width: 460px) {
            header h1 {
                font-size: 100%;
            }

            .grid {
                grid-template-columns: repeat(2, 1fr);
            }

            .product-img {
                height: 80px;
            }

            #cartBox {
                width: calc(100% - 20px);
                right: 7px;
                left: 7px;
                justify-content: center;
            }
        }
    /* CATEGORÍAS nuevas agregadas */
.categories {
    display: flex;
    overflow-x: auto;
    gap: 10px;
    margin-bottom: 15px;
    padding-bottom: 5px;
}

.categories button {
    min-width: max-content;
    padding: 8px 14px;
    border-radius: 20px;
    border: none;
    background: #ddd;
    cursor: pointer;
    font-weight: bold;
    color: black;
}

.categories button.active {
    background: #2e7d32;
    color: white;
}
    </style>
</head>

<body>

<header>
    <h1>LA VERDURIZA</h1>
    <p>FRUTAS Y VERDURAS FRESCAS HASTA LA PUERTA DE TU CASA</p>
</header>

<div class="container">
    <div class="categories" id="categories"></div> <!-- categorias -->
    <h2>Productos</h2>
    <div class="grid" id="products"></div>
</div>

<!-- CARRITO -->
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
        <input type="text" id="address" placeholder="Escribe tu dirección o usa ubicación">

<button onclick="getLocation()">📍 Usar mi ubicación actual</button>

<small style="color:gray;">
            Si la ubicación no es correcta, selecciona manualmente:
        </small>

 <div id="map" style="height:250px; border-radius:10px; margin-top:10px;"></div>

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

<script>
  const products = [
    { name: "Guineo", price: 20, category: "Frutas", img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRje6yTkMzYgIo3uvkDCzLDeqlMPLEF1W5caw&s" },
    { name: "Plátano", price: 22, category: "Frutas", img: "..." },
    { name: "Naranja", price: 27, category: "Frutas", img: "..." },

    { name: "Tomate", price: 42, category: "Verduras", img: "..." },
    { name: "Cebolla", price: 20, category: "Verduras", img: "..." },
    { name: "Zanahoria", price: 22, category: "Verduras", img: "..." },
    { name: "Chayote", price: 27, category: "Verduras", img: "..." },

    { name: "Aguacate", price: 60, category: "Abarrotes", img: "..." },
    { name: "Chile huajillo", price: 22, category: "Chiles", img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTg2IULLonAKrnWtVO0vQNqjOUOkj0v-QhYFQ&s" },
    { name: "Coco", price: 20, category: "Semillas", img: "..." }
];
    let cart = [];
    let total = 0;

    /* 🔥 VARIABLE GLOBAL PARA UBICACIÓN */
    let userCoords = "";
    let map, marker;

    const container = document.getElementById("products");
/*agregado antes*/
    const categories = ["Todos", "Verduras", "Chiles", "Semillas", "Abarrotes"];

let currentCategory = "Todos";

const categoriesContainer = document.getElementById("categories");

categories.forEach(cat => {
    const btn = document.createElement("button");
    btn.innerText = cat;

    if (cat === "Todos") btn.classList.add("active");

    btn.onclick = () => {
        currentCategory = cat;

        document.querySelectorAll(".categories button")
            .forEach(b => b.classList.remove("active"));

        btn.classList.add("active");

        renderProducts();
    };

    categoriesContainer.appendChild(btn);
});
   function renderProducts() {
    container.innerHTML = "";

    products.forEach((p, i) => {

        if (currentCategory !== "Todos" && p.category !== currentCategory) return;

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
}
renderProducts();

    /*fin de ccodigo nuevo*/
    
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
        document.getElementById("cartBox").style.display = "none";

        setTimeout(() => {
            if (!map) {
                initMap();
            } else {
                map.invalidateSize();
            }
        }, 300);
    }

    function closeModal() {
        document.getElementById("modal").style.display = "none";
        document.getElementById("cartBox").style.display = "flex";
    }

    function getLocation() {
        if (navigator.geolocation) {
            navigator.geolocation.getCurrentPosition(
                async function(position) {
                    const lat = position.coords.latitude;
                    const lng = position.coords.longitude;

                    userCoords = lat + "," + lng;

                    if (map) {
                        map.setView([lat, lng], 16);

                        if (marker) {
                            map.removeLayer(marker);
                        }

                        marker = L.marker([lat, lng]).addTo(map);
                    }

                    document.getElementById("address").value = `Ubicación GPS: ${lat}, ${lng}`;

                    try {
                        const response = await fetch(`https://nominatim.openstreetmap.org/reverse?format=json&lat=${lat}&lon=${lng}`);
                        const data = await response.json();

                        if (data && data.display_name) {
                            document.getElementById("address").value = data.display_name;
                        }
                    } catch (error) {
                        console.log("No se pudo obtener dirección exacta");
                    }

                    alert("Ubicación agregada automáticamente 📍");
                },
                function(error) {
                    alert("Activa la ubicación en tu celular ❌");
                },
                {
                    enableHighAccuracy: true,
                    timeout: 10000,
                    maximumAge: 0
                }
            );
        } else {
            alert("Tu navegador no soporta ubicación");
        }
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
        message += `👤 ${name}%0A`;
        message += `📍 Dirección: ${address}%0A`;

        if (userCoords) {
            const mapsLink = `https://maps.google.com/?q=${userCoords}`;
            message += `📍 Ubicación exacta: ${mapsLink}%0A`;
        }

        message += `📝 ${references}%0A`;
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

    function initMap() {
        map = L.map('map').setView([20.5888, -100.3899], 13);

        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '&copy; OpenStreetMap'
        }).addTo(map);

        map.on('click', function(e) {
            const lat = e.latlng.lat;
            const lng = e.latlng.lng;

            userCoords = lat + "," + lng;

            if (marker) {
                map.removeLayer(marker);
            }

            marker = L.marker([lat, lng]).addTo(map);

            document.getElementById("address").value = `Ubicación seleccionada: ${lat}, ${lng}`;
        });
    }
</script>

</body>
</html>
