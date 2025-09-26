<html lang="th">
<head>
  <meta charset="UTF-8">
  <title>Fashionเทพ | เว็บแฟชั่นครบวงจร</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>.hidden{display:none}</style>
</head>
<body class="bg-gray-100 text-gray-900">

  <!-- Navbar -->
  <nav class="bg-white shadow-md p-4 flex justify-between items-center">
    <h1 class="text-2xl font-bold text-pink-600">Fashionเทพ</h1>
    <ul class="flex gap-6">
      <li><a href="#" onclick="showPage('home')" class="hover:text-pink-600">หน้าหลัก</a></li>
      <li><a href="#" onclick="showPage('products')" class="hover:text-pink-600">สินค้า</a></li>
      <li><a href="#" onclick="showPage('promo')" class="hover:text-pink-600">โปรโมชั่น</a></li>
      <li><a href="#" onclick="showPage('cart')" class="hover:text-pink-600">ตะกร้า 🛒(<span id="cart-count">0</span>)</a></li>
      <li><a href="#" onclick="showPage('checkout')" class="hover:text-pink-600">ชำระเงิน</a></li>
    </ul>
  </nav>

  <!-- Home -->
  <section id="home" class="page text-center py-20 bg-gradient-to-r from-pink-500 to-red-500 text-white">
    <h2 class="text-5xl font-extrabold">ยินดีต้อนรับสู่โลกแฟชั่น</h2>
    <p class="mt-4 text-lg">แฟชั่นที่จะทำให้คุณแตกต่างในทุกๆ วัน</p>
    <button onclick="showPage('products')" class="mt-6 bg-white text-pink-600 px-6 py-3 rounded-full shadow hover:bg-gray-200">เริ่มช้อปเลย</button>
  </section>

  <!-- Products -->
  <section id="products" class="page hidden p-10 grid grid-cols-1 md:grid-cols-3 gap-8"></section>

  <!-- Promo -->
  <section id="promo" class="page hidden text-center py-20 bg-gradient-to-r from-yellow-400 to-pink-500 text-white">
    <h2 class="text-4xl font-extrabold">🔥 โปรโมชั่นพิเศษ 🔥</h2>
    <p class="mt-4 text-lg">ซื้อครบ 1,000 บาท ลดทันที 20%</p>
    <p class="mt-2">และส่งฟรีทั่วประเทศ</p>
    <button onclick="applyPromo();showPage('cart');" class="mt-6 bg-white text-pink-600 px-6 py-3 rounded-full shadow hover:bg-gray-200">ใช้โปรโมชั่น</button>
  </section>

  <!-- Cart -->
  <section id="cart" class="page hidden p-10 max-w-2xl mx-auto bg-white rounded shadow">
    <h2 class="text-2xl font-bold mb-4">ตะกร้าสินค้า</h2>
    <div id="cart-items"></div>
    <p class="mt-4 text-lg font-bold">รวมทั้งหมด: ฿<span id="cart-total">0</span></p>
    <p id="promo-applied" class="text-green-600 font-bold mt-2"></p>
    <button onclick="showPage('checkout')" class="mt-6 w-full bg-pink-600 text-white py-3 rounded hover:bg-pink-700">ไปชำระเงิน</button>
  </section>

  <!-- Checkout -->
  <section id="checkout" class="page hidden p-10 max-w-xl mx-auto bg-white rounded shadow">
    <h2 class="text-2xl font-bold mb-6">ข้อมูลชำระเงิน</h2>
    <form class="space-y-4" onsubmit="payNow(event)">
      <input type="text" placeholder="ชื่อ-นามสกุล" class="w-full border p-3 rounded" required>
      <input type="text" placeholder="ที่อยู่จัดส่ง" class="w-full border p-3 rounded" required>
      <input type="text" placeholder="เบอร์โทร" class="w-full border p-3 rounded" required>
      <input type="email" placeholder="อีเมล" class="w-full border p-3 rounded" required>
      <select class="w-full border p-3 rounded">
        <option>บัตรเครดิต</option>
        <option>โอนผ่านธนาคาร</option>
        <option>เก็บเงินปลายทาง</option>
      </select>
      <button class="w-full bg-green-600 text-white py-3 rounded hover:bg-green-700">ยืนยันการชำระเงิน</button>
    </form>
    <p id="pay-status" class="mt-4 text-center text-green-600 font-bold"></p>
  </section>

  <script>
    // ============================
    // Mock Data
    // ============================
    const products = [
      {id:1,name:"เสื้อยืดแฟชั่น",price:350,img:"https://i.pinimg.com/1200x/c8/3d/11/c83d11a5f59dff3949e9981da78b0e55.jpg"},
      {id:2,name:"กางเกงยีนส์",price:690,img:"https://i.pinimg.com/1200x/61/36/35/613635eb218642993787264b0f4e2c9d.jpg"},
      {id:3,name:"รองเท้าผ้าใบ",price:1200,img:"https://i.pinimg.com/736x/7f/71/83/7f7183e50207f5fa95e2fb2b8586cb00.jpg"},
      {id:4,name:"เดรสออกงาน",price:1500,img:"https://i.pinimg.com/736x/af/f2/b9/aff2b90687b455f1830b47dca0ce2d18.jpg"},
      {id:5,name:"แจ็คเก็ตเท่ๆ",price:2000,img:"https://i.pinimg.com/1200x/72/e5/32/72e532dcd9b26591efa6c93896a8d406.jpg"},
      {id:6,name:"หมวกแฟชั่น",price:250,img:"https://i.pinimg.com/1200x/c4/cb/2a/c4cb2a568cd57a6543e8e54fb48d2b3f.jpg"}
    ];
    let cart = [];
    let promoActive = false;

    // ============================
    // Page Switch
    // ============================
    function showPage(pageId){
      document.querySelectorAll('.page').forEach(p=>p.classList.add('hidden'));
      document.getElementById(pageId).classList.remove('hidden');
      if(pageId==="products") renderProducts();
      if(pageId==="cart") renderCart();
    }

    // ============================
    // Products Render
    // ============================
    function renderProducts(){
      const container=document.getElementById('products');
      container.innerHTML="";
      products.forEach(p=>{
        container.innerHTML+=`
          <div class="bg-white rounded-xl shadow hover:shadow-lg transition p-4 text-center">
            <img src="${p.img}" class="mx-auto h-48 w-full object-cover rounded-lg">
            <h3 class="mt-4 text-xl font-semibold">${p.name}</h3>
            <p class="text-gray-500">฿${p.price}</p>
            <button onclick="addToCart(${p.id})" class="mt-4 bg-pink-600 text-white px-4 py-2 rounded">เพิ่มลงตะกร้า</button>
          </div>`;
      });
    }

    // ============================
    // Cart Functions
    // ============================
    function addToCart(id){
      const item=products.find(p=>p.id===id);
      cart.push(item);
      document.getElementById("cart-count").innerText=cart.length;
      alert("เพิ่ม "+item.name+" ลงในตะกร้าแล้ว");
    }

    function renderCart(){
      const itemsDiv=document.getElementById("cart-items");
      itemsDiv.innerHTML="";
      let total=0;
      cart.forEach((item,i)=>{
        total+=item.price;
        itemsDiv.innerHTML+=`
          <div class="flex justify-between items-center border-b py-2">
            <div class="flex items-center gap-2">
              <img src="${item.img}" class="h-12 w-12 object-cover rounded"/>
              <span>${item.name}</span>
            </div>
            <div>
              <span>฿${item.price}</span>
              <button onclick="removeFromCart(${i})" class="ml-2 text-red-600">ลบ</button>
            </div>
          </div>`;
      });
      if(promoActive && total>=1000){
        const discount=total*0.2;
        total -= discount;
        document.getElementById("promo-applied").innerText="🎉 ลดราคา "+discount+" บาท";
      } else {
        document.getElementById("promo-applied").innerText="";
      }
      document.getElementById("cart-total").innerText=total;
    }

    function removeFromCart(i){
      cart.splice(i,1);
      document.getElementById("cart-count").innerText=cart.length;
      renderCart();
    }

    function applyPromo(){
      if(cart.length===0){ alert("❌ ตะกร้าว่าง"); return; }
      promoActive = true;
      renderCart();
      alert("✅ โปรโมชั่นลด 20% ถูกนำไปใช้แล้ว!");
    }

    // ============================
    // Payment
    // ============================
    function payNow(e){
      e.preventDefault();
      if(cart.length===0){ document.getElementById("pay-status").innerText="❌ ไม่มีสินค้าในตะกร้า"; return; }
      document.getElementById("pay-status").innerText="✅ ชำระเงินสำเร็จ! ขอบคุณที่อุดหนุน ❤️";
      cart=[]; promoActive=false;
      document.getElementById("cart-count").innerText=0;
      renderCart();
    }

    // เปิดเว็บแล้วโชว์หน้าแรก
    showPage('home');
  </script>
</body>
</html>
