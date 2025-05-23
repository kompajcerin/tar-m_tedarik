# tar-m_tedarik
tarım
<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8">
  <title>Tarım Tedarik Zinciri</title>
  <script src="https://cdn.jsdelivr.net/npm/web3@1.8.2/dist/web3.min.js"></script>
  <style>
    body { font-family: sans-serif; padding: 20px; max-width: 600px; margin: auto; }
    input, button { padding: 8px; margin: 5px 0; width: 100%; }
    #walletAddress { font-weight: bold; margin-top: 10px; color: green; }
  </style>
</head>
<body>

  <h2>MetaMask Cüzdanını Bağla</h2>
  <button id="connectWallet">Cüzdanı Bağla</button>
  <div id="walletAddress"></div>

  <hr>

  <h2>Ürün Ekle</h2>
  <input id="urunID" placeholder="Ürün ID (ör. TR12345)" />
  <input id="urunAdi" placeholder="Ürün Adı (ör. Domates)" />
  <input id="uretimTarihi" placeholder="Üretim Tarihi (ör. 2025-05-22)" />
  <input id="konum" placeholder="Konum (ör. Antalya)" />
  <input id="sicaklik" type="number" placeholder="Sıcaklık (ör. 4)" />
  <input id="durum" placeholder="Durum (ör. Hasat Edildi)" />
  <button onclick="urunEkle()">Ürünü Ekle</button>

  <script>
    const contractAddress = "0xd9145CCE52D386f254917e481eB44e9943F39138"; // Remix'ten aldığın adres
    const contractABI = [
      {
        "inputs": [
          { "internalType": "string", "name": "_urunID", "type": "string" },
          { "internalType": "string", "name": "_urunAdi", "type": "string" },
          { "internalType": "string", "name": "_uretimTarihi", "type": "string" },
          { "internalType": "string", "name": "_konum", "type": "string" },
          { "internalType": "int256", "name": "_sicaklik", "type": "int256" },
          { "internalType": "string", "name": "_durum", "type": "string" }
        ],
        "name": "urunEkle",
        "outputs": [],
        "stateMutability": "nonpayable",
        "type": "function"
      }
    ];

    let web3;
    let contract;
    let currentAccount;

    // Cüzdanı bağlama butonuna tıklanınca
    document.getElementById('connectWallet').addEventListener('click', async () => {
      if (typeof window.ethereum !== 'undefined') {
        try {
          web3 = new Web3(window.ethereum);
          const accounts = await window.ethereum.request({ method: 'eth_requestAccounts' });
          currentAccount = accounts[0];
          contract = new web3.eth.Contract(contractABI, contractAddress);
          document.getElementById('walletAddress').innerText = "Bağlı Cüzdan: " + currentAccount;
        } catch (err) {
          alert("Cüzdan bağlantısı reddedildi veya hata oluştu.");
          console.error(err);
        }
      } else {
        alert("MetaMask yüklü değil. Lütfen tarayıcıya MetaMask uzantısı yükleyin.");
      }
    });

    // Ürün ekleme fonksiyonu
    async function urunEkle() {
      if (!contract || !currentAccount) {
        alert("Lütfen önce MetaMask cüzdanınızı bağlayın.");
        return;
      }

      const urunID = document.getElementById('urunID').value;
      const urunAdi = document.getElementById('urunAdi').value;
      const uretimTarihi = document.getElementById('uretimTarihi').value;
      const konum = document.getElementById('konum').value;
      const sicaklik = parseInt(document.getElementById('sicaklik').value);
      const durum = document.getElementById('durum').value;

      try {
        await contract.methods.urunEkle(urunID, urunAdi, uretimTarihi, konum, sicaklik, durum)
          .send({ from: currentAccount });
        alert("✅ Ürün başarıyla eklendi!");
      } catch (err) {
        console.error(err);
        alert("Ürün eklenirken bir hata oluştu: " + err.message);
      }
    }
  </script>

</body>
</html>
